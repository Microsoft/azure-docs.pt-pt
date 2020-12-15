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
ms.openlocfilehash: 20f25e0add5d05bb2dcf7f3ebdc86ccd5ae889d0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510792"
---
# <a name="peering-policy"></a>Política de peering
A Microsoft mantém uma política de observação seletiva concebida para garantir a melhor experiência possível do cliente apoiada pelos padrões e melhores práticas da indústria, escalando para a procura futura e colocação estratégica de peering. Como tal, a Microsoft reserva-se o direito de abrir exceções à política, conforme considerado necessário. Os requisitos gerais da Microsoft da sua rede são explicados nas secções abaixo. Estes são aplicáveis tanto a pedidos de observação direta como de perspitagem de câmbio. 

## <a name="technical-requirements"></a>Requisitos técnicos

* Uma rede totalmente redundante com capacidade suficiente para trocar tráfego sem congestionamento.
* Peer terá um número de sistema autónomo (ASN) publicamente rotaível.
* Tanto o IPv4 como o IPv6 são suportados e a Microsoft espera estabelecer sessões de ambos os tipos em cada local de observação.
* MD5 não é suportado.
* **Detalhes da ASN:**

    * A Microsoft gere o AS8075 juntamente com as seguintes ASNs: AS8068, AS8069, AS12076. Para uma lista completa de ASNs com peering AS8075, referência AS-SET MICROSOFT.
    * Todas as partes que espreitarem com a Microsoft concordam em não aceitar rotas a partir de AS12076 (Rota Expressa) em qualquer circunstância e devem filtrar o AS12076 em todos os pares.

* **Política de encaminhamento:**
    * Peer terá pelo menos um roteável /24 publicamente.
    * A Microsoft substituirá os discriminadores multi-exit recebidos (MED).
    * A Microsoft prefere receber tags comunitários BGP de pares para indicar a origem da rota.
    * Recomendamos que os pares estabeleçam um prefixo máximo de 2000 (IPv4) e 500 (IPv6) em sessões de observação com a Microsoft.
    * A menos que especificamente acordados previamente, espera-se que os pares anunciem rotas consistentes em todos os locais onde estão em pares com a Microsoft.
    * Em geral, as sessões de observação com o AS8075 anunciarão todas as rotas AS-MICROSOFT. A Microsoft pode anunciar algumas especificidades regionais.
    * Nenhuma das partes estabelecerá uma rota estática, uma rota de último recurso, ou enviará de outra forma tráfego para a outra parte para uma rota não anunciada via BGP.
    * Os pares são obrigados a registar as suas rotas numa base de dados pública do Registo de Encaminhamento de Internet (IRR), com o objetivo de filtragem, e manterão essa informação atualizada.      
    * Os pares aderirão aos padrões da indústria MANRS para a segurança das rotas.  A seu exclusivo critério, a Microsoft pode optar por: 1.) não estabelecer o seu conhecimento com empresas que não tenham rotas assinadas e registadas; 2.) remover rotas RPKI inválidas; 3.) não aceitar rotas de pares estabelecidos que não estejam registados e assinados. 

## <a name="operational-requirements"></a>Requisitos operacionais
* Um Centro de Operações de Rede 24x7 (NOC), totalmente equipado com pessoal 24x7, capaz de ajudar na resolução de todos os problemas técnicos e de desempenho, violações de segurança, negação de ataques de serviço, ou qualquer outro abuso originário do par ou dos seus clientes.
* Espera-se que os pares tenham um perfil completo e atualizado no [PeeringDB,](https://www.peeringdb.com) incluindo um e-mail 24x7 NOC do domínio corporativo e número de telefone. Utilizamos estas informações para validar os detalhes do par, tais como informações NOC, informações técnicas de contacto, e a sua presença nas instalações de observação, etc. As contas pessoais de Yahoo, Gmail e Hotmail não são aceites.

## <a name="physical-connection-requirements"></a>Requisitos de ligação física
* As localizações onde pode conectar-se com a Microsoft para espreitar diretamente ou trocar de peering estão listadas no [PeeringDB](https://www.peeringdb.com/net/694)

* **Permutando olhando:**
    * Espera-se que os pares tenham, no mínimo, uma ligação de 10 Gb à troca.
    * Espera-se que os pares atualizem os seus portos quando a utilização máxima for superior a 50%.
    * A Microsoft encoraja os pares a manter uma conectividade diversificada para trocar para suportar cenários de failover.

* **Observação direta:**
    * A interligação deve ser sobre fibra de modo único utilizando ótica de 100 Gbps.
    * A Microsoft apenas estabelecerá o espreitamento direto com os fornecedores de ISP ou Serviço de Rede.
    * Espera-se que os pares atualizem os seus portos quando a utilização máxima exceder 50% e mantenham uma capacidade diversificada em cada metro, seja dentro de um único local ou em vários locais de um metro.
    * Cada persimento direto consiste em duas ligações a dois routers de borda microsoft dos routers do Peer localizados na borda do Peer. A Microsoft requer sessões de BGP duplas através destas ligações. O par pode optar por não implantar dispositivos redundantes no seu fim.


## <a name="traffic-requirements"></a>Requisitos de tráfego

* Os pares sobre o perspível de Exchange devem ter no mínimo 500 Mb de tráfego e menos de 2 Gb. Para o tráfego superior a 2 Gb deve ser estabelecido um perspionamento direto.
* A Microsoft exige no mínimo 2 Gb para espreitar diretamente. Cada um mutuamente acordado para o local de observação deve apoiar o failover que garante que o espreitamento permanece localizado durante um cenário de failover. 

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre os passos para configurar o persigo direto com a Microsoft, siga [o walkthrrough de observação direto](walkthrough-direct-all.md).
* Para aprender sobre os passos para configurar o Exchange peering com a Microsoft, siga [o Exchange peering walkthrough](walkthrough-exchange-all.md).
