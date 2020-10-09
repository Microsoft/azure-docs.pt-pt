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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75775228"
---
# <a name="peering-policy"></a>Política de peering
Os requisitos gerais da Microsoft da sua rede são explicados nas secções abaixo. Estes são aplicáveis tanto a pedidos de observação direta como de perspitagem de câmbio.

## <a name="technical-requirements"></a>Requisitos técnicos

* Uma rede totalmente redundante com capacidade suficiente para trocar tráfego sem congestionamento.
* Peer terá um número de sistema autónomo (ASN) publicamente rotaível.
* Tanto o IPv4 como o IPv6 são suportados e a Microsoft espera estabelecer sessões de ambos os tipos em cada local de observação.
* MD5 não é suportado.
* **Detalhes da ASN:**
    * A Microsoft gere o AS8075 juntamente com as seguintes ASNs: AS8068, AS8069, AS12076. Para uma lista completa de ASNs com peering AS8075, referência AS-SET MICROSOFT.
    * Todas as partes que espreitam com a Microsoft concordam em não aceitar rotas a partir de AS12076 (Rota Expressa) em nenhuma circunstância, e devem filtrar o AS12076 em todos os pares.
* **Política de encaminhamento:**
    * Peer terá pelo menos um roteável /24 publicamente.
    * A Microsoft substituirá os discriminadores multi-exit recebidos (MED).
    * A Microsoft prefere receber tags comunitários BGP de pares para indicar a origem da rota.
    * Espera-se que os pares registem as suas rotas numa base de dados pública do Registo de Encaminhamento de Internet (IRR), com o objetivo de filtragem, e ensacam os esforços de boa fé para manter esta informação atualizada.
    * Sugerimos que os pares estabeleçam um prefixo máximo de 1000 (IPv4) e 100 (IPv6) em sessões de observação com a Microsoft.
    * A menos que especificamente acordados previamente, espera-se que os pares anunciem rotas consistentes em todos os locais onde estão em pares com a Microsoft.
    * Em geral, as sessões de observação com o AS8075 anunciarão todas as rotas AS-MICROSOFT. As interligações AS8075 em África e na Ásia podem limitar-se às rotas relevantes para a respetiva região.
    * Nenhuma das partes estabelecerá uma rota estática, uma rota de último recurso, ou enviará de outra forma tráfego para a outra parte para uma rota não anunciada via BGP.
    * Espera-se que os pares cumpram os padrões da indústria [MANRS](https://www.manrs.org/) para a segurança das rotas.

## <a name="operational-requirements"></a>Requisitos operacionais
* Um Centro de Operações de Rede 24x7 (NOC), totalmente equipado com pessoal 24x7, capaz de ajudar na resolução de todos os problemas técnicos e de desempenho, violações de segurança, negação de ataques de serviço, ou qualquer outro abuso originário do par ou dos seus clientes.
* Espera-se que os pares tenham um perfil completo e atualizado no [PeeringDB,](https://www.peeringdb.com) incluindo um e-mail 24x7 NOC do domínio corporativo e número de telefone. Utilizamos estas informações para validar os detalhes do par, tais como informações NOC, informações técnicas de contacto, e a sua presença nas instalações de observação, etc. Não são aceites contas pessoais de Yahoo, Gmail e hotmail.

## <a name="physical-connection-requirements"></a>Requisitos de ligação física
* As localizações onde pode conectar-se com a Microsoft para espreitar diretamente ou trocar de peering estão listadas no [PeeringDB](https://www.peeringdb.com/net/694)
* **Permutando olhando:**
    * A interligação deve ser sobre fibra de modo único utilizando a ótica adequada de 10Gbps.
    * Espera-se que os pares atualizem os seus portos quando a utilização máxima for superior a 50%.
* **Observação direta:**
    * A interligação deve ser sobre fibra de modo único utilizando a ótica adequada de 10Gbps ou 100Gbps.
    * A Microsoft apenas estabelecerá o espreitamento direto com os fornecedores de ISP ou Serviço de Rede.
    * Espera-se que os pares atualizem os seus portos quando a utilização máxima exceder 50% e mantenham uma capacidade diversificada em cada metro, seja dentro de um único local ou em vários locais de um metro.
    * Cada persimento direto consiste em duas ligações a dois routers de borda microsoft dos routers do Peer localizados na borda do Peer. A Microsoft requer sessões de BGP duplas através destas ligações. O par pode optar por não implantar dispositivos redundantes no seu fim.

## <a name="traffic-requirements"></a>Requisitos de tráfego
* Os pares sobre o persificado da Exchange devem ter no mínimo 200Mb de tráfego e menos de 2Gb.  Para o tráfego superior a 2Gb Deve ser revisto o seu persituo direto.
* Para o estojo direto, o tráfego da sua rede para a Microsoft deve satisfazer um requisito abaixo do mínimo.

    | Área Geográfica                      | Tráfego mínimo para a Microsoft   |
    | :----------------------- |:-------------------------------|
    | África                   | 500 Mbps                       |
    | APAC (exceto a Índia)      |   2 Gbps                       |
    | APAC (apenas na Índia)        | 500 Mbps                       |
    | Europa                   |   2 Gbps                       |
    | LATAM                    |   2 Gbps                       |
    | Médio Oriente              | 500 Mbps                       |
    | ND                       |   2 Gbps                       |

* **Diversidade:**
    * Na NA, Europa, APAC e LATAM, interligam-se em pelo menos três locais geograficamente diversos, se possível e manter uma capacidade diversificada para permitir que o tráfego falhe dentro de cada metro.
    * Em África, Médio Oriente e Índia, interligam-se em diversos locais possíveis. Deve manter uma capacidade suficientemente diversificada para garantir que o tráfego permaneça na região.

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre os passos para configurar o persigo direto com a Microsoft, siga [o walkthrrough de observação direto](walkthrough-direct-all.md).
* Para aprender sobre os passos para configurar o Exchange peering com a Microsoft, siga [o Exchange peering walkthrough](walkthrough-exchange-all.md).