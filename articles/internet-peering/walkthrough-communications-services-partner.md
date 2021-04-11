---
title: Internet Azure olhando para serviços de comunicações walkthrough
titleSuffix: Azure
description: Internet Azure olhando para serviços de comunicações walkthrough
services: internet-peering
author: gthareja
ms.service: internet-peering
ms.topic: how-to
ms.date: 03/30/2021
ms.author: gatharej
ms.openlocfilehash: ff6750883a904ff5ddbddd3ddfd1ed82e72aebbc
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106499009"
---
# <a name="azure-internet-peering-for-communications-services-walkthrough"></a>Internet Azure olhando para serviços de comunicações walkthrough

Esta secção explica os passos que um Fornecedor de Serviços de Comunicações deve seguir para estabelecer uma interligação direta com a Microsoft.

**Prestadores de Serviços de Comunicações:**  Os Fornecedores de Serviços de Comunicações são as organizações que oferecem serviços de comunicação (Comunicações, mensagens, conferências, etc.) e procuram integrar a sua infraestrutura de serviços de comunicações (SBC/SIP Gateway, etc.)  com a Azure Communication Services e as Equipas Microsoft. 

Azure Internet peering support Communications Services Providers para estabelecer interligação direta com a Microsoft em qualquer um dos seus sites de borda (localizações pop). A lista de todos os sites de bordas públicas está disponível em [PeeringDB](https://www.peeringdb.com/net/694).

O azure Internet peering fornece altamente fiável e QoS (Qualidade de Serviço) permitiu a interligação para serviços de comunicações para garantir serviços centrados na alta qualidade e desempenho.

## <a name="technical-requirements"></a>Requisitos Técnicos
Os requisitos técnicos para estabelecer uma interligação direta para os serviços de comunicação são os seguintes:
-   O Peer MUST fornece o número de sistema autónomo (ASN), que DEVE ser público.
-   O par DEVE ter interligação redundante (PNI) em cada local de interligação para garantir o despedimento local.
-   O Peer MUST tem redundância geo-redundância no local para garantir o incumprimento em caso de falhas no local na região/metro.
-   O Peer MUST tem as sessões de BGP como Active-Active para garantir alta disponibilidade e convergência mais rápida e não deve ser a provisionada como Primário e backup.
-   O Peer MUST mantém uma proporção de 1:1 para os routers de pares para circuitos de observação e não é aplicada qualquer limitação de taxa.
-   O Peer MUST fornece e anuncia o seu próprio espaço de endereço IPv4 publicamente remetido utilizado pelos pontos finais do serviço de comunicações peer (por exemplo, SBC). 
-   O Peer MUST fornece detalhes de que classe de tráfego e pontos finais estão alojados em cada sub-rede anunciada. 
-   O Peer MUST executou bGP sobre a deteção bidirecional de encaminhamento (BFD) para facilitar a convergência de sub-segunda rota.
-   Todos os prefixos de infraestrutura de comunicações estão registados no portal Azure e anunciados com a cadeia comunitária 8075:8007.
-   O peer MUST NOT termina olhando para um dispositivo que executa uma firewall imponente. 
-   A Microsoft configurará todas as ligações de interligação como LAG (pacotes de ligação) por padrão, por isso, o peer MUST suporta LACP (Link A agregador Decomposição Protocol) nas ligações de interligação.

## <a name="establishing-direct-interconnect-with-microsoft-for-communications-services"></a>Estabelecer interligação direta com a Microsoft para serviços de comunicações.

Para estabelecer uma interligação direta utilizando o azure Internet espreitando, siga os passos abaixo:

**1. Associar o público peer asn à Subscrição Azure:**

Caso a Peer já tenha associado a assinatura pública ASN à Azure, por favor ignore este passo.

[Associar a subscrição do peer ASN à Azure utilizando o portal - Azure | Microsoft Docs](https://docs.microsoft.com/azure/internet-peering/howto-subscription-association-portal)

O próximo passo é criar uma ligação de observação direta para o Serviço de Observação.

> [!NOTE]
> Assim que a associação ASN for aprovada, envie-nos um e-mail peeringservices@microsoft.com com a sua ASN e iD de subscrição para associar a sua subscrição aos serviços de Comunicações. 

**2. Criar ligação de espreitamento direto para o Serviço de Observação:**

Siga as instruções para [criar ou modificar um espreitamento direto utilizando o portal](https://docs.microsoft.com/azure/internet-peering/howto-direct-portal)

Certifique-se de que cumpre os requisitos de alta disponibilidade.

Certifique-se de que está a selecionar as seguintes opções na página "Criar um Parante":

Tipo de peering:   **Direto**

Rede Microsoft:  **8075**

SKU:        **Premium Grátis**


Em "Página de Ligação de Pares Diretos" selecione as seguintes opções:

Fornecedor de endereços de sessão:   **Microsoft**

Utilização para serviços de peering:   **Ativado**

> [!NOTE] 
> Ignore a seguinte mensagem ao selecionar para ativar para serviços de peering.
> *Não ative a menos que tenha contactado peering@microsoft.com sobre se tornar um fornecedor DE MAPS.*


  **2a. Utilize a ligação de espreitamento direto existente para serviços de peering**

Se tiver um espreitamento direto existente que pretende utilizar para suportar o Serviço de Peering, pode ser ativado no portal Azure.
1.  Siga as instruções para [converter um legado Espreitar diretamente para o recurso Azure utilizando o portal](https://docs.microsoft.com/azure/internet-peering/howto-legacy-direct-portal).
Conforme necessário, encomende circuitos adicionais para satisfazer os requisitos de alta disponibilidade.

2.  Siga os passos para [ativar o Serviço de Observação de Pares](https://docs.microsoft.com/azure/internet-peering/howto-peering-service-portal) num esprevamento direto utilizando o portal.




**3. Registe os seus prefixos para encaminhamento otimizado**

Para o encaminhamento otimizado para os prefixos da infraestrutura dos seus serviços de comunicação, deve registar todos os seus prefixos com as suas interligações de observação.
[Registre o Serviço de Observação Azure - portal Azure | Microsoft Docs](https://docs.microsoft.com/azure/peering-service/azure-portal)

A chave prefixo é auto-povoada para Parceiros de Serviço de Comunicações, pelo que o parceiro não precisa de utilizar nenhuma chave de prefixo para se registar. 

Certifique-se de que os prefixos que estão a ser registados estão a ser anunciados sobre as interligações diretas estabelecidas para a região.


## <a name="faqs"></a>FAQs:

**Q.**  Tenho sub-redes menores (</24) para os meus serviços de Comunicação. Posso ter as sub-redes mais pequenas também?

**A.**  Sim, o serviço Microsoft Azure Peering também suporta um encaminhamento de prefixo mais pequeno. Certifique-se de que está a registar os prefixos mais pequenos para o encaminhamento e o mesmo é anunciado ao longo das interligações.

**Q.**  Que rotas da Microsoft vamos receber ao longo destas interligações?

**A.** A Microsoft anuncia todos os prefixos de serviço público da Microsoft sobre estas interligações. Isto garantirá não só comunicações, mas outros serviços em nuvem estão acessíveis a partir da mesma interligação.

**Q.**  Tenho de definir o limite de prefixo, quantas rotas a Microsoft anunciaria?

**A.** A Microsoft anuncia cerca de 280 prefixos na internet, podendo aumentar 10-15% no futuro. Assim, um limite seguro de 400-500 pode ser bom para definir como "Contagem de prefixos Max"

**Q.** A Microsoft irá re-anunciar os prefixos peer para a Internet?

**A.** N.º

**Q.** Há uma taxa para este serviço?

**A.** Não, no entanto, espera-se que peer carregue os custos de ligação cruzada do site.

**Q.** Qual é a velocidade mínima de ligação para uma interligação?

**A.** 10Gbps.

**Q.** O Peer está ligado a um SLA?

**A.** Sim, uma vez que a utilização atinge 40% um processo de aumento de 45-60day LAG deve começar.

**Q.** Qual é a vantagem deste serviço em relação à atual rota de observação direta ou expressa?

**A.** O caminho livre de liquidação e todo o caminho é otimizado para o tráfego de voz sobre o Microsoft WAN e a convergência é sintonizada para sub-segundo com BFD.

**Q.** Como é preciso completar o processo de embarque?

**A.** O tempo será variável dependendo do número e localização dos sites, e se peer estiver migrando os pares privados existentes ou estabelecendo novos cabos. O porta-aviões deve planear mais de 3 semanas.

**Q.** Podemos usar APIs para embarque?

**A.** Atualmente não existe suporte a API, e a configuração deve ser realizada através do portal web. 
