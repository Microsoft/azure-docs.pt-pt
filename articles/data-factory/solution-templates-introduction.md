---
title: Descrição geral dos modelos
description: Aprenda a usar um modelo pré-definido para começar rapidamente com a Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: 14f1d3b58996d438279bfa2a234c754a6d840c79
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414737"
---
# <a name="templates"></a>Modelos
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Os modelos são oleodutos pré-definidos da Fábrica de Dados Azure que permitem começar rapidamente com a Data Factory. Os modelos são úteis quando se é novo na Data Factory e quer começar rapidamente. Estes modelos reduzem o tempo de desenvolvimento para a construção de projetos de integração de dados, melhorando assim a produtividade do desenvolvedor.

## <a name="create-data-factory-pipelines-from-templates"></a>Criar oleodutos data factory a partir de modelos

Você pode começar a criar um pipeline Data Factory a partir de um modelo nas seguintes duas maneiras:

1.  Selecione **Criar o pipeline a partir do modelo** na página overview para abrir a galeria do modelo.

    ![Abra a galeria do modelo a partir da página de visão geral](media/solution-templates-introduction/templates-intro-image1.png)

1.  No separador Autor no **+** Explorador de Recursos, selecione, em seguida, **Pipeline do modelo** para abrir a galeria do modelo.

    ![Abra a galeria do modelo a partir do separador Autor](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galeria do Modelo

![A galeria do modelo](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Fora da caixa modelos de fábrica de dados

Data Factory usa modelos do Gestor de Recursos Azure para salvar modelos de gasodutos de fábrica de dados. Pode ver todos os modelos do Gestor de Recursos, juntamente com o ficheiro manifesto utilizado para fora dos modelos da fábrica de dados da caixa, no repo oficial da Fábrica de [Dados do Azure.](https://github.com/Azure/Azure-DataFactory/tree/master/templates) Os modelos pré-definidos fornecidos pela Microsoft incluem, mas não se limitam aos seguintes itens:

-   Modelos de cópia:

    -   [Cópia a granel da Base de Dados](solution-template-bulk-copy-with-control-table.md)
    
    -   [Copiar novos ficheiros por DataÚltimaModificação](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Copiar vários contentores de ficheiros entre lojas baseadas em ficheiros](solution-template-copy-files-multiple-containers.md)

    -   [Mover ficheiros](solution-template-move-files.md)

    -   [Cópia delta da Base de Dados](solution-template-delta-copy-with-control-table.md)

    -   Cópia \<de\> \<fonte para destino\>

        -   [Da Amazon S3 à Azure Data Lake Store Gen 2](solution-template-migration-s3-azure.md)

        -   Do Google Big Query à Azure Data Lake Store Gen 2

        -   De HDF a Azure Data Lake Store Gen 2

        -   De Netezza a Azure Data Lake Store Gen 1

        -   Do Servidor SQL nas instalações até à Base de Dados Azure SQL

        -   Do Servidor SQL nas instalações do Armazém de Dados Azure SQL

        -   Da Oracle nas instalações até ao Armazém de Dados Azure SQL

-   Modelos SSIS

    -   AgendaR Tempo de Integração Azure-SSIS para executar pacotes SSIS

-   Transformar modelos

    -   [ETL com tijolos de dados Azure](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Meus modelos

Também pode guardar um pipeline como modelo selecionando **Guardar como modelo** no separador Pipeline.

![Guarde um oleoduto como modelo](media/solution-templates-introduction/templates-intro-image4.png)

Pode ver os oleodutos guardados como modelos na secção **My Templates** da Galeria do Modelo. Também pode vê-los na secção **Modelos** no Explorador de Recursos.

![Meus modelos](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Para utilizar a funcionalidade My Templates, tem de permitir a integração do GIT. Tanto o Azure DevOps GIT como o GitHub são apoiados.
