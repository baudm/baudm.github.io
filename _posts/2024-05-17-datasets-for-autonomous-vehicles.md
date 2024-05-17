---
layout: distill
title: Democratizing large-scale autonomous driving datasets
description:
tags:
giscus_comments: false
date: 2024-05-17
featured: false
chart:
  echarts: true

bibliography: 2024-05-17-datasets-for-autonomous-vehicles.bib

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc:
  - name: The case for autonomous vehicles
  - name: Road to full autonomy
  - name: Towards democratized large-scale autonomous driving datasets
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

Recent advances in computer vision and artificial intelligence paved the way for major leaps in machine perception and understanding--key elements of autonomous systems. As a result, autonomous vehicles (AVs), particularly self-driving cars, have risen in popularity in the past several years. Tesla is arguably the most well-known company in the AV space. Autonomous taxis--or robotaxis--are also gaining traction. Waymo (formerly the Google Self-Driving Car Project) and Motional (formerly nuTonomy) are just a few of the several companies competing in the AV industry.

## The case for autonomous vehicles
Enhanced safety is one of the primary motivations for developing AV technology.

In autonomous vehicles, the degree of autonomy is classified into 6 levels. Level 0 pertains to the case where a human is 100% in control of the vehicle and no automation is employed. Meanwhile, the other end of the spectrum is Level 5--the fully autonomous case--where a human is merely a passenger in a driverless car. The generally-accepted notion is that we are currently at Level 4, or _high driving automation_. At Level 4, a vehicle is autonomous only for certain specific conditions, e.g. driving in the freeway, or backing out of a garage. In contrast, Level 5 AVs are fully autonomous 100% of the time, and should be able to seamlessly coordinate with each other, enhancing passenger safety and reducing--if not totally eliminating--human-caused accidents.

## Road to full autonomy

{% include video.liquid path="https://www.facebook.com/plugins/video.php?href=https%3A%2F%2Fwww.facebook.com%2Fupeeei%2Fvideos%2F408094458765889" class="img-fluid rounded z-depth-1 responsive-iframe" caption="<strong>Figure 1</strong>. Advancing AI: A Fireside Chat &amp; Research Talk with Dr. Holger Caesar" %}

