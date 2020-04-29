---
title: Backup e restauro do sistema operativo SAP HANA em Azure (Grandes Instâncias) tipo II SKUs; Microsoft Docs
description: Executar a cópia de segurança do sistema operativo e restaurar para SAP HANA em Azure (Grandes Instâncias) Tipo II SKUs
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 100e1b974e54d8c0065194bc7beb18f458011434
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77616872"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Backup osS e restauro para SKUs tipo II de carimbos de Revisão 3

Este documento descreve os passos para executar uma cópia de segurança do nível de ficheiro do sistema operativo e restaurar para as **SKUs tipo II** das Grandes Instâncias HANA da Revisão 3. 

>[!Important]
> **Este artigo não se aplica às implantações do Tipo II SKU em selos de grande instância HANA.** Boot LUNS of Type II HANA Grandes unidades de instância que são implantadas em carimbos de grande instância HANA revisão podem ser apoiados com instantâneos de armazenamento, como é o caso das SKUs tipo I já em carimbos de Revisão 3


>[!NOTE]
>Os scripts de backup DO OS utilizam o software ReaR, que está pré-instalado no servidor.  

Após a disponibilização estar `Service Management` completa da equipa da Microsoft, por padrão, o servidor é configurado com dois horários de backup para fazer o backup do nível do sistema de ficheiros no detrás do sistema operativo. Pode verificar os horários dos trabalhos de backup utilizando o seguinte comando:
```
#crontab –l
```
Pode alterar o horário de cópia de segurança a qualquer momento utilizando o seguinte comando:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Como fazer uma cópia manual?

A cópia de segurança do sistema de ficheiros S está agendada usando um **trabalho de cron.** No entanto, também pode executar manualmente o nível de cópia de segurança do nível de ficheiro do sistema operativo. Para efetuar uma cópia de segurança manual, execute o seguinte comando:

```
#rear -v mkbackup
```
O seguinte ecrã mostra a cópia de segurança manual da amostra:

![como como](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Como restaurar uma reserva?

Pode restaurar uma cópia de segurança completa ou um ficheiro individual a partir da cópia de segurança. Para restaurar, utilize o seguinte comando:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Após a restauração, o ficheiro é recuperado no atual diretório de trabalho.

O comando seguinte mostra a restauração de um ficheiro */etc/fstab da* cópia de segurança do ficheiro de *reserva.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Tem de copiar o ficheiro para a localização desejada depois de restaurado a partir da cópia de segurança.

A imagem seguinte mostra a restauração de uma cópia de segurança completa:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Como instalar a ferramenta ReaR e alterar a configuração? 

Os pacotes Relax-and-Recover (ReaR) são **pré-instalados** no **Tipo II SKUs** de grandes instâncias HANA, e nenhuma ação necessária de si. Pode começar a utilizar diretamente o ReaR para a cópia de segurança do sistema operativo.
No entanto, nas circunstâncias em que necessita de instalar as embalagens por si só, pode seguir os passos listados para instalar e configurar a ferramenta ReaR.

Para instalar as embalagens de backup **ReaR,** utilize os seguintes comandos:

Para o sistema operativo **SLES,** utilize o seguinte comando:
```
#zypper install <rear rpm package>
```
Para o sistema operativo **RHEL,** utilize o seguinte comando: 
```
#yum install rear -y
```
Para configurar a ferramenta ReaR, é necessário atualizar os parâmetros **OUTPUT_URL** e **BACKUP_URL** no *ficheiro /etc/traseira/local.conf*.
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

A seguinte imagem mostra a restauração de uma cópia de segurança completa: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
