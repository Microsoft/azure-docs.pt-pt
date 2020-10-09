---
title: Instalação silenciosa do Azure Backup Server V2
description: Utilize um script PowerShell para instalar silenciosamente o Servidor de Backup V2 do Azure. Este tipo de instalação também é chamado de instalação não acompanhada.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74172235"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Executar uma instalação não acompanhada do Azure Backup Server

Aprenda a executar uma instalação não acompanhada do Azure Backup Server.

Estes passos não se aplicam se estiver a instalar o Azure Backup Server V1.

## <a name="install-backup-server"></a>Instalar servidor de backup

1. No servidor que acolhe o Azure Backup Server V2 ou mais tarde, crie um ficheiro de texto. (Pode criar o ficheiro no Bloco de Notas ou noutro editor de texto.) Guarde o ficheiro como MABSSetup.ini.

2. Cole o seguinte código no ficheiro MABSSetup.ini. Substitua o texto no interior dos suportes \< \> () com valores do seu ambiente. O seguinte texto é um exemplo:

   ```text
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

3. Guarde o ficheiro. Em seguida, num pedido de comando elevado no servidor de instalação, insira este comando:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Pode utilizar estas bandeiras para a instalação:</br>
**/f**: .ini caminho de arquivo</br>
**/l**: Caminho de registo</br>
**/i**: Caminho de instalação</br>
**/x**: Desinstalar caminho</br>

## <a name="next-steps"></a>Passos seguintes

Depois de instalar o Backup Server, aprenda a preparar o seu servidor ou comece a proteger uma carga de trabalho.

- [Preparar cargas de trabalho do servidor de backup](backup-azure-microsoft-azure-backup.md)
- [Utilize o Servidor de Backup para fazer backup de um servidor VMware](backup-azure-backup-server-vmware.md)
- [Use o Servidor de Backup para fazer backup no SQL Server](backup-azure-sql-mabs.md)
- [Adicione armazenamento de backup moderno ao servidor de backup](backup-mabs-add-storage.md)
