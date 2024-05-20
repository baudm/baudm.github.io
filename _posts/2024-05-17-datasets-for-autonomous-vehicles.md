---
layout: distill
title: Democratizing autonomous driving research and development
description: Largely based on Dr. Holger Caesar's research talk at UP EEEI
tags:
giscus_comments: false
date: 2024-05-17
featured: false
chart:
  echarts: true

bibliography: 2024-05-17-datasets-for-autonomous-vehicles.bib

toc:
  - name: The case for autonomous vehicles
  - name: Road to full autonomy
  - name: Towards affordable large-scale autonomous driving datasets
    subsections:
      - name: "Ingredient 1: Unsupervised object discovery and self-supervised learning"
      - name: "Ingredient 2: Active learning"
      - name: "Ingredient 3: Foundation and Language Models"
  - name: Conclusion

# Ref: https://www.w3schools.com/howto/howto_css_responsive_iframes.asp
_styles: >
  figure:has(iframe) {
    position: relative;
    overflow: hidden;
    width: 100%;
    padding-top: 56.25%; /* 16:9 Aspect Ratio (divide 9 by 16 = 0.5625) */
  }
  .responsive-iframe {
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    width: 100%;
    height: 100%;
  }
---

Recent advances in computer vision and artificial intelligence paved the way for major leaps in machine perception and understanding--key elements of autonomous systems. As a result, autonomous vehicles (AVs), particularly self-driving cars, have risen in popularity in the past several years. Tesla is arguably the most well-known company in the AV space. Autonomous taxis--or robotaxis--are also gaining traction. Waymo (formerly the Google Self-Driving Car Project) and Motional (formerly nuTonomy) are two of the several companies competing in the AV industry.

## The case for autonomous vehicles

Enhanced safety is one of the primary motivations for developing AV technology.

