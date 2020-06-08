# Pattern Modelling
<div align="right">June 2020</div>
<div align="right">David Stevens</div>

The majority of my day job is in the role of product owner of [DXC Digital Explorer](https://digitalexplorer.dxc.com); one key module within this platform is the Solutions module; allowing teams to share, discover and reuse solutions DXC has delivered to it's customers.   At the core of this module is the [DXC technologies solution reference model](https://github.com/dxc-technology/dxc-digitalexplorer/blob/master/DataModels/SolutionMetaModel.md
). <br> 

Within the construct of a data fuelled organisation the benefit to not only adopt a common information model to describe it's products or solutions provided to customers, but capture this information as a **true information data model** unlocks a number of use cases and brings increased value.


## A single (meta)model

The first benefit I am attempting to gain with a single information model for a "Solution" is the potential for any number of different types of solutions to be documented; these could range from internal offerings, partner capabilities, demonstration or concepts through to full scale production solutions delivered to a customer.

![image](images/solutionTypes.png)<br>

This is achieved by including a category (2 tier structure) against each solution model stored within Digital Explorer and is selected/managed on the very first pages when adding or editing a solution.

<img src="images/SolutionTypesDE.png" height="350"><br>
_View of the solution type selection within Digital Explorer ~ Note Pattern and Reference Architecture are within the "Reference Datasets" category_


## It's a model for a reason
Once a solution is captured as a data model the real potential to unlock new value becomes a reality and the means to leverage and reuse common patterns is possible.<br>
<br>
**Documenting a pattern without the consumers understanding how it can be integrated into their own solution nullifies it's definition as a pattern; the information model used to describe the pattern must be known on both sides.**


## Standalone Patterns
It's possible to create a single solution model as a *Pattern* within Digital Explorer; leveraging the full information model and integrating the pattern into the wider information eco-system the platform enables.<br>  A dedicated solution (sub)type for pattern is available today.<br>
<img src="images/aPattern.png" height="300"><br>


## Embedding Patterns
Digital Explorer also provides the ability to document 1 or more patterns within a single larger solution model (e.g. a Reference Architecture)<br>
![image](images/embeddedPatterns.png)<br>
<br>
![image](images/ExamplePattern.png)<br>

_Example Pattern defined within DXC Digital Explorer_


## Publishing and Discovering Patterns
By the very natural of creating a connected Graph dataset, the information created and managed within the Solution's module is also available within the other modules within Digital Explorer (and also via API calls for other systems to use)

### Publishing
Pattern and Reference Architecture owners (in fact any solution owner), can publish and share a single or a collection of patterns via an online **Playbook** directly within the Digital Explorer platform.  Each playbook allows authors to provide any additional supporting material (slides, text, data views) to complement the presentation of their solution models.   Being an online record, the viewing and sharing of each playbook provides another data stream into the data fuelled organisation and provides further input to support the value a pattern or reference architecture has provided external consumers.<br>

<img src="images/Playbook.png" height="400"><br>

_Example playbook page showing a summary of the solution model_


### Discovering Patterns
As mentioned each solution model it's own type or category, allowing users within the Workspace analytics module to filter the suggested solutions down to an appropriate level (i.e.  Show only matching patterns) 

<img src="images/Suggestions.png" height="200"><br>

_Filtering suggestions within the Digital Explorer Workspace module_

---

## Future planned work


### Solutions become patterns
One of the true benefits of modelling all solutions, is the potential to use machine learning algorithms to help identify potential patterns from the complete or a subset of the solution information held.<br>

![image](images/PatternMatching.png)<br>


### Pattern becomes a standalone solution
Similar to the use case above, following the identification of a new pattern within a single solution, it may be beneficial to extract out the pattern into it's own dedicated solution model; allowing further information and it's own development lifecycle to be applied to the pattern, without impacting the original solution where the pattern was first described.


### Nesting Solutions ~ removing duplication and identifying risk

The ability to compose or layer in solutions models on top of each other allows complex solutions to be composed and validated during solution development.<br>
![image](images/nestedSolutions.png)<br>
<br>
As patterns and other solutions types are added to a solution, duplications and gaps can be identified; Within Digital Explorer this is further simplified by aligning to key architectural domains or [technology groups]() within the solution model itself 

![image](images/nestedSolutions2.png)<br>


---


## Other benefits to consider

- Track changes and pattern alignment 
  - Allow pattern owners to gain a insight into the implementation view of their patterns (changes made, common components, industry and regional selections) 
  - Provide outside-in input to support further development and support within the organisation. 
- Allows for automation and improved workflows
  - Provides a known and trusted data stream into both manual processes and automated workflows.
- Provides integrable components
- The current information model only covers the first 2 layers of an architectural model; to bring true value and provide full automation (the architecture does become architecture-as-code), all 4 layers need to be included within a connected data model.<br>
<img src="images/4Layers.png" height="320"><br>

- Cost modelling
  - Each capabilities and technical component included can also include a associated cost
  - The further down the model we are able to document the more accurate the cost 



---

## References

[Solution Composition ~ Feb 2016](https://aginggeekblog.wordpress.com/2016/02/24/solution-composition/)
