---
title: Analisar o churn do cliente
titleSuffix: ML Studio (classic) - Azure
description: Estudo de caso para desenvolver um modelo integrado para analisar e pontuar o cliente através do Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 4cf918abae51ca330054ef86e57095d29a21a37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204533"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Analise o Customer Churn usando o Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Descrição geral
Este artigo apresenta uma implementação de referência de um projeto de análise de churn do cliente que é construído usando o Azure Machine Learning Studio (clássico). Neste artigo, discutimos modelos genéricos associados para resolver holisticamente o problema do cliente industrial. Também medimos a precisão dos modelos que são construídos através da Machine Learning, e avaliamos direções para o desenvolvimento futuro.  

### <a name="acknowledgements"></a>Agradecimentos
Esta experiência foi desenvolvida e testada por Serge Berger, Principal Data Scientist da Microsoft, e Roger Barga, antigo Product Manager do Microsoft Azure Machine Learning Studio (clássico). A equipa de documentação azure agradece a sua experiência e agradece-lhes por partilharem este livro branco.

> [!NOTE]
> Os dados utilizados para esta experiência não estão disponíveis ao público. Para um exemplo de como construir um modelo de aprendizagem automática para análise de churn, consulte: Modelo de [modelo de retalho](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) churn na Galeria [Azure AI](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>O problema do cliente churn
As empresas do mercado de consumo e de todos os sectores empresariais têm de lidar com a agitação. Por vezes, o churn é excessivo e influencia as decisões políticas. A solução tradicional é prever churners de alta propensão e responder às suas necessidades através de um serviço de concierge, campanhas de marketing ou aplicando dispensas especiais. Estas abordagens podem variar de indústria para indústria. Podem mesmo variar de um determinado aglomerado de consumidores para outro dentro de uma indústria (por exemplo, telecomunicações).

O fator comum é que as empresas precisam de minimizar estes esforços especiais de retenção de clientes. Assim, uma metodologia natural seria marcar todos os clientes com a probabilidade de agitar e dirigir-se aos níbes superiores. Os clientes de topo podem ser os mais rentáveis. Por exemplo, em cenários mais sofisticados é utilizada uma função de lucro durante a seleção de candidatos para dispensa especial. No entanto, estas considerações são apenas uma parte da estratégia completa para lidar com a agitação. As empresas também têm de ter em conta o risco (e a tolerância ao risco associada), o nível e o custo da intervenção e a segmentação plausível do cliente.  

## <a name="industry-outlook-and-approaches"></a>Perspetivas e abordagens da indústria
O tratamento sofisticado do churn é um sinal de uma indústria madura. O exemplo clássico é a indústria das telecomunicações, onde os assinantes são conhecidos por mudar frequentemente de um fornecedor para outro. Esta agitação voluntária é uma preocupação primordial. Além disso, os fornecedores acumularam conhecimentos significativos sobre *os condutores*de churns , que são os fatores que levam os clientes a mudar.

Por exemplo, a escolha do aparelho ou do dispositivo é um conhecido condutor de churn no negócio do telemóvel. Como resultado, uma política popular é subsidiar o preço de um telefone para novos subscritores e cobrar um preço total aos clientes existentes para uma atualização. Historicamente, esta política levou os clientes a saltar de um fornecedor para outro para obterem um novo desconto. Isto, por sua vez, levou os fornecedores a aperfeiçoarem as suas estratégias.

A elevada volatilidade nas ofertas de aparelhos é um fator que rapidamente invalida modelos de churn que são baseados em modelos de aparelhos atuais. Além disso, os telemóveis não são apenas dispositivos de telecomunicações, são também declarações de moda (considere o iPhone). Estes preditores sociais estão fora do âmbito dos conjuntos regulares de dados de telecomunicações.

O resultado líquido da modelação é que não se pode conceber uma política sã simplesmente eliminando razões conhecidas para a agitação. De facto, é **obrigatória**uma estratégia de modelação contínua, incluindo modelos clássicos que quantificam variáveis categóricas (como árvores de decisão).

Utilizando grandes conjuntos de dados nos seus clientes, as organizações estão a realizar a análise de big data (em particular, deteção de churn supor grandes dados) como uma abordagem eficaz ao problema. Pode descobrir mais sobre a abordagem dos big data para o problema da agitação nas recomendações sobre a secção ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodologia para modelar o cliente churn
Um processo comum de resolução de problemas para resolver o resso do cliente é representado nas figuras 1-3:  

1. Um modelo de risco permite-lhe considerar como as ações afetam a probabilidade e o risco.
2. Um modelo de intervenção permite-lhe considerar como o nível de intervenção pode afetar a probabilidade de agitação e a quantidade de valor vitalício do cliente (CLV).
3. Esta análise presta-se a uma análise qualitativa que é escalada para uma campanha de marketing proativa que visa os segmentos de clientes para oferecer a oferta ideal.  

![Diagrama mostrando como a tolerância ao risco mais os modelos de decisão produzem insights atuais](./media/azure-ml-customer-churn-scenario/churn-1.png)

Esta abordagem prospetiva é a melhor forma de tratar o churn, mas vem com complexidade: temos de desenvolver um arquétipo multimodelo e rastrear dependências entre os modelos. A interação entre modelos pode ser encapsulada como mostrado no seguinte diagrama:  

![Diagrama de interação de modelo churn](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Figura 4: Arquétipo multimodelo unificado*  

A interação entre os modelos é fundamental para oferecer mossa a uma abordagem holística à retenção de clientes. Cada modelo necessariamente degrada-se ao longo do tempo; portanto, a arquitetura é um laço implícito (semelhante ao arquétipo definido pela norma de mineração de dados CRISP-DM, [***3]).***  

O ciclo global de segmentação/decomposição de decisões de risco continua a ser uma estrutura generalizada, que se aplica a muitos problemas de negócio. A análise de churn é simplesmente um forte representante deste grupo de problemas porque exibe todos os traços de um problema de negócio complexo que não permite uma solução preditiva simplificada. Os aspetos sociais da abordagem moderna do churn não são particularmente salientados na abordagem, mas os aspetos sociais são encapsulado no arquétipo de modelação, como seriam em qualquer modelo.  

Uma adição interessante aqui é a análise de big data. As empresas de telecomunicações e retalho de hoje recolhem dados exaustivos sobre os seus clientes, e podemos facilmente prever que a necessidade de conectividade multi-modelo se tornará uma tendência comum, dadas as tendências emergentes como a Internet das Coisas e a Ubíqua dispositivos, que permitem às empresas empregar soluções inteligentes em várias camadas.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Implementação do arquétipo de modelação no Machine Learning Studio (clássico)
Tendo em conta o problema descrito, qual é a melhor forma de implementar uma abordagem integrada de modelação e pontuação? Nesta secção, vamos demonstrar como o conseguimos utilizando o Azure Machine Learning Studio (clássico).  

A abordagem multi-modelo é imprescindível ao conceber um arquétipo global para churn. Mesmo a parte de pontuação (preditiva) da abordagem deve ser multi-modelo.  

O diagrama seguinte mostra o protótipo que criamos, que emprega quatro algoritmos de pontuação no Machine Learning Studio (clássico) para prever o churn. A razão para usar uma abordagem multi-modelo não é apenas criar um agrupamento de classificação para aumentar a precisão, mas também para proteger contra o excesso de adaptação e melhorar a seleção prescritiva de recursos.  

![Screenshot retratando um complexo espaço de trabalho studio (clássico) com muitos módulos interligados](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Figura 5: Protótipo de uma abordagem de modelação de churn*  

As seguintes secções fornecem mais detalhes sobre o modelo de pontuação do protótipo que implementamos utilizando o Machine Learning Studio (clássico).  

### <a name="data-selection-and-preparation"></a>Seleção e preparação de dados
Os dados utilizados para construir os modelos e pontuar os clientes foram obtidos a partir de uma solução vertical crm, com os dados obfuscados para proteger a privacidade do cliente. Os dados contêm informações sobre 8.000 subscrições nos EUA, e combina três fontes: fornecer dados (metadados de subscrição), dados de atividade (uso do sistema) e dados de apoio ao cliente. Os dados não incluem qualquer informação relacionada com o negócio sobre os clientes; por exemplo, não inclui metadados de fidelização ou pontuações de crédito.  

Para a simplicidade, a ETL e os processos de limpeza de dados estão fora de âmbito porque assumimos que a preparação de dados já foi feita noutros locais.

A seleção de funcionalidades para modelação baseia-se na pontuação preliminar do conjunto de preditores, incluído no processo que utiliza o módulo florestal aleatório. Para a implementação no Machine Learning Studio (clássico), calculamos a média, mediana e gamas para características representativas. Por exemplo, adicionámos agregados para os dados qualitativos, tais como valores mínimos e máximos para a atividade do utilizador.

Também capturámos informação temporal nos últimos seis meses. Analisámos os dados durante um ano e estabelecemos que, mesmo que houvesse tendências estatisticamente significativas, o efeito sobre o churn diminuiu muito ao fim de seis meses.  

O ponto mais importante é que todo o processo, incluindo eTL, seleção de funcionalidades e modelação foi implementado no Machine Learning Studio (clássico), utilizando fontes de dados no Microsoft Azure.   

Os seguintes diagramas ilustram os dados utilizados.  

![Screenshot mostrando uma amostra dos dados utilizados com valores brutos](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Figura 6: Excerto da fonte de dados (obfuscated)*  

![Screenshot mostrando características estatísticas extraídas de fonte de dados](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Figura 7: Características extraídas de fonte de dados*
 

> Note que estes dados são privados e, portanto, o modelo e os dados não podem ser partilhados.
> No entanto, para um modelo semelhante utilizando dados disponíveis publicamente, consulte esta experiência de amostra na [Galeria Azure AI](https://gallery.azure.ai/): [Telco Customer Churn](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Para saber mais sobre como pode implementar um modelo de análise de churn usando cortana Intelligence Suite, também recomendamos [este vídeo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) pelo Gestor sénior de Programas Wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmos usados no protótipo
Usamos os seguintes quatro algoritmos de aprendizagem automática para construir o protótipo (sem personalização):  

1. Regressão logística (LR)
2. Árvore de decisão reforçada (BT)
3. Perceptron médio (AP)
4. Máquina vetorial de suporte (SVM)  

O diagrama seguinte ilustra uma parte da superfície de conceção da experiência, que indica a sequência em que os modelos foram criados:  

![Screenshot de uma pequena secção da tela de experiência do estúdio](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Figura 8: Criação de modelos no Machine Learning Studio (clássico)*  

### <a name="scoring-methods"></a>Métodos de pontuação
Marcámos os quatro modelos usando um conjunto de dados de treino marcado.  

Também submetemos o conjunto de dados de pontuação a um modelo comparável construído usando a edição de desktop do SAS Enterprise Miner 12. Medimos a precisão do modelo SAS e dos quatro modelos machine learning studio (clássicos).  

## <a name="results"></a>Resultados
Nesta secção, apresentamos as nossas descobertas sobre a precisão dos modelos, com base no conjunto de dados de pontuação.  

### <a name="accuracy-and-precision-of-scoring"></a>Precisão e precisão de pontuação
Geralmente, a implementação no Azure Machine Learning Studio (clássico) está por trás da SAS em precisão em cerca de 10-15% (Área Sob Curva ou AUC).  

No entanto, a métrica mais importante em churn é a taxa de desclassificação: isto é, dos melhores n churners como previsto pelo classificador, qual deles realmente **não** rebentou, e ainda recebeu tratamento especial? O diagrama seguinte compara esta taxa de classificação errada para todos os modelos:  

![Área sob o gráfico de curva comparando o desempenho de 4 algoritmos](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Figura 9: Área do protótipo passau sob curva*

### <a name="using-auc-to-compare-results"></a>Utilização da AUC para comparar resultados
A Área Sob Curva (AUC) é uma métrica que representa uma medida global de *separabilidade* entre a distribuição de pontuações para populações positivas e negativas. É semelhante ao gráfico tradicional do operador recetor Característico (ROC), mas uma diferença importante é que a métrica AUC não requer que escolha um valor-limiar. Em vez disso, resume os resultados sobre **todas as** escolhas possíveis. Em contraste, o gráfico roc tradicional mostra a taxa positiva no eixo vertical e a taxa falsa positiva no eixo horizontal, e o limiar de classificação varia.   

A Uc é usada como uma medida de valor para diferentes algoritmos (ou sistemas diferentes) porque permite que os modelos sejam comparados através dos seus valores AUC. Esta é uma abordagem popular em indústrias como a meteorologia e as biociências. Assim, a AUC representa uma ferramenta popular para avaliar o desempenho do classificador.  

### <a name="comparing-misclassification-rates"></a>Comparar taxas de desclassificação erradas
Comparamos as taxas de desclassificação no conjunto de dados em questão utilizando os dados de CRM de aproximadamente 8.000 subscrições.  

* A taxa de desclassificação da SAS foi de 10-15%.
* A taxa de classificação errada do Machine Learning Studio (clássico) foi de 15-20% para os 200-300 melhores churners.  

No sector das telecomunicações, é importante abordar apenas os clientes que têm maior risco de rebolar, oferecendo-lhes um serviço de concierge ou outro tratamento especial. A este respeito, a implementação do Machine Learning Studio (clássico) obtém resultados a par com o modelo SAS.  

Da mesma forma, a precisão é mais importante do que a precisão, porque estamos principalmente interessados em classificar corretamente potenciais churners.  

O diagrama seguinte da Wikipédia retrata a relação num gráfico animado e fácil de entender:  

![Dois alvos. Um alvo mostra marcas de sucesso vagamente agruparadas, mas perto do olho de touro com a marca "Baixa precisão: boa verdade, má precisão. Outro alvo bem agrupado, mas longe do olho-touro marcado "Baixa precisão: má verdade, boa precisão"](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Figura 10: Troca entre precisão e precisão*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Resultados de precisão e precisão para o modelo de árvore de decisão reforçado
O gráfico seguinte mostra os resultados brutos da pontuação utilizando o protótipo Machine Learning para o modelo de árvore de decisão reforçado, que por acaso é o mais preciso entre os quatro modelos:  

![Corte de mesa mostrando precisão, precisão, recordação, F-Score, AUC, Perda média de registo e perda de registo de treino para quatro algoritmos](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Figura 11: Características do modelo da árvore de decisão reforçada*

## <a name="performance-comparison"></a>Comparação de desempenho
Comparamos a velocidade com que os dados foram obtidos utilizando os modelos Machine Learning Studio (clássico) e um modelo comparável criado através da edição de desktop do SAS Enterprise Miner 12.1.  

A tabela seguinte resume o desempenho dos algoritmos:  

*Mesa 1. Desempenho geral (precisão) dos algoritmos*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Modelo Médio |O Melhor Modelo |Desempenho fraco |Modelo Médio |

Os modelos hospedados no Machine Learning Studio (clássico) superaram o SAS em 15-25% para a velocidade de execução, mas a precisão foi em grande parte a par.  

## <a name="discussion-and-recommendations"></a>Discussão e recomendações
No sector das telecomunicações, surgiram várias práticas para analisar o churn, incluindo:  

* Derivam métricas para quatro categorias fundamentais:
  * **Entidade (por exemplo, uma subscrição)**. Fornecer informações básicas sobre a subscrição e/ou cliente que é objeto de churn.
  * **Atividade**. Obtenha todas as informações de utilização possíveis relacionadas com a entidade, por exemplo, o número de logins.
  * **Apoio ao cliente**. Recolha informações dos registos de apoio ao cliente para indicar se a subscrição tinha problemas ou interações com o apoio ao cliente.
  * **Dados competitivos e empresariais.** Obtenha qualquer informação possível sobre o cliente (por exemplo, pode estar indisponível ou difícil de localizar).
* Use a importância para conduzir a seleção de recursos. Isto implica que o modelo de árvore de decisão reforçado é sempre uma abordagem promissora.  

A utilização destas quatro categorias cria a ilusão de que uma abordagem *determinística* simples, baseada em índices formados em fatores razoáveis por categoria, deve ser suficiente para identificar os clientes em risco de agitação. Infelizmente, embora esta noção pareça plausível, é um falso entendimento. A razão é que o churn é um efeito temporal e os fatores que contribuem para o churn são geralmente em estados transitórios. O que leva um cliente a considerar sair hoje pode ser diferente amanhã, e certamente será diferente daqui a seis meses. Portanto, um modelo *probabilístico* é uma necessidade.  

Esta observação importante é muitas vezes negligenciada nos negócios, o que geralmente prefere uma abordagem orientada para a inteligência empresarial à análise, principalmente porque é uma venda mais fácil e admite uma automação simples.  

No entanto, a promessa de análise de autosserviço usando o Machine Learning Studio (clássico) é que as quatro categorias de informação, classificadas por divisão ou departamento, se tornam uma valiosa fonte de aprendizagem automática sobre churn.  

Outra capacidade emocionante que chega no Azure Machine Learning Studio (clássico) é a capacidade de adicionar um módulo personalizado ao repositório de módulos pré-definidos que já estão disponíveis. Esta capacidade, essencialmente, cria uma oportunidade para selecionar bibliotecas e criar modelos para mercados verticais. É um diferenciador importante do Azure Machine Learning Studio (clássico) no mercado.  

Esperamos continuar este tema no futuro, especialmente relacionado com a análise de big data.
  

## <a name="conclusion"></a>Conclusão
Este documento descreve uma abordagem sensata para resolver o problema comum do cliente, utilizando um quadro genérico. Considerámos um protótipo para a pontuação de modelos e implementámo-lo utilizando o Azure Machine Learning Studio (clássico). Finalmente, avaliámos a precisão e desempenho da solução protótipo no que diz respeito a algoritmos comparáveis em SAS.  

 

## <a name="references"></a>Referências
[1] Análise Preditiva: Para além das Previsões, W. McKnight, Gestão da Informação, julho/agosto de 2011, p.18-20.  

[2] Artigo da Wikipédia: [Precisão e precisão](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Guia de mineração](https://www.the-modeling-agency.com/crisp-dm.pdf) de dados passo a passo   

[4] [Big Data Marketing: Envolver os seus clientes de forma mais eficaz e impulsionar valor](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Modelo de telco churn](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) na Galeria [Azure AI](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Anexo
![Instantâneo de uma apresentação sobre o protótipo churn](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Figura 12: Instantâneo de uma apresentação sobre o protótipo churn*
