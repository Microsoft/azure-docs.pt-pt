---
title: Use scripts de implantação em modelos / Microsoft Docs
description: utilizar scripts de implementação em modelos de Gestor de Recursos Azure.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jgao
ms.openlocfilehash: 3129d4c664ec487f2def6cc0d2668b7493f4c988
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78272652"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Utilize scripts de implementação em modelos (Pré-visualização)

Aprenda a usar scripts de implementação em modelos de Recursos Azure. Com um novo tipo de recurso chamado `Microsoft.Resources/deploymentScripts`, os utilizadores podem executar scripts de implementação em implementações de modelos e rever os resultados da execução. Estes scripts podem ser usados para executar passos personalizados tais como:

- adicionar utilizadores a um diretório
- criar um registo de aplicativos
- executar operações de aviões de dados, por exemplo, copiar bolhas ou bases de dados de sementes
- olhar para cima e validar uma chave de licença
- criar um certificado auto-assinado
- criar um objeto em Azure AD
- procure blocos ip address do sistema personalizado

Os benefícios do script de implantação:

- Fácil de codificar, usar e depurar. Você pode desenvolver scripts de implantação em seus ambientes de desenvolvimento favoritos. Os scripts podem ser incorporados em modelos ou em ficheiros de script externos.
- Pode especificar o idioma e a plataforma do script. Atualmente, os scripts de implantação Azure PowerShell e Azure CLI no ambiente Linux são suportados.
- Deixe especificar as identidades que são usadas para executar os scripts. Atualmente, apenas a identidade gerida atribuída ao [utilizador Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) é suportada.
- Permita passar argumentos de linha de comando para o script.
- Pode especificar as saídas do script e passá-las de volta para a implementação.

