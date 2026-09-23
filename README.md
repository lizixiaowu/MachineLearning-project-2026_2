【English version below / Version française ci-dessous】

# Criteo 广告增量效果建模

## 项目简介

本项目使用 Criteo 广告数据，估计广告对不同用户转化概率的个体化影响，并根据预测 uplift 对用户进行分群。

## 研究目标

普通转化预测关注用户是否会购买；uplift modeling 进一步估计广告是否会改变用户的购买概率。本项目旨在识别广告可能带来正向、有限或负向影响的用户。

## 数据与挑战

数据集约有 1,398 万条记录，其中转化样本 40,774 条，整体转化率约为 0.29%，类别严重不平衡。下采样实验将训练集中的负样本与正样本比例调整为 5:1。

## 方法

项目使用 T-learner 方法，分别在控制组（`treatment = 0`）和实验组（`treatment = 1`）训练逻辑回归模型。对每位用户计算：

`uplift = P(conversion | treatment = 1) - P(conversion | treatment = 0)`

正值表示模型预测广告可能提高该用户的转化概率，负值表示模型预测广告可能降低该用户的转化概率。

## Notebook 文件说明

`versiondownsampling` notebook 对训练集进行下采样，并使用 K 折交叉验证训练逻辑回归模型。测试时，先将测试集下采样至负样本与正样本比例为 5:1，再在保留原始数据分布的测试集上评估，以分别观察平衡样本评估结果和接近真实场景的模型表现。

`versionfulldata` notebook 使用完整数据训练和评估模型，并根据 Youden’s J 统计量研究分类阈值的选择。

两个 notebook 都包含 uplift 模型的定量分析与评估。

## 实验结果

五折交叉验证中，控制组和实验组模型的 ROC-AUC 分别为 `0.9556 ± 0.0038` 和 `0.9521 ± 0.0009`。

在保留原始数据分布的测试集上，PPT 展示的阈值 0.5 结果中，两组模型的 ROC-AUC 分别为 `0.9496` 和 `0.9519`。由于转化样本稀少，Precision 较低，分别为 `0.0196` 和 `0.0287`。

Youden’s J 分析选出的分类阈值为 `0.3608` 和 `0.4013`。阈值调整后，召回率提高至约 `0.898`，但 Precision 随之下降。

## Uplift 分析

预测 uplift 的分位分析显示，最高分位组的平均预测 uplift 为正，最低分位组为负。这表明模型能够根据预测的广告响应对用户进行排序。

## 局限与后续验证

分位图展示的是模型预测的 uplift。若要验证其广告投放价值，需要在独立测试集上比较各 uplift 分位中实验组与控制组的实际转化表现，并确认 treatment 的分配机制支持因果解释。



# Criteo Advertising Uplift Modeling

## Overview

This project uses Criteo advertising data to estimate the individual impact of advertising on conversion probability and segment users by predicted uplift.

## Objective

Standard conversion prediction estimates whether a user will purchase. Uplift modeling further estimates whether advertising changes that user’s probability of purchasing. This project aims to identify users who may respond positively, weakly, or negatively to advertising.

## Data and Challenge

The dataset contains approximately 14 million records, including 40,774 conversions, for an overall conversion rate of about 0.29%. This creates severe class imbalance. In the downsampling experiment, the training set’s negative-to-positive ratio is adjusted to 5:1.

## Method

The project uses a T-learner approach, training separate logistic regression models for the control group (`treatment = 0`) and treatment group (`treatment = 1`). For each user, uplift is calculated as:

`uplift = P(conversion | treatment = 1) - P(conversion | treatment = 0)`

A positive value means the model predicts that advertising may increase the user’s conversion probability; a negative value means it may decrease it.

## Notebook Guide

The `versiondownsampling` notebook downsamples the training set and trains logistic regression models with K-fold cross-validation. For testing, it first downsamples the test set to a 5:1 negative-to-positive ratio, then evaluates the model on the test set with its original distribution. This allows comparison between a balanced evaluation set and one closer to real-world conditions.

The `versionfulldata` notebook trains and evaluates the model using the full dataset and investigates classification threshold selection using Youden’s J statistic.

Both notebooks include quantitative analysis and evaluation of the uplift model.

## Results

