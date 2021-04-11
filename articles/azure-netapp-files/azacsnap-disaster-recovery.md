---
title: Recuperação de desastres utilizando a ferramenta Azure Application Consistent Snapshot para ficheiros Azure NetApp | Microsoft Docs
description: Explica como realizar a recuperação de desastres ao utilizar a ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 554fa394179e7cfc5b86a2b50eb754547d137a44
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870398"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool-preview"></a>Recuperação de desastres utilizando a ferramenta Azure Application Consistent Snapshot (pré-visualização)

Este artigo explica como realizar a recuperação de desastres ao utilizar a ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.

> [!IMPORTANT]
> Esta operação aplica-se apenas à **Azure Large Instance.**

## <a name="introduction"></a>Introdução

A plataforma Azure Large Instance também pode ter um site de recuperação de desastres configurado onde os instantâneos de volume de armazenamento podem ser replicados.  Se as imagens foram configuradas corretamente com tal configuração, então é possível realizar uma Recuperação de Desastres neste site.  Este documento destina-se a ser um guia para a recuperação de desastres para esta configuração.

## <a name="prerequisites-for-disaster-recovery-setup"></a>Pré-requisitos para a instalação de recuperação de desastres

Os seguintes requisitos devem ser cumpridos antes de planear o fracasso da recuperação de desastres.

- Tem um nó DR no site dr. Há duas opções para dr. Um é DR normal, e outro é DR multiusos.
- Tem replicação de armazenamento a funcionar. A equipa de operações da Microsoft executa a configuração de replicação de armazenamento no momento do fornecimento de DR automaticamente. Pode monitorizar a replicação do armazenamento utilizando o comando `azacsnap -c details --details replication` no local DR.
- Configuraste e configuraste as fotos de armazenamento no local principal.
- Tem uma instância HANA instalada no local de DR para o primário com o mesmo SID que o exemplo principal tem.
- Você leu e compreende o procedimento dr failover descrito em [SAP HANA Large Instances alta disponibilidade e recuperação de desastres em Azure](../virtual-machines/workloads/sap/hana-failover-procedure.md)
- Você montou e configuraram instantâneos de armazenamento no local dr.
- Foi criado um ficheiro de configuração (por `DR.json` exemplo, ) com os volumes de armazenamento DR e informações associadas no servidor DR.
- Completou os passos no local dr para:
  - Ativar a comunicação com o armazenamento.
  - Ativar a comunicação com a SAP HANA.

## <a name="set-up-disaster-recovery"></a>Configurar a recuperação após desastre

A Microsoft suporta a replicação do nível de armazenamento para a recuperação de DR. Há duas maneiras de preparar o DR.

Um é **normal** e outro é **multiusos.** No DR **normal,** você tem um caso dedicado no local dr para falha. No cenário de DR **multiusos,** você tem outro caso QA ou desenvolvimento HANA em execução na unidade de grande instância HANA no site DR. Mas também instalou uma instância HANA pré-instalada que está adormecida e tem o mesmo SID que a instância HANA que pretende falhar naquela unidade HANA Large Instance. As operações da Microsoft configuram o ambiente para si, incluindo a replicação de armazenamento com base na entrada fornecida no Formulário de Pedido de Serviço (SRF) no momento do embarque.

> [!IMPORTANT]
> Certifique-se de que todos os pré-requisitos são cumpridos para a configuração dr.

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>Monitorize a replicação de dados do site Primário para o DR

A equipa de operações da Microsoft já gere e monitoriza a ligação DR do site principal para o site DR.
Pode monitorizar a replicação de dados do seu servidor primário para o servidor DR utilizando o comando snapshot `azacsnap -c details --details replication` .

## <a name="perform-a-failover-to-dr-site"></a>Execute uma falha no site DR

Executar o comando de failover no local DR `azacsnap -c restore --restore revertvolume` ().

> [!IMPORTANT]
> O `azacsnap -c restore --restore revertvolume` comando quebra a replicação de armazenamento do local de produção para o local dr. Tem de contactar as Operações da Microsoft para voltar a configurar a replicação. Uma vez que a replicação seja re-activada, todos os dados no armazenamento DR para este SID serão inicializados. O comando que executa o failover disponibiliza o instantâneo de armazenamento mais recentemente replicado. Se precisar de voltar a ter um instantâneo mais antigo, abra um pedido de apoio para que as operações possam ajudar a fornecer uma imagem anterior restaurada no site DR.

A um nível elevado, aqui estão os passos a seguir para dr failover:

- Tens de desligar a instância HANA no local **principal.** Esta ação só é necessária se estiver realmente a fazer o failover para o site DR para que não tenha inconsistências de dados.
- Desligue a instância HANA no nó DR para a produção SID.
- Execute o comando `azacsnap -c restore --restore revertvolume` no nó DR com o SID a ser recuperado
  - O comando quebra a ligação de replicação de armazenamento da Primária para o local DR
  - O comando restaura apenas o volume /dados e /logbackups, /volume partilhado NÃO é recuperado, mas antes utiliza o existente /partilhado para SID na localização DR.
  - Monte o volume de /dados e /logbackups – certifique-se de adicioná-lo ao ficheiro fstab
