# EE348: Knowledge Representation and Reasoning in AI
## Lab 7 Report: Description Logic and Ontology Reasoning in Python

- **Department:** Department of Computing
- **Class:** BSAI-2024A
- **Date:** 22-10-2026 | **Time:** 02:00 to 05:00 (3 Hours)
- **Course Instructor:** Dr. Saqib Nawaz
- **Lab Engineer:** Syed Muhammad Ali Musa
- **CLO 1:** Understand various knowledge representation formalisms and reasoning techniques

---

## 1. Executive Summary
This laboratory explores formal knowledge representation and automated reasoning using **Description Logics (DL)**, the mathematical formalism underlying modern ontology languages such as the W3C Web Ontology Language (OWL 2). Building upon the university domain from Lab 6, we implemented, evaluated, and verified core Description Logic reasoning mechanisms in Python, including:
1. **Structural Separation ($\mathcal{K} = \langle \mathcal{T}, \mathcal{A} \rangle$):** Separating terminological knowledge ($TBox$) from assertional knowledge ($ABox$).
2. **Taxonomic Forward-Chaining:** Upward deductive closure over named concept inclusions ($C \sqsubseteq D$).
3. **Model-Theoretic Semantic Operators:** Evaluating Boolean constructors ($\sqcap, \sqcup, \neg$) and quantified role restrictions ($\exists R.C, \forall R.C$) over an explicit finite interpretation $\mathcal{I} = (\Delta^{\mathcal{I}}, \cdot^{\mathcal{I}})$.
4. **Finite Interpretation Checking:** Formally verifying concept subsumption in bounded models and diagnosing counterexamples.
5. **Concept Equivalence & Complex Classification:** Formulating defined concepts ($C \equiv D_1 \sqcap \exists R.D_2$) for automated recognizer-based classification.
6. **Inconsistency vs. Unsatisfiability:** Detecting disjointness clashes ($C \sqcap D \sqsubseteq \bot$) and distinguishing empty concept models from contradictory knowledge bases.
7. **Open-World Reasoning (OWA) & Anonymous Witnesses:** Evaluating three-valued epistemic queries (ENTAILED vs. UNKNOWN) and analyzing the absence of the Unique Name Assumption (UNA).
8. **University Mini-Ontology Challenge:** Synthesizing an academic library resource ontology with taxonomy, complex equivalence, universal restriction, clash detection, and comparative evaluation against production Tableau reasoners.

---

## 2. Task-Wise Results & Detailed Exercises

### Task 1 — Build a University TBox and ABox
#### Formal Representation
- **TBox Subclass Axioms:**
  $$\text{GraduateStudent} \sqsubseteq \text{Student}$$
  $$\text{TeachingAssistant} \sqsubseteq \text{Student}$$
  $$\text{Student} \sqsubseteq \text{Person}$$
  $$\text{Professor} \sqsubseteq \text{Person}$$
- **ABox Concept Assertions:**
  $$\text{Student}(\text{Ali}), \quad \text{TeachingAssistant}(\text{Sara}), \quad \text{Professor}(\text{DrAhmed}), \quad \text{Course}(\text{CS101}), \quad \text{Project}(\text{P1})$$
- **ABox Role Assertions:**
  $$\text{enrolledIn}(\text{Ali}, \text{CS101}), \quad \text{worksOn}(\text{Ali}, \text{P1}), \quad \text{teaches}(\text{DrAhmed}, \text{CS101})$$

#### Exercise 1.1: Added Assertions & ABox Output
We added $\text{GraduateStudent}(\text{Ahmed})$ and $\text{Course}(\text{AI201})$ to the ABox.
- **Output:**
  - `TBox subclass axioms: 4`
  - `ABox class assertions: 7`
  - `ABox role assertions: 3`
  - **Class assertions:** `Course(AI201)`, `Course(CS101)`, `GraduateStudent(Ahmed)`, `Person/Student(Ali)`, `Professor(DrAhmed)`, `Project(P1)`, `TeachingAssistant(Sara)`.
  - **Role assertions:** `enrolledIn(Ali, CS101)`, `teaches(DrAhmed, CS101)`, `worksOn(Ali, P1)`.

