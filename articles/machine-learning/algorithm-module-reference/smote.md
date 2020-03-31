---
title: SMOTE
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo SMOTE em Aprendizagem automática Azure para aumentar o número de exemplos de baixa incidência num conjunto de dados utilizando uma amostragem excessiva.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: ed6d9e86143c3a5d6c97c4bd92a07c258bbd1bbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477464"
---
# <a name="smote"></a>SMOTE

Este artigo descreve como usar o módulo SMOTE em Azure Machine Learning designer (pré-visualização) para aumentar o número de casos sub-representados num conjunto de dados que é usado para machine learning. O SMOTE é uma forma melhor de aumentar o número de casos raros do que simplesmente duplicar os casos existentes.  

Liga o módulo SMOTE a um conjunto de dados *desequilibrado.* Há muitas razões pelas quais um conjunto de dados pode ser desequilibrado. Por exemplo, a categoria que está a apontar pode ser rara na população, ou os dados podem ser difíceis de recolher. Normalmente, usa-se SMOTE quando a *aula* que pretende analisar está sub-representada. 
  
O módulo devolve um conjunto de dados que contém as amostras originais. Também devolve uma série de amostras de minoriasintética, dependendo da percentagem que especifica.  
  
## <a name="more-about-smote"></a>Mais sobre o SMOTE

A Técnica De Sobreamostragem de Minoria Sintética (SMOTE) é uma técnica estatística para aumentar o número de casos no seu conjunto de dados de forma equilibrada. O módulo funciona gerando novos casos a partir de casos minoritários existentes que você fornece como entrada. Esta implementação do SMOTE *não* altera o número de casos maioritários.

Os novos casos não são apenas cópias dos casos minoritários existentes. Em vez disso, o algoritmo recolheu amostras do espaço de *recurso* para cada classe alvo e seus vizinhos mais próximos. O algoritmo gera então novos exemplos que combinam características do caso alvo com características dos seus vizinhos. Esta abordagem aumenta as funcionalidades disponíveis para cada classe e torna as amostras mais gerais.
  
O SMOTE toma todo o conjunto de dados como uma entrada, mas aumenta a percentagem apenas dos casos minoritários. Por exemplo, suponha que tenha um conjunto de dados desequilibrado onde apenas 1% dos casos têm o valor-alvo A (a classe minoritária), e 99 por cento dos casos têm o valor B. Para aumentar a percentagem de casos minoritários para o dobro da percentagem anterior, introduziria **200** para a percentagem de **SMOTE** nas propriedades do módulo.  
  
## <a name="examples"></a>Exemplos  

Recomendamos que tente utilizar o SMOTE com um pequeno conjunto de dados para ver como funciona. O exemplo que se segue utiliza o conjunto de dados da dádiva de sangue disponível no designer de Aprendizagem automática Azure.
  
Se adicionar o conjunto de dados a um pipeline e selecionar **Visualizar** a saída do conjunto de dados, pode ver que das 748 linhas ou casos no conjunto de dados, 570 casos (76 por cento) são da Classe 0, e 178 casos (24 por cento) são da Classe 1. Embora este resultado não seja terrivelmente desequilibrado, a Classe 1 representa as pessoas que doaram sangue, por isso estas filas contêm o espaço de *características* que você quer modelar.
 
Para aumentar o número de casos, pode definir o valor da **percentagem sMOTE,** utilizando múltiplos de 100, da seguinte forma:

||Classe 0|Classe 1|total|  
|-|-------------|-------------|-----------|  
|Conjunto de dados original<br /><br /> (equivalente à =  **percentagem smote****0**)|570<br /><br /> 76%|178<br /><br /> 24%|748|  
|**Percentagem** = de SMOTE**100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**Percentagem** = de SMOTE**200**|570<br /><br /> 52%|534<br /><br /> 48%|1,104|  
|**Percentagem** = de SMOTE**300**|570<br /><br /> 44%|712<br /><br /> 56%|1,282|  
  
> [!WARNING]
> O aumento do número de casos através da utilização do SMOTE não é garantido para produzir modelos mais precisos. Experimente pipelinecom diferentes percentagens, diferentes conjuntos de funcionalidades e diferentes números de vizinhos mais próximos para ver como adicionar casos influencia o seu modelo.  
  
## <a name="how-to-configure-smote"></a>Como configurar o SMOTE
  
1.  Adicione o módulo SMOTE ao seu oleoduto. Pode encontrar o módulo em módulos de Transformação de **Dados,** na categoria **Manipulação.**

