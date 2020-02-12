---
title: Analisar o churn do cliente
titleSuffix: ML Studio (classic) - Azure
description: Estudo de caso para desenvolver um modelo integrado para analisar e pontuar o cliente através do Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 903e3f3dcbcc72289fc82ec59dec0305b6adbc17
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150923"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Analise o Customer Churn usando o Azure Machine Learning Studio (clássico)
## <a name="overview"></a>Descrição geral
Este artigo apresenta uma implementação de referência de um projeto de análise de churn do cliente que é construído usando o Azure Machine Learning Studio (clássico). Neste artigo, discutimos a modelos genéricos associados para holística resolver o problema de abandono de clientes industriais. Podemos também medir a precisão de modelos que são criadas com Machine Learning e avaliar as direções para o desenvolvimento ainda mais.  

### <a name="acknowledgements"></a>Confirmações
Esta experiência foi desenvolvida e testada por Serge Berger, Principal Data Scientist da Microsoft, e Roger Barga, antigo Product Manager do Microsoft Azure Machine Learning Studio (clássico). A equipe de documentação do Azure gratamente reconhece os seus conhecimentos e obrigado-los para partilhar este white paper.

> [!NOTE]
> Os dados utilizados para esta fase experimental não estão disponíveis publicamente. Para um exemplo de como construir um modelo de aprendizagem automática para análise de churn, consulte: Modelo de [modelo de retalho](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) churn na Galeria [Azure AI](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>O problema de abandono de clientes
As empresas no mercado consumidor e em todos os setores de enterprise tem de lidar com alterações a dados. Por vezes, é excessiva e influencia as decisões de política de alterações. A solução tradicional é prever churners propensão alta e atender as suas necessidades através de um serviço de assistente, campanhas, de marketing ou ao aplicar dispensations especiais. Essas abordagens podem variar do setor para setor. Ainda pode variar de um cluster de consumidor específica para outra dentro de um setor (por exemplo, telecomunicações).

O fator comum é que as empresas precisam minimizar esses esforços de retenção de clientes especial. Portanto, uma metodologia natural seria a classificar todos os clientes com a probabilidade de abandono e a abordar os N maiores aqueles. Os principais clientes podem ser as mais rentáveis. Por exemplo, em cenários mais sofisticados, uma função de lucro é empregada durante a seleção de candidatos para dispensation especial. No entanto, estas considerações são apenas uma parte da estratégia completa para lidar com alterações a dados. As empresas também deve levar em risco de conta (e tolerância a riscos associados), o nível e o custo da intervenção e segmentação de clientes plausível.  

## <a name="industry-outlook-and-approaches"></a>Outlook do setor e abordagens
Processamento sofisticado de alterações é um sinal de um setor madura. O exemplo clássico é a indústria de telecomunicações em que os assinantes são conhecidos com frequência alternar de um fornecedor para outro. Estas alterações voluntária é uma preocupação principal. Além disso, os fornecedores acumularam conhecimentos significativos sobre *os condutores*de churns , que são os fatores que levam os clientes a mudar.

Por exemplo, escolha aparelho ou o dispositivo é um driver bem conhecido de rotatividade a empresa de telefone celular. Como resultado, uma política de popular é subsidize o preço de um monofone para novos subscritores e cobrar um preço integral, para os clientes existentes para uma atualização. Historicamente, esta política levou aos clientes saltos de um fornecedor para outro, para obter um desconto de novo. Isso, por sua vez, possui solicitado fornecedores para refinar suas estratégias.

Volatilidade elevada nas ofertas de aparelho é um fator que invalida rapidamente os modelos de alterações a dados que são baseados em modelos de aparelho atuais. Além disso, telemóveis não são apenas os dispositivos de telecomunicação, eles também são declarações de forma (considere iPhone). Estes indicadores sociais estão fora do escopo de conjuntos de dados de telecomunicações regular.

O resultado para a Modelagem é que não é possível planejar uma política de som simplesmente eliminando conhecidos motivos para o volume de alterações. De facto, é **obrigatória**uma estratégia de modelação contínua, incluindo modelos clássicos que quantificam variáveis categóricas (como árvores de decisão).

Utilizar conjuntos de macrodados em seus clientes, as organizações estão a efetuar análise de macrodados (em especial, deteção de alterações a dados com base em grandes volumes de dados) como uma abordagem eficaz para o problema. Pode encontrar mais informações sobre a abordagem de grandes volumes de dados para o problema de rotatividade as recomendações na seção ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodologia para o abandono de clientes do modelo
Um processo de resolução de problemas comuns para resolver o abandono de clientes está descrito em números de 1 a 3:  

1. Um modelo de risco permite que considere o impacto das ações probabilidade e o risco.
2. Um modelo de intervenção permite que considere a forma como o nível de intervenção pode afetar a probabilidade de fluxo de dados e a quantidade de cliente o valor de tempo de vida (CLV).
3. Esta análise se presta a uma análise de qualitativo será escalada para uma campanha de marketing proativa que tenha como destino os segmentos de clientes para fornecer a oferta ideal.  

![Diagrama mostrando como a tolerância ao risco mais os modelos de decisão produzem insights atuais](./media/azure-ml-customer-churn-scenario/churn-1.png)

Essa abordagem para a frente atraente é a melhor forma de tratar alterações a dados, mas ele vem com a complexidade: temos que desenvolver um mvn com vários modelo e as dependências de rastreamento entre os modelos. A interação entre os modelos pode ser encapsulada como mostrado no diagrama seguinte:  

![Diagrama de interação de modelo churn](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Figura 4: Arquétipo multimodelo unificado*  

Interação entre os modelos é a chave se estivermos fornecer uma abordagem holística para retenção de clientes. Cada modelo necessariamente degrada-se ao longo do tempo; portanto, a arquitetura é um laço implícito (semelhante ao arquétipo definido pela norma de mineração de dados CRISP-DM, [***3]).***  

O ciclo de geral de risco-decisão-marketing segmentação/hierárquica ainda é uma estrutura generalizada, o que é aplicável a vários problemas de negócios. A análise de alterações a dados é simplesmente um representante forte deste grupo de problemas, porque ele exibe um todos os traços de um problema de negócio complexa que não permite uma solução preditiva simplificada. Os aspectos de redes sociais da abordagem moderna para alterações a dados não são particularmente realçados na abordagem, mas os aspectos de redes sociais são encapsulados em mvn modelagem, como elas podem ser em qualquer modelo.  

Uma adição interessante aqui é a análise de macrodados. Hoje em dia telecomunicação e empresas de varejo recolhem dados exaustivos sobre os seus clientes e previr pode facilmente que a necessidade de conectividade com vários modelo irá tornar-se de uma tendência comuns, determinada emergentes tendências, como a Internet das coisas e mais usada dispositivos, que permitem empresa empregar soluções inteligentes em múltiplas camadas.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Implementação do arquétipo de modelação no Machine Learning Studio (clássico)
Tendo em conta o problema descrito, qual é a melhor forma de implementar uma abordagem integrada de modelação e pontuação? Nesta secção, vamos demonstrar como o conseguimos utilizando o Azure Machine Learning Studio (clássico).  

A abordagem com vários modelo é essencial ao projetar um mvn global para o volume de alterações. Até mesmo a classificação (preditiva) parte da abordagem deve ser com vários modelo.  

O diagrama seguinte mostra o protótipo que criamos, que emprega quatro algoritmos de pontuação no Machine Learning Studio (clássico) para prever o churn. O motivo para usar uma abordagem com vários modelo não é apenas para criar um classificador de ensemble para aumentar a precisão, mas também para proteger contra ajuste excessivo e para melhorar a seleção de funcionalidades prescritivas.  

![Screenshot retratando um complexo espaço de trabalho studio (clássico) com muitos módulos interligados](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Figura 5: Protótipo de uma abordagem de modelação de churn*  

As seguintes secções fornecem mais detalhes sobre o modelo de pontuação do protótipo que implementamos utilizando o Machine Learning Studio (clássico).  

### <a name="data-selection-and-preparation"></a>Seleção de dados e a preparação
Os dados utilizados para criar os modelos e os clientes de pontuação foi obtido a partir de uma solução de vertical de CRM, com os dados oculto para proteger a privacidade dos clientes. Os dados contêm informações sobre as assinaturas de 8.000 nos EUA e combina três origens: aprovisionamento de dados (metadados de subscrição), dados de atividade (utilização do sistema) e dados de suporte do cliente. Os dados não incluem qualquer informação relacionada com o negócio sobre os clientes; por exemplo, não inclui metadados de fidelização ou pontuações de crédito.  

Para simplificar, ETL e processos de limpeza de dados estão fora do âmbito porque partimos do princípio de que a preparação de dados já foi feitos noutro local.

Seleção de funcionalidades para a Modelagem é baseada em significância preliminar de classificação do conjunto de indicadores, incluídos no processo que utiliza o módulo de floresta aleatório. Para a implementação no Machine Learning Studio (clássico), calculamos a média, mediana e gamas para características representativas. Por exemplo, adicionamos agregados para os dados qualitativos, por exemplo, valores mínimos e máximo para a atividade do utilizador.

Também capturamos o temporais informações para os mais recentes seis meses. Analisámos dados durante um ano e Estabelecemos que mesmo que haja tendências estatisticamente significativas, o efeito sobre o volume de alterações é muito reduzido depois de seis meses.  

O ponto mais importante é que todo o processo, incluindo eTL, seleção de funcionalidades e modelação foi implementado no Machine Learning Studio (clássico), utilizando fontes de dados no Microsoft Azure.   

Os diagramas seguintes mostram os dados que foi utilizados.  

![Screenshot mostrando uma amostra dos dados utilizados com valores brutos](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Figura 6: Excerto da fonte de dados (obfuscated)*  

![Screenshot mostrando características estatísticas extraídas de fonte de dados](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Figura 7: Características extraídas de fonte de dados*
 

> Tenha em atenção que estes dados são privados e, portanto, não não possível partilhar o modelo e os dados.
> No entanto, para um modelo semelhante utilizando dados disponíveis publicamente, consulte esta experiência de amostra na [Galeria Azure AI](https://gallery.azure.ai/): [Telco Customer Churn](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Para saber mais sobre como pode implementar um modelo de análise de churn usando cortana Intelligence Suite, também recomendamos [este vídeo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) pelo Gestor sénior de Programas Wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmos utilizados no protótipo
Usamos os seguintes quatro algoritmos de machine learning para criar o protótipo (nenhuma personalização):  

1. Regressão logística (LR)
2. Árvore de decisões elevada (BT)
3. Média perceptron (AP)
4. Máquina de vetor com suporte (SVM)  

O diagrama seguinte ilustra uma parte da superfície de design experimentação, que indica a seqüência em que foram criados os modelos:  

![Screenshot de uma pequena secção da tela de experiência do estúdio](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Figura 8: Criação de modelos no Machine Learning Studio (clássico)*  

### <a name="scoring-methods"></a>Métodos de classificação
Podemos classificadas de quatro modelos usando um conjunto de dados de treinamento etiquetadas.  

Podemos também submetido o classificação do conjunto de dados para um modelo comparável criado utilizando a ambiente de trabalho edição do SAS Enterprise Miner 12. Medimos a precisão do modelo SAS e dos quatro modelos machine learning studio (clássicos).  

## <a name="results"></a>Resultados
Nesta seção, Apresentaremos nossos descobertas sobre a precisão dos modelos, com base no conjunto de dados de classificação.  

### <a name="accuracy-and-precision-of-scoring"></a>Precisão e a precisão da classificação
Geralmente, a implementação no Azure Machine Learning Studio (clássico) está por trás da SAS em precisão em cerca de 10-15% (Área Sob Curva ou AUC).  

No entanto, a métrica mais importante em churn é a taxa de desclassificação: isto é, dos melhores n churners como previsto pelo classificador, qual deles realmente **não** rebentou, e ainda recebeu tratamento especial? O diagrama a seguir compara essa taxa de misclassification para todos os modelos:  

![Área sob o gráfico de curva comparando o desempenho de 4 algoritmos](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Figura 9: Área do protótipo passau sob curva*

### <a name="using-auc-to-compare-results"></a>Usando AUC para comparar os resultados
A Área Sob Curva (AUC) é uma métrica que representa uma medida global de *separabilidade* entre a distribuição de pontuações para populações positivas e negativas. Ele é semelhante ao gráfico característica de operador de destinatário (ROC) tradicional, mas uma diferença importante é que a métrica AUC não requer que escolha um valor de limiar. Em vez disso, resume os resultados sobre **todas as** escolhas possíveis. Por outro lado, o gráfico ROC tradicional mostra a taxa de positiva sobre o eixo vertical e a taxa de falsos positivos no eixo horizontal e o limiar de classificação varia.   

A Uc é usada como uma medida de valor para diferentes algoritmos (ou sistemas diferentes) porque permite que os modelos sejam comparados através dos seus valores AUC. Essa é uma abordagem popular em setores como meteorology e biosciences. Portanto, AUC representa uma ferramenta popular para avaliar o desempenho do classificador.  

### <a name="comparing-misclassification-rates"></a>Comparando as taxas de misclassification
Estamos em comparação com as tarifas de misclassification no conjunto de dados em questão utilizando os dados CRM de aproximadamente 8.000 subscrições.  

* A taxa de misclassification SAS era de 10 a 15%.
* A taxa de classificação errada do Machine Learning Studio (clássico) foi de 15-20% para os 200-300 melhores churners.  

No ramo de telecomunicações, é importante resolver apenas aqueles clientes que têm o risco mais elevado de abandono oferecendo um serviço de assistente ou outro tratamento especial. A este respeito, a implementação do Machine Learning Studio (clássico) obtém resultados a par com o modelo SAS.  

Pelo mesmo token, precisão é mais importante do que a precisão, uma vez que estão mais interessados corretamente classificar churners potenciais.  

O diagrama seguinte da Wikipedia descreve a relação num elemento de gráfico animada, fácil de compreender:  

![Dois alvos. Um alvo mostra marcas de sucesso vagamente agruparadas, mas perto do olho de touro com a marca "Baixa precisão: boa verdade, má precisão. Outro alvo bem agrupado, mas longe do olho-touro marcado "Baixa precisão: má verdade, boa precisão"](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Figura 10: Troca entre precisão e precisão*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Resultados de precisão e a precisão para o modelo de árvore de decisões elevada
A tabela a seguir apresenta os resultados brutos de usando o protótipo de Machine Learning para o modelo de árvore de decisões elevada, o que vem a ser mais precisos entre quatro modelos de classificação:  

![Corte de mesa mostrando precisão, precisão, recordação, F-Score, AUC, Perda média de registo e perda de registo de treino para quatro algoritmos](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Figura 11: Características do modelo da árvore de decisão reforçada*

## <a name="performance-comparison"></a>Comparação de desempenho
Comparamos a velocidade com que os dados foram obtidos utilizando os modelos Machine Learning Studio (clássico) e um modelo comparável criado através da edição de desktop do SAS Enterprise Miner 12.1.  

A tabela seguinte resume o desempenho dos algoritmos de:  

*Mesa 1. Desempenho geral (precisão) dos algoritmos*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Modelo de média |O melhor modelo |Com um desempenho baixo |Modelo de média |

Os modelos hospedados no Machine Learning Studio (clássico) superaram o SAS em 15-25% para a velocidade de execução, mas a precisão foi em grande parte a par.  

## <a name="discussion-and-recommendations"></a>Discussão e recomendações
No ramo de telecomunicações, várias práticas surgiram para analisar a perda, incluindo:  

* Obter métricas para quatro categorias fundamentais:
  * **Entidade (por exemplo, uma subscrição)** . Aprovisionar informações básicas sobre a subscrição e/ou o cliente que é o assunto do volume de alterações.
  * **Atividade**. Obter todas as informações de uso possível que está relacionado com a entidade, por exemplo, o número de inícios de sessão.
  * **Apoio ao cliente**. Coletar informações dos registos de suporte de cliente para indicar se a subscrição tinha problemas ou as interações com o suporte ao cliente.
  * **Dados competitivos e empresariais.** Obter todas as informações possíveis sobre o cliente (por exemplo, pode estar indisponível ou difíceis de rastrear).
* Utilize a importância para seleção de funcionalidades de unidade. Isso implica que o modelo de árvore de decisões elevada sempre é uma abordagem promissor.  

A utilização destas quatro categorias cria a ilusão de que uma abordagem *determinística* simples, baseada em índices formados em fatores razoáveis por categoria, deve ser suficiente para identificar os clientes em risco de agitação. Infelizmente, embora essa noção parece plausível, é uma compreensão FALSO. O motivo é que o volume de alterações é um efeito temporal e os fatores que contribuem para alterações a dados normalmente estão a ser Estados transitórios. O que leva um cliente a serem considerados deixando hoje pode ser diferente, amanhã, e certamente será diferente seis meses de agora. Portanto, um modelo *probabilístico* é uma necessidade.  

Esta observação importante é muitas vezes ignorada na empresas, que, geralmente, prefere uma abordagem de business intelligence e orientados a análise, principalmente porque é um mais fácil vender e admits automatização simples.  

No entanto, a promessa de análise de autosserviço usando o Machine Learning Studio (clássico) é que as quatro categorias de informação, classificadas por divisão ou departamento, se tornam uma valiosa fonte de aprendizagem automática sobre churn.  

Outra capacidade emocionante que chega no Azure Machine Learning Studio (clássico) é a capacidade de adicionar um módulo personalizado ao repositório de módulos pré-definidos que já estão disponíveis. Esta capacidade, essencialmente, cria uma oportunidade de selecionar bibliotecas e criar modelos para mercados verticais. É um diferenciador importante do Azure Machine Learning Studio (clássico) no mercado.  

Esperamos que continue neste tópico no futuro, principalmente relacionadas com a análise de macrodados.
  

## <a name="conclusion"></a>Conclusão
Este documento descreve um método sensato para lidar com o problema comum de abandono de clientes utilizando uma estrutura genérica. Considerámos um protótipo para a pontuação de modelos e implementámo-lo utilizando o Azure Machine Learning Studio (clássico). Por fim, temos avaliado a precisão e o desempenho da solução de protótipo em relação a algoritmos comparáveis no SAS.  

 

## <a name="references"></a>Referências
[1] Análise Preditiva: Para além das Previsões, W. McKnight, Gestão da Informação, julho/agosto de 2011, p.18-20.  

[2] Artigo da Wikipédia: [Precisão e precisão](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Guia de mineração](https://www.the-modeling-agency.com/crisp-dm.pdf) de dados passo a passo   

[4] [Big Data Marketing: Envolver os seus clientes de forma mais eficaz e impulsionar valor](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Modelo de telco churn](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) na Galeria [Azure AI](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Anexo
![Instantâneo de uma apresentação sobre o protótipo churn](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Figura 12: Instantâneo de uma apresentação sobre o protótipo churn*
