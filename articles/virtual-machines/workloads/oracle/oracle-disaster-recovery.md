---
title: Visão geral de um cenário de recuperação de desastres da Oráculo no seu ambiente Azure Microsoft Docs
description: Um cenário de recuperação de desastres para uma base de dados 12c da Oracle Database no seu ambiente Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: adf4c54d4f43f806ae985f7c1523303cf574b4c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687471"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Recuperação de desastres para uma base de dados 12c da Oracle Database num ambiente Azure

## <a name="assumptions"></a>Pressupostos

- Você tem uma compreensão do design da Guarda de Dados Oracle e ambientes Azure.


## <a name="goals"></a>Objetivos
- Desenhe a topologia e configuração que satisfaçam os requisitos de recuperação de desastres (DR).

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Cenário 1: Locais primários e DR em Azure

Um cliente tem uma base de dados da Oracle criada no site principal. Um site de DR está em outra região. O cliente utiliza a Oracle Data Guard para uma rápida recuperação entre estes sites. O site principal também tem uma base de dados secundária para reporte e outros usos. 

### <a name="topology"></a>Topologia

Aqui está um resumo da configuração Azure:

- Dois sites (um local primário e um site dr)
- Duas redes virtuais
- Duas bases de dados da Oracle com Guarda de Dados (primária e de prontidão)
- Duas bases de dados da Oracle com Golden Gate ou Data Guard (apenas no local principal)
- Dois serviços de candidatura, um primário e um no site da DR
- Um *conjunto de disponibilidade,* que é usado para base de dados e serviço de aplicação no site principal
- Uma caixa de salto em cada site, que restringe o acesso à rede privada e só permite o início por um administrador
- Uma caixa de salto, serviço de aplicação, base de dados e gateway VPN em subnets separadas
- NSG aplicada em subredes de aplicação e base de dados

![Screenshot da página de topologia DR](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Cenário 2: Local primário no local e dr no local do Azure

Um cliente tem uma configuração de base de dados Oracle no local (site principal). Um site de DR está no Azure. A Oracle Data Guard é usada para uma rápida recuperação entre estes sites. O site principal também tem uma base de dados secundária para reporte e outros usos. 

Há duas abordagens para esta configuração.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Abordagem 1: Ligações diretas entre as instalações e o Azure, que requerem portas TCP abertas na firewall 

Não recomendamos ligações diretas porque expõem as portas de TCP ao mundo exterior.

#### <a name="topology"></a>Topologia

Segue-se um resumo da configuração azure:

- Um site de DR 
- Uma rede virtual
- Uma base de dados da Oracle com Guarda de Dados (ativo)
- Um serviço de aplicação no site da DR
- Uma jumpbox, que restringe o acesso à rede privada e só permite o início por um administrador
- Uma caixa de salto, serviço de aplicação, base de dados e gateway VPN em subnets separadas
- NSG aplicada em subredes de aplicação e base de dados
- Uma política/regra NSG que permite a entrada da porta TCP 1521 (ou uma porta definida pelo utilizador)
- Uma política/regra NSG para restringir apenas o endereço/endereços IP no local (DB ou aplicação) para aceder à rede virtual

![Screenshot da página de topologia DR](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Abordagem 2: VPN site-to-site
O VPN do site-a-site é uma melhor abordagem. Para obter mais informações sobre a criação de uma VPN, consulte [Criar uma rede virtual com uma ligação VPN site-to-site utilizando cli](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>Topologia

Segue-se um resumo da configuração azure:

- Um site de DR 
- Uma rede virtual 
- Uma base de dados da Oracle com Guarda de Dados (ativo)
- Um serviço de aplicação no site da DR
- Uma jumpbox, que restringe o acesso à rede privada e só permite o início por um administrador
- Uma caixa de salto, serviço de aplicação, base de dados e gateway VPN estão em subnets separadas
- NSG aplicada em subredes de aplicação e base de dados
- Ligação VPN local-a-local entre as instalações e o Azure

![Screenshot da página de topologia DR](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Leitura adicional

- [Conceber e implementar uma base de dados da Oracle no Azure](oracle-design.md)
- [Configurar o Oracle Data Guard](configure-oracle-dataguard.md)
- [Configure Oráculo Golden Gate](configure-oracle-golden-gate.md)
- [Backup e recuperação da Oracle](oracle-backup-recovery.md)


## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar VMs altamente disponíveis](../../linux/create-cli-complete.md)
- [Explore as amostras azure CLI de implantação vm](../../linux/cli-samples.md)
