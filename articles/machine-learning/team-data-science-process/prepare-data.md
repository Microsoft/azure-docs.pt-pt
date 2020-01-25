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
Os dados de pré-processamento e limpeza são tarefas importantes que devem ser realizadas antes de um conjunto de dados poder ser utilizado para a formação de modelos. Dados não processados costuma desnecessárias e pouco fiáveis e podem estar em falta valores. Usar esses dados para a Modelagem pode produzir resultados enganosos. Essas tarefas fazem parte do Team Data Science Process (TDSP) e, normalmente, siga uma análise inicial de um conjunto de dados utilizado para detetar e planear o pré-processamento de necessário. Para obter instruções sobre o processo TDSP mais detalhadas, consulte os passos descritos no [Team Data Science Process](overview.md).

As tarefas de pré-processamento e limpeza, como a tarefa de exploração de dados, podem ser executadas em uma ampla variedade de ambientes, como SQL ou Hive ou Azure Machine Learning Studio (clássico) e com várias ferramentas e linguagens, como R ou Python, dependendo de onde seus dados é armazenado e como é formatado. Uma vez que o TDSP é interativo por natureza, estas tarefas podem ocorrer em várias etapas do fluxo de trabalho do processo.

Este artigo apresenta vários conceitos de processamento de dados e tarefas que podem ser realizadas antes ou depois da ingestão de dados em Azure Machine Learning Studio (clássico).

Para um exemplo de exploração de dados e pré-processamento feito dentro do Azure Machine Learning Studio (clássico), consulte o vídeo [de dados pré-processamento.](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/)

## <a name="why-pre-process-and-clean-data"></a>Por que motivo pré-processar e limpar dados?
Recolha de dados do mundo real de várias origens e processos e ele podem conter irregularidades ou dados danificados comprometer a qualidade do conjunto de dados. Os problemas de qualidade de dados típica que surgem são:

* **Incompleta**: dados não possui atributos ou que contém valores em falta.
* **Ruidosos**: dados contém registos errados ou valores atípicos.
* **Inconsistente**: dados contém registos em conflito ou discrepâncias.

Dados de qualidade são um pré-requisito para modelos de previsão de qualidade. Para evitar "lixo no lixo out" e melhorar a qualidade de dados e, portanto, do desempenho de um modelo, é imperativo para conduzir um ecrã de estado de funcionamento de dados para detetar problemas de dados desde o início e decidir qual o processamento de dados correspondente e os passos de limpeza.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Quais são algumas telas de estado de funcionamento de típica de dados que são empregadas?
Podemos verificar a qualidade geral de dados através da verificação:

* O número de **registos**.
* O número de **atributos** (ou **funcionalidades**).
* O atributo **tipos de dados** (nominal, ordinal ou contínua).
* O número de **valores em falta**.
* **Dados bem formados.**
  * Se os dados estão sendo TSV ou CSV, verifique que os separadores de coluna e os separadores de linha sempre corretamente separam colunas e linhas.
  * Se os dados em formato HTML ou XML, verifique se os dados são bem formados com base em seus respectivos padrões.
  * Análise também poderá ser necessária para extrair informações estruturadas de dados estruturados ou semi-estruturados.
* **Registos de dados inconsistentes**. Verifique o intervalo de valores são permitidos. Por exemplo, se os dados contenham GPA do aluno (média de ponto de nota), verifique se a AG está na gama designada, digamos 0~4.

Quando encontra problemas com dados, são necessárias etapas de **processamento,** que muitas vezes envolvem a limpeza de valores em falta, a normalização de dados, a discretaização, o processamento de texto para remover e/ou substituir caracteres incorporados que podem afetar o alinhamento de dados, tipos de dados mistos em campos comuns, entre outros.

**O Azure Machine Learning consome dados tabulares bem formados**.  Se os dados já estiverem em formato de tabela, o pré-processamento de dados poderá ser executado diretamente com Azure Machine Learning Studio (clássico) no Machine Learning.  Se os dados não estão num formato tabular, digamos que está a ser XML, a análise pode ser necessária para converter os dados em formato tabular.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Quais são algumas das tarefas principais no pré-processamento de dados?
* **Limpeza de dados:** Preencha os valores em falta, detete e remova dados ruidosos e outliers.
* **Transformação de dados**: normalizar dados para reduzir as dimensões e de ruído.
* **Redução de dados**: registos de dados ou atributos para processamento de dados mais fácil de exemplo.
* **A discretização de dados**: converter atributos contínuos para atributos categóricos para facilidade de utilização com determinados métodos do machine learning.
* **Limpeza de texto**: remova caracteres incorporados que possam causar desalinhamento de dados, por exemplo, separados de separados por separados por separados, novas linhas que possam quebrar registos, por exemplo.

