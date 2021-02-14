---
title: Crie uma fábrica de dados Azure utilizando um modelo de gestor de recursos Azure (modelo ARM)
description: Crie uma amostra Azure Data Factory pipeline usando um modelo de Gestor de Recursos Azure (modelo ARM).
ms.service: data-factory
tags: azure-resource-manager
author: dcstwh
ms.author: weetok
ms.reviewer: maghan, jingwang
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/16/2020
ms.openlocfilehash: f3e76bf16b702adb26183209d36189a53b695c40
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373849"
---
# <a name="quickstart-create-an-azure-data-factory-using-arm-template"></a>Quickstart: Criar uma fábrica de dados Azure usando o modelo ARM

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Versão atual](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este quickstart descreve como usar um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma fábrica de dados Azure. O pipeline que criar nesta fábrica de dados **copia** dados de uma pasta para outra pasta num armazenamento de blobs do Azure. Para ter acesso a um tutorial sobre como **transformar** dados com o Azure Data Factory, veja [Tutorial: Transformar dados com o Spark](transform-data-using-spark.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

> [!NOTE]
> Este artigo não disponibiliza uma introdução detalhada do serviço Data Factory. Para obter uma introdução ao serviço Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-subscription"></a>Subscrição do Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="create-a-file"></a>Criar um ficheiro

Abra um editor de texto como **o Notepad,** e crie um ficheiro nomeado **emp.txt** com o seguinte conteúdo:

```emp.txt
John, Doe
Jane, Doe
```

Guarde o ficheiro na pasta **C:\ADFv2QuickStartPSH.** (Se a pasta já não existir, crie-a.)

## <a name="review-template"></a>Modelo de revisão

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-data-factory-v2-blob-to-blob-copy/).

:::code language="json" source="~/quickstart-templates/101-data-factory-v2-blob-to-blob-copy/azuredeploy.json":::

Existem recursos Azure definidos no modelo:

- [Microsoft.Storage/storageAstas:](/azure/templates/Microsoft.Storage/storageAccounts)Define uma conta de armazenamento.
- [Microsoft.DataFactory/fábricas](/azure/templates/microsoft.datafactory/factories): Criar uma Fábrica de Dados Azure.
- [Microsoft.DataFactory/fábricas/linkedServices](/azure/templates/microsoft.datafactory/factories/linkedservices): Criar um serviço ligado à Azure Data Factory.
- [Microsoft.DataFactory/fábricas/conjuntos de dados](/azure/templates/microsoft.datafactory/factories/datasets): Criar um conjunto de dados Azure Data Factory.
- [Microsoft.DataFactory/fábricas/oleodutos](/azure/templates/microsoft.datafactory/factories/pipelines): Criar um oleoduto Azure Data Factory.

Mais amostras de modelo de Azure Data Factory podem ser encontradas na [galeria de modelos quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria uma conta Azure Data Factory, uma conta de armazenamento e um recipiente blob.

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

2. Selecione ou introduza os seguintes valores.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-deploy-arm-template.png" alt-text="Implementar o modelo ADF ARM":::

    A menos que seja especificado, use os valores padrão para criar os recursos da Fábrica de Dados Azure:

    - **Assinatura**: Selecione uma subscrição Azure.
    - **Grupo de recursos**: Selecione **Criar novo,** insira um nome único para o grupo de recursos e, em seguida, selecione **OK**.
    - **Região**: Selecione uma localização.  Por exemplo, *Leste dos EUA.*
    - **Nome da fábrica de dados**: Utilize o valor predefinido.
    - **Localização**: Use o valor predefinido.
    - **Nome da conta de** armazenamento : Use o valor predefinido.
    - **Recipiente blob**: Utilize o valor predefinido.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

1. Selecione **Ir para o grupo de recursos**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-go-to-resource-group.png" alt-text="Grupo de Recursos":::

2.  Verifique se a sua Fábrica de Dados Azure foi criada.
    1. O nome da Fábrica de Dados Azure está no formato - datafactory \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-sample.png" alt-text="Fábrica de Dados de Amostra":::

2. Verifique se a sua conta de armazenamento foi criada.
    1. O nome da conta de armazenamento está no formato - armazenamento \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-storage-account.png" alt-text="Conta de Armazenamento":::

3. Selecione a conta de armazenamento criada e, em seguida, **selecione Recipientes**.
    1. Na página **Recipientes,** selecione o recipiente blob que criou.
        1. O nome do recipiente blob está no formato - blob \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-blob-container.png" alt-text="Recipiente blob":::

### <a name="upload-a-file"></a>Carregar um ficheiro

1. Na página **'Contentores',** **selecione Upload**.

2. No painel direito, selecione a caixa **'Ficheiros'** e, em seguida, navegue para e selecione o ficheiro **emp.txt** que criou anteriormente.

3. Expandir o rumo **avançado.**

4. Na caixa **de upload para pasta,** *introduza a entrada*.

5. Selecione o botão **Carregar**. Deverá ver o ficheiro **emp.txt** e o estado do carregamento na lista.

6. Selecione o ícone **Close** (um **X)** para fechar a página **de blob upload.**

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-upload-blob-file.png" alt-text="Carregar ficheiro para inserir pasta":::

Mantenha a página do recipiente aberta, pois pode usá-la para verificar a saída no final deste arranque rápido.

### <a name="start-trigger"></a>Iniciar gatilho

1. Navegue na página **de fábricas de dados** e selecione a fábrica de dados que criou. 

2. Selecione o **azulejo do Monitor &** autor. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author-monitor-tile.png" alt-text="Monitor & autor":::

2. Selecione o **separador Autor** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author.png" border="false"::: .

3. Selecione o gasoduto criado - ArmtemplateSampleCopyPipeline.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-pipelines.png" alt-text="Pipeline de modelo ARM":::

4. **Selecione Adicionar Gatilho**  >  **Agora**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-trigger-now.png" alt-text="Acionador":::

5. No painel direito sob **a tubagem,** selecione **OK**.

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

1. Selecione o **separador Monitor** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-monitor.png" border="false"::: .

2. Verá as execuções de atividade associadas à execução do pipeline. Neste início rápido, o pipeline só tem uma atividade do tipo Cópia. Como tal, vê-se uma corrida para essa atividade.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-successful-run.png" alt-text="Corrida bem sucedida":::

### <a name="verify-the-output-file"></a>Verifique o ficheiro de saída

O gasoduto cria automaticamente uma pasta de saída no recipiente blob. Em seguida, copia o ficheiro emp.txt da pasta de entrada para a pasta de saída. 

1. No portal Azure, na página **'Recipientes',** **selecione Refresh** para ver a pasta de saída. 

2. Selecione **a saída** na lista de pastas.

3. Confirme se o ficheiro **emp.txt** foi copiado para a pasta de saída. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-output.png" alt-text="Saída":::

## <a name="clean-up-resources"></a>Limpar os recursos

Pode limpar os recursos que criou no Guia de Introdução de duas formas. Pode [eliminar o grupo de recursos Azure,](../azure-resource-manager/management/delete-resource-group.md)que inclui todos os recursos do grupo de recursos. Se quiser manter os outros recursos intactos, elimine apenas a fábrica de dados que criou neste tutorial.

A eliminação de um grupo de recursos dita a eliminação de todos os recursos, incluindo as fábricas de dados nele incluídas. Execute o seguinte comando para eliminar todo o grupo de recursos: 

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Se pretender eliminar apenas a fábrica de dados e não todo o grupo de recursos, executar o seguinte comando: 

```azurepowershell-interactive
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma Fábrica de Dados Azure utilizando um modelo ARM e validou a implementação. Para saber mais sobre a Azure Data Factory e a Azure Resource Manager, continue nos artigos abaixo.

- [Documentação da Fábrica de Dados Azure](index.yml)
- Saiba mais sobre o [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Obtenha outros [modelos ARM da Fábrica de Dados Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)