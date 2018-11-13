---
title: Instalação silenciosa do V2 de servidor de cópia de segurança do Azure
description: Utilize um script do PowerShell para instalar automaticamente a V2 de servidor de cópia de segurança do Azure. Este tipo de instalação também é chamado uma instalação autónoma.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: markgal
ms.openlocfilehash: 3e106d7f669cf14014114ed0fe63651a1a2fe0eb
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578122"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Executar uma instalação autónoma do Azure Backup Server

Saiba como executar uma instalação autónoma do servidor de cópia de segurança do Azure.

Estes passos não se aplicam se estiver a instalar o Azure Backup Server V1.

## <a name="install-backup-server"></a>Instalar o servidor de cópia de segurança

1. No servidor que aloja a V2 de servidor de cópia de segurança do Azure ou posterior, crie um ficheiro de texto. (Pode criar o ficheiro no bloco de notas ou em outro editor de texto.) Guarde o ficheiro como MABSSetup.ini.

2. Cole o seguinte código no ficheiro MABSSetup.ini. Substitua o texto dentro dos parênteses Retos (\< \>) com valores do seu ambiente. O texto seguinte é um exemplo:

  ```
  [OPTIONS]
  UserName=administrator
  CompanyName=<Microsoft Corporation>
  SQLMachineName=localhost
  SQLInstanceName=<SQL instance name>
  SQLMachineUserName=administrator
  SQLMachinePassword=<admin password>
  SQLMachineDomainName=<machine domain>
  ReportingMachineName=localhost
  ReportingInstanceName=<reporting instance name>
  SqlAccountPassword=<admin password>
  ReportingMachineUserName=<username>
  ReportingMachinePassword=<reporting admin password>
  ReportingMachineDomainName=<domain>
  VaultCredentialFilePath=<vault credential full path and complete name>
  SecurityPassphrase=<passphrase>
  PassphraseSaveLocation=<passphrase save location>
  UseExistingSQL=<1/0 use or do not use existing SQL>
  ```

3. Guarde o ficheiro. Em seguida, na linha de comandos elevada no servidor de instalação, introduza este comando:

  ```
  start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
  ```

Pode utilizar estes sinalizadores para a instalação:</br>
**/f**: caminho do ficheiro. ini</br>
**/l**: caminho de registo</br>
**/i**: caminho de instalação</br>
**/x**: desinstalar caminho</br>

## <a name="next-steps"></a>Passos Seguintes
Depois de instalar o servidor de cópia de segurança, saiba como preparar o servidor ou começar a proteger uma carga de trabalho.

- [Preparar as cargas de trabalho do servidor de cópia de segurança](backup-azure-microsoft-azure-backup.md)
- [Utilizar o servidor de cópia de segurança para fazer backup de um servidor VMware](backup-azure-backup-server-vmware.md)
- [Utilizar o servidor de cópia de segurança para criar cópias de segurança do SQL Server](backup-azure-sql-mabs.md)
- [Adicionar armazenamento de cópia de segurança moderno ao servidor de cópia de segurança](backup-mabs-add-storage.md)
