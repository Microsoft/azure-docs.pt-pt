---
title: Submeter um fluxo de trabalho através de um ficheiro BAM de entrada - Microsoft Genomics
titleSuffix: Azure
description: Este artigo pressupõe que o cliente msgen instalado e foram executados com êxito os dados de exemplo através do serviço.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 329ac374edbffa65a1b654a05a4934828f25d5de
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435088"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Submeter um fluxo de trabalho através de entradas de ficheiros BAM

Este artigo demonstra como submeter um fluxo de trabalho para o serviço Microsoft Genomics se o ficheiro de entrada for um único ficheiro BAM. Este tópico pressupõe que já instalou e executou o cliente `msgen` e está familiarizado com a utilização do Armazenamento do Azure. Se tiver submetido com êxito um fluxo de trabalho usando os dados de exemplo fornecidos, está pronto para continuar com este artigo. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Configure: Carregar o ficheiro BAM para o armazenamento do Azure
Vamos supor que tem um único ficheiro BAM, *reads.bam*, e o carregou para a sua conta de armazenamento *myaccount* no Azure como **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads<span></span>.bam<span></span>**. Tem o URL da API e a chave de acesso. Quer ter saídas em **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.



## <a name="submit-your-job-to-the-msgen-client"></a>Submeter a tarefa para o cliente `msgen` 


Segue-se o conjunto mínimo de argumentos que terá de fornecer ao cliente `msgen`; as quebras de linha são adicionadas para maior clareza:

Para Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Para Unix:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Se preferir utilizar um ficheiro de configuração, eis o que deverá conter:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Submeta o ficheiro `config.txt` com esta invocação: `msgen submit -f config.txt`

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, carregou um ficheiro BAM para o Armazenamento do Azure e submeteu um fluxo de trabalho para o serviço Microsoft Genomics através do cliente python `msgen`. Para obter informações adicionais sobre a submissão de fluxos de trabalho e outros comandos que pode utilizar com o serviço Microsoft Genomics, veja as nossas [FAQ](frequently-asked-questions-genomics.md). 
