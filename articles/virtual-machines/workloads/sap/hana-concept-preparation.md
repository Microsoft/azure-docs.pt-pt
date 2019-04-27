---
title: Princípios de recuperação após desastre e a preparação no SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Princípios de recuperação após desastre e a preparação no SAP HANA no Azure (instâncias grandes)
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
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 73643cd6954932f933e200baad09e4301300aac2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794826"
---
# <a name="disaster-recovery-principles"></a>Princípios de recuperação após desastre

Instâncias grandes do HANA oferecem uma funcionalidade de recuperação após desastre entre carimbos de data / instância grande do HANA em diferentes regiões do Azure. Por exemplo, se implementar unidades de instância grande do HANA na região e.u.a. centro-oeste do Azure, pode utilizar as unidades de instância grande do HANA na região E.U. a leste como unidades de recuperação após desastre. Como mencionado anteriormente, recuperação após desastre não é configurada automaticamente, uma vez que ela requer que paga para outra unidade de instância grande do HANA na região DR. A configuração de recuperação após desastre funciona para instalações de aumentar verticalmente, bem como de escalamento horizontal. 

Nos cenários implementados até agora, os clientes utilizam a unidade na região DR para executar sistemas de não produção que utilizam uma instância HANA instalada. A unidade de instância grande do HANA tem de ser do mesmo SKU como o SKU utilizado para fins de produção. A imagem seguinte mostra que a configuração de disco entre a unidade do servidor na região do Azure de produção e a região de recuperação após desastre é semelhante a:

