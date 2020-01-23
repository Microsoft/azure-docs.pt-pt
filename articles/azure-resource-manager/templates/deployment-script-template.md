---
title: Usar scripts de implantação em modelos | Microsoft Docs
description: Use scripts de implantação em modelos de Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: jgao
ms.openlocfilehash: 125fefbb1d83db8b6114b2d09f5bd6da885159ba
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547647"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Usar scripts de implantação em modelos (visualização)

Saiba como usar scripts de implantação em modelos de recursos do Azure. Com um novo tipo de recurso chamado `Microsoft.Resources/deploymentScripts`, os usuários podem executar scripts de implantação em implantações de modelo e examinar os resultados da execução. Esses scripts podem ser usados para executar etapas personalizadas, como:

- Adicionar usuários a um diretório
- criar um registro de aplicativo
- executar operações do plano de dados, por exemplo, copiar BLOBs ou banco de dados de semente
- Pesquisar e validar uma chave de licença
- criar um certificado autoassinado
- criar um objeto no Azure AD
- Pesquisar blocos de endereços IP do sistema personalizado

Os benefícios do script de implantação:

- Fácil de codificar, usar e depurar. Você pode desenvolver scripts de implantação em seus ambientes de desenvolvimento favoritos. Os scripts podem ser inseridos em modelos ou em arquivos de script externos.
- Você pode especificar a linguagem de script e a plataforma. Atualmente, há suporte apenas para Azure PowerShell scripts de implantação no ambiente Linux.
- Permitir a especificação das identidades que são usadas para executar os scripts. Atualmente, há suporte apenas para [a identidade gerenciada atribuída pelo usuário do Azure](../../active-directory/managed-identities-azure-resources/overview.md) .
- Permita passar argumentos de linha de comando para o script.
- Pode especificar saídas de script e passá-las de volta para a implantação.

