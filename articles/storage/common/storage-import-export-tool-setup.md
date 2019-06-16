---
title: Configurar a ferramenta de importação/exportação do Azure | Documentos da Microsoft
description: Saiba como configurar a preparação da unidade e reparar a ferramenta para o serviço importar/exportar do Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: d2ce6c409ae9cbf99589d11dfc850e2324d1b0c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60320470"
---
# <a name="setting-up-the-azure-importexport-tool"></a>Configurar a ferramenta de importação/exportação do Azure

A ferramenta de importação/exportação do Microsoft Azure é a ferramenta de preparação e reparação de unidade que pode utilizar com o serviço de importação/exportação do Microsoft Azure. Pode usar a ferramenta para as seguintes funções:

* Antes de criar uma tarefa de importação, pode utilizar esta ferramenta para copiar dados para as unidades de disco rígido que vai enviar para um centro de dados do Azure.
* Depois de uma tarefa de importação foi concluída, pode utilizar esta ferramenta para reparar os blobs que foram corrompidos, estavam em falta ou que entraram em conflito com outros blobs.
* Depois de receber as unidades de uma tarefa de exportação concluída, pode utilizar esta ferramenta para reparar todos os ficheiros que foram danificado ou em falta nas unidades.

## <a name="prerequisites"></a>Pré-requisitos

Se estiver **preparar as unidades de** para uma tarefa de importação, têm de ser cumpridos os seguintes pré-requisitos:

* Tem de ter uma subscrição do Azure Active Directory.
* A subscrição tem de incluir uma conta de armazenamento com espaço suficiente disponível para armazenar os ficheiros que pretende importar.
* Terá de, pelo menos, uma das chaves de acesso da conta de armazenamento.
* Precisa de um computador (a "máquina de cópia") com o Windows 7, Windows Server 2008 R2 ou um sistema de operativo mais recente do Windows instalada.
* O .NET Framework 4 tem de estar instalado na máquina de cópia.
* O BitLocker tem de estar ativado na máquina de cópia.
* Precisa de um ou mais vazio 2.5 polegadas ou 3.5 polegadas SATAII ou III ou SSD unidades de disco rígido ligadas à máquina de cópia.
* Os ficheiros que pretende importar tem de ser acessíveis a partir da máquina de cópia, estejam eles num compartilhamento de rede ou um disco rígido local.

Se está a tentar **reparar uma importação** que falhou parcialmente, terá de:

* Os ficheiros de registo de cópia
* A chave de conta de armazenamento

Se está a tentar **reparar uma exportação** que falhou parcialmente, terá de:

* Os ficheiros de registo de cópia
* Os arquivos de manifesto (opcionais)
* A chave de conta de armazenamento

## <a name="installing-the-azure-importexport-tool"></a>Instalar a ferramenta de importação/exportação do Azure

