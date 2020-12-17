---
title: Restaurar usando a ferramenta Azure Application Consistent Snapshot para ficheiros Azure NetApp / Microsoft Docs
description: Fornece um guia para executar o comando de restauro da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 1c6b7ec6c4ef24ec00fbfc55a65a968e00561c2e
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632899"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool-preview"></a>Restaurar usando a ferramenta Azure Application Consistent Snapshot (pré-visualização)

Este artigo fornece um guia para executar o comando de restauro da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.

## <a name="introduction"></a>Introdução

Fazer um restauro de volume a partir de uma imagem é feito usando o `azacsnap -c restore` comando.

> [!IMPORTANT]
> Isto não realiza uma recuperação de base de dados, apenas uma restauração de volume(s) conforme descrito para cada uma das opções abaixo.

## <a name="command-options"></a>Opções de comando

O `-c restore` comando tem as seguintes opções:

- `--restore snaptovol` Cria um novo volume com base no último instantâneo no volume alvo. Este comando cria um novo volume "clonado" baseado no volume de alvo configurado, utilizando o último instantâneo de volume como base para criar o novo volume.  Este comando não interrompe a replicação do armazenamento do primário para o secundário. Em vez disso, os clones do último instantâneo disponível são criados no site DR e são apresentados pontos de montagem recomendados do sistema de ficheiros dos volumes clonados. Este comando deve ser executado no sistema Azure Large Instance **na região DR** (isto é, o sistema de falha do alvo).

- `--restore revertvolume` Reverte o volume de alvo para um estado anterior com base no instantâneo mais recente.  Usando este comando como parte do DR Failover na região dr emparelhada. Este comando **para** a replicação de armazenamento do local primário para o local secundário, e reverte o volume(s) alvo dr(s) para o seu mais recente instantâneo disponível nos volumes DR, juntamente com os pontos de montagem recomendados do sistema de ficheiros para os volumes DR revertidos. Este comando deve ser executado no sistema Azure Large Instance **na região DR** (isto é, o sistema de falha do alvo).
    > [!NOTE]
    > O sub-comando `--restore revertvolume` ( ) só está disponível para Azure Large Instance e não está disponível para Ficheiros Azure NetApp.
- `--hanasid <SAP HANA SID>` é o SAP HANA SID sendo selecionado a partir do ficheiro de configuração para aplicar os comandos de restauro de volume para.

- `[--configfile <config filename>]` é um parâmetro opcional que permite nomes de ficheiros de configuração personalizados.

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>Realizar um teste DR failover `azacsnap -c restore --restore snaptovol`

Este comando é como o comando "full" DR Failover ( `--restore restorevolume` ), mas em vez de quebrar a replicação entre o local primário e o local de recuperação de desastres, um volume de clone é criado a partir dos volumes de recuperação de desastres, permitindo a restauração do instantâneo mais recente no site DR. Esses volumes clonados são então utilizáveis pelo cliente para testar a Recuperação de Desastres sem ter de executar uma falha completa do seu ambiente HANA que quebra o acordo de replicação entre o local primário e o local de recuperação de desastres.

- Vários pontos de restauro diferentes podem ser testados desta forma, cada um com o seu próprio ponto de restauração.
- O clone é designado pelo carimbo de tempo quando o comando foi executado e representa os dados mais recentes e outros instantâneos disponíveis quando executado.

> [!IMPORTANT]
> Esta operação aplica-se apenas à Azure Large Instance.
>
> - Quando este comando é executado, requer o e-mail de contacto para as operações para estabelecer a ligação com antes da eliminação dos clones após 4 semanas.
> - Cada execução deste comando criará um novo clone que deve ser eliminado pelas Operações da Microsoft quando o teste estiver concluído.
> - Quaisquer volumes de clones criados serão automaticamente eliminados após 4 semanas.

O ficheiro de configuração (por exemplo, `DR.json` ) deve conter apenas os volumes DR e não os volumes de Produção, caso contrário os volumes de Produção poderiam ter clones criados.

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>Saída do `azacsnap -c restore --restore snaptovol` comando (para Single-Node cenário)

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --hanasid H80
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to simulate a Disaster Recovery
  failover without actually requiring a failover and subsequent failback.
* Any other restore points must be handled by Microsoft Operations.
* As part of the process, a clone is created of the each of the 'data' and 'other'
  volumes per the configuration file.

Do you wish to continue? (y/n) [n]: y

About to create clones of volumes based on the latest snapshot, these will be
kept for 4 weeks before being automatically deleted by Microsoft Operations.
Enter an email address to contact when deleting clones: <b>person@nowhere.com</b>
Checking state of HLI volumes for SID 'PEW'
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_sapprdhdb80_mnt00001_t020_xdp_rwclone_20200916_0256  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_sapprdhdb80_t020_xdp_rwclone_20200916_0256  /hana/log_backups/H80/01 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*******************  HANA Test DR Restore Steps  ******************************
* Complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
*  These snapshot copies (clones) are kept for 4 weeks before                  *
*  being automatically removed.                                                *
*  Please contact Microsoft Operations to delete them earlier.                 *
********************************************************************************
```

> [!IMPORTANT]
> A saída "Display Mount Points by Volume" é diferente para os vários cenários.

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>Executar falha completa dr `azacsnap -c restore --restore revertvolume`

Este comando **para** a replicação de armazenamento do local primário para o local secundário, restaura a última imagem nos volumes DR e fornece os pontos de montagem para os volumes DR.

Este comando DEVE ser executado no servidor DR utilizando um ficheiro de configuração (por exemplo, `DR.json` ) com volumes DR APENAS!

Executar um failover no site DR, executando o comando `azacsnap -c restore --restore revertvolume` .  Este comando requer que um SID seja adicionado como parâmetro. Este é o SID da instância HANA, que precisa de ser recuperado no local dr.

> [!IMPORTANT]
> Só execute este comando se estiver a planear realizar o exercício DR ou um teste. Este comando quebra a replicação. Tem de contactar a Microsoft Operations para voltar a ativar a replicação.

No alto nível, aqui estão os passos para executar um dr failover:

- Tens de desligar a instância HANA no local **principal.** Esta ação só é necessária se estiver realmente a fazer o failover para o site DR para evitar inconsistências de dados.
- Desligue a instância HANA no nó DR para a produção SID.
- Execute o comando `azacsnap -c restore --restore revertvolume` no nó DR com o SID a ser recuperado.
  - O comando quebra a ligação de replicação de armazenamento da Primária para o local DR
  - O comando restaura os volumes "dados" e "outros" como configurados.  Normalmente, esta operação seria para os volumes para os `/hana/data` sistemas de `/hana/logbackups` ficheiros e ficheiros.  O `/hana/shared` sistema de ficheiros NÃO é recuperado, mas utiliza o existente `/hana/shared` para SID na localização DR.
  - Monte os `/hana/data` e `/hana/logbackups` volumes – certifique-se de que são adicionados ao `/etc/fstab` arquivo
- Restaurar a imagem HANA SYSTEMDB. O estúdio HANA apenas mostra o mais recente instantâneo HANA disponível sob o instantâneo de armazenamento restaurado como parte da execução do comando `azacsnap -c restore --restore revertvolume` instantâneo.
- Recupere a base de dados dos inquilinos.
- Inicie a instância HANA no site DR para o SID de produção (exemplo: H80 neste caso).
- Efetuar qualquer teste de base de dados.

## <a name="next-steps"></a>Passos seguintes

- [Pegue uma cópia de segurança.](azacsnap-cmd-ref-backup.md)
- [Obtenha detalhes instantâneos](azacsnap-cmd-ref-details.md)
