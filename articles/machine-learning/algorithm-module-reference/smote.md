---
title: SMOTE
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo SMOTE em Azure Machine Learning para aumentar o número de exemplos de baixa incidência num conjunto de dados utilizando a sobresmaltização.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 501f3e8946023d28d67a33fbbfca661afbc6306d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898273"
---
# <a name="smote"></a>SMOTE

Este artigo descreve como usar o módulo SMOTE no designer de Aprendizagem automática Azure para aumentar o número de casos sub-representados num conjunto de dados que é usado para machine learning. O SMOTE é uma forma melhor de aumentar o número de casos raros do que simplesmente duplicar os casos existentes.  

Liga-se o módulo SMOTE a um conjunto de dados *desequilibrado.* Há muitas razões pelas quais um conjunto de dados pode ser desequilibrado. Por exemplo, a categoria que está a ser alvo pode ser rara na população, ou os dados podem ser difíceis de recolher. Normalmente, usa-se SMOTE quando a *classe* que pretende analisar está sub-representada. 
  
O módulo devolve um conjunto de dados que contém as amostras originais. Também devolve uma série de amostras de minorias sintéticas, dependendo da percentagem que especifica.  
  
## <a name="more-about-smote"></a>Mais sobre SMOTE

A Técnica de Sobresmagem Da Minoria Sintética (SMOTE) é uma técnica estatística para aumentar o número de casos no seu conjunto de dados de forma equilibrada. O módulo funciona gerando novos casos de casos minoritários existentes que fornece como entrada. Esta implementação de SMOTE *não* altera o número de casos maioritários.

Os novos casos não são apenas cópias de casos minoritários existentes. Em vez disso, o algoritmo retira amostras do espaço de *recurso* para cada classe alvo e seus vizinhos mais próximos. O algoritmo gera então novos exemplos que combinam características do caso alvo com características dos seus vizinhos. Esta abordagem aumenta as funcionalidades disponíveis para cada classe e torna as amostras mais gerais.
  
O SMOTE toma todo o conjunto de dados como uma entrada, mas aumenta a percentagem de apenas os casos minoritários. Por exemplo, suponha que tem um conjunto de dados desequilibrado onde apenas 1% dos casos têm o valor-alvo A (a classe minoritária), e 99 por cento dos casos têm o valor B. Para aumentar a percentagem de casos minoritários para o dobro da percentagem anterior, introduziria **200** para **percentagem de SMOTE** nas propriedades do módulo.  
  
## <a name="examples"></a>Exemplos  

Recomendamos que tente utilizar SMOTE com um pequeno conjunto de dados para ver como funciona. O exemplo a seguir utiliza o conjunto de dados de doação de sangue disponível no designer de aprendizagem automática Azure.
  
Se adicionar o conjunto de dados a um pipeline e **selecionar visualizar** a saída do conjunto de dados, pode ver que das 748 linhas ou casos no conjunto de dados, 570 casos (76 por cento) são da Classe 0, e 178 casos (24 por cento) são da Classe 1. Embora este resultado não seja terrivelmente desequilibrado, a Classe 1 representa as pessoas que doaram sangue, por isso estas linhas contêm o espaço de *recurso* que quer modelar.
 
Para aumentar o número de casos, pode definir o valor da **percentagem de SMOTE,** utilizando múltiplos de 100, da seguinte forma:

||Classe 0|Classe 1|total|  
|-|-------------|-------------|-----------|  
|Conjunto de dados original<br /><br /> (equivalente à **percentagem**  =  de SMOTE **0**)|570<br /><br /> 76%|178<br /><br /> 24%|748|  
|Percentagem de **SmoTE**  =  **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|Percentagem de **SmoTE**  =  **200**|570<br /><br /> 52%|534<br /><br /> 48%|1,104|  
|Percentagem de **SmoTE**  =  **300**|570<br /><br /> 44%|712<br /><br /> 56%|1,282|  
  
> [!WARNING]
> Aumentar o número de casos através da utilização de SMOTE não é garantido para produzir modelos mais precisos. Experimente pipelining com percentagens diferentes, diferentes conjuntos de funcionalidades e diferentes números de vizinhos mais próximos para ver como adicionar casos influencia o seu modelo.  
  
## <a name="how-to-configure-smote"></a>Como configurar o SMOTE
  
1.  Adicione o módulo SMOTE ao seu oleoduto. Pode encontrar o módulo em **módulos de Transformação de Dados,** na categoria **Manipulação.**

