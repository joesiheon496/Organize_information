# Contrastive loss를 이용한 유사특허기술 탐색 시스템

4차 산업 시대에 이르러 기술은 다양한 분야에 걸쳐 날이 갈수록 빠르게 발전되고 있다.

수많은 기술이 다양한 분야에서 쏟아져 나오고 있지만, 기존의 기술과 유사하거나 중복되는 등 기술의 침해가 일어날 수 있다.

기술의 침해가 일어날 경우 어떤 기술의 최초 개발자 외에 유사하거나 중복되는 기술로 이익을 얻는 사람이 발생할 수 있다.

이처럼 과거보다 향상된 기술은 발명자의 허락 없이 사용될 위험성이 있는데 발명자의 권리를 지켜주기 위한 권리를 특허권이라 한다. 

특허권은 기업이나 개인의 발명을 다른 이에게서 보호하기 위한 제도이며 발명자의 아이디어를 많은 사람이 누릴 수 있고 발전된 기술로 산업 발전을 이룰수 있게 해주는 조치이다.

앞서 설명한 것과 같이 새로운 기술을 개발하고 특허를 출원하는데, 이전에 유사하거나 중복되는 특허가 있는지 확인하는 일은 필수이다.

이러한 과정을 특허 선행기술 조사라 하고, 이는 신규성과 진보성을 판단하는 과정으로 특허의 출원을 결정짓는 요소이다. 

특허 선행기술 조사 시 특허 조사 범위는 국내와 국외 모두 포함이 된다. 

국내 특허는 특허청의 키프리스 사이트나 윕스 (WIPS) 등의 특허 검색 사이트를 이용해 변리사가 직접 검색하고, 해외 특허는 구글 특허 검색 사이트를 이용해 선행기술을 조사한다. 

문제점은 해가 지날수록 기술의 발전으로 특허의 출원 양도 누적되고 탐색해야할 자료 수가 증가하고 있는 점이다. 

선행기술 조사과정은 자동화되는 구간이 존재하지 않아 특허 양이 많을수록 시간 소모가 크다. 

또한 특허 선행조사 업무를 맡은 담당자가 자신의 세부 전공 분야와 다른 분야의 특허 선행조사를 진행할 경우 정확한 유사 특허를 찾기 힘든 문제점이 생긴다. 

사람이 직접 선행기술 조사를 하는 일에 많은 시간과 노동이 투자되므로 최근에는 인공지능을 이용하여 자동화하는 방법이 연구되고 있다. 

특허는 텍스트로 이루어져있기 때문에 보통 자연어 처리 모델이 이용되고 있다. 

이전 연구에서는 자연어 처리 모델로 특허의 단어를 벡터화하여 특허간의 유사도를 계산하고 유사한 특허를 탐색하는 시스템을 구축하여 변리사들이 확인해야 하는 특허를 추천했다. 

그러나 자연어처리 모델이 글의 문맥적 특징까지 파악하도록 설계되지 않았거나 같은 내용의 특허라도 특허의 단어 구성이 다르다면 추천 받지 못하는 특허가 생기는 문제점이 있다.

최근 연구에서는 글의 벡터화 과정에서 문맥을 반영하는데 좋은 성능을 보이는 Bidirectional Embedding Representation from Transformer (BERT)를 주로 사용되고 있다. 

특히 특허 문서를 처리하는데 더 적합한 모델을 사용하기 위해 특허 문서로 미세조정한 PatentBERT가 활용되고 있다. 

그리고 판별하고자하는 특허와 유사한 특허를 찾기 위해서, 데이터 비교를 통해 학습시키는 Contrastive learning을 활용한 사례도 있다. 

유사한 특허를 탐색하는 용도로 쓰이던 PatentBERT와 Contrastive loss\cite{contrastiveloss}를 활용하여 Contrastive learning을 진행했다.  

우리는 특허 문서의 초록이라는 텍스트 형태의 데이터를 잠재공간에 올리기 위해, PatentBERT를 이용해서 텍스트 데이터를 벡터화했다.  

위 과정에서 벡터화된 특허 문서 초록은 잠재공간에 위차할 수 있게 된다.

