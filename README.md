# Working with Alpha Genome

This is an example repository for working with AlphaGenome.

I have been working on the article Ibrahim I. Taskiran et al. Cell-type-directed design of synthetic enhancers, focusing on the use of the DeepMEL2 model for the application of saturation mutagenesis -in silico mutagenesis- (ISM), obtaining, from a random DNA sequence (random but with probabilities of nucleotides per position obtained from the biological context), an endo specifically enhanced in melanoma cells. The work has consisted, broadly, in the use of the AlphaGenome model as a substitute for DeepMEL2.

More specifically, I initially created (I use genimi and copilot) two notebooks, which I describe:

## Notebook titled: Relevance_nucleotides_shap.ipynb.
https://colab.research.google.com/drive/1vP-hm1Rq-RV6u8DLNqGN9CaxsZTpN067?usp=sharing
I adapt the example presented in AlphaGneome quick start, selecting the genomic interval chr20: 3753000-3753400 and within it the central interval of length 256 bp. I follow the steps of the example from the AlphaGenome website to obtain the most relevant positions for the model prediction in the ISM interval.
Next I try to obtain the same result but using the Shap library (Shapley Additive exPlanations), to do this I extract the sequence from the previous ism interval, I provide the context for the application of Shap by randomly selecting 10,000 real sequences of length 256 bp belonging to chromosome 20. The steps followed are:
- ** Define functions for one-hot-encoding** of DNA sequences since this format of input variables is supported by Shap. 
- **  Define an additional function that receives the input in one-hot-encodig, converts it to a DNA string sequence**, sends that sequence to the AlphaGenome model and adds the prediction obtained for the sequence. The ontology sent to the model is 'EFO:0002067' corresponding to the k562 cell line while the selected output is DNAse (the example of the AlphaGenome quick start notebook has been followed).
- **  To reduce the execution time, the shpa.kmeans function is applied to the context sequences, selecting K=8.**
- **  The shap.KernelExplainer functions are applied**, with the parameters being the call function to AlphaGenome defined above and the context sequences and the explainer.shap_values ​​function being the parameter now the target dna sequence. The calculation took 42 minutes and I logged 36,260 calls to the predictor function.
- **  AlphaGenome DNAse predictions for the target sequence are calculated**.
#### The results graphs are presented:	
- ***  AlphaGenome prediction values ​​for DNASE of each position***, 
- ***  Values ​​of the contributions of each position obtained by the ism_score_variants function of AlphaGenome
- ***  Values ​​of the contributions of each position obtained with Shap.
#### A reasonable coincidence is observed between the values ​​of the contributions to the prediction obtained with AlphaGenome and those obtained with Shap.

### Detailed Analysis of SHAP vs ISM Results

The comparison of the top 10 indices from both the AlphaGenome (ISM) and SHAP methods reveals insights into their agreement and potential differences in identifying important nucleotide positions.

- **Common Indices:** There are 7 common indices (148, 149, 154, 155, 158, 160, and 161) present in the top 10 of both methods. This significant overlap suggests that both ISM and SHAP identify a core set of positions that are highly influential to the model's prediction.

- **Unique ISM Indices:** The indices 156, 162, and 157 are in the top 10 for ISM but not for SHAP. This might indicate that ISM is more sensitive to specific types of single-nucleotide changes or their impact on the central masked region, which are not as strongly captured by the SHAP values calculated with the chosen background data and prediction function.

- **Unique SHAP Indices:** The indices 166, 153, and 150 are in the top 10 for SHAP but not for ISM. This could imply that SHAP, considering the influence of features in the context of the background data, identifies positions whose importance is more apparent when viewed in relation to other sequences, or that their contribution is more distributed and captured differently by SHAP's marginal contribution calculation.

The lack of common indices between the top 10 AlphaGenome (ISM) contribution indices and the top 20 AlphaGenome prediction value indices further highlights that the interpretation of "importance" can differ depending on the method used (contribution to prediction change vs. absolute prediction value).

Adding this analysis to the introduction will provide a more nuanced understanding of the comparison between the two methods.