> [!NOTE]
> O script de implantação está atualmente em pré-visualização. Para usá-lo, deve [inscrever-se para a pré-visualização](https://aka.ms/armtemplatepreviews).

> [!IMPORTANT]
> Dois recursos de script de implementação, uma conta de armazenamento e uma instância de contentores, são criados no mesmo grupo de recursos para execução de scripts e resolução de problemas. Estes recursos são geralmente eliminados pelo serviço de script quando a execução do script de implementação fica em estado terminal. Você é cobrado pelos recursos até que os recursos sejam eliminados. Para saber mais, consulte [os recursos do script de implementação](#clean-up-deployment-script-resources)de limpeza.

## <a name="prerequisites"></a>Pré-requisitos

- Uma identidade gerida atribuída ao **utilizador com o papel do contribuinte para o grupo-recurso-alvo**. Esta identidade é usada para executar scripts de implantação. Para realizar operações fora do grupo de recursos, é necessário conceder permissões adicionais. Por exemplo, atribuir a identidade ao nível de subscrição se quiser criar um novo grupo de recursos.

  > [!NOTE]
  > O motor de script de implantação precisa de criar uma conta de armazenamento e uma instância de contentores em segundo plano.  É necessária uma identidade gerida atribuída ao utilizador com o papel do contribuinte ao nível da subscrição se a subscrição não tiver registado o recurso de armazenamento Azure (Microsoft.Storage) e azure (Microsoft.ContainerInstance) fornecedores.

  Para criar uma identidade, consulte Criar uma identidade gerida atribuída pelo [utilizador utilizando o portal Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), ou utilizando o [Azure CLI,](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)ou utilizando o [Azure PowerShell.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) Precisa da identificação de identidade quando implementar o modelo. O formato da identidade é:

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
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```
  ---

- **Versão Azure PowerShell 3.0.0, 2.8.0 ou 2.7.0** ou **versão Azure CLI 2.0.80, 2.0.79, 2.0.78 ou 2.0.77**. Você não precisa destas versões para implementar modelos. Mas estas versões são necessárias para testar scripts de implantação localmente. Consulte [A instalação do módulo PowerShell Azure](/powershell/azure/install-az-ps). Podes usar uma imagem do Docker reconfigurada.  Ver Ambiente de [desenvolvimento configure](#configure-development-environment).

## <a name="sample-templates"></a>Modelos de exemplo

O seguinte json é um exemplo.  O mais recente esquema de modelo pode ser encontrado [aqui.](/azure/templates/microsoft.resources/deploymentscripts)

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
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
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
> O exemplo é para fins de demonstração.  **scriptContent** e **primaryScriptUris** não podem coexistir num modelo.

Detalhes do valor da propriedade:

- **Identidade**: O serviço de script de implementação utiliza uma identidade gerida atribuída pelo utilizador para executar os scripts. Atualmente, apenas a identidade gerida atribuída ao utilizador é suportada.
- **tipo:** Especificar o tipo de script. Atualmente, os scripts Azure PowerShell e Azure CLI são suporte. Os valores são **AzurePowerShell** e **AzureCLI**.
- **forceUpdateTag**: Alterar este valor entre as implementações do modelo obriga o script de implantação a reexecutar. Utilize a função newGuid() ou utcNow() que precisa de ser definida como o valor padrão de um parâmetro. Para saber mais, consulte [o guião run mais de uma vez](#run-script-more-than-once).
- **azPowerShellVersion**/**azCliVersion**: Especifique a versão do módulo a utilizar. O script de implementação suporta atualmente a versão 2.7.0, 2.8.0, 3.0.0 e azure CLI versão 2.0.80, 2.0.79, 2.0.78, 2.0.77.
- **argumentos**: Especificar os valores dos parâmetros. Os valores são separados por espaços.
- **scriptConteúdo**: Especifique o conteúdo do script. Para executar um guião externo, use `primaryScriptUri` em vez disso. Por exemplo, consulte [Utilize o script inline](#use-inline-scripts) e [use script externo](#use-external-scripts).
- **primaryScriptUri**: Especifique um Url acessível ao público para o script de implementação primária com extensões de ficheiros suportadas.
- **suporteScriptUris**: Especifique um conjunto de Urls acessíveis ao público para ficheiros de suporte que são chamados em `ScriptContent` ou `PrimaryScriptUri`.
- **prazo**: Especificar o tempo máximo de execução do script especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor predefinido é **P1D**.
- **limpezaPreferência**. Especifique a preferência de limpar os recursos de implantação quando a execução do script estiver em estado terminal. A definição predefinida é **sempre**, o que significa apagar os recursos apesar do estado terminal (Bem sucedido, falhado, cancelado). Para saber mais, consulte [a Limpeza dos recursos do script de implementação.](#clean-up-deployment-script-resources)
- intervalo de **retençãoIntervalo**: Especifique o intervalo para o qual o serviço mantém os recursos do script de implantação após a execução do script de implantação atingir um estado terminal. Os recursos do script de implantação serão eliminados quando esta duração expirar. A duração baseia-se no [padrão ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor predefinido é **P1D,** o que significa sete dias. Esta propriedade é utilizada quando a limpezaA preferência está definida para *OnExpiration*. A propriedade *OnExpiration* não está ativada atualmente. Para saber mais, consulte [a Limpeza dos recursos do script de implementação.](#clean-up-deployment-script-resources)

### <a name="additional-samples"></a>Amostras adicionais

- [criar e atribuir um certificado a um cofre chave](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [criar e atribuir uma identidade gerida atribuída](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)ao utilizador a um grupo de recursos, e executar um script de implementação .

> [!NOTE]
> Recomenda-se criar uma identidade atribuída ao utilizador e conceder permissões antecipadas. Você pode obter erros relacionados com o início de sessão e permissão se criar a identidade e conceder permissões no mesmo modelo onde executa scripts de implementação. Leva algum tempo até que as permissões se tornem eficazes.

## <a name="use-inline-scripts"></a>Utilizar scripts inline

O modelo seguinte tem um recurso definido com o tipo `Microsoft.Resources/deploymentScripts`.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json?range=1-54)]

> [!NOTE]
> Como os scripts de implementação em linha são fechados em citações duplas, as cordas dentro dos scripts de implementação precisam de ser fechadas em cotações únicas. O personagem de **&#92;** fuga para a PowerShell é. Também pode considerar a substituição de cordas como é mostrada na amostra JSON anterior. Consulte o valor padrão do parâmetro de nome.

O guião leva um parâmetro e produz o valor do parâmetro. **ImplementaçãoScriptOutputs** é usado para armazenar saídas.  Na secção de saídas, a linha de **valor** mostra como aceder aos valores armazenados. `Write-Output` é usado para depurar. Para aprender a aceder ao ficheiro de saída, consulte scripts de [implementação de Debug](#debug-deployment-scripts).  Para obter as descrições da propriedade, consulte [os modelos de amostra](#sample-templates).

Para executar o script, selecione **Experimente-o** para abrir a Casca de Nuvem Azure e, em seguida, colhe o seguinte código no painel de conchas.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

O resultado tem o seguinte aspeto:

![Script de implementação de modelo de gestor de recursos olá saída mundial](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Utilize scripts externos

Além de scripts inline, também pode utilizar ficheiros de script externos. Apenas são suportados scripts primários powerShell com a extensão do ficheiro **PS1.** Para scripts CLI, os scripts primários podem ter quaisquer extensões (ou sem extensão), desde que os scripts sejam scripts de bash válidos. Para utilizar ficheiros de script externos, substitua `scriptContent` por `primaryScriptUri`. Por exemplo:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Para ver um exemplo, selecione [aqui](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Os ficheiros de script externos devem estar acessíveis.  Para proteger os seus ficheiros de script que são armazenados em contas de armazenamento Azure, consulte [Tutorial: Proteja os artefactos nas implementações](./template-tutorial-secure-artifacts.md)do modelo do Gestor de Recursos Azure .

## <a name="use-supporting-scripts"></a>Use scripts de suporte

Pode separar lógicas complicadas em um ou mais ficheiros de scripts de suporte. A propriedade `supportingScriptURI` permite fornecer uma variedade de URIs aos ficheiros de script de suporte, se necessário:

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

Os ficheiros de script de suporte podem ser chamados a partir de scripts inline e ficheiros de script primários. Os ficheiros de script de suporte não têm restrições na extensão do ficheiro.

Os ficheiros de suporte são copiados para azscripts/azscriptinput no tempo de execução. Utilize o caminho relativo para fazer referência aos ficheiros de suporte a partir de scripts inline e ficheiros de script primários.

## <a name="work-with-outputs-from-powershell-script"></a>Trabalhe com saídas do script PowerShell

O seguinte modelo mostra como passar valores entre dois recursos de implementaçãoScripts:

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic.json?range=1-84)]

No primeiro recurso, define-se uma variável chamada **$DeploymentScriptOutputs**, e usa-a para armazenar os valores de saída. Para aceder ao valor de saída de outro recurso dentro do modelo, utilize:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Trabalhar com saídas do script CLI

Diferente do script de implementação powerShell, o suporte CLI/bash não expõe uma variável comum para armazenar saídas de script, em vez disso, existe uma variável ambiental chamada **AZ_SCRIPTS_OUTPUT_PATH** que armazena o local onde o ficheiro de saídas do script reside. Se um script de implementação for executado a partir de um modelo de Gestor de Recursos, esta variável ambiental é definida automaticamente para si pela concha bash.

As saídas de scriptde de implantação devem ser guardadas na localização AZ_SCRIPTS_OUTPUT_PATH, e as saídas devem ser um objeto de corda JSON válido. O conteúdo do ficheiro deve ser guardado como um par de valor-chave. Por exemplo, uma série de cordas é armazenada como { "MyResult": [ "foo", "bar"] }.  Armazenar apenas os resultados da matriz, por exemplo , "foo", "bar" ], é inválido.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json?range=1-44)]

[jq](https://stedolan.github.io/jq/) é usado na amostra anterior. Vem com as imagens do contentor. Ver Ambiente de [desenvolvimento configure](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Desenvolver scripts de implantação

### <a name="handle-non-terminating-errors"></a>Lidar com erros não terminadores

Pode controlar a forma como o PowerShell reage a erros não terminadores utilizando a [**variável $ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) no seu script de implementação. O motor de script de implantação não define/altera o valor.  Apesar do valor que definiu para $ErrorActionPreference, o script de implementação define o estado de fornecimento de recursos para *Failed* quando o script encontra um erro.

### <a name="pass-secured-strings-to-deployment-script"></a>Passe cordas seguras para o script de implementação

A definição de variáveis ambientais nos casos do seu recipiente permite-lhe fornecer uma configuração dinâmica da aplicação ou script executada pelo recipiente. O script de implantação trata de variáveis ambientais não seguras e seguras da mesma forma que a Instância do Contentor Azure. Para obter mais informações, consulte As [variáveis ambiente definidas em instâncias de contentores](../../container-instances/container-instances-environment-variables.md#secure-values).

## <a name="debug-deployment-scripts"></a>Scripts de implementação de depurados

O serviço de script cria uma [conta de armazenamento](../../storage/common/storage-account-overview.md) e uma instância de [contentores](../../container-instances/container-instances-overview.md) para a execução do script. Ambos os recursos têm os **azscripts** sufixos nos nomes de recursos.

![Nomes de script de script de implementação de modelo de gestor de recursos de gestor de recursos de recursos](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

O script do utilizador, os resultados da execução e o ficheiro stdout são armazenados nas partilhas de ficheiros da conta de armazenamento. Há uma pasta chamada **azscripts**. Na pasta, existem mais duas pastas para a entrada e os ficheiros de saída: **azscriptinção** e **saída azscript**.

A pasta de saída contém um **resultado de execução.json** e o ficheiro de saída do script. Pode ver a mensagem de erro de execução do script em **execução resulte.json**. O ficheiro de saída só é criado quando o script é executado com sucesso. A pasta de entrada contém um ficheiro de script PowerShell do sistema e os ficheiros de script de implementação do utilizador. Pode substituir o ficheiro de script de implementação do utilizador por um revisto e reexecutar o script de implementação a partir da instância do contentor Azure.

Você pode obter a informação de implementação de recursos de script de implementação a nível do grupo de recursos e o nível de subscrição usando a API REST:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

O exemplo que se segue utiliza o [ARMClient:](https://github.com/projectkudu/ARMClient)

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

O resultado é semelhante a:

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-status.json?range=1-48)]

A saída mostra o estado de implantação e os IDs de recursos de script de implantação.

O seguinte REST API devolve o registo:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Só funciona antes de os recursos do script de implantação serem eliminados.

Para ver o recurso de implementação Scripts no portal, selecione **Tipos ocultos**:

![Script de implementação de modelo de gestor de recursos, mostrar tipos ocultos, portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Limpar os recursos do script de implementação

O script de implementação cria uma conta de armazenamento e uma instância de contentorque que são usadas para executar scripts de implementação e armazenar informações de depuração. Estes dois recursos são criados no mesmo grupo de recursos que os recursos provisionados, e serão eliminados pelo serviço de script quando o script expirar. Pode controlar o ciclo de vida destes recursos.  Até que sejam apagados, é cobrado por ambos os recursos. Para obter informações sobre os preços dos preços, consulte os preços dos casos de [contentores](https://azure.microsoft.com/pricing/details/container-instances/) e os preços de [armazenamento do Azure.](https://azure.microsoft.com/pricing/details/storage/)

O ciclo de vida destes recursos é controlado pelas seguintes propriedades no modelo:

- **limpezaPreferência**: Limpe a preferência quando a execução do script estiver em estado terminal.  Os valores suportados são:

  - **Sempre:** Apagar os recursos assim que a execução do guião entrar em estado terminal. Uma vez que o recurso de implementação Scripts ainda pode estar presente após a limpeza dos recursos, o script do sistema copiaria os resultados da execução do script, por exemplo, stdout, saídas, valor de retorno, etc. para DB antes de os recursos serem eliminados.
  - **OnSuccess**: Elimine os recursos apenas quando a execução do script for bem sucedida. Ainda pode aceder aos recursos para encontrar a informação de depuração.
  - **OnExpiração**: Elimine os recursos apenas quando a definição de intervalo de **retenção** estiver expirada. Esta propriedade está atualmente desativada.

- intervalo de **retençãoIntervalo**: Especifique o intervalo de tempo de que um recurso de script será retido e após o qual será expirado e eliminado.

> [!NOTE]
> Não é aconselhável utilizar os recursos do script de implantação para outros fins.

## <a name="run-script-more-than-once"></a>Executar o script mais de uma vez

A execução do guião de implantação é uma operação idempotente. Se nenhuma das propriedades de recursos dos Scripts de implementação (incluindo o script inline) for alterada, o script não será executado quando reimplantar o modelo. O serviço de script de implementação compara os nomes de recursos no modelo com os recursos existentes no mesmo grupo de recursos. Existem duas opções se quiser executar o mesmo script de implementação várias vezes:

- Altere o nome do seu recurso de implantaçãoScripts. Por exemplo, utilize a função do modelo [utcNow](./template-functions-string.md#utcnow) como nome de recurso ou como parte do nome do recurso. Mudar o nome do recurso cria um novo recurso de implementação Scripts. É bom para manter um histórico de execução de guião.

    > [!NOTE]
    > A função utcNow só pode ser utilizada no valor padrão para um parâmetro.

- Especifique um valor diferente na propriedade do modelo `forceUpdateTag`.  Por exemplo, use utcNow como valor.

> [!NOTE]
> Escreva os scripts de implantação que são idempotentes. Isto garante que, se voltarem a funcionar acidentalmente, não provocará alterações no sistema. Por exemplo, se o script de implementação for usado para criar um recurso Azure, verifique se o recurso não existe antes de o criar, para que o script tenha sucesso ou não volte a criar o recurso.

## <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

Pode utilizar uma imagem de recipiente de estiva pré-configurada como ambiente de desenvolvimento do script de implementação. O procedimento seguinte mostra como configurar a imagem do estivador no Windows. Para Linux e Mac, você pode encontrar a informação na Internet.

1. Instale [o Docker Desktop](https://www.docker.com/products/docker-desktop) no seu computador de desenvolvimento.
1. Open Docker Desktop.
1. Selecione o ícone do Docker Desktop a partir de barras de tarefas e, em seguida, selecione **Definições**.
1. Selecione **Unidades Partilhadas,** selecione uma unidade local que deseja estar disponível para os seus recipientes e, em seguida, selecione **Aplicar**

    ![Unidade de script de implementação de script de gestor de recursos](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Introduza as credenciais das janelas no momento.
1. Abra uma janela de terminal, seja o Comando Prompt ou o Windows PowerShell (não utilize o PowerShell ISE).
1. Puxe a imagem do recipiente do script de implementação para o computador local:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    O exemplo utiliza a versão PowerShell 2.7.0.

    Para retirar uma imagem CLI de um Registo de Contentores da Microsoft (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Este exemplo utiliza a versão CLI 2.0.80. O script de implementação utiliza as imagens de contentores CLI padrão encontradas [aqui](https://hub.docker.com/_/microsoft-azure-cli).

1. Execute a imagem do estivador localmente.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Substitua **&lt;carta do condutor anfitrião>** e&lt;o nome do **diretório anfitrião>** com uma pasta existente na unidade partilhada.  Mapeia a pasta para a pasta **/dados** no recipiente. Por exemplo, para mapear D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **- significa** manter a imagem do recipiente viva.

    Um exemplo cli:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Selecione **Partilhe-o** quando receber um pedido.
1. A imagem que se segue mostra como executar um script PowerShell, dado que tem um ficheiro helloworld.ps1 na pasta d:\docker.

    ![Modelo de design de gestor de recursos script de implementação cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Depois de o script ser testado com sucesso, pode usá-lo como um script de implementação.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar scripts de implantação. Para percorrer um tutorial de script de implantação:

> [!div class="nextstepaction"]
> [Tutorial: Use scripts de implementação em modelos de Gestor de Recursos Azure](./template-tutorial-deployment-script.md)