---
title: Descrição geral dos modelos
description: Aprenda a usar um modelo pré-definido para começar rapidamente com a Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: 8c0e4db2bc686fff2bd718f45c63a0fc26f6cd55
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375396"
---
# <a name="templates"></a>Modelos

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Os modelos são oleodutos Azure Data Factory predefinidos que permitem começar rapidamente com a Data Factory. Os modelos são úteis quando se é novo na Data Factory e querem começar rapidamente. Estes modelos reduzem o tempo de desenvolvimento para a construção de projetos de integração de dados, melhorando assim a produtividade dos desenvolvedores.

## <a name="create-data-factory-pipelines-from-templates"></a>Criar oleodutos de fábrica de dados a partir de modelos

Pode começar a criar um pipeline data factory a partir de um modelo de duas maneiras:

1.  Selecione **Criar o pipeline a partir do modelo** na página 'Vista Geral' para abrir a galeria do modelo.

    ![Abra a galeria de modelos a partir da página do visão geral](media/solution-templates-introduction/templates-intro-image1.png)

1.  No separador Autor no Explorador de Recursos, selecione, **+** em seguida, **Pipeline do modelo** para abrir a galeria do modelo.

    ![Abra a galeria de modelos a partir do separador Autor](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galeria de Modelos

![A galeria de modelos](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Fora da caixa modelos de fábrica de dados

A Data Factory utiliza modelos do Gestor de Recursos Azure para guardar modelos de gasodutos de fábrica de dados. Pode ver todos os modelos do Gestor de Recursos, juntamente com o ficheiro manifesto utilizado para fora dos modelos de Data Factory da caixa, no repo oficial da Fábrica de [Dados Azure.](https://github.com/Azure/Azure-DataFactory/tree/master/templates) Os modelos predefinidos fornecidos pela Microsoft incluem, mas não se limitam aos seguintes itens:

-   Modelos de cópia:

    -   [Cópia a granel da Base de Dados](solution-template-bulk-copy-with-control-table.md)
    
    -   [Copiar novos ficheiros por DataÚltimaModificação](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Copie vários contentores de ficheiros entre lojas baseadas em ficheiros](solution-template-copy-files-multiple-containers.md)

    -   [Mover ficheiros](solution-template-move-files.md)

    -   [Cópia delta da Base de Dados](solution-template-delta-copy-with-control-table.md)

    -   Cópia \<source\> de \<destination\>

        -   [Da Amazon S3 à Azure Data Lake Store Gen 2](solution-template-migration-s3-azure.md)

        -   Do Google Big Query à Azure Data Lake Store Gen 2

        -   De HDF a Azure Data Lake Store Gen 2

        -   De Netezza a Azure Data Lake Store Gen 1

        -   Do SQL Server nas instalações até à Base de Dados Azure SQL

        -   Do SQL Server nas instalações para a Azure Synapse Analytics

        -   Da Oráculo nas instalações para a Azure Synapse Analytics

-   Modelos SSIS

    -   Agendar o tempo de execução da integração Azure-SSIS para executar pacotes SSIS

-   Modelos de transformação

    -   [ETL com Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Meus modelos

Também pode guardar um pipeline como modelo selecionando **Guardar como modelo** no separador Pipeline.

![Guarde um oleoduto como modelo](media/solution-templates-introduction/templates-intro-image4.png)

Pode ver os oleodutos guardados como modelos na secção **My Templates** da Galeria do Modelo. Também pode vê-los na secção **Modelos** no Explorador de Recursos.

![Meus modelos](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Para utilizar a funcionalidade My Templates, tem de ativar a integração do GIT. Tanto a Azure DevOps GIT como o GitHub são apoiados.
