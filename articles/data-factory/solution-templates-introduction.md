---
title: Visão geral dos modelos para Azure Data Factory | Microsoft Docs
description: Saiba como usar um modelo predefinido para começar rapidamente com o Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: eb7a7eb8e1bdacae4b74e3a0019a376c440fe4d5
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091966"
---
# <a name="templates"></a>Modelos

Os modelos são predefinidos Azure Data Factory pipelines que permitem que você comece rapidamente com Data Factory. Os modelos são úteis quando você é novo no Data Factory e deseja começar rapidamente. Esses modelos reduzem o tempo de desenvolvimento para a criação de projetos de integração de dados, aumentando assim a produtividade do desenvolvedor.

## <a name="create-data-factory-pipelines-from-templates"></a>Criar pipelines de Data Factory de modelos

Você pode começar a criar um pipeline de Data Factory de um modelo das duas maneiras a seguir:

1.  Selecione **criar pipeline de modelo** na página Visão geral para abrir a Galeria de modelos.

    ![Abra a Galeria de modelos na página Visão geral](media/solution-templates-introduction/templates-intro-image1.png)

1.  Na guia autor do Gerenciador de recursos, selecione **+** e, em seguida, **pipeline de modelo** para abrir a Galeria de modelos.

    ![Abrir a Galeria de modelos na guia autor](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galeria de Modelos

![A Galeria de modelos](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Modelos Data Factory prontos para uso

Data Factory usa modelos de Azure Resource Manager para salvar data factory modelos de pipeline. Você pode ver todos os modelos do Resource Manager, juntamente com o arquivo de manifesto usado para modelos de Data Factory prontos, no [repositório do GitHub oficial Azure data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/templates). Os modelos predefinidos fornecidos pela Microsoft incluem, mas não estão limitados aos seguintes itens:

-   Copiar modelos:

    -   [Cópia em massa do banco de dados](solution-template-bulk-copy-with-control-table.md)
    
    -   [Copiar novos arquivos por LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Copiar vários contêineres de arquivos entre repositórios baseados em arquivo](solution-template-copy-files-multiple-containers.md)

    -   [Mover arquivos](solution-template-move-files.md)

    -   [Cópia Delta do banco de dados](solution-template-delta-copy-with-control-table.md)

    -   Copiar da \<origem\> para \<o destino\>

        -   [Do Amazon S3 para Azure Data Lake Store Gen 2](solution-template-migration-s3-azure.md)

        -   Do Google Big Query para Azure Data Lake Store Gen 2

        -   De HDF para Azure Data Lake Store Gen 2

        -   De Netezza para Azure Data Lake Store Gen 1

        -   De SQL Server local para o banco de dados SQL do Azure

        -   De SQL Server local para o Azure SQL Data Warehouse

        -   Do Oracle local para o Azure SQL Data Warehouse

-   Modelos SSIS

    -   Agendar Integration Runtime do Azure-SSIS para executar pacotes do SSIS

-   Transformar modelos

    -   [ETL com Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Meus modelos

Você também pode salvar um pipeline como um modelo selecionando **salvar como modelo** na guia pipeline.

![Salvar um pipeline como um modelo](media/solution-templates-introduction/templates-intro-image4.png)

Você pode exibir os pipelines salvos como modelos na seção **meus modelos** da Galeria de modelos. Você também pode vê-los na seção **modelos** no Gerenciador de recursos.

![Meus modelos](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Para usar o recurso meus modelos, você precisa habilitar a integração do GIT. Há suporte para o Azure DevOps GIT e o GitHub.
