---
title: Azure VMware Solutions (AVS) - Use a Nuvem Privada AVS como local de desastre para cargas de trabalho no local
description: Descreve como configurar a sua Nuvem Privada AVS como um local de recuperação de desastres para cargas de trabalho vMware no local
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5ee43af97e79f1e835787d61bd79cfb256ef445
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083145"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Configurar a AVS Private Cloud como local de recuperação de desastres para cargas de trabalho vMware no local

A sua Nuvem Privada AVS pode ser configurada como um local de recuperação para aplicações no local para fornecer continuidade ao negócio em caso de desastre. A solução de recuperação baseia-se na Replicação Virtual zerto como plataforma de replicação e orquestração. As máquinas virtuais de infraestrutura crítica e aplicação podem ser replicadas continuamente desde o seu vCenter no local até à sua Nuvem Privada AVS. Pode utilizar a sua Nuvem Privada AVS para testar falhas e garantir a disponibilidade da sua aplicação durante um desastre. Uma abordagem semelhante pode ser seguida para configurar a Nuvem Privada AVS como um local primário que é protegido por um local de recuperação em um local diferente.

> [!NOTE]
> Consulte o documento Zerto [dimensionando considerações para a replicação virtual zerto](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) para obter orientações sobre dimensionamento do seu ambiente de recuperação de desastres.

A solução AVS:

* Elimina a necessidade de criar um datacenter especificamente para a recuperação de desastres (DR).
* Permite-lhe aproveitar as localizações do Azure onde o AVS é implantado para resiliência geográfica mundial.
* Dá-lhe a opção de reduzir os custos de implantação e o custo total de propriedade para a DR.

A solução requer:

* Instale, configure e gere o Zerto na sua Nuvem Privada AVS.
* Forneça as suas próprias licenças para zerto quando a Nuvem Privada AVS for o site protegido. Você pode emparelhar Zerto correndo no site da AVS com o seu site no local de licenciamento.

A figura que se segue mostra a arquitetura para a solução Zerto.

![Arquitetura](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Como implementar a solução

As seguintes secções descrevem como implementar uma solução DR utilizando a Replicação Virtual Zerto na sua Nuvem Privada AVS.

1. [Pré-requisitos](#prerequisites)
2. [Configuração opcional na sua Nuvem Privada AVS](#optional-configuration-on-your-avs-private-cloud)
3. [Instale ZVM e VRA na sua Nuvem Privada AVS](#set-up-zvm-and-vra-on-your-avs-private-cloud)
4. [Criar o Grupo de Proteção Virtual Zerto](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Pré-requisitos

Para ativar a Replicação Virtual Zerto do seu ambiente no local até à sua Nuvem Privada AVS, complete os seguintes pré-requisitos.

1. [Instale uma ligação VPN site-to-site entre a sua rede no local e a sua Nuvem Privada AVS](set-up-vpn.md).
2. Configurar a procura dNS para que os seus componentes de [gestão de Cloud Privada Saqueados sejam encaminhados para servidores DNS DNS privados](on-premises-dns-setup.md)da Nuvem AVS . Para permitir o reencaminhamento da procura de DNS, crie uma entrada de zona de encaminhamento no seu servidor DNS no local para `*.cloudsimple.io` aos servidores DNS AVS.
3. Configurar a procura dNS de modo a que os componentes vCenter no local sejam encaminhados para servidores DNS no local. Os servidores DNS devem ser acessíveis a partir da sua Nuvem Privada AVS através do Site-to-Site VPN. Para assistência, apresente um pedido de [apoio,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)fornecendo as seguintes informações. 

    * No local, nome de domínio DNS
    * No local, os endereços IP do servidor DNS

4. Instale um servidor Windows na sua Nuvem Privada AVS. O servidor é utilizado para instalar o Zerto Virtual Manager.
5. [Aumente os seus privilégios AVS.](escalate-private-cloud-privileges.md)
6. Crie um novo utilizador no seu VCenter De Nuvem Privada AVS com a função administrativa a utilizar como conta de serviço para O Gestor Virtual Zerto.

### <a name="optional-configuration-on-your-avs-private-cloud"></a>Configuração opcional na sua Nuvem Privada AVS

1. Crie um ou mais conjuntos de recursos no seu VCenter De Nuvem Privada AVS para usar como conjuntos de recursos-alvo para VMs a partir do seu ambiente no local.
2. Crie uma ou mais pastas no seu VCenter De Nuvem Privada AVS para usar como pastas-alvo para VMs a partir do seu ambiente no local.
3. Crie VLANs para a rede failover e crie regras de firewall. Abra um pedido de [apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para assistência.
4. Crie grupos portuários distribuídos para rede de failover e rede de teste para testar falhas de VMs.
5. Instale [servidores DHCP e DNS](dns-dhcp-setup.md) ou utilize um controlador de domínio Ative Directory no seu ambiente AVS Private Cloud.

### <a name="set-up-zvm-and-vra-on-your-avs-private-cloud"></a>Instale ZVM e VRA na sua Nuvem Privada AVS

1. Instale o Zerto Virtual Manager (ZVM) no servidor Windows na sua Nuvem Privada AVS.
2. Inscreva-se na ZVM utilizando a conta de serviço criada em etapas anteriores.
3. Configurar o licenciamento para zerto Virtual Manager.
4. Instale o Aparelho de Replicação Virtual Zerto (VRA) nos anfitriões ESXi da sua Nuvem Privada AVS.
5. Emparelhe o seu AVS Private Cloud ZVM com o seu ZVM no local.

### <a name="set-up-zerto-virtual-protection-group"></a>Criar o Grupo de Proteção Virtual Zerto

1. Crie um novo Grupo de Proteção Virtual (VPG) e especifique a prioridade para o VPG.
2. Selecione as máquinas virtuais que requerem proteção para a continuidade do negócio e personalize a encomenda de arranque se necessário.
3. Selecione o site de recuperação como o seu AVS Private Cloud e o servidor de recuperação padrão como o cluster AVS Private Cloud ou o grupo de recursos que criou. Selecione **vsanDatastore** para a loja de dados de recuperação na sua Nuvem Privada AVS.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Pode personalizar a opção de anfitrião para VMs individuais sob a opção Definições VM.

4. Personalize as opções de armazenamento conforme necessário.
5. Especifique as redes de recuperação a utilizar para a rede failover e rede de teste failover como os grupos portuários distribuídos criados anteriormente e personalize os scripts de recuperação conforme necessário.
6. Personalize as definições de rede para VMs individuais, se necessário, e crie o VPG.
7. Teste falha quando a replicação terminar.

## <a name="reference"></a>Referência

[Documentação Zerto](https://www.zerto.com/myzerto/technical-documentation/)
