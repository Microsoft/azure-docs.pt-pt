---
title: Backup do sistema operativo e restauro do SAP HANA em SKUs do tipo II do tipo Azure| Microsoft Docs
description: Executar backup do sistema operativo e restaurar para SAP HANA em Azure (Grandes Instâncias) SKUs tipo II
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c03aa119b40a81f553a97ec013f89f88daabf293
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668661"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Backup de SO e restauro para SKUs tipo II de revisão 3 selos

Este documento descreve os passos para executar uma cópia de segurança do nível de ficheiro do sistema operativo e restaurar os **SKUs tipo II** das Grandes Instâncias HANA da Revisão 3. 

>[!Important]
> **Este artigo não se aplica às implementações do Tipo II SKU nos selos de Revisão 4 HANA Large Instance.** Boot LUNS de unidades de grande instância tipo II HANA que são implantadas na Revisão 4 Selos de Grande Instância HANA podem ser apoiados com instantâneos de armazenamento, como é o caso dos SKUs tipo I já em selos de Revisão 3


>[!NOTE]
>Os scripts de backup do SISTEMA utilizam o software ReaR, que está pré-instalado no servidor.  

Depois de o provisionamento ser concluído pela equipa da `Service Management` Microsoft, por padrão, o servidor é configurado com dois horários de backup para fazer backup do sistema de ficheiros de volta do sistema operativo. Pode consultar os horários dos trabalhos de reserva utilizando o seguinte comando:
```
#crontab –l
```
Pode alterar o horário de backup a qualquer momento utilizando o seguinte comando:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Como fazer uma cópia de segurança manual?

A cópia de segurança do sistema de ficheiros OS já está agendada usando um **cron job.** No entanto, também pode efetuar manualmente a cópia de segurança do nível de ficheiro do sistema operativo. Para efetuar uma cópia de segurança manual, execute o seguinte comando:

```
#rear -v mkbackup
```
O seguinte ecrã mostra a cópia de segurança manual da amostra:

![como](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Como restaurar um reforço?

Pode restaurar uma cópia de segurança completa ou um ficheiro individual a partir da cópia de segurança. Para restaurar, utilize o seguinte comando:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Após a restauração, o ficheiro é recuperado no diretório de trabalho atual.

O seguinte comando mostra a restauração de um ficheiro */etc/fstab da* cópia de segurança do ficheiro *de cópia de segurança.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Tem de copiar o ficheiro para a localização desejada depois de ser restaurado a partir da cópia de segurança.

A imagem a seguir mostra o restabelecemento de uma cópia de segurança completa:

![A screenshot mostra uma janela de ordem de comando com o restauro.](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Como instalar a ferramenta ReaR e alterar a configuração? 

Os pacotes Relax-and-Recover (ReaR) estão **pré-instalados** nos **SKUs tipo II** de HANA Large Instances, e não é necessária nenhuma ação por si. Pode começar a utilizar diretamente o ReaR para a cópia de segurança do sistema operativo.
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
Para configurar a ferramenta ReaR, é necessário atualizar os parâmetros **OUTPUT_URL**  e **BACKUP_URL**  no *ficheiro /etc/rear/local.conf*.
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

A imagem que se segue mostra o restabelecemento de uma cópia de segurança completa: ![ O Screenshot mostra uma janela de pedido de comando com a restauração utilizando a ferramenta ReaR.](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
