---
title: Copiar dados de armazenamento de Blobs com o AzCopy - Team Data Science Process
description: Copiar dados de e para armazenamento de Blobs do Azure com o AzCopy
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6c0951eb6ad3b7651da97e1a49c5edf5ab55a199
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61044353"
---
# <a name="copy-data-to-and-from-azure-blob-storage-using-azcopy"></a>Copiar dados de e para armazenamento de Blobs do Azure com o AzCopy
O AzCopy é um utilitário de linha de comandos concebido para carregar, transferir e copiar dados de e para o blob, ficheiro e armazenamento de tabelas do Microsoft Azure.

Para obter instruções sobre como instalar o AzCopy e informações adicionais sobre a utilização-lo com a plataforma do Azure, consulte [de introdução com o utilitário de linha de comandos do AzCopy](../../storage/common/storage-use-azcopy.md).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Se estiver a utilizar VM que foi configurada com os scripts fornecidos pela [máquinas virtuais de ciência de dados no Azure](virtual-machines.md), em seguida, o AzCopy já está instalado na VM.
> 
> [!NOTE]
> Para obter uma introdução completa para o armazenamento de Blobs do Azure, consulte [Noções básicas de Blobs do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e, a [serviço de Blobs do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este documento parte do princípio de que tem uma subscrição do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregamento/transferência de dados, tem de saber a sua chave de conta e o nome da conta de armazenamento do Azure.

* Para configurar uma subscrição do Azure, veja [durante um mês avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma conta de armazenamento e obtenção de conta e informações da chave, consulte [sobre as contas de armazenamento](../../storage/common/storage-create-storage-account.md).

## <a name="run-azcopy-commands"></a>Execute comandos do AzCopy
Para executar comandos do AzCopy, abra uma janela de comando e navegue para o diretório de instalação do AzCopy no seu computador, onde AzCopy.exe executável está localizado. 

A sintaxe básica para comandos do AzCopy é:

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> Pode adicionar a localização de instalação do AzCopy ao caminho do sistema e, em seguida, execute os comandos a partir de qualquer diretório. Por predefinição, o AzCopy é instalado para *% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy* ou *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>Carregar ficheiros para um blob do Azure
Para carregar um ficheiro, utilize o seguinte comando:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Transferir os ficheiros de um blob do Azure
Para transferir um ficheiro de um blob do Azure, utilize o seguinte comando:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="copy-blobs-between-azure-containers"></a>Copiar blobs entre contentores do Azure
Para copiar blobs entre contentores do Azure, utilize o seguinte comando:

    # Copying blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be copied. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Dicas para usar o AzCopy
> [!TIP]
> 1. Quando **carregar** arquivos */S* carrega ficheiros recursivamente. Sem este parâmetro, não forem carregados ficheiros em subdiretórios.  
> 2. Quando **baixar** arquivo, */S* procura o contentor recursivamente até que todos os ficheiros no diretório especificado e respetivos subdiretórios, ou todos os ficheiros que correspondam ao padrão especificado no diretório especificado e respetivos subdiretórios, são transferidos.  
> 3. Não é possível especificar um **ficheiro blob específico** para transferir utilizando o */Source* parâmetro. Para transferir um ficheiro específico, especifique o nome de ficheiro de BLOBs para transferir a utilizar o */padrão* parâmetro. **/S** parâmetro pode ser utilizado para ter o AzCopy, procure um recursivamente de padrão de nome de ficheiro. Sem o parâmetro do padrão, o AzCopy transfere todos os ficheiros nesse diretório.
> 
> 

