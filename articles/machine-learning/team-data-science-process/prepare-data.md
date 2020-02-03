---
title: Preparação de dados para ML Studio (clássico)-processo de ciência de dados de equipe
description: Pré-processar e limpar dados para o preparar para utilizar de forma eficaz para machine learning.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720049"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Tarefas para preparar dados para machine learning otimizado
Os dados de pré-processamento e limpeza são tarefas importantes que devem ser realizadas antes de um conjunto de dados poder ser utilizado para a formação de modelos. Dados não processados costuma desnecessárias e pouco fiáveis e podem estar em falta valores. Usar esses dados para a Modelagem pode produzir resultados enganosos. Essas tarefas fazem parte do Team Data Science Process (TDSP) e, normalmente, siga uma análise inicial de um conjunto de dados utilizado para detetar e planear o pré-processamento de necessário. Para obter instruções mais detalhadas sobre o processo TDSP, consulte os passos delineados no Processo de Ciência de Dados da [Equipa](overview.md).

As tarefas de pré-processamento e limpeza, como a tarefa de exploração de dados, podem ser executadas em uma ampla variedade de ambientes, como SQL ou Hive ou Azure Machine Learning Studio (clássico) e com várias ferramentas e linguagens, como R ou Python, dependendo de onde seus dados é armazenado e como é formatado. Uma vez que o TDSP é interativo por natureza, estas tarefas podem ocorrer em várias etapas do fluxo de trabalho do processo.

Este artigo apresenta vários conceitos de processamento de dados e tarefas que podem ser realizadas antes ou depois da ingestão de dados em Azure Machine Learning Studio (clássico).

Para um exemplo de exploração de dados e pré-processamento feito dentro do Azure Machine Learning Studio (clássico), consulte o vídeo [de dados pré-processamento.](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/)

## <a name="why-pre-process-and-clean-data"></a>Por que motivo pré-processar e limpar dados?
Recolha de dados do mundo real de várias origens e processos e ele podem conter irregularidades ou dados danificados comprometer a qualidade do conjunto de dados. Os problemas de qualidade de dados típica que surgem são:

* **Incompleto**: Os dados carecem de atributos ou que contenham valores em falta.
* **Ruidoso**: Os dados contêm registos ou outliers erróneos.
* **Inconsistente**: Os dados contêm registos ou discrepâncias contraditórias.

Dados de qualidade são um pré-requisito para modelos de previsão de qualidade. Para evitar "lixo no lixo out" e melhorar a qualidade de dados e, portanto, do desempenho de um modelo, é imperativo para conduzir um ecrã de estado de funcionamento de dados para detetar problemas de dados desde o início e decidir qual o processamento de dados correspondente e os passos de limpeza.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Quais são algumas telas de estado de funcionamento de típica de dados que são empregadas?
Podemos verificar a qualidade geral de dados através da verificação:

* O número de **registos.**
* O número de **atributos** (ou **características).**
* Os tipos de **dados** de atributos (nominal, ordinal ou contínuo).
* O número de **valores em falta.**
* **Dados bem formados.**
  * Se os dados estão sendo TSV ou CSV, verifique que os separadores de coluna e os separadores de linha sempre corretamente separam colunas e linhas.
  * Se os dados em formato HTML ou XML, verifique se os dados são bem formados com base em seus respectivos padrões.
  * Análise também poderá ser necessária para extrair informações estruturadas de dados estruturados ou semi-estruturados.
* **Registos de dados inconsistentes.** Verifique o intervalo de valores são permitidos. Por exemplo, se os dados contenham GPA do aluno (média de ponto de nota), verifique se a AG está na gama designada, digamos 0~4.

Quando encontra problemas com dados, são necessárias etapas de **processamento,** que muitas vezes envolvem a limpeza de valores em falta, a normalização de dados, a discretaização, o processamento de texto para remover e/ou substituir caracteres incorporados que podem afetar o alinhamento de dados, tipos de dados mistos em campos comuns, entre outros.

**A Azure Machine Learning consome dados tabulares bem formados.**  Se os dados já estiverem em formato de tabela, o pré-processamento de dados poderá ser executado diretamente com Azure Machine Learning Studio (clássico) no Machine Learning.  Se os dados não estão num formato tabular, digamos que está a ser XML, a análise pode ser necessária para converter os dados em formato tabular.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Quais são algumas das tarefas principais no pré-processamento de dados?
* **Limpeza de dados:** Preencha os valores em falta, detete e remova dados ruidosos e outliers.
* **Transformação de dados**: Normalizar os dados para reduzir dimensões e ruído.
* **Redução de dados**: Registos de dados da amostra ou atributos para um manuseamento mais fácil de dados.
* **Discretização de dados**: Converta atributos contínuos a atributos categóricos para facilitar o uso com certos métodos de aprendizagem automática.
* **Limpeza de texto**: remova caracteres incorporados que possam causar desalinhamento de dados, por exemplo, separados de separados por separados por separados, novas linhas que possam quebrar registos, por exemplo.

