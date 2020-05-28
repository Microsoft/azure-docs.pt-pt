---
title: Ativar a recuperação de desastres da zona para máquinas virtuais azure
description: Este artigo descreve quando e como usar A Recuperação de Desastres da Zona para máquinas virtuais Azure.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 367ca77b0f3a142d8aa36143052993d1ed96f052
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996504"
---
# <a name="enable-zone-to-zone-disaster-recovery-for-azure-virtual-machines"></a>Ativar a recuperação de desastres da zona para máquinas virtuais Azure

Este artigo descreve como replicar, falhar e falhar máquinas virtuais Azure de uma Zona de Disponibilidade para outra dentro da mesma região de Azure.

>[!NOTE]
>
>- Atualmente, a Recuperação do Local não suporta planos de recuperação de zonas para a recuperação de desastres de zona. 
>- O apoio à recuperação de desastres da Zona para a Zona está atualmente limitado a duas regiões: Sudeste Asiático e Reino Unido Sul.  

O serviço de recuperação do site contribui para a continuidade do seu negócio e estratégia de recuperação de desastres, mantendo as suas aplicações de negócio em funcionamento, durante as interrupções planeadas e não planeadas. É a opção recomendada de recuperação de desastres para manter as suas aplicações em funcionamento se houver interrupções regionais.

As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona tem um ou mais datacenters. 

## <a name="using-availability-zones-for-disaster-recovery"></a>Utilização de Zonas de Disponibilidade para Recuperação de Desastres 

Normalmente, as Zonas de Disponibilidade são usadas para implementar VMs numa configuração de alta disponibilidade. Podem estar demasiado próximos um do outro para servirem de solução de recuperação de desastres em caso de catástrofe natural.

No entanto, em alguns cenários, as Zonas de Disponibilidade podem ser alavancadas para a Recuperação de Desastres:

- Muitos clientes que tinham uma estratégia de recuperação de desastres do metro enquanto hospedavam aplicações no local, por vezes procuram imitar esta estratégia uma vez que migram aplicações para O Azure. Estes clientes reconhecem que a estratégia de recuperação de desastres do metro pode não funcionar em caso de uma catástrofe física em larga escala e aceitar este risco. Para tais clientes, a Recuperação de Desastres da Zona para A Zona pode ser usada como uma opção de recuperação de desastres.

- Muitos outros clientes têm infraestruturas de networking complicadas e não querem recriá-la numa região secundária devido ao custo e complexidade associados. A recuperação de desastres de zona para zona reduz a complexidade, uma vez que aproveita conceitos de networking redundantes em zonas de disponibilidade tornando a configuração muito mais simples. Estes clientes preferem a simplicidade e também podem usar Zonas de Disponibilidade para Recuperação de Desastres.

- Em algumas regiões que não têm uma região emparelhada dentro da mesma jurisdição legal (por exemplo, sudeste asiático), a recuperação de desastres da Zona para a Zona pode servir como a solução de recuperação de desastres de facto, uma vez que ajuda a garantir o cumprimento legal, uma vez que as suas aplicações e dados não atravessam as fronteiras nacionais. 

- A recuperação de desastres de zona para zona implica a replicação de dados em distâncias mais curtas quando comparada com azure para Azure Disaster Recovery e, portanto, você pode ver menor latência e consequentemente RPO mais baixo.

Embora estas sejam fortes vantagens, existe a possibilidade de que a recuperação de desastres da Zona para a Zona possa ficar aquém dos requisitos de resiliência em caso de desastre natural em toda a região.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Networking para A Recuperação de Desastres de Zona para Zona

Como mencionado acima, a recuperação de desastres da Zona para a Zona reduz a complexidade, uma vez que aproveita conceitos de networking redundantes em zonas de disponibilidade tornando a configuração muito mais simples. O comportamento dos componentes de networking no cenário de recuperação de desastres da Zona para a Zona é descrito abaixo: 

- Rede Virtual: Pode utilizar a mesma rede virtual que a rede de origem para falhas reais. Utilize uma rede virtual diferente para a rede virtual de origem para falhas nos testes.

- Subnet: A falha na mesma sub-rede é suportada.

- Endereço IP privado: Se estiver a utilizar endereços IP estáticos, pode utilizar os mesmos IP na zona-alvo se optar por configurá-los de tal forma.

- Networking Acelerado: Semelhante ao Azure to Azure Disaster Recovery, pode ativar a Rede Acelerada se o VM SKU o apoiar.

- Endereço IP público: Pode anexar um endereço IP público padrão previamente criado na mesma região ao VM alvo. Os endereços IP públicos básicos não suportam cenários relacionados com a Zona de Disponibilidade.

