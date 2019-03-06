---
title: Fluxo de trabalho de exemplo para a preparação de unidades de disco rígido para uma tarefa de importação de importar/exportar do Azure | Documentos da Microsoft
description: Ver um passo a passo para o processo completo de preparar as unidades para uma tarefa de importação no serviço importar/exportar do Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/07/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 42da285fbb55df43959506996bcde9cf547c2a22
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439270"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Fluxo de trabalho de amostra para preparar unidades de disco rígido para uma tarefa de importação

Este artigo orienta-o processo completo de preparar as unidades para uma tarefa de importação.

## <a name="sample-data"></a>Dados de exemplo

Neste exemplo importa os seguintes dados para uma conta de armazenamento do Azure com o nome `mystorageaccount`:

|Localização|Descrição|Tamanho dos dados|
|--------------|-----------------|-----|
|H:\Video\ |Uma coleção de vídeos|12 TB|
|H:\Photo\ |Uma coleção de fotos|30 GB|
|K:\Temp\FavoriteMovie.ISO|Imagem de disco A Blu-ray™|25 GB|
|\\\bigshare\john\music\ |Uma coleção de arquivos de música num compartilhamento de rede|10 GB|

## <a name="storage-account-destinations"></a>Destinos de conta de armazenamento

A tarefa de importação irá importar os dados para os seguintes destinos na conta de armazenamento:

|Origem|Diretório virtual de destino ou de BLOBs|
|------------|-------------------------------------------|
|H:\Video\ |vídeo /|
|H:\Photo\ |fotografia /|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |Música|

Com este mapeamento, o ficheiro `H:\Video\Drama\GreatMovie.mov` vão ser importados para o blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Determinar os requisitos de disco rígido

Em seguida, para determinar quantas unidades de disco rígido são necessários, o tamanho dos dados de computação:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

Neste exemplo, duas unidades de disco rígido de 8TB devem ser suficientes. No entanto, desde o diretório de origem `H:\Video` tem 12TB de dados e a capacidade do disco rígido único é apenas de 8TB, será possível especificar isso na seguinte forma no **driveset.csv** ficheiro:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
A ferramenta vai distribuir dados em dois discos rígidos de forma otimizada.

## <a name="attach-drives-and-configure-the-job"></a>Ligar a unidades e configurar a tarefa
Anexe ambos os discos na máquina e criar volumes. Em seguida, de autor **dataset.csv** ficheiro:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Além disso, pode definir os seguintes metadados para todos os ficheiros:

* **UploadMethod:** Serviço de importação/exportação do Windows Azure
* **DataSetName:** SampleData
* **CreationDate:** 10/1/2013

Para definir metadados para os ficheiros de importados, crie um ficheiro de texto, `c:\WAImportExport\SampleMetadata.txt`, com o seguinte conteúdo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Também pode definir algumas propriedades para o `FavoriteMovie.ISO` blob:

* **Content-Type:** application/octet-stream
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==
* **Controlo de cache:** no cache

Para definir estas propriedades, crie um ficheiro de texto, `c:\WAImportExport\SampleProperties.txt`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Execute a ferramenta de importação/exportação do Azure (WAImportExport.exe)

Agora, está pronto para executar a ferramenta de importação/exportação do Azure para preparar as duas unidades de disco rígidas.

**Para a primeira sessão:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Se todos os dados mais precisam ser adicionado, crie outro ficheiro de conjunto de dados (mesmo formato que o conjunto de dados inicial).

**Para a segunda sessão:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Depois de concluíram as sessões de cópia, pode desligar as duas unidades do computador cópia e enviá-los para o Centro de dados do Azure adequada. Carregue os ficheiros de dois diário `<FirstDriveSerialNumber>.xml` e `<SecondDriveSerialNumber>.xml`, ao criar a tarefa de importação no portal do Azure.

## <a name="next-steps"></a>Passos Seguintes

* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Referência rápida para comandos utilizados com frequência](../storage-import-export-tool-quick-reference.md)
