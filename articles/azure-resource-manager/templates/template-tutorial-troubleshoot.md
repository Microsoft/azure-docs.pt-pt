---
title: Resolução de problemas de implementações
description: Saiba como monitorar e solucionar problemas Azure Resource Manager implantações de modelo. Mostra os logs de atividade e o histórico de implantação.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ab12b206d5bb82956a8b607368af44ea0eaca40e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472514"
---
# <a name="tutorial-troubleshoot-resource-manager-template-deployments"></a>Tutorial: solucionar problemas de implantações de modelo do Resource Manager

Saiba como solucionar problemas de erros de implantação de modelo do Resource Manager. Neste tutorial, você configura dois erros em um modelo e aprende a usar os logs de atividade e o histórico de implantação para resolver os problemas.

Há dois tipos de erros relacionados à implantação de modelo:

- Os **erros de validação** surgem de cenários que podem ser determinados antes da implantação. Estes incluem erros de sintaxe no modelo ou tentativas de implementação de recursos que excederiam as quotas de subscrição.
- Os **erros de implantação** surgem de condições que ocorrem durante o processo de implantação. Estes incluem a tentativa de acesso a um recurso que está a ser implementado em paralelo.

Ambos os tipos de erros devolvem um código de erro que deverá utilizar para resolver os problemas da implementação. Os dois tipos de erros aparecem no log de atividades. No entanto, os erros de validação não aparecem no histórico de implementação porque a implementação não chegou a ser iniciada.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar um modelo problemático
> * Solucionar erros de validação
> * Resolver erros de implementação
> * Limpar recursos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com a extensão de ferramentas do Resource Manager. Consulte [usar Visual Studio Code para criar modelos de Azure Resource Manager](use-vs-code-to-create-template.md).

## <a name="create-a-problematic-template"></a>Criar um modelo problemático

Abra um modelo chamado [criar uma conta de armazenamento padrão](https://azure.microsoft.com/resources/templates/101-storage-account-create/) nos [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/)e configure dois problemas de modelo.

1. No Visual Studio Code, selecione **Ficheiro**>**Abrir Ficheiro**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Selecione **Abrir** para abrir o ficheiro.
4. Altere a linha **apiVersion** para a seguinte linha:

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - **apiVersion1** é um nome de elemento inválido. É um erro de validação.
    - A versão da API deve ser "2018-07-01".  É um erro de implantação.

5. Selecione **Ficheiro**>**Guardar Como** para guardar o ficheiro como **azuredeploy.json** no computador local.

## <a name="troubleshoot-the-validation-error"></a>Solucionar o erro de validação

Consulte a seção [implantar o modelo](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) para implantar o modelo.

Você deverá receber um erro do Shell semelhante a:

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

A mensagem de erro indica que o problema é com **apiVersion1**.

Use Visual Studio Code para corrigir o problema alterando **apiVersion1** para **apiVersion**e, em seguida, salve o modelo.

## <a name="troubleshoot-the-deployment-error"></a>Solucionar problemas de erro de implantação

Consulte a seção [implantar o modelo](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) para implantar o modelo.

Você deverá receber um erro do Shell semelhante a:

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

O erro de implantação pode ser encontrado no portal do Azure usando o seguinte procedimento:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Abra o grupo de recursos selecionando **grupos de recursos** e o nome do grupo de recursos. Você deverá ver **1 com falha** na **implantação**.

    ![Solução de problemas do tutorial do Gerenciador de recursos](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Selecione os **detalhes do erro**.

    ![Solução de problemas do tutorial do Gerenciador de recursos](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    A mensagem de erro é igual à mostrada anteriormente:

    ![Solução de problemas do tutorial do Gerenciador de recursos](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Você também pode encontrar o erro nos logs de atividade:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **monitorar** > **log de atividades**.
3. Use os filtros para localizar o log.

    ![Solução de problemas do tutorial do Gerenciador de recursos](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Use Visual Studio Code para corrigir o problema e reimplante o modelo.

Para obter uma lista de erros comuns, consulte [solucionar erros comuns de implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a solucionar problemas de erros de implantação de modelo do Resource Manager.  Para obter mais informações, consulte [solucionar erros comuns de implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).
