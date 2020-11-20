---
title: Visão geral de um cenário de recuperação de desastres da Oracle no seu ambiente Azure Microsoft Docs
description: Um cenário de recuperação de desastres para uma base de dados Oracle Database 12c no seu ambiente Azure
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: b8da0b5c55b291af42d9a30db23d6f55f7c0bf2d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965311"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Recuperação de desastres para uma base de dados Oracle Database 12c em ambiente Azure

## <a name="assumptions"></a>Pressupostos

- Você tem uma compreensão do design da Oracle Data Guard e ambientes Azure.


## <a name="goals"></a>Objetivos
- Desenhe a topologia e configuração que satisfaçam os requisitos de recuperação de desastres (DR).

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Cenário 1: Sítios primários e DR em Azure

Um cliente tem uma base de dados oracle configurada no site principal. Um site dr está em uma região diferente. O cliente utiliza a Oracle Data Guard para uma rápida recuperação entre estes sites. O site primário também tem uma base de dados secundária para relatórios e outros usos. 

### <a name="topology"></a>Topologia

Aqui está um resumo da configuração do Azure:

- Dois locais (um site primário e um site DR)
- Duas redes virtuais
- Duas bases de dados da Oracle com Data Guard (primário e em espera)
- Duas bases de dados Oráculos com Golden Gate ou Data Guard (apenas no site primário)
- Dois serviços de candidatura, um primário e outro no site DR
- Um *conjunto de disponibilidade,* que é usado para o serviço de base de dados e aplicação no site primário
- Uma caixa de salto em cada site, que restringe o acesso à rede privada e só permite a entrada por um administrador
- Uma caixa de salto, serviço de aplicações, base de dados e gateway VPN em sub-redes separadas
- NSG aplicada nas sub-redes de aplicação e de base de dados

![Diagrama que mostra sites primários e DR em Azure .](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Cenário 2: Local primário no local e local de DR em Azure

Um cliente tem uma configuração da base de dados Oracle no local (site primário). Um site dr está em Azure. A Oracle Data Guard é usada para uma rápida recuperação entre estes sites. O site primário também tem uma base de dados secundária para relatórios e outros usos. 

Há duas abordagens para esta configuração.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Abordagem 1: Ligações diretas entre as instalações e o Azure, que exigem portas TCP abertas na firewall 

Não recomendamos ligações diretas porque expõem as portas TCP ao mundo exterior.

#### <a name="topology"></a>Topologia

Segue-se um resumo da configuração do Azure:

- Um site DR 
- Uma rede virtual
- Uma base de dados da Oracle com Data Guard (ativa)
- Um serviço de aplicação no site DR
- Uma caixa de salto, que restringe o acesso à rede privada e só permite a entrada por um administrador
- Uma caixa de salto, serviço de aplicações, base de dados e gateway VPN em sub-redes separadas
- NSG aplicada nas sub-redes de aplicação e de base de dados
- Uma política/regra NSG para permitir a entrada da porta TCP 1521 (ou uma porta definida pelo utilizador)
- Uma política/regra NSG para restringir apenas o endereço/endereços IP no local (DB ou aplicação) para aceder à rede virtual

![Diagrama que mostra ligações diretas entre as instalações e o Azure, que requer portas TCP abertas na firewall.](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Abordagem 2: VPN local-a-local
A VPN local é uma melhor abordagem. Para obter mais informações sobre a configuração de uma VPN, consulte [Criar uma rede virtual com uma ligação VPN site-to-site utilizando CLI](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md).

#### <a name="topology"></a>Topologia

Segue-se um resumo da configuração do Azure:

- Um site DR 
- Uma rede virtual 
- Uma base de dados da Oracle com Data Guard (ativa)
- Um serviço de aplicação no site DR
- Uma caixa de salto, que restringe o acesso à rede privada e só permite a entrada por um administrador
- Uma caixa de salto, serviço de aplicações, base de dados e gateway VPN estão em sub-redes separadas
- NSG aplicada nas sub-redes de aplicação e de base de dados
- Ligação VPN local entre as instalações e a Azure

![Screenshot da página de topologia dr](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Leitura adicional

- [Conceça e implemente uma base de dados da Oracle em Azure](oracle-design.md)
- [Configurar o Oracle Data Guard](configure-oracle-dataguard.md)
- [Configure Portão Dourado oráculo](configure-oracle-golden-gate.md)
- [Apoio e recuperação da Oráculo](./oracle-overview.md)


## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar VMs altamente disponíveis](../../linux/create-cli-complete.md)
- [Explore amostras de CLI de implantação VM](../../linux/cli-samples.md)
