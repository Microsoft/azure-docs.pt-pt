---
title: Infraestrutura e a conectividade ao SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Configure a infraestrutura de conectividade necessária para utilizar o SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05bd09d3ab05f3ce426126e5629523fba087dad9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707314"
---
# <a name="sap-hana-large-instances-deployment"></a>Implementação de SAP HANA (instâncias grandes) 

Este artigo pressupõe que concluiu a compra de SAP HANA no Azure (instâncias grandes) da Microsoft. Antes de ler este artigo, para obter informações gerais, consulte [termos comuns de instâncias grandes do HANA](hana-know-terms.md) e [SKUs de instâncias grandes do HANA](hana-available-skus.md).


A Microsoft requer as seguintes informações para implementar unidades de instância grande do HANA:

- Nome do cliente.
- Informações de contacto de negócio (incluindo e-mail endereço e número de telefone).
- Informações de contato técnicas (incluindo e-mail endereço e número de telefone).
- Técnico de informação de contacto (incluindo e-mail endereço e número de telefone) de rede.
- Região de implementação do Azure (por exemplo, E.U.A. oeste, leste da Austrália ou Europa do Norte).
- SAP HANA no Azure (instâncias grandes) SKU (configuração).
- Para cada região de implementação do Azure:
    - / 29 intervalo de endereços IP para ligações de ER P2P que ligar redes virtuais do Azure para instâncias grandes do HANA.
    - Um /24 bloco CIDR utilizados para o conjunto IP de servidor de instâncias grandes do HANA.
- Os valores de intervalo de endereços IP utilizados no atributo de espaço de endereço de rede virtual de cada rede virtual do Azure que liga para o HANA nas instâncias grandes.
- Dados para cada sistema de instâncias grandes do HANA:
  - Nome de anfitrião desejado, idealmente com um nome de domínio completamente qualificado.
  - Endereço IP pretendido para a unidade de instância grande do HANA fora do intervalo de endereços do conjunto de IP do servidor. (Os 30 primeiros endereços IP no intervalo de endereços de conjunto IP de servidor estão reservados para utilização interna nas instâncias grandes do HANA).
  - Nome em SID do SAP HANA para a instância do SAP HANA (obrigatório para criar os volumes de disco necessário relacionados com o SAP HANA). A Microsoft precisa o SID do HANA para criar as permissões para sidadm nos volumes de NFS. Anexar estes volumes para a unidade de instância grande do HANA. O SID do HANA também é utilizado como um dos componentes do nome dos volumes de disco que ser montados. Se quiser executar mais de uma instância HANA na unidade, deverá apresentar a vários SIDs do HANA. Cada versão recebe um conjunto separado de volumes atribuídos.
  - No SO Linux, o usuário de sidadm tem um ID de grupo. Este ID é necessário para criar os volumes de disco necessário relacionados com o SAP HANA. A instalação de SAP HANA, normalmente, cria o grupo de sapsys, com um ID de grupo de 1001. O utilizador de sidadm faz parte desse grupo.
  - No SO Linux, o usuário de sidadm tem um ID de utilizador. Este ID é necessário para criar os volumes de disco necessário relacionados com o SAP HANA. Se estiver a executar várias instâncias do HANA na unidade, liste todos os utilizadores de sidadm. 
- O ID de subscrição do Azure para a subscrição do Azure para o SAP HANA no Azure HANA nas instâncias grandes vão ser ligados diretamente. A subscrição do Azure, o que vai ser cobrado com a unidade de instância grande do HANA ou unidades faz referência a este ID de subscrição.

Depois de fornecer as informações anteriores, o Microsoft Aprovisiona SAP HANA no Azure (instâncias grandes). A Microsoft envia informações para ligar redes virtuais do Azure para instâncias grandes do HANA. Também pode acessar as unidades de instância grande do HANA.

Utilize a seguinte sequência para ligar para o HANA nas instâncias grandes, depois da Microsoft implantou:

1. [Ligar a VMs do Azure para instâncias grandes do HANA](hana-connect-azure-vm-large-instances.md)
2. [Ligar uma VNet para instâncias grandes do HANA ExpressRoute](hana-connect-vnet-express-route.md)
3. [Requisitos de rede adicional (opcionais)](hana-additional-network-requirements.md)

