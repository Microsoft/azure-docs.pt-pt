---
title: Dados de importação para o designer (pré-visualização)
titleSuffix: Azure Machine Learning
description: Saiba como importar dados para o designer de Aprendizagem automática Azure (pré-visualização) a partir de várias fontes de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 92568ceb9c7f0a428189647f259a3f84278eb612
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327009"
---
# <a name="import-data-into-azure-machine-learning-designer-preview"></a>Dados de importação para o designer de aprendizagem automática Azure (pré-visualização)

Neste artigo, aprende-se a importar os seus próprios dados no designer para criar soluções personalizadas. Há duas formas de importar dados para o designer: 

* **Conjuntos de dados Azure Machine Learning** - Registar [conjuntos de dados](concept-data.md#datasets) no Azure Machine Learning para permitir funcionalidades avançadas que o ajudam a gerir os seus dados.
* **Módulo de Dados de Importação** - Utilize o módulo [de Dados de Importação](algorithm-module-reference/import-data.md) para aceder diretamente aos dados de fontes de dados online.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Utilize conjuntos de dados de aprendizagem automática Azure

Recomendamos que utilize [conjuntos de dados](concept-data.md#datasets) para importar dados para o designer. Quando regista um conjunto de dados, pode tirar o máximo partido das funcionalidades avançadas de dados, como [a versão, o rastreio](how-to-version-track-datasets.md) e a [monitorização de dados.](how-to-monitor-datasets.md)

### <a name="register-a-dataset"></a>Registar um conjunto de dados

Pode registar os conjuntos de dados existentes [programaticamente com o SDK](how-to-create-register-datasets.md#use-the-sdk) ou [visualmente no estúdio Azure Machine Learning](how-to-create-register-datasets.md#use-the-ui).

Também pode registar a saída de qualquer módulo de design como conjunto de dados.

1. Selecione o módulo que produz os dados que pretende registar.

1. No painel de propriedades, selecione **Outputs**  >  **Register dataset**.

    ![Screenshot mostrando como navegar para a opção Conjunto de Dados do Registo](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Utilize um conjunto de dados

Os seus conjuntos de dados registados podem ser encontrados na paleta de **módulos,** nos conjuntos de  >  **dados My Datasets**. Para utilizar um conjunto de dados, arraste-o e jogue-o sobre a tela do gasoduto. Em seguida, ligue a porta de saída do conjunto de dados a outros módulos na paleta.

![Screenshot mostrando a localização de conjuntos de dados guardados na paleta de designers](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> Atualmente, o designer apenas suporta o processamento [de conjuntos de dados tabulares.](how-to-create-register-datasets.md#dataset-types) Se pretender utilizar [conjuntos de dados de ficheiros,](how-to-create-register-datasets.md#dataset-types)utilize o Azure Machine Learning SDK disponível para Python e R.

## <a name="import-data-using-the-import-data-module"></a>Dados de importação utilizando o módulo de dados de importação

Embora recomendemos que utilize conjuntos de dados para importar dados, também pode utilizar o módulo [de Dados de Importação.](algorithm-module-reference/import-data.md) O módulo de Dados de Importação ignora o registo do seu conjunto de dados no Azure Machine Learning e importa dados diretamente de uma [datastore](concept-data.md#datastores) ou URL HTTP.

Para obter informações detalhadas sobre como utilizar o módulo de Dados de Importação, consulte a [página de referência de Dados de Importação](algorithm-module-reference/import-data.md).

> [!NOTE]
> Se o seu conjunto de dados tiver demasiadas colunas, poderá encontrar o seguinte erro: "A validação falhou devido à limitação de tamanho". Para evitar isto, [registe o conjunto de dados na interface Datasets](how-to-create-register-datasets.md#use-the-ui).

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

* Cadeia
* Integer (Número inteiro)
* Decimal
* Booleano
* Date

O designer usa um tipo de dados interno para passar dados entre módulos. Pode converter explicitamente os seus dados em formato de tabela de dados utilizando o módulo [Converte-se para Conjunto de Dados.](algorithm-module-reference/convert-to-dataset.md) Qualquer módulo que aceite formatos diferentes do formato interno converterá os dados silenciosamente antes de os passar para o módulo seguinte.

## <a name="data-constraints"></a>Restrições de dados

Os módulos no designer são limitados pelo tamanho do alvo do cálculo. Para conjuntos de dados maiores, deve utilizar um recurso de computação Azure Machine Learning maior. Para obter mais informações sobre o cálculo Azure Machine Learning, veja [quais são os alvos de computação em Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="access-data-in-a-virtual-network"></a>Aceder a dados numa rede virtual

Se o seu espaço de trabalho estiver numa rede virtual, deve executar etapas de configuração adicionais para visualizar dados no designer. Para obter mais informações sobre como utilizar datastores e conjuntos de dados numa rede virtual, consulte [o isolamento da Rede durante o treino & inferência com redes virtuais privadas](how-to-enable-virtual-network.md#machine-learning-studio).

## <a name="next-steps"></a>Passos seguintes

Aprenda o básico do designer com [Tutorial: Predict automobile price with the designer.](tutorial-designer-automobile-price-train-score.md)
