---
title: Política de emparelhamento da Microsoft
titleSuffix: Azure
description: Política de emparelhamento da Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775228"
---
# <a name="peering-policy"></a>Política de emparelhamento
Os requisitos gerais da Microsoft da sua rede são explicados nas seções a seguir. Elas se aplicam a solicitações de emparelhamento direto e emparelhamento do Exchange.

## <a name="technical-requirements"></a>Requisitos técnicos

* Uma rede totalmente redundante com capacidade suficiente para trocar o tráfego sem congestionamento.
* O par terá um número de sistema autônomo roteável publicamente (ASN).
* Há suporte para IPv4 e IPv6 e a Microsoft espera estabelecer sessões de ambos os tipos em cada local de emparelhamento.
* Não há suporte para MD5.
* **Detalhes de ASN:**
    * A Microsoft gerencia o AS8075 juntamente com os seguintes ASNs: AS8068, AS8069, AS12076. Para obter uma lista completa de ASNs com emparelhamento AS8075, faça referência como-SET MICROSOFT.
    * Todas as partes que emparelham com a Microsoft concordam em não aceitar rotas do AS12076 (rota expressa) em nenhuma circunstância e devem filtrar AS12076 em todos os pares.
* **Política de roteamento:**
    * O par terá pelo menos um roteável publicamente/24.
    * A Microsoft substituirá os discriminadores de múltipla saída recebidos (MED).
    * A Microsoft prefere receber marcas de comunidade BGP de pares para indicar a origem da rota.
    * Espera-se que o par Registre suas rotas em um banco de dados de TIR (registro de roteamento de Internet) público, para fins de filtragem, e faça com que os esforços de boa fé mantenham essas informações atualizadas.
    * Sugerimos que os colegas definam um prefixo máximo de rotas 1000 (IPv4) e 100 (IPv6) em sessões de emparelhamento com a Microsoft.
    * A menos que seja especificamente acordado anteriormente, os pares devem anunciar rotas consistentes em todos os locais onde emparelham com a Microsoft.
    * Em geral, as sessões de emparelhamento com AS8075 anunciarão todas as rotas AS-MICROSOFT. Interconexões AS8075 na África e na Ásia podem ser limitadas a rotas relevantes para a respectiva região.
    * Nenhuma das partes estabelecerá uma rota estática, uma rota de último recurso ou enviará tráfego para a outra entidade para uma rota não anunciada via BGP.
    * Espera-se que os pares sigam os padrões do setor [MANRS](https://www.manrs.org/) para segurança de rota.

## <a name="operational-requirements"></a>Requisitos operacionais
* Um NOC (centro de operações de rede) 24x7, capaz de auxiliar na resolução de todos os problemas técnicos e de desempenho, violações de segurança, ataques de negação de serviço ou qualquer outro abuso originado no par ou em seus clientes.
* Espera-se que os pares tenham um perfil completo e atualizado em [PeeringDB](https://www.peeringdb.com) , incluindo um email do NOC 24x7 do domínio corporativo e do número de telefone. Usamos essas informações para validar os detalhes do par, como informações do NOC, informações de contato técnico e sua presença nos recursos de emparelhamento, etc. As contas pessoais Yahoo, Gmail e hotmail não são aceitas.

## <a name="physical-connection-requirements"></a>Requisitos de conexão física
* Os locais em que você pode se conectar à Microsoft para emparelhamento direto ou emparelhamento do Exchange estão listados em [PeeringDB](https://www.peeringdb.com/net/694)
* **Emparelhamento do Exchange:**
    * A interconexão deve ser sobre a fibra de modo único usando a fibra óptica 10 Gbps apropriada.
    * Espera-se que os pares atualizem suas portas quando a utilização de pico exceder 50%.
* **Emparelhamento direto:**
    * A interconexão deve ser sobre a fibra de modo único usando a fibra óptica 10 Gbps ou 100Gbps apropriada.
    * A Microsoft estabelecerá somente o emparelhamento direto com provedores de serviços de rede ou ISP.
    * Espera-se que os pares atualizem suas portas quando a utilização de pico exceder 50% e manter a capacidade diversificada em cada metro, seja dentro de um único local ou em vários locais em um metro.
    * Cada emparelhamento direto consiste em duas conexões com dois roteadores do Microsoft Edge dos roteadores do par localizados na borda do par. A Microsoft requer sessões BGP duplas entre essas conexões. O par pode optar por não implantar dispositivos redundantes no final deles.

## <a name="traffic-requirements"></a>Requisitos de tráfego
* Os pares do emparelhamento do Exchange devem ter, no mínimo, 200 MB de tráfego e menos de 2Gb.  Para o tráfego que excede o emparelhamento direto de 2 GB deve ser revisado.
* Para o emparelhamento direto, o tráfego de sua rede para a Microsoft deve atender ao requisito mínimo.

    | Geo                      | Tráfego mínimo para a Microsoft   |
    | :----------------------- |:-------------------------------|
    | África                   | 500 Mbps                       |
    | Pacífico (exceto Índia)      |   2 Gbps                       |
    | No Pacífico (somente Índia)        | 500 Mbps                       |
    | Europa                   |   2 Gbps                       |
    | LATA de                    |   2 Gbps                       |
    | Médio Oriente              | 500 Mbps                       |
    | N/D                       |   2 Gbps                       |

* **Diversidade**
    * Em na, Europa, oeste e América do, interconecte em pelo menos três locais geograficamente diversificados, se possível, e mantenha uma capacidade diversificada para permitir o tráfego para failover em cada metro.
    * Na África, Oriente Médio e Índia, interconecte-se em tantos locais diversificados quanto possível. Deve manter uma capacidade suficiente para garantir que o tráfego permaneça na região.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as etapas para configurar o emparelhamento direto com a Microsoft, siga [a explicação do emparelhamento direto](walkthrough-direct-all.md).
* Para saber mais sobre as etapas para configurar o emparelhamento do Exchange com a Microsoft, siga [a explicação do emparelhamento do Exchange](walkthrough-exchange-all.md).