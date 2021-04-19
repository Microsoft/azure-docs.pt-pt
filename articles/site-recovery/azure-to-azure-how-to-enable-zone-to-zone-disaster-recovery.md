---
title: Ativar a zona de recuperação de desastres para máquinas virtuais Azure
description: Este artigo descreve quando e como usar Zone to Zone Disaster Recovery for Azure virtual machines.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 786d877328b1ab3d0f03a75604b7345dba14aa9d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713413"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>Permitir a recuperação de desastres em Azure VM entre zonas de disponibilidade

Este artigo descreve como replicar, falhar e falhar as máquinas virtuais Azure de uma Zona de Disponibilidade para outra, dentro da mesma região do Azure.

>[!NOTE]
>
>- Atualmente, o apoio à recuperação de desastres zona para zona está limitado às seguintes regiões: Sudeste Asiático, Japão Oriental, Austrália Oriental, JIO Índia Oeste, Reino Unido Sul, Europa Ocidental, Europa do Norte, EUA Centrais, Leste dos EUA, Leste dos EUA 2 e Eua Ocidental 2.  
>- A Recuperação do Site não move nem armazena os dados dos clientes para fora da região em que é implantado quando o cliente está a utilizar a Zone to Zone Disaster Recovery. Os clientes podem selecionar um Cofre de Serviços de Recuperação de uma região diferente, se assim o desejarem. O Cofre de Serviços de Recuperação contém metadados, mas nenhum dado real do cliente.

O serviço de recuperação de sites contribui para a sua estratégia de continuidade e recuperação de desastres, mantendo as suas aplicações de negócio em funcionamento, durante as paragens planeadas e não planeadas. É a opção recomendada de recuperação de desastres para manter as suas aplicações em funcionamento se houver paragens regionais.

As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona tem um ou mais centros de dados. 

Se quiser mover VMs para uma zona de disponibilidade numa região diferente, [reveja este artigo.](../resource-mover/move-region-availability-zone.md)

## <a name="using-availability-zones-for-disaster-recovery"></a>Utilização de zonas de disponibilidade para recuperação de desastres 

Normalmente, as Zonas de Disponibilidade são usadas para implantar VMs numa configuração de alta disponibilidade. Podem estar demasiado próximos uns dos outros para servirem de solução de recuperação de desastres em caso de desastre natural.

No entanto, em alguns cenários, as Zonas de Disponibilidade podem ser alavancadas para a recuperação de desastres:

- Muitos clientes que tinham uma estratégia de recuperação de desastres do metro enquanto hospedam aplicações no local às vezes procuram imitar esta estratégia assim que migram para a Azure. Estes clientes reconhecem que a estratégia de recuperação de desastres do metro pode não funcionar em caso de catástrofe física em larga escala e aceitar este risco. Para estes clientes, a Recuperação de Desastres zona para zona pode ser usada como uma opção de recuperação de desastres.

- Muitos outros clientes têm infraestruturas de networking complicadas e não querem recriá-la numa região secundária devido ao custo e complexidade associados. Zone to Zone Disaster Recovery reduz a complexidade, uma vez que alavanca conceitos de networking redundantes em zonas de disponibilidade tornando a configuração muito mais simples. Estes clientes preferem a simplicidade e também podem usar Zonas de Disponibilidade para recuperação de desastres.

- Em algumas regiões que não têm uma região emparelhada dentro da mesma jurisdição legal (por exemplo, Sudeste Asiático), a Zona para a Recuperação de Desastres pode servir como a solução de recuperação de desastres de facto, uma vez que ajuda a garantir o cumprimento legal, uma vez que as suas aplicações e dados não se movem além das fronteiras nacionais. 

- Zone to Zone Disaster Recovery implica a replicação de dados em distâncias mais curtas quando comparado com Azure para Azure Disaster Recovery e, portanto, você pode ver menor latência e consequentemente rPO mais baixo.

Embora estas sejam fortes vantagens, existe a possibilidade de que a Zona para a Recuperação de Desastres de Zona possa ficar aquém dos requisitos de resiliência em caso de catástrofe natural em toda a região.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Rede de zona para recuperação de desastres de zona

Como mencionado acima, Zone to Zone Disaster Recovery reduz a complexidade, uma vez que alavanca conceitos de networking redundantes em zonas de disponibilidade tornando a configuração muito mais simples. O comportamento dos componentes de networking no cenário de recuperação de desastres de zona para zona é descrito abaixo: 

- Rede Virtual: Pode utilizar a mesma rede virtual que a rede de origem para falhas reais. Utilize uma rede virtual diferente para a rede virtual de origem para falhas de teste.

- Sub-rede: A falha na mesma sub-rede é suportada.

- Endereço IP privado: Se estiver a utilizar endereços IP estáticos, pode utilizar os mesmos IPs na zona-alvo se optar por configurá-los dessa forma.

- Rede Acelerada: Semelhante ao Azure à Recuperação de Desastres do Azure, pode ativar a rede acelerada se o VM SKU o suportar.

- Endereço IP público: Pode anexar um endereço IP público padrão previamente criado na mesma região ao VM alvo. Os endereços IP públicos básicos não suportam cenários relacionados com a Zona de Disponibilidade.

