---
title: Instalação silenciosa do Servidor de Backup Azure V2
description: Utilize um script PowerShell para instalar silenciosamente o Servidor de Backup Azure V2. Este tipo de instalação também é chamada de instalação sem supervisão.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74172235"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Executar uma instalação sem supervisão do Servidor de Backup Azure

Aprenda a executar uma instalação sem supervisão do Servidor de Backup Azure.

Estes passos não se aplicam se estiver a instalar o Servidor de Backup Azure V1.

## <a name="install-backup-server"></a>Instalar servidor de backup

1. No servidor que acolhe o Azure Backup Server V2 ou posteriormente, crie um ficheiro de texto. (Pode criar o ficheiro no Bloco de Notas ou noutro editor de texto.) Guarde o ficheiro como MABSSetup.ini.

2. Colar o seguinte código no ficheiro MABSSetup.ini. Substitua o texto dentro\< \>dos suportes () por valores do seu ambiente. O seguinte texto é um exemplo:

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

3. Guarde o ficheiro. Em seguida, num pedido de comando elevado no servidor de instalação, introduza este comando:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Pode utilizar estas bandeiras para a instalação:</br>
**/f**: .ini caminho de arquivo</br>
**/l**: Log path</br>
**/i**: Caminho de instalação</br>
**/x:** Desinstalar caminho</br>

## <a name="next-steps"></a>Passos seguintes

Depois de instalar o Backup Server, aprenda a preparar o seu servidor ou comece a proteger uma carga de trabalho.

- [Preparar cargas de trabalho do Servidor de Backup](backup-azure-microsoft-azure-backup.md)
- [Utilize o Servidor de Backup para fazer backup de um servidor VMware](backup-azure-backup-server-vmware.md)
- [Utilize o Servidor de Backup para fazer backup do Servidor SQL](backup-azure-sql-mabs.md)
- [Adicione armazenamento de backup moderno ao servidor de backup](backup-mabs-add-storage.md)
