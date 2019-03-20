---
title: Preparar as unidades de disco rígidas para uma tarefa de importação de importar/exportar do Azure | Documentos da Microsoft
description: Saiba como preparar as unidades de disco rígido usando a ferramenta de WAImportExport para criar uma tarefa de importação para o serviço importar/exportar do Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 777e0aac46dbffb1e491874b5889667a888aadf5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57898691"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Preparar as unidades de disco rígidas para uma tarefa de importação

A ferramenta de WAImportExport é a ferramenta de preparação e reparação de unidade que pode utilizar com o [serviço de importação/exportação do Microsoft Azure](../storage-import-export-service.md). Pode utilizar esta ferramenta para copiar dados para as unidades de disco rígido que vai enviar num Datacenter do Azure. Depois de uma tarefa de importação foi concluída, pode utilizar esta ferramenta para reparar os blobs que foram corrompidos, estavam em falta ou que entraram em conflito com outros blobs. Depois de receber as unidades de uma tarefa de exportação concluída, pode utilizar esta ferramenta para reparar todos os ficheiros que foram danificado ou em falta nas unidades. Neste artigo, vamos abordar o uso dessa ferramenta.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="requirements-for-waimportexportexe"></a>Requisitos para WAImportExport.exe

- **Configuração da máquina**
  - Windows 7, Windows Server 2008 R2 ou um sistema de operativo mais recente do Windows
  - Deve ser instalado o .NET framework 4. Ver [FAQ](#faq) sobre como verificar se o .NET Framework está instalado na máquina.
- **Chave da conta de armazenamento** -tem de, pelo menos, uma das chaves de conta para a conta de armazenamento.

### <a name="preparing-disk-for-import-job"></a>Preparar o disco para a tarefa de importação

- **BitLocker -** BitLocker tem de estar ativado no computador com a ferramenta de WAImportExport. Consulte a [FAQ](#faq) para saber como ativar o BitLocker.
- **Discos** acessível a partir do computador no qual a ferramenta de WAImportExport é executada. Ver [FAQ](#faq) para especificação de disco.
- **Arquivos de origem** -os ficheiros que pretende importar tem de ser acessíveis a partir da máquina de cópia, estejam eles num compartilhamento de rede ou um disco rígido local.

### <a name="repairing-a-partially-failed-import-job"></a>Reparação de uma tarefa de importação falhou parcialmente

- **Ficheiro de registo de cópia** que é gerado quando o serviço importar/exportar do Azure copia dados entre a conta de armazenamento e de disco. O mesmo está localizado na sua conta de armazenamento de destino.

### <a name="repairing-a-partially-failed-export-job"></a>Reparação de uma tarefa de exportação falhou parcialmente

- **Ficheiro de registo de cópia** que é gerado quando o serviço importar/exportar do Azure copia dados entre a conta de armazenamento e de disco. O mesmo está localizado na sua conta de armazenamento de origem.
- **Ficheiro de manifesto** -Located [opcional] na exportados unidade que foi devolvida pela Microsoft.

## <a name="download-and-install-waimportexport"></a>Transferir e instalar WAImportExport

Transfira o [versão mais recente do WAImportExport.exe](https://www.microsoft.com/download/details.aspx?id=55280). Extraia o conteúdo compactado para um diretório no seu computador.

Sua próxima tarefa é criar ficheiros CSV.

## <a name="prepare-the-dataset-csv-file"></a>Preparar o ficheiro CSV de conjunto de dados

### <a name="what-is-dataset-csv"></a>O que é o conjunto de dados CSV

Ficheiro CSV de conjunto de dados é que o valor do sinalizador de /dataset é um ficheiro CSV que contém uma lista de diretórios e/ou uma lista de ficheiros ser copiados para unidades de destino. O primeiro passo para criar uma tarefa de importação é determinar a quais diretórios e arquivos que pretende importar. Isso pode ser uma lista de diretórios, uma lista de ficheiros exclusivos ou uma combinação dessas duas. Quando um diretório está incluído, todos os ficheiros no diretório e respetivos subdiretórios vão fazer parte da tarefa de importação.

Para cada diretório ou ficheiro a importar, tem de identificar um diretório virtual de destino ou os BLOBs no serviço Blob do Azure. Usará esses destinos como entradas para a ferramenta de WAImportExport. Diretórios devem ser delimitados com o caráter de barra "/".

A tabela seguinte mostra alguns exemplos de destinos de blob:

| Ficheiro de origem ou diretório | Blob de destino ou diretório virtual |
| --- | --- |
| H:\Video | https://mystorageaccount.blob.core.windows.net/video |
| H:\Photo | https://mystorageaccount.blob.core.windows.net/photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.blob.core.windows.net/music |

### <a name="sample-datasetcsv"></a>Dataset.csv de exemplo

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>Campos do ficheiro CSV de conjunto de dados

| Campo | Descrição |
| --- | --- |
| BasePath | **[Necessário]**<br/>O valor deste parâmetro representa a origem de onde os dados a serem importados estão localizados. A ferramenta irá recursivamente copiar todos os dados localizados sob esse caminho.<br><br/>**Valores permitidos**: Isso tem de ser um caminho válido no computador local ou um caminho de partilha válido e deve estar acessível pelo utilizador. O caminho do diretório tem de ser um caminho absoluto (não um caminho relativo). Se o caminho de terminar com "\\", ele representa um diretório mais um caminho final sem"\\" representa um ficheiro.<br/>Não existem regex é permitido neste campo. Se o caminho contiver espaços, coloque-o "".<br><br/>**Example**: "c:\Directory\c\Directory\File.txt"<br>"\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[Necessário]**<br/> O caminho para o diretório virtual de destino na sua conta de armazenamento do Windows Azure. O diretório virtual pode ou não pode já existir. Se não existir, o serviço importar/exportar criará uma.<br/><br/>Certifique-se de que utilizar nomes de contentor válido ao especificar os diretórios virtuais de destino ou os blobs. Tenha em atenção que os nomes dos contentores têm de estar em minúsculos. Para regras de nomenclatura de contentor, consulte [nomenclatura e referenciação de contentores, Blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata). Se apenas a raiz for especificada, a estrutura do diretório de origem é replicada no contentor do blob de destino. Se for o pretendido uma estrutura de diretório diferente na origem, várias linhas de mapeamento de CSV<br/><br/>Pode especificar um contentor ou um prefixo de BLOBs, como música/anos 70 /. O diretório de destino tem de começar com o nome do contentor, seguido de uma barra "/" e, opcionalmente, pode incluir um diretório virtual blob que termina com "/".<br/><br/>Quando o contentor de destino é o contentor de raiz, deve especificar explicitamente o contêiner raiz, incluindo a barra, como $root /. Uma vez que não podem incluir blobs no contentor de raiz "/" nos respetivos nomes, não serão copiados todos os subdiretórios no diretório de origem quando o diretório de destino é o contêiner raiz.<br/><br/>**Exemplo**<br/>Se o caminho do blob de destino for https://mystorageaccount.blob.core.windows.net/video, o valor deste campo pode ser vídeo /  |
| BlobType | **[Opcional]**  bloquear &#124; página<br/>Atualmente, o serviço importar/exportar suporta 2 tipos de Blobs. Página blobs e BlobsBy bloco padrão em todos os ficheiros serão importados como Blobs de blocos. E \*. vhd e \*serão importados. vhdx como BlobsThere de página é um limite para o blob de blocos e BLOBs de páginas, tamanho permitido. Ver [destinos de escalabilidade do armazenamento](storage-scalability-targets.md) para obter mais informações.  |
| Disposição | **[Opcional]**  mudar o nome &#124; não-substituir &#124; substituir <br/> Este campo especifica o comportamento de cópia durante a importação como Quando dados está a ser carregados para a conta de armazenamento do disco. Opções disponíveis são: mudar o nome&#124;substituir&#124;não-substituir. Por predefinição "se mudar o nome" nada especificado. <br/><br/>**Mudar o nome**: Se um objeto com o mesmo nome estiver presente, cria uma cópia no destino.<br/>Substituir: substitui o ficheiro com arquivo mais recente. O ficheiro com o wins última modificação.<br/>**No-overwrite**: Ignora a escrever o ficheiro se já presente.|
| MetadataFile | **[Opcional]** <br/>O valor para este campo é o ficheiro de metadados que pode ser fornecido se aquele tiver de preservar os metadados dos objetos ou fornecer metadados personalizados. Caminho para o ficheiro de metadados para os blobs de destino. Ver [metadados e o formato de arquivo de propriedades de serviço de importação/exportação](../storage-import-export-file-format-metadata-and-properties.md) para obter mais informações |
| PropertiesFile | **[Opcional]** <br/>Caminho para o ficheiro de propriedade para os blobs de destino. Ver [metadados e o formato de arquivo de propriedades de serviço de importação/exportação](../storage-import-export-file-format-metadata-and-properties.md) para obter mais informações. |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>Preparar ficheiro InitialDriveSet ou AdditionalDriveSet CSV

### <a name="what-is-driveset-csv"></a>O que é driveset CSV

O valor do sinalizador /InitialDriveSet ou /AdditionalDriveSet é um ficheiro CSV que contém a lista de discos para o qual as letras de unidade são mapeadas para que a ferramenta corretamente pode escolher a lista de discos para estar preparado. Se o tamanho dos dados for superior a um tamanho de disco único, a ferramenta de WAImportExport vai distribuir os dados em vários discos inscritas neste ficheiro CSV de forma otimizada.

Não existe nenhum limite no número de discos que é possível escrever os dados numa única sessão. A ferramenta vai distribuir dados com base no tamanho do disco e o tamanho da pasta. Irá selecionar o disco que é mais otimizada para o tamanho do objeto. Os dados quando carregado para a conta de armazenamento serão convergidos de volta para a estrutura de diretórios que foi especificada no ficheiro de conjunto de dados. Para criar um driveset CSV, siga os passos abaixo.

### <a name="create-basic-volume-and-assign-drive-letter"></a>Criar volume básico e atribuir letra de unidade

Para criar um volume básico e atribuir uma letra de unidade, ao seguir as instruções para a "Criação de partição mais simples" fornecido durante [descrição geral da gestão de discos](https://technet.microsoft.com/library/cc754936.aspx).

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>Ficheiro de exemplo InitialDriveSet e AdditionalDriveSet CSV

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>Campos do ficheiro CSV de Driveset

| Campos | Value |
| --- | --- |
| DriveLetter | **[Necessário]**<br/> Cada unidade de disco que está a ser fornecida para a ferramenta, como o destino tem de ter um volume NTFS simple no mesmo e uma letra de unidade atribuída à mesma.<br/> <br/>**Exemplo**: R ou r |
| FormatOption | **[Required]** Format &#124; AlreadyFormatted<br/><br/> **Formato**: Especificar isso irá formatar todos os dados no disco. <br/>**AlreadyFormatted**: A ferramenta irá ignorar a formatação quando este valor for especificado. |
| SilentOrPromptOnFormat | **[Required]** SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: Fornecer este valor irá permitir utilizador execute a ferramenta no modo silencioso. <br/>**PromptOnFormat**: A ferramenta irá pedir ao utilizador para confirmar se a ação realmente destina-se em cada formato.<br/><br/>Se não definir, comando irá abortar e apresentar a mensagem de erro: "Valor incorreto para SilentOrPromptOnFormat: none" |
| Encriptação | **[Necessário]**  Encriptar &#124; AlreadyEncrypted<br/> O valor deste campo decide o disco para encriptar e que não. <br/><br/>**encriptar**: Ferramenta irá formatar o disco. Se o valor do campo de "FormatOption" é "Formato", em seguida, este valor deve ser "Encriptar". Se "AlreadyEncrypted" for especificado, neste caso, irá resultar num erro "Quando o formato é especificado, Encrypt deve ser também especificado".<br/>**AlreadyEncrypted**: Ferramenta irá desencriptar a unidade usando o BitLockerKey fornecida no campo de "ExistingBitLockerKey". Se o valor do campo de "FormatOption" for "AlreadyFormatted", em seguida, este valor pode ser "Encriptar" ou "AlreadyEncrypted" |
| ExistingBitLockerKey | **[Necessário]**  Se o valor do campo de "Encriptação" for "AlreadyEncrypted"<br/> O valor deste campo é a chave do BitLocker que estão associada com o disco específico. <br/><br/>Este campo deve ser deixado em branco se o valor do campo de "Encriptação" é "Encriptar".  Se a chave do BitLocker é especificado neste caso, irá resultar num erro "Chave do BitLocker não deve ser especificado".<br/>  **Exemplo**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>Preparar o disco para a tarefa de importação

Para preparar unidades para uma tarefa de importação, chame a ferramenta de WAImportExport com o **PrepImport** comando. Quais parâmetros incluir depende se esta for a primeira sessão de cópia ou uma sessão de cópia subsequentes.

### <a name="first-session"></a>Primeira sessão

Primeira sessão de cópia para copiar um diretório de Single/Multiple para uma ferramenta de disco (consoante o que é especificado no ficheiro CSV) WAImportExport único/vários PrepImport comando para a primeira sessão de cópia copiar diretórios e/ou ficheiros com uma nova sessão de cópia:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] /DataSet:<dataset.csv>
```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>Adicionar dados de sessão subsequentes

Sessões de cópia subsequentes, não é necessário especificar os parâmetros iniciais. Tem de utilizar o mesmo ficheiro de diário para que a ferramenta de lembrar-se de onde deixou na sessão anterior. O estado da sessão de cópia é escrito no ficheiro de diário. Esta é a sintaxe para uma sessão de subsequentes de cópia para copiar diretórios adicionais e ou de ficheiros:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>Adicionar unidades a sessão mais recentes

Se os dados não foram possível ajustar em unidades especificadas no InitialDriveset, é possível usar a ferramenta para adicionar unidades adicionais a mesma sessão de cópia. 

> [!NOTE]
> O id de sessão deve corresponder ao id de sessão anterior. Ficheiro de diário deve corresponder à especificada na sessão anterior.
> 
> ```
> WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
> ```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>Aborte a sessão mais recentes:

Se uma sessão de cópia é interrompida e não é possível retomar (por exemplo, se um diretório de origem se mostrou inacessível), tem de anular a sessão atual para que podem ser revertida e novas sessões de cópia podem ser iniciados:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

Apenas a última sessão de cópia, se terminou anormalmente, pode ser anulada. Tenha em atenção que não é possível anular a primeira sessão de cópia para uma unidade. Em vez disso, tem de reiniciar a sessão de cópia com um novo ficheiro de diário.

### <a name="resume-a-latest-interrupted-session"></a>Retomar uma sessão interrompida mais recente

Se uma sessão de cópia é interrompida por qualquer motivo, pode continuá-lo ao executar a ferramenta com apenas o ficheiro de diário especificado:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**Exemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> Quando retomar uma sessão de cópia, não modifique os ficheiros de origem de dados e diretórios adicionando ou removendo arquivos.

## <a name="waimportexport-parameters"></a>Parâmetros de WAImportExport

| Parâmetros | Descrição |
| --- | --- |
|     /j:&lt;JournalFile&gt;  | **Necessário**<br/> Caminho para o ficheiro de diário. Um ficheiro de diário acompanha um conjunto de unidades e regista o progresso na preparação destas unidades. Sempre é necessário especificar o ficheiro de diário.  |
|     /logdir:&lt;LogDirectory&gt;  | **Opcional**. O diretório de registo.<br/> Ficheiros de registo verboso, bem como alguns ficheiros temporários serão escritos para este diretório. Se não for especificado, o atual diretório será utilizado como o diretório de registo. O diretório de registo pode ser especificado apenas uma vez para o mesmo ficheiro de diário.  |
|     /id:&lt;SessionId&gt;  | **Necessário**<br/> O Id de sessão que é utilizado para identificar uma sessão de cópia. É utilizado para garantir a recuperação precisa de uma sessão de cópia interrompida.  |
|     /ResumeSession  | Opcional. Se a última sessão de cópia foi anormalmente terminada, este parâmetro pode ser especificado para retomar a sessão.   |
|     /AbortSession  | Opcional. Se a última sessão de cópia foi anormalmente terminada, este parâmetro pode ser especificado para abortar a sessão.  |
|     /sn:&lt;StorageAccountName&gt;  | **Necessário**<br/> Só é aplicável para RepairImport e RepairExport. O nome da conta de armazenamento.  |
|     /sk:&lt;StorageAccountKey&gt;  | **Necessário**<br/> A chave da conta de armazenamento. |
|     /InitialDriveSet:&lt;driveset.csv&gt;  | **Necessário** ao executar a primeira sessão de cópia<br/> Um ficheiro CSV que contém uma lista de unidades para se preparar.  |
|     /AdditionalDriveSet:&lt;driveset.csv&gt; | **Necessário**. Ao adicionar unidades a sessão atual de cópia. <br/> Um ficheiro CSV que contém uma lista de unidades adicionais para ser adicionado.  |
|      /r:&lt;RepairFile&gt; | **Necessário** apenas aplicável para RepairImport e RepairExport.<br/> Caminho para o ficheiro para controlar o progresso de reparação. Cada unidade tem de ter apenas um ficheiro de reparação.  |
|     /d:&lt;TargetDirectories&gt; | **Necessário**. Só é aplicável para RepairImport e RepairExport. Para RepairImport, um ou mais diretórios separados por ponto e vírgula para reparar; Para RepairExport, um diretório para reparar, por exemplo, a raiz o diretório da unidade.  |
|     /CopyLogFile:&lt;DriveCopyLogFile&gt; | **Necessário** apenas aplicável para RepairImport e RepairExport. Caminho para o ficheiro de registo de cópia de unidade (verboso ou erro).  |
|     /ManifestFile:&lt;DriveManifestFile&gt; | **Necessário** apenas aplicável para RepairExport.<br/> Caminho para o ficheiro de manifesto de unidade.  |
|     /PathMapFile:&lt;DrivePathMapFile&gt; | **Opcional**. Só é aplicável para RepairImport.<br/> Caminho do ficheiro que contém mapeamentos de caminhos de ficheiro relativo à raiz da unidade para localizações dos arquivos reais (delimitado por tabulação). Quando especificado em primeiro lugar, ele será populado com caminhos de ficheiro com destinos vazios, que significa que eles não se encontram no TargetDirectories, acesso negado, com um nome inválido, ou se existem na vários diretórios. O ficheiro de mapa do caminho pode ser editado para incluir os caminhos de destino correto manualmente e especificado novamente para a ferramenta resolver corretamente os caminhos de ficheiro.  |
|     /ExportBlobListFile:&lt;ExportBlobListFile&gt; | **Necessário**. Só é aplicável para PreviewExport.<br/> Caminho para o XML que contêm lista de caminhos dos BLOBs de ficheiros ou prefixos de caminho para os blobs ser exportada de Blobs. O formato de arquivo é o mesmo que o formato de blob de lista de BLOBs na operação de colocar a tarefa de REST API do serviço de importação/exportação.  |
|     /DriveSize:&lt;DriveSize&gt; | **Necessário**. Só é aplicável para PreviewExport.<br/>  Tamanho das unidades a ser utilizado para exportação. Por exemplo, 500 GB, 1,5 TB. Nota: 1 GB = 1,000,000,000 bytes1 TB = 1,000,000,000,000 bytes  |
|     /DataSet:&lt;dataset.csv&gt; | **Necessário**<br/> Um ficheiro CSV que contém uma lista de diretórios e/ou uma lista de ficheiros ser copiados para unidades de destino.  |
|     /silentmode  | **Opcional**.<br/> Se não for especificado, ele lembrá-lo sobre o requisito de unidades e precisa a sua confirmação para continuar.  |

## <a name="tool-output"></a>Resultado da ferramenta

### <a name="sample-drive-manifest-file"></a>Ficheiro de manifesto de unidade de exemplo

```xml
<?xml version="1.0" encoding="UTF-8"?>
<DriveManifest Version="2011-MM-DD">
   <Drive>
      <DriveId>drive-id</DriveId>
      <StorageAccountKey>storage-account-key</StorageAccountKey>
      <ClientCreator>client-creator</ClientCreator>
      <!-- First Blob List -->
      <BlobList Id="session#1-0">
         <!-- Global properties and metadata that applies to all blobs -->
         <MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>
         <PropertiesPath Hash="md5-hash">global-properties-file-path</PropertiesPath>
         <!-- First Blob -->
         <Blob>
            <BlobPath>blob-path-relative-to-account</BlobPath>
            <FilePath>file-path-relative-to-transfer-disk</FilePath>
            <ClientData>client-data</ClientData>
            <Length>content-length</Length>
            <ImportDisposition>import-disposition</ImportDisposition>
            <!-- page-range-list-or-block-list -->
            <!-- page-range-list -->
            <PageRangeList>
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
            </PageRangeList>
            <!-- block-list -->
            <BlockList>
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
            </BlockList>
            <MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>
            <PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>
         </Blob>
      </BlobList>
   </Drive>
</DriveManifest>
```

### <a name="sample-journal-file-xml-for-each-drive"></a>Ficheiro de diário de exemplo (XML) para cada unidade

```xml
[BeginUpdateRecord][2016/11/01 21:22:25.379][Type:ActivityRecord]
ActivityId: DriveInfo
DriveState: [BeginValue]
<?xml version="1.0" encoding="UTF-8"?>
<Drive>
   <DriveId>drive-id</DriveId>
   <BitLockerKey>*******</BitLockerKey>
   <ManifestFile>\DriveManifest.xml</ManifestFile>
   <ManifestHash>D863FE44F861AE0DA4DCEAEEFFCCCE68</ManifestHash> </Drive>
[EndValue]
SaveCommandOutput: Completed
[EndUpdateRecord]
```

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>Ficheiro de diário de exemplo (JRN) para a sessão que regista o registo de sessões

```
[BeginUpdateRecord][2016/11/02 18:24:14.735][Type:NewJournalFile]
VocabularyVersion: 2013-02-01
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.749][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
LogDirectory: F:\logs
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.754][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
StorageAccountKey: *******
[EndUpdateRecord]
```

## <a name="faq"></a>FAQ

### <a name="general"></a>Geral

#### <a name="what-is-waimportexport-tool"></a>O que é a ferramenta de WAImportExport?

A ferramenta de WAImportExport é a ferramenta de preparação e reparação de unidade que pode utilizar com o serviço de importação/exportação do Microsoft Azure. Pode utilizar esta ferramenta para copiar dados para as unidades de disco rígido que vai enviar para um centro de dados do Azure. Depois de uma tarefa de importação foi concluída, pode utilizar esta ferramenta para reparar os blobs que foram corrompidos, estavam em falta ou que entraram em conflito com outros blobs. Depois de receber as unidades de uma tarefa de exportação concluída, pode utilizar esta ferramenta para reparar todos os ficheiros que foram danificado ou em falta nas unidades.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>Como funciona a ferramenta de WAImportExport em várias dir de origem e discos?

Se o tamanho dos dados é maior do que o tamanho do disco, a ferramenta de WAImportExport distribuir os dados em todos os discos de forma otimizada. A cópia de dados para vários discos pode ser feita em paralelo ou sequencialmente. Não existe nenhum limite no número de discos, que os dados podem ser gravados em simultâneo. A ferramenta vai distribuir dados com base no tamanho do disco e o tamanho da pasta. Irá selecionar o disco que é mais otimizada para o tamanho do objeto. Os dados quando carregado para a conta de armazenamento serão convergidos de volta para a estrutura do diretório especificado.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>Onde posso encontrar uma versão anterior da ferramenta de WAImportExport?

Ferramenta de WAImportExport tem todas as funcionalidades que tinha de ferramenta WAImportExport V1. Ferramenta de WAImportExport permite aos utilizadores especificar várias origens e gravar em várias unidades. Além disso, um pode gerir facilmente várias localizações de origem a partir do qual os dados têm de ser copiados num único ficheiro CSV. No entanto, caso seja necessário suporte SAS ou se quiser copiar origem única para o disco único, pode [transferir a ferramenta de V1 WAImportExport](https://go.microsoft.com/fwlink/?LinkID=301900&amp;clcid=0x409) e consulte [WAImportExport V1 referência](storage-import-export-tool-how-to-v1.md) para obter ajuda com a utilização de WAImportExport V1 .

#### <a name="what-is-a-session-id"></a>O que é um ID de sessão?

A ferramenta espera que a sessão de cópia (/ id) parâmetro seja o mesmo se a intenção é distribuir os dados entre vários discos. Manter o mesmo nome da sessão de cópia de permitirá ao utilizador copiar dados de uma ou várias localizações de origem para um ou vários discos/diretórios de destino. Manter o mesmo id de sessão permite que a ferramenta de retirada a cópia de ficheiros a partir de onde ele foi deixado da última vez.

No entanto, a mesma sessão de cópia não pode ser utilizado para importar dados para contas de armazenamento diferentes.

Quando o nome de sessão de cópia está mesmo nas várias execuções da ferramenta, o ficheiro de registo (/ logdir) e a chave de conta de armazenamento (/ sk) também deve ser o mesmo.

SessionId pode consistir de letras, 0 ~ 9, um caráter de sublinhado (\_), travessão (-) ou hash (#), e seu comprimento tem de ter 3 ~ 30.

Por exemplo, a sessão-1 ou sessão n. º 1 ou sessão\_1

#### <a name="what-is-a-journal-file"></a>O que é um ficheiro de diário?

Sempre que executar a ferramenta de WAImportExport para copiar ficheiros para o disco rígido, a ferramenta cria uma sessão de cópia. O estado da sessão de cópia é escrito no ficheiro de diário. Se uma sessão de cópia for interrompida (por exemplo, devido a uma queda de energia do sistema), pode ser retomada a executar a ferramenta novamente e especificando o ficheiro de diário na linha de comandos.

Para cada unidade de disco rígido que se preparar com a ferramenta de importação/exportação do Azure, a ferramenta irá criar um ficheiro de diário único com o nome "&lt;DriveID&gt;. xml" em que o Id de unidade é o número de série associado para a unidade que a ferramenta lê a partir do disco. Terá dos ficheiros de diário de todos os das suas unidades para criar a tarefa de importação. O ficheiro de diário também pode ser utilizado para retomar a preparação da unidade, se a ferramenta é interrompida.

#### <a name="what-is-a-log-directory"></a>O que é um diretório de registo?

O diretório de registo Especifica um diretório a ser utilizado para armazenar registos verbosos, bem como arquivos de manifesto temporários. Se não for especificado, será utilizado o diretório atual como o diretório de registo. Os registos são registos verbosos.

### <a name="prerequisites"></a>Pré-requisitos

#### <a name="what-are-the-specifications-of-my-disk"></a>Quais são as especificações do meu disco?

Um ou mais vazias 2.5 polegadas ou 3.5 polegadas SATAII ou III ou SSD unidades de disco rígido ligadas à máquina de cópia.

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>Como posso ativar o BitLocker no meu computador?

Uma forma Simple de verificar é ao clicar na unidade do sistema. Ele mostrará a opções para o BitLocker se a capacidade está ativada. Se estiver desativado, não verá-lo.

![Verifique o BitLocker](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

Eis um artigo sobre [como ativar o BitLocker](https://technet.microsoft.com/library/cc766295.aspx)

É possível que o seu computador não tem o chip do TPM. Se não obtém uma saída usando msc, veja as FAQ seguintes.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>Como desativar o Trusted Platform Module (TPM) no BitLocker?
> [!NOTE]
> Apenas se não houver nenhum TPM em seus servidores, terá de desativar a política TPM. Não é necessário desativar TPM, se houver um TPM fidedigno no servidor do usuário. 
> 

Para desativar o TPM no BitLocker, execute os seguintes passos:<br/>
1. Inicie **Editor de diretiva de grupo** digitando gpedit. msc num prompt de comando. Se **Editor de diretiva de grupo** parece estar indisponível, para ativar o BitLocker pela primeira vez. Veja as FAQ anteriores.
2. Open **política de computador Local &gt; configuração do computador &gt; modelos administrativos &gt; componentes do Windows&gt; encriptação de unidade de disco BitLocker &gt; deunidadesdesistemaoperativo**.
3. Editar **necessitam de autenticação adicional no arranque** política.
4. Defina a política para **Enabled** e certifique-se **permitir BitLocker sem um TPM compatível** está marcada.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>Como verificar se o .NET 4 ou uma versão posterior está instalada no meu computador?

Todas as versões do Microsoft .NET Framework estão instaladas no seguinte diretório: %windir%\Microsoft.NET\Framework\

Navegue para a parte acima mencionada no seu computador de destino onde a ferramenta precisa ser executado. Procure o nome da pasta a partir do "v4". Ausência de um diretório tal significa que o .NET 4 não está instalado no seu computador. Pode baixar o .NET 4 no seu computador com [Microsoft .NET Framework 4 (instalador Web)](https://www.microsoft.com/download/details.aspx?id=17851).

### <a name="limits"></a>Limites

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>O número de unidades pode me preparar/send ao mesmo tempo?

Não existe nenhum limite no número de discos que a ferramenta pode se preparar. No entanto, a ferramenta espera letras de unidade como entradas. Que limita a 25 preparação de disco em simultâneo. Uma única tarefa pode processar o máximo de 10 discos por vez. Se a preparar-se mais de 10 discos visando a mesma conta de armazenamento, os discos podem ser distribuídos em várias tarefas.

#### <a name="can-i-target-more-than-one-storage-account"></a>Pode direcionar mais de uma conta de armazenamento?

Apenas uma conta de armazenamento pode ser submetida por tarefa e na sessão de cópia única.

### <a name="capabilities"></a>Capacidades

#### <a name="does-waimportexportexe-encrypt-my-data"></a>WAImportExport.exe encripta os meus dados?

Sim. Criptografia de disco BitLocker é ativada e necessárias para que este processo.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>Qual será a hierarquia dos meus dados quando aparece na conta de armazenamento?

Embora os dados são distribuídos pelos discos, os dados quando carregado para a conta de armazenamento irão ser convergidos novamente para a estrutura do diretório especificada no ficheiro CSV de conjunto de dados.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>O número da entrada discos terão e/s Active Directory em paralelo, quando a cópia está em curso?

A ferramenta distribui os dados em todos os discos de entrada com base no tamanho dos ficheiros de entrada. Dito isso, o número de discos ativos em paralelo depende completamente a natureza dos dados de entrada. Dependendo do tamanho dos ficheiros individuais no conjunto de dados de entrada, um ou mais discos poderão mostrar e/s Active Directory em paralelo. Consulte a próxima pergunta para obter mais detalhes.

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>Como a ferramenta de distribuir os arquivos em todos os discos?

Ferramenta de WAImportExport lê e escreve ficheiros batch pelo batch, um lote contém o número máximo de 100000 ficheiros. Isso significa que máximo 100000 pode escrever nos ficheiros paralelo. Vários discos são escritos simultaneamente para se estes 100000 ficheiros são distribuídos por várias unidades. No entanto, se a ferramenta grava em vários discos em simultâneo ou um único disco depende do tamanho cumulativo do batch. Por exemplo, em caso de ficheiros mais pequenos, se todos os ficheiros 10,0000 conseguem caber numa única unidade, ferramenta será escrita para apenas um disco durante o processamento de neste lote.

### <a name="waimportexport-output"></a>Saída de WAImportExport

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>Existem dois ficheiros de diário, qual deles se deverá carregar para o portal do Azure?

**. XML** -para cada unidade de disco rígido que se preparar com a ferramenta de WAImportExport, a ferramenta irá criar um ficheiro de diário único com o nome `<DriveID>.xml` onde DriveID é o número de série associado para a unidade que a ferramenta lê a partir do disco. Terá dos ficheiros de diário de todos os das suas unidades para criar a tarefa de importação no portal do Azure. Este ficheiro de diário também pode ser utilizado para retomar a preparação da unidade, se a ferramenta é interrompida.

**jrn** -o ficheiro de diário com o sufixo `.jrn` contém o estado de todas as sessões de cópia para um disco rígido. Também contém as informações necessárias para criar a tarefa de importação. Sempre tem de especificar um ficheiro de diário ao executar a ferramenta de WAImportExport, bem como um ID de sessão de cópia.

## <a name="next-steps"></a>Passos Seguintes

* [Configurar a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup.md)
* [Definir propriedades e metadados durante o processo de importação](storage-import-export-tool-setting-properties-metadata-import.md)
* [Fluxo de trabalho de amostra para preparar unidades de disco rígido para uma tarefa de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [Referência rápida para comandos utilizados com frequência](storage-import-export-tool-quick-reference.md) 
* [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparação de uma tarefa de importação](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparação de uma tarefa de exportação](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
