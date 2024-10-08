---
title: Use sling transformers in AEM
layout: post
author: andreea
lang: en
tags:
- AEM
- SLING
categories:
- AEM
---
Today I want to talk about an interesting problem changing from relative to absolute urls in AEM generated html. When I first
heard about this my mind already was thinking to transformers. I was reading about the transformers in the documentation [Sling documentation](https://sling.apache.org/documentation/bundles/output-rewriting-pipelines-org-apache-sling-rewriter.html)
and trying to understand how it works. I realized that this could actually help me to solve the problem.

The Sling Transformers is a powerful mechanism which allow us to change the output(typically html) generated by the Sling rendering process. It is part of the <em>Apache Sling Rewriter</em> which uses SAX event pipline as in the screen:
![Apache sling rewriter pipeline](/assets/img/posts/apacheslingrewriter.png)

Every pipeline consist of three components and every component has a java interface and factory:

- **The Generator** generates SAX events and puts them in the pipeline
- **Transformers** here is where the actual transformation is happening. The transformers can be placed in chain. A transformer receive SAX events from the previous component and sends events to the next component.
- **Serializer** collects all incomming SAX events, transforms them into the required response by writing into output stream of the response.

An AEM developer usually implements only the transformer.

First we need to create a class which implements the TransformerFactory where we return an instance of our transformer(in tmy case is AbsoluteUrlTransformer)

```
import org.apache.sling.rewriter.Transformer;
import org.apache.sling.rewriter.TransformerFactory;
import org.osgi.service.component.annotations.Component;
import org.osgi.service.component.annotations.Reference;

@Component(service = {TransformerFactory.class}, property = {
    "specVersion=1.1",
    "label=Transform to absolute urls",
    "pipeline.type=transform-to-absolute-urls",
    "service.ranking:Integer=200"
})
public class AbsoluteUrlTransformerFactory implements TransformerFactory {

    public Transformer createTransformer() {
        return new AbsoluteUrlTransformer();
    }

}
```
The <em>pipeline.type</em> is an unique identifier referenced by the pipeline configuration. The <em>service.ranking</em> specifies where to add the transformer in the pipeline. If the value is less than 0 the transformer will be added at the beginning of the pipeline right after the generator. If the ranking is zero or higher the transformer will be added at the end right before the serializer.
The TransformerFactory interface has just one method which returns a new transformer instance. If you plan to use other services in your transformer you might declare the references on the factory and pass in the instances into the newly created transformer

Next we need to implement the Transformer interface in the class where we actually perform the change:
```
public class AbsoluteUrlTransformer extends AbstractSAXPipe implements Transformer {


    private boolean transform = false;

    private static final Set<String> SEARCH_ATTRIBUTE = new HashSet<>(Arrays.asList("href"));
    private static final Set<String> TAGS = new HashSet<>(Arrays.asList("a"));
    
    public AbsoluteLinkTransformer(TenantConfiguration tenantConfiguration) {
        this.tenantConfiguration = tenantConfiguration;
    }

    public void init(ProcessingContext processingContext, ProcessingComponentConfiguration processingComponentConfiguration) throws IOException {
        final SlingHttpServletRequest request = processingContext.getRequest();
      
        WCMMode currentMode = WCMMode.fromRequest(request);

        transform = (WCMMode.PREVIEW.equals(currentMode) || WCMMode.DISABLED.equals(currentMode));
    }

    public void dispose() {

    }

    @Override
    public void startElement (String nsUri, String localname, String rawName, Attributes atts) throws SAXException {
        AttributesImpl linkAttributes = new AttributesImpl(atts);

        if (transform && TAGS.contains(localname)) {
            for (int i=0; i < linkAttributes.getLength(); i++) {
                final String attribute = linkAttributes.getLocalName(i);
                if (SEARCH_ATTRIBUTE.contains(attribute)) {
                    final String path = linkAttributes.getValue(i);
                     if(StringUtils.startsWith(path,"/")){
                         final String absolutePath = "https://test.com"+path;
                         linkAttributes.setValue(i, absolutePath);

                     }
                   
                }

            }
        }

        super.startElement(nsUri, localname, rawName, linkAttributes);
    }

}

```
As you can see in the class above I am searching all the hyperlinks "a" and change the path to the absolute path.

Now we need to configure a html rewriter in the path /apps/**APPNAME**/config/rewriter/*:
```
<custom-pipeline
   jcr:primaryType="nt:unstructured"
   contentTypes="text/html"
   generatorType="htmlparser"
   order="100"
   serializerType="htmlwriter"
   transformerTypes="[transform-to-absolute-urls]">
   <generator-htmlparser
      jcr:primaryType="nt:unstructured"
      includeTags="[A]"/>
</custom-pipeline>
```
You can check that your configuration is correctly registered in the <em>Apache Sling Rewriter</em> system console.
I hope this is helpful next time you need to create a transformer.
