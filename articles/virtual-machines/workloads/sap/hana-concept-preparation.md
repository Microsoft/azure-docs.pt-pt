---
title: Princípios de recuperação de desastres e preparação no SAP HANA em Azure (Grandes Instâncias) Microsoft Docs
description: Princípios de recuperação de desastres e preparação sobre SAP HANA em Azure (Grandes Instâncias)
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "70101268"
---
# <a name="disaster-recovery-principles"></a>Princípios de recuperação de desastres

HANA Large Instances oferecem uma funcionalidade de recuperação de desastres entre selos HANA Large Instance em diferentes regiões de Azure. Por exemplo, se colocar unidades de grande instância HANA na região oeste dos EUA de Azure, você pode usar as unidades HANA Large Instance na região leste dos EUA como unidades de recuperação de desastres. Como mencionado anteriormente, a recuperação de desastres não é configurada automaticamente, porque requer que você pague por outra unidade HANA Large Instance na região DR. A configuração da recuperação de desastres funciona para a escala, bem como para configurações de escala. 

Nos cenários implementados até agora, os clientes usam a unidade na região DR para executar sistemas de não produção que utilizam uma instância HANA instalada. A unidade HANA Large Instance tem de ser do mesmo SKU utilizado para fins de produção. A imagem a seguir mostra como é a configuração do disco entre a unidade de servidor na região de produção do Azure e a região de recuperação de desastres:

