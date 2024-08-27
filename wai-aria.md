
# Web Accessbility Initiative's Accessible Rich Internet Applications

[Web Accessibility Initiative's Accessible Rich Internet Applications](https://www.w3.org/TR/wai-aria/#intro_ria_accessibility) (WAI-ARIA or just ARIA) offers attributes that apply to HTML elements, making web content and applications more accessible to people with disabilities. The core ARIA component, **role**, lets screen readers explicitly describe the intentions of web content or apps to people.

**Note**: Elements can only have one role.

There are six categories for [WAI-ARIA roles](https://www.w3.org/TR/wai-aria-1.1/#roles_categorization):

| Role | Description | Example |
|--------------|-------|-------|
| **Landmark** | Denotes large areas of a document. |`banner`, `search`, `region` |
| **Widget** | Assists in making interactive elements accessible. | `button`, `checkbox`, `radio` |
| **Window** | Assists in creating a sub-window within your webpage. | `alertdialog`,`dialog` |
| **Document structure** | Gives information about your web page's non-interactive, static section. | `article`, `table`, `heading` |
| **Live region** | Informs assistive technologies about the dynamic content of a webpage. The live region works with the `aria-live` attribute. | `alert`, `log`, `timer` |
| **Abstract** | Describes the foundation for other WAI-ARIA roles. Browsers and not developers use abstract roles. | `navigation`, `composite`, `window` |

## ARIA Attributes

ARIA includes attributes that are slightly different from roles. Developers prefix those attributes with **aria-** and add them to HTML in the same way as role, with a wide range of additional attributes available. There are two types of attributes and [states and properties](https://www.w3.org/TR/wai-aria/#states_and_properties):

* **States**: The value of states is bound to change because of user interaction.
* **Properties** The value of properties is unlikely to change.

### States

The ARIA states attributes provide a dynamic way to convey the current state of interactive elements in web accessibility. You can use a state attribute like the one below to reflect changes based on user interaction.

**aria-expanded**: The aria-expanded attribute indicates whether a collapsible element, such as an accordion panel, is currently expanded or collapsed. Its value can change based on user interaction, like clicking to expand or collapse the panel.

### Properties

ARIA properties and attributes define static characteristics and elements' relationships to enhance web accessibility. The following label provides an example of a property attribute.

**aria-labelledby**: The aria-labelledby attribute associates an element with another element that serves as its label. Once set, the association typically remains unchanged unless the document's structure is modified.

## Why ARIA attributes matters

When a developer intends to implement ARIA using either [roles](https://www.w3.org/TR/wai-aria-1.0/roles) or [states and properties](https://www.w3.org/TR/wai-aria-1.0/states_and_properties), they must follow ARIA guidelines. When values are invalid, the attributes may render web content useless and have no effect on a taken action or report inaccurate UI information.

## Common ARIA mistakes

Although ARIA is important in enhancing the web browsing experience for assisstive technologies, problems can arise from a development standpoint. Common mistakes include:

* **Incorrect WAI-ARIA Syntax**: This includes issues like case sensitivity, wrong spelling, invalid role declarations, or the role not existing.
* **Invalid ID value references**: This includes attributes only accepting [supported states and properties](https://www.w3.org/TR/wai-aria-1.1/#state_prop_def).
* **Allowed or required WAI-ARIA attributes missing**: WAI-ARIA provides specific guidance on how attributes are to be [implemented in host languages](https://www.w3.org/TR/2017/REC-wai-aria-1.1-20171214/#host_languages), like semantic HTML or JavaScript.

## Troubleshooting resources

WAI-ARIA explicitly defines attributes, their values, and how to use them. In addition to the links mentioned throughout this webpage, developers use the following documentation to troubleshoot common ARIA issues:

* [Customizable Quick Reference](https://www.w3.org/WAI/WCAG21/quickref/?showtechniques=145#top) is a comprehensive and highly customizable view of ARIA guidelines and techniques for applying ARIA attributes.
* [Using ARIA](https://w3c.github.io/using-aria/) has content about adding accessibility information to elements.
* [WAI-ARIA Authoring Practices 1.1](https://www.w3.org/TR/wai-aria-practices/) provides guidance about successfully applying WAI-ARIA attributes.
