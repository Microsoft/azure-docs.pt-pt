---
title: Fluxo de trabalho de exemplo para a preparação de unidades de disco rígido para uma tarefa de importação de importar/exportar do Azure - v1 | Documentos da Microsoft
description: Ver um passo a passo para o processo completo de preparar as unidades para uma tarefa de importação no serviço importar/exportar do Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: b80ba1cbe168270ec591bdd38859408eae387bbf
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311718"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Fluxo de trabalho de amostra para preparar unidades de disco rígido para uma tarefa de importação
Este tópico descreve o processo completo de preparar as unidades para uma tarefa de importação.  
  
Neste exemplo importa os seguintes dados para uma conta de armazenamento do Windows Azure com o nome `mystorageaccount`:  
  
|Localização|Descrição|  
|--------------|-----------------|  
|H:\Video|Uma coleção de vídeos, 5 TB no total.|  
|H:\Photo|Uma coleção de fotos, 30 GB no total.|  
|K:\Temp\FavoriteMovie.ISO|Imagem de disco A Blu-ray™, 25 GB.|  
|\\\bigshare\john\music|Uma coleção de arquivos de música num compartilhamento de rede, 10 GB no total.|  
  
A tarefa de importação importa estes dados para os seguintes destinos na conta de armazenamento:  
  
|Origem|Diretório virtual de destino ou de BLOBs|  
|------------|-------------------------------------------|  
|H:\Video|https:\//mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|https:\//mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovie.ISO|https:\//mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https:\//mystorageaccount.blob.core.windows.net/music|  
  
Com este mapeamento, o ficheiro `H:\Video\Drama\GreatMovie.mov` são importados para o blob https:\//mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov.  
  
Em seguida, para determinar quantas unidades de disco rígido são necessários, o tamanho dos dados de computação:  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
Neste exemplo, duas unidades de disco rígido de 3 TB devem ser suficientes. No entanto, desde o diretório de origem `H:\Video` tem 5 TB de dados e a capacidade do disco rígido único é apenas 3 TB, é necessário interromper `H:\Video` em dois diretórios menores: `H:\Video1` e `H:\Video2`, antes de executar o Microsoft Azure Ferramenta de importação/exportação. Este passo gera os seguintes diretórios de origem:  
  
|Localização|Tamanho|Diretório virtual de destino ou de BLOBs|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 TB|https:\//mystorageaccount.blob.core.windows.net/video|  
|H:\Video2|2,5 TB|https:\//mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|30 GB|https:\//mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovies.ISO|25 GB|https:\//mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https:\//mystorageaccount.blob.core.windows.net/music|  
  
 Embora o `H:\Video`diretório foi dividido para dois diretórios, apontam para o diretório virtual de destino mesmo na conta de armazenamento. Dessa forma, todos os ficheiros de vídeo são mantidos num único `video` contentor na conta de armazenamento.  
  
 Em seguida, os diretórios de origem anterior sejam igualmente distribuídos para as duas unidades de disco rígidas:  
  
||||  
|-|-|-|  
|Unidade de disco rígido|Diretórios de origem|Tamanho total|  
|Primeira unidade|H:\Video1|2,5 TB + 30 GB|  
||H:\Photo||  
|Unidade de segundo|H:\Video2|2,5 TB + 35 GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Além disso, pode definir os seguintes metadados para todos os ficheiros:  
  
-   **UploadMethod:** Serviço de importação/exportação do Windows Azure  
  
-   **DataSetName:** SampleData  
  
-   **CreationDate:** 10/1/2013  
  
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
  
-   **Content-Type:** application/octet-stream  
  
-   **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==  
  
-   **Controlo de cache:** no cache  
  
Para definir estas propriedades, crie um ficheiro de texto, `c:\WAImportExport\SampleProperties.txt`:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Agora, está pronto para executar a ferramenta de importação/exportação do Azure para preparar as duas unidades de disco rígidas. Tenha em atenção que:  
  
-   A primeira unidade é montada como unidade X.  
  
-   A segunda unidade é montada como unidade Y.  
  
-   A chave da conta do storage `mystorageaccount` é `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Preparar o disco para importação quando previamente os dados são carregados
 
 Se os dados a importar já se encontra presentes no disco, utilize o sinalizador /skipwrite. O valor de /t e /srcdir deve ambos os ponto para o disco a ser preparado para importação. Se todos os dados a serem importados não será o mesmo diretório virtual de destino ou raiz da conta de armazenamento, execute o comando para cada diretório de destino em separado, manter o valor da/ID igual em todas as execuções.

>[!NOTE] 
>Não especifique /format como apagar os dados no disco. Pode especificar / encriptar ou /bk dependendo se o disco já estiver encriptado ou não. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

## <a name="copy-sessions---first-drive"></a>Copiar sessões - em primeiro lugar da unidade

Para a unidade do primeiro, execute a ferramenta de importação/exportação do Azure duas vezes para copiar os diretórios de dois origem:  

**Primeira sessão de cópia**
  
```
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

**Segunda sessão de cópia**

```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```

## <a name="copy-sessions---second-drive"></a>Copiar sessões - segunda unidade
 
Para a unidade do segundo, execute a ferramenta de importação/exportação do Azure três vezes, uma vez cada para os diretórios de origem e, uma vez para o ficheiro de imagem autónomos Blu-Ray™):  
  
**Primeira sessão de cópia** 

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
**Segunda sessão de cópia**

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
**Terceira sessão de cópia**  

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```

## <a name="copy-session-completion"></a>Conclusão de sessão de cópia

Depois de concluíram as sessões de cópia, pode desligar as duas unidades do computador cópia e enviá-los para o Centro de dados apropriado do Windows Azure. Carregue os ficheiros de dois diário `FirstDrive.jrn` e `SecondDrive.jrn`, ao criar a tarefa de importação no [portal do Azure](https://portal.azure.com).  
  
## <a name="next-steps"></a>Passos Seguintes

* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Referência rápida para comandos utilizados com frequência](../storage-import-export-tool-quick-reference-v1.md) 