As secções abaixo detalham alguns destes passos de processamento de dados.

## <a name="how-to-deal-with-missing-values"></a>Como lidar com valores em falta?
Para lidar com valores em falta, é recomendável primeiro identificar o motivo para os valores em falta para processar melhor o problema. Os métodos de manipulação de valor em falta típicos são:

* **Eliminação**: remover registros com valores em falta
* **Substituição de fictício**: substituir valores em falta com um valor fictício: por exemplo, *desconhecido* para 0 ou categóricos dos valores numéricos.
* **Significa que a substituição**: se os dados em falta são numéricos, substitua os valores em falta com a média.
* **Substituição de frequentes**: se os dados em falta são categóricos, substitua os valores em falta com o item mais frequente
* **Substituição de regressão**: utilizar um método de regressão para substituir valores em falta com valores regredidos.  

## <a name="how-to-normalize-data"></a>Como normalizar dados?
A normalização dos dados redimensiona os valores numéricos para uma gama especificada. Os métodos de normalização de dados populares incluem:

* **Normalização mínima-máxima**: linearmente transformar os dados para um intervalo de, digamos entre 0 e 1, em que o valor mínimo é dimensionado para 0 e o máximo valor para 1.
* **Normalização de pontuação de Z**: Dimensionar dados com base na média e desvio padrão: dividir a diferença entre os dados e a média pelo desvio-padrão.
* **Dimensionamento decimal**: dimensionar os dados ao mover a vírgula decimal do valor de atributo.  

## <a name="how-to-discretize-data"></a>Como discretizar os dados?
Podem ser discretizados dados ao converter valores contínuas para atributos nominal ou intervalos. Seguem-se algumas formas de fazer isso:

* **A Discretização de largura de igual**: dividir o intervalo de todos os valores possíveis de um atributo em N de grupos do mesmo tamanho e atribuir os valores que se enquadram-se de um contentor com o número de bin.
* **A compartimentação de altura igual**: dividir o intervalo de todos os valores possíveis de um atributo em grupos de N, cada um contendo o mesmo número de instâncias, em seguida, atribuir os valores que se enquadram-se de um contentor com o número de bin.  

## <a name="how-to-reduce-data"></a>Como reduzir dados?
Existem vários métodos para reduzir o tamanho dos dados para o mais fácil de manuseamento de dados. Dependendo do tamanho dos dados e o domínio, podem ser aplicados os seguintes métodos:

* **Registe a amostragem**: os registos de dados de exemplo e selecione apenas o subconjunto representativo dos dados.
* **Amostragem de atributo**: selecionar apenas um subconjunto dos atributos mais importantes a partir dos dados.  
* **Agregação**: dividir os dados em grupos e armazenar os números de cada grupo. Por exemplo, os números de receita diária de uma cadeia de restauração nos últimos 20 anos podem ser agregados a receita mensal para reduzir o tamanho dos dados.  

## <a name="how-to-clean-text-data"></a>Como limpar dados de texto?
Os campos de **texto nos dados tabulares** podem incluir caracteres que afetam o alinhamento das colunas e/ou limites de registo. Por exemplo, os separadores incorporados num ficheiro separado por separados causam desalinhamento da coluna e os caracteres de linha nova incorporados quebram linhas de registo. O manuseamento impróprio da codificação de texto sem escrever ou ler texto conduz à perda de informação, introdução inadvertida de caracteres ilegíveis (como nulos), e também pode afetar a análise de texto. Análise cuidadosa e edição podem ser necessários para limpar os campos de texto para o alinhamento correto e/ou para os dados estruturado de extração dos dados de texto não estruturados ou semiestruturados.

**Exploração de dados** oferece uma visão antecipada os dados. Um número de problemas de dados pode ser que não foi abordado durante este passo e métodos correspondentes podem ser aplicados para resolver esses problemas.  É importante fazer perguntas, tais como o que é a origem do problema e como o problema pode ter sido introduzido. Este processo também o ajuda a decidir sobre as medidas de processamento de dados que precisam de ser tomadas para resolvê-los. Identificar os casos e personalidades de uso final também pode ser usado para priorizar o esforço de processamento de dados.

## <a name="references"></a>Referências
> *Mineração de dados: Conceitos e técnicas*, 3ª edição, Morgan Kaufmann, 2011, Jiawei Han Micheline Kamber e Jian Pei
> 
> 

