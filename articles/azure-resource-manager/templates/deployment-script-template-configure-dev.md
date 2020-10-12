---
title: Configurar ambiente de desenvolvimento para scripts de implantação em modelos Microsoft Docs
description: configurar ambiente de desenvolvimento para scripts de implementação em modelos Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: jgao
ms.openlocfilehash: 232a1ae5d125a2ea1d5723e85073fb3dd02420cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87294475"
---
# <a name="configure-development-environment-for-deployment-scripts-in-templates-preview"></a>Configure o ambiente de desenvolvimento para scripts de implantação em modelos (Pré-visualização)

Aprenda a criar um ambiente de desenvolvimento para desenvolver e testar scripts de implementação com uma imagem de script de implementação. Pode criar a instância do [recipiente Azure](../../container-instances/container-instances-overview.md) ou utilizar [o Docker.](https://docs.docker.com/get-docker/) Ambos estão cobertos neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver um script de implementação, pode criar um ficheiro **hello.ps1** com o seguinte conteúdo:

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

## <a name="use-azure-container-instance"></a>Use a instância do recipiente Azure

Para escrever os seus scripts no seu computador, precisa de criar uma conta de armazenamento e montar a conta de armazenamento na instância do recipiente. Para que possa fazer o upload do seu script para a conta de armazenamento e executar o script na instância do recipiente.

> [!NOTE]
> A conta de armazenamento que cria para testar o seu script não é a mesma conta de armazenamento que o serviço de script de implementação utiliza para executar o script. O serviço de scripts de implementação cria um nome único como partilha de ficheiros em cada execução.

### <a name="create-an-azure-container-instance"></a>Criar uma instância de recipiente Azure

O modelo ARM a seguir cria uma instância de contentor e uma partilha de ficheiros e, em seguida, monta a partilha de ficheiros na imagem do recipiente.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "deploymentScript",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('projectName'), 'store')]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```
O valor predefinido para a trajetória de montagem é **implementaçãoScript**.  Este é o caminho no caso do recipiente onde é montado na partilha de ficheiros.

A imagem do recipiente predefinido especificada no modelo é **mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3"**.  Para obter uma lista de versões Azure PowerShell suportadas e versões Azure CLI, consulte [Azure PowerShell ou Azure CLI](./deployment-script-template.md#prerequisites).

O gabarito suspende a instância do recipiente em 1800 segundos. Tem 30 minutos antes da instância do contentor entrar em estado terminal e a sessão termina.

Para implementar o modelo:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-azResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-deployment-script"></a>Script de implementação de upload

Faça o upload do seu script de implementação para a conta de armazenamento. Segue-se um exemplo powerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

Também pode fazer o upload do ficheiro utilizando o portal Azure e o Azure CLI.

### <a name="test-the-deployment-script"></a>Teste o script de implementação

1. A partir do portal Azure, abra o grupo de recursos onde implantou a instância do contentor e a conta de armazenamento.
1. Abra o grupo de contentores. O nome do grupo do recipiente predefinido é o nome do projeto com **cg** anexado. Verá que a instância do contentor está no estado **de Marcha.**
1. Selecione **Recipientes** do menu esquerdo. Verá uma instância de contentor.  O nome da instância do recipiente é o nome do projeto com **o recipiente** anexado.

    ![instância de ligação de script de implantação](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Selecione **Connect**e, em seguida, selecione **Connect**. Se não conseguir ligar-se à instância do recipiente, reinicie o grupo de contentores e tente novamente.
1. No painel de consola, executar os seguintes comandos:

    ```
    cd deploymentScript
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    A saída é **Hello John Dole.**

    ![teste de instância de contentor de script de implantação](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-docker"></a>Use Docker

Você pode usar uma imagem de recipiente de estiva pré-configurado como o seu ambiente de desenvolvimento de scripts de implementação. Para instalar o Docker, consulte [get Docker.](https://docs.docker.com/get-docker/)
Também é necessário configurar a partilha de ficheiros para montar o diretório, que contém os scripts de implantação no contentor Docker.

1. Puxe a imagem do contentor do script de implementação para o computador local:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    O exemplo utiliza a versão PowerShell 4.3.0.

    Para retirar uma imagem CLI de um registo de contentores da Microsoft (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Este exemplo utiliza a versão CLI 2.0.80. O script de implementação utiliza as imagens padrão dos recipientes CLI encontrados [aqui](https://hub.docker.com/_/microsoft-azure-cli).

1. Executar a imagem do estivador localmente.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Substitua ** &lt; a carta do anfitrião>** e o nome do ** &lt; diretório de anfitriões>** por uma pasta existente na unidade partilhada.  Mapeia a pasta para a pasta **/dados** no recipiente. Por exemplo, para mapear D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-significa** manter a imagem do contentor viva.

    Um exemplo CLI:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. A imagem que se segue mostra como executar um script PowerShell, dado que tem um ficheiro helloworld.ps1 na unidade partilhada.

    ![Modelo de implementação de modelo de gestor de recursos estivador cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Depois de o script ser testado com sucesso, pode usá-lo como um script de implementação nos seus modelos.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar scripts de implantação. Para percorrer um tutorial de script de implementação:

> [!div class="nextstepaction"]
> [Tutorial: Use scripts de implementação em modelos de Gestor de Recursos Azure](./template-tutorial-deployment-script.md)