PatentBERT를 통과한 벡터 형태의 특허 문서 초록은 글의 문맥 정보에 의해서만 잠재공간에 위치해 있게 되는데, 이는 도메인을 기반으로 만들어진 것이 아니기 때문에 도메인에 대한 미세조정 (fine-tuning)이 필요하다. 

따라서 특허 문서 초록들을 도메인에 대해 임베딩 시키기 위해 PatentBERT 이후에 순방향 신경망을 구성하고 추가로 구성한 부분에 대해서만 미세조정을 진행한다. 

PatentBERT와 Contrastive learning으로 미세조정한 모델은 BERT에 대해 Contrastive learning을 진행한 것 보다 군집화 측면에서 더 뛰어난 성능을 보인다. 

또한 잠재공간 상 유클리드 거리 기반 유사특허탐색에 있어서 다른 단어들로 구성이 되어있지만 내용이 유사한 특허를 찾아내는 것이 가능하다. 
 
# 데이터
\label{sec:data}

%%% 모델 프레임워크 그림
\begin{figure*}[t]
\begin{center}
\includegraphics[width=\textwidth]{imgs/framework.pdf}% Here is how to import EPS ar
\caption[Structure of the model]{\label{fig:structure_of_the_model}Structure of the model. The abstract data of the patent is vectorized with PatentBERT and learned through fine tuning. A method of reducing the scope of patent prior art search by extracting nodes closest to one target node.}
\end{center}
\end{figure*}

%%% 데이터 출처와 훈련데이터, 평가데이터 구성 설명
특허간의 단어 구성이 얼마나 유사한지 측정한 자카드 유사도와 잠재 공간상에서 특허 문서 간의 거리를 나타내는 유클리드 거리를 살펴보기 위해 하버드 Dataverse에서 제공하는 USPTO 데이터\cite{harvarduspto}를 사용했다. 
하버드에서 제공하는 USPTO 데이터는 특허들의 초록에 대한 자카드 유사도가 기재되었다. 
데이터는 미국 특허청에서 제공한 1973년에서 2013년까지 4,422,009개의 특허가 있다. 
본 연구에서는 자카드 유사도 0.5이상 1,276개와 0.5이하 1,276개를 랜덤으로 추출하여 총 2,552개의 특허 초록을 훈련데이터로 사용했다. 
평가데이터는 자카드 유사도를 0.2 단위로 각 구간에서 600개씩 추출하여 총 3,000개의 데이터를 추출하였다. 
추출된 특허들의 섹션은 생활필수품 관련 특허 섹션 A 510개, 운수 관련 특허 섹션 B 918개, 화학 관련 특허 섹션 C 2,069개, 섬유 관련 특허 섹션 D 107개, 고정구조물 관련 특허 섹션 E 140개, 무기 관련 특허 섹션 F 454개, 물리학 관련 특허 섹션 G 779개, 전기 관련 특허 섹션 H 575개로 구성되어있다.

\section{Fine-tuning}
\label{sec:patentbert}
%%% 모델 fine-tuning 파트 설명 (PatentBERT)
PatentBERT는 BERT를 특허의 Cooperative Patent Classification (CPC)을 다중 분류 할 수 있도록 미세조정 (fine-tuning)한 모델이다. 
PatentBERT는 특허 태스크에서 기존 BERT보다 뛰어난 결과를 보여왔다~\cite{patentbert}. 
우리는 PatentBERT를 활용하여 도메인 기반 유사특허 탐색을 진행한다. 
PatentBERT는 CPC 분류를 학습한 모델로서 본 연구의 도메인 기반 탐색을 진행하기 위해서는 추가적인 학습이 필요하다. 
따라서 Contrastive learning으로 같은 섹션의 특허들은 가깝게, 다른 섹션의 특허들은 서로 멀게 임베딩 되도록 모델을 미세조정한다. 
이를 위해 Fig.~\ref{fig:structure_of_the_model}과 같이 PatentBERT 이후 순방향 신경망 (feed forward neural network)을 구성한다. 
순방향 신경망은 학습파라미터인 가중치 행렬 $W$와 비선형성을 가지는 활성화함수 $\sigma$로 입력데이터를 업데이트한다. 
본 연구의 모델은 사전 학습된 PatentBERT 모델 $F$로 특허 $i$의 요약 $X_{i}$를 특허 벡터 $H_{i}^{1}$로 나타내고, 수식 (\ref{eq:model})와 같이 순방향 신경망을 활용하여 업데이트 한다. 
$l$은 순방향 신경망의 층을 나타내는 인덱스로 최종 출력은 섹션에 따라 잠재공간 상에서 클러스터를 이루도록 학습한다.  

