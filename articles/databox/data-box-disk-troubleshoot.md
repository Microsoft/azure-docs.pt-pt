---
title: Resolução de problemas do Azure Data Box Disk | Microsoft Docs
description: Aprenda a usar registos para resolver problemas de validação que podem surgir quando implementar o Disco de Caixa de Dados Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 1c1b38c4021660b9f59098f8442d16bfd0ecc582
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87925547"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Utilize registos para resolver problemas de validação no Disco de Caixa de Dados Azure

Este artigo aplica-se ao Disco de Caixa de Dados do Microsoft Azure. O artigo descreve como usar os registos para resolver problemas de validação que pode ver quando implementa esta solução.

## <a name="validation-tool-log-files"></a>Ficheiros de registo de ferramentas de validação

Quando valida os dados dos discos utilizando a [ferramenta de validação,](data-box-disk-deploy-copy-data.md#validate-data)é gerado um *error.xml* para registar eventuais erros. O ficheiro de registo está localizado na  `Drive:\DataBoxDiskImport\logs` pasta da sua unidade. É fornecida uma ligação ao registo de erros quando executa a validação.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Se executar várias sessões para validação, então um registo de erro é gerado por sessão.

- Aqui está uma amostra do registo de erro quando os dados carregados na `PageBlob` pasta não estão alinhados com 512 bytes. Quaisquer dados enviados para o PageBlob devem estar alinhados com 512 bytes, por exemplo, um VHD ou VHDX. Os erros neste ficheiro estão nos `<Errors>` avisos e avisos em `<Warnings>` .

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

- Aqui está uma amostra do registo de erro quando o nome do recipiente não é válido. A pasta que cria sob `BlockBlob` `PageBlob` , ou `AzureFile` pastas no disco torna-se um recipiente na sua conta de Armazenamento Azure. O nome do recipiente deve seguir as [convenções de nomeação Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)

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

## <a name="validation-tool-errors"></a>Erros de ferramenta de validação

Os erros contidos no *error.xml* com as ações recomendadas correspondentes são resumidos no quadro seguinte.

| Código de erro| Descrição                       | Ações recomendadas               |
|------------|--------------------------|-----------------------------------|
| `None` | Validou com sucesso os dados. | nenhuma ação necessária. |
| `InvalidXmlCharsInPath` |Não foi possível criar um ficheiro manifesto, uma vez que o caminho do ficheiro tem caracteres que não são válidos. | Remova estes caracteres para prosseguir.  |
| `OpenFileForReadFailed`| Não foi possível processar o ficheiro. Isto pode ser devido a um problema de acesso ou corrupção no sistema de ficheiros.|Não foi possível ler o ficheiro devido a um erro. Os detalhes do erro estão na exceção. |
| `Not512Aligned` | Este ficheiro não está num formato válido para a pasta PageBlob.| Apenas faça upload de dados que são 512 bytes alinhados com a `PageBlob` pasta. Retire o ficheiro da pasta PageBlob ou mova-o para a pasta BlockBlob. Repreta a validação.|
| `InvalidBlobPath` | O caminho do arquivo não mapeia para um caminho de bolha válido na nuvem de acordo com as convenções de nomeação Azure Blob.|Siga as diretrizes de nomeação Azure para mudar o nome do caminho do ficheiro. |
| `EnumerationError` | Não foi possível enumerar o ficheiro para validação. |Pode haver várias razões para este erro. A razão mais provável é o acesso ao ficheiro. |
| `ShareSizeExceeded` | Este ficheiro fez com que o tamanho da partilha do ficheiro Azure excedesse o limite de Azure de 5 TB.|Reduza o tamanho dos dados na partilha de modo a que esteja em conformidade com os limites de tamanho do [objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Repreta a validação. |
| `AzureFileSizeExceeded` | O tamanho do ficheiro excede os limites de tamanho do Ficheiro Azure.| Reduza o tamanho do ficheiro ou os dados de modo a que esteja em conformidade com os limites de tamanho do [objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Repreta a validação.|
| `BlockBlobSizeExceeded` | O tamanho do ficheiro excede os limites do tamanho do Bloco Azure Blob. | Reduza o tamanho do ficheiro ou os dados de modo a que esteja em conformidade com os limites de tamanho do [objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Repreta a validação. |
| `ManagedDiskSizeExceeded` | O tamanho do ficheiro excede os limites de tamanho do disco gerido azure. | Reduza o tamanho do ficheiro ou os dados de modo a que esteja em conformidade com os limites de tamanho do [objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Repreta a validação. |
| `PageBlobSizeExceeded` | O tamanho do ficheiro excede os limites de tamanho do disco gerido azure. | Reduza o tamanho do ficheiro ou os dados de modo a que esteja em conformidade com os limites de tamanho do [objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Repreta a validação. |
| `InvalidShareContainerFormat`  |Os nomes do diretório não estão em conformidade com as convenções de nomeação Azure para contentores ou ações.         |A primeira pasta criada sob as pastas pré-existentes no disco torna-se um recipiente na sua conta de armazenamento. Esta parte ou nome do contentor não está em conformidade com as convenções de nomeação Azure. Mude o nome do ficheiro de modo a que esteja em conformidade com as [convenções de nomeação Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repreta a validação.   |
| `InvalidBlobNameFormat` | O caminho do arquivo não mapeia para um caminho de bolha válido na nuvem de acordo com as convenções de nomeação Azure Blob.|Mude o nome do ficheiro de modo a que esteja em conformidade com as [convenções de nomeação Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repreta a validação. |
| `InvalidFileNameFormat` | O caminho do ficheiro não mapeia para um caminho de ficheiro válido na nuvem de acordo com as convenções de nomeação do Ficheiro Azure. |Mude o nome do ficheiro de modo a que esteja em conformidade com as [convenções de nomeação Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repreta a validação. |
| `InvalidDiskNameFormat` | O caminho do ficheiro não mapeia um nome de disco válido na nuvem de acordo com as convenções de nomeação do Disco Gerido Azure. |Mude o nome do ficheiro de modo a que esteja em conformidade com as [convenções de nomeação Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Repreta a validação.       |
| `NotPartOfFileShare` | O caminho de upload para ficheiros não é válido. Faça o upload dos ficheiros para uma pasta nos Ficheiros Azure.   | Remova os ficheiros por engano e faça o upload desses ficheiros para uma pasta pré-crita. Repreta a validação. |
| `NonVhdFileNotSupportedForManagedDisk` | Um ficheiro não-VHD não pode ser carregado como um disco gerido. |Remova os ficheiros não VHD da `ManagedDisk` pasta, uma vez que estes não são suportados ou mover estes ficheiros para uma `PageBlob` pasta. Repreta a validação. |


## <a name="next-steps"></a>Passos seguintes

- Erros [de upload de dados](data-box-disk-troubleshoot-upload.md)de resolução de problemas .
