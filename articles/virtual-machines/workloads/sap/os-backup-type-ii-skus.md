---
title: II SKUs de tipo de cópia de segurança do sistema operativo e o restauro de SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Efetuar cópia de segurança do sistema operativo e o restauro para o SAP HANA no Azure (instâncias grandes) tipo II SKUs
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c82c5c74fe13bad99528486be69089df5f477457
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436345"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Cópia de segurança do sistema operacional e de restauro para SKUs do tipo II

Este documento descreve os passos para executar um backup de nível de ficheiro de sistema operativo e o restauro para o **SKUs do tipo II** das instâncias grandes do HANA. 

>[!NOTE]
>Os scripts de cópia de segurança do sistema operacional utiliza o software de trás, que é previamente instalado no servidor.  

Após o aprovisionamento estiver concluído, a equipe de gerenciamento de serviços da Microsoft, por predefinição, o servidor está configurado com a agenda de duas cópias de segurança para criar cópias de segurança do sistema de ficheiros ao nível de cópia de segurança do sistema operativo. Pode verificar o agendamento da tarefa de cópia de segurança utilizando o seguinte comando:
```
#crontab –l
```
Pode alterar a agenda de cópia de segurança qualquer altura utilizando o seguinte comando:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Como efetuar uma cópia de segurança manual?

A cópia de segurança do sistema de ficheiros do sistema operativo é programada com uma **tarefa cron** já. No entanto, pode executar o sistema operativo ao nível backup de arquivos manualmente também. Para efetuar uma cópia de segurança manual, execute o seguinte comando:

```
#rear -v mkbackup
```
O seguir mostram de ecrã mostra a cópia de segurança manual do exemplo:

![Como](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Como restaurar uma cópia de segurança?

Pode restaurar uma cópia de segurança completa ou de um arquivo individual a partir da cópia de segurança. Para restaurar, utilize o seguinte comando:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Após o restauro, é recuperar o ficheiro no diretório de trabalho atual.

O comando seguinte mostra o restauro de um arquivo *nomedeanfitrião fstabfrom* o arquivo de backup *backup.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Precisa copiar o ficheiro para a localização pretendida, após o restauro da cópia de segurança.

Captura de ecrã seguinte mostra o restauro de uma cópia de segurança completa:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Como instalar a ferramenta de atrás e alterar a configuração? 

Os pacotes de Relax-e-recuperação (traseiro) estão **pré-instalado** no **tipo II SKUs** de instâncias grandes do HANA e nenhuma ação necessária da sua. Diretamente pode começar a utilizar o traseiro para a cópia de segurança do sistema operativo.
No entanto, nas circunstâncias em que tem de instalar os pacotes no seu próprio, pode seguir os passos indicados para instalar e configurar a ferramenta de trás.

Para instalar o **traseiro** pacotes de cópia de segurança, utilize os seguintes comandos:

Para **SLES** sistema operativo, utilize o seguinte comando:
```
#zypper install <rear rpm package>
```
Para **RHEL** sistema operativo, utilize o seguinte comando: 
```
#yum install rear -y
```
Para configurar a ferramenta de trás, tem de atualizar os parâmetros **OUTPUT_URL** e **BACKUP_URL** no *ficheiro /etc/rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

Captura de ecrã seguinte mostra o restauro de uma cópia de segurança completa: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