#### Exercise 1.2: Syntactic Category Classification
| Symbol | Syntactic Category | Formal Semantic Description |
| :--- | :--- | :--- |
| **`Person`** | **Concept (Class)** | Unary predicate denoting a subset of individuals in domain: $\text{Person}^{\mathcal{I}} \subseteq \Delta^{\mathcal{I}}$. |
| **`Sara`** | **Individual (Object)** | Constant symbol denoting a concrete domain entity: $\text{Sara}^{\mathcal{I}} \in \Delta^{\mathcal{I}}$. |
| **`teaches`** | **Role (Binary Property)** | Binary relation denoting pairs of related individuals: $\text{teaches}^{\mathcal{I}} \subseteq \Delta^{\mathcal{I}} \times \Delta^{\mathcal{I}}$. |
| **`AI201`** | **Individual (Object)** | Constant symbol referring to a specific course instance in $\Delta^{\mathcal{I}}$. |
| **`Project`** | **Concept (Class)** | Unary predicate representing the set of all project entities: $\text{Project}^{\mathcal{I}} \subseteq \Delta^{\mathcal{I}}$. |

#### Exercise 1.3: TBox vs. ABox Rationale
- **$\text{Student} \sqsubseteq \text{Person}$ belongs to the TBox** because it expresses an intensional, universal schema constraint that must hold across all possible worlds. In first-order logic: $\forall x (\text{Student}(x) \to \text{Person}(x))$. It defines semantic structure independent of specific individuals.
- **$\text{Student}(\text{Ali})$ belongs to the ABox** because it is an extensional, contingent assertion about a specific named individual in a particular state of affairs. Modifying or asserting Ali's membership does not change the conceptual definition of what a student is.

---

### Task 2 — Subclass Closure and Instance Checking
#### Formal Representation & Deduction Rule
Upward taxonomic inheritance computes the least fixed point of the rule:
$$\frac{C(a) \quad C \sqsubseteq D}{D(a)}$$

#### Exercise 2.1: Confirmation of Sara's Memberships & Derivation Steps
- **Inference Trace:**
  - `Sara : TeachingAssistant => Student` (via $\text{TeachingAssistant} \sqsubseteq \text{Student}$)
  - `Sara : Student => Person` (via $\text{Student} \sqsubseteq \text{Person}$)
- **Verifications:**
  - `Student(Sara) entailed? True`
  - `Person(Sara) entailed?  True`

#### Exercise 2.2: Added $\text{Tutor} \sqsubseteq \text{Student}$ and $\text{Tutor}(\text{Hina})$
- Asserting $\text{Tutor}(\text{Hina})$ derives:
  1. `Tutor(Hina)` (asserted)
  2. `Student(Hina)` (inferred via $\text{Tutor} \sqsubseteq \text{Student}$)
  3. `Person(Hina)` (inferred via $\text{Student} \sqsubseteq \text{Person}$)
- **All derived named memberships for Hina:** `['Person', 'Student', 'Tutor']`.

#### Exercise 2.3: Directionality of $\sqsubseteq$ & Non-Entailment Check
- **Query:** Does $\text{Student}(\text{Ali})$ entail $\text{TeachingAssistant}(\text{Ali})$?
- **Result:** **`False` (Non-entailment).**
- **Explanation:** In Description Logic, $C \sqsubseteq D$ formalizes set inclusion ($C^{\mathcal{I}} \subseteq D^{\mathcal{I}}$) and one-way conditional implication ($\forall x (C(x) \to D(x))$).
  - The axiom $\text{TeachingAssistant} \sqsubseteq \text{Student}$ asserts that *every teaching assistant is a student* ($\text{TA} \to \text{Student}$).
  - It does **not** assert the inverse ($\text{Student} \to \text{TA}$).
  - Inferring $\text{TeachingAssistant}(\text{Ali})$ from $\text{Student}(\text{Ali})$ constitutes the logical fallacy of *affirming the consequent*. General students do not automatically possess teaching assistant roles.

