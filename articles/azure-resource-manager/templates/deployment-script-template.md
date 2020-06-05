---
title: Use scripts de implementação em modelos Microsoft Docs
description: utilizar scripts de implementação em modelos de Gestor de Recursos Azure.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: jgao
ms.openlocfilehash: fb910260c562a41871fe0cd13d5e5e9652b2017d
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417111"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Utilize scripts de implementação em modelos (Pré-visualização)

Aprenda a usar scripts de implementação em modelos de recursos Azure. Com um novo tipo de recurso chamado `Microsoft.Resources/deploymentScripts` , os utilizadores podem executar scripts de implementação em implementações de modelos e rever os resultados da execução. Estes scripts podem ser usados para executar etapas personalizadas, tais como:

- adicionar utilizadores a um diretório
- executar operações de plano de dados, por exemplo, bolhas de cópia ou base de dados de sementes
- olhar para cima e validar uma chave de licença
- criar um certificado auto-assinado
- criar um objeto em Azure AD
- procure os blocos de endereço IP do sistema personalizado

Os benefícios do script de implementação:

- Fácil de codificar, usar e depurar. Pode desenvolver scripts de implementação nos seus ambientes de desenvolvimento favoritos. Os scripts podem ser incorporados em modelos ou em ficheiros de script externos.
- Pode especificar a linguagem e a plataforma do script. Atualmente, os scripts de implementação Azure PowerShell e Azure CLI no ambiente Linux são suportados.
- Permitir especificar as identidades que são usadas para executar os scripts. Atualmente, [apenas a identidade gerida atribuída pelo utilizador Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) é suportada.
- Permitir passar argumentos de linha de comando para o script.
- Pode especificar as saídas do script e passá-las de volta para a implementação.

