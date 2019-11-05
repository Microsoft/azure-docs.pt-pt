---
title: SMOTE
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo SMOTE no serviço Azure Machine Learning para aumentar o número de exemplos de incidência baixa em um conjunto de informações usando a superamostragem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 072268af52ebf7d3bede564d8c949eec9fc9f625
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516191"
---
# <a name="smote"></a>SMOTE

Este artigo descreve como usar o módulo **SMOTE** no designer de Azure Machine Learning (versão prévia) para aumentar o número de casos subrepresentados em um conjunto de informações usado para aprendizado de máquina. O SMOTE é uma maneira melhor de aumentar o número de casos raros do que simplesmente duplicar os casos existentes.  

 Você conecta o módulo SMOTE a um conjunto de um DataSet que está *desequilibrado*. Há muitas razões pelas quais um conjunto de dados pode ser desequilibrado: a categoria que você está direcionando pode ser rara na população ou os dados podem ser difíceis de coletar. Normalmente, você usa SMOTE quando a *classe* que você deseja analisar está em-representada. 
  
 O módulo retorna um conjunto de um DataSet que contém os exemplos originais, além de um número adicional de amostras minoritárias sintéticas, dependendo da porcentagem especificada.  
  
### <a name="more-about-smote"></a>Mais sobre SMOTE

**SMOTE** significa *técnica de sobreamostragem minoritária sintética*. Essa é uma técnica estatística para aumentar o número de casos em seu conjunto de um modo equilibrado.  O módulo funciona gerando novas instâncias de casos minoritários existentes que você fornece como entrada. Essa implementação de SMOTE não **altera o** número de casos de maioria.

As novas instâncias não são apenas cópias de casos minoritários existentes; em vez disso, o algoritmo usa exemplos do *espaço de recurso* para cada classe de destino e seus vizinhos mais próximos e gera novos exemplos que combinam recursos do caso de destino com recursos de seus vizinhos. Essa abordagem aumenta os recursos disponíveis para cada classe e torna os exemplos mais gerais.
  
O SMOTE usa todo o conjunto de dados como uma entrada, mas aumenta a porcentagem apenas dos casos minoritários. Por exemplo, suponha que você tenha um conjunto de valores desbalanceado em que apenas 1% dos casos têm o valor de destino A (a classe minoritária) e 99% dos casos têm o valor B. Para aumentar a porcentagem de casos minoritárias para o dobro da porcentagem anterior, você digitaria 200 para o **percentual de SMOTE** nas propriedades do módulo.  
  
## <a name="examples"></a>Exemplos  

É recomendável que você tente usar o **SMOTE** com um pequeno conjunto de pequenos para ver como ele funciona. O exemplo a seguir usa o conjunto de itens de doação de sangue disponível no designer de Azure Machine Learning.
  
Se você adicionar o conjunto de registros a um pipeline e clicar em **Visualizar** na saída do conjunto de resultados, poderá ver que, das 748 linhas ou casos no conjunto de registros, há 570 casos (76%) de classe 0 e 178 casos (24%) da classe 1. Embora isso não seja extremamente desbalanceado, a classe 1 representa as pessoas que donateu sangue e, portanto, essas linhas contêm o *espaço de recurso* que você deseja modelar.
 
Para aumentar o número de casos, você pode definir o valor de **porcentagem de SMOTE**, usando múltiplos de 100, da seguinte maneira:

||Classe 0|Classe 1|completa|  
|-|-------------|-------------|-----------|  
|Conjunto de um original<br /><br /> (equivalente a **SMOTE percentual** = **0**)|570<br /><br /> 76%|178<br /><br /> 24|748|  
|**Percentual de SMOTE** = **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**Percentual de SMOTE** = **200**|570<br /><br /> 52%|534<br /><br /> 48%|1104|  
|**Percentual de SMOTE** = **300**|570<br /><br /> 44%|712<br /><br /> 56%|1282|  
  
> [!WARNING]
>  O aumento do número de casos usando o SMOTE não é garantido para produzir modelos mais precisos. Você deve experimentar o pipeline com percentuais diferentes, conjuntos de recursos diferentes e números diferentes de vizinhos mais próximos para ver como a adição de casos influencia seu modelo.  
  
## <a name="how-to-configure-smote"></a>Como configurar o SMOTE
  
1.  Adicione o módulo SMOTE ao seu pipeline. Você pode encontrar o módulo em módulos de transformação de dados, na categoria manipulação.