- Balanceador de carga: O equilíbrio de carga padrão é um recurso regional e, portanto, o VM-alvo pode ser ligado ao conjunto de backend do mesmo equilibrista de carga. Não é necessário um novo equilibrador de carga.

- Grupo de Segurança da Rede: Pode utilizar os mesmos grupos de segurança da rede aplicados à Fonte VM.

## <a name="pre-requisites"></a>Pré-requisitos

Antes de implantar a Recuperação de Desastres da Zona para a Zona para os seus VMs, é importante garantir que outras funcionalidades ativadas no VM são interoperáveis com a recuperação de desastres de zona para zona.

|Funcionalidade  | Declaração de apoio  |
|---------|---------|
|VMs clássicas   |     Não suportado    |
|ARM VMs    |    Suportado    |
|Encriptação de disco azure v1 (passe duplo, com AAD)     |     Suportado |
|Encriptação de disco azure v2 (passe único, sem AAD)    |    Suportado    |
|Discos não geridos    |    Não suportado    |
|Managed disks    |    Suportado    |
|Chaves geridas pelo cliente    |    Suportado    |
|Grupos de colocação de proximidade    |    Suportado    |
|Interoperabilidade de backup    |    A cópia de segurança e restauro do nível de ficheiro são suportadas. Backup de nível de disco e VM e restauro e não suportado.    |
|Adicionar/remover quente    |    Os discos podem ser adicionados após a replicação da zona. A remoção dos discos após a replicação da zona de terminação não é suportada.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Configurar a zona de recuperação do local para a recuperação de desastres de zona

### <a name="log-in"></a>Iniciar sessão

Inicie sessão no portal Azure.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Ativar a replicação para a máquina virtual zonal Azure

1. No menu do portal Azure, selecione máquinas Virtuais ou procure e selecione máquinas Virtuais em qualquer página. Selecione o VM que pretende replicar. Para a recuperação de desastres de zona para zona, este VM já deve estar em uma zona de disponibilidade.

2. Em Operações, selecione Recuperação após desastre.

3. Como mostrado abaixo, no separador Basics, selecione 'Sim' para 'Recuperação de Desastres entre Zonas de Disponibilidade?'

    ![Página de Definições Básicas](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Se aceitar todas as predefinições, clique em 'Rever + Iniciar a replicação' seguido de 'Iniciar a replicação'.

5. Se pretender ealterar as definições de replicação, clique em 'Next: Advanced settings'.

6. Mude as definições para longe do padrão sempre que apropriado. Para os utilizadores de Azure to Azure Disaster Recovery, esta página pode parecer familiar. Mais detalhes sobre as opções apresentadas nesta lâmina podem ser encontrados [aqui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)

    ![Página de Definições Avançadas](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Clique em 'Next: Review + Iniciar a replicação' e depois 'Iniciar a replicação'.

## <a name="faqs"></a>FAQs

**1. Como funciona o preço da recuperação de desastres da Zona para a Zona?**
Os preços da recuperação de desastres de zona para zona são idênticos aos preços de Azure to Azure Disaster Recovery. Pode encontrar mais detalhes na página de preços [aqui](https://azure.microsoft.com/pricing/details/site-recovery/) e [aqui.](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) Note-se que as taxas de saída que se veria na zona de recuperação de desastres seriam inferiores à recuperação de desastres da região.

**2. O que é o SLA para RTO e RPO?**
O RTO SLA é o mesmo que para a Recuperação do Site em geral. Prometemos RTO até 2 horas. Não existe SLA definido para RPO.

**3. A capacidade está garantida na zona secundária?**
A equipa de recuperação do site e o plano de equipa de gestão de capacidade seletiva Azure para uma capacidade de infraestrutura suficiente. Quando inicia uma falha, as equipas também ajudam a garantir que os casos de VM protegidos pela Recuperação do Local serão implantados para a zona alvo.

**4. Quais os sistemas operativos suportados?**
A Recuperação de Desastres da Zona para A Zona suporta os mesmos sistemas operativos que o Azure to Azure Disaster Recovery. Consulte a matriz de suporte [aqui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

**5. Os grupos de recursos de origem e de recursos-alvo podem ser os mesmos?**
Não, tens de falhar com um grupo de recursos diferente.

## <a name="next-steps"></a>Próximos passos

Os passos que precisam de ser seguidos para executar um exercício de recuperação de desastres, falhar, reproteger e falhar são os mesmos que os passos no cenário de recuperação de desastres de Azure para Azure.

Para realizar um exercício de recuperação de desastres, siga os passos [aqui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-dr-drill)descritos .

Para executar uma falha e reproteger os VMs na zona secundária, siga os passos [aqui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)descritos .

Para não voltar à zona primária, siga os passos [aqui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failback)descritos.
