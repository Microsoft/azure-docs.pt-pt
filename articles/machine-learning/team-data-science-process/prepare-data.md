---
title: Limpar e preparar dados para o Azure Machine Learning - Team Data Science Process
description: Pré-processar e limpar dados para o preparar para utilizar de forma eficaz para machine learning.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2b3ec3352d6e1939b195bbba87b8a824404346ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61044605"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Tarefas para preparar dados para machine learning otimizado
Pré-processar e limpeza de dados são tarefas importantes que, normalmente, têm de ser conduzidas antes do conjunto de dados pode ser utilizado eficazmente para o machine learning. Dados não processados costuma desnecessárias e pouco fiáveis e podem estar em falta valores. Usar esses dados para a Modelagem pode produzir resultados enganosos. Essas tarefas fazem parte do Team Data Science Process (TDSP) e, normalmente, siga uma análise inicial de um conjunto de dados utilizado para detetar e planear o pré-processamento de necessário. Para obter instruções sobre o processo TDSP mais detalhadas, consulte os passos descritos no [Team Data Science Process](overview.md).

Processamento prévio e limpeza de tarefas, como a tarefa de exploração de dados, podem ser executadas numa ampla variedade de ambientes, tais como SQL ou Hive ou do Azure Machine Learning Studio e com várias ferramentas e linguagens, como o R ou Python, dependendo de onde os dados são armazenados e como é formatado. Uma vez que o TDSP é interativo por natureza, estas tarefas podem ocorrer em várias etapas do fluxo de trabalho do processo.

Este artigo apresenta vários conceitos de processamento de dados e tarefas que podem ser realizadas antes ou depois da ingestão de dados no Azure Machine Learning.

Para obter um exemplo de exploração de dados e processamento prévio de dentro do Azure Machine Learning studio, consulte a [pré-processar dados no Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) vídeo.

## <a name="why-pre-process-and-clean-data"></a>Por que motivo pré-processar e limpar dados?
Recolha de dados do mundo real de várias origens e processos e ele podem conter irregularidades ou dados danificados comprometer a qualidade do conjunto de dados. Os problemas de qualidade de dados típica que surgem são:

* **Incompleta**: Dados não possui atributos ou que contém valores em falta.
* **Ruidosos**: Os dados contêm registos errados ou valores atípicos.
* **Inconsistente**: Dados contém registos em conflito ou discrepâncias.

Dados de qualidade são um pré-requisito para modelos de previsão de qualidade. Para evitar "lixo no lixo out" e melhorar a qualidade de dados e, portanto, do desempenho de um modelo, é imperativo para conduzir um ecrã de estado de funcionamento de dados para detetar problemas de dados desde o início e decidir qual o processamento de dados correspondente e os passos de limpeza.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Quais são algumas telas de estado de funcionamento de típica de dados que são empregadas?
Podemos verificar a qualidade geral de dados através da verificação:

* O número de **registos**.
* O número de **atributos** (ou **funcionalidades**).
* O atributo **tipos de dados** (nominal, ordinal ou contínua).
* O número de **valores em falta**.
* **Da formação correta** dos dados.
  * Se os dados estão sendo TSV ou CSV, verifique que os separadores de coluna e os separadores de linha sempre corretamente separam colunas e linhas.
  * Se os dados em formato HTML ou XML, verifique se os dados são bem formados com base em seus respectivos padrões.
  * Análise também poderá ser necessária para extrair informações estruturadas de dados estruturados ou semi-estruturados.
* **Registos de dados inconsistentes**. Verifique o intervalo de valores são permitidos. Por exemplo, se os dados contêm GPA de estudante, verifique se o GPA está no intervalo designado, digamos 0 ~ 4.

Quando encontrar problemas com os dados, **passos de processamento** são necessárias que muitas vezes envolvem a limpeza de valores em falta, a normalização de dados, a discretização, processamento de texto para remover e/ou substituir os carateres que podem afetar os dados incorporados alinhamento, mistos tipos de dados em comum campos entre outros.

**O Azure Machine Learning consome dados tabulares bem formados**.  Se os dados já estão em formato tabular, o pré-processamento de dados pode ser executado diretamente com o Azure Machine Learning no Machine Learning Studio.  Se os dados não estão num formato tabular, digamos que está a ser XML, a análise pode ser necessária para converter os dados em formato tabular.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Quais são algumas das tarefas principais no pré-processamento de dados?
* **Limpeza de dados**:  Preencha ou valores em falta, detectar e remover dados desnecessárias e valores atípicos.
* **Transformação de dados**:  Normalizar dados para reduzir as dimensões e de ruído.
* **Redução de dados**:  Registos de dados de exemplo ou atributos para processamento de dados mais fácil.
* **A discretização de dados**:  Converta os atributos contínuos para atributos categóricos para facilidade de utilização com determinados métodos de aprendizado de máquina.
* **Limpeza de texto**: remova os caracteres incorporados que podem fazer com que o alinhamento incorreto da dados, para, por exemplo, separadores incorporados num arquivo de dados separados por tabulações, embedded novas linhas que podem ser interrompidos registos, etc.

