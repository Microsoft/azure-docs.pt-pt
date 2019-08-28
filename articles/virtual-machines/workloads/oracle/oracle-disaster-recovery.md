---
title: Visão geral de um cenário de recuperação de desastres Oracle em seu ambiente do Azure | Microsoft Docs
description: Um cenário de recuperação de desastre para um banco de dados Oracle Database 12c em seu ambiente do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: f6f678f91e74ea9b0b68127c1786fee745508b99
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101467"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Recuperação de desastre para um banco de dados Oracle Database 12c em um ambiente do Azure

## <a name="assumptions"></a>Pressupostos

- Você tem uma compreensão do design do Oracle Data Guard e dos ambientes do Azure.


## <a name="goals"></a>Objetivos
- Projete a topologia e a configuração que atendem aos seus requisitos de DR (recuperação de desastre).

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Cenário 1: Sites primários e de recuperação de desastre no Azure

Um cliente tem um banco de dados Oracle configurado no site primário. Um site de recuperação de desastres está em uma região diferente. O cliente usa o Oracle Data Guard para recuperação rápida entre esses sites. O site primário também tem um banco de dados secundário para relatórios e outros usos. 

### <a name="topology"></a>Topologia

Aqui está um resumo da configuração do Azure:

- Dois sites (um site primário e um site de recuperação de desastre)
- Duas redes virtuais
- Dois bancos de dados Oracle com Data Guard (primário e em espera)
- Dois bancos de dados Oracle com portão dourado ou Data Guard (somente site primário)
- Dois serviços de aplicativo, um primário e outro no site de recuperação de desastres
- Um *conjunto de disponibilidade,* que é usado para o banco de dados e o serviço de aplicativo no site primário
- Um Jumpbox em cada site, que restringe o acesso à rede privada e só permite a entrada por um administrador
- Um Jumpbox, serviço de aplicativo, banco de dados e gateway de VPN em sub-redes separadas
- NSG imposto em sub-redes de aplicativo e de banco de dados

![Captura de tela da página de topologia de recuperação de desastre](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Cenário 2: Site primário local e site de recuperação de desastre no Azure

Um cliente tem uma configuração de banco de dados Oracle local (site primário). Um site de DR está no Azure. O Oracle Data Guard é usado para recuperação rápida entre esses sites. O site primário também tem um banco de dados secundário para relatórios e outros usos. 

Há duas abordagens para essa configuração.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Abordagem 1: Conexões diretas entre o local e o Azure, exigindo portas TCP abertas no firewall 

Não recomendamos conexões diretas porque elas expõem as portas TCP para o mundo exterior.

#### <a name="topology"></a>Topologia

Veja a seguir um resumo da configuração do Azure:

- Um site de DR 
- Uma rede virtual
- Um banco de dados Oracle com Data Guard (ativo)
- Um serviço de aplicativo no site de recuperação de desastre
- Um Jumpbox, que restringe o acesso à rede privada e só permite a entrada por um administrador
- Um Jumpbox, serviço de aplicativo, banco de dados e gateway de VPN em sub-redes separadas
- NSG imposto em sub-redes de aplicativo e de banco de dados
- Uma política/regra de NSG para permitir a porta TCP de entrada 1521 (ou uma porta definida pelo usuário)
- Uma política/regra de NSG para restringir apenas endereços IP/endereços locais (banco de aplicativos ou aplicativo) para acessar a rede virtual

![Captura de tela da página de topologia de recuperação de desastre](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Abordagem 2: VPN site a site
A VPN site a site é uma abordagem melhor. Para obter mais informações sobre como configurar uma VPN, consulte [criar uma rede virtual com uma conexão VPN site a site usando a CLI](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>Topologia

Veja a seguir um resumo da configuração do Azure:

- Um site de DR 
- Uma rede virtual 
- Um banco de dados Oracle com Data Guard (ativo)
- Um serviço de aplicativo no site de recuperação de desastre
- Um Jumpbox, que restringe o acesso à rede privada e só permite a entrada por um administrador
- Um Jumpbox, serviço de aplicativo, banco de dados e gateway de VPN estão em sub-redes separadas
- NSG imposto em sub-redes de aplicativo e de banco de dados
- Conexão VPN site a site entre o local e o Azure

![Captura de tela da página de topologia de recuperação de desastre](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Leitura adicional

- [Projetar e implementar um banco de dados Oracle no Azure](oracle-design.md)
- [Configurar o Oracle Data Guard](configure-oracle-dataguard.md)
- [Configurar o Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Backup e recuperação do Oracle](oracle-backup-recovery.md)


## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Criar VMs altamente disponíveis](../../linux/create-cli-complete.md)
- [Explorar exemplos de implantação de VM CLI do Azure](../../linux/cli-samples.md)
