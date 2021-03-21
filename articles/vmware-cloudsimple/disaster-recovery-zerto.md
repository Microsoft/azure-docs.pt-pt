---
title: Azure VMware Solution by CloudSimple - Use a Nuvem Privada como local de desastre para cargas de trabalho no local
description: Descreve como configurar a cloud private cloud como um local de recuperação de desastres para cargas de trabalho VMware no local
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5dd1f157984cdb300cc3b375d71654e5da54d1ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898135"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Configurar cloudSimple Private Cloud como um local de recuperação de desastres para cargas de trabalho VMware no local

A cloudsimple Cloud private Cloud pode ser configurada como um local de recuperação para aplicações no local para fornecer continuidade de negócios em caso de desastre. A solução de recuperação baseia-se na Replicação Virtual Zerto como plataforma de replicação e orquestração. As máquinas virtuais de infraestrutura e aplicação críticas podem ser replicadas continuamente desde o seu vCenter no local até à sua Nuvem Privada. Pode utilizar a sua Cloud Privada para testes de failover e para garantir a disponibilidade da sua aplicação durante um desastre. Uma abordagem semelhante pode ser seguida para configurar a Nuvem Privada como um local primário que é protegido por um local de recuperação em um local diferente.

> [!NOTE]
> Consulte o documento Zerto [Sizing Considerations For Zerto Virtual Replication](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) para obter orientações sobre o dimensionamento do seu ambiente de recuperação de desastres.

A solução CloudSimple:

* Elimina a necessidade de configurar um datacenter especificamente para a recuperação de desastres (DR).
* Permite-lhe aproveitar as localizações do Azure onde o CloudSimple é implantado para resiliência geográfica mundial.
* Dá-lhe a opção de reduzir os custos de implantação e o custo total de propriedade para DR.

A solução requer que:

* Instale, configuure e gere o Zerto na sua Nuvem Privada.
* Forneça as suas próprias licenças para Zerto quando a Nuvem Privada for o site protegido. Você pode emparelhar Zerto correndo no site CloudSimple com o seu site no local para licenciamento.

A figura a seguir mostra a arquitetura para a solução Zerto.

![Arquitetura](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Como implementar a solução

As secções seguintes descrevem como implementar uma solução DR utilizando a replicação virtual Zerto na sua Nuvem Privada.

1. [Pré-requisitos](#prerequisites)
2. [Configuração opcional na CloudSimple Private Cloud](#optional-configuration-on-your-private-cloud)
3. [Configurar ZVM e VRA na CloudSimple Private Cloud](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Criar grupo de proteção virtual Zerto](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Pré-requisitos

Para ativar a replicação virtual Zerto do seu ambiente no local para a sua Nuvem Privada, complete os seguintes pré-requisitos.

1. [Configurar uma ligação VPN site-to-site entre a sua rede no local e a cloudSimple Private Cloud.](set-up-vpn.md)
2. [Configurar o lookup DNS para que os seus componentes de gestão Private Cloud sejam encaminhados para servidores DNS private Cloud](on-premises-dns-setup.md).  Para ativar o encaminhamento do look up DNS, crie uma entrada de zona de encaminhamento no servidor DNS no local `*.cloudsimple.io` para servidores DNS nSimples.
3. Configurar a procura de DNS para que os componentes vCenter no local sejam encaminhados para servidores DNS no local.  Os servidores DNS devem ser alcançáveis a partir da cloudSimple Private Cloud sobre a VPN site-to-site. Para assistência, apresente um pedido de [apoio,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)fornecendo as seguintes informações.  

    * Nome de domínio DNS nas instalações
    * Endereços IP do servidor DNS nas instalações

4. Instale um servidor Windows na sua Cloud Privada. O servidor é utilizado para instalar o Gestor Virtual Zerto.
5. [Aumente os seus privilégios CloudSimple](escalate-private-cloud-privileges.md).
6. Crie um novo utilizador no seu VCenter Private Cloud com a função administrativa a utilizar como conta de serviço para o Zerto Virtual Manager.

### <a name="optional-configuration-on-your-private-cloud"></a>Configuração opcional na sua Nuvem Privada

1. Crie um ou mais conjuntos de recursos no seu VCenter Private Cloud para usar como conjuntos de recursos-alvo para VMs a partir do seu ambiente no local.
2. Crie uma ou mais pastas no seu VCenter Private Cloud para utilizar como pastas-alvo para VMs a partir do seu ambiente no local.
3. Crie VLANs para a rede de falha e crie regras de firewall. Abra um pedido de [apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para assistência.
4. Criar grupos portuários distribuídos para rede de failover e rede de teste para testes de failover de VMs.
5. Instale [servidores DHCP e DNS](dns-dhcp-setup.md) ou utilize um controlador de domínio ative Directory no seu ambiente Private Cloud.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>Configurar ZVM e VRA na sua Nuvem Privada

1. Instale o Zerto Virtual Manager (ZVM) no servidor Windows na sua Cloud Privada.
2. Inscreva-se na ZVM utilizando a conta de serviço criada em etapas anteriores.
3. Configurar o licenciamento para o Gestor Virtual Zerto.
4. Instale o aparelho de replicação virtual Zerto (VRA) nos anfitriões ESXi da sua Nuvem Privada.
5. Emparelhe o seu ZVM cloud privado com o seu ZVM no local.

### <a name="set-up-zerto-virtual-protection-group"></a>Criar grupo de proteção virtual Zerto

1. Crie um novo Grupo de Proteção Virtual (VPG) e especifique a prioridade para o VPG.
2. Selecione as máquinas virtuais que requerem proteção para a continuidade do negócio e personalize a ordem de arranque se necessário.
3. Selecione o site de recuperação como a sua Nuvem Privada e o servidor de recuperação predefinido como o cluster Private Cloud ou o grupo de recursos que criou. Selecione **vsanDatastore** para a loja de dados de recuperação na sua Nuvem Privada.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Pode personalizar a opção de anfitrião para VMs individuais sob a opção VM Settings.

4. Personalize as opções de armazenamento conforme necessário.
5. Especifique as redes de recuperação para utilizar para a rede de failover e rede de teste de failover como os grupos portuários distribuídos criados anteriormente e personalize os scripts de recuperação conforme necessário.
6. Personalize as definições de rede para VMs individuais se necessário e crie o VPG.
7. Teste falha quando a replicação terminar.

## <a name="reference"></a>Referência

[Documentação Zerto](https://www.zerto.com/myzerto/technical-documentation/)
