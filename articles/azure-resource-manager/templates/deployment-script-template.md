---
title: Use scripts de implementação em modelos | Microsoft Docs
description: utilizar scripts de implementação em modelos de Gestor de Recursos Azure.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: jgao
ms.openlocfilehash: c39b332e9ee62a8502d5e2fdf155819194a30e34
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762196"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>Use scripts de implementação em modelos ARM

Aprenda a usar scripts de implementação em modelos de recursos Azure (modelos ARM). Com um novo tipo de recurso chamado `Microsoft.Resources/deploymentScripts` , os utilizadores podem executar scripts em implementações de modelos e rever os resultados da execução. Estes scripts podem ser usados para executar etapas personalizadas, tais como:

- adicionar utilizadores a um diretório
- executar operações de plano de dados, por exemplo, bolhas de cópia ou base de dados de sementes
- olhar para cima e validar uma chave de licença
- criar um certificado auto-assinado
- criar um objeto em Azure AD
- procure os blocos de endereço IP do sistema personalizado

Os benefícios do script de implementação:

- Fácil de codificar, usar e depurar. Pode desenvolver scripts de implementação nos seus ambientes de desenvolvimento favoritos. Os scripts podem ser incorporados em modelos ou em ficheiros de script externos.
- Pode especificar a linguagem e a plataforma do script. Atualmente, os scripts de implementação Azure PowerShell e Azure CLI no ambiente Linux são suportados.
- Permitir passar argumentos de linha de comando para o script.
- Pode especificar as saídas do script e passá-las de volta para a implementação.

