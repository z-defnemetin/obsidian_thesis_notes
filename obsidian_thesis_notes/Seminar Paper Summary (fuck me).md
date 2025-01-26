Causal Transformer for Estimating Counterfactual Outcomes 
-----------------------------
**Counterfactual Outcome**: the outcome that an individual would have experienced if they had received a particular treatment.
"What would have happened if I weren't in this group but another one?"

Goal: To develop a Casual Transformer model that can estimate counterfactual outcomes over time (for both one- and multi-step-ahead predictions) from longitudinal observational data. It uses a transformer architecture to capture long-range dependencies.

Research Gap: Tackling the counterfactural outcome from longitudinal data issue with a transformer-based architecture.

In order to decide on a certain medicine, experts need to know the outcomes of the previously followed treatments. Randomised Controlled Trials (RCTs) are usually used for estimating the effects of treatments, though costly and possibly unethical. The use of electronic health records or other forms of observational data would offer a solution.

Static settings have been researched, but not longitudinal settings. But all electronic health records basically only collect longitudinal data. Estimating counterfactual outcomes over time is hard, because mostly, they just don't happen. On the other hand, estimating these outcomes with typical ML methods lead to generalisation errors that add up through time, and might be biased (with multiple step ahead prediction).
SOTA models use different versions of LSTMs (RMSNs, CRNs) to estimate counterfactual outcomes over time, but they mostly cannot capture long-range dependencies. To tackle this, the authors developed a Causal Transformer that combines a transformer architecture with a novel Counterfactual Domain Confusion (CDC) loss.

Transformer:
Three separate transformer subnetworks are combined and employed for processing time-varying covariates, past treatments, and past outcomes to obtain a joint network with cross attention in between. Every subnetwork is extended with masked multi-head self-attention, shared trainable relative positional encoding, and attentional dropout.

CDC loss:
An adversarial balancing objective is solved to balance the representations to be predictive of outcomes and non-predictive of the current treatment assignment -the latter is necessary to reduce the generalisation error and confounding bias.

Problem Formulation:
Let $i$ refer to some patient and with health trajectories that span time steps
$t = 1, . . . , T^{(i)}$. For each time step $t$ and each patient $i$, we have the following: $d_x$ time-varying covariates $X^{(i)}_{t}$ ∈ $R^{d_{x}} (e.g. blood pressure and heart rate);$ $d_{a}$ categorical treatments $A^{(i)}_{t}∈ {a_1, . . . , a_{d_{a}}}$; and $d_{y}$ out-comes $Y^(i)_t ∈ R^{d_y}$. Treatments are modeled as categorical variables. Static covariates that describe a patient (such as sex, age, gender) are also recorded. The goal can be formulated as: estimate the future counterfactual outcomes $Y_{t+\tau}$ after a treatment intervention $a_{t:t+\tau-1}$ is applied for a given patient history $H_{t}$. This problem can be formulated as a function of these variables, and calculated with traditional machine learning methods. However, it is biased because not only the treatment interventions are influenced by the outcome, but also future covariates.

4. Causal Transformer:
The CT has three separate transformer subnetworks that processes a different sequence as input:
* Past time varying covariates
* Past outcomes
* Past treatments before the intervention

Since the aim is to estimate the outcome after the intervention, the future treatment assignment is also given to the model as input. Additionally, the predictions of (past) outcomes are autoregressively fed to the model, starting at the prediction origin. For this, the two treatment and outcome sequences are concatenated. The static covariates are fed into all subnetworks.
The model outputs a sequence of representations $\PHI_{t+\tau-1} = (\PHI_1, ..., \PHI_{t+\tau-1})$ that are treatment-invariant.

Positional Encodings:
The Positional Encodings are used to preserve information about the order of the hidden states. This is useful in medical contexts as it allows the model to understand the side effect of a treatment based on the order that it happened, and to differentiate between the effects of different treatment types.
A set of vectors $a_{ij}^{V}, a_{ij}^{V} \e \R^{d_{qkv}}$ is used to model the information about the relative positions in the input at time steps j and i. The formalization (11, 12 in paper) ensures that relative encodings are obtained. So the distance between past or current position j and current position i are considered, and not the actual location. Relative PE is regarded are more robustThe current position i only attends to past information or itself, so no calculation that has i < j is necessary, and so there are only ... parameters to estimate. In the CT, the attention scores are shared across all the heads and blocks of the different sub-networks.

4.3 Training the Transformer
During training, the learned representations should be predictive of the next outcome but also non-predictive of the current treatment assignment. So the model should know which treatment assignment is applied, but its prediction should not be clouded by its effect. The adversarial method in that case is that while a model tries to predict the counterfactual outcome, the other model tries to predict which treatment might have caused that counterfactual outcome. Hence, the first model gets better and better and generating outcomes that are 'independent' from the treatment, and the second model tries to ruin the plans of the first model by predicting the treatment.

A CDC loss is developed to fit the adversarial nature of the loss function.

Stabilization: Exponential Moving Average is used 
Mini-batch augmentation with masking: allows for the training of a single model for both one- and multiple-step-ahead prediction. For this, training samples are duplicated with some parts of the series masked.

5. Experiments:
They used a mix of (semi-)synthetic and real datasets to test the accuracy of their model, though it is not possible to know the true counterfactual outcomes for the real datasets.