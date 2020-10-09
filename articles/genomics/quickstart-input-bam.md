---
title: Enviar um fluxo de trabalho usando a entrada de ficheiro BAM
titleSuffix: Microsoft Genomics
description: Este artigo demonstra como enviar um fluxo de trabalho para o serviço Microsoft Genomics se o seu ficheiro de entrada for um único ficheiro BAM.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 5145aa0ffdc4095f178a214f63433e5bcece83b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "72249157"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Submeter um fluxo de trabalho através de entradas de ficheiros BAM

Este artigo demonstra como enviar um fluxo de trabalho para o serviço Microsoft Genomics se o seu ficheiro de entrada for um único ficheiro BAM. Este tópico pressupõe que já instalou e executou o cliente `msgen` e está familiarizado com a utilização do Armazenamento do Azure. Se tiver submetido com sucesso um fluxo de trabalho utilizando os dados da amostra fornecidos, está pronto para prosseguir com este artigo. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Configurar: carregar o ficheiro BAM para o armazenamento do Azure
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

## <a name="next-steps"></a>Passos seguintes
Neste artigo, carregou um ficheiro BAM para o Armazenamento do Azure e submeteu um fluxo de trabalho para o serviço Microsoft Genomics através do cliente python `msgen`. Para obter informações adicionais sobre a submissão de fluxos de trabalho e outros comandos que pode utilizar com o serviço Microsoft Genomics, veja as nossas [FAQ](frequently-asked-questions-genomics.md). 
