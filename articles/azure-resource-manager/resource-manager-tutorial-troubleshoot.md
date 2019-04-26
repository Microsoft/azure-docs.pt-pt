---
title: Resolver problemas de implementações do Resource Manager | Documentos da Microsoft
description: Saiba como monitorizar e resolver problemas de implementações do Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c889c3123160680d96889227d6964ff197dc41cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60388647"
---
# <a name="tutorial-troubleshoot-resource-manager-template-deployments"></a>Tutorial: Resolver problemas de implementações de modelo do Resource Manager

Saiba como resolver erros de implementação de modelo do Resource Manager. Neste tutorial, configurar dois erros num modelo e saiba como utilizar os registos de atividade e o histórico de implementações para resolver os problemas.

Existem dois tipos de erros relacionados com a implementação do modelo:

- **Erros de validação** resultam de cenários que podem ser determinados antes da implantação. Eles incluem erros de sintaxe no seu modelo, ou tentar implementar recursos que iria exceder as quotas de subscrição. 
- **Erros de implementação** resultam de condições que ocorrem durante o processo de implantação. Eles incluem a tentar aceder a um recurso que está a ser implementado em paralelo.

Ambos os tipos de erros de devolvem um código de erro que utilizar para resolver problemas relacionados com a implementação. Ambos os tipos de erros são apresentados no registo de atividades. No entanto, os erros de validação não aparecem no seu histórico de implementação porque nunca iniciou a implementação.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar um modelo problemático
> * Resolver erros de validação
> * Resolver erros de implementação
> * Limpar recursos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

- [Visual Studio Code](https://code.visualstudio.com/) com [extensão Ferramentas do Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="create-a-problematic-template"></a>Criar um modelo problemático

Abra um modelo chamado [criar uma conta de armazenamento standard](https://azure.microsoft.com/resources/templates/101-storage-account-create/) partir [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/)e dois problemas do modelo de configuração.

1. No Visual Studio Code, selecione **Ficheiro**>**Abrir Ficheiro**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Selecione **Abrir** para abrir o ficheiro.
4. Alteração da **apiVersion** linha para a seguinte linha:

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - **apiVersion1** é o nome de elemento inválido. É um erro de validação.
    - A versão de API deve ser "2018-07-01".  É um erro de implementação.

5. Selecione **Ficheiro**>**Guardar Como** para guardar o ficheiro como **azuredeploy.json** no computador local.

## <a name="troubleshoot-the-validation-error"></a>Resolver problemas relacionados com o erro de validação

Consulte a [implementar o modelo](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) secção para implementar o modelo.

Deve receber um erro a partir da shell semelhante a:

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

A mensagem de erro indica que o problema é com **apiVersion1**.

Utilizar o Visual Studio Code para corrigir o problema, alterando **apiVersion1** ao **apiVersion**e, em seguida, guarde o modelo.

## <a name="troubleshoot-the-deployment-error"></a>Resolver problemas relacionados com o erro de implementação

Consulte a [implementar o modelo](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) secção para implementar o modelo.

Deve receber um erro a partir da shell semelhante a:

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

Pode encontrar o erro de implementação do portal do Azure utilizando o seguinte procedimento:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o grupo de recursos, selecionando **grupos de recursos** e, em seguida, o nome do grupo de recursos. Deverá ver **1 Falha** sob **implementação**.

    ![Resolver problemas de tutorial do Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Selecione **detalhes do erro**.

    ![Resolver problemas de tutorial do Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    A mensagem de erro é igual à mostrada anteriormente:

    ![Resolver problemas de tutorial do Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Também pode encontrar o erro dos registos de atividade:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Monitor** > **registo de atividades**.
3. Utilize os filtros para encontrar o registo.

    ![Resolver problemas de tutorial do Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Utilizar o Visual Studio Code para corrigir o problema e, em seguida, voltar a implementar o modelo.

Para obter uma lista dos erros comuns, consulte [resolver erros comuns de implementação do Azure com o Azure Resource Manager](./resource-manager-common-deployment-errors.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a resolver erros de implementação de modelo do Resource Manager.  Para obter mais informações, consulte [resolver erros comuns de implementação do Azure com o Azure Resource Manager](./resource-manager-common-deployment-errors.md).
