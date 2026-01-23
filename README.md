

# ☕ Dripshot
> **"Find the perfect cafe that complements your OOTD."**

Dripshot is a **cafe recommendation service** that takes a **picture of the outfit** you are currently wearing to give you cafe recommendations based on the aesthetic of the outfit! Developed as part of the YBIGTA 25th New-Member Project. 

Please refer to these slides to find out about the creation process and the components of this 
[project](https://drive.google.com/file/d/1Wukl8RSSAWe9i8TCER0tJx4dkLYRmkU_/view?usp=drive_link) (Korean)


## 📍 Service Concept
* **Problem Definition**: Existing cafe recommendation services rely heavily on location or general popularity, making it difficult for users to find spaces that match their specific "look" or mood for the day.
* **Solution**: Dripshot analyzes the user's outfit using Computer Vision and matches it with cafe interior characteristics to provide a personalized curation of "Instagrammable" spots.
* **Target Area**: The service currently focuses on Seongsu-dong, a trend-sensitive hub with a high density of unique cafes. 

## 📍 Project Flow

<div align="center">
  <img src="./images/flow.png" width="700">
</div>

1. **Data Scraping**
   * **Fashion Data**: Scraped style images and tags from Musinsa Snap (646,467+ snaps).
   * **Cafe Data**: Collected cafe info, visitor reviews, and images from Naver Place. 
2. **Keyword Generation**
   * **Style Keywords**: Defined core keywords for 12 distinct fashion styles (e.g., Casual > Daily, Casual Chats, Easy Access).
   * **Cafe Keywords**: Summarized scraped Naver reviews and extracted environmental keywords with frequency-based weights, using GPT-4o-mini to 
3. **Similarity Analysis**
   * Matched fashion styles and cafes by comparing keyword vectors using CountVectorizer. 

## 📍 Service Pipeline & Featres
### Pipeline
<div align="center">
  <img src="./images/frontback.png" width="600">
</div>

### Features
![feature1](./images/feature1.png)
![feature2](./images/feature2.png)


## 📍 CNN Model

### Style Classification
We implemented a CNN-based classification model to identify fashion styles:

<div align="center">
  <img src="./images/image1.png" width="300">
</div>

* **Pre-processing**: Integrated `rembg` for background removal to focus the model on clothing features. 
<div align="center">
  <img src="./images/image2.png" width="450">
</div>

* **Augmentation**: Applied Gaussian Blurring and Affine transformations to enhance model robustness.
* **Model Selection**: **EfficientNet-B0** was chosen for its high efficiency and superior accuracy (Top-3 Acc: 73.72%) compared to ResNet. 

## 📍 Improvements
To improve the service, we developed a **weighted recommendation** algorithm that calculates a unified score for every cafe based on the user's multi-faceted style profile:

* **Dynamic Weighting**: Assign specific weights to each style keyword based on the CNN analysis of the user's photo.

* **Cross-Calculation**: Multiply these  weights by the calculated similarity scores of every cafe in the database.

* **Unified Scoring**: Sum these values to produce a final recommendation score, then rank cafes in descending order.

* **Scoring Formula**:
  $$Score_{i}=\sum_{j}(Weight_{selfie,j} \times Similarity_{cafe_{i},j})$$
  > The algorithm multiplies the probability of each style from the CNN output ($Weight_{selfie}$) by the pre-calculated similarity score of each cafe ($Similarity_{cafe}$) to generate a ranked list of recommendations. 
```python
  def calculate_score(cafe_data, selfie_weights):
    score = 0
    # Iterate through keywords and their weights from the user's selfie
    for keyword, weight in selfie_weights.items():
        # Match against pre-calculated cafe keyword similarities
        for cafe_keyword in cafe_data:
            if cafe_keyword[0] == keyword:
                # Weighted score = Selfie Weight * Cafe Keyword Similarity
                score += weight * cafe_keyword[1]
    return score

# Sorting and Tie-breaking Logic
# 1. Identify the style with the highest weight from user data
max_keyword = max(selfie_data, key=selfie_data.get)
final_recommendations = []
i = 0

while i < len(top_cafes):
    same_score_group = [top_cafes[i]]
    # Group cafes that share the same base score
    while i + 1 < len(top_cafes) and top_cafes[i][1] == top_cafes[i+1][1]:
        same_score_group.append(top_cafes[i+1])
        i += 1
    
    # Tie-breaker: If scores are tied, sort by the specific value of the primary style keyword
    if len(same_score_group) > 1:
        same_score_group.sort(key=lambda x: cafe_avg_data[x[0]][max_keyword], reverse=True)
    
    final_recommendations.extend(same_score_group)
    i += 1
```

## 📍 Tech Stack

* **Language**: ![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white) 
* **Frontend**: ![HTML5](https://img.shields.io/badge/html5-%23E34F26.svg?style=for-the-badge&logo=html5&logoColor=white) ![CSS3](https://img.shields.io/badge/css3-%231572B6.svg?style=for-the-badge&logo=css3&logoColor=white) ![JavaScript](https://img.shields.io/badge/javascript-%23F7DF1E.svg?style=for-the-badge&logo=javascript&logoColor=black)
* **Backend**: ![Django](https://img.shields.io/badge/django-%23092E20.svg?style=for-the-badge&logo=django&logoColor=white)
* **AI/ML**: ![PyTorch](https://img.shields.io/badge/PyTorch-%23EE4C2C.svg?style=for-the-badge&logo=PyTorch&logoColor=white)
* **Database**: ![SQLite](https://img.shields.io/badge/sqlite-%2307405e.svg?style=for-the-badge&logo=sqlite&logoColor=white)
* **Deployment**: ![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)



## 📍 Team DripShot (YBIGTA 25th)
* **Authors**: Minseo Kim (DA), Dogeun Im (DA), Jiyeon Seo (DA), Isaac Chung (DE), Jaebin Cheong (DS)
* **Project Date**: 2024.08


## 📍 Version History
### 0.1
- **Initial Release**
  - Location: 성수역 / Seong-Su Station Only

## 📍 Acknowledgments
Inspiration, code snippets, etc.
* https://github.com/danielgatis/rembg
