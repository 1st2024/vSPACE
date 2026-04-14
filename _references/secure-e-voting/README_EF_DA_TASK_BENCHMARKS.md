# vSPACE FormalBench EF-DA-TASK Kaggle Benchmark Notebooks

## Overview

This directory contains Kaggle benchmark notebooks for evaluating Lean 4 proof generation on Executive Functions (EF) cognitive abilities, specifically the **Directing Action (DA)** sub-abilities as defined in the DeepMind AGI cognitive framework.

## Files

| Notebook | EF-DA Subability | Task Focus | Difficulty Categories | Tasks |
|----------|-----------------|------------|---------------------|-------|
| `00_FormalBench_EF_DA_TASK_Unified.ipynb` | **Both** | Complete framework | All 6 | 1,239 |
| `01_Planning_MultiStep_Proofs.ipynb` | Planning (§7.8.1) | Multi-step proof construction | All | 1,239 |
| `02_Goal_Setting_Theorem_Statements.ipynb` | Goal Setting (§7.8.2) | Theorem formulation | All | 1,239 |
| `03_Planning_Cryptographic_Proofs.ipynb` | Planning (§7.8.1) | Cryptographic verification | coq_based, basic_core | 500 |
| `04_Goal_Setting_Proof_Strategies.ipynb` | Goal Setting (§7.8.2) | Strategy selection | autonomous, augmented | 550 |
| `05_Planning_Decision_Tree_Search.ipynb` | Planning (§7.8.1) | Tactic decision trees | advanced, autonomous | 381 |
| `06_Goal_Setting_Working_Memory.ipynb` | Goal Setting (§7.8.2) | Proof state management | All | 1,239 |

## Cognitive Framework

### Executive Functions (EF) Track

Based on: **Measuring Progress Toward AGI: A Cognitive Framework** (DeepMind, 2025)

**Reference**: https://storage.googleapis.com/deepmind-media/DeepMind.com/Blog/measuring-progress-toward-agi/measuring-progress-toward-agi-a-cognitive-framework.pdf

#### §7.8 Executive Functions

Higher-order cognitive abilities that enable goal-directed behavior by regulating and orchestrating thoughts and actions (Diamond, 2013).

#### §7.8.1 EF-DA-TASK: Planning

**Definition**: The ability to formulate sequences of future actions to achieve specific goals (Owen, 1997).

**Key Components**:
- **Decision Tree Construction**: Building and pruning possible action sequences (Mattar and Lengyel, 2022)
- **Forward Planning**: Anticipating state changes from actions
- **Multi-step Problem Solving**: Coordinating sequences of operations
- **Long-term Goal Achievement**: Maintaining focus through extended tasks

**In Lean 4 Proofs**:
- Selecting appropriate tactics
- Managing subgoals
- Backtracking when tactics fail
- Completing all proof branches

#### §7.8.2 EF-DA-TASK: Goal Setting/Maintenance

**Definition**: The ability to set and maintain goals to organize and direct action (Buschman and Miller, 2014; Dickinson and Balleine, 1994).

**Key Components**:
- **Goal Formulation**: Clearly stating objectives
- **Goal Hierarchy Management**: Organizing subgoals
- **Working Memory**: Maintaining goal states and assumptions
- **Persistence**: Maintaining focus through distractions

**In Lean 4 Proofs**:
- Formulating correct theorem statements
- Tracking proof state
- Managing assumptions
- Maintaining subgoal focus

## Dataset: v_train_extended.csv

**Source**: Generated from vSPACE proof task generator

**Statistics**:
- **Total proof tasks**: 1,239
- **File size**: 2.79 MB
- **Lines**: 68,647 (with multiline Lean4 code)

**Difficulty Distribution**:
| Category | Count | Description |
|----------|-------|-------------|
| `basic` | 108 | Original Verina basic programming |
| `advanced` | 81 | Original Verina advanced programming |
| `coq_based` | 250 | Converted from Coq/ElectionGuard proofs |
| `basic_core` | 250 | F-001 to F-012 (ElectionGuard Core2) |
| `autonomous` | 300 | F-100 to F-103, F-109, F-110 (vSPACE) |
| `augmented` | 250 | F-104 to F-108 (Azure/Entra) |

**Features**:
- Formal verification of cryptographic protocols
- Election system correctness proofs
- Zero-knowledge proof verification
- Modular arithmetic, ElGamal encryption, BBS signatures
- SAAC protocol, credential binding, one-show enforcement

## Kaggle Benchmarks SDK

**Reference**: https://github.com/Kaggle/kaggle-benchmarks/blob/ci/cookbook.md

### Task Definition Pattern

```python
import kaggle_benchmarks as kbench

@kbench.task(name="ef_da_task", store_task=True)
def ef_da_task(llm, id: str, description: str, lean_code: str, difficulty: str) -> dict:
    """
    EF-DA-TASK: Measures Executive Functions in Lean 4 proof generation.
    
    Args:
        llm: Language model interface
        id: Task identifier
        description: Natural language description
        lean_code: Lean 4 code with `sorry` placeholder
        difficulty: Difficulty category
    
    Returns:
        Dictionary with EF-DA metrics
    """
    prompt = f"""Complete the following Lean 4 proof:
    
    {description}
    
    ```lean4
    {lean_code}
    ```
    
    Replace `sorry` with a complete formal proof."""
    
    response = llm.prompt(prompt, max_tokens=2048)
    
    # Extract EF-DA metrics
    tactics_count = count_tactics(response)
    proof_valid = validate_proof(response)
    
    return {
        'task_id': id,
        'ef_da_subability': 'Planning',
        'ef_da_task': 'Multi-Step Proof',
        'tactics_count': tactics_count,
        'proof_valid': proof_valid,
        'planning_efficiency': calculate_efficiency(tactics_count, difficulty)
    }
```

