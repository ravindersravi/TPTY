# TPTY

import javax.management.*;

public class MyMBean implements DynamicMBean {

    private int count;

    @Override
    public Object getAttribute(String attribute) throws AttributeNotFoundException, MBeanException, ReflectionException {
        if (attribute.equals("Count")) {
            return count;
        }
        throw new AttributeNotFoundException(attribute);
    }

    @Override
    public void setAttribute(Attribute attribute) throws AttributeNotFoundException, InvalidAttributeValueException, MBeanException, ReflectionException {
        if (attribute.getName().equals("Count")) {
            count = (Integer) attribute.getValue();
        } else {
            throw new AttributeNotFoundException(attribute.getName());
        }
    }

    @Override
    public AttributeList getAttributes(String[] attributes) {
        AttributeList list = new AttributeList();
        for (String attribute : attributes) {
            try {
                Object value = getAttribute(attribute);
                list.add(new Attribute(attribute, value));
            } catch (Exception e) {
                // Ignore invalid attributes
            }
        }
        return list;
    }

    @Override
    public AttributeList setAttributes(AttributeList attributes) {
        AttributeList list = new AttributeList();
        for (Attribute attribute : attributes.asList()) {
            try {
                setAttribute(attribute);
                list.add(attribute);
            } catch (Exception e) {
                // Ignore invalid attributes
            }
        }
        return list;
    }

    @Override
    public Object invoke(String actionName, Object[] params, String[] signature) throws MBeanException, ReflectionException {
        throw new UnsupportedOperationException("MyMBean does not have any operations");
    }

    @Override
    public MBeanInfo getMBeanInfo() {
        MBeanAttributeInfo countAttributeInfo = new MBeanAttributeInfo(
            "Count",
            "The number of items",
            () -> count,
            value -> count = (Integer) value,
            true, // writable attribute
            false // non-is getter
        );

        return new MBeanInfo(
            MyMBean.class.getName(),
            "MyMBean",
            new MBeanAttributeInfo[] { countAttributeInfo },
            null,
            null,
            null
        );
    }
}