2. Conecte o conjunto de um que você deseja aumentar. Se você quiser especificar o espaço de recurso para criar os novos casos, seja usando apenas colunas específicas ou excluindo alguns, use o módulo [selecionar colunas no conjunto](select-columns-in-dataset.md) de informações para isolar as colunas que você deseja usar antes de usar **SMOTE**.
  
    Caso contrário, a criação de novos casos usando o **SMOTE** é baseada em **todas** as colunas que você fornece como entradas. Pelo menos uma coluna das colunas de recurso é numérica.
  
3.  Verifique se a coluna que contém o rótulo ou a classe de destino está selecionada.  **SMOTE** só aceita rótulos binários.
  
4.  O módulo **SMOTE** identifica automaticamente a classe minoritária na coluna Label e obtém todos os exemplos para a classe minoritária. Todas as colunas não podem ter NaN.
  
5.  Na opção **percentual de SMOTE** , digite um número inteiro que indica a porcentagem de destino de casos minoritários no conjunto de saída. Por exemplo:  
  
    - Você digita **0** (%). O módulo SMOTE retorna exatamente o mesmo conjunto de dados que você forneceu como entrada, não adicionando novos casos minoritários. Nesse conjunto de DataSet, a proporção de classe não foi alterada.  
  
    -   Você digita **100** (%). O módulo SMOTE gera novos casos minoritários, adicionando o mesmo número de ocorrências minoritárias que estavam no conjunto de conjuntos original. Como SMOTE não aumenta o número de casos de maioria, a proporção de casos de cada classe agora foi alterada.  
  
    -   Você digita **200** (%). O módulo dobra a porcentagem de casos minoritários em comparação com o conjunto de um original. Isso **não** resulta no dobro do número de casos minoritários que antes.  Em vez disso, o tamanho do conjunto de resultados é aumentado de forma que o número de casos de maioria permaneça o mesmo e o número de casos minoritárias é aumentado até que corresponda ao valor de percentual desejado.  
  
    > [!NOTE]
    > Use somente múltiplos de 100 para o percentual de SMOTE.

6.  Use a opção **número de vizinhos mais próximos** para determinar o tamanho do espaço de recurso que o algoritmo SMOTE usa ao criar novos casos. Um *vizinho mais próximo* é uma linha de dados (um caso) que é semelhante a algum caso de destino. A distância entre dois casos é medida pela combinação dos vetores ponderados de todos os recursos.  
  
     + Ao aumentar o número de vizinhos mais próximos, você obtém recursos de mais casos.
     + Mantendo o número de vizinhos mais próximos baixos, você usa recursos que são mais semelhantes aos do exemplo original.  
  
7. Digite um valor na caixa de texto **semente aleatória** se desejar garantir os mesmos resultados em vez de execuções do mesmo pipeline, com os mesmos dados. Caso contrário, o módulo gera uma semente aleatória com base nos valores do relógio do processador quando o pipeline é implantado, o que pode causar resultados ligeiramente diferentes em relação a execuções.

8. Executar o pipeline.  
  
     A saída do módulo é um conjunto de registros que contém as linhas originais mais um número de linhas adicionadas com casos minoritários.  

## <a name="technical-notes"></a>Notas técnicas

+ Ao publicar um modelo que usa o módulo **SMOTE** , remova **SMOTE** do pipeline de previsão antes que ele seja publicado como um serviço Web.  O motivo é que o SMOTE destina-se a melhorar um modelo durante o treinamento e não se destina à pontuação.  Você poderá receber um erro se um pipeline de previsão publicado contiver o módulo SMOTE.

+ Muitas vezes, você pode obter resultados melhores se aplicar a limpeza de valor ausente ou outras transformações para corrigir dados antes de aplicar SMOTE. 

+ Alguns pesquisadores investigaram se o SMOTE é eficaz em dados altos ou esparsos, como aqueles usados na classificação de texto ou nos conjuntos de dados de genoma. Este documento tem um bom resumo dos efeitos e da validade teórica da aplicação de SMOTE nesses casos: [Blagus e Lusa: SMOTE para dados de classe altamente dimensional com desequilíbrio](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106)

    Se o SMOTE não for eficaz em seu conjunto de informações, outras abordagens que você pode considerar incluem vários métodos para sobreamostrar os casos minoritários ou subamostrar os casos principais, bem como técnicas de Ensemble que ajudam o aprendiz diretamente, usando clustering, bagging ou aumento adaptável.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 

