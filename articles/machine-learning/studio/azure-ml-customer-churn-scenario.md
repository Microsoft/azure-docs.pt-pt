---
title: Analisar a rotatividade de clientes
titleSuffix: ML Studio (classic) - Azure
description: Estudo de caso do desenvolvimento de um modelo integrado para analisar e pontuar a rotatividade de clientes usando Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 8fd88c3bfad962f264efa030d0a3aea44e95dc8c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839769"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Analisar a rotatividade de clientes usando Azure Machine Learning Studio (clássico)
## <a name="overview"></a>Descrição geral
Este artigo apresenta uma implementação de referência de um projeto de análise de rotatividade de clientes criado usando Azure Machine Learning Studio (clássico). Neste artigo, discutiremos modelos genéricos associados para resolver de forma holística o problema de rotatividade de clientes industriais. Também medimos a exatidão dos modelos criados usando Machine Learning e avaliamos as direções para um desenvolvimento adicional.  

### <a name="acknowledgements"></a>Confirmações
Esse experimento foi desenvolvido e testado pela Serge Bergeron, cientista de dados principal da Microsoft e Roger Barga, anteriormente gerente de produto para Microsoft Azure Machine Learning Studio (clássico). A equipe de documentação do Azure reconhece sua experiência em agradecimento e agradece a eles por compartilhar esse white paper.