As secções abaixo detalham alguns destes passos de processamento de dados.

## <a name="how-to-deal-with-missing-values"></a>Como lidar com valores em falta?
Para lidar com valores em falta, é recomendável primeiro identificar o motivo para os valores em falta para processar melhor o problema. Os métodos de manipulação de valor em falta típicos são:

* **Eliminação**: Remover registos com valores em falta
* **Substituição de fictício**: Substitua os valores em falta com um valor fictício: por exemplo, *desconhecido* para 0 ou categóricos dos valores numéricos.
* **Significa que a substituição**: Se os dados em falta são numéricos, substitua os valores em falta a média.
* **Substituição de frequentes**: Se os dados em falta são categóricos, substitua os valores em falta com o item mais frequente
* **Substituição de regressão**: Utilize um método de regressão para substituir valores em falta com valores regredidos.  

## <a name="how-to-normalize-data"></a>Como normalizar dados?
Normalização de dados redimensiona valores numéricos para um intervalo especificado. Os métodos de normalização de dados populares incluem:

* **Normalização mínima-máxima**: Linearmente transforme os dados para um intervalo de, digamos, entre 0 e 1, em que o valor mínimo é dimensionado para 0 e o máximo valor para 1.
* **Normalização de pontuação Z**: Dimensionar com base na média e o desvio-padrão de dados: dividir a diferença entre os dados e a média pelo desvio-padrão.
* **Dimensionamento decimal**: Dimensione os dados ao mover a vírgula decimal do valor de atributo.  

## <a name="how-to-discretize-data"></a>Como discretizar os dados?
Podem ser discretizados dados ao converter valores contínuas para atributos nominal ou intervalos. Seguem-se algumas formas de fazer isso:

* **A Discretização de largura de igual**: Dividir o intervalo de todos os valores possíveis de um atributo em N de grupos do mesmo tamanho e atribua os valores que se enquadram-se de um contentor com o número de bin.
* **A compartimentação de altura igual**: Dividir o intervalo de todos os valores possíveis de um atributo em grupos de N, cada um contendo o mesmo número de instâncias, em seguida, atribuir os valores que se enquadram-se de um contentor com o número de bin.  

## <a name="how-to-reduce-data"></a>Como reduzir dados?
Existem vários métodos para reduzir o tamanho dos dados para o mais fácil de manuseamento de dados. Dependendo do tamanho dos dados e o domínio, podem ser aplicados os seguintes métodos:

* **Registe a amostragem**: Os registos de dados de exemplo e escolha apenas o subconjunto representativo dos dados.
* **Amostragem de atributo**: Selecione apenas um subconjunto dos atributos mais importantes a partir dos dados.  
* **Agregação**: Dividir os dados em grupos e armazenar os números de cada grupo. Por exemplo, os números de receita diária de uma cadeia de restauração nos últimos 20 anos podem ser agregados a receita mensal para reduzir o tamanho dos dados.  

## <a name="how-to-clean-text-data"></a>Como limpar dados de texto?
**Campos de texto em dados tabulares** pode incluir carateres, que afetam os limites de alinhamento e/ou de registo de colunas. Por exemplo, incorporado separadores num alinhamento incorreto da coluna ficheiro separado por tabulações causa e incorporado novos caracteres de linha quebram a linha de registo. Manipulação de codificação de texto inadequado ao texto de escrita/leitura leva à perda de informações, inadvertida introdução de caracteres ilegíveis, por exemplo, nulos e pode também afetam análise de texto. Análise cuidadosa e edição podem ser necessários para limpar os campos de texto para o alinhamento correto e/ou para os dados estruturado de extração dos dados de texto não estruturados ou semiestruturados.

**Exploração de dados** oferece uma visão antecipada os dados. Um número de problemas de dados pode ser que não foi abordado durante este passo e métodos correspondentes podem ser aplicados para resolver esses problemas.  É importante fazer perguntas, tais como o que é a origem do problema e como o problema pode ter sido introduzido. Isto também ajuda a decidir sobre os passos de processamento de dados que têm de ser efetuados para resolvê-los. O tipo de informações uma pretende derivar a partir dos dados também pode ser utilizado para priorizar os esforços de processamento de dados.

## <a name="references"></a>Referências
> *Extração de dados: Conceitos e técnicas*, 3ª edição, Morgan Kaufmann, 2011, Jiawei Han Micheline Kamber e Jian Pei
> 
> 

