---
title: Use scripts de implementação de modelos / Microsoft Docs
description: Saiba como usar scripts de implementação em modelos de Gestor de Recursos Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2b4b94c05b39dddcef83644638a105d5b6c75118
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184984"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Tutorial: Utilize scripts de implementação para criar um certificado auto-assinado (Pré-visualização)

Saiba como utilizar scripts de implementação em modelos de Gestão de Recursos Azure (ARM). Scripts de implementação podem ser usados para executar passos personalizados que não podem ser feitos por modelos ARM. Por exemplo, criar um certificado auto-assinado.  Neste tutorial, você cria um modelo para implantar um cofre `Microsoft.Resources/deploymentScripts` de chave Azure, e depois usa um recurso no mesmo modelo para criar um certificado e, em seguida, adicionar o certificado ao cofre chave. Para saber mais sobre o script de implementação, consulte [use scripts de implementação em modelos ARM](./deployment-script-template.md).

> [!IMPORTANT]
> Dois recursos de script de implementação, uma conta de armazenamento e uma instância de contentores, são criados no mesmo grupo de recursos para execução de scripts e resolução de problemas. Estes recursos são geralmente eliminados pelo serviço de script quando a execução do script entra em estado terminal. Você é cobrado pelos recursos até que os recursos sejam eliminados. Para saber mais, consulte [a Limpeza dos recursos do script de implementação.](./deployment-script-template.md#clean-up-deployment-script-resources)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Editar o modelo
> * Implementar o modelo
> * Depurar o guião falhado
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* ** [Código de estúdio visual](https://code.visualstudio.com/) com a extensão ferramentas de gestor de recursos**. Consulte [o Use Visual Studio Code para criar modelos ARM](./use-vs-code-to-create-template.md).

* Uma identidade gerida atribuída ao **utilizador com o papel do contribuinte ao nível da subscrição**. Esta identidade é usada para executar scripts de implantação. Para criar um, consulte [a identidade gerida atribuída pelo Utilizador.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity) Precisa da identificação de identidade quando implementar o modelo. O formato da identidade é:

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

Em vez de criar um modelo de raiz, pode abrir um modelo dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/). Os modelos Azure Quickstart é um repositório para modelos ARM.

O modelo usado neste quickstart chama-se [Criar um Cofre chave Azure e um segredo.](https://azure.microsoft.com/resources/templates/101-key-vault-create/) O modelo cria um cofre chave, e depois adiciona um segredo ao cofre da chave.

1. A partir do Código do Estúdio Visual, selecione **File**>**Open File**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Selecione **Abrir** para abrir o ficheiro.
4. Selecione **File**>**Save As** para guardar o ficheiro como **azuredeploy.json** para o seu computador local.

## <a name="edit-the-template"></a>Editar o modelo

Faça as seguintes alterações no modelo:

### <a name="clean-up-the-template-optional"></a>Limpar o modelo (opcional)

O modelo original adiciona um segredo ao cofre da chave.  Para simplificar o tutorial, remova o seguinte recurso:

* **Microsoft.KeyVault/cofres/segredos**

Remova as seguintes duas definições de parâmetro:

* **nome secreto**
* **secretValue**

Se optar por não remover estas definições, tem de especificar os valores do parâmetro durante a implementação.

### <a name="configure-the-key-vault-access-policies"></a>Configure as políticas de acesso ao cofre chave

O guião de implantação adiciona um certificado ao cofre da chave. Configure as políticas de acesso ao cofre chave para dar a permissão à identidade gerida:

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
    > A extensão do modelo do Modelo de Gestor de Recursos do Código do Estúdio Visual ainda não é capaz de formatar scripts de implementação. Não utilize [SHIFT]+[ALT]+F para formatar os recursos dos Scripts de implementação, como o seguinte.

1. Adicione um parâmetro para configurar as políticas de acesso ao cofre chave para que a identidade gerida possa adicionar certificados ao cofre chave.

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

1. Atualize as políticas de acesso ao cofre chave existentes para:

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

    Existem duas políticas definidas, uma para o utilizador inscrito, e a outra é para a identidade gerida.  O utilizador inscrito apenas necessita da permissão da *lista* para verificar a implementação.  Para simplificar o tutorial, o mesmo certificado é atribuído tanto à identidade gerida como aos utilizadores inscritos.

### <a name="add-the-deployment-script"></a>Adicione o script de implementação

1. Adicione três parâmetros que são utilizados pelo script de implantação.

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

1. Adicione um recurso de implementaçãoScripts:

    > [!NOTE]
    > Como os scripts de implementação em linha são fechados em citações duplas, as cordas dentro dos scripts de implementação precisam de ser fechadas em cotações únicas. O personagem de fuga para a PowerShell é **&#92;. **

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

    O `deploymentScripts` recurso depende do recurso chave do cofre e do recurso de atribuição de funções.  Tem estas propriedades:

    * **identidade**: O script de implementação utiliza uma identidade gerida atribuída pelo utilizador para executar os scripts.
    * **tipo:** Especificar o tipo de script. Atualmente, apenas o script PowerShell é suporte.
    * **forceUpdateTag**: Determine se o script de implementação deve ser executado mesmo que a fonte do script não tenha mudado. Pode ser um carimbo de tempo atual ou um GUID. Para saber mais, consulte [o guião run mais de uma vez](./deployment-script-template.md#run-script-more-than-once).
    * **azPowerShellVersion**: Especifica a versão do módulo PowerShell Azure a utilizar. Atualmente, o script de implementação suporta a versão 2.7.0, 2.8.0 e 3.0.0.
    * **prazo**: Especificar o tempo máximo de execução do script especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor predefinido é **P1D**.
    * **argumentos**: Especificar os valores dos parâmetros. Os valores são separados por espaços.
    * **scriptConteúdo**: Especifique o conteúdo do script. Para executar um script externo, utilize o **ScriptURI primário.** Para mais informações, consulte [Utilize o script externo](./deployment-script-template.md#use-external-scripts).
        Declarar **$DeploymentScriptOutputs** só é necessário quando se testa o guião numa máquina local. Declarar a variável permite que o script seja executado numa máquina local e num recurso de implementaçãoDo sem ter de fazer alterações. O valor atribuído a $DeploymentScriptOutputs está disponível como saídas nas implementações. Para mais informações, consulte [Trabalhar com saídas de scripts de implementação powerShell](./deployment-script-template.md#work-with-outputs-from-powershell-script) ou trabalhar com [saídas de scripts de implementação CLI](./deployment-script-template.md#work-with-outputs-from-cli-script).
    * **limpezaPreferência**: Especifique a preferência sobre quando eliminar os recursos do script de implementação.  O valor predefinido é **sempre**, o que significa que os recursos do script de implantação são eliminados apesar do estado terminal (Bem sucedido, falhado, cancelado). Neste tutorial, o **OnSuccess** é usado para que tenha a oportunidade de ver os resultados da execução do script.
    * intervalo de **retençãoIntervalo**: Especifique o intervalo para o qual o serviço mantém os recursos do script depois de atingir um estado terminal. Os recursos serão eliminados quando esta duração expirar. A duração baseia-se no padrão ISO 8601. Este tutorial usa P1D, o que significa um dia.  Esta propriedade é utilizada quando **a limpezaA preferência** está definida para **OnExpiration**. Esta propriedade não está ativada atualmente.

    O script de implantação tem três parâmetros: nome do cofre chave, nome do certificado e nome do assunto.  Cria um certificado e, em seguida, adiciona o certificado ao cofre chave.

    **$DeploymentScriptOutputs** é usado para armazenar o valor de saída.  Para saber mais, consulte [Trabalhar com saídas a partir de scripts de implementação powerShell](./deployment-script-template.md#work-with-outputs-from-powershell-script) ou [trabalhar com saídas de scripts de implementação CLI](./deployment-script-template.md#work-with-outputs-from-cli-script).

    O modelo completo pode ser encontrado [aqui.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

1. Para ver o processo de depuração, coloque um erro no código adicionando a seguinte linha ao script de implementação:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    O comando correto é **Write-Output** em vez de **Write-Output1**.

1. Selecione**Guardar** **ficheiros**>para guardar o ficheiro.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Inscreva-se na [Casca de Nuvem Azure](https://shell.azure.com)

1. Escolha o seu ambiente preferido selecionando **powerShell** ou **Bash** (para CLI) no canto superior esquerdo.  É necessário reiniciar o Shell quando mudar.

    ![Ficheiro de upload do portal Azure Cloud Shell](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecione **Carregar/transferir ficheiros** e, em seguida, selecione **Carregar**. Veja a captura de ecrã anterior.  Selecione o ficheiro que guardou na secção anterior. Depois de fazer o upload do ficheiro, pode utilizar o comando **ls** e o comando do **gato** para verificar se o ficheiro é carregado com sucesso.

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

    O serviço de script de implementação precisa de criar recursos de script de implementação adicionais para a execução do script. A preparação e o processo de limpeza podem demorar até um minuto para ser concluído, para além do tempo real de execução do script.

    A implementação é falhada devido ao comando inválido, **write-output1** é usado no script. Terá um erro dizendo:

    ```error
    The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
    program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
    ```

    O resultado da execução do script de implementação é armazenado nos recursos do script de implementação para o propósito de resolução de problemas.

## <a name="debug-the-failed-script"></a>Depurar o guião falhado

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos. É o nome do projeto com **rg** anexado. Verá dois recursos adicionais no grupo de recursos. Estes recursos são referidos como recursos de script de *implantação.*

    ![Recursos de script de implementação de modelo de gestor de recursos de gestor de recursos](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Ambos os ficheiros têm o sufixo dos **azscripts.** Um é uma conta de armazenamento e o outro é um caso de contentor.

    Selecione **Mostrar tipos ocultos** para listar o recurso de implementaçãoScripts.

1. Selecione a conta de armazenamento com o sufixo **azscripts.**
1. Selecione o azulejo **de partilhas do Arquivo.** Verá uma pasta **azscripts.**  A pasta contém os ficheiros de execução do script de implementação.
1. Selecione **azscripts**. Verá duas pastasL **azscriptinput** e **azscriptoutput**.  A pasta de entrada contém um ficheiro de script PowerShell do sistema e os ficheiros de script de implementação do utilizador. A pasta de saída contém um **resultado de execução.json** e o ficheiro de saída do script. Pode ver a mensagem de erro no resultado da **execução.json**. O ficheiro de saída não está lá porque a execução falhou.

Retire a linha **Write-Output1** e reimplante o modelo.

Quando a segunda implementação for executada com sucesso, os recursos do script de implementação serão removidos pelo serviço de script, porque a propriedade **cleanupPreference** está definida para **OnSuccess**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar o script de implantação em modelos ARM. Para saber como implementar recursos do Azure com base em condições, veja:

> [!div class="nextstepaction"]
> [Condições de utilização](./template-tutorial-use-conditions.md)
