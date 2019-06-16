---
title: Azure formato de ficheiro dos metadados e propriedades de importação/exportação | Documentos da Microsoft
description: Saiba como especificar dos metadados e propriedades para um ou mais blobs que fazem parte de uma importação ou exportação de tarefa.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 2066d4a2ed6db97285d92d15e14dbd21629dbdfa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478560"
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Azure importar/exportar metadados e propriedades de formato de ficheiro serviço
Pode especificar propriedades para um ou mais blobs e metadados como parte de uma tarefa de importação ou uma tarefa de exportação. Para definir metadados ou as propriedades de blobs a ser criadas como parte de uma tarefa de importação, forneça um ficheiro de metadados ou as propriedades no disco rígido que contém os dados a serem importados. Para uma tarefa de exportação, metadados e propriedades são escritas para um ficheiro de metadados ou as propriedades que está incluído no disco rígido retornado para.  
  
## <a name="metadata-file-format"></a>Formato de ficheiro de metadados  
O formato de um ficheiro de metadados é o seguinte:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|Elemento XML|Tipo|Descrição|  
|-----------------|----------|-----------------|  
|`Metadata`|Elemento de raiz|O elemento raiz do ficheiro de metadados.|  
|`metadata-name`|String|Opcional. O elemento XML Especifica o nome dos metadados do blob e seu valor Especifica o valor da definição de metadados.|  
  
## <a name="properties-file-format"></a>Formato de arquivo de propriedades  
O formato de um arquivo de propriedades é o seguinte:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|Elemento XML|Tipo|Descrição|  
|-----------------|----------|-----------------|  
|`Properties`|Elemento de raiz|O elemento raiz do arquivo de propriedades.|  
|`Last-Modified`|String|Opcional. A hora de última modificação de mensagens em fila para o blob. Para tarefas de exportação apenas.|  
|`Etag`|String|Opcional. Valor de ETag do blob. Para tarefas de exportação apenas.|  
|`Content-Length`|String|Opcional. O tamanho do blob em bytes. Para tarefas de exportação apenas.|  
|`Content-Type`|String|Opcional. O tipo de conteúdo do blob.|  
|`Content-MD5`|String|Opcional. Hash do MD5 do blob.|  
|`Content-Encoding`|String|Opcional. Conteúdo do blob de codificação.|  
|`Content-Language`|String|Opcional. Idioma do conteúdo do blob.|  
|`Cache-Control`|String|Opcional. A cadeia de controlo de cache para o blob.|  

## <a name="next-steps"></a>Passos Seguintes

Ver [definir as propriedades do blob](/rest/api/storageservices/set-blob-properties), [definir metadados do Blob](/rest/api/storageservices/set-blob-metadata), e [recursos de blob de definição e ao obter propriedades e metadados para](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) para regras detalhadas sobre os metadados do blob de definição e propriedades.
