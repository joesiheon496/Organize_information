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

# Contrastive Learning

유사한 특허를 탐색하는 용도로 쓰이던 PatentBERT와 Contrastive loss를 활용하여 Contrastive learning을 진행했다.  

우리는 특허 문서의 초록이라는 텍스트 형태의 데이터를 잠재공간에 올리기 위해, PatentBERT를 이용해서 텍스트 데이터를 벡터화했다.  

위 과정에서 벡터화된 특허 문서 초록은 잠재공간에 위차할 수 있게 된다.

PatentBERT를 통과한 벡터 형태의 특허 문서 초록은 글의 문맥 정보에 의해서만 잠재공간에 위치해 있게 되는데, 이는 도메인을 기반으로 만들어진 것이 아니기 때문에 도메인에 대한 미세조정 (fine-tuning)이 필요하다. 

따라서 특허 문서 초록들을 도메인에 대해 임베딩 시키기 위해 PatentBERT 이후에 순방향 신경망을 구성하고 추가로 구성한 부분에 대해서만 미세조정을 진행한다. 

PatentBERT와 Contrastive learning으로 미세조정한 모델은 BERT에 대해 Contrastive learning을 진행한 것 보다 군집화 측면에서 더 뛰어난 성능을 보인다. 

또한 잠재공간 상 유클리드 거리 기반 유사특허탐색에 있어서 다른 단어들로 구성이 되어있지만 내용이 유사한 특허를 찾아내는 것이 가능하다.

$$L_{i} = \sum_{j \in P}(y_{ij}D_{ij}^{2}+(1-y_{ij})\text{max}[0,\alpha-D_{ij}^{2}])$$

$i$와 $j$는 특허 쌍 $P$에 포함된 특허로, $y_{ij}$는 두 특허의 도메인이 일치하는지를 나타내는 라벨이다. 

특허 $i$와 $j$가 같은 섹션인 경우 $y_{ij}=1$이 되고, 다른 섹션이면 $y_{ij}=0$을 나타낸다. 

$\alpha$는 하이퍼파라미터로, 값을 조절함으로써 다른 섹션 특허와의 최소 거리를 설정 할 수 있다. 

Contrastive learning은 데이터 쌍에 대해 손실을 계산하여 모델을 학습시킨다. 

따라서 데이터 쌍을 구성하는 방법에 따라 학습 결과가 달라질 수 있다.
<img src='https://user-images.githubusercontent.com/56191064/214011050-2b3a7fe1-7a0b-4c89-85a0-2a9425d8864c.png' width="45%" height="45%">
<img src='https://user-images.githubusercontent.com/56191064/214011222-f4867d6b-d149-4b5d-974a-631dbc01d715.png' width="45%" height="45%">
