# Pairs Trading with Word Embeddings  

Inspired by a [Quantopian post](https://quantopian-archive.netlify.app/notebooks/notebooks/quantopian_notebook_160) from Johnathan Larkin in 2017, Given that a lot of examples I've seen for finding pairs has been several years old at least I wanted to try and find a way leveraging more contemporary advances in AI/ML.  

## Overview  

Pairs trading is a well-known strategy, but finding effective pairs remains a challenge. The NASDAQ currently lists approximately **7,041 stocks** (as of September 2024), meaning a brute-force approach would require testing **24,784,320 pairs**  

This project aims to use **word embeddings** on **Yahoo! Finance stock descriptions** (Nvidia Example [here](https://finance.yahoo.com/quote/NVDA/profile/)) then clustering to try and find promising monogamous trading pairs.

## Methodology  

1. **Stock Selection**  
   - Retrieve a list of all stocks listed on NASDAQ.  
   - Collect pricing data for stocks with descriptions available on Yahoo! Finance.  
   - Filter out stocks with less than **1 year** of trading history.  

2. **Embedding Generation**  
   - Encode stock descriptions using a **word embedding model** (Used: [`BGE-Base-EN`](https://huggingface.co/BAAI/bge-base-en-v1.5) / FlagEmbedding).  

3. **Dimensionality Reduction**  
   - Apply **Principal Component Analysis (PCA)** and **PaCMAP** for reducing high-dimensional embeddings.  

4. **Clustering with DBSCAN**  
   - Determine the optimal **Eps** value using the **elbow/knee method** from paper [here](https://www.ccs.neu.edu/home/vip/teach/DMcourse/2_cluster_EM_mixt/notes_slides/revisitofrevisitDBSCAN.pdf).  
   - Find a cluster size cut off point. **40** was used in this example.

5. **Pairs Selection**  
   - Identify stock pairs from **PCA**, **PaCMAP**, and **non-reduced embeddings**.
   - Throw out stock pairs with a Levenshtein distance of <= 1 to avoid leveraged ETFs of stocks or stocks with different classes being considered.
   - Perform **cointegration tests** (p-value < 0.05 used) to assess statistical relationships.  
   - Extract **monogamous** pairs for pairs trading.  

The end result was **300** possible pairs found with **89** being monogamous (As of Dec. 7 2024)

## Why **PaCMAP**?  

Unlike standard dimensionality reduction techniques, **PaCMAP** preserves **both local and global structures** which can help find **sector-based groupings** while also attemping to retain clusters within those sectors.  

## Considerations  

While the danger of multiple comparison bias is very real with a method like this, this is should be seen as a starting point for finding pairs. Addtional tests should be applied, such as:  
- **Hurst exponent analysis** for spread mean-reversion.  
- **Time Spread Tradability** to assess spread tradability over a certain time frame.  


## License  

This project is open-source and available under the **MIT License**. 
