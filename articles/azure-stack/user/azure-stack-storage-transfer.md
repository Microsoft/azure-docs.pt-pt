---
title: Ferramentas para o armazenamento do Azure Stack | Documentos da Microsoft
description: Saiba mais sobre dados de armazenamento do Azure Stack a ferramentas de transferência
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 4e92f2aeec21ccef5a6a553b17e099d54de7266a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57774342"
---
# <a name="use-data-transfer-tools-for-azure-stack-storage"></a>Utilizar ferramentas de transferência de dados para o armazenamento do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Microsoft Azure Stack fornece um conjunto de serviços de armazenamento para discos, blobs, tabelas, filas e funções de gestão de conta. Pode utilizar um conjunto de ferramentas de armazenamento do Azure para gerir ou mover dados de ou para armazenamento do Azure Stack. Este artigo fornece uma descrição geral das ferramentas disponíveis.

Os requisitos de determinam qual das seguintes ferramentas funciona melhor para:

* [AZCopy](#azcopy)

    Um utilitário de armazenamento específicos, da linha de comandos que pode baixar para copiar dados de um objeto a outro objeto dentro de sua conta de armazenamento, ou entre contas de armazenamento.

* [Azure PowerShell](#azure-powershell)

    Um shell da linha de comandos baseada em tarefas e linguagem de script concebida especialmente para a administração de sistema.

* [CLI do Azure](#azure-cli)

    Uma ferramenta de código-fonte aberto, para várias plataformas que fornece um conjunto de comandos para trabalhar com as plataformas Azure e o Azure Stack.

* [Explorador de armazenamento da Microsoft](#microsoft-azure-storage-explorer)

    Uma aplicação autónoma fácil de usar com uma interface do usuário.

* [Blobfuse](#blobfuse)

    Um driver de sistema de arquivos virtual para armazenamento de Blobs do Azure, que permite-lhe aceder aos seus dados de BLOBs de bloco existente na sua conta de armazenamento através do sistema de ficheiros do Linux. 

Devido a diferenças de serviços de armazenamento entre o Azure e o Azure Stack, pode haver alguns requisitos específicos para cada ferramenta descrito nas seções a seguir. Para obter uma comparação entre o armazenamento do Azure Stack e o armazenamento do Azure, consulte [armazenamento do Azure Stack: Diferenças e considerações](azure-stack-acs-differences.md).

## <a name="azcopy"></a>AzCopy

O AzCopy é um utilitário de linha de comandos concebido para copiar dados para e do armazenamento de BLOBs e tabelas do Microsoft Azure com comandos simples com um desempenho ideal. Pode copiar dados de um objeto para outro em sua conta de armazenamento, ou entre contas de armazenamento.

### <a name="download-and-install-azcopy"></a>Baixe e instale o AzCopy

Existem duas versões do utilitário AzCopy: AzCopy no Windows e o AzCopy no Linux.

 - **AzCopy no Windows**
    - Baixe a versão suportada do AzCopy para o Azure Stack. Pode instalar e utilizar o AzCopy no Azure Stack da mesma forma que o Azure. Para obter mais informações, consulte [AzCopy no Windows](../../storage/common/storage-use-azcopy.md).
        - Para a atualização 1811 ou versões mais recentes, [transferir AzCopy 7.3.0](https://aka.ms/azcopyforazurestack20171109).
        - Para versões anteriores (atualização 1802 para 1809), [transferir AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).

 - **AzCopy no Linux**

    - Pode instalar e utilizar o AzCopy no Azure Stack da mesma forma que o Azure. Para obter mais informações, consulte [AzCopy no Linux](../../storage/common/storage-use-azcopy-linux.md).
    - Para versões anteriores (1802 para 1809 atualizações), consulte a [passos de instalação para o AzCopy 7.1 e versões anteriores](../../storage/common/storage-use-azcopy-linux.md#installation-steps-for-azcopy-71-and-earlier-versions).

### <a name="azcopy-command-examples-for-data-transfer"></a>Exemplos de comandos do AzCopy para transferência de dados

Os exemplos seguintes, siga os cenários típicos para copiar dados para e de blobs do Azure Stack. Para obter mais informações, consulte [AzCopy no Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) e [AzCopy no Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux).

### <a name="download-all-blobs-to-a-local-disk"></a>Transferir todos os blobs num disco local

**Windows**

```shell
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
```

**Linux**

```bash
azcopy \
    --source https://myaccount.blob.local.azurestack.external/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

### <a name="upload-single-file-to-virtual-directory"></a>Carregar arquivo único para o diretório virtual

**Windows**

```shell
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

**Linux**

```bash
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.local.azurestack.external/mycontainer/vd/abc.txt \
    --dest-key <key>
```

### <a name="move-data-between-azure-and-azure-stack-storage"></a>Mover dados entre o armazenamento do Azure e o Azure Stack

Transferência de dados assíncronos entre o armazenamento do Azure e o Azure Stack não é suportada. Tem de especificar a transferência com o **/SyncCopy** ou **– cópia sincronização** opção.

**Windows**

```shell
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
```

**Linux**

```bash
azcopy \
    --source https://myaccount1.blob.local.azurestack.external/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --sync-copy
```

### <a name="azcopy-known-issues"></a>Problemas conhecidos do Azcopy

 - Qualquer operação de AzCopy num arquivo de ficheiros não está disponível porque o armazenamento de ficheiros ainda não está disponível no Azure Stack.
 - Transferência de dados assíncronos entre o armazenamento do Azure e o Azure Stack não é suportada. Pode especificar a transferência com o **/SyncCopy** opção para copiar os dados.
 - A versão do Linux do Azcopy só suporta a atualização 1802 ou de versões posteriores. E ele não suporta o serviço de tabela.

## <a name="azure-powershell"></a>Azure PowerShell

O Azure PowerShell é um módulo que oferece cmdlets para gerir serviços no Azure e o Azure Stack. É uma shell da linha de comandos baseada em tarefas e linguagem de script concebida especialmente para a administração de sistema.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Instalar e configurar o PowerShell para o Azure Stack

Módulos do Azure Stack compatíveis do PowerShell do Azure são necessários para trabalhar com o Azure Stack. Para obter mais informações, consulte [instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md) e [configurar o ambiente do PowerShell do utilizador do Azure Stack](azure-stack-powershell-configure-user.md) para saber mais.

### <a name="powershell-sample-script-for-azure-stack"></a>Script de exemplo do PowerShell para o Azure Stack 

Este exemplo partem do princípio de que tem com êxito [PowerShell instalado para o Azure Stack](azure-stack-powershell-install.md). Este script irá ajudá-lo a concluir a configuração e peça ao seu inquilino do Azure Stack credenciais para adicionar a sua conta para o ambiente local do PowerShell. Em seguida, o script irá definir o padrão de subscrição do Azure, criar uma nova conta de armazenamento no Azure, criar um novo contentor nesta nova conta de armazenamento e carregar um ficheiro de imagem existente (blob) para esse contentor. Depois do script apresenta uma lista de todos os blobs existentes nesse contentor, irá criar um novo diretório de destino no seu computador local e transfira o ficheiro de imagem.

1. Instale [módulos do Azure Stack compatível com o Azure PowerShell](azure-stack-powershell-install.md).
2. No desenvolvimento do [Azure Stack Kit, deve ser o blobEndpoint](azure-stack-powershell-download.md) .
3. Open **ISE do Windows PowerShell** e **executar como administrador**, clique em **ficheiro** > **New** para criar um novo ficheiro de script.
4. Copie o script abaixo e cole no novo ficheiro de script.
5. Atualize as variáveis de script com base nas suas definições de configuração.
   > [!NOTE]
   > Este script tem de ser executado no diretório de raiz para **AzureStack_Tools**.

```PowerShell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environment name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudience = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\download" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudience

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder

# end
```

### <a name="powershell-known-issues"></a>Problemas conhecidos do PowerShell

A atual versão de módulo de PowerShell do Azure compatível para o Azure Stack é 1.2.11 para as operações de utilizador. É diferente da versão mais recente do Azure PowerShell. Essa diferença tem impacto sobre a operação de serviços de armazenamento:

O formato do valor de retorno dos `Get-AzureRmStorageAccountKey` na versão 1.2.11 tem duas propriedades: `Key1` e `Key2`, enquanto a versão atual do Azure devolve uma matriz que contém todas as chaves de conta.

```powershell
# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.4, and later versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Value[0]

# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.3.2, and previous versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Key1
```

Para obter mais informações, consulte [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey).

## <a name="azure-cli"></a>CLI do Azure

A CLI do Azure é a experiência de linha de comandos do Azure para gerir recursos do Azure. Pode instalá-la no macOS, Linux e Windows e executá-lo a partir da linha de comando.

CLI do Azure está otimizada para gerir e administrar recursos do Azure a partir da linha de comandos e para criar scripts de automatização que funcionam no âmbito do Azure Resource Manager. Ele fornece muitas das mesmas funções encontradas no portal do Azure Stack, incluindo o acesso de dados avançados.

O Azure Stack requer a CLI do Azure versão 2.0 ou posterior. Para obter mais informações sobre como instalar e configurar a CLI do Azure com o Azure Stack, veja [instalar e configurar a CLI do Azure Stack](azure-stack-version-profiles-azurecli2.md). Para obter mais informações sobre como utilizar a CLI do Azure para executar várias tarefas, trabalhar com recursos na sua conta de armazenamento do Azure Stack, veja [utilizar a CLI do Azure com o armazenamento do Azure](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Script de exemplo da CLI do Azure para o Azure Stack

Depois de concluir a instalação da CLI e a configuração, pode tentar os passos seguintes para trabalhar com um script de exemplo de pequenas shell para interagir com recursos de armazenamento do Azure Stack. O script tiver concluído as seguintes ações:

* Cria um novo contentor na sua conta de armazenamento.
* Carrega um ficheiro existente (como um blob) para o contentor.
* Apresenta uma lista de todos os blobs no contentor.
* Transfere o ficheiro para um destino no seu computador local que especificar.

Antes de executar este script, certifique-se de que com êxito pode ligar a e inicie sessão para o Azure Stack de destino.

1. Abra o editor de texto favorito, em seguida, copie e cole o script anterior no editor.
2. Atualize as variáveis do script para refletir as definições de configuração.
3. Depois de atualizou as variáveis necessárias, guarde o script e saia do editor. Os passos seguintes partem do princípio de que com o nome do script **my_storage_sample.sh**.
4. Marca o script como executável, se necessário: `chmod +x my_storage_sample.sh`
5. Execute o script. Por exemplo, no Bash: `./my_storage_sample.sh`

```azurecli
#!/bin/bash
# A simple Azure Stack storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Explorador de armazenamento do Microsoft Azure

Explorador de armazenamento do Microsoft Azure é uma aplicação autónoma da Microsoft. Permite-lhe trabalhar facilmente com o armazenamento do Azure e o armazenamento do Azure Stack dados no Windows, macOS e Linux computadores. Se desejar uma forma fácil de gerir os seus dados de armazenamento do Azure Stack, considere utilizar o Explorador de armazenamento do Microsoft Azure.

* Para saber mais sobre como configurar o Explorador de armazenamento do Azure para trabalhar com o Azure Stack, veja [Explorador de armazenamento de ligar a uma subscrição do Azure Stack](azure-stack-storage-connect-se.md).
* Para saber mais sobre o Explorador de armazenamento do Microsoft Azure, veja [introdução ao Explorador de armazenamento](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="blobfuse"></a>Blobfuse 

[Blobfuse](https://github.com/Azure/azure-storage-fuse) é um driver de sistema de arquivos virtual para armazenamento de Blobs do Azure, que permite-lhe aceder aos seus dados de BLOBs de bloco existente na sua conta de armazenamento através do sistema de ficheiros do Linux. Armazenamento de Blobs do Azure é um serviço de armazenamento de objeto e, portanto, não tem um espaço de nomes hierárquico. Blobfuse fornece este espaço de nomes com o esquema do diretório virtual com o uso de barra `/` como delimitador. Blobfuse funciona no Azure e no Azure Stack. 

Para saber mais sobre como montar o armazenamento de BLOBs como um sistema de ficheiros com Blobfuse no Linux, veja [como armazenamento de BLOBs de montagem como um sistema de ficheiros com Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux). 

Para o Azure Stack, **blobEndpoint** tem de ser especificado para além de accountName, accountKey/sasToken, containerName, ao configurar as credenciais da conta de armazenamento no passo de preparação para a montagem. 

No desenvolvimento do Azure Stack Kit, deve ser o blobEndpoint `myaccount.blob.local.azurestack.external`. Num sistema integrado do Azure Stack, contacte o administrador da nuvem se não tiver a certeza sobre o ponto final. 

Lembre accountKey e sasToken só podem ser configurado um de cada vez. Quando a chave de conta de armazenamento for fornecida, é o ficheiro de configuração de credenciais no seguinte formato: 

```
accountName myaccount 
accountKey myaccesskey== 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

Quando é fornecido o token de acesso partilhado, o ficheiro de configuração de credenciais é no seguinte formato:

```  
accountName myaccount 
sasToken ?mysastoken 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>Passos Seguintes

* [Ligar o Explorador de armazenamento a uma subscrição do Azure Stack](azure-stack-storage-connect-se.md)
* [Introdução ao Explorador de armazenamento](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Armazenamento consistentes do Azure: diferenças e considerações](azure-stack-acs-differences.md)
* [Introdução ao armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)
