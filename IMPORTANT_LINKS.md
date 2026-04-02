# Important Links

Below is a collection of resources and repositories we deem important. This includes links to other
{metæffekt}-projects containing relevant information about {metæffekt} SCA-Tooling and execution.

### [{metæffekt}-examples](https://gitlab.opencode.de/metaeffekt/metaeffekt-examples)

Contains a set of example GitLab pipelines and templates utilizing components found in the {metæffekt}-components
repository. The pipelines are executed based on example-products to provide a set of real-worlds results.

### [{metæffekt}-components](https://gitlab.opencode.de/metaeffekt/metaeffekt-components)

Mirrors the functionality of the {metæffekt}-kontinuum processors in a GitLab CI/CD environment. Each component
directly corresponds to a {metæffekt}-kontinuum processor.

### [{metæffekt}-kontinuum](https://github.com/org-metaeffekt/metaeffekt-kontinuum)

Contains a set of XML files called 'processors' in the form of maven POMs. Each processor is responsible for
executing a single process-flow such as vulnerability-enrichment or license-scanning.

### [{metæffekt}-workbench](https://github.com/org-metaeffekt/metaeffekt-workbench)

Contains a variety of configuration parameters, files and resources required by the {metæffekt}-kontinuum
and {metæffekt}-components to successfully execute processes.