In autonomous vehicles, the degree of autonomy is [classified into 6 levels](https://www.sae.org/standards/content/j3016_202104/). Level 0 pertains to the case where a human is 100% in control of the vehicle and no automation is employed. Meanwhile, the other end of the spectrum is Level 5--the fully autonomous case--where a human is merely a passenger in a driverless car. As of this writing, no system has achieved Level 5 yet.

Waymo was the [first to achieve Level 4](https://www.forbes.com/sites/alanohnsman/2024/01/08/waymos-robotaxis-are-hitting-the-highway-a-first-for-self-driving-cars) autonomy, or _high driving automation_. At Level 4, a vehicle is autonomous only for certain specific conditions or geographical areas, e.g. driving in the freeway, or backing out of a garage. In contrast, Level 5 AVs are fully autonomous 100% of the time, and should be able to seamlessly cooperate with each other, enhancing passenger safety and reducing--if not totally eliminating--human-caused accidents.

## Road to full autonomy

{% include video.liquid class="img-fluid rounded z-depth-1 responsive-iframe"
  path="https://www.facebook.com/plugins/video.php?href=https%3A%2F%2Fwww.facebook.com%2Fupeeei%2Fvideos%2F408094458765889"
  caption="<strong>Figure 1</strong>. Advancing AI: A Fireside Chat &amp; Research Talk with Dr. Holger Caesar"
  %}

How do we progress from the current state-of-the-art, Level 4, to the next frontier--Level 5? This is one of the key problems being tackled by [Dr. Holger Caesar](https://sites.google.com/it-caesar.de/homepage/), Assistant Professor at TU Delft and former Principal Research Scientist at Motional. In his talk at the Electrical and Electronics Engineering Institute in UP Diliman last May 9, 2024 (Figure 1), he highlighted the important correlation between the number of human interventions per mile driven and training data quantity, as shown in Figure 2. Interventions per mile driven is [used by the California Department of Motor Vehicles](https://www.dmv.ca.gov/portal/vehicle-industry-services/autonomous-vehicles/disengagement-reports/) as a proxy measure for AV safety, where lower number of interventions mean safer autonomous vehicles. This measure is considered the industry standard.

```echarts
{
  "tooltip": {
    "trigger": "axis"
  },
  "xAxis": {
    "type": "log",
    "name": "Training dataset size (hours)",
    "nameLocation": "middle",
    "nameGap": 20,
    "max": 2000
  },
  "yAxis": {
    "name": "Interventions per 1,000 miles",
    "nameLocation": "middle",
    "nameGap": 40
  },
  "series": [
    {
      "data": [[3.5, 2190.7], [10, 1024.1], [100, 145.3], [1000, 93.9]],
      "type": "line",
      "label": {
        "show": true
      }
    }
  ]
}
```

<div class="caption">
    <strong>Figure 2.</strong> Human interventions per 1,000 miles vs training dataset size from Houston et al. <d-cite key="houston2021one"></d-cite>.
</div>

While larger AV datasets are desirable, the costs associated in acquiring them are not. Data acquisition costs increase with the amount of data collected and annotated. Manually annotating vast amounts of data is prohibitively expensive. As Dr. Caesar remarked, only billion-dollar companies could afford to annotate such large-scale datasets. What is the solution then?

## Towards affordable large-scale autonomous driving datasets

Due to significant differences in traffic rules, infrastructure, and road user mix across countries, or even cities, a dataset is inherently tied to the place where it is acquired. Therefore, city-specific datasets are preferred since models trained on these will always outperform generic ones.

Aside from the technical aspects, there are human factors involved too. In the Philippines, for example, [workers often face "digital sweatshop" conditions](https://www.business-humanrights.org/en/latest-news/philippines-scale-ai-creating-race-to-the-bottom-as-outsourced-workers-face-poor-conditions-in-digital-sweatshops-incl-low-wages-withheld-payments/) because the data annotation industry is still considered an informal sector and is unregulated.

Dr. Caesar highlighted the need for affordable dataset acquisition in order to democratize autonomous vehicle research. Without an affordable way to acquire data, countries lacking in resources will be left behind, and human annotators will continue to be exploited. The following sections discuss the three key _ingredients_ of the affordable auto-labeling framework he proposed.

### Ingredient 1: Unsupervised object discovery and self-supervised learning

Unsupervised and self-supervised learning are learning paradigms which take advantage of vast amounts of unlabeled data. While the two terms are oftentimes used interchangeably in the literature, there is a nuanced difference between them <d-cite key="lecun2021ssl"></d-cite>. Simply put, self-supervised learning paradigms have a common explicit objective: to reconstruct the whole given some of its parts. The intuition is that this objective coerces the model to learn patterns in the data, not memorize it.

{% include figure.liquid loading="eager" class="img-fluid rounded z-depth-1" zoomable=true
  path="assets/img/dao_2024_label-efficient-3d-object-detection-rsu.jpg"
  caption="<strong>Figure 3.</strong> Overview of the 3D object detection method of Dao et al. Extracted from the paper."
  %}

Dao et al. <d-cite key="dao2024label"></d-cite>--to be presented at the _35th IEEE Intelligent Vehicles Symposium_--is a recent paper by Dr. Caesar and his collaborators. In this work, they introduce an unsupervised object discovery method, as well as adapt the self-training method of Xie et al. <d-cite key="xie2020self"></d-cite> for object detection.

Figure 3 illustrates the overview of their method. Self-training is done as follows:

1. The output of the unsupervised object discovery method serves as the initial labeled training data.
2. An initial detection model is trained on this initial dataset.
3. Once trained, the detection model is then used on the training set to detect objects. Only the high-confidence detections are selected, and these are treated as ground-truth. In the literature, these model-generated labels being used as ground-truth are called pseudo-labels.
4. The pseudo-labels are then used to train a new detection model, and the cycle is repeated (steps 3 and 4) until a satisfactory level of performance is achieved.

Using their proposed method, the **same performance** can be achieved with **2-5x less manual annotation** effort.

### Ingredient 2: Active learning

In machine learning, training on more data is generally better. At some point however, the cost of annotating a data sample exceeds the benefits of being able to use that labeled sample for training. Model performance will start to saturate at some point, and using more data beyond that no longer makes sense. Furthermore, the resources available--money--limit the amount of data which can be acquired. Therefore, machine learning practitioners should be clever and efficient in choosing training samples. In other words, the goal is to minimize the training data required to meet the desired model performance.

Large datasets are bound to have redundancies. Active learning is a learning paradigm which focuses on the selection of the <em>most informative</em> representative samples from a large pool of (possibly unlabeled) data. [Jacob Gildenblat's article](https://jacobgil.github.io/deeplearning/activelearning) provides a more comprehensive overview of active learning in the context of deep learning.

{% include figure.liquid loading="eager" class="img-fluid rounded z-depth-1" zoomable=true
  path="assets/img/tan_2023_active-learning.jpg"
  caption="<strong>Figure 4.</strong> Overview of cross-modal active learning by Tan et al. Extracted from Dr. Caesar's presentation."
  %}

The patent of Tan et al. <d-cite key="tan2024cross"></d-cite> is illustrated in Figure 4. It claims a method for applying active learning to object detection by comparing the consistency of results from two different modalities--images and LiDAR data. Samples where the detection results are consistent across the two modalities are considered high-confidence predictions, and can be used as-is for training detection models. On the other hand, samples which produce inconsistent detections are most likely challenging objects and would require manual annotation.

### Ingredient 3: Foundation and Language Models

Training a large model from scratch requires massive datasets and tremendous compute power. However, not everyone has the resources to do this. To lower the data requirement and shorten the training time, an initial condition close to a local optima is crucial. When two tasks are similar enough, existing trained weights can be reused instead of resorting to random initialization. This is the key idea behind transfer learning and finetuning.

The introduction of the Transformer <d-cite key="vaswani2017attention"></d-cite> in 2017 ushered in new breakthroughs in the realm of natural language processing (NLP). Language models such as BERT <d-cite key="devlin-etal-2019-bert"></d-cite> and GPT <d-cite key="radford2018improving"></d-cite> were pretrained first on large unlabeled text corpora, then finetuned to specific downstream tasks using labeled data. The effectiveness of their approach made the pretraining + finetuning paradigm a staple in NLP research. In the vision domain, pretrained models such as DINO <d-cite key="caron2021emerging"></d-cite> soon followed and played a similar role in computer vision research. These large pretrained models are collectively known today as foundation models <d-cite key="bommasani2021opportunities"></d-cite>. From a software engineering perspective, foundation models are akin to the standard libraries of programming languages. They provide a solid base where countless downstream models can be finetuned from. In the grand scheme of things, foundation models reduce the overall cost of model training.

{% include figure.liquid loading="eager" class="img-fluid rounded z-depth-1" zoomable=true
  path="assets/img/oquab_2024_dino-v2.jpg"
  caption="<strong>Figure 5.</strong> Visualization of the first PCA components from DINO v2. Extracted from the paper."
  %}

Desirable properties--such as understanding of object parts--typically emerge in self-supervised vision models like DINO v2 <d-cite key="oquab2023dinov2"></d-cite>. In Figure 5, notice that functionally similar object parts from different classes, e.g. the wings and nose of a plane and of an eagle, are matched and depicted with the same colors. In an ongoing work, Dr. Caesar and his colleagues exploit these emergent properties in the task of unsupervised 3D object detection.

## Conclusion

Safety is one of the driving motivations behind autonomous vehicle research. By eliminating the possibility of human error, autonomous vehicles are touted as safer alternatives. However, we are still far from achieving fully autonomous vehicles which could operate reliably in diverse and unpredictable environments. One of the major roadblocks is dataset acquisition. Autonomous vehicles would require vast amounts of annotated data in order to reach a full degree of autonomy.

Large-scale manually annotated data is not economically viable, even for big well-funded companies. Thus, we have to be clever about data acquisition while simultaneously developing label-efficient training methods. Dr. Caesar presented a framework for democratizing autonomous vehicle research by making dataset annotation more affordable. By using unsupervised and self-supervised methods, active learning, and foundation models, auto-labeling can be made cheap and effective. Future work should tackle how to use these three ingredients in synergy.
