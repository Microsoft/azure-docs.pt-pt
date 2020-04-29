---
title: Uploads de dados de resolução de problemas usando registos
titleSuffix: Azure Data Box Disk
description: Descreve como usar os registos e problemas de resolução de problemas vistos ao carregar dados para o Disco de Caixas de Dados Do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79260140"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Compreender registos para resolver problemas de envio de dados em Disco de Caixa de Dados Azure

Este artigo aplica-se ao Microsoft Azure Data Box Disk e descreve os problemas que vê quando envia dados para o Azure.

## <a name="about-upload-logs"></a>Sobre os registos de upload

Quando os dados são enviados para o `_error.xml` `_verbose.xml` Azure no datacenter, e os ficheiros são gerados para cada conta de armazenamento. Estes registos são enviados para a mesma conta de armazenamento que foi usada para fazer o upload de dados. 

Ambos os registos estão no mesmo formato e contêm descrições xml dos eventos ocorridos enquanto copiam os dados do disco para a conta de Armazenamento Azure.

O registo verboso contém informações completas sobre o estado da operação de cópia para cada bolha ou ficheiro, enquanto o registo de erros contém apenas as informações para bolhas ou ficheiros que encontraram erros durante o upload.

O registo de erros tem a mesma estrutura que o registo verboso, mas filtra operações bem sucedidas.

## <a name="download-logs"></a>Registos de transferências

Tome os seguintes passos para localizar os registos de upload.

1. Se houver erros ao enviar os dados para o Azure, o portal apresenta um caminho para a pasta onde estão localizados os registos de diagnóstico.

    ![Link para registos no portal](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Vai a **waies.**

    ![erros e registos verbosos](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

Em cada caso, vê os registos de erro e os registos verbosos. Selecione cada log e baixe uma cópia local.

## <a name="sample-upload-logs"></a>Registos de upload de amostras

Uma amostra `_verbose.xml` do é mostrada abaixo. Neste caso, a ordem completou com sucesso sem erros.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

Para a mesma ordem, `_error.xml` uma amostra do abaixo é mostrada.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

Uma amostra `_error.xml` do abaixo é mostrada abaixo onde a ordem foi completada com erros. 

O ficheiro de erro `Summary` neste caso tem uma secção e outra secção que contém todos os erros de nível de ficheiro. 

O `Summary` contém `ValidationErrors` o `CopyErrors`e o . Neste caso, 8 ficheiros ou pastas foram enviados para o Azure e não houve erros de validação. Quando os dados foram copiados para a conta de Armazenamento Azure, 5 ficheiros ou pastas foram carregados com sucesso. Os restantes 3 ficheiros ou pastas foram renomeados de acordo com as convenções de nomeação do contentor Azure e, em seguida, enviados com sucesso para O Azure.

O estado do `BlobStatus` nível do ficheiro está no que descreve quaisquer ações tomadas para carregar as bolhas. Neste caso, três contentores são renomeados porque as pastas às quais os dados foram copiados não estavam em conformidade com as convenções de nomeação do Azure para contentores. Para as bolhas carregadas nesses recipientes, o novo nome do recipiente, o caminho da bolha em Azure, o caminho original de ficheiro inválido, e o tamanho da bolha estão incluídos.
    
```xml
 <?xml version="1.0" encoding="utf-8"?>
    <DriveLog Version="2018-10-01">
      <DriveId>18041C582D7E</DriveId>
      <Summary>
     <!--Summary for validation and data copy to Azure -->
        <ValidationErrors>
          <None Count="8" />
        </ValidationErrors>
        <CopyErrors>
          <Completed Count="5" Description="No errors encountered" />
          <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
        </CopyErrors>
      </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
        <SizeInBytes>71528448</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Status>CompletedWithErrors</Status>
    </DriveLog>
```

## <a name="data-upload-errors"></a>Erros de upload de dados

Os erros gerados ao carregar os dados para o Azure são resumidos na tabela seguinte.

| Código de erro | Descrição                   |
|-------------|------------------------------|
|`None` |  Concluído com sucesso.           |
|`Renamed` | Rebatizou com sucesso a bolha.   |
|`CompletedWithErrors` | Upload completo com erros. Os detalhes dos ficheiros errados estão incluídos no ficheiro de registo.  |
|`Corrupted`|CrC computado durante a ingestão de dados não corresponde ao CRC calculado durante o upload.  |  
|`StorageRequestFailed` | O pedido de armazenamento azure falhou.   |     
|`LeasePresent` | Este artigo é alugado e está a ser utilizado por outro utilizador. |
|`StorageRequestForbidden` |Não foi possível fazer o upload devido a problemas de autenticação. |
|`ManagedDiskCreationTerminalFailure` | Não podia carregar como discos geridos. Os ficheiros estão disponíveis na conta de armazenamento de encenação como bolhas de página. Pode converter manualmente bolhas de página em discos geridos.  |
|`DiskConversionNotStartedTierInfoMissing` | Uma vez que o ficheiro VHD foi copiado fora das pastas de nível pré-criadas, não foi criado um disco gerido. O ficheiro é enviado como blob de página para a conta de armazenamento de encenação, conforme especificado durante a criação da encomenda. Pode convertê-lo manualmente num disco gerido.|
|`InvalidWorkitem` | Não foi possível fazer o upload dos dados, uma vez que não está em conformidade com as convenções de nomeação e limitações do Azure.|
|`InvalidPageBlobUploadAsBlockBlob` | Carregado como bolhas de bloco num recipiente `databoxdisk-invalid-pb-`com prefixo .|
|`InvalidAzureFileUploadAsBlockBlob` | Carregado como bolhas de bloco num recipiente `databoxdisk-invalid-af`com prefixo .|
|`InvalidManagedDiskUploadAsBlockBlob` | Carregado como bolhas de bloco num recipiente `databoxdisk-invalid-md`com prefixo .|
|`InvalidManagedDiskUploadAsPageBlob` |Carregado como bolhas de página num recipiente `databoxdisk-invalid-md-`com prefixo . |
|`MovedToOverflowShare` |Os ficheiros enviados para uma nova ação, uma vez que o tamanho original das ações excedia o limite máximo de tamanho do Azure. O novo nome da partilha de ficheiros tem o nome original sufixo com `-2`.   |
|`MovedToDefaultAzureShare` |Ficheiros carregados que não faziam parte de nenhuma pasta para uma parte padrão. O nome da `databox-`partilha começa com. |
|`ContainerRenamed` |O contentor destes ficheiros não se conformou com as convenções de nomeação do Azure e foi renomeado. O novo nome `databox-` começa com e é sufixo com o haste SHA1 do nome original |
|`ShareRenamed` |A parte destes ficheiros não se conformou com as convenções de nomeação do Azure e é renomeada. O novo nome `databox-` começa com e é sufixo com o haste SHA1 do nome original. |
|`BlobRenamed` |Estes ficheiros não se conformavam com as convenções de nomeação do Azure e foram renomeados. Verifique `BlobPath` o campo para o novo nome. |
|`FileRenamed` |Estes ficheiros não se conformavam com as convenções de nomeação do Azure e foram renomeados. Verifique `FileStoragePath` o campo para o novo nome. |
|`DiskRenamed` |Estes ficheiros não se conformavam com as convenções de nomeação do Azure e foram renomeados. Verifique `BlobPath` o campo para o novo nome. |


## <a name="next-steps"></a>Passos seguintes

- Abra um bilhete de [suporte para problemas do Disco](data-box-disk-contact-microsoft-support.md)de Caixa de Dados.
