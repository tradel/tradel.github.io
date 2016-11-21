<!--
.. title: Custom Null Serialization With Jackson 2
.. slug: custom-null-serialization-with-jackson-2
.. date: 2014-01-31 14:16:55 UTC-05:00
.. tags: java, jersey, jackson
.. category: Java
.. link:
.. description:
.. type: text
-->


However, it's a bit of a pain to override the default serialization behavior for null values. Jackson only allows you to have one `NullSerializer`, which is supposed to apply to all object types. What if you want to serialize null strings differently from dates or other objects?

<!-- TEASER_END -->

Here's an example. I have an `Application` class which Jackson serializes this way:

``` json
{
  "id" : 2,
  "name" : "ACME Book Store Application",
  "createdOn" : 1390338602011,
  "description" : null,
  "runningSince" : null
}
```

I would rather have the description appear as an empty string if it has no value. Same goes for `runningSince`, except that since Jackson is converting dates to timestamps, I would like to use 0 for null values, like this:

``` json
{
  "id" : 2,
  "name" : "ACME Book Store Application",
  "createdOn" : 1390338602011,
  "description" : "",
  "runningSince" : 0
}
```

As with many things, the answer turned out to be surprisingly easy, but it took me days trawling through some of the less reputable parts of StackOverflow and Github before I finally found some useful pointers. Mind you, I found lots of answers right away -- but they were along the lines of "just write your own subclass of NullSerializer, duh". Which kind of misses the point, because `NullSerializer` doesn't know how to differentiate between data types, as I mentioned above.

Finally I came across [this post](http://stackoverflow.com/questions/14714328/jackson-how-to-add-custom-property-to-the-json-without-modifying-the-pojo) on StackOverflow. Almost as an afterthought, someone mentions creating their own implementations of `BeanSerializer`, `BeanSerializerBuilder` and
`BeanSerializerFactory`.

Eureka! I bet I can just subclass those and override the parts that write out null values. It turned out to be almost that easy.

I wrote my own `CustomBeanPropertyWriter` and override the `serializeAsField` method. It handles null Dates and Strings, and passes everything off to the original method in `BeanPropertyWriter`:

``` java
    @Override
    public void serializeAsField(Object bean, JsonGenerator jgen, SerializerProvider prov) throws Exception
    {
        Object value = get(bean);
        if (value == null)
        {
            if (_declaredType.getRawClass().equals(String.class))
            {
                jgen.writeFieldName(_name);
                jgen.writeString("");
            }
            else if (_declaredType.getRawClass().equals(Date.class))
            {
                jgen.writeFieldName(_name);
                jgen.writeNumber(0);
            }
            else if (_nullSerializer != null)
            {
                jgen.writeFieldName(_name);
                _nullSerializer.serialize(null, jgen, prov);
            }
            return;
        }
        else
        {
            super.serializeAsField(bean, jgen, prov);
        }
    }
```
