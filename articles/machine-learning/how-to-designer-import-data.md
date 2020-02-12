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
ms.openlocfilehash: d41b0499df1a62440e8c1991e29f7963224a50d6
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138157"
---
# <a name="import-your-data-into-azure-machine-learning-designer-preview"></a>Importar seus dados para o designer de Azure Machine Learning (versão prévia)

Neste artigo, você aprenderá a importar seus próprios dados no designer para criar soluções personalizadas. Há duas maneiras de importar dados para o designer: 

* **Conjuntos de dados de Aprendizagem Automática Azure** - Registe [os conjuntos de dados](concept-data.md#datasets) no Azure Machine Learning para permitir funcionalidades avançadas que o ajudem a gerir os seus dados.
* **Módulo de Dados de Importação** - Utilize o módulo dados de [importação](algorithm-module-reference/import-data.md) para aceder diretamente a dados de fontes de dados online.

## <a name="use-azure-machine-learning-datasets"></a>Usar conjuntos de Azure Machine Learning

Recomendamos que utilize [conjuntos](concept-data.md#datasets) de dados para importar dados para o designer. Ao registar um conjunto de dados, pode tirar o máximo partido de funcionalidades avançadas de dados, como [verversão e rastreio](how-to-version-track-datasets.md) e [monitorização de dados](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Registrar um conjunto de uma

Pode registar os conjuntos de dados existentes [programaticamente com o SDK](how-to-create-register-datasets.md#use-the-sdk) ou [visualmente no estúdio Azure Machine Learning.](how-to-create-register-datasets.md#use-the-ui)

Você também pode registrar a saída para qualquer módulo do designer como um conjunto de um.

1. Selecione o módulo que gera os dados que você deseja registrar.

1. No painel de propriedades, selecione **Saídas** > **Registar conjunto**de dados .

    ![Captura de tela mostrando como navegar para a opção Registrar conjunto de registros](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Utilize um conjunto de dados

Os seus conjuntos de dados registados podem ser encontrados na paleta de módulos, em **conjuntos** de dados > **Meus Conjuntos**de Dados . Para usar um conjunto de um, arraste-o e solte-o na tela do pipeline. Em seguida, conecte a porta de saída do conjunto de resultados a outros módulos na paleta.

![Captura de tela mostrando o local dos conjuntos de itens salvos na paleta do designer](media/how-to-designer-import-data/use-datasets-designer.png)



> [!NOTE]
> Atualmente, o designer apenas suporta o processamento de conjuntos de [dados tabular.](how-to-create-register-datasets.md#dataset-types) Se pretender utilizar conjuntos de dados de [ficheiros,](how-to-create-register-datasets.md#dataset-types)utilize o SDK de Aprendizagem automática Azure disponível para Python e R.

## <a name="import-data-using-the-import-data-module"></a>Importar dados usando o módulo importar dados

Embora recomendemos que utilize conjuntos de dados para importar dados, também pode utilizar o módulo [de Dados de Importação.](algorithm-module-reference/import-data.md) O módulo de dados de importação ignora o registo do seu conjunto de dados no Azure Machine Learning e importa dados diretamente de uma loja de [dados](concept-data.md#datastores) ou URL HTTP.

Para obter informações detalhadas sobre como utilizar o módulo de dados de importação, consulte a página de [referência dos dados de importação](algorithm-module-reference/import-data.md).


## <a name="supported-sources"></a>Fontes com suporte

Esta seção lista as fontes de dados com suporte pelo designer. Os dados entram no designer a partir de uma datastore ou de conjunto de [dados tabular](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Fontes de repositório de armazenamento
Para obter uma lista de fontes suportadas de datastore, consulte os dados do Access nos serviços de [armazenamento do Azure.](how-to-access-data.md#supported-data-storage-service-types)

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
* decimal
* Booleano
* Data

O designer usa um tipo de dados interno para passar dados entre módulos. Pode converter explicitamente os seus dados em formato de tabela de dados utilizando o módulo [Converte para Dataset.](algorithm-module-reference/convert-to-dataset.md) Qualquer módulo que aceite formatos diferentes do formato interno converterá os dados silenciosamente antes de passá-los para o próximo módulo.

## <a name="data-constraints"></a>Restrições de dados

Os módulos no designer são limitados pelo tamanho do destino de computação. Para conjuntos de grandes maiores, você deve usar um recurso de computação Azure Machine Learning maior. Para obter mais informações sobre o computacional Azure Machine Learning, veja [quais são os alvos da computação no Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="next-steps"></a>Passos seguintes

Conheça o básico do designer com [tutorial: Preveja](tutorial-designer-automobile-price-train-score.md)o preço do automóvel com o designer.