O recurso de script de implantação só está disponível nas regiões onde o Azure Container Instance está disponível.  Consulte [a disponibilidade de recursos para instâncias de contentores Azure nas regiões de Azure](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Uma conta de armazenamento e uma instância de contentor são necessárias para a execução do script e resolução de problemas. Tem as opções para especificar uma conta de armazenamento existente, caso contrário a conta de armazenamento juntamente com a instância do contentor são automaticamente criadas pelo serviço de scripts. Os dois recursos criados automaticamente são normalmente eliminados pelo serviço de scripts quando a execução do script de implementação entra num estado terminal. É cobrado pelos recursos até que os recursos sejam apagados. Para saber mais, consulte [os recursos do script de implementação de limpeza](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Pré-requisitos

- **Uma identidade gerida atribuída pelo utilizador com o papel do contribuinte no grupo de recursos-alvo**. Esta identidade é usada para executar scripts de implantação. Para realizar operações fora do grupo de recursos, precisa conceder permissões adicionais. Por exemplo, atribua a identidade ao nível de subscrição se quiser criar um novo grupo de recursos.

  > [!NOTE]
  > O serviço de scripts cria uma conta de armazenamento (a menos que especifique uma conta de armazenamento existente) e uma instância de contentor em segundo plano .  É necessária uma identidade gerida atribuída pelo utilizador com o papel do contribuinte ao nível da subscrição se a subscrição não tiver registado os fornecedores de recursos da conta de armazenamento Azure (Microsoft.Storage) e da instância de contentores Azure (Microsoft.ContainerInstance).

  Para criar uma identidade, consulte [Criar uma identidade gerida atribuída pelo utilizador utilizando o portal Azure,](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)ou utilizando o [Azure CLI,](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)ou [utilizando a Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Precisa da identificação de identidade quando implementar o modelo. O formato da identidade é:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Utilize o seguinte script CLI ou PowerShell para obter o ID fornecendo o nome do grupo de recursos e o nome de identidade.

  # <a name="cli"></a>[CLI](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g $resourceGroupName -n $idName --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** ou **Azure CLI**. Para obter uma lista de versões Azure PowerShell suportadas, consulte [aqui;](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list) para uma lista de versões Azure CLI suportadas, consulte [aqui](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

    >[!IMPORTANT]
    > O script de implementação utiliza as imagens CLI disponíveis do Microsoft Container Registry (MCR) . Leva cerca de um mês para certificar uma imagem CLI para o script de implementação. Não utilize as versões CLI que foram lançadas dentro de 30 dias. Para encontrar as datas de lançamento das imagens, consulte [as notas de lançamento do Azure CLI](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest). Se for utilizada uma versão não suportada, a lista de mensagens de erro lista as versões suportadas.

    Não precisa destas versões para implementar modelos. Mas estas versões são necessárias para testar scripts de implementação localmente. Consulte [a instalação do módulo Azure PowerShell](/powershell/azure/install-az-ps). Podes usar uma imagem pré-configurada do Docker.  Consulte [o ambiente de desenvolvimento Configure](#configure-development-environment).

## <a name="sample-templates"></a>Modelos de exemplo

O seguinte json é um exemplo.  O esquema de modelo mais recente pode ser encontrado [aqui.](/azure/templates/microsoft.resources/deploymentscripts)

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
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
    "forceUpdateTag": 1,
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
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
> O exemplo é para o propósito de demonstração.  **scriptContent** e **primaryScriptUri** não podem coexistir em um modelo.

Detalhes do valor da propriedade:

- **Identidade**: O serviço de scripts de implementação utiliza uma identidade gerida atribuída pelo utilizador para executar os scripts. Atualmente, apenas a identidade gerida atribuída pelo utilizador é suportada.
- **tipo**: Especificar o tipo de script. Atualmente, os scripts Azure PowerShell e Azure CLI são suporte. Os valores são **AzurePowerShell** e **AzureCLI**.
- **forceUpdateTag**: Alterar este valor entre as implementações do modelo força o script de implantação a ser re-executado. Utilize a função newGuid() ou utcNow() que precisa de ser definida como o padrãoValue de um parâmetro. Para saber mais, consulte [o roteiro run mais de uma vez](#run-script-more-than-once).
- **configurações de contentores**: Especifique as definições para personalizar a instância do recipiente Azure.  **containerGroupName** destina-se a especificar o nome do grupo do contentor.  Se não for especificado, o nome do grupo será gerado automaticamente.
- **armazenamentoCotascos**: Especifique as definições para utilizar uma conta de armazenamento existente. Se não for especificada, uma conta de armazenamento é criada automaticamente. Ver [Utilizar uma conta de armazenamento existente.](#use-an-existing-storage-account)
- **azPowerShellVersion** / **azCliVersion**: Especifique a versão do módulo a utilizar. Para obter uma lista de versões PowerShell e CLI suportadas, consulte [Pré-requisitos](#prerequisites).
- argumentos : Especificar os **valores**dos parâmetros. Os valores são separados por espaços.
- **ambienteVariables**: Especifique as variáveis ambientais para passar para o script. Para obter mais informações, consulte [Desenvolver scripts de implementação.](#develop-deployment-scripts)
- **scriptContent**: Especifique o conteúdo do script. Para executar um script externo, use `primaryScriptUri` em vez disso. Por exemplo, consulte [o script inline](#use-inline-scripts) e [use script externo](#use-external-scripts).
- **PrimaryScriptUri**: Especifique um Url acessível ao público para o script de implementação primária com extensões de ficheiros suportadas.
- **suportandoScriptUris**: Especifique uma matriz de Urls acessíveis ao público para suportar ficheiros que são chamados em `ScriptContent` ambos ou `PrimaryScriptUri` .
- **tempo limite**: Especifique o tempo máximo de execução do script permitido especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor predefinido é **P1D.**
- **limpezaPreferência**. Especifique a preferência de limpar os recursos de implantação quando a execução do script chegar a um estado terminal. A definição predefinida é **sempre**, o que significa eliminar os recursos apesar do estado terminal (Bem sucedido, Falhado, Cancelado). Para saber mais, consulte [Limpar os recursos do script de implementação](#clean-up-deployment-script-resources).
- **retençãoInterval**: Especifique o intervalo para o qual o serviço mantém os recursos de script de implantação após a execução do script de implantação atingir um estado terminal. Os recursos do script de implantação serão eliminados quando esta duração expirar. A duração baseia-se no [padrão ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor predefinido é **P1D,** o que significa sete dias. Esta propriedade é usada quando a limpezaPreferência é definida para *OnExpiration*. A propriedade *OnExpiration* não está ativada atualmente. Para saber mais, consulte [Limpar os recursos do script de implementação](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Amostras adicionais

- [criar e atribuir um certificado a um cofre chave](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [criar e atribuir uma identidade gerida atribuída ao utilizador a um grupo de recursos e executar um script de implementação](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json).

> [!NOTE]
> Recomenda-se criar uma identidade atribuída ao utilizador e conceder permissões antecipadamente. Poderá obter erros relacionados com a inscrição e permissão se criar a identidade e conceder permissões no mesmo modelo onde executa scripts de implementação. Leva algum tempo até que as permissões se tornem eficazes.

## <a name="use-inline-scripts"></a>Use scripts inline

O modelo a seguir tem um recurso definido com o `Microsoft.Resources/deploymentScripts` tipo. A parte realçada é o roteiro em linha.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Como os scripts de implementação inline são incluídos em citações duplas, as cordas dentro dos scripts de implementação precisam de ser escapados usando um **&#92;** ou incluído em citações únicas. Também pode considerar a utilização da substituição de cordas tal como é mostrada na amostra JSON anterior.

O script requer um parâmetro e produz o valor do parâmetro. **ImplementaçõesScriptOuts** são utilizados para armazenar saídas.  Na secção de saídas, a linha de **valor** mostra como aceder aos valores armazenados. `Write-Output`é usado para depurar propósito. Para aprender a aceder ao ficheiro de saída, consulte os [scripts de implementação de Debug](#debug-deployment-scripts).  Para as descrições da propriedade, consulte [os modelos de amostra.](#sample-templates)

Para executar o script, selecione **Experimente-o** para abrir a Cloud Shell e, em seguida, cole o seguinte código no painel de conchas.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

O resultado tem o seguinte aspeto:

![Script de implementação de modelo de gestor de recursos olá saída do mundo](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Use scripts externos

Além dos scripts inline, também pode utilizar ficheiros de scripts externos. Apenas os scripts principais do PowerShell com a extensão do ficheiro **PS1** são suportados. Para scripts CLI, os scripts primários podem ter quaisquer extensões (ou sem uma extensão), desde que os scripts sejam scripts de bash válidos. Para utilizar ficheiros de script externos, `scriptContent` substitua-o por `primaryScriptUri` . Por exemplo:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Para ver um exemplo, selecione [aqui.](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)

Os ficheiros de script externos devem estar acessíveis.  Para proteger os seus ficheiros de scripts que são armazenados em contas de armazenamento Azure, consulte [o modelo ARM privado com ficha SAS](./secure-template-with-sas-token.md).

É responsável por garantir a integridade dos scripts que são referenciados por script de implementação, quer **PrimaryScriptUri,** quer **SupportIngScriptUris**.  Referência apenas scripts em que confia.

## <a name="use-supporting-scripts"></a>Use scripts de suporte

Pode separar lógicas complicadas em um ou mais ficheiros de scripts de suporte. A `supportingScriptURI` propriedade permite fornecer uma variedade de URIs para os ficheiros de script de suporte, se necessário:

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

Os ficheiros de suporte são copiados para azscripts/azscriptinput no tempo de execução. Utilize um caminho relativo para fazer referência aos ficheiros de suporte a partir de scripts inline e ficheiros de scripts primários.

## <a name="work-with-outputs-from-powershell-script"></a>Trabalhar com saídas do script PowerShell

O modelo a seguir mostra como passar valores entre dois recursos de implementaçãoScripts:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

No primeiro recurso, define-se uma variável chamada **$DeploymentScriptOutputs**, e utiliza-a para armazenar os valores de saída. Para aceder ao valor de saída a partir de outro recurso dentro do modelo, utilize:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Trabalhar com saídas do script CLI

Diferente do script de implementação powerShell, o suporte CLI/bash não expõe uma variável comum para armazenar saídas de scripts, em vez disso, existe uma variável ambiental chamada **AZ_SCRIPTS_OUTPUT_PATH** que armazena o local onde o ficheiro de saídas de script reside. Se um script de implementação for executado a partir de um modelo de Gestor de Recursos, esta variável de ambiente é definida automaticamente para si pela concha Bash.

As saídas de scripts de implementação devem ser guardadas no local AZ_SCRIPTS_OUTPUT_PATH e as saídas devem ser um objeto de corda JSON válido. O conteúdo do ficheiro deve ser guardado como um par de valores-chave. Por exemplo, uma matriz de cordas é armazenada como { "MyResult": [ "foo", "bar"] }.  Armazenar apenas os resultados da matriz, por exemplo [ "foo", "bar" ], é inválido.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/) é usado na amostra anterior. Vem com as imagens do contentor. Consulte [o ambiente de desenvolvimento Configure](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Desenvolver scripts de implementação

### <a name="handle-non-terminating-errors"></a>Lidar com erros não terminantes

Pode controlar como o PowerShell responde a erros não terminantes utilizando a variável **$ErrorActionPreference** no seu script de implementação. Se a variável não estiver definida no seu script de implementação, o serviço de script utiliza o valor predefinido **Continue**.

O serviço de script define o estado de fornecimento de recursos para **Falhado** quando o script encontra um erro apesar da definição de $ErrorActionPreference.

### <a name="pass-secured-strings-to-deployment-script"></a>Passe cordas seguras para script de implementação

Definir variáveis ambientais (EnvironmentVariable) nas instâncias do seu contentor permite-lhe fornecer uma configuração dinâmica da aplicação ou do script executado pelo recipiente. O script de implementação lida com variáveis ambientais não seguras e seguras da mesma forma que a Instância do Contentor Azure. Para obter mais informações, consulte [variáveis ambientais definidas em casos de recipientes](../../container-instances/container-instances-environment-variables.md#secure-values).

O tamanho máximo permitido para variáveis ambientais é de 64KB.

## <a name="debug-deployment-scripts"></a>Scripts de implementação de depuração de depuração

O serviço de scripts cria uma [conta de armazenamento](../../storage/common/storage-account-overview.md) (a menos que especifique uma conta de armazenamento existente) e uma instância de [contentor](../../container-instances/container-instances-overview.md) para execução de scripts. Se estes recursos forem automaticamente criados pelo serviço de scripts, ambos os recursos têm o **sufixo azscripts** nos nomes dos recursos.

![Nomes de recursos de design de design de design de gestão de modelo de gestor de recursos de recursos de gest](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

O script do utilizador, os resultados da execução e o ficheiro stdout são armazenados nas partilhas de ficheiros da conta de armazenamento. Há uma pasta chamada **azscripts**. Na pasta, há mais duas pastas para a entrada e os ficheiros de saída: **azscriptinput** e **azscriptout .**

A pasta de saída contém um **resultado de execução.json** e o ficheiro de saída do script. Pode ver a mensagem de erro de execução do script em **execuçãoult.json**. O ficheiro de saída só é criado quando o script é executado com sucesso. A pasta de entrada contém um ficheiro de script powerShell do sistema e os ficheiros de script de implementação do utilizador. Pode substituir o ficheiro de script de implementação do utilizador por um revisto e refazer o script de implementação a partir da instância do recipiente Azure.

Pode obter a informação de implementação de recursos de envio ao nível do grupo de recursos e ao nível de subscrição utilizando a API REST:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

O exemplo a seguir utiliza [ARMClient:](https://github.com/projectkudu/ARMClient)

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

O resultado é semelhante a:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

A saída mostra o estado de implantação e os IDs de recursos de implementação.

A API do seguinte REST devolve o registo:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Só funciona antes de os recursos do script de implantação serem eliminados.

Para ver o recurso de implementaçãoDiss no portal, selecione **Mostrar os tipos ocultos**:

![Script de implementação do modelo do gestor de recursos, mostrar tipos ocultos, portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="use-an-existing-storage-account"></a>Utilize uma conta de armazenamento existente

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

    Estas combinações suportam a partilha de ficheiros.  Para obter mais informações, consulte [Criar uma partilha de ficheiros Azure](../../storage/files/storage-how-to-create-file-share.md) e tipos de contas de [armazenamento.](../../storage/common/storage-account-overview.md)
- As regras de firewall de conta de armazenamento ainda não estão suportadas. Para obter mais informações, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](../../storage/common/storage-network-security.md).
- A identidade gerida atribuída pelo utilizador do script deve ter permissões para gerir a conta de armazenamento, que inclui ler, criar, eliminar ações de ficheiros.

Para especificar uma conta de armazenamento existente, adicione o seguinte json ao elemento de propriedade `Microsoft.Resources/deploymentScripts` de:

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- **armazenamentoApoma:** especifique o nome da conta de armazenamento.
- **armazenamentoCocountKey"**: especifique uma das chaves da conta de armazenamento. Pode utilizar a [`listKeys()`](./template-functions-resource.md#listkeys) função para recuperar a chave. Por exemplo:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Consulte [os modelos de amostra](#sample-templates) para obter uma amostra de `Microsoft.Resources/deploymentScripts` definição completa.

Quando uma conta de armazenamento existente é usada, o serviço de script cria uma partilha de ficheiros com um nome único. Consulte [os recursos do script de implementação de limpeza](#clean-up-deployment-script-resources) para saber como o serviço de scripts limpa a partilha de ficheiros.

## <a name="clean-up-deployment-script-resources"></a>Limpar recursos de script de implementação

Uma conta de armazenamento e uma instância de contentor são necessárias para a execução do script e resolução de problemas. Tem as opções para especificar uma conta de armazenamento existente, caso contrário uma conta de armazenamento juntamente com uma instância de contentores são automaticamente criadas pelo serviço de script. Os dois recursos criados automaticamente são eliminados pelo serviço de scripts quando a execução do script de implementação entra num estado terminal. É cobrado pelos recursos até que os recursos sejam apagados. Para obter a informação sobre os preços, consulte os [preços de Container Instances](https://azure.microsoft.com/pricing/details/container-instances/) e [preços de armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/).

O ciclo de vida destes recursos é controlado pelas seguintes propriedades no modelo:

- **limpezaPreferência**: Limpar a preferência quando a execução do script chegar a um estado terminal. Os valores suportados são:

  - **Sempre:** Elimine os recursos automaticamente criados assim que a execução do script chegar a um estado terminal. Se for utilizada uma conta de armazenamento existente, o serviço de scripts elimina a parte de ficheiro criada na conta de armazenamento. Como o recurso de implementaçãoScripts ainda pode estar presente após a limpeza dos recursos, o serviço de scripts persiste nos resultados da execução do script, por exemplo, stdout, saídas, valor de retorno, etc. antes de os recursos serem eliminados.
  - **OnSuccess**: Elimine os recursos criados automaticamente apenas quando a execução do script for bem sucedida. Se for utilizada uma conta de armazenamento existente, o serviço de scripts remove a partilha de ficheiros apenas quando a execução do script for bem sucedida. Você ainda pode aceder aos recursos para encontrar a informação de depurar.
  - **OnExpiration**: Elimine os recursos automaticamente apenas quando a **definição de retençãoInterval** expirar. Se for utilizada uma conta de armazenamento existente, o serviço de scripts remove a parte do ficheiro, mas conserva a conta de armazenamento.

- **retençãoInterval**: Especifique o intervalo de tempo de que um recurso de script será mantido e após o qual será expirado e eliminado.

> [!NOTE]
> Não é aconselhável utilizar a conta de armazenamento e a instância do recipiente que são geradas pelo serviço de scripts para outros fins. Os dois recursos podem ser removidos dependendo do ciclo de vida do script.

## <a name="run-script-more-than-once"></a>Executar script mais de uma vez

A execução do script de implantação é uma operação idempotente. Se nenhuma das propriedades de recursos de implementação (incluindo o script inline) for alterada, o script não será executado quando recolocar o modelo. O serviço de scripts de implementação compara os nomes de recursos do modelo com os recursos existentes no mesmo grupo de recursos. Existem duas opções se quiser executar o mesmo script de implementação várias vezes:

- Alterar o nome do recurso de implementaçãoScripts. Por exemplo, utilize a função do modelo [utcNow](./template-functions-date.md#utcnow) como nome de recurso ou como parte do nome do recurso. Alterar o nome de recurso cria um novo recurso de implementaçãoScripts. É bom para manter uma história de execução de guiões.

    > [!NOTE]
    > A função utcNow só pode ser utilizada no valor predefinido para um parâmetro.

- Especifique um valor diferente na propriedade do `forceUpdateTag` modelo.  Por exemplo, use o utcNow como o valor.

> [!NOTE]
> Escreva os scripts de implementação que são idempotentes. Isto garante que, se voltarem a funcionar acidentalmente, não provocará alterações no sistema. Por exemplo, se o script de implementação for usado para criar um recurso Azure, verifique se o recurso não existe antes de o criar, para que o script tenha sucesso ou não crie o recurso novamente.

## <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

Você pode usar uma imagem de recipiente de estiva pré-configurado como o seu ambiente de desenvolvimento de scripts de implementação. O procedimento a seguir mostra-lhe como configurar a imagem do estivador no Windows. Para Linux e Mac, pode encontrar a informação na Internet.

1. Instale [o Docker Desktop](https://www.docker.com/products/docker-desktop) no seu computador de desenvolvimento.
1. Abra o Ambiente de Trabalho do Docker.
1. Selecione o ícone do Ambiente de Trabalho do Docker a partir de barras de tarefas e, em seguida, selecione **Definições**.
1. Selecione **Unidades Partilhadas,** selecione uma unidade local que deseja estar disponível para os seus contentores e, em seguida, selecione **Aplicar**

    ![Unidade de estivador de script de design de modelo de gestor de recursos](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Introduza as credenciais das suas janelas no momento.
1. Abra uma janela de terminal, quer comando, quer Windows PowerShell (Não utilize o PowerShell ISE).
1. Puxe a imagem do contentor do script de implementação para o computador local:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    O exemplo utiliza a versão PowerShell 2.7.0.

    Para retirar uma imagem CLI de um registo de contentores da Microsoft (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Este exemplo utiliza a versão CLI 2.0.80. O script de implementação utiliza as imagens padrão dos recipientes CLI encontrados [aqui](https://hub.docker.com/_/microsoft-azure-cli).

1. Executar a imagem do estivador localmente.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Substitua ** &lt; a carta do anfitrião>** e o nome do ** &lt; diretório de anfitriões>** por uma pasta existente na unidade partilhada.  Mapeia a pasta para a pasta **/dados** no recipiente. Por exemplo, para mapear D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-significa** manter a imagem do contentor viva.

    Um exemplo CLI:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. **Selecione Partilhe-o** quando receber uma solicitação.
1. A imagem que se segue mostra como executar um script PowerShell, dado que tens um ficheiro helloworld.ps1 na pasta d:\docker.

    ![Modelo de implementação de modelo de gestor de recursos estivador cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Depois de o script ser testado com sucesso, pode usá-lo como um script de implementação.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar scripts de implantação. Para percorrer um tutorial de script de implementação:

> [!div class="nextstepaction"]
> [Tutorial: Use scripts de implementação em modelos de Gestor de Recursos Azure](./template-tutorial-deployment-script.md)