- Restaurar a imagem HANA SYSTEMDB. O estúdio HANA apenas mostra o mais recente instantâneo HANA disponível sob o instantâneo de armazenamento restaurado como parte da execução do `azacsnap -c restore --restore revertvolume` comando.
- Recupere a base de dados dos inquilinos.
- Inicie a instância HANA no site DR para o SID de produção (exemplo: H80 neste caso).
- Faça testes.

### <a name="example-performing-disaster-recovery"></a>Exemplo de recuperação de desastres

Esta subsecção descreve os passos detalhados para uma falha no local de recuperação de desastres.

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>Passo 1: Obtenha os detalhes do volume do nó DR

Execute o comando `df –h` para listar os sistemas de ficheiros e os volumes associados a que se refere após a falha.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>Passo 2: Desligar HANA no local primário

Se efetuar uma falha completa das cargas de trabalho de produção, e for possível ligar-se ao local de produção primário, desligue o(s) caso SAP HANA sendo falhado em DR.

Por exemplo, se iniciar sessão como raiz, o exemplo a seguir mostra como o SAP HANA pode ser desligado.  <sid>Substitua-o pelo SEU SAP HANA SID.

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>Passo 3: Desligue HANA no site DR

É importante desligar o SAP HANA no local dr antes de restaurar os volumes.

Por exemplo, se iniciar sessão como raiz, o exemplo a seguir mostra como o SAP HANA pode ser desligado.  <sid>Substitua-o pelo SEU SAP HANA SID.

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> Certifique-se de que as instâncias HANA no site dr estão desligadas antes de restaurar quaisquer volumes.

#### <a name="step-4-restore-the-volumes"></a>Passo 4: Restaurar os volumes

```bash
azacsnap -c restore --restore revertvolume --dbsid H80
```

**_Saída do comando de failover DR_**.

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --dbsid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> É necessário dar os passos no final do visor da consola para completar a preparação de armazenamento para uma falha dr.

#### <a name="step-5-unmount-unnecessary-filesystems"></a>Passo 5: Desmonte sistemas de ficheiros desnecessários

Execute o comando `umount` para desmontar os sistemas de ficheiros/volumes que não são necessários.

```bash
umount <Mount point>
```

Desmonte os dados e faça login pontos de suporte de reserva. Pode ter vários pontos de montagem de dados no cenário de escala.

#### <a name="step-6-configure-the-mount-points"></a>Passo 6: Configurar os pontos de montagem

Modifique o ficheiro `/etc/fstab` para comentar os dados e registar as entradas de backups para o SID primário (neste exemplo, SID=H80) e adicione as novas entradas de ponto de montagem criadas a partir dos volumes DR do site primário. As novas entradas do ponto de montagem são fornecidas na saída do comando.

- Comente os pontos de montagem existentes em execução no site DR com o `#` carácter:

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- Adicione as seguintes linhas para `/etc/fstab`
  > esta deve ser a mesma saída do comando

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>Passo 7: Monte os volumes de recuperação

Execute o comando `mount –a` para montar todos os pontos de montagem.

```bash
mount -a
```

Agora, se `df –h` executar, deve ver os `*_dp` volumes montados.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>Passo 8: Recuperar o SYSTEMDB

Do Estúdio HANA, clique à direita no systemDB e escolheu "Backup and Recovery", e depois "Recuperar Base de Dados do Sistema"

Consulte o guia para recuperar uma base de dados a partir de uma imagem, especificamente o SYSTEMDB.

#### <a name="step-9-recover-the-tenant-database"></a>Passo 9: Recuperar a base de dados do Inquilino

Do Estúdio HANA, clique à direita no systemDB e escolheu "Backup and Recovery", e depois "Recuperar a Base de Dados do Inquilino".

Consulte o guia para recuperar uma base de dados a partir de um instantâneo, especificamente da base de dados (s) DO INE.

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>Corra `azacsnap -c backup` no site dr

Se estiver a executar cópias de segurança baseadas em instantâneos no site DR, então o nome do servidor HANA configurado `azacsnap` no ficheiro de configuração no site DR deve ser o mesmo que o nome do servidor de produção.

> [!IMPORTANT]
> Executar a `azacsnap -c backup` lata criar instantâneos de armazenamento no site DR, estes não são automaticamente replicados para outro site.  Trabalhe com a Microsoft Operations para entender melhor a devolução de quaisquer ficheiros ou dados ao site de produção original.

## <a name="next-steps"></a>Passos seguintes

- [Obtenha detalhes instantâneos](azacsnap-cmd-ref-details.md)
- [Pegue uma cópia de segurança.](azacsnap-cmd-ref-backup.md)