---

### Task 3 — Interpret DL Operators over a Finite Model
#### Formal Model Definition
$$\Delta^{\mathcal{I}} = \{\text{Ali}, \text{Sara}, \text{DrAhmed}, \text{CS101}, \text{AI201}\}$$
$$\text{Student}^{\mathcal{I}} = \{\text{Ali}, \text{Sara}\}, \quad \text{Employee}^{\mathcal{I}} = \{\text{Sara}, \text{DrAhmed}\}$$
$$\text{Professor}^{\mathcal{I}} = \{\text{DrAhmed}\}, \quad \text{Course}^{\mathcal{I}} = \{\text{CS101}, \text{AI201}\}$$
$$\text{enrolledIn}^{\mathcal{I}} = \{(\text{Ali}, \text{CS101})\}, \quad \text{teaches}^{\mathcal{I}} = \{(\text{DrAhmed}, \text{CS101})\}$$

#### Exercise 3.1: Boolean Connective Results
- $\text{Student} \sqcap \text{Employee} = \text{Student}^{\mathcal{I}} \cap \text{Employee}^{\mathcal{I}} = \{\textbf{Sara}\}$
- $\text{Student} \sqcup \text{Professor} = \text{Student}^{\mathcal{I}} \cup \text{Professor}^{\mathcal{I}} = \{\textbf{Ali}, \textbf{DrAhmed}, \textbf{Sara}\}$
- $\neg \text{Course} = \Delta^{\mathcal{I}} \setminus \text{Course}^{\mathcal{I}} = \{\textbf{Ali}, \textbf{DrAhmed}, \textbf{Sara}\}$

#### Exercise 3.2: Vacuous Truth of Universal Restriction for Sara
- **Semantics:** $(\forall R.C)^{\mathcal{I}} = \{x \in \Delta^{\mathcal{I}} \mid \forall y ((x, y) \in R^{\mathcal{I}} \to y \in C^{\mathcal{I}})\}$.
- In the initial model, Sara has zero `enrolledIn` successors: $\{y \mid (\text{Sara}, y) \in \text{enrolledIn}^{\mathcal{I}}\} = \emptyset$.
- In classical logic, the implication $\text{False} \to P$ evaluates to **True**.
- Because there exist no successors $y$ that could violate the condition $y \in \text{Course}^{\mathcal{I}}$, the universal condition is satisfied **vacuously**. Every individual with zero outgoing relations satisfies any universal restriction on that role.

#### Exercise 3.3: Effect of Adding $\text{enrolledIn}(\text{Sara}, \text{AI201})$
- **$\exists \text{enrolledIn}.\text{Course}$:** Expands from `['Ali']` to `['Ali', 'Sara']` because Sara now has a concrete successor in `Course`.
- **$\forall \text{enrolledIn}.\text{Course}$:** Remains all domain members `['AI201', 'CS101', 'DrAhmed', 'Sara', 'Ali']`, since Sara's only successor is a course.

#### Exercise 3.4: Effect of Adding $\text{enrolledIn}(\text{Ali}, \text{DrAhmed})$
- **Prediction:** Ali will now be related to DrAhmed, who is *not* a member of `Course`. Consequently, it will no longer be true that *all* of Ali's successors are courses, so Ali will be expelled from $\forall \text{enrolledIn}.\text{Course}$.
- **Experimental Result:** Confirmed!
  - `forall enrolledIn.Course:` `['AI201', 'CS101', 'DrAhmed', 'Sara']` (Ali removed).
  - `exists enrolledIn.Course:` `['Ali', 'Sara']` (Ali retained because CS101 still satisfies the existential requirement).

