# VQC Diabetes — Quantum Machine Learning Classifier

Classificador quântico variacional (VQC) aplicado ao dataset **Pima Indians Diabetes** para prever diagnóstico de diabetes usando um computador quântico real da IBM.

Projeto desenvolvido como desafio final do curso **Quantum Machine Learning for Fullstack Developers**.

---

## Resultado

| Métrica | Valor |
|---|---|
| Backend | ibm_marrakesh (hardware real) |
| Acurácia (simulador) | 72.4% |
| Acurácia (hardware IBM) | 72.4% |
| Queda por ruído | 0.0 p.p. |
| Baseline ingênuo | 65.0% |
| Qubits usados | 2 |
| Parâmetros treináveis | 12 |
| Execução | 04/06/2026 — ibm_marrakesh |

---

## O que é um VQC?

Um **Variational Quantum Classifier (VQC)** é o equivalente quântico de uma rede neural simples. Ele tem três partes:

1. **Feature map** — codifica dados clássicos como estados quânticos
2. **Ansatz** — circuito com parâmetros treináveis (os "pesos" do modelo)
3. **Medição** — lê o resultado do circuito e converte em previsão

O treinamento acontece classicamente: um otimizador (COBYLA) ajusta os parâmetros do circuito para minimizar o erro nas previsões.

---

## Dataset

**Pima Indians Diabetes Dataset** — 768 pacientes do sexo feminino com pelo menos 21 anos.

- **Features usadas:** `Glucose` e `BMI` (maior correlação com o diagnóstico)
- **Rótulos:** `+1` = diabética, `-1` = não-diabética
- **Desbalanceamento:** 65% não-diabética / 35% diabética
- **Pré-processamento:** zeros fisiologicamente impossíveis substituídos por mediana por classe; normalização para `[0, π]`

---

## Arquitetura do circuito

```
|0⟩ ── z_feature_map(x₀) ── efficient_su2(θ) ── measure ZZ
|0⟩ ── z_feature_map(x₁) ── efficient_su2(θ) ── measure ZZ
```

| Componente | Configuração |
|---|---|
| Feature map | `z_feature_map`, reps=1, 2 qubits |
| Ansatz | `efficient_su2`, reps=2, entanglement linear |
| Observável | Z⊗Z (autovalores ±1) |
| Otimizador | COBYLA, maxiter=50 |
| Amostras de treino | 50 (subsampling para viabilidade) |

---

## Estrutura do projeto

```
VQC_Diabetes_Explicado.ipynb    # notebook principal
diabetes.csv                    # dataset
resultado_vqc_diabetes.txt      # resultados salvos automaticamente
graficos_vqc_diabetes/
    01_convergencia.png         # curva de treinamento
    02_matriz_confusao.png      # matriz de confusão
    03_fronteira_decisao.png    # fronteira de decisão 2D
    04_simulador_vs_hardware.png # comparação simulador x hardware
```

---

## Como rodar

### 1. Instalar dependências

```bash
pip install qiskit qiskit-ibm-runtime scikit-learn seaborn matplotlib pylatexenc
```

### 2. Configurar credenciais IBM (opcional — para hardware real)

```python
from qiskit_ibm_runtime import QiskitRuntimeService

QiskitRuntimeService.save_account(
    channel="ibm_cloud",
    token="SUA_API_KEY",
    instance="SEU_CRN",
    overwrite=True
)
```

### 3. Executar o notebook

Abra `VQC_Diabetes_Explicado.ipynb` e execute todas as células em ordem.

Para rodar no hardware real, mude `RUN_ON_HARDWARE = True` na Seção 8.

---

## Dependências

| Biblioteca | Versão |
|---|---|
| qiskit | >= 2.1.0 |
| qiskit-ibm-runtime | >= 0.40.0 |
| scikit-learn | >= 1.0 |
| numpy | >= 1.24 |
| pandas | >= 2.0 |
| matplotlib | >= 3.7 |
| seaborn | >= 0.12 |
| pylatexenc | qualquer |

---

## Referências

- IBM Quantum Learning — *Quantum Machine Learning*
- Havlíček et al., *Supervised learning with quantum-enhanced feature spaces* (Nature, 2019)
- Smith, J.W. et al., *Using the ADAP learning algorithm to forecast the onset of diabetes mellitus* (1988)
- Qiskit Documentation — https://docs.quantum.ibm.com

---

## Contexto acadêmico

Desenvolvido como **Desafio Final** do Módulo de QML.

**Entregas:**
1. Execução do classificador em computador quântico ✅ — ibm_marrakesh, 72.4% acurácia
2. Relatório de entendimento sobre QML (em desenvolvimento)
