---
title: Problemas com os registos de carregamento de dados de resolução de problemas de disco de caixa de dados do Azure | Documentos da Microsoft
description: Descreve como utilizar os registos e resolver problemas de visto ao carregar dados para o disco do Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: deaa9a220ee4d765650779b40742225e300ffdb7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807499"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Compreender os registos para resolver problemas de carregamento de dados no disco do Azure Data Box

Este artigo aplica-se para o disco do Microsoft Azure Data Box e descreve os problemas que vê ao carregar dados para o Azure.

## <a name="about-upload-logs"></a>Sobre os registos de carregamento

Quando os dados são carregados para o Azure no datacenter, `_error.xml` e `_verbose.xml` ficheiros são gerados para cada conta de armazenamento. Estes registos são carregados para a mesma conta de armazenamento que foi utilizada para carregar dados. 

Ambos os registos estão no mesmo formato e contêm descrições XML dos eventos que ocorreu ao copiar os dados a partir do disco para a conta de armazenamento do Azure.

O registo verboso contém informações completas sobre o estado da operação de cópia para cada blob ou um ficheiro, ao passo que o registo de erros contém apenas as informações de blobs ou ficheiros que encontrou erros durante o carregamento.

O registo de erros com a mesma estrutura como o log detalhado, mas filtra operações bem-sucedidas.

## <a name="download-logs"></a>Transferir registos

Siga os passos seguintes para localizar os registos de carregamento.

1. Se houver algum erro ao carregar os dados para o Azure, o portal apresenta um caminho para a pasta onde estão localizados os registos de diagnóstico.

    ![Ligar a registos no portal](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Aceda a **waies**.

    ![erro e registos verbosos](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

Em cada caso, consulte os registos de erros e os registos verbosos. Selecione cada registo e transferir uma cópia local.

## <a name="sample-upload-logs"></a>Registos de carregamento de exemplo

Um exemplo do `_verbose.xml` é mostrado abaixo. Neste caso, o pedido for concluído com êxito sem erros.

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

Para a mesma ordem, uma amostra do `_error.xml` é mostrado abaixo.

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

Um exemplo do `_error.xml` é mostrado abaixo onde o pedido foi concluído com erros. 

O ficheiro de erro em vez disso tem um `Summary` erros de nível de seção e outra seção que contém todos os ficheiros. 

O `Summary` contém o `ValidationErrors` e o `CopyErrors`. Neste caso, 8 de ficheiros ou pastas que foram carregadas para o Azure e não havia nenhum erro de validação. Quando os dados foram copiados para a conta de armazenamento do Azure, 5 ficheiros ou pastas carregado com êxito. Os restantes 3 arquivos ou pastas foram mudar o nome de acordo com as convenções de nomenclatura de contentores do Azure e, em seguida, carregadas com êxito para o Azure.

O estado de nível de ficheiro estão na `BlobStatus` que descreve quaisquer ações executadas para carregar os blobs. Neste caso, três contentores são mudar o nome porque as pastas para que os dados foram copiados não cumpriu com as convenções de nomenclatura do Azure para contentores. Para blobs desses contentores carregados, o novo nome de contentor, o caminho do blob no Azure, o caminho de ficheiro inválido original e o tamanho do blob são incluídos.
    
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

## <a name="data-upload-errors"></a>Erros de carregamento de dados

Os erros gerados quando o carregamento dos dados para o Azure estão resumidos na tabela seguinte.

| Código de erro | Descrição                   |
|-------------|------------------------------|
|`None` |  Foi concluída com êxito.           |
|`Renamed` | Mudar o nome com êxito o blob.   |
|`CompletedWithErrors` | Carregamento concluído com erros. Os detalhes dos ficheiros de erro são incluídos no ficheiro de registo.  |
|`Corrupted`|CRC calculado durante a ingestão de dados não corresponde ao CRC calculado durante o carregamento.  |  
|`StorageRequestFailed` | Falha no pedido de armazenamento do Azure.   |     
|`LeasePresent` | Este item é concedido e está a ser utilizado por outro utilizador. |
|`StorageRequestForbidden` |Não foi possível carregar devido a problemas de autenticação. |
|`ManagedDiskCreationTerminalFailure` | Não foi possível carregar como discos geridos. Os ficheiros estão disponíveis na conta de armazenamento de teste como blobs de páginas. Pode converter manualmente os blobs de páginas para discos geridos.  |
|`DiskConversionNotStartedTierInfoMissing` | Uma vez que o ficheiro VHD foi copiado fora as pastas de escalão pré-criada, não foi criado um disco gerido. O ficheiro é carregado como blob de página para a conta de armazenamento de teste conforme especificado durante a criação de ordem. Pode convertê-lo manualmente para um disco gerido.|
|`InvalidWorkitem` | Não foi possível carregar os dados à medida que ele não está em conformidade com a nomenclatura do Azure e limita as convenções.|
|`InvalidPageBlobUploadAsBlockBlob` | Carregar como blobs de blocos num contentor com o prefixo `databoxdisk-invalid-pb-`.|
|`InvalidAzureFileUploadAsBlockBlob` | Carregar como blobs de blocos num contentor com o prefixo `databoxdisk-invalid-af`-.|
|`InvalidManagedDiskUploadAsBlockBlob` | Carregar como blobs de blocos num contentor com o prefixo `databoxdisk-invalid-md`-.|
|`InvalidManagedDiskUploadAsPageBlob` |Carregar como blobs de páginas num contentor com o prefixo `databoxdisk-invalid-md-`. |
|`MovedToOverflowShare` |Os ficheiros carregados para uma nova partilha de tamanho da partilha original excederam o limite de tamanho máximo do Azure. O novo nome de partilha de ficheiros tem o nome original com o sufixo `-2`.   |
|`MovedToDefaultAzureShare` |Ficheiros carregados que não estavam uma parte de qualquer pasta numa partilha de predefinição. O nome da partilha começa com `databox-`. |
|`ContainerRenamed` |O contentor para estes ficheiros não está em conformidade com as convenções de nomenclatura do Azure e é alterado. O novo nome começa com `databox-` e é o sufixo com o hash SHA1 do nome original |
|`ShareRenamed` |A partilha para esses ficheiros não está em conformidade com as convenções de nomenclatura do Azure e é alterada. O novo nome começa com `databox-` e é o sufixo com o hash SHA1 do nome original. |
|`BlobRenamed` |Estes ficheiros não está em conformidade com as convenções de nomenclatura do Azure e foram renomeados. Verifique o `BlobPath` campo para o novo nome. |
|`FileRenamed` |Estes ficheiros não está em conformidade com as convenções de nomenclatura do Azure e foram renomeados. Verifique o `FileStoragePath` campo para o novo nome. |
|`DiskRenamed` |Estes ficheiros não está em conformidade com as convenções de nomenclatura do Azure e foram renomeados. Verifique o `BlobPath` campo para o novo nome. |


## <a name="next-steps"></a>Passos Seguintes

- [Abra um pedido de suporte para problemas de disco Data Box](data-box-disk-contact-microsoft-support.md).
