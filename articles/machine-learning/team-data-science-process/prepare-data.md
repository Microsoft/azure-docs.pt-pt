---
title: Dados de preparação para ml studio (clássico) - Team Data Science Process
description: Pré-processo e dados limpos para prepará-lo para ser usado eficazmente para aprendizagem automática.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720049"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Tarefas para preparar dados para machine learning otimizado
Os dados de pré-processamento e limpeza são tarefas importantes que devem ser realizadas antes de um conjunto de dados poder ser utilizado para a formação de modelos. Os dados brutos são muitas vezes barulhentos e pouco fiáveis, e podem estar a perder valores. A utilização destes dados para modelação pode produzir resultados enganadores. Estas tarefas fazem parte do Processo de Ciência de Dados da Equipa (TDSP) e normalmente seguem uma exploração inicial de um conjunto de dados usado para descobrir e planear o pré-processamento necessário. Para obter instruções mais detalhadas sobre o processo TDSP, consulte os passos delineados no Processo de Ciência de Dados da [Equipa](overview.md).

Tarefas de pré-processamento e limpeza, como a tarefa de exploração de dados, podem ser realizadas em uma grande variedade de ambientes, tais como SQL ou Hive ou Azure Machine Learning Studio (clássico), e com várias ferramentas e idiomas, como R ou Python, dependendo de onde os seus dados é armazenado e como é formatado. Uma vez que a TDSP é de natureza iterativa, estas tarefas podem decorrer em várias etapas no fluxo de trabalho do processo.

Este artigo introduz vários conceitos e tarefas de processamento de dados que podem ser realizadas antes ou depois de ingerir dados no Azure Machine Learning Studio (clássico).

Para um exemplo de exploração de dados e pré-processamento feito dentro do Azure Machine Learning Studio (clássico), consulte o vídeo [de dados pré-processamento.](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/)

## <a name="why-pre-process-and-clean-data"></a>Porquê pré-processar e limpar dados?
Os dados do mundo real são recolhidos de várias fontes e processos e podem conter irregularidades ou dados corruptos que comprometam a qualidade do conjunto de dados. As questões típicas de qualidade de dados que surgem são:

* **Incompleto**: Os dados carecem de atributos ou que contenham valores em falta.
* **Ruidoso**: Os dados contêm registos ou outliers erróneos.
* **Inconsistente**: Os dados contêm registos ou discrepâncias contraditórias.

Os dados de qualidade são um pré-requisito para modelos preditivos de qualidade. Para evitar "lixo, lixo para fora" e melhorar a qualidade dos dados e, portanto, modelar o desempenho, é imperativo realizar um ecrã de saúde de dados para detetar problemas de dados precocemente e decidir sobre as etapas correspondentes de processamento e limpeza de dados.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Quais são alguns ecrãs típicos de saúde de dados que são empregados?
Podemos verificar a qualidade geral dos dados verificando:

* O número de **registos.**
* O número de **atributos** (ou **características).**
* Os tipos de **dados** de atributos (nominal, ordinal ou contínuo).
* O número de **valores em falta.**
* **Dados bem formados.**
  * Se os dados estiverem em TSV ou CSV, verifique se os separadores de colunas e separadores de linha separam corretamente as colunas e as linhas.
  * Se os dados estiverem em formato HTML ou XML, verifique se os dados estão bem formados com base nas respetivas normas.
  * A análise pode igualmente ser necessária para extrair informações estruturadas a partir de dados semi-estruturados ou não estruturados.
* **Registos de dados inconsistentes.** Verifique a gama de valores permitidos. Por exemplo, se os dados contenham GPA do aluno (média de ponto de nota), verifique se a AG está na gama designada, digamos 0~4.

Quando encontra problemas com dados, são necessárias etapas de **processamento,** que muitas vezes envolvem a limpeza de valores em falta, a normalização de dados, a discretaização, o processamento de texto para remover e/ou substituir caracteres incorporados que podem afetar o alinhamento de dados, tipos de dados mistos em campos comuns, entre outros.

**A Azure Machine Learning consome dados tabulares bem formados.**  Se os dados já estiverem em forma tabular, o pré-processamento de dados pode ser realizado diretamente com o Azure Machine Learning Studio (clássico) no Machine Learning.  Se os dados não estiverem na forma tabular, diga que está em XML, pode ser necessário analisar para converter os dados em forma tabular.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Quais são algumas das principais tarefas no pré-processamento de dados?
* **Limpeza de dados:** Preencha os valores em falta, detete e remova dados ruidosos e outliers.
* **Transformação de dados**: Normalizar os dados para reduzir dimensões e ruído.
* **Redução de dados**: Registos de dados da amostra ou atributos para um manuseamento mais fácil de dados.
* **Discretização de dados**: Converta atributos contínuos a atributos categóricos para facilitar o uso com certos métodos de aprendizagem automática.
* **Limpeza de texto**: remova caracteres incorporados que possam causar desalinhamento de dados, por exemplo, separados de separados por separados por separados, novas linhas que possam quebrar registos, por exemplo.

