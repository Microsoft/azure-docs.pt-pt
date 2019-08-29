---
title: Princípios de recuperação de desastres e preparação em SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Princípios de recuperação de desastres e preparação em SAP HANA no Azure (instâncias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d52f871de75a7f7d34016b040e44d6f1623fd8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101268"
---
# <a name="disaster-recovery-principles"></a>Princípios de recuperação de desastre

O HANA em instâncias grandes oferece uma funcionalidade de recuperação de desastre entre carimbos de instância grande do HANA em diferentes regiões do Azure. Por exemplo, se você implantar unidades de instância grande do HANA na região oeste dos EUA do Azure, poderá usar as unidades de instância grande do HANA na região leste dos EUA como unidades de recuperação de desastre. Como mencionado anteriormente, a recuperação de desastre não é configurada automaticamente, pois requer que você pague por outra unidade de instância grande do HANA na região de DR. A configuração de recuperação de desastre funciona para configurações de expansão e expansão. 

Nos cenários implantados até agora, os clientes usam a unidade na região de recuperação de desastre para executar sistemas de não produção que usam uma instância do HANA instalada. A unidade de instância grande do HANA precisa ser da mesma SKU que a SKU usada para fins de produção. A imagem a seguir mostra qual a configuração de disco entre a unidade do servidor na região de produção do Azure e a região de recuperação de desastre é semelhante a:

![Configuração de DR a partir do ponto de vista do disco](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Conforme mostrado neste gráfico de visão geral, você precisa solicitar um segundo conjunto de volumes de disco. Os volumes de disco de destino são do mesmo tamanho que os volumes de produção para a instância de produção nas unidades de recuperação de desastre. Esses volumes de disco estão associados à unidade de servidor do SAP HANA em instâncias grandes no site de recuperação de desastre. Os seguintes volumes são replicados da região de produção para o local de recuperação de desastre:

- /Hana/data
- /Hana/logbackups 
- /Hana/Shared (inclui/usr/SAP)

O volume/Hana/log não é replicado porque o log de transações SAP HANA não é necessário na forma como a restauração desses volumes é feita. 

A base da funcionalidade de recuperação de desastre oferecida é a funcionalidade de replicação de armazenamento oferecida pela infraestrutura de instância grande do HANA. A funcionalidade usada no lado do armazenamento não é um fluxo constante de alterações que são replicadas de maneira assíncrona quando as alterações acontecem no volume de armazenamento. Em vez disso, é um mecanismo que depende do fato de que os instantâneos desses volumes são criados regularmente. O Delta entre um instantâneo já replicado e um novo instantâneo que ainda não foi replicado é transferido para o site de recuperação de desastres para volumes de disco de destino.  Esses instantâneos são armazenados nos volumes e, se houver um failover de recuperação de desastre, precisarão ser restaurados nesses volumes.  

A primeira transferência dos dados completos do volume deve ser anterior à quantidade de dados que se torna menor do que os deltas entre os instantâneos. Como resultado, os volumes no site de recuperação de desastre contêm todos os instantâneos de volume executados no site de produção. Eventualmente, você pode usar esse sistema de DR para obter um status anterior para recuperar dados perdidos, sem reverter o sistema de produção.

Se houver uma implantação do MCOD com várias instâncias de SAP HANA independentes em uma unidade do SAP HANA em instâncias grandes, espera-se que todas as instâncias de SAP HANA estejam obtendo armazenamento replicado para o lado da recuperação de desastre.

Nos casos em que você usa a replicação de sistema do HANA como funcionalidade de alta disponibilidade em seu site de produção e usa a replicação baseada em armazenamento para o site de recuperação de desastre, os volumes de ambos os nós do site primário para a instância de DR são replicados. Você deve adquirir armazenamento adicional (mesmo tamanho do nó primário) no site de DR para acomodar a replicação de primária e secundária para a recuperação de desastre. 



>[!NOTE]
>A funcionalidade de replicação de armazenamento de instância grande do HANA é espelhamento e replicação de instantâneos de armazenamento. Se você não executar instantâneos de armazenamento, conforme apresentado na seção backup e restauração deste artigo, não poderá haver nenhuma replicação para o site de recuperação de desastre. A execução do instantâneo de armazenamento é um pré-requisito para a replicação de armazenamento para o site de recuperação de desastre.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Preparação do cenário de recuperação de desastres
Nesse cenário, você tem um sistema de produção em execução no HANA em instâncias grandes na região do Azure de produção. Para as etapas a seguir, vamos supor que o SID desse sistema HANA seja "PRD" e que você tenha um sistema que não seja de produção em execução no HANA em instâncias grandes na região do Azure de recuperação de desastres. Para o último, vamos supor que seu SID é "TST". A imagem a seguir mostra essa configuração:

![Início da configuração de DR](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Se a instância de servidor ainda não tiver sido ordenada com o conjunto de volumes de armazenamento adicional, SAP HANA no gerenciamento de serviços do Azure anexará o conjunto adicional de volumes como um destino para a réplica de produção à unidade de instância grande do HANA na qual você está executando o TST Instância do HANA. Para essa finalidade, você precisa fornecer o SID de sua instância do HANA de produção. Depois que SAP HANA no gerenciamento de serviços do Azure confirmar o anexo desses volumes, você precisará montar esses volumes na unidade de instância grande do HANA.

![Próxima etapa da configuração de DR](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

A próxima etapa é instalar a segunda instância de SAP HANA na unidade de instância grande do HANA na região do Azure de recuperação de desastre, em que você executa a instância do TST HANA. A instância de SAP HANA recém-instalada precisa ter o mesmo SID. Os usuários criados precisam ter a mesma UID e ID de grupo que a instância de produção tem. Leia [backup e restauração](hana-backup-restore.md) para obter detalhes. Se a instalação tiver sido bem-sucedida, você precisará:

- Execute a etapa 2 da preparação do instantâneo de armazenamento descrita em [backup e restauração](hana-backup-restore.md).
- Crie uma chave pública para a unidade de recuperação de desastres da unidade de instância grande do HANA, caso ainda não tenha feito isso. Consulte a etapa 3 da preparação do instantâneo de armazenamento descrita em [backup e restauração](hana-backup-restore.md).
- Mantenha o *HANABackupCustomerDetails. txt* com a nova instância do Hana e teste se a conectividade com o armazenamento funciona corretamente.  
- Pare a instância de SAP HANA recentemente instalada na unidade de instância grande do HANA na região do Azure de recuperação de desastre.
- Desmonte esses volumes PRD e entre em contato com SAP HANA no gerenciamento de serviços do Azure. Os volumes não podem permanecer montados na unidade porque não podem ser acessados enquanto funcionam como destino de replicação de armazenamento.  

![Etapa de configuração de DR antes de estabelecer a replicação](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

A equipe de operações estabelece a relação de replicação entre os volumes PRD na região do Azure de produção e os volumes PRD na região do Azure de recuperação de desastre.

>[!IMPORTANT]
>O volume/Hana/log não é replicado porque não é necessário restaurar o banco de dados SAP HANA replicado para um estado consistente no site de recuperação de desastre.

Em seguida, configure ou ajuste o agendamento de backup do instantâneo de armazenamento para chegar ao RTO e RPO no caso de desastre. Para minimizar o objetivo do ponto de recuperação, defina os seguintes intervalos de replicação no serviço de instância grande do HANA:
- Para os volumes cobertos pelo instantâneo combinado (tipo de instantâneo **Hana**), defina para replicar a cada 15 minutos para os destinos de volume de armazenamento equivalentes no site de recuperação de desastre.
- Para o volume de backup de log de transações ( **logs**de tipo de instantâneo), defina para replicar a cada 3 minutos para os destinos de volume de armazenamento equivalentes no site de recuperação de desastre.

Para minimizar o objetivo do ponto de recuperação, configure o seguinte:
- Execute um instantâneo de armazenamento do tipo **Hana** (consulte a "etapa 7: Executar instantâneos ") a cada 30 minutos a 1 hora.
- Execute SAP HANA backups de log de transações a cada 5 minutos.
- Execute um instantâneo de armazenamento do tipo de **logs** a cada 5-15 minutos. Com esse período de intervalo, você alcança um RPO de cerca de 15-25 minutos.

Com essa configuração, a sequência de backups de log de transações, instantâneos de armazenamento e a replicação do volume de backup de log de transações do HANA e/Hana/data e/Hana/Shared (inclui/usr/SAP) podem ser semelhantes aos dados mostrados neste gráfico:

 ![Relação entre um instantâneo de backup de log de transações e um espelho de ajuste em um eixo de tempo](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Para obter um RPO ainda melhor no caso de recuperação de desastres, você pode copiar os backups de log de transações do HANA de SAP HANA no Azure (instâncias grandes) para a outra região do Azure. Para obter essa redução adicional de RPO, execute as seguintes etapas:

1. Fazer backup do log de transações do HANA o mais frequentemente possível para/Hana/logbackups.
1. Use o rsync para copiar os backups de log de transações para as máquinas virtuais do Azure hospedadas no compartilhamento NFS. As VMs estão em redes virtuais do Azure na região de produção do Azure e nas regiões de DR. Você precisa conectar ambas as redes virtuais do Azure ao circuito conectando as instâncias grandes do HANA de produção ao Azure. Consulte os elementos gráficos na seção [considerações de rede para recuperação de desastre com instâncias grandes do Hana](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) . 
1. Mantenha os backups de log de transações na região da VM anexada ao armazenamento exportado NFS.
1. Em um caso de failover de desastre, complemente os backups de log de transações encontrados no volume/Hana/logbackups com backups de log de transações mais recentes no compartilhamento NFS no site de recuperação de desastre. 
1. Inicie um backup de log de transações para restaurar o backup mais recente que pode ser salvo na região de recuperação de desastres.

Quando as operações do HANA em instâncias grandes confirmarem a configuração do relacionamento de replicação e você iniciar os backups de instantâneo de armazenamento de execução, a replicação de dados começará.

![Etapa de configuração de DR antes de estabelecer a replicação](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

À medida que a replicação progride, os instantâneos nos volumes PRD nas regiões do Azure de recuperação de desastre não são restaurados. Eles são armazenados somente. Se os volumes forem montados nesse estado, eles representarão o estado no qual você desmontou esses volumes após a instância de SAP HANA de PRD ter sido instalada na unidade de servidor na região do Azure de recuperação de desastre. Eles também representam os backups de armazenamento que ainda não foram restaurados.

Se houver um failover, você também poderá optar por restaurar para um instantâneo de armazenamento mais antigo em vez do instantâneo de armazenamento mais recente.

## <a name="next-steps"></a>Passos Seguintes

- Consulte o [procedimento de failover de recuperação de desastre](hana-failover-procedure.md).