As secções abaixo detalham alguns destes passos de processamento de dados.

## <a name="how-to-deal-with-missing-values"></a>Como lidar com valores em falta?
Para lidar com valores em falta, é recomendável primeiro identificar o motivo para os valores em falta para processar melhor o problema. Os métodos de manipulação de valor em falta típicos são:

* **Eliminação**: Remover registos com valores em falta
* **Substituição**falsa : Substitua os valores em falta por um valor de boneco: por exemplo, *desconhecido* para valores numéricos ou categóricos.
* **Substituição média**: Se os dados em falta forem numéricos, substitua os valores em falta pela média.
* **Substituição frequente**: Se os dados em falta forem categóricos, substitua os valores em falta pelo item mais frequente
* **Substituição de regressão:** Utilize um método de regressão para substituir valores em falta por valores regredidos.  

## <a name="how-to-normalize-data"></a>Como normalizar dados?
A normalização dos dados redimensiona os valores numéricos para uma gama especificada. Os métodos de normalização de dados populares incluem:

* **Normalização Min-Max**: A Lialmost transforme os dados num intervalo, por exemplo, entre 0 e 1, onde o valor do min é escalado para 0 e o valor máximo para 1.
* **Normalização**da pontuação Z : Dimensione os dados com base no desvio médio e padrão: divida a diferença entre os dados e a média pelo desvio padrão.
* **Escaladel decimal**: Dimensione os dados movendo o ponto decimal do valor do atributo.  

## <a name="how-to-discretize-data"></a>Como discretizar os dados?
Podem ser discretizados dados ao converter valores contínuas para atributos nominal ou intervalos. Seguem-se algumas formas de fazer isso:

* **Ligação de largura**igual: Divida a gama de todos os valores possíveis de um atributo em grupos N do mesmo tamanho e atribua os valores que caem num caixote do lixo com o número do caixote do lixo.
* **Placa de altura igual**: Divida a gama de todos os valores possíveis de um atributo em grupos N, cada um contendo o mesmo número de instâncias, em seguida, atribua os valores que caem num caixote do lixo com o número do caixote do lixo.  

## <a name="how-to-reduce-data"></a>Como reduzir dados?
Existem vários métodos para reduzir o tamanho dos dados para o mais fácil de manuseamento de dados. Dependendo do tamanho dos dados e o domínio, podem ser aplicados os seguintes métodos:

* **Amostragem de registo**: Experimente os registos de dados e escolha apenas o subconjunto representativo dos dados.
* **Amostragem de atributo:** Selecione apenas um subconjunto dos atributos mais importantes dos dados.  
* **Agregação**: Divida os dados em grupos e guarde os números para cada grupo. Por exemplo, os números de receita diária de uma cadeia de restauração nos últimos 20 anos podem ser agregados a receita mensal para reduzir o tamanho dos dados.  

## <a name="how-to-clean-text-data"></a>Como limpar dados de texto?
Os campos de **texto nos dados tabulares** podem incluir caracteres que afetam o alinhamento das colunas e/ou limites de registo. Por exemplo, os separadores incorporados num ficheiro separado por separados causam desalinhamento da coluna e os caracteres de linha nova incorporados quebram linhas de registo. O manuseamento impróprio da codificação de texto sem escrever ou ler texto conduz à perda de informação, introdução inadvertida de caracteres ilegíveis (como nulos), e também pode afetar a análise de texto. Análise cuidadosa e edição podem ser necessários para limpar os campos de texto para o alinhamento correto e/ou para os dados estruturado de extração dos dados de texto não estruturados ou semiestruturados.

**A exploração** de dados oferece uma visão precoce dos dados. Um número de problemas de dados pode ser que não foi abordado durante este passo e métodos correspondentes podem ser aplicados para resolver esses problemas.  É importante fazer perguntas, tais como o que é a origem do problema e como o problema pode ter sido introduzido. Este processo também o ajuda a decidir sobre as medidas de processamento de dados que precisam de ser tomadas para resolvê-los. Identificar os casos e personalidades de uso final também pode ser usado para priorizar o esforço de processamento de dados.

## <a name="references"></a>Referências
> *Data Mining: Conceitos e Técnicas*, Terceira Edição, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber e Jian Pei
> 
> 