> [!NOTE]
> Os dados usados para esse experimento não estão disponíveis publicamente. Para obter um exemplo de como criar um modelo de aprendizado de máquina para análise de rotatividade, consulte: [modelo de variação de varejo](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) no [Galeria de ia do Azure](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>O problema da rotatividade de clientes
As empresas no mercado de consumidores e em todos os setores da empresa precisam lidar com a rotatividade. Às vezes, a rotatividade é excessiva e influencia as decisões da política. A solução tradicional é prever a rotatividade de grandes prognósticos e atender às suas necessidades por meio de um serviço do concierge, campanhas de marketing ou aplicando dispensações especiais. Essas abordagens podem variar de setor para setor. Eles podem até variar de um determinado cluster de consumidor para outro dentro de um setor (por exemplo, telecomunicações).

O fator comum é que as empresas precisam minimizar esses esforços especiais de retenção do cliente. Portanto, uma metodologia natural seria pontuar cada cliente com a probabilidade de variação e abordar os N maiores. Os principais clientes podem ser os mais lucrativos. Por exemplo, em cenários mais sofisticados, uma função de lucro é empregada durante a seleção de candidatos para isenção especiais. No entanto, essas considerações são apenas uma parte da estratégia completa para lidar com a rotatividade. As empresas também precisam levar em conta o risco (e a tolerância a riscos associados), o nível e o custo da intervenção e a segmentação de clientes plausível.  

## <a name="industry-outlook-and-approaches"></a>Outlook e abordagens do setor
A manipulação sofisticada de rotatividade é um sinal de uma indústria madura. O exemplo clássico é o setor de telecomunicações, em que os assinantes são conhecidos a mudarem frequentemente de um provedor para outro. Essa rotatividade voluntária é uma preocupação principal. Além disso, os provedores acumularam conhecimento significativo sobre os *drivers de rotatividade*, que são os fatores que orientam os clientes a mudarem.

Por exemplo, a escolha de dispositivo ou aparelho é um driver bem conhecido de rotatividade no negócio de celular. Como resultado, uma política popular é subsidiar o preço de um monofone para novos assinantes e cobrar um preço total aos clientes existentes para uma atualização. Historicamente, essa política levou a clientes que saltam de um provedor para outro para obter um novo desconto. Isso, por sua vez, tem provedores solicitados para refinar suas estratégias.

Alta volatilidade nas ofertas de telefone é um fator que invalida rapidamente os modelos de rotatividade que se baseiam em modelos de monofone atuais. Além disso, os celulares não são apenas dispositivos de telecomunicação, eles também são instruções de moda (considere o iPhone). Essas previsões sociais estão fora do escopo de conjuntos de dados de telecomunicações regulares.

O resultado líquido da modelagem é que você não pode planejar uma diretiva de som simplesmente eliminando motivos conhecidos de variação. Na verdade, uma estratégia de modelagem contínua, incluindo modelos clássicos que quantificam variáveis categóricas (como árvores de decisão), é **obrigatória**.

Usando conjuntos de Big Data em seus clientes, as organizações estão executando Big Data análise (em particular, detecção de rotatividade baseada em Big Data) como uma abordagem efetiva para o problema. Você pode encontrar mais informações sobre a abordagem de Big Data para o problema de rotatividade na seção recomendações sobre ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodologia para modelar a rotatividade de clientes
Um processo comum de solução de problemas para resolver a rotatividade de clientes é representado nas figuras 1-3:  

1. Um modelo de risco permite que você considere como as ações afetam a probabilidade e o risco.
2. Um modelo de intervenção permite que você considere como o nível de intervenção pode afetar a probabilidade de variação e o valor de tempo de vida do cliente (CLV).
3. Essa análise se presta a uma análise qualitativa que é escalonada para uma campanha de marketing proativa que visa segmentos de clientes para fornecer a oferta ideal.  

![Diagrama mostrando como a tolerância a riscos mais modelos de decisão produz informações acionáveis](./media/azure-ml-customer-churn-scenario/churn-1.png)

Essa abordagem de aparência direta é a melhor maneira de tratar a rotatividade, mas ela vem com a complexidade: temos que desenvolver um arquétipo multimodelo e as dependências de rastreamento entre os modelos. A interação entre os modelos pode ser encapsulada conforme mostrado no diagrama a seguir:  

![Diagrama de interação do modelo de rotatividade](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Figura 4: arquétipo multimodelo unificado*  

A interação entre os modelos é a chave se for fornecer uma abordagem holística à retenção do cliente. Cada modelo é necessariamente degradado ao longo do tempo; Portanto, a arquitetura é um loop implícito (semelhante ao conjunto de arquétipos pelo padrão de Data Mining-DM nítido, [***3***]).  

O ciclo geral da segmentação/decomposição de marketing de decisão de risco é uma estrutura generalizada, que é aplicável a muitos problemas de negócios. A análise de rotatividade é simplesmente um grande representante desse grupo de problemas, pois ele exibe todas as características de um problema comercial complexo que não permite uma solução de previsão simplificada. Os aspectos sociais da abordagem moderna para a rotatividade não são especialmente destacados na abordagem, mas os aspectos sociais são encapsulados no arquétipo de modelagem, pois eles seriam em qualquer modelo.  

Uma adição interessante aqui é Big Data análise. As empresas de telecomunicações e varejo de hoje coletam dados exaustivos sobre seus clientes, e podemos prever facilmente que a necessidade de conectividade multimodelo se tornará uma tendência comum, dadas as tendências emergentes, como as Internet das Coisas e onipresentes dispositivos, que permitem que a empresa empregue soluções inteligentes em várias camadas.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Implementando o arquétipo de modelagem no Machine Learning Studio (clássico)
Devido ao problema descrito, qual é a melhor maneira de implementar uma abordagem integrada de modelagem e Pontuação? Nesta seção, demonstraremos como isso foi feito usando a versão clássica do Azure Machine Learning Studio.  

A abordagem de vários modelos é a que se deve ao criar um arquétipo global para variação. Até mesmo a parte de Pontuação (preditiva) da abordagem deve ser multimodelo.  

O diagrama a seguir mostra o protótipo que criamos, que emprega quatro algoritmos de pontuação no Machine Learning Studio (clássico) para prever a rotatividade. O motivo para usar uma abordagem multimodelo não é apenas criar um classificador Ensemble para aumentar a precisão, mas também para proteger contra sobreajuste e para melhorar a seleção de recursos prescritivas.  

![Captura de tela descrevendo um espaço de trabalho complexo do estúdio (clássico) com muitos módulos interconectados](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Figura 5: protótipo de uma abordagem de modelagem de rotatividade*  

As seções a seguir fornecem mais detalhes sobre o modelo de Pontuação de protótipo que implementamos usando Machine Learning Studio (clássico).  

### <a name="data-selection-and-preparation"></a>Seleção e preparação de dados
Os dados usados para criar os modelos e pontuar os clientes foram obtidos de uma solução vertical do CRM, com os dados ofuscados para proteger a privacidade do cliente. Os dados contêm informações sobre 8.000 assinaturas nos EUA e combinam três fontes: provisionamento de dados (metadados de assinatura), dados de atividade (uso do sistema) e dados de atendimento ao cliente. Os dados não incluem nenhuma informação relacionada aos negócios sobre os clientes; por exemplo, ele não inclui metadados de fidelidade ou pontuações de crédito.  

Para simplificar, os processos de limpeza de ETL e de dados estão fora do escopo porque presumimos que a preparação de dados já foi feita em outro lugar.

A seleção de recursos para modelagem baseia-se na pontuação de significância preliminar do conjunto de previsões, incluído no processo que usa o módulo de floresta aleatória. Para a implementação no Machine Learning Studio (clássico), calculamos a média, mediana e os intervalos dos recursos representativos. Por exemplo, adicionamos agregações para os dados qualitativos, como os valores mínimo e máximo para a atividade do usuário.

Também capturamos informações temporais para os últimos seis meses. Analisamos dados por um ano e estabelecemos que, mesmo que haja tendências estatisticamente significativas, o efeito na rotatividade é muito menor após seis meses.  

O ponto mais importante é que todo o processo, incluindo ETL, seleção de recursos e modelagem, foi implementado em Machine Learning Studio (clássico), usando fontes de dados no Microsoft Azure.   

Os diagramas a seguir ilustram os dados que foram usados.  

![Captura de tela mostrando um exemplo dos dados usados com valores brutos](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Figura 6: trecho da fonte de dados (ofuscado)*  

![Captura de tela mostrando recursos estatísticos extraídos da fonte de dados](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Figura 7: recursos extraídos da fonte de dados*
 

> Observe que esses dados são privados e, portanto, o modelo e os dados não podem ser compartilhados.
> No entanto, para um modelo semelhante usando dados publicamente disponíveis, consulte este experimento de exemplo na [Galeria de ia do Azure](https://gallery.azure.ai/): [rotatividade de clientes de telecomunicações](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Para saber mais sobre como você pode implementar um modelo de análise de rotatividade usando Cortana Intelligence Suite, também recomendamos [este vídeo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) pelo gerente de programa sênior Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmos usados no protótipo
Utilizamos os quatro algoritmos de aprendizado de máquina a seguir para criar o protótipo (sem personalização):  

1. Regressão logística (LR)
2. Árvore de decisão aumentada (BT)
3. Média de perceptron (AP)
4. Computador de vetor de suporte (SVM)  

O diagrama a seguir ilustra uma parte da superfície de design do experimento, que indica a sequência na qual os modelos foram criados:  

![Captura de tela de uma pequena seção da Canvas do experimento do estúdio](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Figura 8: criando modelos no Machine Learning Studio (clássico)*  

### <a name="scoring-methods"></a>Métodos de Pontuação
Nós classificamos os quatro modelos usando um conjunto de um DataSet de treinamento rotulado.  

Também enviamos o conjunto de quadros de Pontuação para um modelo comparável criado usando a edição de desktop do SAS Enterprise Miner 12. Medimos a exatidão do modelo SAS e todos os quatro modelos Machine Learning Studio (clássico).  

## <a name="results"></a>Resultados
Nesta seção, apresentamos nossas conclusões sobre a precisão dos modelos, com base no conjunto de informações de pontuação.  

### <a name="accuracy-and-precision-of-scoring"></a>Precisão e precisão da Pontuação
Em geral, a implementação na versão clássica do Azure Machine Learning Studio está atrás da SAS em precisão em cerca de 10-15% (área sob a curva ou AUC).  

No entanto, a métrica mais importante na rotatividade é a taxa de classificação insuficiente: ou seja, das N maiores classificações de variação como previsto pelo classificador, quais delas na verdade **não** estão se alterando e, ainda assim, receberam tratamento especial? O diagrama a seguir compara essa taxa de classificação indesse erro para todos os modelos:  

![Área sob o grafo de curva comparando o desempenho de 4 algoritmos](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Figura 9: área do protótipo Passau sob a curva*

### <a name="using-auc-to-compare-results"></a>Usando AUC para comparar os resultados
A área sob a curva (AUC) é uma métrica que representa uma medida global de *separabilidade* entre as distribuições de pontuações para populações positivas e negativas. É semelhante ao gráfico de características do operador do receptor tradicional (ROC), mas uma diferença importante é que a métrica AUC não exige que você escolha um valor de limite. Em vez disso, ele resume os resultados em **todas as** opções possíveis. Por outro lado, o grafo ROC tradicional mostra a taxa positiva no eixo vertical e a taxa de falsos positivos no eixo horizontal e o limite de classificação varia.   

AUC é usado como uma medida de valor para algoritmos diferentes (ou sistemas diferentes) porque permite que modelos sejam comparados por meio de seus valores de AUC. Essa é uma abordagem popular em setores como meteorologia e Biociências. Assim, AUC representa uma ferramenta popular para avaliar o desempenho do classificador.  

### <a name="comparing-misclassification-rates"></a>Comparando taxas de classificação inalguma
Comparamos as taxas de classificação inconsistentes no conjunto de dados em questão usando o CRM data de aproximadamente 8.000 assinaturas.  

* A taxa de classificação innormal de SAS foi de 10-15%.
* A taxa de classificação innormal de Machine Learning Studio (clássica) foi de 15-20% para os principais índices de 200-300.  

No setor de telecomunicações, é importante abordar apenas os clientes que têm o maior risco de rotatividade, oferecendo a eles um serviço de concierge ou outro tratamento especial. Nesse sentido, a implementação Machine Learning Studio (clássica) alcança os resultados em relação ao modelo SAS.  

Pelo mesmo token, a precisão é mais importante do que a precisão porque estamos mais interessados em classificar corretamente os possíveis rotatividadees.  

O diagrama a seguir da Wikipédia representa a relação em um gráfico de interrupções, fácil de entender:  

![Dois destinos. Um destino mostra as marcas de sucesso agrupadas de forma flexível, mas próximo do Bull-olho marcado como "precisão baixa: boa verdadeira, precisão ruim. Outro destino rigidamente agrupado, mas longe do alto-diferenciado de olhos marcados como "precisão baixa: má verdadeiraidade, boa precisão"](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Figura 10: compensação entre precisão e precisão*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Resultados de precisão e precisão para o modelo de árvore de decisão impulsionado
O gráfico a seguir exibe os resultados brutos de Pontuação usando o protótipo de Machine Learning para o modelo de árvore de decisão aumentada, que é o mais preciso entre os quatro modelos:  

![Trecho de tabela mostrando precisão, precisão, recall, F-score, AUC, perda de log média e perda de log de treinamento para quatro algoritmos](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Figura 11: características do modelo de árvore de decisão impulsionadas*

## <a name="performance-comparison"></a>Comparação de desempenho
Comparamos a velocidade na qual os dados foram pontuados usando os modelos Machine Learning Studio (clássico) e um modelo comparável criado usando a edição de desktop do SAS Enterprise Miner 12,1.  

A tabela a seguir resume o desempenho dos algoritmos:  

*Tabela 1. Desempenho geral (precisão) dos algoritmos*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Modelo médio |O melhor modelo |Com baixo desempenho |Modelo médio |

Os modelos hospedados no Machine Learning Studio (clássico) de SAS de 15-25% em velocidade de execução, mas a precisão era em grande parte do par.  

## <a name="discussion-and-recommendations"></a>Discussão e recomendações
No setor de telecomunicações, várias práticas surgiram para analisar a rotatividade, incluindo:  

* Derive métricas para quatro categorias fundamentais:
  * **Entidade (por exemplo, uma assinatura)** . Provisione informações básicas sobre a assinatura e/ou o cliente que é o assunto da rotatividade.
  * **Atividade**. Obtenha todas as informações de uso possíveis relacionadas à entidade, por exemplo, o número de logons.
  * Atendimento **ao cliente**. Colete informações de logs de atendimento ao cliente para indicar se a assinatura teve problemas ou interações com o atendimento ao cliente.
  * **Dados de negócios e competitivos**. Obtenha qualquer informação possível sobre o cliente (por exemplo, pode estar indisponível ou difícil de rastrear).
* Use a importância para impulsionar a seleção de recursos. Isso significa que o modelo de árvore de decisão aumentada é sempre uma abordagem promissora.  

O uso dessas quatro categorias cria a ilusão de que uma abordagem *determinística* simples, com base em índices formados em fatores razoáveis por categoria, deve ser suficiente para identificar os clientes em risco de variação. Infelizmente, embora essa noção pareça plausível, é uma compreensão falsa. O motivo é que a rotatividade é um efeito temporal e os fatores que contribuem para a rotatividade geralmente estão em estados transitórios. O que leva um cliente a levar em consideração hoje pode ser diferente do amanhã e certamente será diferente seis meses a partir de agora. Portanto, um modelo *probabilística* é uma necessidade.  

Essa observação importante muitas vezes é ignorada no negócio, o que geralmente prefere uma abordagem orientada por business intelligence para análise, principalmente porque é uma fácil venda e admite automação direta.  

No entanto, a promessa de análise de autoatendimento usando Machine Learning Studio (clássico) é que as quatro categorias de informações, classificadas por divisão ou departamento, se tornam uma fonte valiosa para o aprendizado de máquina sobre a rotatividade.  

Outro recurso empolgante na versão clássica do Azure Machine Learning Studio é a capacidade de adicionar um módulo personalizado ao repositório de módulos predefinidos que já estão disponíveis. Esse recurso, essencialmente, cria uma oportunidade para selecionar bibliotecas e criar modelos para mercados verticais. É um diferencial importante da versão clássica do Azure Machine Learning Studio no mercado.  

Esperamos continuar este tópico no futuro, especialmente relacionado à análise de Big Data.
  

## <a name="conclusion"></a>Conclusão
Este documento descreve uma abordagem sensata para lidar com o problema comum de rotatividade de clientes usando uma estrutura genérica. Consideramos um protótipo para modelos de Pontuação e o implementamos usando a versão clássica do Azure Machine Learning Studio. Por fim, avaliamos a exatidão e o desempenho da solução de protótipo em relação aos algoritmos comparáveis na SAS.  

 

## <a name="references"></a>Referências
[1] análise preditiva: além das previsões, W. McKnight, gerenciamento de informações, julho/agosto de 2011, p. 18-20.  

[2] artigo da Wikipédia: [precisão e precisão](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [Crisp-DM 1,0: guia de mineração de dados passo a passo](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [marketing de Big Data: envolver seus clientes com mais eficiência e impulsionar valor](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [modelo de rotatividade de telecomunicações](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) no [Galeria de ia do Azure](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Anexo
![Instantâneo de uma apresentação no protótipo de rotatividade](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Figura 12: instantâneo de uma apresentação no protótipo de rotatividade*
