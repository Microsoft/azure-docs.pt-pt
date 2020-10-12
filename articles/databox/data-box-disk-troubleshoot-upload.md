---
title: Envios de dados de resolução de problemas usando registos
titleSuffix: Azure Data Box Disk
description: Descreve como utilizar os registos e problemas de resolução de problemas vistos ao enviar dados para O Disco de Caixa de Dados Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7225b04908753bb7c07ac89510859bac9db5b89c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85565020"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Compreenda os registos para resolver problemas de dados no disco de caixa de dados Azure

Este artigo aplica-se ao Disco de Caixa de Dados do Microsoft Azure e descreve os problemas que vê quando envia dados para o Azure.

## <a name="about-upload-logs"></a>Sobre upload de registos

Quando os dados são enviados para o Azure no datacenter, `_error.xml` e `_verbose.xml` os ficheiros são gerados para cada conta de armazenamento. Estes registos são enviados para a mesma conta de armazenamento que foi usada para carregar dados. 

Ambos os registos estão no mesmo formato e contêm descrições XML dos eventos ocorridos enquanto copiam os dados do disco para a conta de Armazenamento Azure.

O registo verboso contém informações completas sobre o estado da operação de cópia para cada bolha ou ficheiro, enquanto o registo de erros contém apenas as informações para bolhas ou ficheiros que encontraram erros durante o upload.

O registo de erros tem a mesma estrutura que o log verboso, mas filtra as operações bem sucedidas.

## <a name="download-logs"></a>Registos de transferências

Tome os seguintes passos para localizar os registos de upload.

1. Se houver erros ao carregar os dados para o Azure, o portal apresenta um caminho para a pasta onde estão localizados os registos de diagnóstico.

    ![Link para registos no portal](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Vai para **os waies.**

    ![erro e registos verbosos](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

Em cada caso, vê os registos de erro e os registos verbosos. Selecione cada registo e descarregue uma cópia local.

## <a name="sample-upload-logs"></a>Registos de upload de amostras

Uma amostra do `_verbose.xml` é mostrada abaixo. Neste caso, a encomenda foi concluída com sucesso sem erros.

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

Para a mesma ordem, uma amostra do `_error.xml` é mostrada abaixo.

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

Uma amostra do `_error.xml` é mostrada abaixo onde a encomenda foi completada com erros. 

O ficheiro de erro neste caso tem uma `Summary` secção e outra secção que contém todos os erros do nível do ficheiro. 

O `Summary` contém o e o `ValidationErrors` `CopyErrors` . Neste caso, 8 ficheiros ou pastas foram enviados para o Azure e não houve erros de validação. Quando os dados foram copiados para a conta de Armazenamento Azure, 5 ficheiros ou pastas foram carregados com sucesso. Os restantes 3 ficheiros ou pastas foram renomeados de acordo com as convenções de nomeação do contentor Azure e depois enviados com sucesso para Azure.

O estado do nível do ficheiro é `BlobStatus` no local que descreve quaisquer ações tomadas para carregar as bolhas. Neste caso, três contentores são renomeados porque as pastas às quais os dados foram copiados não estão em conformidade com as convenções de nomeação Azure para contentores. Para as bolhas carregadas nesses recipientes, o novo nome do recipiente, o caminho da bolha em Azure, o caminho original do ficheiro inválido e o tamanho da bolha estão incluídos.
    
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

Os erros gerados ao carregar os dados para Azure são resumidos na tabela seguinte.

| Código de erro | Descrição                   |
|-------------|------------------------------|
|`None` |  Concluído com sucesso.           |
|`Renamed` | Renomeou com sucesso a bolha.   |
|`CompletedWithErrors` | Upload concluído com erros. Os detalhes dos ficheiros em erro estão incluídos no ficheiro de registo.  |
|`Corrupted`|CRC calculado durante a ingestão de dados não corresponde ao CRC calculado durante o upload.  |  
|`StorageRequestFailed` | O pedido de armazenamento Azure falhou.   |     
|`LeasePresent` | Este artigo é alugado e está a ser utilizado por outro utilizador. |
|`StorageRequestForbidden` |Não foi possível fazer o upload devido a problemas de autenticação. |
|`ManagedDiskCreationTerminalFailure` | Não foi possível carregar como discos geridos. Os ficheiros estão disponíveis na conta de armazenamento de encenação como bolhas de página. Pode converter manualmente bolhas de página em discos geridos.  |
|`DiskConversionNotStartedTierInfoMissing` | Uma vez que o ficheiro VHD foi copiado fora das pastas de nível pré-recriadas, não foi criado um disco gerido. O ficheiro é carregado como bolha de página para a conta de armazenamento de encenação, conforme especificado durante a criação da encomenda. Pode convertê-lo manualmente num disco gerido.|
|`InvalidWorkitem` | Não foi possível fazer o upload dos dados, uma vez que não está em conformidade com as convenções de nomeação e limitação do Azure.|
|`InvalidPageBlobUploadAsBlockBlob` | Carregado como bolhas de bloco num recipiente com prefixo `databoxdisk-invalid-pb-` .|
|`InvalidAzureFileUploadAsBlockBlob` | Carregado como bolhas de bloco num recipiente com `databoxdisk-invalid-af` prefixo..|
|`InvalidManagedDiskUploadAsBlockBlob` | Carregado como bolhas de bloco num recipiente com `databoxdisk-invalid-md` prefixo..|
|`InvalidManagedDiskUploadAsPageBlob` |Carregado como bolhas de página num recipiente com prefixo `databoxdisk-invalid-md-` . |
|`MovedToOverflowShare` |Os ficheiros enviados para uma nova ação, uma vez que o tamanho original da ação excedeu o limite máximo de tamanho Azure. O novo nome da partilha de ficheiros tem o nome original sufixado com `-2` .   |
|`MovedToDefaultAzureShare` |Carregado ficheiros que não faziam parte de nenhuma pasta para uma parte predefinida. O nome da partilha começa `databox-` com. |
|`ContainerRenamed` |O contentor destes ficheiros não se conformava com as convenções de nomeação do Azure e foi renomeado. O novo nome começa com `databox-` e é sufixado com o haxixe SHA1 do nome original |
|`ShareRenamed` |A parte destes ficheiros não estava em conformidade com as convenções de nomeação do Azure e foi renomeada. O novo nome começa `databox-` com e é sufixado com o haxixe SHA1 do nome original. |
|`BlobRenamed` |Estes ficheiros não estavam em conformidade com as convenções de nomeação do Azure e foram renomeados. Verifique o `BlobPath` novo nome no campo. |
|`FileRenamed` |Estes ficheiros não estavam em conformidade com as convenções de nomeação do Azure e foram renomeados. Verifique o `FileStoragePath` novo nome no campo. |
|`DiskRenamed` |Estes ficheiros não estavam em conformidade com as convenções de nomeação do Azure e foram renomeados. Verifique o `BlobPath` novo nome no campo. |


## <a name="next-steps"></a>Passos seguintes

- [Abra um bilhete de suporte para problemas de caixa de dados](data-box-disk-contact-microsoft-support.md).
