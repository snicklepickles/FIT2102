- Provides structure & content of a web page using a system of *markup tags & attributes*
- HTML documents are *interpreted* by web browsers to render the content visually for users
## Declarative Nature
- Describes the structure & content of a webpage without specifying how to achieve it
- Developers *declare* the desired structure
- Browsers handle the rendering process
### Key Aspects
#### Descriptive Tags
- HTML tags define purpose & meaning of content
- Pairs of opening/closing tags define *elements* in a **Document Object Model (DOM)**
- DOM is a hierarchical (tree) structure
	- Elements can be nested
#### Attribute-Based
- Provide additional information/functionality
- E.g., `class`, `id`, `src`, `href`, etc.
- Provide hooks for styling, scripting, or specifying behaviour
- Do not dictate how elements are displayed
	- Provide metadata/instructions to browsers
#### Separation of Concerns
- Modern HTML5:
	- Structure: HTML
	- Presentation: CSS
	- Behaviour (JavaScript)
- Promotes maintainability & scalability
#### Browser Interpretation
- Browsers apply default styles & layout algorithms to HTML elements
- Ensures consistency across different platforms & devices
## Comparison
* **Declarative**
	- Easier and faster to code, easier to read
	- Less likely to be buggy
	- We spend less time on trying to figure out _how_ to do things and focus on _what_ we want
- **Imperative**
	- Greater control than the pure declarative HTML/CSS approach
	- We can create richer interaction
- **Functional**
	- Is as powerful as imperative programming
	- But is more declarative (through functions that clearly declare their inputs and outputs)
	- Forces us to clearly distinguish code with side effects from pure computation.