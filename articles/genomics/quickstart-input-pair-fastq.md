---
title: Submeter um fluxo de trabalho através de entradas de ficheiros FASTQ
titleSuffix: Microsoft Genomics
description: Este artigo demonstra como enviar um fluxo de trabalho para o serviço Microsoft Genomics se os seus ficheiros de entrada forem um único par de ficheiros FASTQ.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 3806b165e5abb661e53c6a315650d025fd42e17f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "72248550"
---
# <a name="submit-a-workflow-using-fastq-file-inputs-in-microsoft-genomics"></a>Submeter um fluxo de trabalho através de entradas de ficheiros FASTQ no Microsoft Genomics

Este artigo demonstra como enviar um fluxo de trabalho para o serviço Microsoft Genomics se os seus ficheiros de entrada forem um único par de ficheiros FASTQ. Este tópico pressupõe que já instalou e executou o cliente `msgen` e está familiarizado com a utilização do Armazenamento do Azure. Se tiver submetido com sucesso um fluxo de trabalho utilizando os dados da amostra fornecidos, está pronto para prosseguir com este artigo. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>Configurar: carregar os ficheiros FASTQ para o armazenamento do Azure
Vamos supor que tem dois ficheiros, *reads_1.fq.gz* e *reads_2.fq.gz*, e os carregou para a sua conta de armazenamento *myaccount* no Azure como **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads_1<span></span>.fq<span></span>.gz<span></span>** and **https://<span></span>myaccount.blob.core.<span></span>windows<span></span>.net/<span></span>inputs/<span></span>reads_2.fq<span></span>.gz<span></span>**. Tem o URL da API e a chave de acesso. Quer ter saídas em **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


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
  --input-blob-name-1 reads_1.fq.gz ^
  --input-blob-name-2 reads_2.fq.gz ^
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
  --input-blob-name-1 reads_1.fq.gz \
  --input-blob-name-2 reads_2.fq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Se preferir utilizar um ficheiro de configuração, eis o que deverá conter:

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz
input_blob_name_2:                reads_2.fq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Submeta o ficheiro `config.txt` com esta invocação: `msgen submit -f config.txt`

## <a name="next-steps"></a>Passos seguintes
Neste artigo, carregou um par de ficheiros FASTQ para o Armazenamento do Azure e submeteu um fluxo de trabalho para o serviço Microsoft Genomics através do cliente python `msgen`. Para saber mais sobre a submissão do fluxo de trabalho e outros comandos que pode utilizar com o serviço Microsoft Genomics, consulte as nossas [FAQ](frequently-asked-questions-genomics.md). 