%%% BERT 모델과 FNN 모델 단순 수식화
\begin{align}
H_{i}^{1} &= F(X_{i}),\\  
H_{i}^{l+1} &= \sigma(W^{l}H_{i}^{l}).  
\label{eq:model}
\end{align}

\section{Contrastive loss with multiple positive pairs}
\label{sec:contrastiveloss}
%%%Contrastive loss 설명
Contrastive learning은 입력된 데이터 쌍의 비교로 학습하는 방법이다. 
본 연구에서는 잠재공간에 같은 섹션의 특허 문서들끼리 모이고, 다른 섹션의 특허끼리는 멀게 배치하는 역할을 하여 도메인의 군집화를 했다.
데이터 쌍은 비교하는 대상에 따라 양성 (positive)과 음성 (negative)으로 나눌 수 있다. 
데이터 쌍이 유사한 경우 양성으로, 아닌경우 음성으로 정의한다. 
우리는 섹션이 같은 특허 쌍을 양성, 다른 경우를 음성으로 하여 도메인 중심의 유사 특허 탐색을 진행한다. 
본 연구의 유사 특허 탐색은 유클리드 거리 $D$를 기준으로 같은 섹션의 특허는 거리가 가깝게, 다른 섹션의 특허는 멀어지도록 학습시켜서 같은 도메인의 특허들의 군집화를 이끌었다. 
거리 기반의 Contrastive learning은 수식 (\ref{eq:contrastivemaineq})을 통해서 진행된다. 

%%% Contrastive loss 설명 수식
\begin{equation}
L_{i} = \sum_{j \in P}(y_{ij}D_{ij}^{2}+(1-y_{ij})\text{max}[0,\alpha-D_{ij}^{2}]).
\label{eq:contrastivemaineq}
\end{equation}

%% Contrastive 수식 설명
$i$와 $j$는 특허 쌍 $P$에 포함된 특허로, $y_{ij}$는 두 특허의 도메인이 일치하는지를 나타내는 라벨이다. 
특허 $i$와 $j$가 같은 섹션인 경우 $y_{ij}=1$이 되고, 다른 섹션이면 $y_{ij}=0$을 나타낸다. 
$\alpha$는 하이퍼파라미터로, 값을 조절함으로써 다른 섹션 특허와의 최소 거리를 설정 할 수 있다. 
Contrastive learning은 데이터 쌍에 대해 손실을 계산하여 모델을 학습시킨다. 
따라서 데이터 쌍을 구성하는 방법에 따라 학습 결과가 달라질 수 있다.

%%% Multiple-positive pairs sampling
데이터 쌍 $P$를 구성하는 방법에는 다양한 알고리즘이 있다. 
많은 연구들에서 배치 (batch) 내 데이터 쌍을 구성할 때 양성은 한 개, 음성의 수는 여러 개로 구성한다~\cite{triplet, distancemet, tripleloss}. 
데이터셋의 규모가 작을수록 양성보다는 음성들이 많이 존재한다. 
특허 탐색에서 유사한 특허를 발견하기 위해서는 적은 수의 양성도 중요하다. 
하지만 데이터 쌍에서 양성과 음성의 불균형으로 인해 양성과 음성의 중요도가 달라지게 된다. 
이처럼 양성과 음성에 대한 중요도가 달라지는 문제로 인해 데이터 쌍을 여러 개의 양성을 구성한 연구도 존재한다~\cite{CLTopp-k}. 
우리는 양성의 중요도를 높이기 위해, 데이터 쌍 구성에서 여러 개의 양성을 포함시킨다. 
이를 위해 배치를 구성하는 특허들의 조합으로 데이터 쌍을 생성한다. 
배치는 다른 섹션들의 특허 데이터 뿐만 아니라 같은 섹션의 특허 데이터들도 있다. 
따라서 배치 내 특허들의 조합으로 음성 뿐만 아니라 양성도 여러 개 생성 할 수 있다.