In five-fold cross-validation, the control and treatment models achieve ROC-AUC scores of `0.9556 ± 0.0038` and `0.9521 ± 0.0009`, respectively.

On the test set with its original distribution, the presentation reports ROC-AUC scores of `0.9496` and `0.9519` at a threshold of 0.5. Precision is low, at `0.0196` and `0.0287`, because conversions are rare.

Youden’s J selects classification thresholds of `0.3608` and `0.4013`. These thresholds raise recall to about `0.898` while reducing precision.

## Uplift Analysis

Uplift decile analysis shows positive average predicted uplift in the highest decile and negative average predicted uplift in the lowest deciles. This suggests that the model can rank users by their predicted response to advertising.

## Limitations and Further Validation

The decile chart shows predicted uplift. To validate its value for advertising decisions, compare observed conversions between treatment and control groups within each uplift decile on an independent test set, and confirm that the treatment assignment supports a causal interpretation.



# Modélisation de l’uplift publicitaire avec Criteo

## Présentation du projet

Ce projet utilise les données publicitaires de Criteo pour estimer l’effet individuel de la publicité sur la probabilité de conversion et segmenter les utilisateurs selon leur uplift prédit.

## Objectif

La prédiction classique de conversion estime si un utilisateur va acheter. La modélisation uplift estime également si la publicité modifie sa probabilité d’achat. Le projet vise à identifier les utilisateurs susceptibles de répondre positivement, faiblement ou négativement à la publicité.

## Données et défi

Le jeu de données contient environ 14 millions d’observations, dont 40 774 conversions, soit un taux global d’environ 0,29 %. Cela entraîne un fort déséquilibre des classes. Dans l’expérience de sous-échantillonnage, le rapport entre les échantillons négatifs et positifs de l’ensemble d’entraînement est ajusté à 5:1.

## Méthode

Le projet utilise l’approche T-learner et entraîne des modèles de régression logistique distincts pour le groupe témoin (`treatment = 0`) et le groupe traité (`treatment = 1`). Pour chaque utilisateur, l’uplift est calculé ainsi :

`uplift = P(conversion | treatment = 1) - P(conversion | treatment = 0)`

Une valeur positive signifie que le modèle prédit que la publicité peut augmenter la probabilité de conversion de l’utilisateur ; une valeur négative indique qu’elle peut la diminuer.

## Présentation des notebooks

Le notebook `versiondownsampling` sous-échantillonne l’ensemble d’entraînement et entraîne des modèles de régression logistique avec validation croisée K-fold. Pour les tests, il sous-échantillonne d’abord le jeu de test afin d’obtenir un rapport négatifs/positifs de 5:1, puis évalue le modèle sur le jeu de test conservant sa distribution d’origine. Cela permet de comparer une évaluation sur un jeu équilibré à une évaluation plus proche des conditions réelles.

Le notebook `versionfulldata` entraîne et évalue le modèle avec l’ensemble des données et étudie le choix du seuil de classification selon l’indice J de Youden.

Les deux notebooks comprennent une analyse quantitative et une évaluation du modèle uplift.

## Résultats

En validation croisée à cinq plis, les modèles témoin et traité obtiennent des scores ROC-AUC de `0.9556 ± 0.0038` et `0.9521 ± 0.0009`, respectivement.

Sur le jeu de test conservant sa distribution d’origine, la présentation rapporte des scores ROC-AUC de `0.9496` et `0.9519` avec un seuil de 0,5. La précision reste faible, à `0.0196` et `0.0287`, car les conversions sont rares.

L’indice J de Youden sélectionne les seuils de classification `0.3608` et `0.4013`. Ces seuils portent le rappel à environ `0.898`, tout en réduisant la précision.

## Analyse de l’uplift

L’analyse par déciles montre un uplift moyen prédit positif dans le décile supérieur et négatif dans les déciles inférieurs. Cela suggère que le modèle peut classer les utilisateurs selon leur réponse prédite à la publicité.

## Limites et validation complémentaire

Le graphique par déciles présente l’uplift prédit. Pour valider son utilité dans les décisions publicitaires, il faut comparer les conversions observées des groupes traité et témoin dans chaque décile sur un jeu de test indépendant, et vérifier que l’affectation au traitement permet une interprétation causale.