![Configuração de configuração de DR do ponto de vista do disco](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Conforme mostrado neste gráfico de descrição geral, em seguida, terá de encomendar um segundo conjunto de volumes de disco. Os volumes de disco de destino são o mesmo tamanho que os volumes de produção para a instância de produção em unidades de recuperação após desastre. Estes volumes de disco estão associados com a unidade do servidor de instância grande do HANA no site de recuperação após desastre. Os seguintes volumes são replicados a partir da região de produção para o site de DR:

- /hana/data
- /hana/logbackups 
- /Hana/Shared (inclui o sap/usr /)

O volume de /hana/log não é replicado porque o registo de transações do SAP HANA não é necessária da forma que a restauração a partir desses volumes. 

A base da funcionalidade de recuperação após desastre oferecido a funcionalidade de replicação de armazenamento é oferecida pela infraestrutura de instância grande do HANA. A funcionalidade que é utilizada no lado do armazenamento não é um fluxo constante de alterações que são replicados de forma assíncrona, à medida que as alterações ocorrem ao volume de armazenamento. Em vez disso, é um mecanismo que se baseia no fato de que os instantâneos destes volumes são criados em intervalos regulares. O delta entre um instantâneo já replicado e um novo instantâneo que ainda não foi replicado, em seguida, é transferido para o site de recuperação após desastre em volumes de disco de destino.  Estes instantâneos são armazenados nos volumes e, se houver uma ativação de pós-falha para a recuperação de desastres, tem de ser restaurada nesses volumes.  

A primeira transferência dos dados do volume completos deve ter antes da quantidade de dados torna-se mais pequena do que as diferenças entre os instantâneos. Como resultado, os volumes no site de DR contenham cada um dos instantâneos de volume realizada no site de produção. Eventualmente, pode utilizar esse sistema de DR para obter um estado anterior para recuperar dados perdidos, sem reverter o sistema de produção.

Se existir uma implementação de MCOD com várias instâncias do SAP HANA independentes numa só unidade de instância grande do HANA, espera-se que todas as instâncias do SAP HANA estão a obter armazenamento replicado para o lado do DR.

Em casos em que utiliza o HANA System Replication como funcionalidade de elevada disponibilidade no seu site de produção e utilizar a replicação de armazenamento para o site de DR, os volumes de ambos os nós de site primário para a instância de DR são replicados. Tem de comprar armazenamento adicional (tamanho da mesmo a partir do nó principal) no site de DR para acomodar os replicação a partir de primários e secundários para o DR. 



>[!NOTE]
>A funcionalidade de replicação de armazenamento de instância grande do HANA é espelhamento e replicação de instantâneos de armazenamento. Se não efetuar instantâneos de armazenamento como a apresentada na secção de cópia de segurança e restauro deste artigo, não pode ser qualquer replicação para o site de recuperação após desastre. Execução de instantâneo de armazenamento é um pré-requisito para a replicação de armazenamento para o site de recuperação após desastre.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Preparação do cenário de recuperação após desastre
Neste cenário, tem um sistema de produção em execução nas instâncias grandes do HANA a região do Azure de produção. Para obter os passos que se seguem, vamos supor que o SID desse sistema HANA é "PRD=IIS&sbp=&PVER=5.0&ID=500;100&cat", e de que tem um sistema de não produção em execução nas instâncias grandes do HANA na região do Azure de DR. Para o último, vamos supor que o seu SID é "TST." A imagem seguinte mostra esta configuração:

![Início do programa de configuração de DR](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Se a instância de servidor já não tiver sido ordenada com o conjunto de volume de armazenamento adicional, SAP HANA no Azure Service Management anexa o conjunto adicional de volumes como um destino para a réplica de produção para a unidade de instância grande do HANA no qual está a executar o TST Instância do HANA. Para essa finalidade, terá de fornecer o SID da sua instância do HANA de produção. Depois de SAP HANA no Azure Service Management confirma o anexo desses volumes, terá de montar os volumes para a unidade de instância grande do HANA.

![Passo seguinte de configuração de DR](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

A próxima etapa é para instalar a segunda instância do SAP HANA na unidade na região do Azure de DR, em que executou a instância TST HANA instância grande do HANA. A instância recém-instalada do SAP HANA tem de ter o mesmo SID. Os utilizadores criados tem de ter o mesmo UID e ID de grupo que tenha a instância de produção. Leia [Backup e restauração](hana-backup-restore.md) para obter detalhes. Se a instalação foi concluída com êxito, terá de:

- Executar o passo 2 da preparação de instantâneo de armazenamento descrito em [Backup e restauração](hana-backup-restore.md).
- Crie uma chave pública para a unidade de DR de unidade de instância grande do HANA, se ainda não o fez isso. Consulte o passo 3 da preparação de instantâneo de armazenamento descrito em [Backup e restauração](hana-backup-restore.md).
- Manter o *HANABackupCustomerDetails.txt* com a nova instância HANA e teste se a conectividade para o armazenamento funciona corretamente.  
- Pare a instância do SAP HANA recentemente instalada na unidade instância grande do HANA na região do Azure de DR.
- Desmonte estes volumes PRD=IIS&sbp=&PVER=5.0&ID=500;100&cat e contacte o SAP HANA no Azure Service Management. Os volumes não podem permanecer montados para a unidade, pois não pode estar acessíveis ao funcionar como destino de replicação de armazenamento.  

![Passo de configuração de DR antes de estabelecer a replicação](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

A equipe de operações estabelece a relação de replicação entre os volumes de PRD=IIS&sbp=&PVER=5.0&ID=500;100&cat a região do Azure de produção e os volumes de PRD=IIS&sbp=&PVER=5.0&ID=500;100&cat na região do Azure de DR.

>[!IMPORTANT]
>O volume de /hana/log não é replicado porque não é necessário restaurar a base de dados do SAP HANA replicada para um estado consistente no site de recuperação após desastre.

Em seguida, configurar ou ajustar o agendamento de cópia de segurança de instantâneo de armazenamento para obter o RTO e RPO, no caso de desastre. Para minimizar o objetivo de ponto de recuperação, defina os seguintes intervalos de replicação no serviço de instância grande do HANA:
- Para os volumes abrangidos pelo instantâneo combinado (tipo de instantâneo **hana**), o conjunto para replicar a cada 15 minutos para os destinos de volume de armazenamento equivalente no site de recuperação após desastre.
- Para o volume de cópia de segurança de registo de transação (tipo de instantâneo **registos**), o conjunto para replicar a cada 3 minutos para os destinos de volume de armazenamento equivalente no site de recuperação após desastre.

Para minimizar o objetivo de ponto de recuperação, configure o seguinte:
- Efetuar uma **hana** instantâneo de armazenamento do tipo (consulte "passo 7: Executar instantâneos") a cada 30 minutos para 1 hora.
- Execute backups de log de transação do SAP HANA a cada 5 minutos.
- Efetuar uma **registos** escreva cada 5-15 minutos de instantâneos de armazenamento. Com este período de intervalo, alcançar um RPO de cerca de 15 a 25 minutos.

Com esta configuração, a seqüência de backups de log de transação, instantâneos de armazenamento e a replicação da transação HANA registar dados de volume e/hana/cópia de segurança e, como os dados mostrados neste gráfico, pode ser /hana/shared (inclui o sap/usr /):

 ![Relação entre um instantâneo de cópia de segurança de registo de transação e um espelho snap num eixo de tempo](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Para alcançar um RPO ainda melhor no caso de recuperação após desastre, pode copiar os backups de log de transação do HANA do SAP HANA no Azure (instâncias grandes) para a outra região do Azure. Para obter esta redução de RPO adicional, execute os seguintes passos:

1. Cópia de segurança da transação do HANA inicie sessão com tanta freqüência que possível para /hana/logbackups.
1. Utilize o rsync para copiar os backups de log de transação para as NFS partilha alojada máquinas virtuais do Azure. As VMs estão em redes virtuais do Azure na região do Azure de produção e nas regiões de DR. Tem de se ligar a ambas as redes virtuais do Azure para o circuito a ligar a instâncias grandes do HANA de produção para o Azure. Ver os gráficos da [considerações sobre a recuperação após desastre com o HANA nas instâncias grandes de rede](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) secção. 
1. Mantenha que os backups de log de transação na região na VM anexados para o NFS exportados armazenamento.
1. Um caso de ativação pós-falha do desastre, complemente os backups de log de transação encontradas no /hana/logbackups volume com mais recentemente backups de log de transação no NFS partilham no site de recuperação após desastre. 
1. Inicie uma cópia de segurança do registo de transações para restaurar a cópia de segurança mais recente que pode ser guardada em para a região de DR.

Quando as operações de instância grande do HANA confirmar a configuração de relação de replicação e iniciar as cópias de segurança de instantâneos de armazenamento de execução, começa a replicação de dados.

![Passo de configuração de DR antes de estabelecer a replicação](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Conforme o andamento da replicação, os instantâneos nos volumes PRD=IIS&sbp=&PVER=5.0&ID=500;100&cat nas regiões do Azure de DR não forem restaurado. Apenas são armazenados. Se os volumes são instalados num estado desse tipo, eles representam o estado em que desmontados desses volumes depois da instância de PRD=IIS&sbp=&PVER=5.0&ID=500;100&cat SAP HANA foi instalada na unidade do servidor na região do Azure de DR. Eles também representam as cópias de segurança de armazenamento que ainda não foram restauradas.

Se houver uma ativação pós-falha, também pode optar por restaurar para um instantâneo de armazenamento mais antigo em vez do instantâneo de armazenamento mais recentes.

## <a name="next-steps"></a>Passos Seguintes

- Consultar [procedimento de ativação pós-falha de recuperação após desastre](hana-failover-procedure.md).
