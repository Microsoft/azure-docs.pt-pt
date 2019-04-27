---
title: Definir propriedades e metadados através de importar/exportar do Azure - v1 | Documentos da Microsoft
description: Aprenda a especificar propriedades e metadados para ser definida nos blobs de destino ao executar a ferramenta de importação/exportação do Azure para preparar as suas unidades. Refere-se para a v1 da ferramenta importar/exportar.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 66ae7045cfb94ec70f3b14046af736f784b88ea6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320555"
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Definir propriedades e metadados durante o processo de importação
Quando executar a ferramenta de importação/exportação do Microsoft Azure para preparar as suas unidades, pode especificar propriedades e metadados para ser definidas nos blobs de destino. Siga estes passos.  
  
1.  Para definir as propriedades do blob, crie um arquivo de texto no seu computador local que especifica os nomes das propriedades e valores.  
  
2.  Para definir os metadados do blob, crie um ficheiro de texto no seu computador local que especifica os nomes dos metadados e valores.  
  
3.  Passar o caminho completo para um ou ambos os ficheiros para a ferramenta de importação/exportação do Azure como parte do `PrepImport` operação.  
  
> [!NOTE]
>  Quando especificar um ficheiro de metadados ou propriedades como parte de uma sessão de cópia, essas propriedades ou metadados estão definidas para todos os BLOBs que são importados como parte dessa sessão de cópia. Se pretender especificar um conjunto diferente de propriedades ou metadados para alguns dos blobs a ser importados, terá de criar uma sessão de cópia separada com diferentes propriedades ou arquivos de metadados.  
  
## <a name="specify-blob-properties-in-a-text-file"></a>Especificar as propriedades do Blob num arquivo de texto  
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
  
## <a name="specify-blob-metadata-in-a-text-file"></a>Especifique os metadados do Blob num arquivo de texto  
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
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>Criar uma sessão de cópia, incluindo as propriedades ou arquivos de metadados  
Quando executar a ferramenta de importação/exportação do Azure para preparar a tarefa de importação, especifique o ficheiro de propriedades no linha de comandos com o `PropertyFile` parâmetro. Especifique o ficheiro de metadados sobre como utilizar a linha de comandos a `/MetadataFile` parâmetro. Eis um exemplo que especifica ambos os ficheiros:  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="next-steps"></a>Passos Seguintes

* [Formato de ficheiro dos metadados e propriedades do serviço Importar/Exportar](../storage-import-export-file-format-metadata-and-properties.md)
