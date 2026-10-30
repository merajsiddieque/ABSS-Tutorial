# Advanced Biometric Systems and Security (AI461)
## Assignment 1: User Authentication using Biometric Features

### Course Details
* **Course:** Advanced Biometric Systems and Security (AI461)
* **Assignment:** 1 (User Authentication using Biometric Features)
* **Inspired by:** Prof. Ajay Kumar, The Hong Kong Polytechnic University
* **Instructor / TA:** Ms. Priyanka Sharma / Dr. Praveen Kumar Chandaliya (DoAI, SVNIT)

---

### 1. Objective and Overview
The primary objective of this assignment is to evaluate the performance of a biometric verification system using high-dimensional biometric feature vectors. Performance is evaluated and compared across two primary matching distance criteria:
1. **(i) Euclidean Distance** (distance-based matching)
2. **(ii) Cosine Similarity** (directional/angular similarity matching)

The evaluation covers:
* **Part A:** Genuine Distribution
* **Part B:** Imposter Distribution
* **Part C:** False Accept Rate (FAR) Plot
* **Part D:** False Reject Rate (FRR) Plot
* **Part E:** Receiver Operating Characteristics (ROC) Curve
* **Part F:** Equal Error Rate (EER) and Decidability Index ($d'$)

---

### 2. Dataset Description and Preprocessing
* **Source File:** `biomet_data.csv`
* **Raw Matrix Dimension:** $(144, 1000)$ — acquired without column headers (`header=None`).
* **Entities:** $100$ users, each contributing $10$ biometric sample vectors:
  - Samples $1$ to $5$: Acquired during enrollment (training set).
  - Samples $6$ to $10$: Acquired $6$ months after enrollment (testing set / probe).
* **Feature Dimension:** $144$ features per biometric image vector.
* **Reshaped Working Tensor:** Dimensions $(100 \text{ users}, 10 \text{ samples}, 144 \text{ features})$.

---

### 3. Enrollment Protocol and Matching Strategy
1. **Enrolled Template Generation:**
   For each user $u \in \{1, \dots, 100\}$, an enrolled template vector $\mathbf{T}_u \in \mathbb{R}^{144}$ is synthesized by computing the centroid (mean feature vector) of the first 5 enrollment samples:
   $$\mathbf{T}_u = \frac{1}{5} \sum_{i=1}^{5} \mathbf{x}_{u, i}^{\text{train}}$$

2. **Test Protocol:**
   Each of the remaining 5 test samples $\mathbf{x}_{u, j}^{\text{test}}$ ($j \in \{1, \dots, 5\}$) is matched against all 100 enrolled templates:
   - **Genuine Comparisons:** Test sample $\mathbf{x}_{u, j}^{\text{test}}$ matched with $\mathbf{T}_u$ (same user).
     $$\text{Total Genuine Comparisons} = 100 \times 5 = 500$$
   - **Imposter Comparisons:** Test sample $\mathbf{x}_{u, j}^{\text{test}}$ matched with $\mathbf{T}_v$ where $v \neq u$ (cross-user claims).
     $$\text{Total Imposter Comparisons} = 100 \times 5 \times 99 = 49{,}500$$

3. **Distance & Similarity Metrics:**
   - **Euclidean Distance:**
     $$d(\mathbf{x}, \mathbf{y}) = \|\mathbf{x} - \mathbf{y}\|_2 = \sqrt{\sum_{k=1}^{144} (x_k - y_k)^2}$$
     *(Lower distance indicates a more genuine match)*
   - **Cosine Similarity:**
     $$s(\mathbf{x}, \mathbf{y}) = \frac{\mathbf{x} \cdot \mathbf{y}}{\|\mathbf{x}\|_2 \|\mathbf{y}\|_2}$$
     *(Higher similarity indicates a more genuine match)*

---

### 4. Mathematical Definitions of Evaluation Metrics
* **False Accept Rate (FAR):** Proportion of imposter comparisons incorrectly accepted as genuine under operating threshold $\eta$:
  - Distance metric: $\text{FAR}(\eta) = \frac{1}{N_{\text{imp}}} \sum_{i=1}^{N_{\text{imp}}} \mathbb{I}(d_i \le \eta)$
  - Similarity metric: $\text{FAR}(\eta) = \frac{1}{N_{\text{imp}}} \sum_{i=1}^{N_{\text{imp}}} \mathbb{I}(s_i \ge \eta)$

* **False Reject Rate (FRR):** Proportion of genuine comparisons incorrectly rejected as imposter under operating threshold $\eta$:
  - Distance metric: $\text{FRR}(\eta) = \frac{1}{N_{\text{gen}}} \sum_{i=1}^{N_{\text{gen}}} \mathbb{I}(d_i > \eta)$
  - Similarity metric: $\text{FRR}(\eta) = \frac{1}{N_{\text{gen}}} \sum_{i=1}^{N_{\text{gen}}} \mathbb{I}(s_i < \eta)$

* **Genuine Accept Rate (GAR):**
  $$\text{GAR}(\eta) = 1 - \text{FRR}(\eta)$$

* **Equal Error Rate (EER):**
  The unique operating point where the False Accept Rate equals the False Reject Rate ($\text{FAR} = \text{FRR}$). Linear interpolation between threshold sample points is utilized to compute the exact crossing point.

* **Decidability Index ($d'$):**
  Measures the statistical separation between the genuine and imposter score distributions:
  $$d' = \frac{|\mu_{\text{gen}} - \mu_{\text{imp}}|}{\sqrt{\frac{1}{2}(\sigma_{\text{gen}}^2 + \sigma_{\text{imp}}^2)}}$$
  where $\mu_{\text{gen}}, \mu_{\text{imp}}$ are the score means and $\sigma_{\text{gen}}^2, \sigma_{\text{imp}}^2$ are the corresponding variances. A higher $d'$ value indicates greater distinctiveness and superior biometric separability.

---

### 5. Summary of Results (Part F)

| Evaluation Metric | (i) Euclidean Distance | (ii) Cosine Similarity | Performance Assessment |
| :--- | :---: | :---: | :--- |
| **Genuine Score Mean ($\mu_{\text{gen}}$)** | $291.6547$ | $0.9895$ | Expected range for verified users |
| **Genuine Score Variance ($\sigma^2_{\text{gen}}$)** | $18350.1314$ | $0.000120$ | Cosine shows tight clustering |
| **Imposter Score Mean ($\mu_{\text{imp}}$)** | $703.8264$ | $0.9604$ | Imposters clearly separated |
| **Imposter Score Variance ($\sigma^2_{\text{imp}}$)** | $91078.4397$ | $0.000337$ | Euclidean exhibits high spread |
| **Equal Error Rate (EER)** | **11.53%** | **8.38%** | **Cosine is 3.15% lower (Better)** |
| **EER Operating Threshold** | **429.4316** | **0.9790** | Optimal decision boundary |
| **Decidability Index ($d'$)** | **1.7621** | **1.9257** | **Cosine has higher $d'$ (Better)** |

---

### 6. Performance Evaluation: Best Matching Criterion

#### Question: Which matching distance criteria, in your opinion, perform the best?
**Answer: Cosine Similarity performs best.**

#### Justifications:
1. **Lower Equal Error Rate:**
   Cosine similarity achieves an EER of **8.38%**, outperforming Euclidean distance (**11.53%**) by a significant margin of **3.15%**.
2. **Higher Decidability Index ($d'$):**
   Cosine similarity achieves a higher $d'$ value (**1.9257** vs. **1.7621**), demonstrating wider statistical separation between genuine and imposter distributions relative to their internal variances.
3. **ROC Superiority:**
   The Receiver Operating Characteristics (GAR vs. FAR) curve for Cosine similarity strictly dominates the Euclidean ROC curve across all operational security points, offering higher genuine recognition rates at lower false alarm rates.
4. **Magnitude Invariance in High Dimensions:**
   In $144$-dimensional feature space, raw vector magnitudes can vary due to environmental noise, sensor gain, or acquisition conditions across the $6$-month gap. Euclidean distance is sensitive to absolute magnitude offsets, whereas Cosine similarity isolates directional angular alignment, ensuring robust and invariant verification.

---

### 7. File Structure & Serial Execution
* `ABSS-Tut1 (1).ipynb`: Clean, fully executed Jupyter Notebook containing:
  - **Cell 0:** Assignment Header (Markdown)
  - **Cell 1:** Library Imports and Portable CSV Data Loading
  - **Cell 2:** Data Reshaping, 5/5 Enrollment Split, and Centroid Template Generation
  - **Cell 3:** Matching Score Computations (500 Genuine & 49,500 Imposter scores)
  - **Cell 4:** Performance Calculation Engine (FAR, FRR, EER, $d'$)
  - **Cell 5:** Part A & B Plots (Genuine & Imposter Distributions)
  - **Cell 6:** Part C & D Plots (FAR & FRR Curves with marked EER threshold)
  - **Cell 7:** Part E Plots (Receiver Operating Characteristics - Full & Zoomed)
  - **Cell 8:** Part F Output Table (EER & Decidability Index)
  - **Cell 9:** Conclusion and Performance Evaluation (Markdown)
* `biomet_data.csv`: Biometric feature dataset ($144 \times 1000$).
* `README.md`: Implementation documentation and grading guide.

---

### 8. Instructions to Run
1. Ensure the required dependencies are installed:
   ```bash
   pip install numpy pandas matplotlib scipy
   ```
2. Place `biomet_data.csv` in the same directory as `ABSS-Tut1 (1).ipynb` (or in `/content/` if executing in Google Colab).
3. Open `ABSS-Tut1 (1).ipynb` in Jupyter Notebook, VS Code, or Google Colab, and select **Run All Cells**. All cells will execute in sequential order without errors.

