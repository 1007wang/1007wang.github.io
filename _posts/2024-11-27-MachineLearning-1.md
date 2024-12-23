---
title: 机器学习——银行客户聚类
date: 2024-11-27 23:46 +0800
categories: [MachineLearning]
tags: [MachineLearning，KMeans]
---

## 代码

```python
import pandas as pd
from sklearn.cluster import KMeans
from sklearn.decomposition import PCA
from sklearn.metrics import silhouette_score
import matplotlib.pyplot as plt
import matplotlib

# 设置matplotlib支持中文显示
matplotlib.rcParams['font.sans-serif'] = ['SimHei']  # 使用黑体
matplotlib.rcParams['axes.unicode_minus'] = False  # 解决负号显示问题

# 第一步：加载数据
file_path = "C:\\Users\\20199\\Documents\\作业\\大三上\\机器学习\\银行客户聚类\\select-data.csv"
data = pd.read_csv(file_path)

# 删除无意义列
data = data.drop(columns=['Unnamed: 0'], errors='ignore')

# 选择用于聚类的特征列
columns_to_use = ['Age', 'CreditScore', 'EstimatedSalary', 'HasCrCard', 'IsActiveMember', 'NumOfProducts', 'Tenure']

# 第二步：使用轮廓系数确定最佳聚类数
def find_optimal_clusters(data, columns, range_clusters):
    silhouette_scores = []
    for n_clusters in range_clusters:
        kmeans = KMeans(n_clusters=n_clusters, n_init=10, random_state=42)
        cluster_labels = kmeans.fit_predict(data[columns])
        silhouette_avg = silhouette_score(data[columns], cluster_labels)
        silhouette_scores.append(silhouette_avg)
    return silhouette_scores

range_clusters = range(2, 10)
silhouette_scores = find_optimal_clusters(data, columns_to_use, range_clusters)

# 绘制轮廓系数图
plt.figure(figsize=(10, 6))
plt.plot(range_clusters, silhouette_scores, marker='o')
plt.title('最佳聚类数的轮廓系数')
plt.xlabel('聚类数')
plt.ylabel('轮廓系数')
plt.grid(True)
plt.show()

# 确定最佳聚类数
optimal_clusters = range_clusters[silhouette_scores.index(max(silhouette_scores))]
print(f"最佳聚类数：{optimal_clusters}")

# 第三步：根据最佳聚类数进行聚类分析
kmeans = KMeans(n_clusters=optimal_clusters, n_init=10, random_state=42)
data['Cluster'] = kmeans.fit_predict(data[columns_to_use])

# 输出每个簇的中心点
print("簇的中心点：\n", kmeans.cluster_centers_)

# 第四步：使用PCA降维到2D并进行可视化
pca = PCA(n_components=2)
data_pca = pca.fit_transform(data[columns_to_use])
data['PCA1'] = data_pca[:, 0]
data['PCA2'] = data_pca[:, 1]

# 绘制聚类结果
plt.figure(figsize=(10, 6))
for cluster in range(optimal_clusters):
    cluster_data = data[data['Cluster'] == cluster]
    plt.scatter(cluster_data['PCA1'], cluster_data['PCA2'], label=f'簇 {cluster}')
plt.xlabel('PCA1')
plt.ylabel('PCA2')
plt.title('客户聚类结果可视化')
plt.legend()
plt.grid(True)
plt.show()

```

1. **数据预处理**：

   - 删除了无意义的 `Unnamed: 0` 列。

   - 选择了特征列进行聚类分析：`['Age', 'CreditScore', 'EstimatedSalary', 'HasCrCard', 'IsActiveMember', 'NumOfProducts', 'Tenure']`。

2. **轮廓系数评估最佳聚类数**：

   - 使用 `silhouette_score` 在 2 到 9 个簇之间选择最佳聚类数。

   - 绘制轮廓系数得分分布图，选择使得轮廓系数最大的簇数（4 个簇）。

3. **聚类分析**：

   - 使用 `KMeans` 聚类算法对数据进行分组。

   - 输出了 4 个簇的中心点。

4. **降维和可视化**：

   - 使用 PCA 将高维特征降维到 2D 平面。

   - 绘制了聚类结果的二维分布图，显示每个簇的分布情况。

## 结果

肘部法则：

![image-20241127031018448](https://cdn.jsdelivr.net/gh/1007wang/Typora-img@img/img/image-20241127031018448.png)

轮廓系数：

![image-20241126212506209](https://cdn.jsdelivr.net/gh/1007wang/Typora-img@img/img/image-20241126212506209.png)

![image-20241126212436462](https://cdn.jsdelivr.net/gh/1007wang/Typora-img@img/img/image-20241126212436462.png)

![image-20241126212445700](https://cdn.jsdelivr.net/gh/1007wang/Typora-img@img/img/image-20241126212445700.png)



![image-20241126212451670](https://cdn.jsdelivr.net/gh/1007wang/Typora-img@img/img/image-20241126212451670.png)