### Evaluation Pipeline

```python
# Load dataset
df = pd.read_csv('v_train_extended.csv')

# Run benchmark
results = ef_da_task.evaluate(
    stop_condition=lambda runs: len(runs) == len(df),
    llm=[llm],
    evaluation_data=df,
    n_jobs=4,
    timeout=300
)

# Analyze
analysis = analyze_results(pd.DataFrame(results))
plot_ef_da_results(analysis)
```

## Metrics

### Planning Metrics (§7.8.1)

| Metric | Description | Calculation |
|--------|-------------|-------------|
| **Tactics Count** | Number of tactics used | Count of tactic keywords |
| **Structure Validity** | Proof has correct structure | `theorem` + `:=` + no `sorry` |
| **Subgoals Managed** | Explicit subgoal handling | Count of `case`, `·`, `next` |
| **Planning Efficiency** | Tactics vs. expected ratio | `1.0 - |1.0 - (actual/expected)|` |
| **Completion Rate** | % proofs without `sorry` | `(total - with_sorry) / total` |

### Goal Setting/Maintenance Metrics (§7.8.2)

| Metric | Description | Calculation |
|--------|-------------|-------------|
| **Type Signatures** | Variables have types | Check for `: Type`, `: ℕ`, etc. |
| **Quantifiers** | Appropriate ∀/∃ usage | Match description semantics |
| **Structure** | Theorem has correct form | `theorem` + name + `:` |
| **Goal Clarity** | Statement specificity | Length-based heuristic |
| **Goal Maintenance** | Overall goal quality | Average of above metrics |

## Usage

### Running Individual Notebooks

```bash
cd _references/secure-e-voting

# Run unified benchmark
jupyter execute 00_FormalBench_EF_DA_TASK_Unified.ipynb

# Run specific task notebook
jupyter execute 01_Planning_MultiStep_Proofs.ipynb
```

### Kaggle Submission

1. **Create Kaggle Notebook**: Copy notebook code to Kaggle kernel
2. **Upload Dataset**: Upload `v_train_extended.csv` to Kaggle dataset
3. **Configure LLM**: Select appropriate language model
4. **Run Evaluation**: Execute all cells
5. **Submit Results**: Submit to "Measuring AGI" competition

### Expected Runtime

| Notebook | Tasks | Est. Time (4 jobs) | Est. Time (8 jobs) |
|----------|-------|-------------------|-------------------|
| 00_Unified | 1,239 | ~60 min | ~30 min |
| 01_Planning_MultiStep | 1,239 | ~60 min | ~30 min |
| 02_Goal_Setting_Theorem | 1,239 | ~40 min | ~20 min |
| 03_Planning_Crypto | 500 | ~25 min | ~13 min |
| 04_Goal_Setting_Strategy | 550 | ~28 min | ~14 min |
| 05_Planning_Decision_Tree | 381 | ~20 min | ~10 min |
| 06_Goal_Setting_Working_Memory | 1,239 | ~60 min | ~30 min |

**Note**: Times assume 2048 tokens max, 0.2 temperature, 2s per task average.

## Output Files

After execution, each notebook generates:

1. **Results CSV**: `results_{notebook_name}.csv`
2. **Analysis JSON**: `analysis_{notebook_name}.json`
3. **Visualization PNG**: `ef_da_task_results.png`
4. **Log File**: `benchmark_{timestamp}.log`

## References

### Academic

- Diamond, A. (2013). Executive functions. *Annual Review of Psychology*, 64, 135-168.
- Owen, A. M. (1997). Cognitive planning in humans: Neuropsychological, neuroanatomical and neuropharmacological perspectives. *Progress in Neurobiology*, 52(6), 433-450.
- Buschman, T. J., & Miller, E. K. (2014). Goal-direction and top-down control. *Philosophical Transactions of the Royal Society B*, 369(1655), 20130471.
- Dickinson, A., & Balleine, B. (1994). Motivational control of goal-directed action. *Animal Learning & Behavior*, 22(1), 1-18.
- Mattar, M. G., & Lengyel, M. (2022). Planning as inference in a hierarchical model. *Nature Neuroscience*, 25, 1197-1208.

### Technical

- DeepMind AGI Cognitive Framework: https://www.kaggle.com/competitions/kaggle-measuring-agi
- Kaggle Benchmarks SDK Cookbook: https://github.com/Kaggle/kaggle-benchmarks/blob/ci/cookbook.md
- Lean 4 Documentation: https://leanprover.github.io/
- Mathlib4: https://github.com/leanprover-community/mathlib4
- vSPACE Repository: https://github.com/vSpaceVote/vSPACE

## License

MIT License (aligned with vSPACE and ElectionGuard ecosystem)

---

**Generated**: 2026-04-14  
**Version**: 1.0.0  
**Contact**: vSPACE Research Team
