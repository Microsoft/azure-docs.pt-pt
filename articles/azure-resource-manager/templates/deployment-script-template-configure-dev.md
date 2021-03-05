---
title: Configurar ambiente de desenvolvimento para scripts de implantação em modelos | Microsoft Docs
description: Configure o ambiente de desenvolvimento para scripts de implementação em modelos de Gestor de Recursos Azure (modelos ARM).
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: b2e1ffb3cbd513766945864e33589c46284bf942
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200942"
---
# <a name="configure-development-environment-for-deployment-scripts-in-arm-templates"></a>Configurar ambiente de desenvolvimento para scripts de implantação em modelos ARM

Aprenda a criar um ambiente de desenvolvimento para desenvolver e testar scripts de implementação de modelos ARM com uma imagem de script de implementação. Pode criar uma instância de [recipiente Azure](../../container-instances/container-instances-overview.md) ou utilizar [o Docker.](https://docs.docker.com/get-docker/) Ambas as opções estão abrangidas por este artigo.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-powershell-container"></a>Recipiente Azure PowerShell

Se não tiver um script de implementação Azure PowerShell, pode criar um ficheiro *hello.ps1* utilizando o seguinte conteúdo:

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

### <a name="azure-cli-container"></a>Recipiente CLI de Azure

Para uma imagem do recipiente Azure CLI, pode criar um ficheiro *hello.sh* utilizando o seguinte conteúdo:

```bash
firstname=$1
lastname=$2
output="{\"name\":{\"displayName\":\"$firstname $lastname\",\"firstName\":\"$firstname\",\"lastName\":\"$lastname\"}}"
echo -n "Hello "
echo $output | jq -r '.name.displayName'
```

> [!NOTE]
> Quando executou um script de implementação do Azure CLI, uma variável de ambiente chamada `AZ_SCRIPTS_OUTPUT_PATH` armazena a localização do ficheiro de saída do script. A variável ambiental não está disponível no recipiente do ambiente de desenvolvimento. Para obter mais informações sobre o trabalho com as saídas do Azure CLI, consulte [Trabalhar com saídas a partir do script CLI](deployment-script-template.md#work-with-outputs-from-cli-script).

## <a name="use-azure-powershell-container-instance"></a>Use a instância do recipiente Azure PowerShell

Para escrever os seus scripts no seu computador, precisa de criar uma conta de armazenamento e montar a conta de armazenamento na instância do recipiente. Para que possa fazer o upload do seu script para a conta de armazenamento e executar o script na instância do recipiente.

> [!NOTE]
> A conta de armazenamento que cria para testar o seu script não é a mesma conta de armazenamento que o serviço de script de implementação utiliza para executar o script. O serviço de scripts de implementação cria um nome único como partilha de ficheiros em cada execução.

### <a name="create-an-azure-powershell-container-instance"></a>Criar uma instância de recipiente Azure PowerShell

O modelo seguinte do Gestor de Recursos Azure (modelo ARM) cria uma instância de contentor e uma partilha de ficheiros e, em seguida, monta a partilha de ficheiros na imagem do recipiente.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
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

O valor predefinido para o caminho de montagem é `/mnt/azscripts/azscriptinput` . Este é o caminho no caso do contentor onde está montado na partilha de ficheiros.

A imagem do recipiente predefinido especificada no modelo é **mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2**. Consulte uma lista de todas as [versões Azure PowerShell suportadas](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

O gabarito suspende a instância do recipiente após 1.800 segundos. Tem 30 minutos antes que a instância do contentor entre em estado encerrado e a sessão termine.

Para implementar o modelo:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>Faça o upload do script de implementação

Faça o upload do seu script de implementação para a conta de armazenamento. Aqui está um exemplo de um script PowerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

Também pode fazer o upload do ficheiro utilizando o portal Azure ou o Azure CLI.

### <a name="test-the-deployment-script"></a>Teste o script de implementação

1. No portal Azure, abra o grupo de recursos onde implantou a instância do contentor e a conta de armazenamento.
2. Abra o grupo de contentores. O nome do grupo do recipiente predefinido é o nome do projeto anexado à *cg*. A instância do contentor está no estado **de Marcha.**
3. No menu de recursos, selecione **Containers**. O nome da instância do recipiente é o nome do projeto anexado ao *recipiente*.

    ![Screenshot do script de implementação conecte a instância do recipiente no portal Azure.](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

4. Selecione **Connect** e, em seguida, selecione **Connect**. Se não conseguir ligar-se à instância do recipiente, reinicie o grupo de contentores e tente novamente.
5. No painel de consola, executar os seguintes comandos:

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    A saída é **Hello John Dole.**

    ![A screenshot do script de implementação liga a saída de teste de instância do contentor na consola.](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-an-azure-cli-container-instance"></a>Use uma instância de recipiente CLI Azure

Para autoria dos seus scripts no seu computador, crie uma conta de armazenamento e monte a conta de armazenamento para a instância do recipiente. Em seguida, pode fazer o upload do seu script para a conta de armazenamento e executar o script na instância do recipiente.

> [!NOTE]
> A conta de armazenamento que cria para testar o seu script não é a mesma conta de armazenamento que o serviço de script de implementação utiliza para executar o script. O serviço de script de implementação cria um nome único como partilha de ficheiros em cada execução.

### <a name="create-an-azure-cli-container-instance"></a>Criar uma instância de contentor CLI Azure

O modelo ARM a seguir cria uma instância de contentor e uma partilha de ficheiros e, em seguida, monta a partilha de ficheiros na imagem do recipiente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "defaultValue": "mcr.microsoft.com/azure-cli:2.9.1",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
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
                "/bin/bash",
                "-c",
                "echo hello; sleep 1800"
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

O valor predefinido para o caminho de montagem é `/mnt/azscripts/azscriptinput` . Este é o caminho no caso do contentor onde está montado na partilha de ficheiros.

A imagem do recipiente predefinido especificada no modelo é **mcr.microsoft.com/azure-cli:2.9.1**. Consulte uma lista de [versões Azure CLI suportadas](https://mcr.microsoft.com/v2/azure-cli/tags/list).

> [!IMPORTANT]
> O script de implementação utiliza as imagens CLI disponíveis do Microsoft Container Registry (MCR). Leva cerca de um mês para certificar uma imagem CLI para um script de implementação. Não utilize as versões CLI que foram lançadas dentro de 30 dias. Para encontrar as datas de lançamento das imagens, consulte [as notas de lançamento do Azure CLI](/cli/azure/release-notes-azure-cli). Se utilizar uma versão não suportada, a mensagem de erro lista as versões suportadas.

O gabarito suspende a instância do recipiente após 1.800 segundos. Tem 30 minutos antes que a instância do contentor entre em estado terminal e a sessão termine.

Para implementar o modelo:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>Faça o upload do script de implementação

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

Também pode fazer o upload do ficheiro utilizando o portal Azure ou o Azure CLI.

### <a name="test-the-deployment-script"></a>Teste o script de implementação

1. No portal Azure, abra o grupo de recursos onde implantou a instância do contentor e a conta de armazenamento.
1. Abra o grupo de contentores. O nome do grupo do recipiente predefinido é o nome do projeto anexado à *cg*. A instância do recipiente é mostrada no estado **de Funcionamento.**
1. No menu de recursos, selecione **Containers**. O nome da instância do recipiente é o nome do projeto anexado ao *recipiente*.

    ![instância de ligação de script de implantação](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Selecione **Connect** e, em seguida, selecione **Connect**. Se não conseguir ligar-se à instância do recipiente, reinicie o grupo de contentores e tente novamente.
1. No painel de consola, executar os seguintes comandos:

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    ./hello.sh John Dole
    ```

    A saída é **Hello John Dole.**

    ![teste de instância de contentor de script de implantação](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test-cli.png)

## <a name="use-docker"></a>Use Docker

Pode utilizar uma imagem de recipiente Docker pré-configurada como ambiente de desenvolvimento de scripts de implementação. Para instalar o Docker, consulte [get Docker.](https://docs.docker.com/get-docker/)
Também é necessário configurar a partilha de ficheiros para montar o diretório, que contém os scripts de implantação no contentor Docker.

1. Puxe a imagem do contentor do script de implementação para o computador local:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    O exemplo utiliza a versão PowerShell 4.3.0.

    Para tirar uma imagem CLI de um MCR:

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Este exemplo utiliza a versão CLI 2.0.80. O script de implementação utiliza as imagens padrão dos recipientes CLI encontrados [aqui](https://hub.docker.com/_/microsoft-azure-cli).

1. Executar a imagem do Docker localmente.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Substitua **&lt; a carta do anfitrião>** e o nome do **&lt; diretório de anfitriões>** por uma pasta existente na unidade partilhada. Mapeia a pasta para a pasta _/dados_ no recipiente. Por exemplo, para mapear _D:\docker_:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-significa** manter a imagem do contentor viva.

    Um exemplo CLI:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. A imagem que se segue mostra como executar um script PowerShell, dado que tem um ficheiro *helloworld.ps1* na unidade partilhada.

    ![Modelo de implementação de modelo de gestor de recursos estivador cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Depois de o script ser testado com sucesso, pode usá-lo como um script de implementação nos seus modelos.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar scripts de implantação. Para percorrer um tutorial de script de implementação:

> [!div class="nextstepaction"]
> [Tutorial: Use scripts de implementação em modelos ARM](./template-tutorial-deployment-script.md)