![Configuração de configuração DR do ponto de vista do disco](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Como mostrado neste gráfico de visão geral, você precisa então encomendar um segundo conjunto de volumes de disco. Os volumes-alvo dos discos têm o mesmo tamanho que os volumes de produção para a produção nas unidades de recuperação de desastres. Estes volumes de disco estão associados à unidade de servidor HANA Large Instance no local de recuperação de desastres. Os seguintes volumes são replicados da região de produção para o local dr:

- /hana/dados
- /hana/logbackups 
- /hana/shared (inclui /usr/seiva)

O volume /hana/log não é replicado porque o registo de transações SAP HANA não é necessário na forma como a restauração desses volumes é feita. 

A base da funcionalidade de recuperação de desastres oferecida é a funcionalidade de replicação de armazenamento oferecida pela infraestrutura HANA Large Instance. A funcionalidade que é utilizada no lado do armazenamento não é um fluxo constante de alterações que se replicam de forma assíncronea à medida que as alterações acontecem no volume de armazenamento. Em vez disso, trata-se de um mecanismo que se baseia no facto de que os instantâneos destes volumes são criados regularmente. O delta entre um instantâneo já replicado e um novo instantâneo que ainda não foi replicado é então transferido para o local de recuperação de desastres em volumes de disco-alvo.  Estas imagens são armazenadas nos volumes e, se houver uma falha de recuperação de desastres, têm de ser restauradas nesses volumes.  

A primeira transferência dos dados completos do volume deve ser antes que a quantidade de dados se torne menor do que os deltas entre instantâneos. Como resultado, os volumes no site DR contêm cada um dos instantâneos de volume realizados no local de produção. Eventualmente, pode usar esse sistema DR para chegar a um estado anterior para recuperar dados perdidos, sem reverter o sistema de produção.

Se houver uma implantação mcOD com múltiplas instâncias SAP HANA independentes numa unidade de HANA Large Instance, espera-se que todas as instâncias SAP HANA estejam a ser replicadas para o lado DR.

Nos casos em que utiliza a replicação do sistema HANA como funcionalidade de alta disponibilidade no seu site de produção e utiliza a replicação baseada em armazenamento para o site DR, os volumes de ambos os nós do local primário para a instância DR são replicados. Deve adquirir armazenamento adicional (do mesmo tamanho que o nó primário) no local de DR para acomodar a replicação tanto primária como secundária ao DR. 



>[!NOTE]
>A funcionalidade de replicação de armazenamento HANA Large Instance está a espelhar e a replicar instantâneos de armazenamento. Se não efetuar instantâneos de armazenamento como introduzido na secção de backup e restauro deste artigo, não pode haver qualquer replicação no local de recuperação de desastres. A execução de instantâneo de armazenamento é um pré-requisito para armazenar a replicação para o local de recuperação de desastres.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Preparação do cenário de recuperação de desastres
Neste cenário, tem um sistema de produção em execução na HANA Large Instances na região de produção Azure. Para os passos que se seguem, vamos supor que o SID desse sistema HANA é "PRD", e que você tem um sistema de não produção em execução em HANA Large Instances na região dr Azure. Para este último, vamos supor que o seu SID é "TST". A imagem a seguir mostra esta configuração:

![Início da configuração de DR](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Se a instância do servidor ainda não tiver sido encomendada com o conjunto adicional de volume de armazenamento, o SAP HANA na Azure Service Management anexa o conjunto adicional de volumes como alvo para a réplica de produção à unidade HANA Large Instance em que está a executar a instância TST HANA. Para isso, você precisa fornecer o SID da sua produção HANA exemplo. Depois de o SAP HANA na Azure Service Management confirmar a fixação desses volumes, é necessário montar esses volumes na unidade HANA Large Instance.

![CONFIGURAÇÃO DR próximo passo](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

O próximo passo é instalar a segunda instância SAP HANA na unidade HANA Large Instance na região dr Azure, onde você dirige a instância TST HANA. A nova instância SAP HANA precisa de ter o mesmo SID. Os utilizadores criados precisam de ter o mesmo UID e ID do Grupo que a instância de produção tem. Leia [backup e restaure](hana-backup-restore.md) para mais detalhes. Se a instalação tiver sido bem sucedida, tem de:

- Executar o passo 2 da preparação do instantâneo de armazenamento descrito em [Backup e restaurar](hana-backup-restore.md).
- Crie uma chave pública para a unidade DR da unidade HANA Large Instance se ainda não o tiver feito. Consulte o passo 3 da preparação do instantâneo de armazenamento descrito na [Cópia de Segurança e restauro](hana-backup-restore.md).
- Mantenha o *HANABackupCustomerDetails.txt* com a nova instância HANA e teste se a conectividade no armazenamento funciona corretamente.  
- Pare a nova instância SAP HANA instalada na unidade HANA Large Instance na região dr Azure.
- Desmonte estes volumes de PRD e contacte a SAP HANA na Azure Service Management. Os volumes não podem permanecer montados na unidade porque não podem ser acessíveis enquanto funcionam como alvo de replicação de armazenamento.  

![Passo de configuração dr antes de estabelecer a replicação](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

A equipa de operações estabelece a relação de replicação entre os volumes de PRD na região de produção Azure e os volumes de PRD na região dr Azure.

>[!IMPORTANT]
>O volume /hana/log não é replicado porque não é necessário restaurar a base de dados SAP HANA replicada para um estado consistente no local de recuperação de desastres.

Em seguida, configurar ou ajustar o horário de backup do instantâneo de armazenamento para chegar ao seu RTO e RPO no caso do desastre. Para minimizar o objetivo do ponto de recuperação, desaponte os seguintes intervalos de replicação no serviço HANA Large Instance:
- Para os volumes cobertos pelo instantâneo combinado (snapshot type **hana),** definido para replicar a cada 15 minutos os alvos equivalentes do volume de armazenamento no local de recuperação de desastres.
- Para o volume de backup de registo de transações **(registos**de tipo snapshot), desaplicou a cada 3 minutos os alvos equivalentes do volume de armazenamento no local de recuperação de desastres.

Para minimizar o objetivo do ponto de recuperação, estabeleça o seguinte:
- Execute uma imagem de armazenamento do tipo **hana** (ver "Passo 7: Execute instantâneos") a cada 30 minutos a 1 hora.
- Execute cópias de registo de transações SAP HANA a cada 5 minutos.
- Execute uma foto de armazenamento de tipo **de troncos** a cada 5-15 minutos. Com este intervalo, obtém-se um RPO de cerca de 15-25 minutos.

Com esta configuração, a sequência de cópias de segurança do registo de transações, instantâneos de armazenamento e a replicação do volume de backup do registo de transações HANA e /hana/dados, e /hana/shared (inclui /usr/seiva) podem parecer os dados apresentados neste gráfico:

 ![Relação entre um instantâneo de backup de registo de transação e um espelho instantâneo em um eixo de tempo](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Para obter um RPO ainda melhor no caso de recuperação de desastres, você pode copiar os backups de registo de transações HANA da SAP HANA em Azure (Grandes Instâncias) para a outra região de Azure. Para conseguir esta redução adicional de RPO, execute os seguintes passos:

1. Faça o registo de transações HANA com a maior frequência possível para /hana/logbackups.
1. Utilize o rsync para copiar as cópias de segurança do registo de transações para as máquinas virtuais Azure, hospedadas em ações da NFS. Os VM estão em redes virtuais Azure na região de produção de Azure e nas regiões DR. É necessário ligar ambas as redes virtuais Azure ao circuito que liga a produção HANA Large Instances ao Azure. Consulte os gráficos na [Rede considerações para recuperação de desastres com](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) a secção HANA Large Instances. 
1. Mantenha os backups de registo de transações na região no VM anexado ao armazenamento exportado da NFS.
1. Num caso de falha de desastre, complemente as cópias de segurança do registo de transações que encontra no volume /hana/logbackups com cópias de segurança de registo de transações mais recentemente tomadas na parte NFS no site de recuperação de desastres. 
1. Inicie uma cópia de segurança do registo de transações para restaurar a mais recente cópia de segurança que pode ser guardada para a região dr.

Quando as operações da HANA Large Instance confirmarem a configuração da relação de replicação e iniciarem as cópias de segurança do armazenamento de execução, a replicação de dados começa.

![Passo de configuração dr antes de estabelecer a replicação](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

À medida que a replicação progride, os instantâneos dos volumes de PRD nas regiões dr Azure não são restaurados. Só estão armazenadas. Se os volumes forem montados em tal estado, representam o estado em que desmontou esses volumes após a instalação da instância PRD SAP HANA na unidade do servidor na região de DR Azure. Também representam as cópias de segurança de armazenamento que ainda não foram restauradas.

Se houver uma falha, também pode optar por restaurar para um instantâneo de armazenamento mais antigo em vez do último instantâneo de armazenamento.

## <a name="next-steps"></a>Passos seguintes

- Consulte [o procedimento de falha de recuperação de desastres](hana-failover-procedure.md).