- Balanceador de carga: O balanceador de carga padrão é um recurso regional e, portanto, o VM-alvo pode ser ligado ao pool de backend do mesmo equilibrador de carga. Não é necessário um novo equilibrador de carga.

- Grupo de Segurança de Rede: Pode utilizar os mesmos grupos de segurança de rede que são aplicados à fonte VM.

## <a name="pre-requisites"></a>Pré-requisitos

Antes de implementar a Zona para a Recuperação de Desastres para os seus VMs, é importante garantir que outras funcionalidades ativadas no VM são interoperáveis com a zona para a recuperação de desastres de zona.

|Funcionalidade  | Declaração de apoio  |
|---------|---------|
|VMs clássicas   |     Não suportado    |
|ARM VMs    |    Suportado    |
|Azure Disk Encryption v1 (passe duplo, com Diretório Ativo Azure (Azure AD))     |     Suportado   |
|Azure Disk Encryption v2 (passe único, sem Azure AD)    |    Suportado    |
|Discos não geridos    |    Não suportado    |
|Managed disks    |    Suportado    |
|Chaves geridas pelo cliente    |    Suportado    |
|Grupos de colocação de proximidade    |    Suportado    |
|Interoperabilidade de backup    |    A cópia de segurança do nível de ficheiro e a restauração são suportadas. Backup de nível de disco e VM e restauro e não suportado.    |
|Adicionar/remover quente    |    Os discos podem ser adicionados depois de permitirem a replicação da zona. A remoção dos discos após permitir a replicação da zona de zona não é suportada.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Criar zona de recuperação do local para recuperação de desastres de zona

### <a name="log-in"></a>Iniciar sessão

Inicie sessão no portal do Azure.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Ativar a replicação para a máquina virtual zonal Azure

1. No menu do portal Azure, selecione máquinas Virtuais ou procure e selecione máquinas Virtuais em qualquer página. Selecione o VM que pretende replicar. Para a zona de recuperação de desastres, este VM já deve estar em zona de disponibilidade.

2. Em Operações, selecione Recuperação após desastre.

3. Como mostrado abaixo, no separador Básico, selecione 'Sim' para 'Recuperação de Desastres entre Zonas de Disponibilidade?'

    ![Página de Definições Básicas](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Se aceitar todos os incumprimentos, clique em 'Rever + Iniciar a replicação' seguido de 'Iniciar a replicação'.

5. Se pretender fazer alterações nas definições de replicação, clique em 'Seguinte: Definições avançadas'.

6. Altere as definições para longe do padrão sempre que apropriado. Para os utilizadores de Azure to Azure Disaster Recovery, esta página pode parecer familiar. Mais detalhes sobre as opções apresentadas nesta lâmina podem ser [consultados aqui](./azure-to-azure-tutorial-enable-replication.md)

    ![Página de Definições Avançadas](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Clique em 'Seguinte: Rever + Iniciar a replicação' e, em seguida, 'Iniciar a replicação'.

## <a name="faqs"></a>FAQs

**1. Como funciona o preço da Zona para a Recuperação de Desastres de Zona?**
Os preços da zona para a recuperação de desastres da zona são idênticos ao preço do Azure para a recuperação de desastres de Azure. Pode encontrar mais detalhes na página de preços [aqui](https://azure.microsoft.com/pricing/details/site-recovery/) e [aqui.](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) Note que as taxas de saída que veria na zona para a recuperação de desastres de zona seriam inferiores à recuperação de desastres da região para a região.

**2. O que é o SLA para RTO e RPO?**
O RTO SLA é o mesmo que para a Recuperação do Local em geral. Prometemos RTO de até 2 horas. Não existe um SLA definido para a RPO.

**3. A capacidade está garantida na zona secundária?**
A equipa de recuperação do local e o plano da equipa de gestão de capacidades da Azure para capacidade de infraestrutura suficiente. Quando inicia uma falha, as equipas também ajudam a garantir que as instâncias VM protegidas pela Recuperação do Local serão implantadas na zona alvo.

**4. Que sistemas operativos são suportados?**
Zona para zona Recuperação de Desastres suporta os mesmos sistemas operativos que Azure para Azure Disaster Recovery. Consulte aqui a [matriz](./azure-to-azure-support-matrix.md)de suporte.

**5. Os grupos de recursos de origem e de destino podem ser os mesmos?**
Não, tens de falhar com um grupo de recursos diferente.

## <a name="next-steps"></a>Passos seguintes

Os passos que precisam de ser seguidos para executar um exercício de recuperação de desastres, falhar, re-proteger e falhar são os mesmos que os passos em Azure para O cenário de Recuperação de Desastres Azure.

Para realizar um exercício de recuperação de desastres, siga os passos [aqui](./azure-to-azure-tutorial-dr-drill.md)descritos.

Para realizar um Failover e reprotectR os VMs na zona secundária, siga os passos [aqui](./azure-to-azure-tutorial-failover-failback.md)descritos.

Para não responder à zona primária, siga os passos [aqui](./azure-to-azure-tutorial-failback.md)descritos.
