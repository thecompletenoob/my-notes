# Juniper Security Concepts

## High Level Security Challenges

- Attack Frequency and Cost
- Threat Surface Expanding
- Security Technology Interaction

## Juniper Security Viewpoint

- **Operational Efficiency:** The solution must offer a centralized management and control. It must also be scalable for both SP and Enterprise environment and must be open and programmable.
- **Security Efficacy:** The solution must support advanced security services, integrated threat intelligence and fine-grained policy controls.
- **Business Agility:** The solution must easily scale up and out using a variety of deployment models.

## Focus Points

- **Performance:** How the network will perform across a wide range of use cases
- **Efficacy:** How the quality of threat protection will be measured and the ability to identify and mitigate the most pressing threats.
- **Scalability:** How the network will scale as traffic increases without major upgrade costs
- **Automation:** How programmable the security solution will be.
- **Centralized Control:** How the solution will be controlled from a single location.

## Unified Threat Management

### Web filtering

- **Integrated Web filtering**: The decision making for blocking or permitting web access is done on the device after it identifies the category for a URL either from a user-defined category or a category server. Requires a separate license
- **Redirect Web filtering**: intercepts the HTTP requests and forwards the server URL to an external URL filtering server to determine whether to block or permit the requested web access. Does not need a separate license
- **Local Web Filtering**: The decision making is done on the device after it identifies the category for a URL from user-defined categories stored on the device. No license required.

### Sophos Antivirus

In-the-cloud antivirus solution. It has a smaller memory footprint and less CPU intensive.

### Antispam Filtering

Antispam filtering - requires separate license subscription.

### Content Filtering

Blocks or permits certain types of traffic based on the MIME type, file extension, protocol command and embedded object type. It does not require a separate license.