\section{결과}
\label{sec:result}

%%% t-SNE 이용한 잠재공간 시각화 그림
\begin{figure*}[t]
%\begin{center}
\begin{tabular}{cc}
\includegraphics[width=0.495\textwidth]{imgs/embedding_value1.pdf}&% Here is how to import EPS art
\includegraphics[width=0.495\textwidth]{imgs/embedding_value2.pdf}
\end{tabular}
\caption[Visualizing]{\label{fig:embedding_value}Visualizing data vector embeddings. (a) Visualizing vector embeddings of learning data without Contrastive loss, and (b) Visualizing vector embeddings of data with Contrastive loss, In the figure without Contrastive loss, there are many places where different domains are mixed. However, it is confirmed that the embedding applying the Contrastive loss is well-organized between domains.}
%\end{center}
\end{figure*}

%%% 잠재공간 시각화 (BERT 통과 후와 constrastive 러닝 이후)
본 연구의 방법론을 적용했을 때와 적용하기 전의 잠재공간 상 특허 분포를 비교하기 위해 미세조정한 순방향 신경망의 출력과 BERT의 출력을 t-SNE\cite{t-SNE}로 시각화했다~(Fig.~\ref{fig:embedding_value}). 
산점도에서 점은 특허로, 특허간의 유사도가 높을수록 가깝게 위치한다. 
Fig.~\ref{fig:embedding_value}(a)는 PatentBERT로 특허를 벡터화 시킨 후 잠재공간 상에 나타낸 그림으로 여러 섹션들이 섞여 있는 것을 알 수 있다. 
BERT모델들은 글 내용과 관련하여 벡터화하므로 섹션별로 분리가 되지 않은 것은 당연한 결과이다. 
반면 Contrastive learning으로 미세조정한 순방향 신경망 모델의 출력을 잠재공간 상에 나타낸 Fig.~\ref{fig:embedding_value}(b)는 같은 섹션끼리 잘 모여 도메인 군집화가 이루어진 것을 확인할 수 있다. 
잠재공간에 단순히 글의 유사도에 의해서만 배치시키는 BERT 모델과는 다르게 Contrastive learning은 섹션별로 도메인 군집화까지 시키므로, 새로운 특허에 대해 유사한 특허들과 더 쉽게 비교할 수 있게 된다. 

%%% 혼동행렬 그림
\begin{figure}[t]
\begin{tabular}{cc}
\includegraphics[width=.495\textwidth]{imgs/ConfusionMatrixBERT.pdf}&
\includegraphics[width=.495\textwidth]{imgs/ConfusionMatrixPatentBERT.pdf}\\
\includegraphics[width=.495\textwidth]{imgs/bertContrastive.pdf}&
\includegraphics[width=.495\textwidth]{imgs/PatentbertContrastive.pdf}
\end{tabular}
\caption[Confusion]{\label{fig:confusionmatrix}Confusion Matrix for Patent Clustering. (a) Confusion Matrix of BERT, (b) Confusion Matrix of Patent BERT, (c) Confusion Matrix of BERT + Contrastive loss, and (d) Confusion Matrix of Patent BERT + Contrastive loss, which is more accurate than the existing section.}
\end{figure}

