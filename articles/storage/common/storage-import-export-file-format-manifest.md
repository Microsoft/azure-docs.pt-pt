---
title: Formato de ficheiro de manifesto de importação/exportação do Azure | Documentos da Microsoft
description: Saiba mais sobre o formato do ficheiro de manifesto unidade que descreve o mapeamento entre os blobs no armazenamento de Blobs do Azure e os ficheiros numa unidade numa importação ou exportação tarefa no serviço de importação/exportação.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ee53cc3a639a79e1b29ac6cd537bfb04e05b1bca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478628"
---
# <a name="azure-importexport-service-manifest-file-format"></a>Formato de ficheiro de manifesto do serviço de importação/exportação do Azure
O ficheiro de manifesto de unidade descreve o mapeamento entre os blobs no armazenamento de Blobs do Azure e os ficheiros numa unidade que consiste numa tarefa de importação ou exportação. Para uma operação de importação, o arquivo de manifesto é criado como parte do processo de preparação de unidade e armazenado na unidade antes da unidade é enviada para o Centro de dados do Azure. Durante uma operação de exportação, o manifesto é criado e armazenado na unidade pelo serviço importar/exportar do Azure.  
  
Para ambos importar e exportar tarefas, o arquivo de manifesto de unidade é armazenado na unidade de importação ou exportação; não são transmitido para o serviço por meio de qualquer operação de API.  
  
A seguir descreve o formato geral de um arquivo de manifesto de unidade:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveManifest Version="2014-11-01">  
  <Drive>  
    <DriveId>drive-id</DriveId>  
    import-export-credential  
  
    <!-- First Blob List -->  
    <BlobList>  
      <!-- Global properties and metadata that applies to all blobs -->  
      [<MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>]  
      [<PropertiesPath   
        Hash="md5-hash">global-properties-file-path</PropertiesPath>]  
  
      <!-- First Blob -->  
      <Blob>  
        <BlobPath>blob-path-relative-to-account</BlobPath>  
        <FilePath>file-path-relative-to-transfer-disk</FilePath>  
        [<ClientData>client-data</ClientData>]  
        [<Snapshot>snapshot</Snapshot>]  
        <Length>content-length</Length>  
        [<ImportDisposition>import-disposition</ImportDisposition>]  
        page-range-list-or-block-list          
        [<MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>]  
        [<PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>]  
      </Blob>  
  
      <!-- Second Blob -->  
      <Blob>  
      . . .  
      </Blob>  
    </BlobList>  
  
    <!-- Second Blob List -->  
    <BlobList>  
    . . .  
    </BlobList>  
  </Drive>  
</DriveManifest>  
  
import-export-credential ::=   
  <StorageAccountKey>storage-account-key</StorageAccountKey> | <ContainerSas>container-sas</ContainerSas>  
  
page-range-list-or-block-list ::=   
  page-range-list | block-list  
  
page-range-list ::=   
    <PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
    </PageRangeList>  
  
block-list ::=  
    <BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       Hash="md5-hash"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       Hash="md5-hash"/>]  
    </BlockList>  

