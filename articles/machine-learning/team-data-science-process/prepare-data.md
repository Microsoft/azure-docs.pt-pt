---
title: Dados de preparação para o ML Studio (clássico) - Processo de Ciência de Dados de Equipa
description: Pré-processar e limpar dados para prepará-lo para ser usado eficazmente para a aprendizagem automática.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76720049"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Tarefas para preparar dados para machine learning otimizado
Os dados de pré-processamento e limpeza são tarefas importantes que devem ser conduzidas antes de um conjunto de dados poder ser utilizado para a formação de modelos. Os dados brutos são muitas vezes barulhentos e pouco fiáveis, e podem estar a faltar valores. A utilização destes dados para modelação pode produzir resultados enganadores. Estas tarefas fazem parte do Processo de Ciência de Dados de Equipa (TDSP) e seguem tipicamente uma exploração inicial de um conjunto de dados utilizado para descobrir e planear o pré-processamento necessário. Para obter instruções mais detalhadas sobre o processo TDSP, consulte os passos descritos no [Processo de Ciência de Dados da Equipa.](overview.md)

As tarefas de pré-processamento e limpeza, como a tarefa de exploração de dados, podem ser realizadas em uma grande variedade de ambientes, tais como SQL ou Hive ou Azure Machine Learning Studio (clássico), e com várias ferramentas e idiomas, como R ou Python, dependendo de onde os seus dados são armazenados e como é formatado. Uma vez que a TDSP é de natureza iterativa, estas tarefas podem decorrer em várias etapas no fluxo de trabalho do processo.

Este artigo introduz vários conceitos e tarefas de processamento de dados que podem ser realizadas antes ou depois de ingerir dados no Azure Machine Learning Studio (clássico).

Para um exemplo de exploração de dados e pré-processamento feito dentro do Azure Machine Learning Studio (clássico), consulte o [vídeo de dados pré-processamento.](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/)

## <a name="why-pre-process-and-clean-data"></a>Porquê pré-processar e limpar dados?
Os dados do mundo real são recolhidos de várias fontes e processos e podem conter irregularidades ou dados corruptos que comprometem a qualidade do conjunto de dados. As questões típicas de qualidade de dados que surgem são:

* **Incompleto**: Os dados carecem de atributos ou de valores em falta.
* **Barulhento**: Os dados contêm registos erróneos ou forasteiros.
* **Inconsistente:** Os dados contêm registos ou discrepâncias contraditórios.

Os dados de qualidade são um pré-requisito para modelos preditivos de qualidade. Para evitar "lixo, lixo para fora" e melhorar a qualidade dos dados e, portanto, o desempenho do modelo, é imperativo realizar um ecrã de saúde de dados para detetar problemas de dados precocemente e decidir sobre as etapas correspondentes de processamento e limpeza de dados.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Quais são alguns típicos ecrãs de saúde de dados que são utilizados?
Podemos verificar a qualidade geral dos dados verificando:

* O número de **registos.**
* O número de **atributos** (ou **funcionalidades).**
* Os **tipos de dados** de atributos (nominais, ordinais ou contínuos).
* O número de **valores em falta.**
* **Dados bem formados.**
  * Se os dados estiverem em TSV ou CSV, verifique se os separadores da coluna e os separadores de linha separam sempre corretamente as colunas e linhas.
  * Se os dados estiverem em formato HTML ou XML, verifique se os dados estão bem formados com base nos respetivos padrões.
  * Pode igualmente ser necessária a análise de informações estruturadas a partir de dados semi-estruturados ou não estruturados.
* **Registos de dados inconsistentes.** Verifique a gama de valores permitida. Por exemplo, se os dados contiverem GPA do aluno (média de ponto de nota), verifique se a GPA está na gama designada, digamos 0~4.

Quando encontra problemas com dados, são necessárias etapas de **processamento,** que muitas vezes envolvem a limpeza de valores em falta, normalização de dados, discretização, processamento de texto para remover e/ou substituir caracteres incorporados que possam afetar o alinhamento de dados, tipos de dados mistos em campos comuns, entre outros.

**A Azure Machine Learning consome dados tabulares bem formados.**  Se os dados já estiverem em forma tabular, o pré-processamento de dados pode ser realizado diretamente com o Azure Machine Learning Studio (clássico) no Machine Learning.  Se os dados não estiverem na forma tabular, digamos que está em XML, pode ser necessária a análise para converter os dados em formato tabular.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Quais são algumas das principais tarefas no pré-processamento de dados?
* **Limpeza de dados**: Preencha os valores em falta, detete e remova dados ruidosos e outliers.
* **Transformação de**dados : Normalizar os dados para reduzir dimensões e ruído.
* **Redução de dados**: Registos de dados de amostra ou atributos para facilitar o tratamento de dados.
* **Discretização de dados**: Converta atributos contínuos em atributos categóricos para facilitar a utilização com certos métodos de aprendizagem automática.
* **Limpeza de texto**: remova caracteres incorporados que possam causar desalinhamento de dados, por exemplo, separados de separados num ficheiro de dados separados por separados por separados, embutidas em novas linhas que possam quebrar registos, por exemplo.