2. Ligue o conjunto de dados que pretende impulsionar. Se pretender especificar o espaço de funcionalidade para a construção dos novos casos, utilizando apenas colunas específicas ou excluindo algumas, utilize as [Colunas Selecionadas no módulo Dataset.](select-columns-in-dataset.md) Em seguida, pode isolar as colunas que pretende utilizar antes de utilizar o SMOTE.
  
    Caso contrário, a criação de novos casos através do SMOTE baseia-se em *todas as* colunas que fornece como entradas. Pelo menos uma coluna das colunas de recurso é numérica.
  
3.  Certifique-se de que a coluna que contém a etiqueta, ou a classe alvo, é selecionada. O SMOTE só aceita rótulos binários.
  
4.  O módulo SMOTE identifica automaticamente a classe minoritária na coluna do rótulo e, em seguida, obtém todos os exemplos para a classe minoritária. Todas as colunas não podem ter valores naN.
  
5.  Na opção **percentual SMOTE,** introduza um número inteiro que indique a percentagem de casos minoritários no conjunto de dados de produção. Por exemplo:  
  
    - Entra **0.** O módulo SMOTE devolve exatamente o mesmo conjunto de dados que forneceu como entrada. Não acrescenta novos casos minoritários. Neste conjunto de dados, a proporção de classe não mudou.  
  
    - Entra-se **a 100.** O módulo SMOTE gera novos casos minoritários. Acrescenta o mesmo número de casos minoritários que estavam no conjunto de dados original. Como o SMOTE não aumenta o número de casos maioritários, a proporção de casos de cada classe mudou.  
  
    - Entra-se **no 200.** O módulo duplica a percentagem de casos minoritários em comparação com o conjunto de dados original. Isto *não resulta* em ter o dobro dos casos minoritários que antes. Pelo contrário, o tamanho do conjunto de dados é aumentado de forma a que o número de casos maioritários permaneça o mesmo. O número de casos minoritários é aumentado até corresponder ao valor percentual desejado.  
  
    > [!NOTE]
    > Utilize apenas múltiplos de 100 para a percentagem de SMOTE.

6.  Use a opção **número de vizinhos mais próximos** para determinar o tamanho do espaço de recurso que o algoritmo SMOTE usa na construção de novos casos. Um vizinho mais próximo é uma fileira de dados (um caso) que é semelhante a um caso alvo. A distância entre dois casos é medida combinando os vetores ponderados de todas as características.  
  
    + Ao aumentar o número de vizinhos mais próximos, obtém-se funcionalidades de mais casos.
    + Ao manter o número de vizinhos mais próximos baixo, você usa características que são mais parecidas com as da amostra original.  
  
7. Introduza um valor na caixa **de sementes Aleatórias** se quiser garantir os mesmos resultados ao longo de percursos do mesmo oleoduto, com os mesmos dados. Caso contrário, o módulo gera uma semente aleatória com base nos valores do relógio do processador quando o pipeline é implantado. A geração de uma semente aleatória pode causar resultados ligeiramente diferentes ao longo dos percursos.

8. Envie o oleoduto.  
  
   A saída do módulo é um conjunto de dados que contém as linhas originais mais uma série de linhas adicionadas com casos minoritários.  

## <a name="technical-notes"></a>Notas técnicas

+ Quando publicar um modelo que utiliza o módulo **SMOTE,** remova o **SMOTE** do pipeline preditivo antes de ser publicado como um serviço web. A razão é que o SMOTE destina-se a melhorar um modelo durante o treino, e não para pontuar. Pode obter um erro se um pipeline preditivo publicado contiver o módulo SMOTE.

+ Muitas vezes pode obter melhores resultados se limpar valores em falta ou aplicar outras transformações para corrigir dados antes de aplicar SMOTE. 

+ Alguns investigadores investigaram se o SMOTE é eficaz em dados de alta dimensão ou escassos, tais como dados utilizados na classificação de texto ou conjuntos de dados genómicas. Este artigo tem um bom resumo dos efeitos e da validade teórica da aplicação de SMOTE nestes [casos: Blagus e Lusa: SMOTE para dados de alto nível de desequilíbrio de classe](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106).

+ Se o SMOTE não for eficaz no seu conjunto de dados, outras abordagens que poderá considerar incluem:
  + Métodos para sobrecarregar os casos minoritários ou subestimar a maioria dos casos.
  + Conjunto de técnicas que ajudam o aluno diretamente usando clustering, embalamento ou impulso adaptativo.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 