2. Ligue o conjunto de dados que pretende impulsionar. Se pretender especificar o espaço de recurso para a construção dos novos casos, quer utilizando colunas específicas ou excluindo algumas, utilize as Colunas Select no módulo [Dataset.](select-columns-in-dataset.md) Em seguida, pode isolar as colunas que pretende utilizar antes de utilizar o SMOTE.
  
    Caso contrário, a criação de novos casos através do SMOTE baseia-se em *todas as* colunas que fornece como inputs. Pelo menos uma coluna das colunas de características é numérica.
  
3.  Certifique-se de que a coluna que contém a etiqueta, ou classe alvo, é selecionada. O SMOTE aceita apenas rótulos binários.
  
4.  O módulo SMOTE identifica automaticamente a classe minoritária na coluna de etiquetas e, em seguida, recebe todos os exemplos para a classe minoritária. Todas as colunas não podem ter valores nan.
  
5.  Na opção **percentual SMOTE,** insira um número inteiro que indique a percentagem-alvo de casos minoritários no conjunto de dados de saída. Por exemplo:  
  
    - Entra si mesmo **0**. O módulo SMOTE retorna exatamente o mesmo conjunto de dados que forneceu como entrada. Não acrescenta novos casos minoritários. Neste conjunto de dados, a proporção de classe não mudou.  
  
    - Entra-se **no 100.** O módulo SMOTE gera novos casos minoritários. Acrescenta o mesmo número de casos minoritários que estavam no conjunto de dados original. Como o SMOTE não aumenta o número de casos maioritários, a proporção de casos de cada classe mudou.  
  
    - Entra no **200.** O módulo duplica a percentagem de casos minoritários em comparação com o conjunto de dados original. Isto *não* resulta em ter o dobro dos casos minoritários do que antes. Pelo contrário, a dimensão do conjunto de dados é aumentada de modo a que o número de casos maioritários permaneça o mesmo. O número de casos minoritários é aumentado até corresponder ao valor percentual desejado.  
  
    > [!NOTE]
    > Utilize apenas múltiplos de 100 para a percentagem sMOTE.

6.  Use o **número de vizinhos mais próximos** para determinar o tamanho do espaço de recurso que o algoritmo SMOTE usa na construção de novos casos. Um vizinho mais próximo é uma fileira de dados (um caso) que é semelhante a um caso alvo. A distância entre dois casos é medida combinando os vetores ponderados de todas as características.  
  
    + Ao aumentar o número de vizinhos mais próximos, obtém-se características de mais casos.
    + Ao manter o número de vizinhos mais próximos baixos, você usa características que são mais parecidas com as da amostra original.  
  
7. Introduza um valor na caixa de **sementes Aleatória** se pretender garantir os mesmos resultados ao longo das corridas do mesmo pipeline, com os mesmos dados. Caso contrário, o módulo gera uma semente aleatória com base nos valores do relógio do processador quando o gasoduto é implantado. A geração de uma semente aleatória pode causar resultados ligeiramente diferentes ao longo de corridas.

8. Submeta o oleoduto.  
  
   A saída do módulo é um conjunto de dados que contém as linhas originais mais uma série de linhas adicionadas com casos minoritários.  

## <a name="technical-notes"></a>Notas técnicas

+ Quando estiver a publicar um modelo que utiliza o módulo **SMOTE,** remova o **SMOTE** do pipeline preditivo antes de ser publicado como um serviço web. A razão é que o SMOTE se destina a melhorar um modelo durante o treino, não para marcar. Pode ter um erro se um gasoduto preditivo publicado contiver o módulo SMOTE.

+ Muitas vezes pode obter melhores resultados se limpar valores em falta ou aplicar outras transformações para corrigir dados antes de aplicar o SMOTE. 

+ Alguns investigadores investigaram se o SMOTE é eficaz em dados dimensionais ou escassos, tais como dados utilizados na classificação de texto ou conjuntos de dados genómicas. Este documento tem um bom resumo dos efeitos e da validade teórica da aplicação do SMOTE nestes casos: [Blagus e Lusa: SMOTE para dados de classe alta desequilibrados.](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106)

+ Se o SMOTE não for eficaz no seu conjunto de dados, outras abordagens que possa considerar incluem:
  + Métodos para sobresfazer a sobre-amostragem dos casos minoritários ou subestimar os casos maioritários.
  + Ensemble técnicas que ajudam o aprendiz diretamente usando agrupamento, ensaque ou reforço adaptativo.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 

