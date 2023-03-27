``` 
import javax.management.*;
import java.lang.management.ManagementFactory;
import java.util.ArrayList;
import java.util.List;

public class ExampleDynamicMBean implements DynamicMBean {

    private int attributeValue;

    @Override
    public Object getAttribute(String attribute) throws AttributeNotFoundException, MBeanException, ReflectionException {
        if (attribute.equals("AttributeValue")) {
            return attributeValue;
        } else {
            throw new AttributeNotFoundException(attribute);
        }
    }

    @Override
    public void setAttribute(Attribute attribute) throws AttributeNotFoundException, InvalidAttributeValueException, MBeanException, ReflectionException {
        if (attribute.getName().equals("AttributeValue")) {
            attributeValue = (int) attribute.getValue();
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
                // Ignore attribute if it doesn't exist or if there was an error getting it
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
                // Ignore attribute if it doesn't exist or if there was an error setting it
            }
        }
        return list;
    }

    @Override
    public Object invoke(String actionName, Object[] params, String[] signature) throws MBeanException, ReflectionException {
        throw new ReflectionException(new NoSuchMethodException(actionName));
    }

    @Override
    public MBeanInfo getMBeanInfo() {
        List<MBeanAttributeInfo> attributeList = new ArrayList<>();
        attributeList.add(new MBeanAttributeInfo("AttributeValue", int.class.getName(),
                "A single attribute value", true, true, false));
        MBeanAttributeInfo[] attributes = attributeList.toArray(new MBeanAttributeInfo[attributeList.size()]);
        return new MBeanInfo(this.getClass().getName(), "Example Dynamic MBean", attributes, null, null, null);
    }

    public static void main(String[] args) throws Exception {
        MBeanServer mbs = ManagementFactory.getPlatformMBeanServer();
        ObjectName name = new ObjectName("com.example:type=ExampleDynamicMBean");
        ExampleDynamicMBean mbean = new ExampleDynamicMBean();
        mbs.registerMBean(mbean, name);

        // Wait for user input before exiting
        System.out.println("Press any key to exit");
        System.in.read();

        // Unregister the MBean
        mbs.unregisterMBean(name);
    }
}


```
