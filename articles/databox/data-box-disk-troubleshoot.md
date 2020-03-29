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
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67805714"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Utilize registos para resolver problemas de validação em Disco de Caixa de Dados Azure

Este artigo aplica-se ao Disco de Caixa de Dados do Microsoft Azure. O artigo descreve como usar os registos para resolver os problemas de validação que pode ver quando implementa esta solução.

## <a name="validation-tool-log-files"></a>Ficheiros de registo de ferramentas de validação

Quando valida os dados dos discos utilizando a ferramenta de [validação,](data-box-disk-deploy-copy-data.md#validate-data)gera-se um *erro.xml* para registar quaisquer erros. O ficheiro de registo `Drive:\DataBoxDiskImport\logs` está localizado na pasta da sua unidade. É fornecido um link para o registo de erros quando executa a validação.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Se executar várias sessões para validação, então um registo de erro é gerado por sessão.

- Aqui está uma amostra do registo de `PageBlob` erros quando os dados carregados na pasta não estão alinhados 512 bytes. Quaisquer dados enviados para o PageBlob devem estar alinhados 512 bytes, por exemplo, um VHD ou VHDX. Os erros neste ficheiro `<Errors>` estão `<Warnings>`no e advertências em .

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

- Aqui está uma amostra do registo de erros quando o nome do recipiente não é válido. A pasta que `BlockBlob`cria `PageBlob`sob `AzureFile` , ou pastas no disco torna-se um recipiente na sua conta de Armazenamento Azure. O nome do recipiente deve seguir as convenções de [nomeação de Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)

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

## <a name="validation-tool-errors"></a>Erros da ferramenta de validação

Os erros contidos no *erro.xml* com as ações recomendadas correspondentes são resumidos na tabela seguinte.

| Código de erro| Descrição                       | Ações recomendadas               |
|------------|--------------------------|-----------------------------------|
| `None` | Validou com sucesso os dados. | nenhuma ação necessária. |
| `InvalidXmlCharsInPath` |Não poderia criar um ficheiro manifesto, uma vez que o caminho do ficheiro tem caracteres que não são válidos. | Remova estes caracteres para prosseguir.  |
| `OpenFileForReadFailed`| Não consegui processar o ficheiro. Isto pode ser devido a uma questão de acesso ou corrupção no sistema de ficheiros.|Não consegui ler o ficheiro devido a um erro. Os detalhes do erro estão na exceção. |
| `Not512Aligned` | Este ficheiro não se encontra num formato válido para a pasta PageBlob.| Basta carregar dados que sejam 512 `PageBlob` bytes alinhados com pasta. Retire o ficheiro da pasta PageBlob ou desloque-o para a pasta BlockBlob. Tente a validação.|
| `InvalidBlobPath` | O caminho dos ficheiros não mapeia um caminho de bolha válido na nuvem de acordo com as convenções de nomeação de Azure Blob.|Siga as diretrizes de nomeação do Azure para renomear o caminho do ficheiro. |
| `EnumerationError` | Não conseguiu enumerar o ficheiro para validação. |Pode haver várias razões para este erro. Uma razão mais provável é o acesso ao ficheiro. |
| `ShareSizeExceeded` | Este ficheiro fez com que o tamanho da partilha de ficheiros Azure excedesse o limite Azure de 5 TB.|Reduza o tamanho dos dados da parte de modo a que esteja em conformidade com os limites de tamanho do [objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Tente a validação. |
| `AzureFileSizeExceeded` | O tamanho do ficheiro excede os limites de tamanho do Ficheiro Azure.| Reduza o tamanho do ficheiro ou os dados de modo a que esteja em conformidade com os limites de tamanho do [objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Tente a validação.|
| `BlockBlobSizeExceeded` | O tamanho do ficheiro excede os limites do tamanho do Bloco Azure. | Reduza o tamanho do ficheiro ou os dados de modo a que esteja em conformidade com os limites de tamanho do [objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Tente a validação. |
| `ManagedDiskSizeExceeded` | O tamanho do ficheiro excede os limites de tamanho do disco gerido pelo Azure. | Reduza o tamanho do ficheiro ou os dados de modo a que esteja em conformidade com os limites de tamanho do [objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Tente a validação. |
| `PageBlobSizeExceeded` | O tamanho do ficheiro excede os limites de tamanho do disco gerido pelo Azure. | Reduza o tamanho do ficheiro ou os dados de modo a que esteja em conformidade com os limites de tamanho do [objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Tente a validação. |
| `InvalidShareContainerFormat`  |Os nomes de diretório não estão em conformidade com as convenções de nomeação de Azure para contentores ou ações.         |A primeira pasta criada sob as pastas pré-existentes no disco torna-se um recipiente na sua conta de armazenamento. Esta partilha ou nome de contentor não está em conformidade com as convenções de nomeação do Azure. Mude o nome do ficheiro de modo a que esteja em conformidade com as convenções de [nomeação do Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Tente a validação.   |
| `InvalidBlobNameFormat` | O caminho dos ficheiros não mapeia um caminho de bolha válido na nuvem de acordo com as convenções de nomeação de Azure Blob.|Mude o nome do ficheiro de modo a que esteja em conformidade com as convenções de [nomeação do Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Tente a validação. |
| `InvalidFileNameFormat` | O caminho do ficheiro não mapeia um caminho de ficheiro válido na nuvem de acordo com as convenções de nomeação do Ficheiro Azure. |Mude o nome do ficheiro de modo a que esteja em conformidade com as convenções de [nomeação do Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Tente a validação. |
| `InvalidDiskNameFormat` | O caminho do ficheiro não mapeia um nome de disco válido na nuvem de acordo com as convenções de nomeação do Disco Gerido azure. |Mude o nome do ficheiro de modo a que esteja em conformidade com as convenções de [nomeação do Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Tente a validação.       |
| `NotPartOfFileShare` | O caminho de upload para ficheiros não é válido. Faça o upload dos ficheiros para uma pasta em Ficheiros Azure.   | Remova os ficheiros por engano e faça o upload desses ficheiros para uma pasta pré-criada. Tente a validação. |
| `NonVhdFileNotSupportedForManagedDisk` | Um ficheiro não VHD não pode ser carregado como um disco gerido. |Remova os ficheiros não `ManagedDisk` VHD da pasta, uma vez `PageBlob` que estes não são suportados ou move estes ficheiros para uma pasta. Tente a validação. |


## <a name="next-steps"></a>Passos seguintes

- Erros de upload de dados de resolução de [problemas.](data-box-disk-troubleshoot-upload.md)
