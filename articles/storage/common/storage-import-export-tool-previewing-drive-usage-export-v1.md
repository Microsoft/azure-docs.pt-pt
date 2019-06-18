---
title: Pré-visualização da utilização da unidade para uma tarefa de exportação de importar/exportar do Azure - v1 | Documentos da Microsoft
description: Aprenda a pré-visualizar a lista de blobs que selecionou para uma tarefa de exportação no serviço importar/exportar do Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 53ab1e28c5864b403d52bf5e73f0c5c41b8f18a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478458"
---
# <a name="previewing-drive-usage-for-an-export-job"></a>Pré-visualização da utilização da unidade para uma tarefa de exportação
Antes de criar uma tarefa de exportação, terá de escolher um conjunto de blobs para ser exportada. O serviço de importação/exportação do Microsoft Azure permite-lhe utilizar uma lista de caminhos de blob ou BLOBs prefixos para representar os blobs que selecionou.  
  
Em seguida, terá de determinar quantas unidades tiver de enviar. A ferramenta de importação/exportação fornece o `PreviewExport` comando para pré-visualizar a utilização da unidade para os blobs que selecionou, com base no tamanho das unidades de que pretende utilizar.

## <a name="command-line-parameters"></a>Parâmetros da linha de comandos

Pode utilizar os seguintes parâmetros ao utilizar o `PreviewExport` comando da ferramenta importar/exportar.

|Parâmetro da linha de comandos|Descrição|  
|--------------------------|-----------------|  
|**/logdir:** <LogDirectory\>|Opcional. O diretório de registo. Ficheiros de registo verboso serão escritos para este diretório. Se não for especificado nenhum diretório de registo, será utilizado o diretório atual como o diretório de registo.|  
|**/sn:** <StorageAccountName\>|Necessário. O nome da conta de armazenamento para a tarefa de exportação.|  
|**/sk:** <StorageAccountKey\>|Necessário se e apenas se não for especificado um SAS de contentor. A chave de conta para a conta de armazenamento para a tarefa de exportação.|  
|**/csas:** <ContainerSas\>|Necessário se e apenas se não for especificada uma chave de conta de armazenamento. O contentor de SAS para listar os blobs para ser exportado na tarefa de exportação.|  
|**/ExportBlobListFile:** <ExportBlobListFile\>|Necessário. Caminho para o XML que contêm lista de caminhos dos BLOBs de ficheiros ou prefixos de caminho para os blobs ser exportada de Blobs. O formato de ficheiro utilizado na `BlobListBlobPath` elemento no [colocar tarefa](/rest/api/storageimportexport/jobs) operação de REST API do serviço de importação/exportação.|  
|**/DriveSize:** <DriveSize\>|Necessário. O tamanho das unidades a utilizar para uma tarefa de exportação *por exemplo,* , 500 GB, 1,5 TB.|  

## <a name="command-line-example"></a>Exemplo de linha de comandos

O exemplo seguinte demonstra a `PreviewExport` comando:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
O ficheiro de lista de blob de exportação pode conter nomes de BLOBs e BLOBs de prefixos, conforme mostrado aqui:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

A ferramenta de importação/exportação do Azure apresenta uma lista de todos os blobs para ser exportada calcula como empacotá-los em unidades de tamanho especificado, tendo em conta a qualquer sobrecarga necessária, e estima o número de unidades necessária para armazenar os blobs e as informações de utilização da unidade.  
  
Eis um exemplo da saída, com registos informativas omitido:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```  
  
## <a name="next-steps"></a>Passos Seguintes

* [Referência da ferramenta importar/exportar do Azure](../storage-import-export-tool-how-to-v1.md)
