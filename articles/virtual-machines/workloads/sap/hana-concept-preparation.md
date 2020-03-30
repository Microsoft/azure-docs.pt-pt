---
title: Princípios de recuperação de desastres e preparação sobre sap HANA em Azure (Grandes Instâncias) [ Microsoft Docs
description: Princípios de recuperação de desastres e preparação sobre sap HANA em Azure (Grandes Instâncias)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101268"
---
# <a name="disaster-recovery-principles"></a>Princípios da recuperação de desastres

Hana Large Instances oferecem uma funcionalidade de recuperação de desastres entre selos hana large instância em diferentes regiões de Azure. Por exemplo, se implantar unidades HANA Large Instance na região oeste dos EUA de Azure, pode utilizar as unidades HANA Large Instance na região leste dos EUA como unidades de recuperação de desastres. Como mencionado anteriormente, a recuperação de desastres não é configurada automaticamente, porque requer que você pague por outra unidade HANA Large Instance na região de DR. A configuração de recuperação de desastres funciona para a escala-up, bem como configurações de escala para fora. 

Nos cenários implementados até agora, os clientes utilizam a unidade na região DR para executar sistemas de não produção que utilizam uma instância HANA instalada. A unidade HANA Large Instance tem de ser do mesmo SKU que o SKU utilizado para fins de produção. A imagem que se segue mostra como é a configuração do disco entre a unidade de servidores na região de produção do Azure e a região de recuperação de desastres:

![Configuração de configuração dr a partir do ponto de vista do disco](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Como mostrado neste gráfico de visão geral, é necessário encomendar um segundo conjunto de volumes de disco. Os volumes de disco-alvo são do mesmo tamanho que os volumes de produção para a instância de produção nas unidades de recuperação de desastres. Estes volumes de disco estão associados à unidade de servidor HANA Large Instance no local de recuperação de desastres. Os seguintes volumes são replicados da região de produção para o sítio DR:

- /hana/dados
- /hana/logbackups 
- /hana/shared (inclui /usr/seiva)

O volume de /hana/log não é replicado porque o registo de transações SAP HANA não é necessário na forma como a restauração desses volumes é feita. 

A base da funcionalidade de recuperação de desastres oferecida é a funcionalidade de replicação de armazenamento oferecida pela infraestrutura HANA Large Instance. A funcionalidade que é utilizada no lado do armazenamento não é um fluxo constante de alterações que se replicam de forma assíncrona à medida que as alterações acontecem ao volume de armazenamento. Em vez disso, é um mecanismo que se baseia no facto de que as imagens destes volumes são criadas regularmente. O delta entre um instantâneo já replicado e um novo instantâneo que ainda não é replicado é então transferido para o local de recuperação de desastres em volumes de discos-alvo.  Estes instantâneos são armazenados nos volumes e, se houver uma falha de recuperação de desastres, precisam de ser restaurados nesses volumes.  

A primeira transferência dos dados completos do volume deve ser antes que a quantidade de dados se torne menor do que os deltas entre instantâneos. Como resultado, os volumes no site dr contêm cada um dos instantâneos de volume realizados no local de produção. Eventualmente, pode usar esse sistema DEDr para chegar a um estado anterior para recuperar dados perdidos, sem reverter o sistema de produção.

Se houver uma implantação MCOD com múltiplas instâncias independentes de SAP HANA numa unidade HANA Large Instance, espera-se que todos os casos de SAP HANA estejam a ser replicados para o lado DR.

Nos casos em que utiliza a Replicação do Sistema HANA como funcionalidade de alta disponibilidade no seu local de produção e utiliza replicação baseada em armazenamento para o site DR, os volumes de ambos os nós do local primário para a instância DR são replicados. Deve adquirir armazenamento adicional (do mesmo tamanho do nó primário) no local dr para acomodar a replicação tanto do primário como secundário para o DR. 



>[!NOTE]
>A funcionalidade de replicação de armazenamento HANA Large Instance está a espelhar e replicar instantâneos de armazenamento. Se não executar instantâneos de armazenamento como introduzido na secção Backup e restaurar este artigo, não pode haver nenhuma replicação para o local de recuperação de desastres. A execução instantânea de armazenamento é um pré-requisito para a replicação de armazenamento para o local de recuperação de desastres.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Preparação do cenário de recuperação de desastres
Neste cenário, tem um sistema de produção em funcionamento em HANA Large Instances na produção da região de Azure. Para os passos que se seguem, vamos assumir que o SID desse sistema HANA é "PRD", e que você tem um sistema de não produção em funcionamento em HANA Grandes Instâncias na região DR Azure. Para este último, vamos supor que o seu SID é "TST". A imagem seguinte mostra esta configuração:

![Início da configuração de DR](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Se a instância do servidor ainda não tiver sido encomendada com o conjunto adicional de volume de armazenamento, o SAP HANA na Azure Service Management anexa o conjunto adicional de volumes como alvo para a réplica de produção da unidade HANA Large Instance em que está a executar a TST Caso HANA. Para o efeito, é necessário fornecer o SID da sua produção HANA. Depois de o SAP HANA na Azure Service Management confirmar a fixação desses volumes, é necessário montar esses volumes na unidade HANA Large Instance.

![Dr configurar próximo passo](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

O próximo passo é instalar a segunda instância SAP HANA na unidade HANA Large Instance na região DR Azure, onde executa a instância TST HANA. A instância Recém-instalada SAP HANA precisa de ter o mesmo SID. Os utilizadores criados precisam de ter o mesmo UID e id de grupo que a instância de produção tem. Leia [backup e restaure](hana-backup-restore.md) para mais detalhes. Se a instalação tiver sucesso, é necessário:

- Execute o passo 2 da preparação instantânea de armazenamento descrita em [Backup e restaurar](hana-backup-restore.md).
- Crie uma chave pública para a unidade DR da unidade HANA Large Instance se ainda não o tiver feito. Consulte o passo 3 da preparação instantânea de armazenamento descrita em [Backup e restaurar](hana-backup-restore.md).
- Mantenha o *HANABackupCustomerDetails.txt* com a nova instância HANA e teste se a conectividade no armazenamento funciona corretamente.  
- Pare a recém-instalada instância SAP HANA na unidade HANA Large Instance na região DR Azure.
- Desmonte estes volumes de PRD e contacte a SAP HANA na Azure Service Management. Os volumes não podem ficar montados na unidade porque não podem ser acessíveis enquanto funcionam como alvo de replicação de armazenamento.  

![Passo de configuração DR antes de estabelecer a replicação](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

A equipa de operações estabelece a relação de replicação entre os volumes de PRD na produção da região azure e os volumes prd na região DR Azure.

>[!IMPORTANT]
>O volume de /hana/log não é replicado porque não é necessário restaurar a base de dados SAP HANA replicada para um estado consistente no local de recuperação de desastres.

Em seguida, configurar ou ajustar o calendário de cópia de segurança do instantâneo de armazenamento para chegar ao seu RTO e RPO no caso do desastre. Para minimizar o objetivo do ponto de recuperação, detete os seguintes intervalos de replicação no serviço HANA Large Instance:
- Para os volumes cobertos pelo instantâneo combinado (tipo instantâneo **hana),** definido para replicar a cada 15 minutos os alvos de volume de armazenamento equivalentes no local de recuperação de desastres.
- Para o volume de cópia de segurança do registo de transações **(registos**de tipo instantâneo), decidiu replicar a cada 3 minutos os objetivos equivalentes de volume de armazenamento no local de recuperação de desastres.

Para minimizar o objetivo do ponto de recuperação, configurar o seguinte:
- Execute uma foto de armazenamento do tipo **Hana** (ver "Passo 7: Executar instantâneos") a cada 30 minutos a 1 hora.
- Efetue cópias de segurança de registo de transações SAP HANA a cada 5 minutos.
- Efetue uma foto de armazenamento do tipo **logs** a cada 5-15 minutos. Com este intervalo, obtém-se um RPO de cerca de 15-25 minutos.

Com esta configuração, a sequência de cópias de segurança de registo de transações, instantâneos de armazenamento e a replicação do volume de cópia de segurança de registo de transações HANA e /hana/data, e /hana/shared (inclui /usr/seap) podem parecer os dados mostrados neste gráfico:

 ![Relação entre um instantâneo de backup de registo de transações e um espelho instantâneo num eixo temporal](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Para obter um RPO ainda melhor no caso de recuperação de desastres, pode copiar os backups de registo de transações HANA da SAP HANA em Azure (Grandes Instâncias) para a outra região de Azure. Para conseguir esta nova redução de RPO, execute os seguintes passos:

1. Faça o backup do registo de transações HANA com a maior frequência possível para /hana/logbackups.
1. Utilize rsync para copiar as cópias de registo de transações para as máquinas virtuais Azure hospedadas em ações NFS. Os VMs estão em redes virtuais Azure na região de produção do Azure e nas regiões DR. É necessário ligar ambas as redes virtuais Azure ao circuito que liga a produção HANA Large Instances ao Azure. Consulte os gráficos na Rede para a recuperação de desastres com a secção [HANA Large Instances.](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) 
1. Mantenha as cópias de segurança do registo de transações na região do VM anexadas ao armazenamento exportado da NFS.
1. Num caso de falha de desastre, complemente as cópias de segurança do registo de transações que encontrar no volume de backups /hana/logbackups com cópias de segurança de registo de transações mais recentemente tomadas na parte NFS no site de recuperação de desastres. 
1. Inicie uma cópia de segurança de registo de transações para restaurar a mais recente cópia de segurança que poderá ser guardada para a região de DR.

Quando as operações da HANA Large Instance confirmam a configuração da relação de replicação e inicia as cópias de segurança do armazenamento de armazenamento de execução, a replicação de dados começa.

![Passo de configuração DR antes de estabelecer a replicação](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

À medida que a replicação progride, os instantâneos dos volumes de PRD nas regiões DR Azure não são restaurados. Só estão armazenadas. Se os volumes forem montados em tal estado, representam o estado em que desmontou esses volumes após a instalação da instância PRD SAP HANA na unidade de servidores da região DR Azure. Também representam as cópias de segurança que ainda não foram restauradas.

Se houver uma falha, também pode optar por restaurar um instantâneo de armazenamento mais antigo em vez do mais recente instantâneo de armazenamento.

## <a name="next-steps"></a>Passos seguintes

- Consulte o procedimento de falha de recuperação de [desastres](hana-failover-procedure.md).