---

### Task 4 — Check Restrictions against a Known Interpretation
#### Formal Subsumption Evaluation
Testing $C \sqsubseteq D$ evaluates whether $C^{\mathcal{I}} \setminus D^{\mathcal{I}} = \emptyset$.

#### Exercise 4.1: Model Comparison (Original vs. Updated)
- **Model State 1 (Original):**
  - $\text{Student} \sqsubseteq \exists \text{enrolledIn}.\text{Course}$: **`False`** (Counterexamples: `['Sara']`).
  - $\text{Student} \sqsubseteq \forall \text{enrolledIn}.\text{Course}$: **`True`** (Counterexamples: `[]`).
- **Model State 2 (With Sara Enrolled in AI201):**
  - $\text{Student} \sqsubseteq \exists \text{enrolledIn}.\text{Course}$: **`True`** (Counterexamples: `[]`).
  - $\text{Student} \sqsubseteq \forall \text{enrolledIn}.\text{Course}$: **`True`** (Counterexamples: `[]`).

#### Exercise 4.2: Model Construction Satisfying Both Axioms
- Domain: $\Delta^{\mathcal{I}} = \{\text{Ali}, \text{Sara}, \text{CS101}, \text{AI201}\}$
- Concepts: $\text{Student}^{\mathcal{I}} = \{\text{Ali}, \text{Sara}\}$, $\text{Course}^{\mathcal{I}} = \{\text{CS101}, \text{AI201}\}$
- Role: $\text{enrolledIn}^{\mathcal{I}} = \{(\text{Ali}, \text{CS101}), (\text{Sara}, \text{AI201})\}$
- **Verification:** Both axioms evaluate to `True` with empty counterexample sets.

#### Exercise 4.3: Why Universal Restriction Alone Does Not Guarantee Enrollment
The universal restriction $\forall R.C$ imposes a constraint on existing successors without asserting that any successor must exist ($\forall y ((x, y) \in R^{\mathcal{I}} \to y \in C^{\mathcal{I}})$). As established in Exercise 3.2, an individual with zero enrollments satisfies $\forall \text{enrolledIn}.\text{Course}$ vacuously. Therefore, $\forall$ alone permits students taking zero courses; enforcing actual participation requires an existential restriction $\exists R.C$ (or cardinal restriction $\ge 1 R$).

---

### Task 5 — Use Equivalence to Classify a ResearchStudent
#### Equivalence Definition
$$\text{ResearchStudent} \equiv \text{Student} \sqcap \exists \text{worksOn}.\text{Project}$$

#### Exercise 5.1: Verification of ResearchStudent(Ali)
1. **Condition 1 ($\text{Ali} \in \text{Student}^{\mathcal{I}}$):** True (asserted and confirmed in closure).
2. **Condition 2 ($\exists y ((\text{Ali}, y) \in \text{worksOn}^{\mathcal{I}} \land y \in \text{Project}^{\mathcal{I}})$):** True (Ali worksOn P1, and P1 is a Project).
- **Result:** `ResearchStudent(Ali) follows? True`.

#### Exercise 5.2: Added $\text{Project}(\text{P2})$ and $\text{worksOn}(\text{Sara}, \text{P2})$
- Sara is a `Student` (via closure from `TeachingAssistant`) and `worksOn(Sara, P2)` where `P2` is a `Project`.
- **Updated Classified ResearchStudents:** `['Ali', 'Sara']`.

#### Exercise 5.3: Reverse Entailment & Anonymous Existential Witnesses
If $\text{ResearchStudent}(\text{Hina})$ is explicitly asserted in the ABox without an explicit `worksOn` assertion:
1. **What follows logically?** By bidirectional equivalence ($\text{ResearchStudent} \sqsubseteq \text{Student} \sqcap \exists \text{worksOn}.\text{Project}$):
   - $\text{Student}(\text{Hina})$ is entailed.
   - $\exists \text{worksOn}.\text{Project}(\text{Hina})$ is entailed: there **must exist** some project $w \in \Delta^{\mathcal{I}}$ such that $(\text{Hina}, w) \in \text{worksOn}^{\mathcal{I}}$ and $w \in \text{Project}^{\mathcal{I}}$.