> [!NOTE]
> O script de implantação está atualmente em visualização. Para usá-lo, você deve [se inscrever para a versão prévia](https://aka.ms/armtemplatepreviews).

> [!IMPORTANT]
> Dois recursos de script de implantação, uma conta de armazenamento e uma instância de contêiner, são criados no mesmo grupo de recursos para execução de script e solução de problemas. Esses recursos geralmente são excluídos pelo serviço de script quando a execução do script de implantação entra em um estado terminal. Você será cobrado pelos recursos até que os recursos sejam excluídos. Para saber mais, consulte [limpar recursos de script de implantação](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Pré-requisitos

- **Uma identidade gerenciada atribuída pelo usuário com a função do colaborador no nível da assinatura**. Essa identidade é usada para executar scripts de implantação. Para criar um, consulte [criar uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), ou [usando CLI do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md), ou [usando Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Você precisa da ID de identidade ao implantar o modelo. O formato da identidade é:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Use o seguinte script do PowerShell para obter a ID fornecendo o nome do grupo de recursos e o nome da identidade.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

- **Azure PowerShell versão 2.7.0, 2.8.0 ou 3.0.0**. Você não precisa dessas versões para implantar modelos. Mas essas versões são necessárias para testar os scripts de implantação localmente. Consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). Você pode usar uma imagem pré-configurada do Docker.  Consulte [Configurar ambiente de desenvolvimento](#configure-development-environment).

## <a name="resource-schema"></a>Esquema de recursos

O JSON a seguir é um exemplo.  O esquema de modelo mais recente pode ser encontrado [aqui](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "myDeploymentScript",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "2.8",
    "arguments": "[concat('-name ', parameters('name'))]",
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ",
    "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> O exemplo é para fins de demonstração.  **scriptContent** e **primaryScriptUris** não podem coexistir em um modelo.

Detalhes do valor da propriedade:

- **Identidade**: o serviço de script de implantação usa uma identidade gerenciada atribuída pelo usuário para executar os scripts. Atualmente, somente a identidade gerenciada atribuída pelo usuário tem suporte.
- **tipo**: especifique o tipo de script. Atualmente, há suporte apenas para Azure PowerShell script. O valor é **AzurePowerShell**.
- **forceUpdateTag**: a alteração desse valor entre implantações de modelo força o script de implantação a ser executado novamente. Use a função newGuid () ou utcNow () que precisa ser definida como o defaultValue de um parâmetro. Para saber mais, veja [Executar script mais de uma vez](#run-script-more-than-once).
- **azPowerShellVersion**: Especifique a versão do módulo Azure PowerShell a ser usada. O script de implantação atualmente dá suporte à versão 2.7.0, 2.8.0 e 3.0.0.
- **argumentos**: Especifique os valores de parâmetro. Os valores são separados por espaços.
- **scriptContent**: especifique o conteúdo do script. Para executar um script externo, use `primaryScriptUri` em vez disso. Para obter exemplos, consulte [usar script embutido](#use-inline-scripts) e [usar script externo](#use-external-scripts).
- **primaryScriptUri**: especifique uma URL publicamente acessível para o script primário do PowerShell com a extensão de arquivo do PowerShell com suporte.
- **supportingScriptUris**: especifique uma matriz de URLs acessíveis publicamente para dar suporte a arquivos do PowerShell que serão chamados em `ScriptContent` ou `PrimaryScriptUri`.
- **tempo limite**: especifique o tempo de execução máximo permitido do script especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor padrão é **P1D**.
- **cleanupPreference**. Especifique a preferência de limpeza dos recursos de implantação quando a execução do script chegar em um estado de terminal. A configuração padrão é **sempre**, o que significa excluir os recursos, apesar do estado do terminal (êxito, falha, cancelado). Para saber mais, confira [limpar recursos de script de implantação](#clean-up-deployment-script-resources).
- **retentionInterval**: especifique o intervalo para o qual o serviço retém os recursos de script de implantação após a execução do script de implantação atingir um estado de terminal. Os recursos de script de implantação serão excluídos quando essa duração expirar. A duração é baseada no [padrão ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor padrão é **P1D**, que significa sete dias. Essa propriedade é usada quando cleanupPreference está definido como *Ontermination*. A propriedade *Onexpiretion* não está habilitada no momento. Para saber mais, confira [limpar recursos de script de implantação](#clean-up-deployment-script-resources).

## <a name="use-inline-scripts"></a>Usar scripts embutidos

O modelo a seguir tem um recurso definido com o tipo de `Microsoft.Resources/deploymentScripts`.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json?range=1-54)]

> [!NOTE]
> Como os scripts de implantação embutidos são colocados entre aspas duplas, as cadeias de caracteres dentro dos scripts de implantação precisam ser colocadas entre aspas simples em vez disso. O caractere de escape para o **&#92;** PowerShell é. Você também pode considerar o uso da substituição de cadeia de caracteres, como mostrado no exemplo de JSON anterior. Consulte o valor padrão do parâmetro Name.

O script usa um parâmetro e a saída do valor do parâmetro. **DeploymentScriptOutputs** é usado para armazenar saídas.  Na seção de saídas, a linha de **valor** mostra como acessar os valores armazenados. `Write-Output` é usado para fins de depuração. Para saber como acessar o arquivo de saída, consulte [depurar scripts de implantação](#debug-deployment-scripts).  Para obter as descrições de propriedade, consulte [esquema de recursos](#resource-schema).

Para executar o script, selecione **Experimente-** o para abrir o Cloud Shell e cole o código a seguir no painel Shell.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

O resultado tem o seguinte aspeto:

![Saída do script de implantação do modelo do Resource Manager Olá, mundo](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Usar scripts externos

Além dos scripts embutidos, você também pode usar arquivos de script externos. Atualmente, somente scripts do PowerShell com a extensão de arquivo **ps1** têm suporte. Para usar arquivos de script externo, substitua `scriptContent` por `primaryScriptUri`. Por exemplo:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Para ver um exemplo, selecione [aqui](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Os arquivos de script externo devem estar acessíveis.  Para proteger os arquivos de script armazenados nas contas de armazenamento do Azure, consulte [tutorial: artefatos seguros em implantações de modelo Azure Resource Manager](./template-tutorial-secure-artifacts.md).

## <a name="use-supporting-scripts"></a>Usar scripts de suporte

Você pode separar as lógicas complicadas em um ou mais arquivos de script de suporte. A propriedade `supportingScriptURI` permite que você forneça uma matriz de URIs para os arquivos de script de suporte, se necessário:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Os arquivos de script de suporte podem ser chamados de scripts embutidos e arquivos de script primários.

Os arquivos de suporte são copiados para azscripts/azscriptinput no tempo de execução. Use o caminho relativo para fazer referência aos arquivos de suporte de scripts embutidos e arquivos de script primários.

## <a name="work-with-outputs-from-deployment-scripts"></a>Trabalhar com saídas de scripts de implantação

O modelo a seguir mostra como passar valores entre dois recursos de deploymentScripts:

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic.json?range=1-84)]

No primeiro recurso, você define uma variável chamada **$DeploymentScriptOutputs**e a usa para armazenar os valores de saída. Para acessar o valor de saída de outro recurso dentro do modelo, use:

```json
reference('<ResourceName>').output.text
```

## <a name="debug-deployment-scripts"></a>Depurar scripts de implantação

O serviço de script cria uma [conta de armazenamento](../../storage/common/storage-account-overview.md) e uma instância de [contêiner](../../container-instances/container-instances-overview.md) para a execução do script. Ambos os recursos têm o sufixo **azscripts** nos nomes dos recursos.

![Nomes de recursos de script de implantação de modelo do Resource Manager](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

O script do usuário, os resultados da execução e o arquivo stdout são armazenados nos compartilhamentos de arquivos da conta de armazenamento. Há uma pasta chamada **azscripts**. Na pasta, há mais duas pastas para a entrada e os arquivos de saída: **azscriptinput** e **azscriptoutput**.

A pasta de saída contém um **ExecutionResult. JSON** e o arquivo de saída de script. Você pode ver a mensagem de erro de execução de script em **ExecutionResult. JSON**. O arquivo de saída é criado somente quando o script é executado com êxito. A pasta de entrada contém um arquivo de script do System PowerShell e os arquivos de script de implantação do usuário. Você pode substituir o arquivo de script de implantação do usuário por um revisado e executar novamente o script de implantação da instância de contêiner do Azure.

Você pode obter as informações de implantação de recurso de script de implantação no nível do grupo de recursos e no nível da assinatura usando a API REST:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

O exemplo a seguir usa [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

O resultado é semelhante a:

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-status.json?range=1-48)]

A saída mostra o estado de implantação e as IDs de recurso de script de implantação.

A API REST a seguir retorna o log:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Ele só funciona antes de os recursos de script de implantação serem excluídos.

Para ver o recurso deploymentScripts no portal, selecione **Mostrar tipos ocultos**:

![Script de implantação de modelo do Resource Manager, Mostrar tipos ocultos, portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Limpar recursos de script de implantação

O script de implantação cria uma conta de armazenamento e uma instância de contêiner que são usadas para executar scripts de implantação e armazenar informações de depuração. Esses dois recursos são criados no mesmo grupo de recursos que os recursos provisionados e serão excluídos pelo serviço de script quando o script expirar. Você pode controlar o ciclo de vida desses recursos.  Até que eles sejam excluídos, você será cobrado por ambos os recursos. Para obter as informações de preço, consulte [preços de instâncias de contêiner](https://azure.microsoft.com/pricing/details/container-instances/) e preços de [armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

O ciclo de vida desses recursos é controlado pelas seguintes propriedades no modelo:

- **cleanupPreference**: limpar a preferência quando a execução do script chegar em um estado de terminal.  Os valores com suporte são:

  - **Sempre**: exclua os recursos depois que a execução do script chegar em um estado de terminal. Como o recurso deploymentScripts ainda pode estar presente depois que os recursos são limpos, o script do sistema copiaria os resultados da execução do script, por exemplo, stdout, Outputs, valor de retorno, etc. para DB antes de os recursos serem excluídos.
  - **OnSuccess**: exclua os recursos somente quando a execução do script for bem-sucedida. Você ainda pode acessar os recursos para localizar as informações de depuração.
  - **Onexpiretion**: exclua os recursos somente quando a configuração **retentionInterval** estiver expirada. Esta propriedade está desabilitada no momento.

- **retentionInterval**: especifique o intervalo de tempo que um recurso de script será retido e depois o qual será expirado e excluído.

> [!NOTE]
> Não é recomendável usar os recursos de script de implantação para outras finalidades.

## <a name="run-script-more-than-once"></a>Executar script mais de uma vez

A execução do script de implantação é uma operação idempotente. Se nenhuma das propriedades de recurso deploymentScripts (incluindo o script embutido) for alterada, o script não será executado quando você reimplantar o modelo. O serviço de script de implantação compara os nomes de recursos no modelo com os recursos existentes no mesmo grupo de recursos. Há duas opções se você quiser executar o mesmo script de implantação várias vezes:

- Altere o nome do recurso deploymentScripts. Por exemplo, use a função de modelo [UtcNow](./template-functions-string.md#utcnow) como o nome do recurso ou como parte do nome do recurso. Alterar o nome do recurso cria um novo recurso deploymentScripts. É bom manter um histórico da execução do script.

    > [!NOTE]
    > A função utcNow só pode ser usada no valor padrão para um parâmetro.

- Especifique um valor diferente na Propriedade do modelo de `forceUpdateTag`.  Por exemplo, use utcNow como o valor.

> [!NOTE]
> Grave os scripts de implantação idempotentes. Isso garante que, se eles forem executados novamente acidentalmente, não causarão alterações no sistema. Por exemplo, se o script de implantação for usado para criar um recurso do Azure, verifique se o recurso não existe antes de criá-lo, para que o script seja bem sucedido ou você não crie o recurso novamente.

## <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

Atualmente, o script de implantação dá suporte à Azure PowerShell versão 2.7.0, 2.8.0 e 3.0.0.  Se você tiver um computador com Windows, poderá instalar uma das versões de Azure PowerShell com suporte e começar a desenvolver e testar os scripts de implantação.  Se você não tiver um computador Windows ou se não tiver uma dessas versões Azure PowerShell instaladas, poderá usar uma imagem de contêiner pré-configurada do Docker. O procedimento a seguir mostra como configurar a imagem do Docker no Windows. Para Linux e Mac, você pode encontrar as informações na Internet.

1. Instale o [Docker desktop](https://www.docker.com/products/docker-desktop) em seu computador de desenvolvimento.
1. Abra o Docker desktop.
1. Selecione o ícone de área de trabalho do Docker em barras de tarefas e, em seguida, selecione **configurações**.
1. Selecione **unidades compartilhadas**, selecione uma unidade local que você deseja disponibilizar para seus contêineres e, em seguida, selecione **aplicar**

    ![Unidade do Docker do script de implantação do modelo do Resource Manager](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Insira suas credenciais do Windows no prompt.
1. Abra uma janela de terminal, um prompt de comando ou o Windows PowerShell (não use o ISE do PowerShell).
1. Efetuar pull da imagem de contêiner do script de implantação para o computador local:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    O exemplo usa a versão 2.7.0.

1. Execute a imagem do Docker localmente.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Substitua **&lt;letra do driver de host >** e **&lt;nome do diretório de host >** com uma pasta existente na unidade compartilhada.  Ele mapeia a pasta para a pasta **/Data** no contêiner. Para obter exemplos, para mapear D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-isso** significa manter a imagem de contêiner ativa.

1. Selecione **compartilhá-lo** quando receber um prompt.
1. Execute um script do PowerShell, conforme mostrado na captura de tela a seguir (Considerando que você tem um arquivo HelloWorld. ps1 na pasta d:\docker)

    ![Gerenciador de script de implantação do modelo do Resource Manager cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Depois que o script do PowerShell é testado com êxito, você pode usá-lo como um script de implantação.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a usar os scripts de implantação. Para percorrer um tutorial de script de implantação:

> [!div class="nextstepaction"]
> [Tutorial: usar scripts de implantação em modelos de Azure Resource Manager](./template-tutorial-deployment-script.md)