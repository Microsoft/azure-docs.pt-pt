---
title: Analisar o abandono de clientes
titleSuffix: Azure Machine Learning Studio
description: Estudo de caso de desenvolvimento de um modelo integrado para analisar e classificação de abandono de clientes com o Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 320dd28ba22d7919e3f3afdcd8baa47780f363dd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57856132"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio"></a>Analisar o abandono de clientes com o Azure Machine Learning Studio
## <a name="overview"></a>Descrição geral
Este artigo apresenta uma implementação de referência de um projeto de análise de alterações a dados de cliente que baseia-se com o Azure Machine Learning Studio. Neste artigo, discutimos a modelos genéricos associados para holística resolver o problema de abandono de clientes industriais. Podemos também medir a precisão de modelos que são criadas com Machine Learning e avaliar as direções para o desenvolvimento ainda mais.  

### <a name="acknowledgements"></a>Confirmações
Esta experiência foi desenvolvida e testada pela Serge Berger, cientista de dados Principal da Microsoft e Roger Barga, anteriormente conhecido como o gerente de produto do Microsoft Azure Machine Learning Studio. A equipe de documentação do Azure gratamente reconhece os seus conhecimentos e obrigado-los para partilhar este white paper.

> [!NOTE]
> Os dados utilizados para esta fase experimental não estão disponíveis publicamente. Para obter um exemplo de como criar um modelo de aprendizagem automática para análise de alterações a dados, consulte: [Modelo de modelo de abandono de varejo](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) em [Galeria de IA do Azure](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>O problema de abandono de clientes
As empresas no mercado consumidor e em todos os setores de enterprise tem de lidar com alterações a dados. Por vezes, é excessiva e influencia as decisões de política de alterações. A solução tradicional é prever churners propensão alta e atender as suas necessidades através de um serviço de assistente, campanhas, de marketing ou ao aplicar dispensations especiais. Essas abordagens podem variar do setor para setor. Ainda pode variar de um cluster de consumidor específica para outra dentro de um setor (por exemplo, telecomunicações).

O fator comum é que as empresas precisam minimizar esses esforços de retenção de clientes especial. Portanto, uma metodologia natural seria a classificar todos os clientes com a probabilidade de abandono e a abordar os N maiores aqueles. Os principais clientes podem ser as mais rentáveis. Por exemplo, em cenários mais sofisticados, uma função de lucro é empregada durante a seleção de candidatos para dispensation especial. No entanto, estas considerações são apenas uma parte da estratégia completa para lidar com alterações a dados. As empresas também deve levar em risco de conta (e tolerância a riscos associados), o nível e o custo da intervenção e segmentação de clientes plausível.  

## <a name="industry-outlook-and-approaches"></a>Outlook do setor e abordagens
Processamento sofisticado de alterações é um sinal de um setor madura. O exemplo clássico é a indústria de telecomunicações em que os assinantes são conhecidos com frequência alternar de um fornecedor para outro. Estas alterações voluntária é uma preocupação principal. Além disso, fornecedores acumulado um conhecimento significativo sobre *drivers de abandono*, que são os fatores que direcionam os clientes a mudar.

Por exemplo, escolha aparelho ou o dispositivo é um driver bem conhecido de rotatividade a empresa de telefone celular. Como resultado, uma política de popular é subsidize o preço de um monofone para novos subscritores e cobrar um preço integral, para os clientes existentes para uma atualização. Historicamente, esta política levou aos clientes saltos de um fornecedor para outro, para obter um desconto de novo. Isso, por sua vez, possui solicitado fornecedores para refinar suas estratégias.

Volatilidade elevada nas ofertas de aparelho é um fator que invalida rapidamente os modelos de alterações a dados que são baseados em modelos de aparelho atuais. Além disso, telemóveis não são apenas os dispositivos de telecomunicação, eles também são declarações de forma (considere iPhone). Estes indicadores sociais estão fora do escopo de conjuntos de dados de telecomunicações regular.

O resultado para a Modelagem é que não é possível planejar uma política de som simplesmente eliminando conhecidos motivos para o volume de alterações. Na verdade, é uma estratégia de modelagem contínua, incluindo modelos clássicos que quantificam as variáveis de categóricas (como árvores de decisão), **obrigatório**.

Utilizar conjuntos de macrodados em seus clientes, as organizações estão a efetuar análise de macrodados (em especial, deteção de alterações a dados com base em grandes volumes de dados) como uma abordagem eficaz para o problema. Pode encontrar mais informações sobre a abordagem de grandes volumes de dados para o problema de rotatividade as recomendações na seção ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodologia para o abandono de clientes do modelo
Um processo de resolução de problemas comuns para resolver o abandono de clientes está descrito em números de 1 a 3:  

1. Um modelo de risco permite que considere o impacto das ações probabilidade e o risco.
2. Um modelo de intervenção permite que considere a forma como o nível de intervenção pode afetar a probabilidade de fluxo de dados e a quantidade de cliente o valor de tempo de vida (CLV).
3. Esta análise se presta a uma análise de qualitativo será escalada para uma campanha de marketing proativa que tenha como destino os segmentos de clientes para fornecer a oferta ideal.  

![Diagrama que mostra como modelos de decisão além de tolerância a riscos gera informações acionáveis](./media/azure-ml-customer-churn-scenario/churn-1.png)

Essa abordagem para a frente atraente é a melhor forma de tratar alterações a dados, mas ele vem com a complexidade: temos que desenvolver um mvn com vários modelo e as dependências de rastreamento entre os modelos. A interação entre os modelos pode ser encapsulada como mostrado no diagrama seguinte:  

![Diagrama do modelo de interação de alterações](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Figura 4: Unificada mvn com vários modelo*  

Interação entre os modelos é a chave se estivermos fornecer uma abordagem holística para retenção de clientes. Cada modelo necessariamente degrada o ao longo do tempo; Por conseguinte, a arquitetura é um loop implícito (semelhante ao mvn definido pelo padrão de extração de dados de CRISP DM, [***3***]).  

O ciclo de geral de risco-decisão-marketing segmentação/hierárquica ainda é uma estrutura generalizada, o que é aplicável a vários problemas de negócios. A análise de alterações a dados é simplesmente um representante forte deste grupo de problemas, porque ele exibe um todos os traços de um problema de negócio complexa que não permite uma solução preditiva simplificada. Os aspectos de redes sociais da abordagem moderna para alterações a dados não são particularmente realçados na abordagem, mas os aspectos de redes sociais são encapsulados em mvn modelagem, como elas podem ser em qualquer modelo.  

Uma adição interessante aqui é a análise de macrodados. Hoje em dia telecomunicação e empresas de varejo recolhem dados exaustivos sobre os seus clientes e previr pode facilmente que a necessidade de conectividade com vários modelo irá tornar-se de uma tendência comuns, determinada emergentes tendências, como a Internet das coisas e mais usada dispositivos, que permitem empresa empregar soluções inteligentes em múltiplas camadas.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementando o mvn de modelação no Machine Learning Studio
Tendo em conta o problema descrito, o que é a melhor forma de implementar uma modelagem integrada e a abordagem de classificação? Nesta secção, vamos demonstrar como conseguimos isso com o Azure Machine Learning Studio.  

A abordagem com vários modelo é essencial ao projetar um mvn global para o volume de alterações. Até mesmo a classificação (preditiva) parte da abordagem deve ser com vários modelo.  

O diagrama seguinte mostra o protótipo que criámos, que utiliza quatro algoritmos de classificação no Machine Learning Studio para prever o volume de alterações. O motivo para usar uma abordagem com vários modelo não é apenas para criar um classificador de ensemble para aumentar a precisão, mas também para proteger contra ajuste excessivo e para melhorar a seleção de funcionalidades prescritivas.  

![Captura de ecrã que mostram uma área de trabalho complexa do Studio com muitos módulos interconectados](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Figura 5: Protótipo de uma abordagem de modelagem de alterações*  

As secções seguintes fornecem mais detalhes sobre o modelo que foi implementados com o Machine Learning Studio de classificação de protótipo.  

### <a name="data-selection-and-preparation"></a>Seleção de dados e a preparação
Os dados utilizados para criar os modelos e os clientes de pontuação foi obtido a partir de uma solução de vertical de CRM, com os dados oculto para proteger a privacidade dos clientes. Os dados contêm informações sobre as assinaturas de 8.000 nos EUA e combina três origens: aprovisionamento de dados (metadados de subscrição), dados de atividade (utilização do sistema) e dados de suporte do cliente. Os dados não inclui quaisquer informações relacionadas com negócios sobre clientes; Por exemplo, não inclui as pontuações de metadados ou o crédito de fidelização.  

Para simplificar, ETL e processos de limpeza de dados estão fora do âmbito porque partimos do princípio de que a preparação de dados já foi feitos noutro local.

Seleção de funcionalidades para a Modelagem é baseada em significância preliminar de classificação do conjunto de indicadores, incluídos no processo que utiliza o módulo de floresta aleatório. Para a implementação no Machine Learning Studio, vamos calculada a média, mediana e intervalos para as funcionalidades representativas. Por exemplo, adicionamos agregados para os dados qualitativos, por exemplo, valores mínimos e máximo para a atividade do utilizador.

Também capturamos o temporais informações para os mais recentes seis meses. Analisámos dados durante um ano e Estabelecemos que mesmo que haja tendências estatisticamente significativas, o efeito sobre o volume de alterações é muito reduzido depois de seis meses.  

O ponto mais importante é que o processo inteiro, incluindo ETL, seleção de funcionalidades e modelagem foi implementado no Machine Learning Studio, utilizar origens de dados no Microsoft Azure.   

Os diagramas seguintes mostram os dados que foi utilizados.  

![Captura de ecrã que mostra um exemplo dos dados utilizados com valores não processados](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Figura 6: Trecho de origem de dados (oculto)*  

![Captura de ecrã que mostra estatísticos recursos extraídos da origem de dados](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Figura 7: Recursos extraídos da origem de dados*
 

> Tenha em atenção que estes dados são privados e, portanto, não não possível partilhar o modelo e os dados.
> No entanto, para um modelo semelhante usando dados publicamente disponíveis, veja este exemplo na experimentação da [Galeria de IA do Azure](https://gallery.azure.ai/): [Telco Customer Churn](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Para saber mais sobre como implementar um modelo de análise de alterações a dados com o Cortana Intelligence Suite, também é recomendável [este vídeo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) pelo gerente de programas Sênior Wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmos utilizados no protótipo
Usamos os seguintes quatro algoritmos de machine learning para criar o protótipo (nenhuma personalização):  

1. Regressão logística (LR)
2. Árvore de decisões elevada (BT)
3. Média perceptron (AP)
4. Máquina de vetor com suporte (SVM)  

O diagrama seguinte ilustra uma parte da superfície de design experimentação, que indica a seqüência em que foram criados os modelos:  

![Captura de ecrã de uma pequena seção da experimentação do studio baseadas em telas](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Figura 8: A criação de modelos de Machine Learning Studio*  

### <a name="scoring-methods"></a>Métodos de classificação
Podemos classificadas de quatro modelos usando um conjunto de dados de treinamento etiquetadas.  

Podemos também submetido o classificação do conjunto de dados para um modelo comparável criado utilizando a ambiente de trabalho edição do SAS Enterprise Miner 12. Podemos medida a precisão do modelo SAS e todos os quatro modelos de Machine Learning Studio.  

## <a name="results"></a>Resultados
Nesta seção, Apresentaremos nossos descobertas sobre a precisão dos modelos, com base no conjunto de dados de classificação.  

### <a name="accuracy-and-precision-of-scoring"></a>Precisão e a precisão da classificação
Em geral, a implementação no Azure Machine Learning Studio é por trás do SAS precisão em cerca de 10 a 15% (área em curva ou AUC).  

No entanto, a métrica mais importante no volume de alterações é a taxa de misclassification: ou seja, de churners de principais N como previsto pelo classificador, que eles realmente de fez **não** alterações a dados e ainda recebeu um tratamento especial? O diagrama a seguir compara essa taxa de misclassification para todos os modelos:  

![Área de gráfico de curva comparando o desempenho dos algoritmos de 4](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Figura 9: Área de protótipo Passau em curva*

### <a name="using-auc-to-compare-results"></a>Usando AUC para comparar os resultados
Área na curva (AUC) é uma métrica que representa uma medida global de *separability* entre as distribuições de pontuações para populações positivas e negativas. Ele é semelhante ao gráfico característica de operador de destinatário (ROC) tradicional, mas uma diferença importante é que a métrica AUC não requer que escolha um valor de limiar. Em vez disso, ele resume os resultados ao longo **todos os** opções possíveis. Por outro lado, o gráfico ROC tradicional mostra a taxa de positiva sobre o eixo vertical e a taxa de falsos positivos no eixo horizontal e o limiar de classificação varia.   

AUC é utilizado como um indicador de que vale a pena para algoritmos diferentes (ou sistemas diferentes), porque ela permite que os modelos de ser comparadas por meio de seus valores AUC. Essa é uma abordagem popular em setores como meteorology e biosciences. Portanto, AUC representa uma ferramenta popular para avaliar o desempenho do classificador.  

### <a name="comparing-misclassification-rates"></a>Comparando as taxas de misclassification
Estamos em comparação com as tarifas de misclassification no conjunto de dados em questão utilizando os dados CRM de aproximadamente 8.000 subscrições.  

* A taxa de misclassification SAS era de 10 a 15%.
* A taxa de misclassification de Machine Learning Studio foi de 15 a 20% para os churners superior 200 e 300.  

No ramo de telecomunicações, é importante resolver apenas aqueles clientes que têm o risco mais elevado de abandono oferecendo um serviço de assistente ou outro tratamento especial. Nesse ponto, a implementação de Machine Learning Studio alcança resultados no mesmo nível do modelo SAS.  

Pelo mesmo token, precisão é mais importante do que a precisão, uma vez que estão mais interessados corretamente classificar churners potenciais.  

O diagrama seguinte da Wikipedia descreve a relação num elemento de gráfico animada, fácil de compreender:  

![Dois destinos. Mostra um destino pressionar marcas livremente agrupadas mas perto o olho-de-bulls marcados como "precisão de baixa: trueness boa, precisão fraco. Outro destino rigidamente agrupados, mas longe de ser o olho-de-bulls marcado como "precisão de baixa: trueness fraco, precisão bom"](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Figura 10: Variação entre a precisão e a precisão*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Resultados de precisão e a precisão para o modelo de árvore de decisões elevada
A tabela a seguir apresenta os resultados brutos de usando o protótipo de Machine Learning para o modelo de árvore de decisões elevada, o que vem a ser mais precisos entre quatro modelos de classificação:  

![O trecho de código de tabela que mostra a precisão, precisão, Recall, pontuação F, AUC, perda de registo médio e perda de registo de treinamento para quatro algoritmos](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Figura 11: Características do modelo de árvore de decisões elevada*

## <a name="performance-comparison"></a>Comparação de desempenho
Estamos em comparação com a velocidade a que dados foi classificados com os modelos de Machine Learning Studio e um modelo de comparável criados com a edição de área de trabalho do SAS Enterprise Miner 12.1.  

A tabela seguinte resume o desempenho dos algoritmos de:  

*Tabela 1. Gerais de desempenho (precisão) dos algoritmos*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Modelo de média |O melhor modelo |Com um desempenho baixo |Modelo de média |

Os modelos alojados no Machine Learning Studio ultrapassou o desempenho do SAS por 15 a 25% para a velocidade de execução, mas precisão foi amplamente no mesmo nível.  

## <a name="discussion-and-recommendations"></a>Discussão e recomendações
No ramo de telecomunicações, várias práticas surgiram para analisar a perda, incluindo:  

* Obter métricas para quatro categorias fundamentais:
  * **Entidade (por exemplo, uma subscrição)**. Aprovisionar informações básicas sobre a subscrição e/ou o cliente que é o assunto do volume de alterações.
  * **Atividade**. Obter todas as informações de uso possível que está relacionado com a entidade, por exemplo, o número de inícios de sessão.
  * **Suporte ao cliente**. Coletar informações dos registos de suporte de cliente para indicar se a subscrição tinha problemas ou as interações com o suporte ao cliente.
  * **Dados empresariais e competitivos**. Obter todas as informações possíveis sobre o cliente (por exemplo, pode estar indisponível ou difíceis de rastrear).
* Utilize a importância para seleção de funcionalidades de unidade. Isso implica que o modelo de árvore de decisões elevada sempre é uma abordagem promissor.  

A utilização destas quatro categorias cria a ilusão de que um simples *determinística* abordagem, com base nos índices formados em fatores razoáveis por categoria, deve ser suficientes para identificar os clientes em risco para o volume de alterações. Infelizmente, embora essa noção parece plausível, é uma compreensão FALSO. O motivo é que o volume de alterações é um efeito temporal e os fatores que contribuem para alterações a dados normalmente estão a ser Estados transitórios. O que leva um cliente a serem considerados deixando hoje pode ser diferente, amanhã, e certamente será diferente seis meses de agora. Por conseguinte, um *probabilístico* modelo é uma necessidade.  

Esta observação importante é muitas vezes ignorada na empresas, que, geralmente, prefere uma abordagem de business intelligence e orientados a análise, principalmente porque é um mais fácil vender e admits automatização simples.  

No entanto, a promessa de análises de autoatendimento com o Machine Learning Studio é que as quatro categorias de informações, classificados pela divisão ou departamento, tornar-se uma fonte valiosa para machine learning sobre alterações a dados.  

Outro recurso interessante do Azure Machine Learning Studio é a capacidade de adicionar um módulo personalizado para o repositório de módulos predefinidos que já estão disponíveis. Esta capacidade, essencialmente, cria uma oportunidade de selecionar bibliotecas e criar modelos para mercados verticais. É um diferenciador importante do Azure Machine Learning Studio no mercado.  

Esperamos que continue neste tópico no futuro, principalmente relacionadas com a análise de macrodados.
  

## <a name="conclusion"></a>Conclusão
Este documento descreve um método sensato para lidar com o problema comum de abandono de clientes utilizando uma estrutura genérica. É considerada um protótipo para modelos de classificação e a Implementei com o Azure Machine Learning Studio. Por fim, temos avaliado a precisão e o desempenho da solução de protótipo em relação a algoritmos comparáveis no SAS.  

 

## <a name="references"></a>Referências
[1] análise de preditiva: Além do gerenciamento de informações Predições, McKnight Ocidental, Julho/Agosto de 2011, p.18 e 20.  

[2] artigo da Wikipedia: [Precisão e a precisão](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Guia de mineração de dados passo a passo](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Marketing de grandes volumes de dados: Cative os clientes com mais eficiência e o valor da unidade](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [modelo de modelo de abandono de telecomunicações](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) no [Galeria de IA do Azure](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Anexo
![Instantâneo de uma apresentação no protótipo de alterações a dados](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Figura 12: Instantâneo de uma apresentação no protótipo de alterações a dados*
