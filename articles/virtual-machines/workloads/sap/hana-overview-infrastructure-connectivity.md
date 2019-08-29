---
title: Infraestrutura e conectividade para SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Configure a infraestrutura de conectividade necessária para usar SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4fa0fe072fe98d565ad9d6f947540b7e1b039732
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101165"
---
# <a name="sap-hana-large-instances-deployment"></a>Implantação de SAP HANA (instâncias grandes) 

Este artigo pressupõe que você concluiu a compra de SAP HANA no Azure (instâncias grandes) da Microsoft. Antes de ler este artigo, para o plano de fundo geral, consulte [termos comuns do Hana em instâncias grandes](hana-know-terms.md) e [SKUs de instâncias grandes do Hana](hana-available-skus.md).


A Microsoft requer as seguintes informações para implantar unidades de instância grande do HANA:

- Nome do cliente.
- Informações de contato comercial (incluindo endereço de email e número de telefone).
- Informações de contato técnico (incluindo endereço de email e número de telefone).
- Informações de contato de rede técnica (incluindo endereço de email e número de telefone).
- Região de implantação do Azure (por exemplo, oeste dos EUA, leste da Austrália ou Europa Setentrional).
- SAP HANA no SKU (instâncias grandes) do Azure (configuração).
- Para cada região de implantação do Azure:
    - Um intervalo de endereços IP/29 para conexões de ER-P2P que conectam redes virtuais do Azure a instâncias grandes do HANA.
    - Um bloco CIDR/24 usado para o pool de IPS do servidor de instâncias grandes do HANA.
    - Opcional ao usar o [ExpressRoute alcance global](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) para habilitar o roteamento direto do local para as unidades de instância grande do Hana ou o roteamento entre unidades de instância grande do Hana em diferentes regiões do Azure, você precisa reservar outro/29 intervalo de endereços IP. Esse intervalo específico não pode se sobrepor a nenhum dos outros intervalos de endereços IP definidos anteriormente.
- Os valores de intervalo de endereços IP usados no atributo espaço de endereço de rede virtual de todas as redes virtuais do Azure que se conectam às instâncias grandes HANA.
- Dados para cada sistema de instâncias grandes do HANA:
  - Hostname desejado, idealmente com um nome de domínio totalmente qualificado.
  - Endereço IP desejado para a unidade de instância grande do HANA do intervalo de endereços do pool de IPS do servidor. (Os 30 primeiros endereços IP no intervalo de endereços do pool de IPS do servidor são reservados para uso interno em instâncias grandes do HANA.)
  - SAP HANA nome do SID para a instância de SAP HANA (necessário para criar os volumes de disco relacionados ao SAP HANA necessário). A Microsoft precisa do SID do HANA para criar as permissões para SIDADM nos volumes de NFS. Esses volumes são anexados à unidade de instância grande do HANA. O SID do HANA também é usado como um dos componentes de nome dos volumes de disco que são montados. Se você quiser executar mais de uma instância do HANA na unidade, deverá listar vários SIDs do HANA. Cada uma Obtém um conjunto separado de volumes atribuídos.
  - No sistema operacional Linux, o usuário SIDADM tem uma ID de grupo. Essa ID é necessária para criar os volumes de disco relacionados ao SAP HANA necessários. A instalação do SAP HANA geralmente cria o grupo de SAPs, com uma ID de grupo de 1001. O usuário SIDADM faz parte desse grupo.
  - No sistema operacional Linux, o usuário SIDADM tem uma ID de usuário. Essa ID é necessária para criar os volumes de disco relacionados ao SAP HANA necessários. Se você estiver executando várias instâncias do HANA na unidade, liste todos os usuários do SIDADM. 
- A ID de assinatura do Azure para a assinatura do Azure à qual SAP HANA nas instâncias grandes do Azure HANA serão conectadas diretamente. Essa ID de assinatura referencia a assinatura do Azure, que será cobrada com a unidade ou unidades de instância grande do HANA.

Depois de fornecer as informações anteriores, a Microsoft provisiona SAP HANA no Azure (instâncias grandes). A Microsoft envia informações para vincular suas redes virtuais do Azure às instâncias grandes do HANA. Você também pode acessar as unidades de instância grande do HANA.

Use a sequência a seguir para se conectar às instâncias grandes HANA depois que a Microsoft a tiver implantada:

1. [Conectando VMs do Azure a instâncias grandes do HANA](hana-connect-azure-vm-large-instances.md)
2. [Conectando uma VNet ao SAP HANA em instâncias grandes](hana-connect-vnet-express-route.md)
3. [Requisitos de rede adicionais (opcional)](hana-additional-network-requirements.md)

