---
title: Configurando a ferramenta de importação/exportação do Azure | Microsoft Docs
description: Saiba como configurar a ferramenta de preparação e reparo da unidade para o serviço de importação/exportação do Azure.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 01432ab68fc399f3e97eac2de5a7c356bef7078a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979041"
---
# <a name="setting-up-the-azure-importexport-tool"></a>Configurando a ferramenta de importação/exportação do Azure

A ferramenta de Importação/Exportação do Microsoft Azure é a ferramenta de preparação e reparo da unidade que pode ser usada com o serviço Importação/Exportação do Microsoft Azure. Você pode usar a ferramenta para as seguintes funções:

* Antes de criar um trabalho de importação, você pode usar essa ferramenta para copiar dados para os discos rígidos que serão enviados para um data center do Azure.
* Após a conclusão de um trabalho de importação, você pode usar essa ferramenta para reparar todos os blobs que estavam corrompidos, estavam ausentes ou em conflito com outros BLOBs.
* Depois de receber as unidades de um trabalho de exportação concluído, você pode usar essa ferramenta para reparar todos os arquivos que estavam corrompidos ou ausentes nas unidades.

## <a name="prerequisites"></a>Pré-requisitos

Se você estiver **preparando unidades** para um trabalho de importação, os seguintes pré-requisitos devem ser atendidos:

* Você deve ter uma assinatura ativa do Azure.
* Sua assinatura deve incluir uma conta de armazenamento com espaço disponível suficiente para armazenar os arquivos que você pretende importar.
* Você precisa de pelo menos uma das chaves de acesso da conta de armazenamento.
* Você precisa de um computador (a "máquina de cópia") com o Windows 7, o Windows Server 2008 R2 ou um sistema operacional Windows mais recente instalado.
* O .NET Framework 4 deve ser instalado no computador de cópia.
* O BitLocker deve estar habilitado no computador de cópia.
* Você precisa de um ou mais discos rígidos SATAII, III ou SSD de 2,5 polegadas ou 3,5 de polegada vazia conectados à máquina de cópia.
* Os arquivos que você planeja importar devem estar acessíveis no computador de cópia, estejam eles em um compartilhamento de rede ou disco rígido local.

Se você estiver tentando **reparar uma importação** com falha parcial, será necessário:

* Os arquivos de log de cópia
* A chave da conta de armazenamento

Se estiver tentando **reparar uma exportação** que falhou parcialmente, você precisará de:

* Os arquivos de log de cópia
* Os arquivos de manifesto (opcional)
* A chave da conta de armazenamento

## <a name="installing-the-azure-importexport-tool"></a>Instalando a ferramenta de importação/exportação do Azure

Primeiro, [Baixe a ferramenta de importação/exportação do Azure](https://www.microsoft.com/download/details.aspx?id=55280) e extraia-a para um diretório em seu computador, por exemplo `c:\WAImportExport`.

A ferramenta de importação/exportação do Azure consiste nos seguintes arquivos:

* dataset.csv
* driveset.csv
* hddid.dll
* Microsoft.Data.Services.Client.dll
* Microsoft.WindowsAzure.Storage.dll
* Microsoft.WindowsAzure.Storage.pdb
* Microsoft.WindowsAzure.Storage.xml
* WAImportExport. exe
* WAImportExport.exe.config
* WAImportExport. pdb
* WAImportExportCore. dll
* WAImportExportCore.pdb
* WAImportExportRepair.dll
* WAImportExportRepair. pdb

Em seguida, abra uma janela de prompt de comando no **modo de administrador**e altere para o diretório que contém os arquivos extraídos.

Para gerar a ajuda para o comando, execute a ferramenta (`WAImportExport.exe`) sem parâmetros:

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

## <a name="next-steps"></a>Passos seguintes

* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Pré-visualização da utilização da unidade para uma tarefa de exportação](../storage-import-export-tool-previewing-drive-usage-export-v1.md)
* [Revisão do estado da tarefa com ficheiros de registo de cópia](../storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparação de uma tarefa de importação](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)
* [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
