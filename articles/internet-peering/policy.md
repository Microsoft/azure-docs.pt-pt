---
title: Política de peering da Microsoft
titleSuffix: Azure
description: Política de peering da Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775228"
---
# <a name="peering-policy"></a>Política de peering
Os requisitos gerais da Microsoft a partir da sua rede são explicados nas secções abaixo. Estes aplicam-se tanto aos pedidos de peering direto e de persparo de troca.

## <a name="technical-requirements"></a>Requisitos técnicos

* Uma rede totalmente redundante com capacidade suficiente para trocar tráfego sem congestionamento.
* Peer terá um número de sistema autónomo (ASN) publicamente resaída.
* Tanto o IPv4 como o IPv6 são suportados e a Microsoft espera estabelecer sessões de ambos os tipos em cada local de observação.
* MD5 não é suportado.
* **Detalhes da ASN:**
    * A Microsoft gere o AS8075 juntamente com as seguintes ASNs: AS8068, AS8069, AS12076. Para uma lista completa de ASNs com peering AS8075, referência AS-SET MICROSOFT.
    * Todas as partes que estão a par da Microsoft concordam em não aceitar rotas a partir de AS12076 (Rota Expresso) em qualquer circunstância, e devem filtrar o AS12076 em todos os pares.
* **Política de encaminhamento:**
    * Peer terá pelo menos um routable público /24.
    * A Microsoft irá substituir os discriminadores multi-saída (MED).
    * A Microsoft prefere receber etiquetas comunitárias de BGP de pares para indicar a origem da rota.
    * Espera-se que os pares registem as suas rotas numa base de dados pública do Registo de Encaminhamento de Internet (IRR), com o propósito da filtragem, e envidarão esforços de boa fé para manter essa informação atualizada.
    * Sugerimos que os pares estabeleçam um prefixo máximo de 1000 (IPv4) e 100 (IPv6) em sessões de pares com a Microsoft.
    * A menos que seja especificamente acordado previamente, espera-se que os pares anunciem rotas consistentes em todos os locais onde eles se peer com a Microsoft.
    * Em geral, as sessões de observação com as 8075 irão anunciar todas as rotas AS-MICROSOFT. As 8075 interligações em África e na Ásia podem limitar-se a rotas relevantes para a região respetiva.
    * Nenhuma das partes estabelecerá uma rota estática, uma rota de último recurso, ou enviará tráfego para a outra parte para uma rota não anunciada via BGP.
    * Espera-se que os pares cumpram os padrões da indústria [MANRS](https://www.manrs.org/) para a segurança das rotas.

## <a name="operational-requirements"></a>Requisitos operacionais
* Um Centro de Operações de Rede 24x7 (NOC), totalmente dotado de pessoal, capaz de ajudar na resolução de todos os problemas técnicos e de desempenho, violações de segurança, negação de ataques de serviço, ou qualquer outro abuso originário do par ou dos seus clientes.
* Espera-se que os pares tenham um perfil completo e atualizado no [PeeringDB,](https://www.peeringdb.com) incluindo um e-mail NOC 24x7 do domínio corporativo e número de telefone. Utilizamos estas informações para validar os dados do par, tais como informações no CNoC, informações técnicas de contacto e sua presença nas instalações de observação, etc. Não são aceites contas pessoais da Yahoo, Gmail e hotmail.

## <a name="physical-connection-requirements"></a>Requisitos de ligação física
* Os locais onde pode ligar-se à Microsoft para peering direto ou perspinagem de troca estão listados em [PeeringDB](https://www.peeringdb.com/net/694)
* **Persque-se:**
    * A interligação deve ser sobre a fibra de modo único utilizando a ótica adequada de 10Gbps.
    * Espera-se que os pares atualizem as suas portas quando a utilização máxima ultrapassa os 50%.
* **Peering direto:**
    * A interligação deve ser sobre a fibra de modo único utilizando as óticas adequadas de 10Gbps ou 100Gbps.
    * A Microsoft apenas estabelecerá o peering direto com os fornecedores de ISP ou Network Service.
    * Espera-se que os pares atualizem os seus portos quando a utilização máxima exceda 50% e mantenham uma capacidade diversificada em cada metro, seja dentro de um único local ou em vários locais de um metro.
    * Cada peering direto consiste em duas ligações a dois routers de borda Microsoft dos routers do Peer localizados na borda de Peer. A Microsoft necessita de sessões de BGP duplas nestas ligações. O par pode optar por não implementar dispositivos redundantes no seu final.

## <a name="traffic-requirements"></a>Requisitos de tráfego
* Os pares sobre o exchange peering devem ter no mínimo 200Mb de tráfego e menos de 2Gb.  Para o tráfego superior a 2Gb o peering direto deve ser revisto.
* Para espreitar diretamente, o tráfego da sua rede para a Microsoft deve satisfazer abaixo do requisito mínimo.

    | Área Geográfica                      | Tráfego mínimo para a Microsoft   |
    | :----------------------- |:-------------------------------|
    | África                   | 500 Mbps                       |
    | APAC (exceto índia)      |   2 Gbps                       |
    | APAC (apenas na Índia)        | 500 Mbps                       |
    | Europa                   |   2 Gbps                       |
    | LATAM                    |   2 Gbps                       |
    | Médio Oriente              | 500 Mbps                       |
    | ND                       |   2 Gbps                       |

* **Diversidade:**
    * Na NA, Europa, APAC e LATAM, interligam-se em pelo menos três locais geograficamente diversos, se possível e mantêm uma capacidade diversificada para permitir que o tráfego falhe em cada metro.
    * Em África, Médio Oriente e Índia, interligam-se em todos os locais diversos possível. Deve manter uma capacidade diversificada suficiente para garantir que o tráfego permaneça na região.

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre os passos para configurar o direct peering com a Microsoft, siga o [direct peering walkthrough](walkthrough-direct-all.md).
* Para aprender sobre os passos para configurar o Exchange peering com a Microsoft, siga o [exchange peering walkthrough](walkthrough-exchange-all.md).