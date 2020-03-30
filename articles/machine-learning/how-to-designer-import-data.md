---
title: Importar dados
titleSuffix: Azure Machine Learning
description: Aprenda a importar os seus dados para o designer de Machine Learning Azure de várias fontes de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: bd1c2ca182ae8be8425246f691dca805bf38637b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064021"
---
# <a name="import-your-data-into-azure-machine-learning-designer-preview"></a>Importe os seus dados para o designer de machine learning Azure (pré-visualização)

Neste artigo, aprende-se a importar os seus próprios dados no designer para criar soluções personalizadas. Há duas formas de importar dados para o designer: 

* **Conjuntos de dados de Aprendizagem Automática Azure** - Registe [os conjuntos de dados](concept-data.md#datasets) no Azure Machine Learning para permitir funcionalidades avançadas que o ajudem a gerir os seus dados.
* **Módulo de Dados de Importação** - Utilize o módulo dados de [importação](algorithm-module-reference/import-data.md) para aceder diretamente a dados de fontes de dados online.

## <a name="use-azure-machine-learning-datasets"></a>Utilizar conjuntos de dados azure machine learning

Recomendamos que utilize [conjuntos](concept-data.md#datasets) de dados para importar dados para o designer. Ao registar um conjunto de dados, pode tirar o máximo partido de funcionalidades avançadas de dados, como [verversão e rastreio](how-to-version-track-datasets.md) e [monitorização de dados](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Registar um conjunto de dados

Pode registar os conjuntos de dados existentes [programaticamente com o SDK](how-to-create-register-datasets.md#use-the-sdk) ou [visualmente no estúdio Azure Machine Learning.](how-to-create-register-datasets.md#use-the-ui)

Também pode registar a saída de qualquer módulo de design como conjunto de dados.

1. Selecione o módulo que produz os dados que pretende registar.

1. No painel de propriedades, selecione **Outputs** > **Registe o conjunto de dados**.

    ![Screenshot mostrando como navegar para a opção Registar Dataset](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Utilize um conjunto de dados

Os seus conjuntos de dados registados podem ser encontrados na paleta de módulos, em **conjuntode** > Dados**My Datasets**. Para utilizar um conjunto de dados, arraste-o e deixe-o cair na tela do gasoduto. Em seguida, ligue a porta de saída do conjunto de dados a outros módulos da paleta.

![Screenshot mostrando localização de conjuntos de dados guardados na paleta de designer](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> Atualmente, o designer apenas suporta o processamento de conjuntos de [dados tabular.](how-to-create-register-datasets.md#dataset-types) Se pretender utilizar conjuntos de dados de [ficheiros,](how-to-create-register-datasets.md#dataset-types)utilize o SDK de Aprendizagem automática Azure disponível para Python e R.

## <a name="import-data-using-the-import-data-module"></a>Dados de importação utilizando o módulo de dados de importação

Embora recomendemos que utilize conjuntos de dados para importar dados, também pode utilizar o módulo [de Dados de Importação.](algorithm-module-reference/import-data.md) O módulo de dados de importação ignora o registo do seu conjunto de dados no Azure Machine Learning e importa dados diretamente de uma loja de [dados](concept-data.md#datastores) ou URL HTTP.

Para obter informações detalhadas sobre como utilizar o módulo de dados de importação, consulte a página de [referência dos dados de importação](algorithm-module-reference/import-data.md).

> [!NOTE]
> Se o seu conjunto de dados tiver demasiadas colunas, poderá encontrar o seguinte erro: "A validação falhou devido à limitação do tamanho". Para evitar isto, [registe o conjunto de dados na interface datasets](how-to-create-register-datasets.md#use-the-ui).

## <a name="supported-sources"></a>Fontes apoiadas

Esta secção lista as fontes de dados suportadas pelo designer. Os dados entram no designer a partir de uma datastore ou de conjunto de [dados tabular](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Fontes de datastore
Para obter uma lista de fontes suportadas de datastore, consulte os dados do Access nos serviços de [armazenamento do Azure.](how-to-access-data.md#supported-data-storage-service-types)

### <a name="tabular-dataset-sources"></a>Fontes tabular de conjuntos de dados

O designer suporta conjuntos de dados tabular criados a partir das seguintes fontes:
 * Ficheiros delimitados
 * Ficheiros JSON
 * Ficheiros Parquet
 * Consultas SQL

## <a name="data-types"></a>Tipos de dados

O designer reconhece internamente os seguintes tipos de dados:

* Cadeia
* Número inteiro
* Decimal
* Booleano
* Date

O designer usa um tipo de dados internos para passar dados entre módulos. Pode converter explicitamente os seus dados em formato de tabela de dados utilizando o módulo [Converte para Dataset.](algorithm-module-reference/convert-to-dataset.md) Qualquer módulo que aceite formatos diferentes do formato interno converterá os dados silenciosamente antes de passá-lo para o módulo seguinte.

## <a name="data-constraints"></a>Restrições de dados

Os módulos do designer são limitados pelo tamanho do alvo da computação. Para conjuntos de dados maiores, deve utilizar um recurso de computação azure machine learning maior. Para obter mais informações sobre o computacional Azure Machine Learning, veja [quais são os alvos da computação no Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="next-steps"></a>Passos seguintes

Conheça o básico do designer com [tutorial: Preveja](tutorial-designer-automobile-price-train-score.md)o preço do automóvel com o designer.