As secções abaixo detalham algumas destas etapas de processamento de dados.

## <a name="how-to-deal-with-missing-values"></a>Como lidar com valores em falta?
Para lidar com os valores em falta, o melhor é primeiro identificar a razão para os valores em falta para melhor lidar com o problema. Os métodos típicos de tratamento de valor em falta são:

* **Eliminação**: Remover registos com valores em falta
* **Substituição do boneco**: Substitua os valores em falta por um valor falso: por exemplo, *desconhecido* para valores categóricos ou 0 para valores numéricos.
* **Substituição média**: Se os dados em falta forem numéricos, substitua os valores em falta pela média.
* **Substituição frequente**: Se os dados em falta forem categóricos, substitua os valores em falta pelo item mais frequente
* **Substituição de regressão**: Utilize um método de regressão para substituir os valores em falta por valores regredidos.  

## <a name="how-to-normalize-data"></a>Como normalizar os dados?
A normalização dos dados reescala os valores numéricos para um intervalo especificado. Os métodos de normalização de dados populares incluem:

* **Normalização Min-Max**: Transforme linearmente os dados num intervalo, por exemplo, entre 0 e 1, onde o valor do min é dimensionado para 0 e o valor máximo para 1.
* **Normalização Z-score**: Dimensione os dados com base no desvio médio e padrão: divida a diferença entre os dados e a média pelo desvio padrão.
* **Escala decimal**: Dimensione os dados movendo o ponto decimal do valor do atributo.  

## <a name="how-to-discretize-data"></a>Como desestar dados?
Os dados podem ser discretizados convertendo valores contínuos em atributos ou intervalos nominais. Algumas formas de fazer isto são:

* **Binning de largura igual**: Divida a gama de todos os valores possíveis de um atributo em grupos N do mesmo tamanho e atribua os valores que caem num recipiente com o número do recipiente.
* **Binário de altura igual**: Divida a gama de todos os valores possíveis de um atributo em grupos N, cada um contendo o mesmo número de instâncias, e depois atribua os valores que caem num caixote com o número do lixo.  

## <a name="how-to-reduce-data"></a>Como reduzir os dados?
Existem vários métodos para reduzir o tamanho dos dados para facilitar o tratamento dos dados. Dependendo do tamanho dos dados e do domínio, podem ser aplicados os seguintes métodos:

* **Amostragem de**registo : Prove os registos de dados e escolha apenas o subconjunto representativo a partir dos dados.
* **Amostragem do atributo**: Selecione apenas um subconjunto dos atributos mais importantes a partir dos dados.  
* **Agregação**: Divida os dados em grupos e guarde os números para cada grupo. Por exemplo, o número diário de receitas de uma cadeia de restaurantes ao longo dos últimos 20 anos pode ser agregado a receitas mensais para reduzir a dimensão dos dados.  

## <a name="how-to-clean-text-data"></a>Como limpar dados de texto?
**Os campos de texto em dados tabulares** podem incluir caracteres que afetam o alinhamento de colunas e/ou os limites de registo. Por exemplo, os separados incorporados num ficheiro separado do separado por separado do separador causam desalinhamento da coluna e os caracteres de novas linhas quebram linhas de gravação. O manuseamento impróprio do texto codificado durante a escrita ou leitura de texto leva à perda de informação, à introdução inadvertida de caracteres ilegíveis (como nulos), podendo também afetar a análise de texto. Pode ser necessária uma análise e edição cuidadosas para limpar campos de texto para um alinhamento adequado e/ou extrair dados estruturados de dados de texto não estruturados ou semi-estruturados.

**A exploração de** dados oferece uma visão antecipada dos dados. Uma série de questões de dados podem ser descobertas durante este passo e os métodos correspondentes podem ser aplicados para resolver essas questões.  É importante fazer perguntas como a origem da questão e como a questão pode ter sido introduzida. Este processo também o ajuda a decidir sobre as etapas de processamento de dados que precisam de ser tomadas para os resolver. Identificar os casos de utilização final e as personalidades também pode ser usado para priorizar o esforço de processamento de dados.

## <a name="references"></a>Referências
> *Data Mining: Concepts and Techniques*, Terceira Edição, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber e Jian Pei
> 
> 

