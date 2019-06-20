---
title: Resolução de problemas do Azure Data Box Disk | Microsoft Docs
description: Descreve como resolver problemas no Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f725f38a335972ae8e0a8b8402a99202caa54a70
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147073"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Utilizar registos para resolver problemas de validação no disco do Azure Data Box

Este artigo aplica-se para o disco do Microsoft Azure Data Box. O artigo descreve como utilizar os registos para resolver os problemas de validação, que pode ver quando implementar esta solução.

## <a name="validation-tool-log-files"></a>Ficheiros de registo da ferramenta de validação

Quando validar os dados nos discos com o [ferramenta de validação](data-box-disk-deploy-copy-data.md#validate-data), uma *error.xml* gerados para quaisquer erros. O ficheiro de registo está localizado no `Drive:\DataBoxDiskImport\logs` pasta de sua unidade. Quando executa a validação, é fornecida uma ligação para o registo de erros.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Se executar várias sessões para validação, é gerado um registo de erros por sessão.

- Eis um exemplo de registo de erros quando os dados carregados para o `PageBlob` pasta não está alinhados de bytes de 512. Quaisquer dados carregados para o PageBlob têm de ter 512-bytes alinhados, por exemplo, um VHD ou VHDX. Os erros existentes neste ficheiro estão na `<Errors>` e avisos na `<Warnings>`.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Eis um exemplo de registo de erros quando o nome do contentor não é válido. A pasta que cria `BlockBlob`, `PageBlob`, ou `AzureFile` pastas no disco torna-se de um contentor na sua conta de armazenamento do Azure. O nome do contentor tem de seguir a [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Erros de validação de ferramenta

Os erros contidos no *error.xml* com correspondente ações recomendadas são resumidas na tabela seguinte.

| Código de erro| Descrição                       | Ações recomendadas               |
|------------|--------------------------|-----------------------------------|
| `None` | Validado com êxito os dados. | nenhuma ação necessária. |
| `InvalidXmlCharsInPath` |Não foi possível criar um arquivo de manifesto, como o caminho do ficheiro tem os carateres que não são válidos. | Remova esses caracteres para continuar.  |
| `OpenFileForReadFailed`| Não foi possível processar o ficheiro. Isto pode dever-se um acesso problema ou o ficheiro de danos no sistema.|Não foi possível ler o ficheiro devido a um erro. São os detalhes do erro da exceção. |
| `Not512Aligned` | Este ficheiro não está num formato válido para a pasta de PageBlob.| Apenas dados de carregamento que seja 512 bytes alinhados com `PageBlob` pasta. Remova o ficheiro a partir da pasta PageBlob ou movê-lo para a pasta de BlockBlob. Repita a validação.|
| `InvalidBlobPath` | Caminho do ficheiro não mapear para um caminho de blob válido na nuvem de acordo com o Blob do Azure convenções de nomenclatura.|Siga as diretrizes de nomenclatura do Azure para mudar o nome do caminho do ficheiro. |
| `EnumerationError` | Não foi possível enumerar o ficheiro para a validação. |Pode haver vários motivos para este erro. Uma razão mais provável é o acesso ao ficheiro. |
| `ShareSizeExceeded` | Este ficheiro causado o tamanho da partilha de ficheiros do Azure para excedem o limite do Azure de 5 TB.|Reduzir o tamanho dos dados na partilha de modo a que estão de acordo com o [limites de tamanho de objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação. |
| `AzureFileSizeExceeded` | Tamanho do ficheiro excede os limites de tamanho de ficheiros do Azure.| Reduzir o tamanho do ficheiro ou os dados, de modo a que estão de acordo com o [limites de tamanho de objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação.|
| `BlockBlobSizeExceeded` | Tamanho do ficheiro excede os limites de tamanho do Blob de blocos do Azure. | Reduzir o tamanho do ficheiro ou os dados, de modo a que estão de acordo com o [limites de tamanho de objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação. |
| `ManagedDiskSizeExceeded` | Tamanho do ficheiro excede os limites de tamanho de disco gerido do Azure. | Reduzir o tamanho do ficheiro ou os dados, de modo a que estão de acordo com o [limites de tamanho de objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação. |
| `PageBlobSizeExceeded` | Tamanho do ficheiro excede os limites de tamanho de disco gerido do Azure. | Reduzir o tamanho do ficheiro ou os dados, de modo a que estão de acordo com o [limites de tamanho de objeto do Azure](data-box-disk-limits.md#azure-object-size-limits). Repita a validação. |
| `InvalidShareContainerFormat`  |Os nomes de diretório não em conformidade com as convenções de nomenclatura do Azure para contentores ou compartilhamentos.         |A primeira pasta criada sob as pastas já existentes no disco torna-se um contentor na sua conta de armazenamento. Este nome de partilha ou o contentor não em conformidade com as convenções de nomenclatura do Azure. Renomeie o arquivo para que ele está em conformidade com [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repita a validação.   |
| `InvalidBlobNameFormat` | Caminho do ficheiro não mapear para um caminho de blob válido na nuvem de acordo com o Blob do Azure convenções de nomenclatura.|Renomeie o arquivo para que ele está em conformidade com [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repita a validação. |
| `InvalidFileNameFormat` | Caminho do ficheiro não mapear para um caminho de ficheiro válido na nuvem de acordo com os ficheiros do Azure convenções de nomenclatura. |Renomeie o arquivo para que ele está em conformidade com [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repita a validação. |
| `InvalidDiskNameFormat` | Caminho do ficheiro não mapear para um nome de disco válido na cloud de acordo com as convenções de nomenclatura de disco gerido do Azure. |Renomeie o arquivo para que ele está em conformidade com [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repita a validação.       |
| `NotPartOfFileShare` | Não foi possível carregar os ficheiros porque o caminho de carregamento não é válido. Carregue os ficheiros numa pasta do ficheiros do Azure.   | Remova os ficheiros de erro e carregar esses ficheiros para uma pasta pré-criada. Repita a validação. |
| `NonVhdFileNotSupportedForManagedDisk` | Não é possível carregar um ficheiro de não-VHD como um disco gerido. |Remova os ficheiros não VHD estas não são suportadas. Repita a validação. |


## <a name="next-steps"></a>Passos Seguintes

- Resolução de problemas [erros de carregamento de dados](data-box-disk-troubleshoot-upload.md).