How do we progress from the current state-of-the-art, Level 4, to the next frontier--Level 5? This is one of the key problems being tackled by [Dr. Holger Caesar](https://sites.google.com/it-caesar.de/homepage/), an Assistant Professor at TU Delft and a former research scientist at Motional. In his talk at the Electrical and Electronics Engineering Institute in UP Diliman last May 9, 2024 (Figure 1), he highlighted the important correlation between the number of human interventions per mile driven and training data quantity, as shown in Figure 2. Interventions per mile driven is [used by the California DMV](https://www.dmv.ca.gov/portal/vehicle-industry-services/autonomous-vehicles/disengagement-reports/) as a proxy measure for AV safety, where lower number of interventions mean safer autonomous vehicles. This measure is considered the industry standard.

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
    <strong>Figure 2.</strong> Human interventions per 1,000 miles vs training dataset size from Houston <em>et al.</em><d-cite key="houston2021one"></d-cite>.
</div>

While larger AV datasets are desirable, the costs associated in acquiring them are not. Data acquisition costs increase with the amount of data collected and annotated. Manually annotating vast amounts of data is prohibitively expensive. As Dr. Caesar remarked, only billion-dollar companies could afford to annotate such large-scale datasets. What is the solution then?

## Towards democratized large-scale autonomous driving datasets

Dr. Caesar highlighted the need for democratizing autonomous vehicle research by making dataset acquisition more affordable. Due to the significant differences in traffic rules, infrastructure, and road user mix across countries, or even cities, datasets are inherently tied to the place where it is acquired. Without an affordable way to acquire data, countries without abundant resources will be left behind.

He presented a framework for affordable auto-labeling which consists of three key _ingredients_.

### Ingredient 1: Unsupervised object discovery and self-supervised learning

Unsupervised and self-supervised learning are learning paradigms which take advantage of vast amounts of unlabeled data. While the two terms are oftentimes used interchangeably in the literature, there is a nuanced difference between them<d-cite key="lecun2021ssl"></d-cite>. Simply put, self-supervised learning paradigms have a common explicit objective: to reconstruct the whole given its parts. The intuition is that this objective coerces the model to learn patterns in the data, not memorize it.

{% include figure.liquid loading="eager" class="img-fluid rounded z-depth-1" zoomable=true
  path="assets/img/dao_2024_label-efficient-3d-object-detection-rsu.jpg"
  caption='<strong>Figure 3.</strong> Overview of the 3D object detection method of Dao et al. <em>Extracted from the paper</em>.'
  %}

Dao et al.<d-cite key="dao2024label"></d-cite>--to be presented at the _35th IEEE Intelligent Vehicles Symposium_--is a recent paper by Dr. Caesar and his collaborators. In this work, they introduce an unsupervised object discovery method, as well as adapt the self-training method of Xie et al.<d-cite key="xie2020self"></d-cite> for object detection.

Figure 3 illustrates the overview of their method:
1. The output of the unsupervised object discovery method serves as the initial labeled training data.
2. An initial detection model is trained on this initial dataset.
3. Once trained, the detection model is then used on the training set to detect objects. Only the high-confidence detections are selected, and these are treated as ground-truth. In the literature, these model-generated labels being used as ground-truth are called pseudo-labels.
4. The pseudo-labels are then used to train a new detection model, and the cycle repeats (steps 3 and 4) until a satisfactory level of performance is achieved.

Using their proposed method, the **same performance** can be achieved with **2-5x less manual annotation** effort.

### Ingredient 2: Active learning

In machine learning, training on more data is generally better. At some point however, the cost of annotating a data sample exceeds the benefits of being able to use that labeled sample for training. Model performance will start to saturate at some point, and using more data beyond that no longer makes sense. Furthermore, the resources available--money--limit the amount of data which can be acquired. Therefore, machine learning practitioners should be clever and efficient in choosing training samples. In other words, the goal is to minimize the training data required to meet the desired model performance.

Large datasets are bound to have redundancies. Active learning is a learning paradigm which focuses on the selection of the <em>best</em> representative samples from a large pool of (possibly unlabeled) data. [Jacob Gildenblat's article](https://jacobgil.github.io/deeplearning/activelearning) provides a more comprehensive overview of active learning in the context of deep learning.

{% include figure.liquid loading="eager" class="img-fluid rounded z-depth-1" zoomable=true
  path="assets/img/tan_2023_active-learning.jpg"
  caption="<strong>Figure 4.</strong> Overview of cross-modal active learning by Tan et al. <em>Extracted from Dr. Caesar's presentation</em>."
  %}

The patent of Tan et al.<d-cite key="tan2024cross"></d-cite> is illustrated in Figure 4. It claims a method for applying active learning to object detection by comparing the consistency of results from two different modalities--images and LiDAR data.

### Ingredient 3: Foundation and Language Models

Training a large model from scratch requires massive datasets and tremendous compute power. However, not everyone has the resources to do this. To lower the data requirement and shorten the training time, an initial condition close to a local optima is crucial. In other words, existing trained weights can be reused instead of resorting to random initialization.

The introduction of the Transformer<d-cite key="vaswani2017attention"></d-cite> in 2017 ushered in new breakthroughs in the realm of natural language processing (NLP). Language models such as BERT<d-cite key="devlin-etal-2019-bert"></d-cite> and GPT<d-cite key="radford2018improving"></d-cite> were pretrained first on large unlabeled text corpora, then finetuned to specific downstream tasks using labeled data. The effectiveness of their approach made the pretraining + finetuning paradigm a staple in NLP research. In the vision domain, pretrained models such as DINO<d-cite key="caron2021emerging"></d-cite> soon followed and played a similar role in computer vision research. These large pretrained models are collectively known today as foundation models<d-cite key="bommasani2021opportunities"></d-cite>. From a software engineering perspective, foundation models are akin to the standard libraries of programming languages. They reduce the cost of training models for specific downstream tasks or applications.

{% include figure.liquid loading="eager" class="img-fluid rounded z-depth-1" zoomable=true
  path="assets/img/oquab_2024_dino-v2.jpg"
  caption='<strong>Figure 5.</strong> Visualization of the first PCA components from DINO v2. <em>Extracted from the paper</em>.'
  %}

Desirable properties--such as understanding of object parts--typically emerge in self-supervised vision models such DINO v2 <d-cite key="oquab2023dinov2"></d-cite>, as shown in Figure 5. In an ongoing work, Dr. Caesar and his colleagues exploit these emergent properties in the task of 3D object detection without using any manual labels.

## Conclusion

Safety is one of the driving motivations behind autonomous vehicle research. By eliminating the possibility of human error, autonomous vehicles are touted as safer alternatives. However, we are still far from achieving fully autonomous vehicles which could operate reliably in diverse and unpredictable environments. One of the major roadblocks is dataset acquisition. Autonomous vehicles would require vast amounts of annotated data in order to reach a full degree of autonomy.

Large-scale manually annotated data is not economically viable, even for big well-funded companies. Thus, we have to be clever about data acquisition while simultaneously developing label-efficient training methods. Dr. Caesar presented a framework for democratizing autonomous vehicle research by making dataset annotation more affordable. By using unsupervised and self-supervised methods, active learning, and foundation models, auto-labeling can be made cheap and effective. Future work should tackle on how to better combine these three ingredients.
