---
title: Definir propriedades e metadados através de importar/exportar do Azure | Documentos da Microsoft
description: Aprenda a especificar propriedades e metadados para ser definida nos blobs de destino ao executar a ferramenta de importação/exportação do Azure para preparar as suas unidades.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 3e2796d943fbc4a4ce99576de74b2fc5c77bcca9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320538"
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Definir propriedades e metadados durante o processo de importação

Quando executar a ferramenta de importação/exportação do Microsoft Azure para preparar as suas unidades, pode especificar propriedades e metadados para ser definidas nos blobs de destino. Siga estes passos.

1.  Para definir as propriedades do blob, crie um arquivo de texto no seu computador local que especifica os nomes das propriedades e valores.
2.  Para definir os metadados do blob, crie um ficheiro de texto no seu computador local que especifica os nomes dos metadados e valores.
3.  Passar o caminho completo para um ou ambos os ficheiros para a ferramenta de importação/exportação do Azure como parte do `PrepImport` operação.

> [!NOTE]
>  Quando especificar um ficheiro de metadados ou propriedades como parte de uma sessão de cópia, essas propriedades ou metadados estão definidas para todos os BLOBs que são importados como parte dessa sessão de cópia. Se pretender especificar um conjunto diferente de propriedades ou metadados para alguns dos blobs a ser importados, terá de criar uma sessão de cópia separada com diferentes propriedades ou arquivos de metadados.

## <a name="specify-blob-properties-in-a-text-file"></a>Especificar as propriedades do blob num arquivo de texto

Para especificar propriedades de blob, crie um ficheiro de texto local e incluir o XML que especifica os nomes das propriedades como elementos e os valores de propriedade como valores. Eis um exemplo que especifica alguns valores de propriedade:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

Guarde o ficheiro numa localização local, como `C:\WAImportExport\ImportProperties.txt`.

## <a name="specify-blob-metadata-in-a-text-file"></a>Especifique os metadados do blob num arquivo de texto

Da mesma forma, para especificar os metadados do blob, crie um ficheiro de texto local que especifica os nomes dos metadados como os elementos e valores de metadados como valores. Eis um exemplo que especifica alguns valores de metadados:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Guarde o ficheiro numa localização local, como `C:\WAImportExport\ImportMetadata.txt`.

## <a name="add-the-path-to-properties-and-metadata-files-in-datasetcsv"></a>Adicionar o caminho para as propriedades e os arquivos de metadados no dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,https://mystorageaccount.blob.core.windows.net/video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,https://mystorageaccount.blob.core.windows.net/photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,https://mystorageaccount.blob.core.windows.net/music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

## <a name="next-steps"></a>Passos Seguintes

* [Formato de ficheiro dos metadados e propriedades do serviço Importar/Exportar](../storage-import-export-file-format-metadata-and-properties.md)