2. **Must the project have a known name?** **NO.** In Description Logic, this entity is an **anonymous existential witness**. Under open-world reasoning, the existence of an object does not require an explicit constant symbol or database row in the ABox. Production reasoners instantiate an un-named tableau node to satisfy the existential restriction.

---

### Task 6 — Inconsistency and Unsatisfiable Concepts
#### Disjointness Constraint
$$\text{Student} \sqcap \text{Employee} \sqsubseteq \bot$$

#### Exercise 6.1: Added $\text{Undergraduate} \sqsubseteq \text{Student}$, $\text{Undergraduate}(\text{Hina})$, $\text{Employee}(\text{Hina})$
- Inference trace derives $\text{Student}(\text{Hina})$ from $\text{Undergraduate}(\text{Hina})$ via $\text{Undergraduate} \sqsubseteq \text{Student}$.
- Hina now possesses both `Student` and `Employee`.
- **Detected Clash:** `[('Hina', ('Employee', 'Student'))]`.

#### Exercise 6.2: Concept Unsatisfiability Analysis
Consider:
$$\text{Student} \sqsubseteq \text{Person} \quad \text{and} \quad \text{Student} \sqsubseteq \neg \text{Person}$$
1. **Is Student satisfiable?** **NO, Student is unsatisfiable ($\text{Student} \equiv \bot$).**
   - Any instance $x \in \text{Student}^{\mathcal{I}}$ must satisfy $x \in \text{Person}^{\mathcal{I}}$ and $x \in (\Delta^{\mathcal{I}} \setminus \text{Person}^{\mathcal{I}})$.
   - Since $\text{Person}^{\mathcal{I}} \cap (\Delta^{\mathcal{I}} \setminus \text{Person}^{\mathcal{I}}) = \emptyset$, no such $x$ can exist.
   - Therefore, $\text{Student}^{\mathcal{I}} = \emptyset$ in all models.
2. **Could the TBox still have a model?** **YES.**
   - A TBox is consistent if there exists at least one interpretation satisfying all its axioms.
   - The model $\Delta^{\mathcal{I}} = \{d_1\}$, $\text{Person}^{\mathcal{I}} = \{d_1\}$, and $\text{Student}^{\mathcal{I}} = \emptyset$ satisfies $\emptyset \subseteq \{d_1\}$ and $\emptyset \subseteq \emptyset$.
   - A TBox with unsatisfiable concepts remains consistent as long as the empty concept is never populated.

#### Exercise 6.3: Why Adding `Student(Ali)` Makes the Entire KB Inconsistent
- Asserting $\text{Student}(\text{Ali})$ requires that the concrete domain individual $\text{Ali}^{\mathcal{I}} \in \text{Student}^{\mathcal{I}}$, forcing $|\text{Student}^{\mathcal{I}}| \ge 1$.
- However, the TBox mathematically requires $|\text{Student}^{\mathcal{I}}| = 0$.
- Because no mathematical interpretation can simultaneously satisfy $|\text{Student}^{\mathcal{I}}| = 0$ and $|\text{Student}^{\mathcal{I}}| \ge 1$, the set of valid models becomes empty ($\text{Mod}(\mathcal{K}) = \emptyset$).
- Thus, the **entire Knowledge Base becomes inconsistent**.

---

### Task 7 — Open-World Reasoning and Anonymous Witnesses
#### Epistemic Querying Semantics
- `ENTAILED`: True in every model of $\mathcal{K}$.
- `UNKNOWN`: True in some models, false in others (neither entailed nor refuted).

