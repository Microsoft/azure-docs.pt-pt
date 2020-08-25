---
title: Use scripts de implementação de modelos Microsoft Docs
description: Saiba como usar scripts de implementação em modelos do Gestor de Recursos Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e1094befcc6b3a6e9d56ba3b603dc45fcb91ba13
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88825499"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Tutorial: Utilize scripts de implantação para criar um certificado auto-assinado (Pré-visualização)

Saiba como utilizar scripts de implementação em modelos Azure Resource Manage (ARM). Os scripts de implementação podem ser usados para executar passos personalizados que não podem ser feitos por modelos ARM. Por exemplo, criar um certificado auto-assinado.  Neste tutorial, você cria um modelo para implantar um cofre de chave Azure, e em seguida, usar um `Microsoft.Resources/deploymentScripts` recurso no mesmo modelo para criar um certificado e, em seguida, adicionar o certificado ao cofre de chaves. Para saber mais sobre o script de implementação, consulte [use scripts de implementação em modelos ARM](./deployment-script-template.md).

> [!IMPORTANT]
> Dois recursos de script de implantação, uma conta de armazenamento e uma instância de contentor, são criados no mesmo grupo de recursos para a execução do script e resolução de problemas. Estes recursos são geralmente eliminados pelo serviço de scripts quando a execução do script entra num estado terminal. É cobrado pelos recursos até que os recursos sejam apagados. Para saber mais, consulte [Limpar os recursos do script de implementação](./deployment-script-template.md#clean-up-deployment-script-resources).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Editar o modelo
> * Implementar o modelo
> * Depurar o roteiro falhado
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* ** [Código de Estúdio Visual](https://code.visualstudio.com/) com a extensão de Ferramentas do Gestor de Recursos**. Consulte [Quickstart: Crie modelos de Gestor de Recursos Azure com Código de Estúdio Visual](./quickstart-create-templates-use-visual-studio-code.md).

* **Uma identidade gerida atribuída pelo utilizador com o papel do contribuinte ao nível da subscrição**. Esta identidade é usada para executar scripts de implantação. Para criar uma, consulte a [identidade gerida atribuída pelo Utilizador](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Precisa da identificação de identidade quando implementar o modelo. O formato da identidade é:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Utilize o seguinte script CLI para obter o ID fornecendo o nome do grupo de recursos e o nome de identidade.

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  az identity list -g $resourceGroupName
  ```

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Em vez de criar um modelo de raiz, pode abrir um modelo dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/). Azure Quickstart Templates é um repositório para modelos ARM.

O modelo utilizado neste quickstart chama-se [Criar um Cofre de Chaves Azure e um segredo](https://azure.microsoft.com/resources/templates/101-key-vault-create/). O modelo cria um cofre chave, e depois adiciona um segredo ao cofre da chave.

1. A partir do Código do Estúdio Visual, selecione Ficheiro Aberto **de** > **Ficheiros**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Selecione **Abrir** para abrir o ficheiro.
4. Selecione **File** > **'Guardar ficheiros' para** guardar o ficheiro à medida **queazuredeploy.jsno** computador local.

## <a name="edit-the-template"></a>Editar o modelo

Faça as seguintes alterações ao modelo:

### <a name="clean-up-the-template-optional"></a>Limpe o modelo (opcional)

O modelo original adiciona um segredo ao cofre da chave.  Para simplificar o tutorial, remova o seguinte recurso:

* **Microsoft.KeyVault/vaults/secrets**

Remover as duas definições de parâmetros seguintes:

* **nome secreto**
* **secretValue**

Se optar por não remover estas definições, tem de especificar os valores dos parâmetros durante a implantação.

### <a name="configure-the-key-vault-access-policies"></a>Configure as principais políticas de acesso ao cofre

O roteiro de implantação adiciona um certificado ao cofre da chave. Configure as principais políticas de acesso ao cofre para dar a permissão à identidade gerida:

1. Adicione um parâmetro para obter o ID de identidade gerido:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > A extensão do modelo do Gestor de Recursos do Código do Estúdio Visual ainda não é capaz de formatar scripts de implementação. Não utilize [SHIFT]+[ALT]+F para formatar os recursos de implementaçãoScripts, como o seguinte.

1. Adicione um parâmetro para configurar as políticas de acesso ao cofre chave para que a identidade gerida possa adicionar certificados ao cofre da chave.

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Atualize as políticas de acesso ao cofre de chaves existentes para:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Há duas políticas definidas, uma para o utilizador inscrito, e a outra para a identidade gerida.  O utilizador inscrito só precisa da permissão da *lista* para verificar a implementação.  Para simplificar o tutorial, o mesmo certificado é atribuído tanto à identidade gerida como aos utilizadores inscritos.

### <a name="add-the-deployment-script"></a>Adicione o script de implementação

1. Adicione três parâmetros que são usados pelo script de implementação.

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. Adicionar um recurso de implementaçãoScripts:

    > [!NOTE]
    > Como os scripts de implementação inline são incluídos em citações duplas, as cordas dentro dos scripts de implementação precisam de ser incluídas em cotações únicas. O personagem de fuga da PowerShell é **&#92;**.

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    O `deploymentScripts` recurso depende do recurso chave do cofre e do recurso de atribuição de funções.  Possui estas propriedades:

    * **identidade**: O script de implementação utiliza uma identidade gerida atribuída pelo utilizador para executar os scripts.
    * **tipo**: Especificar o tipo de script. Atualmente, apenas o script PowerShell é suporte.
    * **forceUpdateTag**: Determine se o script de implementação deve ser executado mesmo que a fonte do script não tenha mudado. Pode ser a hora atual ou um GUID. Para saber mais, consulte [o roteiro run mais de uma vez](./deployment-script-template.md#run-script-more-than-once).
    * **azPowerShellVersion**: Especifica a versão do módulo Azure PowerShell a ser utilizada. Atualmente, o script de implementação suporta a versão 2.7.0, 2.8.0 e 3.0.0.
    * **tempo limite**: Especifique o tempo máximo de execução do script permitido especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor predefinido é **P1D.**
    * argumentos : Especificar os **valores**dos parâmetros. Os valores são separados por espaços.
    * **scriptContent**: Especifique o conteúdo do script. Para executar um script externo, use **o PrimaryScriptURI** em vez disso. Para obter mais informações, consulte [utilizar o script externo](./deployment-script-template.md#use-external-scripts).
        Declarar **$DeploymentScriptOutputs** só é necessário quando se testa o script numa máquina local. Declarar a variável permite que o script seja executado numa máquina local e num recurso de implementaçãoScript sem ter de fazer alterações. O valor atribuído a $DeploymentScriptOutputs está disponível como saídas nas implementações. Para obter mais informações, consulte [Trabalhar com saídas de scripts de implementação PowerShell](./deployment-script-template.md#work-with-outputs-from-powershell-script) ou [trabalhar com saídas a partir de scripts de implementação CLI](./deployment-script-template.md#work-with-outputs-from-cli-script).
    * **limpezaPreferência**: Especifique a preferência sobre quando eliminar os recursos do script de implantação.  O valor predefinido é **Sempre**, o que significa que os recursos de script de implantação são eliminados apesar do estado terminal (Bem sucedido, Falhado, cancelado). Neste tutorial, **o OnSuccess** é utilizado para que tenha a oportunidade de ver os resultados da execução do script.
    * **retençãoInterval**: Especifique o intervalo para o qual o serviço mantém os recursos de script depois de atingir um estado terminal. Os recursos serão eliminados quando esta duração expirar. A duração baseia-se no padrão ISO 8601. Este tutorial usa P1D, o que significa um dia.  Esta propriedade é usada quando **a limpezaPreferência** é definida para **OnExpiration**. Esta propriedade não está ativada atualmente.

    O script de implementação tem três parâmetros: nome do cofre chave, nome do certificado e nome do sujeito.  Cria um certificado e, em seguida, adiciona o certificado ao cofre da chave.

    **$DeploymentScriptOutputs** é usado para armazenar o valor de saída.  Para saber mais, consulte [Trabalhar com saídas a partir de scripts de implementação PowerShell](./deployment-script-template.md#work-with-outputs-from-powershell-script) ou [trabalhar com saídas a partir de scripts de implementação CLI](./deployment-script-template.md#work-with-outputs-from-cli-script).

    O modelo completo pode ser encontrado [aqui.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

1. Para ver o processo de depuração, coloque um erro no código adicionando a seguinte linha ao script de implementação:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    O comando correto é **Write-Output** em vez de **Write-Output1**.

1. Selecione **Guardar ficheiros** > **Save** para guardar o ficheiro.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Inscreva-se na [Azure Cloud Shell](https://shell.azure.com)

1. Escolha o seu ambiente preferido selecionando **PowerShell** ou **Bash** (para CLI) no canto superior esquerdo.  É necessário reiniciar o Shell quando mudar.

    ![Arquivo de upload do portal Azure Cloud Shell](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecione **Carregar/transferir ficheiros** e, em seguida, selecione **Carregar**. Veja a captura de ecrã anterior.  Selecione o ficheiro que guardou na secção anterior. Depois de carregar o ficheiro, pode utilizar o comando **ls** e o comando do **gato** para verificar se o ficheiro é carregado com sucesso.

1. Executar o seguinte script PowerShell para implementar o modelo.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
    $identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

    $adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
    $resourceGroupName = "${projectName}rg"
    $keyVaultName = "${projectName}kv"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

    Write-Host "Press [ENTER] to continue ..."
    ```

    O serviço de scripts de implementação precisa de criar recursos de script de implementação adicionais para a execução do script. A preparação e o processo de limpeza podem demorar até um minuto para ser concluído, além do tempo real de execução do script.

    A implementação é falhada devido ao comando inválido, **Write-Output1** é usado no script. Terá um erro dizendo:

    ```error
    The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
    program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
    ```

    O resultado da execução do script de implementação é armazenado nos recursos de script de implementação para o propósito de resolução de problemas.

## <a name="debug-the-failed-script"></a>Depurar o roteiro falhado

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos. É o nome do projeto com **rg** anexado. Verá dois recursos adicionais no grupo de recursos. Estes recursos são referidos como *recursos de script de implantação.*

    ![Recursos de script de implementação de modelo de gestor de recursos](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Ambos os ficheiros têm o sufixo **azscripts.** Um é uma conta de armazenamento e o outro é um caso de contentor.

    Selecione **Mostrar tipos ocultos** para listar o recurso de implementaçãoScripts.

1. Selecione a conta de armazenamento com o sufixo de **azscripts.**
1. Selecione o **azulejo de partilha de fichas.** Verá uma pasta **de azscripts.**  A pasta contém os ficheiros de execução do script de implementação.
1. Selecione **azscripts**. Verá duas pastasL **azscriptinput** e **azscriptout .**  A pasta de entrada contém um ficheiro de script powerShell do sistema e os ficheiros de script de implementação do utilizador. A pasta de saída contém um **executionresult.jsligado** e o ficheiro de saída do script. Pode ver a mensagem de erro **executionresult.jsligado**. O ficheiro de saída não está lá porque a execução falhou.

Remova a linha **Write-Output1** e reimplante o modelo.

Quando a segunda implantação for executada com sucesso, os recursos de script de implantação serão removidos pelo serviço de script, porque a propriedade **de limpezaPreference** é definida para **OnSuccess**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar o script de implementação em modelos ARM. Para saber como implementar recursos do Azure com base em condições, veja:

> [!div class="nextstepaction"]
> [Condições de utilização](./template-tutorial-use-conditions.md)
