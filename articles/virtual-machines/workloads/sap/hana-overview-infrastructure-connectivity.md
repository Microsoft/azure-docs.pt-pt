---
title: Infraestruturas e conectividade com a SAP HANA em Azure (grandes instâncias) | Microsoft Docs
description: Configure a infraestrutura de conectividade necessária para utilizar o SAP HANA em Azure (grandes instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27dc2f6c647308984a02f2cf52dbc0213abad088
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676837"
---
# <a name="sap-hana-large-instances-deployment"></a>Implantação SAP HANA (grandes instâncias) 

Este artigo pressupõe que concluiu a sua compra de SAP HANA em Azure (grandes instâncias) da Microsoft. Antes de ler este artigo, para o fundo geral, consulte [hana grandes instâncias termos comuns](hana-know-terms.md) e [HANA grandes instâncias SKUs](hana-available-skus.md).


A Microsoft requer as seguintes informações para implementar unidades de grande instância HANA:

- Nome do cliente.
- Informações de contacto comercial (incluindo endereço de e-mail e número de telefone).
- Informações técnicas de contacto (incluindo endereço de e-mail e número de telefone).
- Informações técnicas de contacto em rede (incluindo endereço de e-mail e número de telefone).
- Região de implantação de Azure (por exemplo, Eua Ocidental, Austrália Oriental ou Norte da Europa).
- SAP HANA em Azure (grandes instâncias) SKU (configuração).
- Para cada região de implantação de Azure:
    - Um intervalo de endereços IP /29 para ligações ER-P2P que ligam redes virtuais Azure a grandes instâncias HANA.
    - Um bloco CIDR /24 utilizado para o conjunto IP do servidor de grandes instâncias HANA.
    - Opcional ao utilizar o [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) para permitir o encaminhamento direto das unidades de grande instância da HANA ou o encaminhamento entre unidades HANA Large Instance em diferentes regiões de Azure, é necessário reservar outra gama de endereços IP /29. Este intervalo em particular pode não se sobrepor a nenhum dos outros intervalos de endereços IP definidos anteriormente.
- Os valores de gama de endereços IP utilizados no atributo espaço de endereço de rede virtual de cada rede virtual Azure que se conecta às grandes instâncias HANA.
- Dados para cada sistema de grandes instâncias HANA:
  - Nome de anfitrião desejado, idealmente com um nome de domínio totalmente qualificado.
  - Endereço IP desejado para a unidade de grande instância HANA fora da gama de endereços do pool IP do Servidor. (Os primeiros 30 endereços IP na gama de endereços IP do servidor são reservados para uso interno dentro de grandes instâncias HANA.)
  - Nome SAP HANA SID para a instância SAP HANA (necessário para criar os volumes necessários de disco relacionados com SAP HANA). A Microsoft precisa do SID HANA para criar as permissões para sidadm nos volumes NFS. Estes volumes ligam-se à unidade de grande instância HANA. O HANA SID também é usado como um dos componentes de nome dos volumes de disco que são montados. Se quiser executar mais de uma instância HANA na unidade, deve listar vários SIDs HANA. Cada um recebe um conjunto separado de volumes atribuídos.
  - No Linux OS, o utilizador do sidadm tem um ID de grupo. Este ID é necessário para criar os volumes necessários de disco relacionados com o SAP HANA. A instalação SAP HANA geralmente cria o grupo sapsys, com um ID de grupo de 1001. O utilizador sidadm faz parte desse grupo.
  - No Linux OS, o utilizador sidadm tem uma identificação de utilizador. Este ID é necessário para criar os volumes necessários de disco relacionados com o SAP HANA. Se estiver a executar várias instâncias HANA na unidade, liste todos os utilizadores de sidadm. 
- O ID de assinatura Azure para a subscrição Azure a que o SAP HANA em Azure HANA grandes instâncias vão estar diretamente ligados. Este ID de subscrição refere-se à subscrição Azure, que será cobrada com a unidade ou unidades de grande instância HANA.

Depois de fornecer as informações anteriores, a Microsoft fornece SAP HANA em Azure (grandes instâncias). A Microsoft envia-lhe informações para ligar as suas redes virtuais Azure a grandes instâncias da HANA. Você também pode aceder às unidades de grande instância HANA.

Utilize a seguinte sequência para ligar ao HANA grandes instâncias depois de a Microsoft a ter implantado:

1. [Ligação de VMs Azure a HANA grandes instâncias](hana-connect-azure-vm-large-instances.md)
2. [Ligação de um VNet a HANA grandes instâncias ExpressRoute](hana-connect-vnet-express-route.md)
3. [Requisitos adicionais de rede (opcional)](hana-additional-network-requirements.md)
