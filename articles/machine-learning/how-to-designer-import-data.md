---
title: Importar dados para o designer
titleSuffix: Azure Machine Learning
description: Aprenda a importar dados para o designer Azure Machine Learning utilizando conjuntos de dados de aprendizagem automática Azure e o módulo de dados de importação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 5569b625b8f0c4ba890c0cd5b1700ca6fe83d968
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591993"
---
# <a name="import-data-into-azure-machine-learning-designer"></a>Importar dados para o designer de aprendizagem automática Azure

Neste artigo, aprende-se a importar os seus próprios dados no designer para criar soluções personalizadas. Há duas formas de importar dados para o designer: 

* **Conjuntos de dados Azure Machine Learning** - Registar [conjuntos de dados](concept-data.md#datasets) no Azure Machine Learning para permitir funcionalidades avançadas que o ajudam a gerir os seus dados.
* **Módulo de Dados de Importação** - Utilize o módulo [de Dados de Importação](algorithm-module-reference/import-data.md) para aceder diretamente aos dados de fontes de dados online.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Utilize conjuntos de dados de aprendizagem automática Azure

Recomendamos que utilize [conjuntos de dados](concept-data.md#datasets) para importar dados para o designer. Quando regista um conjunto de dados, pode tirar o máximo partido das funcionalidades avançadas de dados, como [a versão, o rastreio](how-to-version-track-datasets.md) e a [monitorização de dados.](how-to-monitor-datasets.md)

### <a name="register-a-dataset"></a>Registar um conjunto de dados

Pode registar os conjuntos de dados existentes [programaticamente com o SDK](how-to-create-register-datasets.md#datasets-sdk) ou [visualmente no estúdio Azure Machine Learning](how-to-connect-data-ui.md#create-datasets).

Também pode registar a saída de qualquer módulo de design como conjunto de dados.

1. Selecione o módulo que produz os dados que pretende registar.

1. No painel de propriedades, selecione **Outputs + logs**  >  **Registar conjunto de dados**.

    ![Screenshot mostrando como navegar para a opção Conjunto de Dados do Registo](media/how-to-designer-import-data/register-dataset-designer.png)

Se os dados de saída do módulo estiverem num formato tabular, deve optar por registar a saída como conjunto de dados de **ficheiros** ou **conjunto de dados tabulares**.

 - **O conjunto de dados de ficheiros** regista a pasta de saída do módulo como conjunto de dados de ficheiros. A pasta de saída contém um ficheiro de dados e ficheiros de meta que o designer utiliza internamente. Selecione esta opção se pretender continuar a utilizar o conjunto de dados registado no designer. 

 - **O conjunto de dados tabular** regista apenas o ficheiro de dados de saída do módulo como um conjunto de dados tabular. Este formato é facilmente consumido por outras ferramentas, por exemplo, em Machine Learning automatizado ou no Python SDK. Selecione esta opção se pretender utilizar o conjunto de dados registado fora do designer.  



### <a name="use-a-dataset"></a>Utilize um conjunto de dados

Os seus conjuntos de dados registados podem ser encontrados na paleta de **módulos, em Conjuntos de Dados**. Para utilizar um conjunto de dados, arraste-o e jogue-o sobre a tela do gasoduto. Em seguida, ligue a porta de saída do conjunto de dados a outros módulos na tela. 

Se registar um conjunto de dados de ficheiros, o tipo de porta de saída do conjunto de dados é **AnyDirectory**. Se registar um conjunto de dados Tabular, o tipo de porta de saída do conjunto de dados se **dataFrameDirectory**. Note que se ligar a porta de saída do conjunto de dados a outros módulos do designer, o tipo de porta de conjuntos de dados e módulos deve estar alinhado.

![Screenshot mostrando a localização de conjuntos de dados guardados na paleta de designers](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> O designer suporta [a versão do conjunto de dados.](how-to-version-track-datasets.md) Especifique a versão do conjunto de dados no painel de propriedade do módulo de conjunto de dados.


## <a name="import-data-using-the-import-data-module"></a>Dados de importação utilizando o módulo de dados de importação

Embora recomendemos que utilize conjuntos de dados para importar dados, também pode utilizar o módulo [de Dados de Importação.](algorithm-module-reference/import-data.md) O módulo de Dados de Importação ignora o registo do seu conjunto de dados no Azure Machine Learning e importa dados diretamente de uma [datastore](concept-data.md#datastores) ou URL HTTP.

Para obter informações detalhadas sobre como utilizar o módulo de Dados de Importação, consulte a [página de referência de Dados de Importação](algorithm-module-reference/import-data.md).

> [!NOTE]
> Se o seu conjunto de dados tiver demasiadas colunas, poderá encontrar o seguinte erro: "A validação falhou devido à limitação de tamanho". Para evitar isto, [registe o conjunto de dados na interface Datasets](how-to-connect-data-ui.md#create-datasets).

## <a name="supported-sources"></a>Fontes apoiadas

Esta secção lista as fontes de dados suportadas pelo designer. Os dados chegam ao designer a partir de uma datastore ou de um conjunto de [dados tabulares.](how-to-create-register-datasets.md#dataset-types)

### <a name="datastore-sources"></a>Fontes de datas-loja
Para obter uma lista de fontes de datas-lojas suportadas, consulte [os dados do Access nos serviços de armazenamento Azure](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Fontes de conjunto de dados tabulares

O designer suporta conjuntos de dados tabulares criados a partir das seguintes fontes:
 * Ficheiros delimitados
 * Ficheiros JSON
 * Ficheiros Parquet
 * Consultas SQL

## <a name="data-types"></a>Tipos de dados

O designer reconhece internamente os seguintes tipos de dados:

* String
* Número inteiro
* Decimal
* Booleano
* Date

O designer usa um tipo de dados interno para passar dados entre módulos. Pode converter explicitamente os seus dados em formato de tabela de dados utilizando o módulo [Converte-se para Conjunto de Dados.](algorithm-module-reference/convert-to-dataset.md) Qualquer módulo que aceite formatos diferentes do formato interno converterá os dados silenciosamente antes de os passar para o módulo seguinte.

## <a name="data-constraints"></a>Restrições de dados

Os módulos no designer são limitados pelo tamanho do alvo do cálculo. Para conjuntos de dados maiores, deve utilizar um recurso de computação Azure Machine Learning maior. Para obter mais informações sobre o cálculo Azure Machine Learning, veja [quais são os alvos de computação em Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="access-data-in-a-virtual-network"></a>Aceder a dados numa rede virtual

Se o seu espaço de trabalho estiver numa rede virtual, deve executar etapas de configuração adicionais para visualizar dados no designer. Para obter mais informações sobre como utilizar datastores e conjuntos de dados numa rede virtual, consulte [o estúdio Use Azure Machine Learning numa rede virtual Azure](how-to-enable-studio-virtual-network.md).

## <a name="next-steps"></a>Passos seguintes

Aprenda os fundamentos do designer com este [Tutorial: Preveja o preço do automóvel com o designer.](tutorial-designer-automobile-price-train-score.md)
