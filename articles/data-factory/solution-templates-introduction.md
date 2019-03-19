---
title: Descrição geral dos modelos do Azure Data Factory | Documentos da Microsoft
description: Saiba como utilizar um modelo predefinido para se familiarizar rapidamente com o Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 4bd38991b2452bdda65a7647f844dcc17fdfb125
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003544"
---
# <a name="templates"></a>Modelos

Os modelos são predefinidas pipelines do Azure Data Factory que permitem que comece a trabalhar rapidamente com o Data Factory. Os modelos são úteis quando estiver familiarizado com o Data Factory e pretende começar a trabalhar rapidamente. Estes modelos reduzem o tempo de desenvolvimento para a criação de projetos de integração de dados, deste modo, melhorar produtividade do desenvolvedor.

## <a name="create-data-factory-pipelines-from-templates"></a>Criar pipelines do Data Factory a partir de modelos

Pode começar a criar um pipeline do Data Factory a partir de um modelo a seguir duas formas:

1.  Selecione **criar o pipeline de modelo** na página de descrição geral para abrir a Galeria de modelos.

    ![Abrir a Galeria de modelos a partir da página de descrição geral](media/solution-templates-introduction/templates-intro-image1.png)

1.  No separador de autor no Explorador de recursos, selecione **+**, em seguida, **Pipeline de modelo** para abrir a Galeria de modelos.

    ![Abra a Galeria de modelos da guia do autor](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galeria de Modelos

![Galeria de modelos](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Sem os modelos de fábrica de dados de caixa

O Data Factory utiliza modelos Azure Resource Manager para salvar os modelos de pipeline de fábrica de dados. Pode ver todos os modelos do Resource Manager, juntamente com o arquivo de manifesto utilizado para fora os modelos de fábrica de dados de caixa no [repositório do GitHub de fábrica de dados do Azure oficial](https://github.com/Azure/Azure-DataFactory/tree/master/templates). Os modelos predefinidos, fornecidos pela Microsoft incluem, mas não estão limitados aos seguintes itens:

-   Copie modelos:

    -   [Cópia em massa da base de dados](solution-template-bulk-copy-with-control-table.md)
    
    -   [Copiar novos ficheiros ao LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Copiar vários contêineres de arquivos entre arquivos baseados em ficheiros](solution-template-copy-files-multiple-containers.md)

    -   [Cópia do delta da base de dados](solution-template-delta-copy-with-control-table.md)

    -   Copiar a partir \<origem\> para \<destino\>

        -   Do Amazon S3 para o Azure Data Lake Store Ger 2

        -   Da consulta grande do Google para o Azure Data Lake Store Ger 2

        -   De HDF para o Azure Data Lake Store Ger 2

        -   De Netezza para o Azure Data Lake Store Ger 1

        -   Do SQL Server no local para a base de dados do Azure SQL

        -   Do SQL Server no local ao Azure SQL Data Warehouse

        -   Do Oracle no local ao Azure SQL Data Warehouse

-   Modelos SSIS

    -   Agendar o Runtime de integração Azure-SSIS executar pacotes SSIS

-   Transformar modelos

    -   [ETL com o Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Meus modelos

Também é possível salvar um pipeline como um modelo selecionando **guardar como modelo** no separador do Pipeline.

![Guardar um pipeline como um modelo](media/solution-templates-introduction/templates-intro-image4.png)

Pode ver pipelines guardados como modelos na **meus modelos** secção de Galeria de modelos. Também pode ver os mesmos no **modelos** secção no Explorador de recursos.

![Meus modelos](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Para utilizar a funcionalidade de meus modelos, terá de ativar a integração de GIT. GIT de DevOps do Azure e do GitHub são suportados.
