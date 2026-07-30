# USNCO Part I Style Card

This document specifies the characteristic voice, rhythm, and formatting of USNCO National Exam Part I questions. It captures how the exams *sound*, ensuring generated questions feel authentic.

## Stem Phrasing and Sentence Rhythm
- **Concise and Direct:** Sentences are short and devoid of fluff. Information is presented factually.
- **Direct Questions:** The stem typically ends with a direct question (e.g., "What is the...", "How much...", "Which..."). Avoid ending with fill-in-the-blanks unless necessary.
- **Neutral Register:** The tone is completely objective, dry, and third-person. Never use conversational language, "you", "we", or "calculate". Describe the scenario as a statement of fact or experimental observation.

## Conditions and Given Data
- **Inline Formatting:** Quantities and units are placed inline, often using the `\ch{}` macro (e.g., `\ch{10.00 g}`, `25.0~g`). 
- **Molar Masses/Constants:** Auxiliary data like molar masses are frequently provided immediately following the chemical name or formula in parentheses (e.g., `($M=146.15$)` or `($M = 44.01$)`).
- **Temperatures and Pressures:** Standard formats are used, e.g., $25^\circ\text{C}$, 298 K, 1.00 atm, 1.0 bar.

## Answer-Choice Conventions
- **Ordering:** Numerical answers are almost universally arranged in ascending order. Text answers are arranged logically or alphabetically.
- **Significant Figures:** Answer choices generally exhibit consistent significant figures (typically 3-4, matching the stem's precision).
- **Units:** Units are repeated in every choice (e.g., `3.61 g`, `6.39 g`) rather than factored into the stem, unless the stem explicitly asks for a dimensionless value.
- **Roman Numerals:** For complex evaluations, statements are numbered I, II (and III). Choices are standard: "(A) I only, (B) II only, (C) Both I and II, (D) Neither I nor II".

## Stem Length
There is **no length limit**. Stem length is dictated entirely by the chemistry: state the full setup the problem needs and stop. Never compress a system, drop a given, or simplify an experimental setup to hit a word count — a coupled multi-step problem is expected to run long, and brevity is never a reason to make a question easier. Real Part I stems span 6 to 233 words (median 42); the short ones are short because those questions are simple, not because length is capped.

## Exemplar Stem Openings
These are verbatim from past exams and are chosen to show the *demanding* end of the register — multi-step inference, experimental design, and error analysis. Match this level of reasoning load, not merely the phrasing.

- "The rate law for the oxidation of iodide to iodine by hydrogen peroxide in weakly acidic solution is studied by allowing the reaction to proceed in the presence of a fixed amount of sodium thiosulfate (which rapidly reduces iodine to iodide) and measuring the time to the first appearance of a blue color due to the starch-iodine complex. The following data are obtained on solutions made with the indicated volumes of reactant solutions and enough water to give a total volume of 10.0~mL. What are the reaction orders in \ch{H2O2}, \ch{I-}, and \ch{H+}?"
- "A student determines the rate constant for the reaction of iodide with hydrogen peroxide in strongly acidic solution ($\text{Rate} = k[\ch{H2O2}][\ch{I-}][\ch{H+}]$) by mixing 1.00~mL aliquots of solutions of known concentrations of potassium iodide, ascorbic acid, and hydrochloric acid with four drops of starch solution, then adding 1.00~mL of hydrogen peroxide solution and recording the time required for the solution to turn blue. A student uses half the stipulated amount of one of the solutions but does not realize it. Underdosing of which solutions will cause the value of the rate constant to be too high?"
- "The density of white tin is $7.28\text{ g cm}^{-3}$ and the density of gray tin is $4.75\text{ g cm}^{-3}$. The conversion of white tin to gray tin, $\ch{Sn}(s, \text{white}) \rightarrow \ch{Sn}(s, \text{gray})$, has a standard enthalpy of reaction of $-2.1\text{ kJ mol}^{-1}$ at 298 K. At 1 atm pressure, white tin and gray tin are in equilibrium at $13^\circ\text{C}$. Which best represents the phase diagram of tin near $13^\circ\text{C}$ and 1 atm?"
- "When a 1.000~g sample of \ch{CoCO3} is heated under vacuum, it forms 0.630~g of an oxide of cobalt. When this sample is exposed to air, it forms a second oxide of cobalt, and attains a mass of 0.675~g. What are the formulas of the first and second oxide of cobalt, respectively?"