As secções abaixo detalham algumas destas etapas de processamento de dados.

## <a name="how-to-deal-with-missing-values"></a>Como lidar com valores em falta?
Para lidar com os valores em falta, o melhor é identificar primeiro a razão para os valores em falta lidarem melhor com o problema. Os métodos típicos de manuseamento de valor em falta são:

* **Eliminação**: Remover registos com valores em falta
* **Substituição**falsa : Substitua os valores em falta por um valor de boneco: por exemplo, *desconhecido* para valores numéricos ou categóricos.
* **Substituição média**: Se os dados em falta forem numéricos, substitua os valores em falta pela média.
* **Substituição frequente**: Se os dados em falta forem categóricos, substitua os valores em falta pelo item mais frequente
* **Substituição de regressão:** Utilize um método de regressão para substituir valores em falta por valores regredidos.  

## <a name="how-to-normalize-data"></a>Como normalizar os dados?
A normalização dos dados redimensiona os valores numéricos para uma gama especificada. Os métodos populares de normalização de dados incluem:

* **Normalização Min-Max**: A Lialmost transforme os dados num intervalo, por exemplo, entre 0 e 1, onde o valor do min é escalado para 0 e o valor máximo para 1.
* **Normalização**da pontuação Z : Dimensione os dados com base no desvio médio e padrão: divida a diferença entre os dados e a média pelo desvio padrão.
* **Escaladel decimal**: Dimensione os dados movendo o ponto decimal do valor do atributo.  

## <a name="how-to-discretize-data"></a>Como discricionar dados?
Os dados podem ser discretos convertendo valores contínuos em atributos ou intervalos nominais. Algumas maneiras de fazer isto são:

* **Ligação de largura**igual: Divida a gama de todos os valores possíveis de um atributo em grupos N do mesmo tamanho e atribua os valores que caem num caixote do lixo com o número do caixote do lixo.
* **Placa de altura igual**: Divida a gama de todos os valores possíveis de um atributo em grupos N, cada um contendo o mesmo número de instâncias, em seguida, atribua os valores que caem num caixote do lixo com o número do caixote do lixo.  

## <a name="how-to-reduce-data"></a>Como reduzir os dados?
Existem vários métodos para reduzir o tamanho dos dados para um manuseamento mais fácil dos dados. Dependendo do tamanho dos dados e do domínio, podem ser aplicados os seguintes métodos:

* **Amostragem de registo**: Experimente os registos de dados e escolha apenas o subconjunto representativo dos dados.
* **Amostragem de atributo:** Selecione apenas um subconjunto dos atributos mais importantes dos dados.  
* **Agregação**: Divida os dados em grupos e guarde os números para cada grupo. Por exemplo, o número diário de receitas de uma cadeia de restaurantes nos últimos 20 anos pode ser agregado a receitas mensais para reduzir a dimensão dos dados.  

## <a name="how-to-clean-text-data"></a>Como limpar dados de texto?
Os campos de **texto nos dados tabulares** podem incluir caracteres que afetam o alinhamento das colunas e/ou limites de registo. Por exemplo, os separadores incorporados num ficheiro separado por separados causam desalinhamento da coluna e os caracteres de linha nova incorporados quebram linhas de registo. O manuseamento impróprio da codificação de texto sem escrever ou ler texto conduz à perda de informação, introdução inadvertida de caracteres ilegíveis (como nulos), e também pode afetar a análise de texto. Pode ser necessária uma análise e edição cuidadosas para limpar os campos de texto para um alinhamento adequado e/ou para extrair dados estruturados a partir de dados de texto não estruturados ou semi-estruturados.

**A exploração** de dados oferece uma visão precoce dos dados. Durante este passo, podem ser descobertos alguns problemas de dados e os métodos correspondentes podem ser aplicados para resolver essas questões.  É importante fazer perguntas como qual é a origem da questão e como a questão pode ter sido introduzida. Este processo também o ajuda a decidir sobre as medidas de processamento de dados que precisam de ser tomadas para resolvê-los. Identificar os casos e personalidades de uso final também pode ser usado para priorizar o esforço de processamento de dados.

## <a name="references"></a>Referências
> *Data Mining: Conceitos e Técnicas*, Terceira Edição, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber e Jian Pei
> 
> 