```

## <a name="manifest-xml-elements-and-attributes"></a>Manifesto elementos e atributos XML

Os elementos de dados e atributos do formato XML do manifesto unidade são especificados na tabela seguinte.  
  
|Elemento XML|Tipo|Descrição|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Elemento de raiz|O elemento raiz do ficheiro de manifesto. Todos os outros elementos no arquivo são abaixo deste elemento.|  
|`Version`|Atributo de cadeia de caracteres|A versão do ficheiro de manifesto.|  
|`Drive`|Elemento XML aninhado|Contém o manifesto para cada unidade.|  
|`DriveId`|String|O identificador exclusivo de unidade para a unidade. O identificador de unidade encontra-se ao consultar a unidade para o número de série. O número de série da unidade normalmente é impresso no lado de fora da unidade também. O `DriveID` elemento tem de aparecer antes de qualquer `BlobList` elemento no arquivo de manifesto.|  
|`StorageAccountKey`|String|Necessário para tarefas de importação se e apenas se `ContainerSas` não for especificado. A chave de conta para a conta de armazenamento do Azure associados à tarefa.<br /><br /> Este elemento é omitido do manifesto para uma operação de exportação.|  
|`ContainerSas`|String|Necessário para tarefas de importação se e apenas se `StorageAccountKey` não for especificado. O contentor de SAS para aceder aos blobs associados à tarefa. Ver [colocar tarefa](/rest/api/storageimportexport/jobs) para seu formato. Este elemento é omitido do manifesto para uma operação de exportação.|  
|`ClientCreator`|String|Especifica o cliente que criou o arquivo XML. Este valor não é interpretado pelo serviço de importação/exportação.|  
|`BlobList`|Elemento XML aninhado|Contém uma lista de blobs que fazem parte da importação ou exportação de tarefa. Cada blob numa lista de BLOBs compartilha os mesmos metadados e propriedades.|  
|`BlobList/MetadataPath`|String|Opcional. Especifica o caminho relativo de um ficheiro no disco que contém os metadados predefinidos que serão definidos nos blobs da lista de BLOBs para uma operação de importação. Estes metadados podem ser substituído, opcionalmente, de forma de blob por blob.<br /><br /> Este elemento é omitido do manifesto para uma operação de exportação.|  
|`BlobList/MetadataPath/@Hash`|Atributo de cadeia de caracteres|Especifica o valor de hash MD5 Base16 codificado para o ficheiro de metadados.|  
|`BlobList/PropertiesPath`|String|Opcional. Especifica o caminho relativo de um ficheiro no disco que contém as propriedades predefinidas que serão definidas nos blobs da lista de BLOBs para uma operação de importação. Estas propriedades podem ser substituídas, opcionalmente, de forma de blob por blob.<br /><br /> Este elemento é omitido do manifesto para uma operação de exportação.|  
|`BlobList/PropertiesPath/@Hash`|Atributo de cadeia de caracteres|Especifica o valor de hash MD5 Base16 codificado para o ficheiro de propriedades.|  
|`Blob`|Elemento XML aninhado|Contém informações sobre cada blob em cada lista de Blobs.|  
|`Blob/BlobPath`|String|O URI relativo para o blob, começando com o nome do contentor. Se o blob estiver no contêiner raiz, tem de começar com `$root`.|  
|`Blob/FilePath`|String|Especifica o caminho relativo para o ficheiro na unidade. Para tarefas de exportação, será utilizado o caminho do blob para o caminho do ficheiro se possível; *por exemplo,* , `pictures/bob/wild/desert.jpg` serão exportados para `\pictures\bob\wild\desert.jpg`. No entanto, devido às limitações dos nomes NTFS, um blob pode ser exportado para um ficheiro com um caminho que não se assemelha o caminho do blob.|  
|`Blob/ClientData`|String|Opcional. Contém os comentários do cliente. Este valor não é interpretado pelo serviço de importação/exportação.|  
|`Blob/Snapshot`|DateTime|Opcional para tarefas de exportação. Especifica o identificador de instantâneo de um instantâneo de blob exportado.|  
|`Blob/Length`|Integer|Especifica o comprimento total do blob em bytes. O valor pode ser até 200 GB para um blob de blocos e até 1 TB para um blob de página. Para um blob de página, este valor tem de ser um múltiplo de 512.|  
|`Blob/ImportDisposition`|String|Opcional para tarefas de importação, omitidas para tarefas de exportação. Esta ação Especifica a forma como o serviço importar/exportar deve processar o caso de uma tarefa de importação onde um blob com o mesmo nome já existe. Se este valor for omitido a partir do manifesto de importação, o valor predefinido é `rename`.<br /><br /> Os valores para este elemento incluem:<br /><br /> -   `no-overwrite`: Se já existe um blob de destino com o mesmo nome, a operação de importação irá ignorar este ficheiro a importar.<br />-   `overwrite`: Qualquer blob de destino existente será substituído completamente pelo ficheiro recentemente importado.<br />-   `rename`: O blob novo será carregado com um nome modificado.<br /><br /> A regra de mudança de nome é o seguinte:<br /><br /> – Se o nome do blob não contém um ponto, é gerado um nome novo, acrescentando `(2)` para o nome de blob original; se esse novo nome também está em conflito com um nome de blob existente, em seguida, `(3)` é acrescentado em vez de `(2)`; e assim por diante.<br />– Se o nome do blob contém um ponto, a parte após o último ponto é considerada o nome de extensão. Semelhante ao procedimento acima, `(2)` é inserido antes de tenta o último ponto para gerar um novo nome; se o novo nome ainda está em conflito com uma nome, em seguida, o serviço de BLOBs `(3)`, `(4)`e assim por diante, até que seja encontrado um nome não conflitantes.<br /><br /> Alguns exemplos:<br /><br /> O blob `BlobNameWithoutDot` passará a ser denominado:<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> O blob `Seattle.jpg` passará a ser denominado:<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Elemento XML aninhado|É necessário para um blob de página.<br /><br /> Para uma importação operação, especifica uma lista de intervalos de byte de um ficheiro a importar. Cada intervalo de páginas é descrito por um desvio e comprimento no arquivo de origem que descreve o intervalo de páginas, juntamente com um hash MD5 da região. O `Hash` atributo de um intervalo de página é necessário. O serviço irá validar se o hash dos dados no blob corresponde do hash MD5 calculado do intervalo de página. Qualquer número de intervalos de página pode ser utilizado para descrever um ficheiro para uma importação, com o total de tamanho até 1 TB. Todos os intervalos de página tem de ser ordenados por desvio e sem sobreposições são permitidas.<br /><br /> Para uma exportação a operação, especifica um conjunto de intervalos de byte de um blob que foram exportados para a unidade.<br /><br /> Em conjunto, os intervalos de página podem abranger apenas secundárias intervalos de um blob ou um ficheiro.  Espera-se a parte restante do arquivo não abrangido por qualquer intervalo de páginas e seu conteúdo pode ser indefinido.|  
|`PageRange`|Elemento XML|Representa um intervalo de páginas.|  
|`PageRange/@Offset`|Atributo de número inteiro|Especifica o deslocamento no arquivo transferência e o blob onde começa o intervalo de páginas especificado. Este valor tem de ser um múltiplo de 512.|  
|`PageRange/@Length`|Atributo de número inteiro|Especifica o comprimento do intervalo de página. Este valor tem de ser um múltiplo de 512 e não mais de 4 MB.|  
|`PageRange/@Hash`|Atributo de cadeia de caracteres|Especifica o valor de hash MD5 Base16 codificado para o intervalo de página.|  
|`BlockList`|Elemento XML aninhado|É necessário para um blob de blocos com blocos com nome.<br /><br /> Para uma operação de importação, a lista de bloqueios Especifica um conjunto de blocos que serão importados para o armazenamento do Azure. Para uma operação de exportação, a lista de bloqueios Especifica onde cada bloco tenha sido armazenado no arquivo no disco de exportação. Cada bloco é descrito por um desvio no arquivo e um comprimento do bloco; cada bloco além disso é chamado por um atributo de ID de bloco e contém um hash MD5 para o bloco. Até 50 000 blocos pode ser utilizado para descrever um blob.  Todos os blocos têm de ser ordenados por deslocamento e juntos deve abranger o intervalo completo do ficheiro *, ou seja,* , não deve haver nenhum lacuna entre blocos. Se o blob é mais do que 64 MB, o IDs de bloco para cada bloco tem de ser ausência de tudo ou todos presentes. IDs de bloco têm de ser cadeias de caracteres codificada em Base64. Ver [colocação blocos](/rest/api/storageservices/put-block) para ainda mais os requisitos para IDs de bloco.|  
|`Block`|Elemento XML|Representa um bloco.|  
|`Block/@Offset`|Atributo de número inteiro|Especifica o deslocamento onde começa o bloco especificado.|  
|`Block/@Length`|Atributo de número inteiro|Especifica o número de bytes no bloco de; Este valor tem de ser não mais de 4MB.|  
|`Block/@Id`|Atributo de cadeia de caracteres|Especifica uma cadeia de caracteres que representa o ID de bloco para o bloco.|  
|`Block/@Hash`|Atributo de cadeia de caracteres|Especifica o hash MD5 Base16 codificado do bloco.|  
|`Blob/MetadataPath`|String|Opcional. Especifica o caminho relativo de um ficheiro de metadados. Durante a importação, os metadados é definido no blob de destino. Durante uma operação de exportação, os metadados do blob é armazenado no ficheiro de metadados na unidade.|  
|`Blob/MetadataPath/@Hash`|Atributo de cadeia de caracteres|Especifica o hash MD5 Base16 com codificação de ficheiro de metadados do blob.|  
|`Blob/PropertiesPath`|String|Opcional. Especifica o caminho relativo de um arquivo de propriedades. Durante a importação, as propriedades são definidas no blob de destino. Durante uma operação de exportação, as propriedades de blob são armazenadas no arquivo de propriedades na unidade.|  
|`Blob/PropertiesPath/@Hash`|Atributo de cadeia de caracteres|Especifica o hash MD5 Base16 codificado do arquivo de propriedades do blob.|  
  
## <a name="next-steps"></a>Passos Seguintes
 
* [API de REST de importação/exportação de armazenamento](/rest/api/storageimportexport/)
