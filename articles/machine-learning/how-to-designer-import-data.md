---
title: Importar dados
titleSuffix: Azure Machine Learning
description: Saiba como importar seus dados para o Azure Machine Learning designer de várias fontes de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: 421f1f733e64daaa4d87ce3c5af5126a48a8ffa5
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170891"
---
# <a name="import-your-data-into-azure-machine-learning-designer-preview"></a>Importar seus dados para o designer de Azure Machine Learning (versão prévia)

Neste artigo, você aprenderá a importar seus próprios dados no designer para criar soluções personalizadas. Há duas maneiras de importar dados para o designer: 

* **Azure Machine Learning conjuntos** de dados-registrar [conjuntos](concept-data.md#datasets) de dados no Azure Machine Learning para habilitar recursos avançados que o ajudarão a gerenciá-los.
* **Importar módulo de dados** – use o módulo [importar dados](algorithm-module-reference/import-data.md) para acessar diretamente os dados de fontes de dados online.

## <a name="use-azure-machine-learning-datasets"></a>Usar conjuntos de Azure Machine Learning

É recomendável que você use [conjuntos](concept-data.md#datasets) de dados para importar o designer. Quando você registra um conjunto de dados, você pode aproveitar ao máximo os recursos de recurso avançados, como [controle de versão e](how-to-version-track-datasets.md) [monitoramento de dados](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Registrar um conjunto de uma

Você pode registrar conjuntos de registros existentes [programaticamente com o SDK](how-to-create-register-datasets.md#use-the-sdk) ou [visualmente no Azure Machine Learning Studio](how-to-create-register-datasets.md#use-the-ui).

Você também pode registrar a saída para qualquer módulo do designer como um conjunto de um.

1. Selecione o módulo que gera os dados que você deseja registrar.

1. No painel Propriedades, selecione **saídas** > **conjunto de registros**.

    ![Captura de tela mostrando como navegar para a opção Registrar conjunto de registros](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Usar um conjunto de uma

Seus conjuntos de valores registrados podem ser encontrados na paleta de módulos, em **conjuntos** de > **meus conjuntos de valores**. Para usar um conjunto de um, arraste-o e solte-o na tela do pipeline. Em seguida, conecte a porta de saída do conjunto de resultados a outros módulos na paleta.

![Captura de tela mostrando o local dos conjuntos de itens salvos na paleta do designer](media/how-to-designer-import-data/use-datasets-designer.png)

Qualquer [conjunto](how-to-create-register-datasets.md#dataset-types) de informações de arquivo registrado no seu espaço de trabalho do Machine Learning será exibido na paleta de módulos. Você não está limitado ao uso de conjuntos de valores criados no designer.

> [!NOTE]
> Atualmente, o designer dá suporte apenas ao processamento de [conjuntos de tabelas de tabela](how-to-create-register-datasets.md#dataset-types). Se você quiser usar [conjuntos de arquivos de arquivo](how-to-create-register-datasets.md#dataset-types), use o SDK do Azure Machine Learning disponível para Python e R.

## <a name="import-data-using-the-import-data-module"></a>Importar dados usando o módulo importar dados

Embora seja recomendável usar os conjuntos de dados para importar, você também pode usar o módulo [importar dados](algorithm-module-reference/import-data.md) . O módulo importar dados ignora o registro de seu conjunto em Azure Machine Learning e importa os dados diretamente de um [repositório de armazenamento](concept-data.md#datastores) ou URL http.

Para obter informações detalhadas sobre como usar o módulo importar dados, consulte a [página Importar dados referência](algorithm-module-reference/import-data.md).


## <a name="supported-sources"></a>Fontes com suporte

Esta seção lista as fontes de dados com suporte pelo designer. Os dados entram no designer de um datastore ou de um [DataSet tabular](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Fontes de repositório de armazenamento
Para obter uma lista de fontes de armazenamento de dados com suporte, confira [acessar os recursos nos serviços de armazenamento do Azure](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Fontes de conjunto de tabelas de tabela

O designer dá suporte a conjuntos de tabelas de tabela criados a partir das seguintes fontes:
 * Arquivos delimitados
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

O designer usa um tipo de dados interno para passar dados entre módulos. Você pode converter explicitamente seus dados em formato de tabela de dados usando o módulo [converter para conjunto de](algorithm-module-reference/convert-to-dataset.md) dados. Qualquer módulo que aceite formatos diferentes do formato interno converterá os dados silenciosamente antes de passá-los para o próximo módulo.

## <a name="data-constraints"></a>Restrições de dados

Os módulos no designer são limitados pelo tamanho do destino de computação. Para conjuntos de grandes maiores, você deve usar um recurso de computação Azure Machine Learning maior. Para obter mais informações sobre Azure Machine Learning computação, consulte [o que são destinos de computação no Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="next-steps"></a>Passos seguintes

Aprenda as noções básicas do designer com [o tutorial: prever o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md).