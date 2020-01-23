---
title: Usar scripts de implantação de modelo | Microsoft Docs
description: Saiba como usar scripts de implantação em modelos de Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/09/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 459d75bec3d4b4d0cf9057e0c6de238e7f165bfb
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548990"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Tutorial: usar scripts de implantação para criar um certificado autoassinado (versão prévia)

Saiba como usar scripts de implantação no recurso do Azure gerenciar modelos. Os scripts de implantação podem ser usados para executar etapas personalizadas que não podem ser feitas por modelos do Resource Manager. Por exemplo, criar um certificado autoassinado.  Neste tutorial, você cria um modelo para implantar um cofre de chaves do Azure e, em seguida, usa um recurso `Microsoft.Resources/deploymentScripts` no mesmo modelo para criar um certificado e, em seguida, adicionar o certificado ao cofre de chaves. Para saber mais sobre o script de implantação, consulte [usar scripts de implantação em modelos de Azure Resource Manager](./deployment-script-template.md).

> [!NOTE]
> O script de implantação está atualmente em visualização. Para usá-lo, você deve [se inscrever para a versão prévia](https://aka.ms/armtemplatepreviews).

> [!IMPORTANT]
> Dois recursos de script de implantação, uma conta de armazenamento e uma instância de contêiner, são criados no mesmo grupo de recursos para execução de script e solução de problemas. Esses recursos geralmente são excluídos pelo serviço de script quando a execução do script entra em um estado terminal. Você será cobrado pelos recursos até que os recursos sejam excluídos. Para saber mais, confira [limpar recursos de script de implantação](./deployment-script-template.md#clean-up-deployment-script-resources).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Editar o modelo
> * Implementar o modelo
> * Depurar o script com falha
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* **[Visual Studio Code](https://code.visualstudio.com/) com a extensão de ferramentas do Resource Manager**. Consulte [usar Visual Studio Code para criar modelos de Azure Resource Manager](./use-vs-code-to-create-template.md).

* **Uma identidade gerenciada atribuída pelo usuário com a função do colaborador no nível da assinatura**. Essa identidade é usada para executar scripts de implantação. Para criar um, consulte [identidade gerenciada atribuída pelo usuário](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity). Você precisa da ID de identidade ao implantar o modelo. O formato da identidade é:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Use o seguinte script do PowerShell para obter a ID fornecendo o nome do grupo de recursos e o nome da identidade.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Em vez de criar um modelo de raiz, pode abrir um modelo dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/). Os modelos de início rápido do Azure são um repositório para modelos do Resource Manager.

O modelo usado neste guia de início rápido é chamado [criar um Azure Key Vault e um segredo](https://azure.microsoft.com/resources/templates/101-key-vault-create/). O modelo cria um cofre de chaves e, em seguida, adiciona um segredo ao cofre de chaves.

1. No Visual Studio Code, selecione **Ficheiro**>**Abrir Ficheiro**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Selecione **Abrir** para abrir o ficheiro.
4. Selecione **Ficheiro**>**Guardar Como** para guardar o ficheiro como **azuredeploy.json** no computador local.

## <a name="edit-the-template"></a>Editar o modelo

Faça as seguintes alterações no modelo:

### <a name="clean-up-the-template-optional"></a>Limpar o modelo (opcional)

O modelo original adiciona um segredo ao cofre de chaves.  Para simplificar o tutorial, remova o seguinte recurso:

* **Microsoft. keyvault/cofres/segredos**

Remova as duas definições de parâmetro a seguir:

* **secretName**
* **segredo**

Se você optar por não remover essas definições, será necessário especificar os valores de parâmetro durante a implantação.

### <a name="configure-the-key-vault-access-policies"></a>Configurar as políticas de acesso do cofre de chaves

O script de implantação adiciona um certificado ao cofre de chaves. Configure as políticas de acesso do cofre de chaves para conceder a permissão para a identidade gerenciada:

1. Adicione um parâmetro para obter a ID de identidade gerenciada:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > A extensão do modelo do Gerenciador de recursos do Visual Studio Code não é capaz de Formatar scripts de implantação ainda. Não use [SHIFT] + [ALT] + F para formatar os recursos de deploymentScripts, como o seguinte.

1. Adicione um parâmetro para configurar as políticas de acesso do cofre de chaves para que a identidade gerenciada possa adicionar certificados ao cofre de chaves.

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

1. Atualize as políticas de acesso existentes do cofre de chaves para:

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

    Há duas políticas definidas, uma para o usuário conectado e a outra é para a identidade gerenciada.  O usuário conectado só precisa da permissão de *lista* para verificar a implantação.  Para simplificar o tutorial, o mesmo certificado é atribuído à identidade gerenciada e aos usuários conectados.

### <a name="add-the-deployment-script"></a>Adicionar o script de implantação

1. Adicione três parâmetros que são usados pelo script de implantação.

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

1. Add a deploymentScripts resource:

    > [!NOTE]
    > Because the inline deployment scripts are enclosed in double quotes, the strings inside the deployment scripts need to be enclosed in single quotes instead. The escape character for PowerShell is **&#92;**.

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
        "azPowerShellVersion": "2.8",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an arguement string, double quotes must be escaped
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

    O recurso `deploymentScripts` depende do recurso do Key Vault e do recurso de atribuição de função.  Ele tem estas propriedades:

    * **identidade**: o script de implantação usa uma identidade gerenciada atribuída pelo usuário para executar os scripts.
    * **tipo**: especifique o tipo de script. Atualmente, somente o script do PowerShell tem suporte.
    * **forceUpdateTag**: Determine se o script de implantação deve ser executado mesmo se a origem do script não tiver sido alterada. Pode ser carimbo de data/hora atual ou um GUID. Para saber mais, veja [Executar script mais de uma vez](./deployment-script-template.md#run-script-more-than-once).
    * **azPowerShellVersion**: especifica a versão do módulo Azure PowerShell a ser usada. Atualmente, o script de implantação dá suporte à versão 2.7.0, 2.8.0 e 3.0.0.
    * **tempo limite**: especifique o tempo de execução máximo permitido do script especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor padrão é **P1D**.
    * **argumentos**: Especifique os valores de parâmetro. Os valores são separados por espaços.
    * **scriptContent**: especifique o conteúdo do script. Para executar um script externo, use **primaryScriptURI** em vez disso. Para obter mais informações, consulte [usar script externo](./deployment-script-template.md#use-external-scripts).
        A declaração de **$DeploymentScriptOutputs** só é necessária ao testar o script em um computador local. Declarar a variável permite que o script seja executado em um computador local e em um recurso deploymentScript sem precisar fazer alterações. O valor atribuído a $DeploymentScriptOutputs está disponível como saídas nas implantações. Para obter mais informações, consulte [trabalhar com saídas de scripts de implantação](./deployment-script-template.md#work-with-outputs-from-deployment-scripts).
    * **cleanupPreference**: Especifique a preferência em quando excluir os recursos de script de implantação.  O valor padrão é **sempre**, o que significa que os recursos de script de implantação são excluídos, apesar do estado do terminal (êxito, falha, cancelado). Neste tutorial, **OnSuccess** é usado para que você tenha a oportunidade de exibir os resultados da execução do script.
    * **retentionInterval**: especifique o intervalo para o qual o serviço retém os recursos de script depois que ele atinge um estado de terminal. Os recursos serão excluídos quando essa duração expirar. A duração é baseada no padrão ISO 8601. Este tutorial usa P1D, que significa um dia.  Essa propriedade é usada quando **cleanupPreference** está definido como **ontermination**. Esta propriedade não está habilitada no momento.

    O script de implantação usa três parâmetros: nome do Key Vault, nome do certificado e nome da entidade.  Ele cria um certificado e, em seguida, adiciona o certificado ao cofre de chaves.

    **$DeploymentScriptOutputs** é usado para armazenar o valor de saída.  Para saber mais, confira [trabalhar com saídas de scripts de implantação](./deployment-script-template.md#work-with-outputs-from-deployment-scripts).

    O modelo concluído pode ser encontrado [aqui](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json).

1. Para ver o processo de depuração, coloque um erro no código adicionando a seguinte linha ao script de implantação:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    O comando correto é **Write-Output** em vez de **Write-Saída1**.

1. Selecione **Ficheiro**>**Guardar** para guardar o ficheiro.

## <a name="deploy-the-template"></a>Implementar o modelo

Consulte a seção [implantar o modelo](./quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template) no guia de início rápido do Visual Studio Code para abrir o Cloud Shell e carregar o arquivo de modelo no Shell. Em seguida, execute o seguinte script do PowerShell:

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

O serviço de script de implantação precisa criar recursos de script de implantação adicionais para execução de script. A preparação e o processo de limpeza podem levar até um minuto para serem concluídos, além do tempo de execução do script real.

A implantação falhou devido ao comando inválido, **Write-Saída1** é usado no script. Você deverá receber um erro dizendo:

```error
The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
```

O resultado da execução do script de implantação é armazenado nos recursos de script de implantação para a finalidade da solução de problemas.

## <a name="debug-the-failed-script"></a>Depurar o script com falha

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos. É o nome do projeto com **RG** acrescentado. Você deverá ver dois recursos adicionais no grupo de recursos. Esses recursos são chamados de *recursos de script de implantação*.

    ![Recursos de script de implantação de modelo do Resource Manager](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Ambos os arquivos têm o sufixo **azscripts** . Uma é uma conta de armazenamento e a outra é uma instância de contêiner.

    Selecione **Mostrar tipos ocultos** para listar o recurso deploymentScripts.

1. Selecione a conta de armazenamento com o sufixo **azscripts** .
1. Selecione o bloco **compartilhamentos de arquivos** . Você deverá ver uma pasta **azscripts** .  A pasta contém os arquivos de execução de script de implantação.
1. Selecione **azscripts**. Você deverá ver duas pastas **azscriptinput** e **azscriptoutput**.  A pasta de entrada contém um arquivo de script do System PowerShell e os arquivos de script de implantação do usuário. A pasta de saída contém um **ExecutionResult. JSON** e o arquivo de saída de script. Você pode ver a mensagem de erro em **ExecutionResult. JSON**. O arquivo de saída não está lá porque a execução falhou.

Remova a linha **Write-Saída1** e reimplante o modelo.

Quando a segunda implantação for executada com êxito, os recursos de script de implantação deverão ser removidos pelo serviço de script, pois a propriedade **cleanupPreference** está definida como **OnSuccess**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a usar o script de implantação em modelos de Azure Resource Manager. Para saber como implementar recursos do Azure com base em condições, veja:

> [!div class="nextstepaction"]
> [Condições de utilização](./template-tutorial-use-conditions.md)