%%% 혼동행렬 (confusion matrix)로 정량적 분석
%%% F1 score에 대해 받은 수치가 문제가 있어보여서 혼동행렬의 상대적 비교로 작성했습니다. (박상준)
Figure.~\ref{fig:embedding_value}의 군집화를 정량적으로 평가하기 위해 특허들이 어떤 도메인의 군집 중심에 가장 가까운지 측정했다. 
군집의 중심은 테스트 데이터로 사용되는 특허들을 모두 잠재공간 상에 나타내고 각 섹션별 특허 벡터들의 평균으로 계산하여 클러스터의 무게 중심을 의미한다. 
모델의 성능을 평가하기 위해 테스트 특허 문서들을 잠재공간 입력하여 가장 가까운 도메인 군집의 중심과 거리를 측정하고, 가장 가까운 도메인 중심을 입력해준 테스트 특허 문서의 섹션으로 생각했다. 
특허들의 섹션과 가장 가까운 도메인 군집의 섹션이 일치하는 정도를 나타낸 것이 Fig.~\ref{fig:confusionmatrix}이다. 
본 연구의 방법론이 기존 방법론들에 비해 군집화가 뛰어난지 확인하기 위해 BERT와 PatentBERT 그리고 Contrastive learning으로 미세조정한 결과를 비교했다.
미세조정을 하지 않은 모델의 결과인 Fig.~\ref{fig:confusionmatrix}(a)와 (b)를 보면 특정 섹션들에 대해서 정확도가 낮은 것을 볼 수 있다. 
반면 Contrastive Learning으로 미세조정을 진행 후 측정한 혼동행렬 Fig.~\ref{fig:confusionmatrix}(c)와 (d)는 미세조정을 하지 않을 때보다 섹션들을 더 골고루 맞추는 것을 알 수 있다. 
이는 미세조정이 섹션 분류에 도움이 되는 것을 알 수 있다. 
그리고 Fig~\ref{fig:confusionmatrix} (c)와 (d)를 비교해보면 PatentBERT를 활용한 경우에 섹션별 정확도가 더 높은 것을 알 수 있다. 
따라서 본 연구에서 활용한 PatentBERT와 Contrastive learning은 특허 섹션 분류에 있어 성능을 높여주는 것을 Fig.~\ref{fig:confusionmatrix}로부터 알 수 있다.

%%% 자카드유사도 & 유클리디안 거리 산점도 그림
\begin{figure}[t]
\begin{tabular}{cc}
\includegraphics[width=.495\textwidth]{imgs/jaccardeuclidean_a.pdf}&
\includegraphics[width=.495\textwidth]{imgs/jaccardeuclidean_b.pdf}\\
\includegraphics[width=.495\textwidth]{imgs/jaccardeuclidean_c.pdf}&
\includegraphics[width=.495\textwidth]{imgs/jaccardeuclidean_d.pdf}
\end{tabular}
\caption[Confusion]{\label{fig:jaccard}Scatter plot of Jaccard similarity and Euclidean distance. (a)Scatterplot showing Jaccard similarity and Euclidean distance of simple BERT, (b)Scatterplot of Jaccard similarity and Euclidean distance of PatentBERT, (c)Scatterplot of Jaccard similarity and Euclidean distance after learning simple BERT with Contrastive loss, (d)Scatter plot of Jaccard similarity and Euclidean distance after learning PatentBERT with Contrastive loss}
\end{figure}

%%% 예제 특허 그림 
\begin{figure}[t]
\includegraphics[width=.780\textwidth]{imgs/PatentExample2.pdf}
\caption{Examples with the same patent content but with low jaccard similarity, Both patents are related to the entrance of the can container, but the similarity of the jaccard was measured relatively low because the word composition of the text was different.} 
\label{fig:patentexample}
\end{figure}

%%% 자카드 유사도와 유클리디안 거리 비교 설명
USPTO에 기재되어있는 특허간의 자카드 유사도와 잠재공간 상에서 유클리드 거리 관계를 확인하기 위해 Fig.~\ref{fig:jaccard}와 같이 산점도 그림을 나타냈다. 
자카드 유사도와 유클리드 거리 관계를 확인함으로써 잠재공간 상에서 특허들의 분포가 자카드 유사도와 연관이 있는지 비교했다. 
Fig.~\ref{fig:jaccard}(a)와 (b)는 Contrastive learning으로 미세조정하지 않고 BERT와 PatentBERT의 출력으로 얻은 그림으로, 자카드 유사도가 감소함에 따라 유클리드 거리가 증가하는 경향이 있다. 
BERT모델들의 경우 특허 문서끼리 단어 구성이 유사성을 확인하는 자카드 유사도가 높을 수록 가깝게 배치하는 경향이 보였다. 
반면 Contrastive learning으로 미세조정 후 나타낸 Fig.~\ref{fig:jaccard}(c)와 (d)에서는 자카드 유사도와 유클리디안 거리 사이에 뚜렷한 경향이 나타나지 않는다. 
따라서 Contrastive learning를 거친 특허 문서들은 잠재공간에서 자카드 유사도와 연관 없이 배치되어있다는 것을 의미한다. 
실제로 유사한 특허 문서라 해도 단어의 구성이 다르면 자카드 유사도가 작은 값으로 측정될 수 있다. 
따라서 본 연구에서 사용한 모델은 단어의 구성이 다른 특허 문서의 경우에도 내용의 유사도에 따라 판단할 수 있다. 