#### Exercise 7.1: Entailment vs. Non-Entailment of Role Assertions
Given $\text{Student} \sqsubseteq \exists \text{enrolledIn}.\text{Course}$ and $\text{Student}(\text{Ali})$:
- **Entailed:** $\exists \text{enrolledIn}.\text{Course}(\text{Ali})$ is strictly entailed. In every model, Ali has at least one course successor.
- **Why `enrolledIn(Ali, CS101)` is not entailed:** Entailment requires truth in *all* models. There are valid models where Ali is enrolled in `AI201` or another course but not in `CS101`. Because there exists at least one model where `enrolledIn(Ali, CS101)` is false, it is not logically entailed. Its truth value is **UNKNOWN**.

#### Exercise 7.2: Absence of Unique Name Assumption (UNA)
- **Do `Ali` and `Student123` necessarily denote different objects?** **NO.**
- In Description Logic and OWL, distinct constant symbols can refer to the same domain entity ($\text{Ali}^{\mathcal{I}} = \text{Student123}^{\mathcal{I}}$).
- **Required Assertion:** To force them to be distinct, an explicit assertion is required:
  $$\text{DifferentIndividuals}(\text{Ali}, \text{Student123}) \quad \text{or} \quad \text{Ali} \neq \text{Student123}$$

#### Exercise 7.3: Empty Role Facts vs. Negation
Under the Open-World Assumption, absence of knowledge does not mean knowledge of absence. An empty role list simply indicates that no enrollment facts have been asserted yet. It is not evidence for $\neg \exists \text{enrolledIn}.\text{Course}(\text{Ali})$. To entail the negation, the ontology would require an explicit negative assertion or axiom such as $\text{Ali} : \forall \text{enrolledIn}.\bot$.

---

### Task 8 — Mini University Ontology Challenge: Academic Library System
#### Ontology Specification & Labeling
1. **Concepts ($\ge 5$):** `LibraryItem` (TBox), `Book` (TBox), `JournalArticle` (TBox), `Patron` (TBox), `FacultyPatron` (TBox), `UndergradPatron` (TBox), `ActiveBorrower` (TBox, Defined), `RestrictedBorrower` (TBox).
2. **Roles ($\ge 2$):** `borrows` (TBox), `authoredBy` (TBox).
3. **Named Individuals ($\ge 4$):** `Alice`, `Bob`, `Charlie`, `Book_AI101`, `Journal_DL2026` (ABox).
4. **Subclass Axioms ($\ge 3$):**
   - $\text{Book} \sqsubseteq \text{LibraryItem}$ (TBox)
   - $\text{JournalArticle} \sqsubseteq \text{LibraryItem}$ (TBox)
   - $\text{FacultyPatron} \sqsubseteq \text{Patron}$ (TBox)
   - $\text{UndergradPatron} \sqsubseteq \text{Patron}$ (TBox)
5. **Equivalence Definition ($\sqcap$ and $\exists$):**
   $$\text{ActiveBorrower} \equiv \text{Patron} \sqcap \exists \text{borrows}.\text{LibraryItem}$$
6. **Universal Restriction:**
   $$\text{RestrictedBorrower} \sqsubseteq \forall \text{borrows}.\text{Book}$$
7. **Class & Role Assertions:**
   - $\text{FacultyPatron}(\text{Alice}), \quad \text{UndergradPatron}(\text{Bob}), \quad \text{Patron}(\text{Charlie})$
   - $\text{Book}(\text{Book_AI101}), \quad \text{JournalArticle}(\text{Journal_DL2026})$
   - $\text{borrows}(\text{Alice}, \text{Book_AI101}), \quad \text{borrows}(\text{Bob}, \text{Journal_DL2026}), \quad \text{borrows}(\text{Charlie}, \text{Book_AI101})$

