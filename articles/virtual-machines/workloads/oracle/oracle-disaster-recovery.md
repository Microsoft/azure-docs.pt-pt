---
title: Descrição geral de um cenário de recuperação de desastres do Oracle no ambiente do Azure | Documentos da Microsoft
description: Um cenário de recuperação após desastre para uma base de dados do Oracle Database 12C no ambiente do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: db0b9887b80f13938045a5d11fb09ed0a43efc19
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706970"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Recuperação após desastre para uma base de dados do Oracle Database 12C no ambiente do Azure

## <a name="assumptions"></a>Suposições

- Tem um entendimento do design de proteção de dados Oracle e ambientes do Azure.


## <a name="goals"></a>Objetivos
- Conceber a topologia e a configuração que satisfazem os requisitos de recuperação (DR) após desastre.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Cenário 1: Principais e sites de DR no Azure

Um cliente tiver uma Oracle da base de dados conjunto de cópia de segurança no site primário. DR de um site estiver numa região diferente. O cliente utiliza o Oracle Data Guard para uma recuperação rápida entre esses locais. O site primário também tem uma base de dados secundária para relatórios e outras utilizações. 

### <a name="topology"></a>Topologia

Aqui está um resumo da configuração do Azure:

- Dois sites (um site primário e um site de DR)
- Duas redes virtuais
- Duas bases de dados do Oracle com a proteção de dados (primário e de reserva)
- Duas bases de dados do Oracle com Golden Gate ou proteção de dados (apenas no site primário)
- Dois serviços de aplicativo, uma primária e outro no site de DR
- Uma *conjunto de disponibilidade,* que é utilizado para o serviço de base de dados e aplicações no site primário
- Uma jumpbox em cada site, que restringe o acesso à rede privada e só permite o início de sessão por um administrador
- Uma jumpbox, o serviço de aplicações, a base de dados e o gateway de VPN em sub-redes separadas
- NSG aplicado na aplicação e as sub-redes de base de dados

![Captura de ecrã da página de topologia de DR](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Cenário 2: Site primário no local e o site de DR no Azure

Um cliente tem uma configuração de base de dados do Oracle no local (site primário). DR de um site está no Azure. Oracle Data Guard é utilizada para recuperação rápida entre esses locais. O site primário também tem uma base de dados secundária para relatórios e outras utilizações. 

Existem duas abordagens para esta configuração.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Abordagem 1: Ligações diretas entre no local e o Azure, a necessidade de abrir portas TCP na firewall 

Não recomendamos ligações diretas porque elas expõem as portas TCP para o mundo exterior.

#### <a name="topology"></a>Topologia

Segue-se um resumo da configuração do Azure:

- DR de um site 
- Uma rede virtual
- Uma base de dados do Oracle com a proteção de dados (ativo)
- Serviço de um aplicativo no site de DR
- Uma jumpbox, que restringe o acesso à rede privada e só permite o início de sessão por um administrador
- Uma jumpbox, o serviço de aplicações, a base de dados e o gateway de VPN em sub-redes separadas
- NSG aplicado na aplicação e as sub-redes de base de dados
- Uma política/regra de NSG para permitir que a porta TCP de entrada 1521 (ou uma porta definida pelo utilizador)
- Endereço de uma política/regra de NSG para restringir apenas o IP/endereços no local (DB ou aplicação) para acessar a rede virtual

![Captura de ecrã da página de topologia de DR](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Abordagem 2: VPN site a site
VPN site a site é uma abordagem melhor. Para obter mais informações sobre como configurar uma VPN, consulte [criar uma rede virtual com uma ligação de VPN de Site a Site com a CLI](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>Topologia

Segue-se um resumo da configuração do Azure:

- DR de um site 
- Uma rede virtual 
- Uma base de dados do Oracle com a proteção de dados (ativo)
- Serviço de um aplicativo no site de DR
- Uma jumpbox, que restringe o acesso à rede privada e só permite o início de sessão por um administrador
- Uma jumpbox, o serviço de aplicação, a base de dados e o gateway de VPN estiverem em sub-redes separadas
- NSG aplicado na aplicação e as sub-redes de base de dados
- Ligação de VPN de site a site entre no local e o Azure

![Captura de ecrã da página de topologia de DR](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Leitura adicional

- [Conceber e implementar uma base de dados do Oracle no Azure](oracle-design.md)
- [Configurar a proteção de dados Oracle](configure-oracle-dataguard.md)
- [Configurar Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Oracle backup e recuperação](oracle-backup-recovery.md)


## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Criar VMs de elevada disponibilidade](../../linux/create-cli-complete.md)
- [Explore exemplos de CLI do Azure de implementação de VM](../../linux/cli-samples.md)
