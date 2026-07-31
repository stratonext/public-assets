# Rules to Define Requirements

## Terminology

| Term | Meaning |
|------|---------|
| **Shall** | Requirement |
| **Will** | Fact or declaration of purpose |
| **Should** | Goal |

---

## C.1 Use of Correct Terms

- **Shall** = requirement
- **Will** = facts or declaration of purpose
- **Should** = goal

---

## C.2 Editorial Checklist

### Personnel Requirement
The requirement is in the form "responsible party shall perform such and such." Use the active, rather than the passive voice. A requirement should state who shall (do, perform, provide, weigh, or other verb) followed by a description of what should be performed.

### Product Requirement
The requirement is in the form "product ABC shall XYZ." A requirement should state "The product shall" (do, perform, provide, weigh, or other verb) followed by a description of what should be done.

- The requirement uses consistent terminology to refer to the product and its lower-level entities.
- Complete with tolerances for qualitative/performance values (e.g., less than, greater than or equal to, plus or minus, 3 sigma root sum squares).
- Is the requirement free of implementation? Requirements should state **what** is needed, **not how** to provide it; i.e., state the problem not the solution. Ask, "Why do you need the requirement?" The answer may point to the real requirement.
- Free of descriptions of operations? Is this a need the product should satisfy or an activity involving the product? Sentences like "The operator shall…" are almost always operational statements, not requirements.

### Example Product Requirements

- The system shall operate at a power level of…
- The software shall acquire data from the…
- The structure shall withstand loads of…
- The hardware shall have a mass of…

---

## C.3 General Goodness Checklist

- The requirement is grammatically correct.
- The requirement is free of typos, misspellings, and punctuation errors.
- The requirement complies with the project's template and style rules.
- The requirement is stated positively (as opposed to negatively, i.e., "shall not").
- The use of "To Be Determined" (TBD) values should be minimized. It is better to use a best estimate for a value and mark it "To Be Resolved" (TBR) with the rationale, who is responsible for its elimination, and by when it should be eliminated.
- The requirement is accompanied by an intelligible rationale, including any assumptions. Assumptions should be confirmed before baselining.
- The requirement is located in the proper section of the document (e.g., not in an appendix).

---

## C.4 Requirements Validation Checklist

### Clarity

- Are the requirements clear and unambiguous? Are all aspects understandable and not subject to misinterpretation? Is the requirement free from indefinite pronouns (this, these) and ambiguous terms (e.g., "as appropriate," "etc.," "and/or," "but not limited to")?
- Are the requirements concise and simple?
- Do the requirements express only one thought per statement, as opposed to multiple requirements in a single statement, or a paragraph that contains both requirements and rationale?
- Does the requirement statement have one subject and one predicate?

### Completeness

- Are requirements stated as completely as possible? Have all incomplete requirements been captured as TBDs or TBRs with a complete listing maintained?
- Are any requirements missing? Have the following areas been considered: functional, performance, interface, environment, facility, transportation, training, personnel, operability, safety, security, appearance, physical characteristics, and design?
- Have all assumptions been explicitly stated?

### Compliance

- Are all requirements at the correct level (e.g., system, segment, element, subsystem)?
- Are requirements free of implementation specifics? (Requirements should state what is needed, not how to provide it.)
- Are requirements free of descriptions of operations? (Update the ConOps instead.)
- Are requirements free of personnel or task assignments? (Update the SOW or Task Order instead.)

### Consistency

- Are the requirements stated consistently without contradicting themselves or related systems?
- Is the terminology consistent with the user and sponsor's terminology? With the project glossary?
- Is the terminology consistently used throughout the document? Are key terms included in the project's glossary?

### Traceability

- Are all requirements necessary to meet the parent requirement? Distinguish between needs and wants. Ask, "What is the worst that could happen if the requirement was not included?"
- Are all requirements bidirectionally traceable to higher-level requirements or mission/system-of-interest scope (needs, goals, objectives, constraints, or concept of operations)?
- Is each requirement stated so that it can be uniquely referenced (e.g., uniquely numbered) in subordinate documents?

### Correctness

- Is each requirement correct?
- Is each stated assumption correct? Assumptions should be confirmed before the document can be baselined.
- Are the requirements technically feasible?

### Functionality

- Are all described functions necessary and together sufficient to meet mission and system goals and objectives?

### Performance

- Are all required performance specifications and margins listed (e.g., timing, throughput, storage size, latency, accuracy and precision)?
- Is each performance requirement realistic?
- Are the tolerances overly tight? Are they defendable and cost-effective? Ask, "What is the worst thing that could happen if the tolerance was doubled or tripled?"

### Interfaces

- Are all external interfaces clearly defined?
- Are all internal interfaces clearly defined?
- Are all interfaces necessary, sufficient, and consistent with each other?

### Maintainability

- Have maintainability requirements been specified in a measurable, verifiable manner?
- Are requirements written so that ripple effects from changes are minimized (i.e., requirements are as weakly coupled as possible)?

### Reliability

- Are clearly defined, measurable, and verifiable reliability requirements specified?
- Are there error detection, reporting, handling, and recovery requirements?
- Are undesired events (e.g., single-event upset, data loss, operator error) considered and their required responses specified?
- Have assumptions about the intended sequence of functions been stated? Are these sequences required?
- Do requirements adequately address survivability after a software or hardware fault from the point of view of hardware, software, operations, personnel, and procedures?

### Verifiability / Testability

- Can the system be tested, demonstrated, inspected, or analyzed to show that it satisfies requirements? Does a means exist to measure and verify compliance?
- Are the requirements stated precisely to facilitate specification of system test success criteria?
- Are the requirements free of unverifiable terms (e.g., flexible, easy, sufficient, safe, ad hoc, adequate, accommodate, user-friendly, usable, when required, if required, appropriate, fast, portable, light-weight, small, large, maximize, minimize, robust, quickly, easily, clearly, other "ly" words, other "ize" words)?

### Data Usage

- Where applicable, are "don't care" conditions truly "don't care"? Are "don't care" values explicitly stated? (Correct identification may improve a design's portability.)

---

*Source: [NASA — Appendix C: How to Write a Good Requirement](https://www.nasa.gov/reference/appendix-c-how-to-write-a-good-requirement/)*