#### Derived Memberships & Step-by-Step Explanation
1. **Derivation 1 — $\text{Patron}(\text{Alice})$:**
   - Asserted: $\text{FacultyPatron}(\text{Alice})$.
   - Axiom: $\text{FacultyPatron} \sqsubseteq \text{Patron}$.
   - Result: Alice upwardly inherits membership in `Patron`.
2. **Derivation 2 — $\text{LibraryItem}(\text{Book_AI101})$:**
   - Asserted: $\text{Book}(\text{Book_AI101})$.
   - Axiom: $\text{Book} \sqsubseteq \text{LibraryItem}$.
   - Result: `Book_AI101` upwardly inherits membership in `LibraryItem`.
3. **Equivalence Classification — $\text{ActiveBorrower}$:**
   - Evaluated criteria: $\text{Patron} \sqcap \exists \text{borrows}.\text{LibraryItem}$.
   - `ActiveBorrowers` classified: `['Alice', 'Bob', 'Charlie']`.

#### Disjointness Inconsistency Case
- **Disjointness Axiom:** $\text{FacultyPatron} \sqcap \text{UndergradPatron} \sqsubseteq \bot$.
- **Conflicting Assertion Added:** $\text{UndergradPatron}(\text{Alice})$.
- **Clash Output:** `[('Alice', ('FacultyPatron', 'UndergradPatron'))]`.
- **Cause:** Alice is asserted as `FacultyPatron` and subsequently asserted as `UndergradPatron`. Because these two concepts are declared disjoint, their intersection is empty ($\bot$), making the ABox inconsistent.

#### Open-World Query Answering Results
- `Query 1 [Entailed]: Patron(Alice)                  => ENTAILED`
- `Query 2 [Unknown]:  FacultyPatron(Charlie)         => UNKNOWN`
- `Query 3 [Unknown]:  borrows(Alice, Journal_DL2026) => UNKNOWN`

#### Python Helper vs. Complete DL Reasoner Comparison
| Feature | Lightweight Python Helper | Production DL Reasoner (HermiT / Pellet / FaCT++) |
| :--- | :--- | :--- |
| **Logic Fragment** | Propositional subset + named subclass forward chaining. | Highly expressive Description Logics (e.g., $\mathcal{SROIQ}(\mathcal{D})$, OWL 2 DL). |
| **Reasoning Core** | Iterative forward-chaining over known ground assertions. | Sound and complete **Tableau / Hypertableau** algorithms. |
| **Existential Witnesses** | Bounded to explicit named individuals in the ABox. | Generates **anonymous existential witness trees** with cycle-blocking. |
| **Disjunctions ($\sqcup$)** | Evaluates only set union over known domain elements. | Non-deterministic **branching search** exploring alternative models. |
| **Complements ($\neg$)** | Closed set difference against a known finite domain. | Full open-world complement evaluation via **Negation Normal Form (NNF)**. |
| **General Concept Inclusions (GCIs)** | Only atomic subsumption ($A \sqsubseteq B$). | Arbitrary complex expressions on both sides of $\sqsubseteq$. |
| **Completeness** | Incomplete under open-world deduction. | Provably **sound and complete** across all possible interpretations. |

---

## 3. Post-Lab Synthesis
1. **Core Distinctions:** Description Logic clearly decouples the structural schema (TBox) from contingent instance data (ABox), enabling modular ontology engineering.
2. **Quantifier Semantics:** The universal restriction $\forall R.C$ operates conditionally and is vacuously true for individuals with zero successors, whereas $\exists R.C$ mandates the existence of at least one valid successor.
3. **Satisfiability vs. Consistency:** An ontology can contain unsatisfiable concepts ($C \sqsubseteq \bot$) and remain consistent as long as those concepts have no instances in the ABox. Asserting an instance of an unsatisfiable concept destroys all models and renders the entire knowledge base inconsistent.
4. **Open-World & UNA:** DL systems refrain from assuming that missing information is false (OWA) or that different names denote different objects (no UNA), making them uniquely suitable for open, distributed, and evolving web knowledge.