Primeiro, [transferir a ferramenta de importação/exportação do Azure](https://www.microsoft.com/download/details.aspx?id=55280) e extraia-o para um diretório no seu computador, por exemplo `c:\WAImportExport`.

A ferramenta de importação/exportação do Azure inclui os seguintes ficheiros:

* dataset.csv
* driveset.csv
* hddid.dll
* Microsoft.Data.Services.Client.dll
* Microsoft.WindowsAzure.Storage.dll
* Microsoft.WindowsAzure.Storage.pdb
* Microsoft.WindowsAzure.Storage.xml
* WAImportExport.exe
* WAImportExport.exe.config
* WAImportExport.pdb
* WAImportExportCore.dll
* WAImportExportCore.pdb
* WAImportExportRepair.dll
* WAImportExportRepair.pdb

Em seguida, abra uma janela de linha de comandos no **modo de administrador**e altere para o diretório que contém os ficheiros extraídos.

A ajuda de saída para o comando, execute a ferramenta (`WAImportExport.exe`) sem parâmetros:

```
WAImportExport, a client tool for Windows Azure Import/Export Service. Microsoft (c) 2013


Copy directories and/or files with a new copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>]
        [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]
        DataSet:<dataset.csv>

Add more drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>

Abort an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession

Resume an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession

List drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListDrives

List copy sessions:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListCopySessions

Repair a Drive:
    WAImportExport.exe RepairImport | RepairExport
        /r:<RepairFile> [/logdir:<LogDirectory>]
        [/d:<TargetDirectories>] [/bk:<BitLockerKey>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        [/CopyLogFile:<DriveCopyLogFile>] [/ManifestFile:<DriveManifestFile>]
        [/PathMapFile:<DrivePathMapFile>]

Preview an Export Job:
    WAImportExport.exe PreviewExport
        [/logdir:<LogDirectory>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        /ExportBlobListFile:<ExportBlobListFile> /DriveSize:<DriveSize>

Parameters:

    /j:<JournalFile>
        - Required. Path to the journal file. A journal file tracks a set of drives and
          records the progress in preparing these drives. The journal file must always
          be specified.
    /logdir:<LogDirectory>
        - Optional. The log directory. Verbose log files as well as some temporary
          files will be written to this directory. If not specified, current directory
          will be used as the log directory. The log directory can be specified only
          once for the same journal file.
    /id:<SessionId>
        - Optional. The session Id is used to identify a copy session. It is used to
          ensure accurate recovery of an interrupted copy session.
    /ResumeSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to resume the session.
    /AbortSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to abort the session.
    /sn:<StorageAccountName>
        - Required. Only applicable for RepairImport and RepairExport. The name of
          the storage account.
    /sk:<StorageAccountKey>
        - Required. The key of the storage account.
    /InitialDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of drives to prepare.
    /AdditionalDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of additional drives to be added.
    /r:<RepairFile>
        - Required. Only applicable for RepairImport and RepairExport.
          Path to the file for tracking repair progress. Each drive must have one
          and only one repair file.
    /d:<TargetDirectories>
        - Required. Only applicable for RepairImport and RepairExport.
          For RepairImport, one or more semicolon-separated directories to repair;
          For RepairExport, one directory to repair, e.g. root directory of the drive.
    /CopyLogFile:<DriveCopyLogFile>
        - Required. Only applicable for RepairImport and RepairExport. Path to the
          drive copy log file (verbose or error).
    /ManifestFile:<DriveManifestFile>
        - Required. Only applicable for RepairExport. Path to the drive manifest file.
    /PathMapFile:<DrivePathMapFile>
        - Optional. Only applicable for RepairImport. Path to the file containing
          mappings of file paths relative to the drive root to locations of actual files
          (tab-delimited). When first specified, it will be populated with file paths
          with empty targets, which means either they are not found in TargetDirectories,
          access denied, with invalid name, or they exist in multiple directories. The
          path map file can be manually edited to include the correct target paths and
          specified again for the tool to resolve the file paths correctly.
    /ExportBlobListFile:<ExportBlobListFile>
        - Required. Path to the XML file containing list of blob paths or blob path
          prefixes for the blobs to be exported. The file format is the same as the
          blob list blob format in the Put Job operation of the Import/Export Service
          REST API.
    /DriveSize:<DriveSize>
        - Required. Size of drives to be used for export. For example, 500GB, 1.5TB.
          Note: 1 GB = 1,000,000,000 bytes
                1 TB = 1,000,000,000,000 bytes
    /DataSet:<dataset.csv>
        - Required. A .csv file that contains a list of directories and/or a list files
          to be copied to target drives.

    /silentmode
        - Optional. If not specified, it will remind you the requirement of drives and
          need your confirmation to continue.

Examples:

    Copy a data set to a drive:
    WAImportExport.exe PrepImport
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /InitialDriveSet:driveset1.csv
        /DataSet:data.csv

    Copy another dataset to the same drive following the above command:
    WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#2 /DataSet:dataset2.csv

    Preview how many 1.5 TB drives are needed for an export job:
    WAImportExport.exe PreviewExport
        /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7K
        ysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\temp\myexportbloblist.xml
        /DriveSize:1.5TB

    Repair an finished import job:
    WAImportExport.exe RepairImport
        /r:9WM35C2V.rep /d:X:\ /bk:442926-020713-108086-436744-137335-435358-242242-2795
        98 /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94
        f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\temp\9WM35C2V_error.log
```

## <a name="next-steps"></a>Passos Seguintes

* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Pré-visualização da utilização da unidade para uma tarefa de exportação](../storage-import-export-tool-previewing-drive-usage-export-v1.md)
* [Revisão do estado da tarefa com ficheiros de registo de cópia](../storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparação de uma tarefa de importação](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)
* [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