%%% 실제 특허 문서 비교 예제 
%%%%%%%%%%%%% 용성: 자카드 유사도가 비교적 낮지만 유사하다는 말을 주장하려면, Contrastive learning을 통과한 후에 잠재공간에서 유클리디안 거리 & 클러스터 상에서 위치를 보여주어야할듯 합니다. 주장에 대한 근거 없이 자카드 유사도가 낮아도 관련이 존재한다는 내용같습니다. 
우리는 잠재공간 상에서 특허들간의 유클리드 거리에 문맥이 반영되었는지 확인하기 위해 인접한 특허들에 대한 내용을 확인했다. 
앞의 결과에서 보았듯이 자카드 유사도의 경우 글에서 단어 구성이 비슷할 수록 큰 값을 내뱉기에, 실제 글이 유사한 특허를 다룬다 하더라도 다른 단어를 쓰게 되면 자카드 유사도 값이 작아지게 된다. 
실제로 모델이 이러한 점을 잘 반영했는지 확인하기 위해 예시로 두개의 특허를 확인했다.
Fig.~\ref{fig:patentexample}를 보면 캔 용기의 입구와 관련된 두개의 특허가 있다 (U.S Patent No.3,696,961 (1972)\cite{us3696961}, U.S Patent No.3,705,563 (1972)\cite{us3705563}).
그림을 보면 두 특허 모두 캔 용기의 입구와 관련된 내용이지만, 글의 단어 구성이 다르기 때문에 자카드 유사도는 0.15로 비교적 낮게 측정되었다. 
자카드 유사도로 유사특허탐색을 진행한다면 Fig.~\ref{fig:patentexample}의 경우는 찾을 수 없다. 
하지만 Contrastive learning을 통해서 찾아내는 것이 가능하다. %%%% ? 이부분에 대한 근거가 부족하다 생각합니다. 
이처럼 비슷한 내용을 가지는 특허가 가깝게 위치하는 것으로부터 잠재공간 상 특허들의 분포에 문맥이 고려된 것으로 볼 수 있다.


\section{요약 및 결론}

특허 선행조사는 출원과정에 있어서 필요한 과정이지만 출원되는 특허들이 누적됨에 따라 특허 탐색 범위가 매우 넓어지는 어려움이 있다. 
본 연구는 Contrastive learning을 활용한 도메인 중심의 특허 군집화로 특허 선행조사의 탐색 범위를 줄여줄 수 있다. 
새롭게 출원하려는 특허를 본 모델에 적용한다면, 유사한 특허 섹션을 알아낼 수 있다.
뿐만 아니라 매우 유사한 특허들을 확인하여 특허 선행조사에 도움을 줄 수 있다.
또한 본 모델의 경우 특허 문서의 단어 구성보다 문맥을 중심으로 분석할 수 있다.
기존에 글의 유사도 측정에 활용되던 자카드 유사도의 경우 단어 구성이 달라지면 값이 낮아지는 경향이 존재한다. 
그러나 단어의 구성이 다르더라도 특허 기술의 내용이 유사할 경우도 있으므로 문서의 문맥을 파악하는 일은 중요하다.
우리는 결과를 통해 Contrastive learning은 단어의 구성과 관계 없이 유사한 특허를 찾는데 좋은 성능이 있다는 것을 보였다.
이러한 결과는 Contrastive learning은 단순히 단어의 구성과 빈도로 특허 문서를 파악하기 보다는 글의 문맥까지 파악하는 능력을 가졌다고 볼 수 있다.
또한 도메인 중심 군집화 과정을 통해 특허에 대한 섹션 분류에도 활용이 가능하다. 
본 연구의 방법론은 표절 문서 검사나 유사 내용 문서 선별과 같이 특허뿐만 아니라 다양한 영역에서 활용이 가능하다.
