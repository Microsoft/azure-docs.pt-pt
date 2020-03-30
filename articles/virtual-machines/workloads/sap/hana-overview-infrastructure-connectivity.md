---
title: Infraestrutura e conectividade com sAP HANA em Azure (grandes instâncias) / Microsoft Docs
description: Configure a infraestrutura de conectividade necessária para utilizar o SAP HANA no Azure (grandes instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cea89087742f1987f693b8bfb627bd71038a0c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616925"
---
# <a name="sap-hana-large-instances-deployment"></a>Implantação sap HANA (grandes instâncias) 

Este artigo assume que concluiu a compra do SAP HANA no Azure (grandes instâncias) da Microsoft. Antes de ler este artigo, para segundo plano geral, consulte os termos comuns de [grandes instâncias HANA](hana-know-terms.md) e as [grandes instâncias HANA SKUs.](hana-available-skus.md)


A Microsoft necessita das seguintes informações para implementar unidades de grande instância HANA:

- Nome do cliente.
- Informações de contacto comercial (incluindo endereço de e-mail e número de telefone).
- Informações técnicas de contacto (incluindo endereço de e-mail e número de telefone).
- Informações técnicas de contacto em rede (incluindo endereço de e-mail e número de telefone).
- Região de implantação de Azure (por exemplo, Oeste dos EUA, Austrália Leste ou Norte da Europa).
- SAP HANA em Azure (grandes instâncias) SKU (configuração).
- Para cada região de implantação de Azure:
    - Uma gama de endereços IP /29 para ligações ER-P2P que ligam redes virtuais Azure a grandes instâncias HANA.
    - Um bloco CIDR /24 usado para o conjunto IP de grandes instâncias HANA.
    - Opcional ao utilizar o [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) para permitir o encaminhamento direto das instalações para as unidades HANA Large Instance ou o encaminhamento entre unidades HANA Large Instance em diferentes regiões do Azure, é necessário reservar outra gama de endereços IP /29. Esta gama específica pode não se sobrepor a nenhuma das outras gamas de endereços IP que definiu anteriormente.
- Os valores de gama de endereços IP utilizados no espaço de endereço de rede virtual de cada rede virtual Azure que se conecta às grandes instâncias HANA.
- Dados para cada sistema de grandes instâncias HANA:
  - Nome de anfitrião desejado, idealmente com um nome de domínio totalmente qualificado.
  - Endereço IP desejado para a unidade de instância de grande instância HANA fora da gama de endereços ip do servidor. (Os primeiros 30 endereços IP na gama de endereços IP do servidor estão reservados para uso interno em grandes instâncias HANA.)
  - Nome SAP HANA SID para a instância SAP HANA (necessário para criar os volumes de disco relacionados com SAP HANA necessários). A Microsoft precisa do SID HANA para criar as permissões para sidadm nos volumes NFS. Estes volumes fixam-se à unidade de instância de grande instância HANA. O HANA SID também é usado como um dos componentes de nome dos volumes de disco que são montados. Se quiser executar mais de uma instância HANA na unidade, deve listar vários SIDs HANA. Cada um recebe um conjunto separado de volumes atribuídos.
  - No Sistema Operativo Linux, o utilizador da sidadm tem uma identificação de grupo. Este ID é necessário para criar os volumes de disco relacionados com sap HANA necessários. A instalação SAP HANA geralmente cria o grupo sapsys, com um ID de grupo de 1001. O utilizador da sidadm faz parte desse grupo.
  - No Sistema Operativo Linux, o utilizador da sidadm tem um ID de utilizador. Este ID é necessário para criar os volumes de disco relacionados com sap HANA necessários. Se estiver a executar várias instâncias hana na unidade, enumere todos os utilizadores do SIDADM. 
- O ID de subscrição Azure para a subscrição Azure a que o SAP HANA em Azure HANA grandes instâncias vão estar diretamente ligados. Este ID de subscrição refere a subscrição Azure, que será carregada com a unidade ou unidades de instância de grande instância HANA.

Depois de fornecer as informações anteriores, a Microsoft fornece SAP HANA no Azure (grandes instâncias). A Microsoft envia-lhe informações para ligar as suas redes virtuais Azure a grandes instâncias HANA. Também pode aceder às grandes unidades de instância da HANA.

Utilize a seguinte sequência para se ligar às grandes instâncias HANA depois de a Microsoft a ter implementado:

1. [Ligação de VMs Azure a grandes instâncias HANA](hana-connect-azure-vm-large-instances.md)
2. [Ligando uma VNet a grandes instâncias DA HANA ExpressRoute](hana-connect-vnet-express-route.md)
3. [Requisitos adicionais de rede (opcional)](hana-additional-network-requirements.md)