O recurso de script de implantação só está disponível nas regiões onde o Azure Container Instance está disponível.  Consulte [a disponibilidade de recursos para instâncias de contentores Azure nas regiões de Azure](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Uma conta de armazenamento e uma instância de contentor são necessárias para a execução do script e resolução de problemas. Tem as opções para especificar uma conta de armazenamento existente, caso contrário a conta de armazenamento juntamente com a instância do contentor são automaticamente criadas pelo serviço de scripts. Os dois recursos criados automaticamente são normalmente eliminados pelo serviço de scripts quando a execução do script de implementação entra num estado terminal. É cobrado pelos recursos até que os recursos sejam apagados. Para saber mais, consulte [os recursos do script de implementação de limpeza](#clean-up-deployment-script-resources).

> [!IMPORTANT]
> A versão API de recursos de implementação 2020-10-01 suporta [OnBehalfofTokens (OBO)](../../active-directory/develop/v2-oauth2-on-behalf-of-flow.md). Ao utilizar o OBO, o serviço de scripts de implementação utiliza o símbolo principal de implantação para criar os recursos subjacentes para executar scripts de implementação, que incluem a instância do Azure Container, a conta de armazenamento Azure e atribuições de funções para a identidade gerida. Na versão API mais antiga, a identidade gerida é usada para criar estes recursos.
> A lógica de relemissão para o sinal de Azure está agora incorporada no script do invólucro. Se conceder permissões no mesmo modelo onde executa scripts de implementação. As retríss em dia de serviço de script de implantação iniciaram o sessão durante 10 minutos com intervalo de 10 segundos até que a atribuição de função de identidade gerida seja replicada.

## <a name="configure-the-minimum-permissions"></a>Configure as permissões mínimas

Para a versão API de script de implementação 2020-10-01 ou posterior, o principal de implantação é utilizado para criar recursos subjacentes necessários para que o recurso de script de implementação execute - uma conta de armazenamento e uma instância de contentores Azure. Se o script precisar de autenticar para a Azure e executar ações específicas do Azure, recomendamos fornecer ao script uma identidade gerida atribuída pelo utilizador. A identidade gerida deve ter o acesso necessário para completar a operação no script.

Para configurar as permissões de menor privilégio, precisa:

- Atribuir uma função personalizada com as seguintes propriedades ao principal de implantação:

  ```json
  {
    "roleName": "deployment-script-minimum-privilege-for-deployment-principal",
    "description": "Configure least privilege for the deployment principal in deployment script",
    "type": "customRole",
    "IsCustom": true,
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/*",
          "Microsoft.ContainerInstance/containerGroups/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/deploymentScripts/*"
        ],
      }
    ],
    "assignableScopes": [
      "[subscription().id]"
    ]
  }
  ```

  Se os fornecedores de recursos Azure Storage e Azure Container Instance não tão bem registados, também é necessário adicionar `Microsoft.Storage/register/action` e `Microsoft.ContainerInstance/register/action` . .

- Se for utilizada uma identidade gerida, o principal de implantação necessita da função de Operador de **Identidade Gerida** (uma função incorporada) atribuída ao recurso de identidade gerido.

## <a name="sample-templates"></a>Modelos de exemplo

O seguinte JSON é um exemplo. Para mais informações, consulte o [esquema de modelo](/azure/templates/microsoft.resources/deploymentscripts)mais recente.

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2020-10-01",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": "1",
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80",
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "UserName",
        "value": "jdole"
      },
      {
        "name": "Password",
        "secureValue": "jDolePassword"
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}. The username is {1}, the password is {2}.' -f $name,${Env:UserName},${Env:Password}
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> O exemplo é para fins de demonstração. As propriedades `scriptContent` e `primaryScriptUri` não podem coexistir num modelo.

> [!NOTE]
> O _scriptContent_ mostra um script com várias linhas.  O portal Azure e o oleoduto Azure DevOps não podem analisar um script de implementação com várias linhas. Pode acorrentar os comandos PowerShell (utilizando pontos de sn ou _\\ r \\ n_ ou _\\ n)_ numa única linha, ou utilizar a `primaryScriptUri` propriedade com um ficheiro de script externo. Existem muitas ferramentas gratuitas de fuga/desacapagem da cadeia JSON disponíveis. Por exemplo, [https://www.freeformatter.com/json-escape.html](https://www.freeformatter.com/json-escape.html).

Detalhes do valor da propriedade:

- `identity`: Para a versão API de script de implementação 2020-10-01 ou posterior, uma identidade gerida atribuída ao utilizador é opcional, a menos que necessite de realizar quaisquer ações específicas do Azure no script.  Para a versão API 2019-10-01-pré-visualização, é necessária uma identidade gerida, uma vez que o serviço de scripts de implementação o utiliza para executar os scripts. Quando a propriedade de identidade é especificada, o serviço de script liga `Connect-AzAccount -Identity` antes de invocar o script do utilizador. Atualmente, apenas a identidade gerida atribuída pelo utilizador é suportada. Para iniciar sessão com uma identidade diferente, pode ligar para [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) no script.
- `kind`: Especificar o tipo de script. Atualmente, os scripts Azure PowerShell e Azure CLI são suportados. Os valores são **AzurePowerShell** e **AzureCLI**.
- `forceUpdateTag`: Alterar este valor entre as implementações do modelo obriga a re-executar o script de implantação. Se utilizar as `newGuid()` funções ou as `utcNow()` funções, ambas as funções só podem ser utilizadas no valor predefinido para um parâmetro. Para saber mais, consulte [o roteiro run mais de uma vez](#run-script-more-than-once).
- `containerSettings`: Especificar as definições para personalizar a instância do recipiente Azure. O script de implementação requer uma nova instância do contentor Azure. Não é possível especificar uma instância existente do contentor Azure. No entanto, pode personalizar o nome do grupo do recipiente utilizando `containerGroupName` . Se não for especificado, o nome do grupo é gerado automaticamente.
- `storageAccountSettings`: Especifique as definições para utilizar uma conta de armazenamento existente. Se `storageAccountName` não for especificada, uma conta de armazenamento é criada automaticamente. Ver [Utilizar uma conta de armazenamento existente.](#use-existing-storage-account)
- `azPowerShellVersion`/`azCliVersion`: Especifique a versão do módulo a utilizar. Consulte uma lista de [versões Azure PowerShell suportadas](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Consulte uma lista de [versões Azure CLI suportadas](https://mcr.microsoft.com/v2/azure-cli/tags/list).

  >[!IMPORTANT]
  > O script de implementação utiliza as imagens CLI disponíveis do Microsoft Container Registry (MCR). Leva cerca de um mês para certificar uma imagem CLI para o script de implementação. Não utilize as versões CLI que foram lançadas dentro de 30 dias. Para encontrar as datas de lançamento das imagens, consulte [as notas de lançamento do Azure CLI](/cli/azure/release-notes-azure-cli). Se for utilizada uma versão não suportada, a mensagem de erro lista as versões suportadas.

- `arguments`: Especificar os valores dos parâmetros. Os valores são separados por espaços.

  Os Scripts de Implementação dividem os argumentos numa série de cordas invocando a chamada do sistema [CommandLineToArgvW.](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) Este passo é necessário porque os argumentos são passados como uma propriedade de [comando](/rest/api/container-instances/containergroups/createorupdate#containerexec) para Azure Container Instance, e a propriedade de comando é uma matriz de cordas.

  Se os argumentos contiverem caracteres escapados, use [JsonEscaper](https://www.jsonescaper.com/) para escapar duas vezes dos personagens. Cole a sua corda original escapada na ferramenta e, em seguida, selecione **Escape**.  A ferramenta produz uma corda dupla escapada. Por exemplo, no modelo de amostra anterior, o argumento é `-name \"John Dole\"` . A corda escapada `-name \\\"John Dole\\\"` é.

  Para passar um parâmetro do modelo ARM do objeto do tipo como argumento, converta o objeto numa corda utilizando a função [de corda()](./template-functions-string.md#string) e, em seguida, utilize a função [de substituição para](./template-functions-string.md#replace) substituir qualquer um em `\"` `\\\"` . Por exemplo:

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  Para obter mais informações, consulte o modelo de [amostragem.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json)

- `environmentVariables`: Especifique as variáveis ambientais para passar para o script. Para obter mais informações, consulte [Desenvolver scripts de implementação.](#develop-deployment-scripts)
- `scriptContent`: Especificar o conteúdo do script. Para executar um script externo, use `primaryScriptUri` em vez disso. Por exemplo, consulte [o script inline](#use-inline-scripts) e [use script externo](#use-external-scripts).
- `primaryScriptUri`: Especifique um URL acessível ao público para o script de implantação primária com extensões de ficheiros suportadas. Para obter mais informações, consulte [utilizar scripts externos.](#use-external-scripts)
- `supportingScriptUris`: Especifique uma série de URLs acessíveis ao público para suportar ficheiros que são chamados em `scriptContent` ambos ou `primaryScriptUri` . Para obter mais informações, consulte [utilizar scripts externos.](#use-external-scripts)
- `timeout`: Especificar o tempo máximo de execução do script permitido especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor predefinido é **P1D.**
- `cleanupPreference`. Especifique a preferência de limpar os recursos de implantação quando a execução do script chegar a um estado terminal. A definição predefinida é **sempre**, o que significa eliminar os recursos apesar do estado terminal (Bem sucedido, Falhado, Cancelado). Para saber mais, consulte [Limpar os recursos do script de implementação](#clean-up-deployment-script-resources).
- `retentionInterval`: Especifique o intervalo para o qual o serviço mantém os recursos de script de implantação após a execução do script de implantação atingir um estado terminal. Os recursos do script de implantação serão eliminados quando esta duração expirar. A duração baseia-se no [padrão ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O intervalo de retenção é entre 1 e 26 horas (PT26H). Esta propriedade é usada quando `cleanupPreference` é definida para **OnExpiration**. Para saber mais, consulte [Limpar os recursos do script de implementação](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Amostras adicionais

- [Amostra 1](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json): crie um cofre-chave e utilize o script de implantação para atribuir um certificado ao cofre de chaves.
- [Amostra 2](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json): criar um grupo de recursos ao nível de subscrição, criar um cofre chave no grupo de recursos e, em seguida, usar o script de implantação para atribuir um certificado ao cofre de chaves.
- [Amostra 3](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json): criar uma identidade gerida atribuída pelo utilizador, atribuir a função de contribuinte à identidade ao nível do grupo de recursos, criar um cofre-chave e, em seguida, utilizar o script de implantação para atribuir um certificado ao cofre de chaves.

## <a name="use-inline-scripts"></a>Use scripts inline

O modelo a seguir tem um recurso definido com o `Microsoft.Resources/deploymentScripts` tipo. A parte realçada é o roteiro em linha.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> Como os scripts de implementação inline são incluídos em citações duplas, as cordas dentro dos scripts de implementação precisam de ser escapados usando um backslash **(&#92;**) ou incluídos em citações únicas. Também pode considerar a utilização da substituição de cordas tal como é mostrada na amostra JSON anterior.

O script requer um parâmetro e produz o valor do parâmetro. `DeploymentScriptOutputs` é utilizado para armazenar saídas. Na secção de saídas, a `value` linha mostra como aceder aos valores armazenados. `Write-Output` é usado para depurar propósito. Para aprender a aceder ao ficheiro de saída, consulte [os scripts de implementação de resolução de problemas do Monitor e da resolução de problemas](#monitor-and-troubleshoot-deployment-scripts). Para as descrições da propriedade, consulte [os modelos de amostra.](#sample-templates)

Para executar o script, selecione **Experimente-o** para abrir a Cloud Shell e, em seguida, cole o seguinte código no painel de conchas.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

O resultado tem o seguinte aspeto:

![Script de implementação de modelo de gestor de recursos olá saída do mundo](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Use scripts externos

Além dos scripts inline, também pode utilizar ficheiros de scripts externos. Apenas os scripts principais do PowerShell com a extensão do ficheiro _PS1_ são suportados. Para scripts CLI, os scripts primários podem ter quaisquer extensões (ou sem uma extensão), desde que os scripts sejam scripts de bash válidos. Para utilizar ficheiros de script externos, `scriptContent` substitua-o por `primaryScriptUri` . Por exemplo:

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Para obter mais informações, consulte o [modelo de exemplo.](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)

Os ficheiros de script externos devem estar acessíveis. Para proteger os seus ficheiros de scripts que são armazenados em contas de armazenamento Azure, gere um token SAS e inclua-o no URI para o modelo. Desavenda o prazo de validade para permitir tempo suficiente para completar a implantação. Para obter mais informações, consulte [implementar o modelo ARM privado com o token SAS](./secure-template-with-sas-token.md).

Você é responsável por garantir a integridade dos scripts que são referenciados por script de implementação, ou `primaryScriptUri` `supportingScriptUris` . Referência apenas scripts em que confia.

## <a name="use-supporting-scripts"></a>Use scripts de suporte

Pode separar lógicas complicadas em um ou mais ficheiros de scripts de suporte. A `supportingScriptUris` propriedade permite fornecer uma variedade de URIs para os ficheiros de script de suporte, se necessário:

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

Os ficheiros de scripts de suporte podem ser chamados a partir de scripts inline e ficheiros de scripts primários. Os ficheiros de scripts de suporte não têm restrições na extensão do ficheiro.

Os ficheiros de suporte são copiados `azscripts/azscriptinput` no tempo de funcionamento. Utilize um caminho relativo para fazer referência aos ficheiros de suporte a partir de scripts inline e ficheiros de scripts primários.

## <a name="work-with-outputs-from-powershell-script"></a>Trabalhar com saídas do script PowerShell

O modelo a seguir mostra como passar valores entre dois `deploymentScripts` recursos:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

No primeiro recurso, define-se uma variável chamada `$DeploymentScriptOutputs` , e utiliza-a para armazenar os valores de saída. Para aceder ao valor de saída a partir de outro recurso dentro do modelo, utilize:

```json
reference('<ResourceName>').outputs.text
```

## <a name="work-with-outputs-from-cli-script"></a>Trabalhar com saídas do script CLI

Diferente do script de implementação powerShell, o suporte CLI/bash não expõe uma variável comum para armazenar saídas de scripts, em vez disso, há uma variável ambiental chamada `AZ_SCRIPTS_OUTPUT_PATH` que armazena o local onde o ficheiro de saídas do script reside. Se um script de implementação for executado a partir de um modelo de Gestor de Recursos, esta variável de ambiente é definida automaticamente para si pela concha Bash. O valor de `AZ_SCRIPTS_OUTPUT_PATH` *é /mnt/azscripts/azscriptoutput/scriptoutputs.json*.

As saídas de scripts de implementação devem ser guardadas no `AZ_SCRIPTS_OUTPUT_PATH` local e as saídas devem ser um objeto de corda JSON válido. O conteúdo do ficheiro deve ser guardado como um par de valores-chave. Por exemplo, uma matriz de cordas é armazenada como `{ "MyResult": [ "foo", "bar"] }` .  Armazenar apenas os resultados da matriz, por `[ "foo", "bar" ]` exemplo, é inválido.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/) é usado na amostra anterior. Vem com as imagens do contentor. Consulte [o ambiente de desenvolvimento Configure](#configure-development-environment).

## <a name="use-existing-storage-account"></a>Utilize a conta de armazenamento existente

Uma conta de armazenamento e uma instância de contentor são necessárias para a execução do script e resolução de problemas. Tem as opções para especificar uma conta de armazenamento existente, caso contrário a conta de armazenamento juntamente com a instância do contentor são automaticamente criadas pelo serviço de scripts. Os requisitos para a utilização de uma conta de armazenamento existente:

- Os tipos de conta de armazenamento suportados são:

    | SKU             | Tipo Suportado     |
    |-----------------|--------------------|
    | Premium_LRS     | Arquitorage de arquivos        |
    | Premium_ZRS     | Arquitorage de arquivos        |
    | Standard_GRS    | Armazenamento, ArmazenamentoV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Armazenamento, ArmazenamentoV2 |
    | Standard_RAGRS  | Armazenamento, ArmazenamentoV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Estas combinações suportam ações de ficheiros. Para obter mais informações, consulte [Criar uma partilha de ficheiros Azure](../../storage/files/storage-how-to-create-file-share.md) e tipos de contas de [armazenamento.](../../storage/common/storage-account-overview.md)

- As regras de firewall da conta de armazenamento ainda não estão suportadas. Para obter mais informações, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](../../storage/common/storage-network-security.md).
- O principal de implantação deve ter permissões para gerir a conta de armazenamento, que inclui ler, criar, eliminar ações de ficheiros.

Para especificar uma conta de armazenamento existente, adicione o seguinte JSON ao elemento de propriedade `Microsoft.Resources/deploymentScripts` de:

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`: especificar o nome da conta de armazenamento.
- `storageAccountKey`: especifique uma das chaves da conta de armazenamento. Pode utilizar a função [listKeys](./template-functions-resource.md#listkeys) para recuperar a tecla. Por exemplo:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Consulte [os modelos de amostra](#sample-templates) para obter uma amostra de `Microsoft.Resources/deploymentScripts` definição completa.

Quando uma conta de armazenamento existente é usada, o serviço de script cria uma partilha de ficheiros com um nome único. Consulte [os recursos do script de implementação de limpeza](#clean-up-deployment-script-resources) para saber como o serviço de scripts limpa a partilha de ficheiros.

## <a name="develop-deployment-scripts"></a>Desenvolver scripts de implementação

### <a name="handle-non-terminating-errors"></a>Lidar com erros não terminantes

Pode controlar como o PowerShell responde a erros não terminantes utilizando a `$ErrorActionPreference` variável no seu script de implementação. Se a variável não estiver definida no seu script de implementação, o serviço de script utiliza o valor predefinido **Continuar**.

O serviço de script define o estado de fornecimento de recursos para **Falhado** quando o script encontra um erro apesar da definição de `$ErrorActionPreference` .

### <a name="use-environment-variables"></a>Use environment variables (Utilizar variáveis de ambiente)

O script de implementação utiliza estas variáveis ambientais:

|Variável de ambiente|Valor predefinido|Sistema reservado|
|--------------------|-------------|---------------|
|AZ_SCRIPTS_AZURE_ENVIRONMENT|AzureCloud|N|
|AZ_SCRIPTS_CLEANUP_PREFERENCE|OnExpiration|N|
|AZ_SCRIPTS_OUTPUT_PATH|<AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY>/<AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME>|Y|
|AZ_SCRIPTS_PATH_INPUT_DIRECTORY|/mnt/azscripts/azscriptinput|Y|
|AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY|/mnt/azscripts/azscriptoutput|Y|
|AZ_SCRIPTS_PATH_USER_SCRIPT_FILE_NAME|Azure PowerShell: userscript.ps1; Azure CLI: userscript.sh|Y|
|AZ_SCRIPTS_PATH_PRIMARY_SCRIPT_URI_FILE_NAME|primaryscripturi.config|Y|
|AZ_SCRIPTS_PATH_SUPPORTING_SCRIPT_URI_FILE_NAME|supportingscripturi.config|Y|
|AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME|scriptoutputs.jsem|Y|
|AZ_SCRIPTS_PATH_EXECUTION_RESULTS_FILE_NAME|executionresult.jsem|Y|
|AZ_SCRIPTS_USER_ASSIGNED_IDENTITY|/subscrições/|N|

Para obter mais informações sobre a `AZ_SCRIPTS_OUTPUT_PATH` utilização, consulte Trabalhar com [saídas a partir do script CLI](#work-with-outputs-from-cli-script).

### <a name="pass-secured-strings-to-deployment-script"></a>Passe cordas seguras para script de implementação

Definir variáveis ambientais (EnvironmentVariable) nas instâncias do seu contentor permite-lhe fornecer uma configuração dinâmica da aplicação ou do script executado pelo recipiente. O script de implementação lida com variáveis ambientais não seguras e seguras da mesma forma que a Instância do Contentor Azure. Para obter mais informações, consulte [variáveis ambientais definidas em casos de recipientes](../../container-instances/container-instances-environment-variables.md#secure-values). Por exemplo, consulte [os modelos de amostra.](#sample-templates)

O tamanho máximo permitido para variáveis ambientais é de 64 KB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Scripts de implementação de monitorização e resolução de problemas

O serviço de scripts cria uma [conta de armazenamento](../../storage/common/storage-account-overview.md) (a menos que especifique uma conta de armazenamento existente) e uma instância de [contentor](../../container-instances/container-instances-overview.md) para execução de scripts. Se estes recursos forem automaticamente criados pelo serviço de scripts, ambos os recursos têm o `azscripts` sufixo nos nomes dos recursos.

![Nomes de recursos de design de design de design de gestão de modelo de gestor de recursos de recursos de gest](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

O script do utilizador, os resultados da execução e o ficheiro stdout são armazenados nas partilhas de ficheiros da conta de armazenamento. Há uma pasta chamada `azscripts` . Na pasta, há mais duas pastas para a entrada e os ficheiros de saída: `azscriptinput` e `azscriptoutput` .

A pasta de saída contém um _executionresult.jsligado_ e o ficheiro de saída do script. Pode ver a mensagem de erro de execução do script _executionresult.jsem_. O ficheiro de saída só é criado quando o script é executado com sucesso. A pasta de entrada contém um ficheiro de script powerShell do sistema e os ficheiros de script de implementação do utilizador. Pode substituir o ficheiro de script de implementação do utilizador por um revisto e refazer o script de implementação a partir da instância do recipiente Azure.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Depois de implementar um recurso de script de implementação, o recurso é listado sob o grupo de recursos no portal Azure. A imagem que se segue mostra a página **de visão geral** de um recurso de script de implementação:

![Visão geral do portal do portal de implementação do modelo do gestor de recursos](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

A página geral apresenta algumas informações importantes do recurso, tais como **Estado de Provisionamento,** **conta de armazenamento,** **instância de contentores** e **Registos**.

A partir do menu esquerdo, pode ver o conteúdo do script de implementação, os argumentos passados para o script e a saída. Também pode exportar um modelo para o script de implementação, incluindo o script de implementação.

### <a name="use-powershell"></a>Utilizar o PowerShell

Utilizando o Azure PowerShell, pode gerir scripts de implementação no âmbito de subscrição ou grupo de recursos:

- [Get-AzDeploymentScript:](/powershell/module/az.resources/get-azdeploymentscript)Obtém ou lista scripts de implementação.
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog): Obtém o registo de uma execução de script de implementação.
- [Remove-AzDeploymentScript:](/powershell/module/az.resources/remove-azdeploymentscript)Remove um script de implementação e os seus recursos associados.
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog): Guarda o registo de uma execução de script de implementação para o disco.

A `Get-AzDeploymentScript` saída é semelhante a:

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Utilizar a CLI do Azure

Utilizando o CLI Azure, pode gerir scripts de implementação no âmbito de subscrição ou grupo de recursos:

- [az implementações-scripts eliminar](/cli/azure/deployment-scripts#az_deployment_scripts_delete): Eliminar um script de implementação.
- [lista de scripts de implementação az](/cli/azure/deployment-scripts#az_deployment_scripts_list): Listar todos os scripts de implantação.
- [az deployment-scripts show](/cli/azure/deployment-scripts#az_deployment_scripts_show): Recuperar um script de implementação.
- [az implementação-scripts show-log](/cli/azure/deployment-scripts#az_deployment_scripts_show_log): Mostrar registos de scripts de implementação.

A saída do comando da lista é semelhante a:

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>Utilizar API de Repouso

Pode obter a informação de implementação de recursos de envio ao nível do grupo de recursos e ao nível de subscrição utilizando a API REST:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

O exemplo a seguir utiliza [ARMClient:](https://github.com/projectkudu/ARMClient)

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2020-10-01
```

O resultado é semelhante a:

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

A API do seguinte REST devolve o registo:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2020-10-01
```

Só funciona antes de os recursos do script de implantação serem eliminados.

Para ver o recurso de implementaçãoDiss no portal, selecione **Mostrar os tipos ocultos**:

![Script de implementação do modelo do gestor de recursos, mostrar tipos ocultos, portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Limpar recursos de script de implementação

Uma conta de armazenamento e uma instância de contentor são necessárias para a execução do script e resolução de problemas. Tem as opções para especificar uma conta de armazenamento existente, caso contrário uma conta de armazenamento juntamente com uma instância de contentores são automaticamente criadas pelo serviço de script. Os dois recursos criados automaticamente são eliminados pelo serviço de scripts quando a execução do script de implementação entra num estado terminal. É cobrado pelos recursos até que os recursos sejam apagados. Para obter a informação sobre os preços, consulte os [preços de Container Instances](https://azure.microsoft.com/pricing/details/container-instances/) e [preços de armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/).

O ciclo de vida destes recursos é controlado pelas seguintes propriedades no modelo:

- `cleanupPreference`: Limpar a preferência quando a execução do guião chegar a um estado terminal. Os valores suportados são:

  - **Sempre:** Elimine os recursos automaticamente criados assim que a execução do script chegar a um estado terminal. Se for utilizada uma conta de armazenamento existente, o serviço de scripts elimina a parte de ficheiro criada na conta de armazenamento. Como o `deploymentScripts` recurso ainda pode estar presente após a limpeza dos recursos, o serviço de scripts persiste nos resultados da execução do script, por exemplo, stdout, outputs e valor de retorno antes de os recursos serem eliminados.
  - **OnSuccess**: Elimine os recursos criados automaticamente apenas quando a execução do script for bem sucedida. Se for utilizada uma conta de armazenamento existente, o serviço de scripts remove a partilha de ficheiros apenas quando a execução do script for bem sucedida. Você ainda pode aceder aos recursos para encontrar a informação de depurar.
  - **OnExpiration**: Elimine os recursos criados automaticamente apenas quando a `retentionInterval` definição expirar. Se for utilizada uma conta de armazenamento existente, o serviço de scripts remove a parte do ficheiro, mas mantém a conta de armazenamento.

- `retentionInterval`: Especificar o intervalo de tempo de que um recurso de script será mantido e após o qual será expirado e eliminado.

> [!NOTE]
> Não é aconselhável utilizar a conta de armazenamento e a instância do recipiente que são geradas pelo serviço de scripts para outros fins. Os dois recursos podem ser removidos dependendo do ciclo de vida do script.

A instância do contentor e a conta de armazenamento são eliminadas de acordo com o `cleanupPreference` . No entanto, se o script falhar e `cleanupPreference` não estiver definido para **Always**, o processo de implantação mantém automaticamente o recipiente a funcionar durante uma hora. Podes usar esta hora para resolver problemas no guião. Se quiser manter o recipiente a funcionar após implementações bem sucedidas, adicione um passo de sono no seu script. Por exemplo, adicione [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) ao fim do seu script. Se não adicionar o passo de sono, o recipiente está definido para um estado terminal e não pode ser acedido mesmo que ainda não tenha sido apagado.

## <a name="run-script-more-than-once"></a>Executar script mais de uma vez

A execução do script de implantação é uma operação idempotente. Se nenhuma das propriedades do `deploymentScripts` recurso (incluindo o script inline) for alterada, o script não executa quando recolocar o modelo. O serviço de scripts de implementação compara os nomes de recursos do modelo com os recursos existentes no mesmo grupo de recursos. Existem duas opções se quiser executar o mesmo script de implementação várias vezes:

- Mude o nome do seu `deploymentScripts` recurso. Por exemplo, utilize a função do modelo [utcNow](./template-functions-date.md#utcnow) como nome de recurso ou como parte do nome do recurso. Mudar o nome do recurso cria um novo `deploymentScripts` recurso. É bom para manter um historial de execução de guiões.

    > [!NOTE]
    > A `utcNow` função só pode ser utilizada no valor predefinido para um parâmetro.

- Especifique um valor diferente na propriedade do `forceUpdateTag` modelo. Por exemplo, usar `utcNow` como valor.

> [!NOTE]
> Escreva os scripts de implementação que são idempotentes. Isto garante que, se voltarem a funcionar acidentalmente, não provocará alterações no sistema. Por exemplo, se o script de implementação for usado para criar um recurso Azure, verifique se o recurso não existe antes de o criar, para que o script tenha sucesso ou não crie o recurso novamente.

## <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

Pode utilizar uma imagem de recipiente pré-configurada como ambiente de desenvolvimento de scripts de implementação. Para obter mais informações, consulte [o ambiente de desenvolvimento configurar para scripts de implementação em modelos](./deployment-script-template-configure-dev.md).

Depois de o script ser testado com sucesso, pode usá-lo como um script de implementação nos seus modelos.

## <a name="deployment-script-error-codes"></a>Códigos de erro de script de implementação

| Código de erro | Description |
|------------|-------------|
| ImplementaçãoScriptInvalidOperação | A definição de recursos de script de implementação no modelo contém nomes de propriedade inválidos. |
| ImplementaçãoScriptResourceConflict | Não é possível apagar um recurso de script de implantação que esteja em estado não terminal e a execução não tenha excedido 1 hora. Ou não pode refazer o mesmo script de implementação com o mesmo identificador de recursos (mesma subscrição, nome de grupo de recursos e nome de recurso), mas diferentes conteúdos corporais de script ao mesmo tempo. |
| ImplementaçãoScriptOperationFailed | A operação do script de implantação falhou internamente. Contacte o suporte da Microsoft. |
| ImplementaçãoScriptStorageAccountAccessKeyNotSpecified | A chave de acesso não foi especificada para a conta de armazenamento existente.|
| ImplementaçãoScriptContainerGroupContainsInvalidContainers | Um grupo de contentores criado pelo serviço de scripts de implantação foi modificado externamente, e os recipientes inválidos foram adicionados. |
| ImplementaçãoScriptContainerGroupInNonterminalState | Dois ou mais recursos de script de implantação usam o mesmo nome de instância de contentores Azure no mesmo grupo de recursos, e um deles ainda não terminou a sua execução. |
| ImplementaçãoScriptstorageAccountInvalidKind | A conta de armazenamento existente do tipo BlobBlobStorage ou BlobStorage não suporta ações de ficheiros e não pode ser utilizada. |
| ImplementaçãoScriptstorageAccountInvalidKindAndsku | A conta de armazenamento existente não suporta ações de ficheiros. Para obter uma lista de tipos de conta de armazenamento suportados, consulte [a conta de armazenamento existente](#use-existing-storage-account). |
| ImplementaçãoScriptstorageAccountNotFound | A conta de armazenamento não existe ou foi eliminada por um processo ou ferramenta externa. |
| ImplementaçãoScriptStorageAccountWithServiceEndpointEnabled | A conta de armazenamento especificada tem um ponto final de serviço. Uma conta de armazenamento com um ponto final de serviço não é suportada. |
| ImplementaçãoScriptStorageAccountInvalidAccessKey | Chave de acesso inválida especificada para a conta de armazenamento existente. |
| ImplementaçãoScriptStorageAccountInvalidAccessKeyFormat | Formato chave da conta de armazenamento inválido. Consulte [as teclas de acesso à conta de armazenamento](../../storage/common/storage-account-keys-manage.md). |
| ImplementaçãoScriptExceededMaxAllowedTime | O tempo de execução do script de implementação excedeu o valor de tempo limite especificado na definição de recursos de script de implementação. |
| ImplementaçãoScriptInvalidOutputs | A saída do script de implementação não é um objeto JSON válido. |
| ImplementaçãoScriptContainerInstancesServiceLoginFailure | A identidade gerida atribuída pelo utilizador não conseguiu entrar após 10 tentativas com intervalo de 1 minuto. |
| ImplementaçãoScriptContainerGroupNotFound | Um grupo de contentores criado pelo serviço de scripts de implantação foi eliminado por uma ferramenta ou processo externo. |
| ImplementaçãoScriptDownloadFailure | Falhou em descarregar um script de suporte. Ver [Utilizar o script de suporte .](#use-supporting-scripts)|
| ImplementaçãoScriptError | O script do utilizador atirou um erro. |
| ImplementaçãoScriptBootstrapScriptExecutionFailed | O guião da bota atirou um erro. O script bootstrap é o script do sistema que orquestra a execução do script de implementação. |
| ImplementaçãoScriptExecutionFailed | Erro desconhecido durante a execução do script de implementação. |
| ImplementaçãoScriptContainerInstancesServiceUn disponível | Ao criar a instância do contentor Azure (ACI), a ACI lançou um erro indisponíveis de serviço. |
| ImplementaçãoScriptContainerGroupInNonterminalState | Ao criar a instância do recipiente Azure (ACI), outro script de implantação está a utilizar o mesmo nome ACI no mesmo âmbito (mesma subscrição, nome de grupo de recursos e nome de recurso). |
| ImplementaçãoScriptContainerGroupNameInvalid | O nome de instância do contentor Azure (ACI) especificado não satisfaz os requisitos de ACI. Consulte [questões comuns de resolução de problemas em instâncias de contentores Azure](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar scripts de implantação. Para percorrer um tutorial de script de implementação:

> [!div class="nextstepaction"]
> [Tutorial: Use scripts de implementação em modelos de Gestor de Recursos Azure](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [Aprenda módulo: Alargar os modelos ARM utilizando scripts de implementação](/learn/modules/extend-resource-manager-template-deployment-scripts/)
