---
title: Resolução de problemas de implementações
description: Saiba como monitorizar e resolver problemas as implementações do modelo do Gestor de Recursos Azure (modelo ARM). Mostra registos de atividade e histórico de implantação.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 31c4e6383b5eaea2bb66dc1baafa0fbff4918a7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97589122"
---
# <a name="tutorial-troubleshoot-arm-template-deployments"></a>Tutorial: Implementações de modelos de braço de resolução de problemas

Saiba como resolver os erros de implementação do modelo do Gestor de Recursos Azure (modelo ARM). Neste tutorial, você configura dois erros em um modelo, e aprender a usar os registos de atividade e histórico de implementação para resolver os problemas.

Existem dois tipos de erros que estão relacionados com a implementação do modelo:

- **Os erros** de validação surgem de cenários que podem ser determinados antes da implementação. Estes incluem erros de sintaxe no modelo ou tentativas de implementação de recursos que excederiam as quotas de subscrição.
- **Os erros** de implantação surgem de condições que ocorrem durante o processo de implantação. Estes incluem a tentativa de acesso a um recurso que está a ser implementado em paralelo.

Ambos os tipos de erros devolvem um código de erro que deverá utilizar para resolver os problemas da implementação. Ambos os tipos de erros são apresentados no registo de atividades. No entanto, os erros de validação não aparecem no histórico de implementação porque a implementação não chegou a ser iniciada.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> - Criar um modelo problemático
> - Erros de validação de resolução de problemas
> - Resolver erros de implementação
> - Limpar os recursos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

- Visual Studio Code com extensão Ferramentas do Resource Manager. Consulte [Quickstart: Crie modelos ARM com Código de Estúdio Visual](quickstart-create-templates-use-visual-studio-code.md).

## <a name="create-a-problematic-template"></a>Criar um modelo problemático

Abra um modelo chamado Criar uma conta de [armazenamento padrão](https://azure.microsoft.com/resources/templates/101-storage-account-create/) a partir de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/), e configurar dois problemas de modelo.

1. A partir do Código do Estúdio Visual, selecione Ficheiro Aberto **de**  >  **Ficheiros**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Selecione **Abrir** para abrir o ficheiro.
4. Altere a `apiVersion` linha para a seguinte linha:

    ```json
    "apiVersion1": "2018-07-02",
    ```

    - `apiVersion1` é um nome de elemento inválido. É um erro de validação.
    - A versão API deve ser `"2018-07-01"` .  É um erro de implantação.

5. Selecione   >  **'Guardar ficheiros' para** guardar o ficheiro à medida _queazuredeploy.jsno_ computador local.

## <a name="troubleshoot-the-validation-error"></a>Resolução de problemas do erro de validação

Consulte a secção [de modelo](template-tutorial-create-multiple-instances.md#deploy-the-template) para implementar o modelo.

Você receberá um erro da concha semelhante a:

```azurepowershell
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

A mensagem de erro indica que o problema está com `apiVersion1` .

Utilize o Código do Estúdio Visual para corrigir o problema alterando `apiVersion1` para `apiVersion` , e, em seguida, guarde o modelo.

## <a name="troubleshoot-the-deployment-error"></a>Resolver problemas no erro de implantação

Consulte a secção [de modelo](template-tutorial-create-multiple-instances.md#deploy-the-template) para implementar o modelo.

Você receberá um erro da concha semelhante a:

```azurepowershell
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

O erro de implantação pode ser encontrado a partir do portal Azure utilizando o seguinte procedimento:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o grupo de recursos selecionando **grupos de recursos** e, em seguida, o nome do grupo de recursos. Verá **1 Falhado** no âmbito **da implantação.**

    ![Screenshot que realça a implementação falhada.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Selecione **detalhes de erro**.

    ![Screenshot que realça a ligação de detalhes de Erro.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    A mensagem de erro é a mesma que foi mostrada anteriormente:

    ![Screenshot que mostra os detalhes do erro.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Também pode encontrar o erro a partir dos registos de atividade:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **log**  >  **de atividade do** monitor .
3. Utilize os filtros para encontrar o registo.

    ![Resolução de problemas tutoriais do Gestor de Recursos](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Utilize o Código do Estúdio Visual para corrigir o problema e, em seguida, reposicione o modelo.

Para obter uma lista de erros comuns, consulte [os erros comuns de implementação do Azure com o Azure Resource Manager](common-deployment-errors.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a resolver erros de implementação do modelo ARM.  Para obter mais informações, consulte [os erros comuns de implementação do Azure com o Azure Resource Manager](common-deployment-errors.md).
