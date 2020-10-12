---
title: Failover em vários pontos finais do Azure CDN com o Traffic Manager
description: Saiba como configurar o failover em vários pontos finais da Rede de Entrega de Conteúdos Azure utilizando o Azure Traffic Manager.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/18/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: b55e418393d6d446ae0d3557f2d1f4cf98d89293
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88192500"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Configurar failover em vários pontos finais da Azure CDN com O Gestor de Tráfego Azure

Ao configurar a Rede de Entrega de Conteúdos Azure (CDN), pode selecionar o fornecedor ideal e o nível de preços para as suas necessidades. O Azure CDN, com a sua infraestrutura distribuída globalmente, por defeito cria redundância local e geográfica e equilíbrio global de carga para melhorar a disponibilidade e desempenho do serviço. Se uma localização não estiver disponível para servir o conteúdo, os pedidos são automaticamente encaminhados para outro local e o POP ideal (baseado em fatores como localização de pedido e carga do servidor) é usado para servir cada pedido do cliente. 
 
Se tiver vários perfis de CDN, pode melhorar ainda mais a disponibilidade e o desempenho com o Azure Traffic Manager. Pode utilizar o Azure Traffic Manager com a Azure CDN para carregar o equilíbrio entre vários pontos finais do CDN para o failover, o equilíbrio geo-carga e outros cenários. Num cenário típico de failover, todos os pedidos do cliente são primeiro direcionados para o perfil principal da CDN; se o perfil não estiver disponível, os pedidos são então transmitidos para o perfil de CDN secundário até que o seu perfil principal de CDN esteja novamente online. A utilização do Azure Traffic Manager garante assim que a sua aplicação web está sempre disponível. 

Este artigo fornece orientação e um exemplo de como configurar failover com **Azure CDN Standard da Verizon** e **Azure CDN Standard a partir de** perfis Akamai.

## <a name="set-up-azure-cdn"></a>Configurar a Azure CDN 
Crie dois ou mais perfis de CDN Azure e pontos finais com diferentes fornecedores.

1. Crie um **Padrão Azure CDN a partir de Verizon** e **Azure CDN Standard a partir do** perfil da Akamai, seguindo os passos na [Criação de um novo perfil CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CdN vários perfis](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Em cada um dos novos perfis, crie pelo menos um ponto final seguindo os passos na [Criação de um novo ponto final cdn](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Configurar gestor de tráfego Azure
Crie um perfil de Gestor de Tráfego Azure e confiem o equilíbrio de carga nos seus pontos finais cdn. 

1. Crie um perfil de Gestor de Tráfego Azure seguindo os passos no [perfil Criar um Gestor de Tráfego.](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile) 

    Para **o método de encaminhamento**, selecione **Priority**.

2. Adicione os seus pontos finais cdn no seu perfil de Gestor de Tráfego seguindo os passos nos [pontos finais do Add Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    Para **tipo**, selecione **pontos finais externos**. Para **prioridade,** insira um número.

    Por exemplo, criar *cdndemo101akamai.azureedge.net* com uma prioridade de *1* e *cdndemo101verizon.azureedge.net* com uma prioridade de *2*.

   ![Pontos finais do gestor de tráfego da CDN](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Configurar o domínio personalizado em Azure CDN e Azure Traffic Manager
Depois de configurar os perfis do CDN e do Traffic Manager, siga estes passos para adicionar mapeamento DNS e registar o domínio personalizado nos pontos finais do CDN. Para este exemplo, o nome de domínio personalizado é *cdndemo101.dustydogpetcare.online*.

1. Vá ao site para o fornecedor de domínio do seu domínio personalizado, como o GoDaddy, e crie duas entradas DNS CNAME. 

    a. Para a primeira entrada CNAME, mapeeeia o seu domínio personalizado, com o subdomínio cdnverify, para o seu ponto final CDN. Esta entrada é um passo necessário para registar o domínio personalizado no ponto final do CDN que adicionou ao Traffic Manager no passo 2.

      Por exemplo: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Para a segunda entrada CNAME, mapeeeia o seu domínio personalizado, sem o subdomínio cdnverificar, para o seu ponto final CDN. Esta entrada mapeia o domínio personalizado para O Gestor de Tráfego. 

      Por exemplo: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Se o seu domínio estiver atualmente ao vivo e não puder ser interrompido, faça este passo por último. Verifique se os pontos finais do CDN e os domínios do gestor de tráfego estão ao vivo antes de atualizar o seu domínio personalizado DNS para Traffic Manager.
    >


2.  A partir do seu perfil Azure CDN, selecione o primeiro ponto final CDN (Akamai). **Selecione Adicionar domínio personalizado** e inserir *cdndemo101.dustydogpetcare.online*. Verifique se a marca de verificação para validar o domínio personalizado é verde. 

    O Azure CDN utiliza o subdomínio *cdnverify* para validar o mapeamento DNS para completar este processo de registo. Para obter mais informações, consulte [Criar um registo DE DNS CNAME](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Este passo permite ao Azure CDN reconhecer o domínio personalizado para que possa responder aos seus pedidos.
    
    > [!NOTE]
    > Para ativar o TLS num **CDN Azure a partir dos** perfis da Akamai, tem de cnamar diretamente o domínio personalizado para o seu ponto final. cdnverify para permitir tLS ainda não é suportado. 
    >

3.  Volte ao site para o fornecedor de domínio do seu domínio personalizado e atualize o primeiro mapeamento DNS criado para que o domínio personalizado seja mapeado para o seu segundo ponto final CDN.
                             
    Por exemplo: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. A partir do seu perfil Azure CDN, selecione o segundo ponto final CDN (Verizon) e repita o passo 2. **Selecione Adicionar domínio personalizado**e inserir *cdndemo101.dustydogpetcare.online*.
 
Depois de completar estes passos, o seu serviço multi-CDN com capacidades de failover é configurado com O Gestor de Tráfego Azure. Poderá aceder aos URLs de teste a partir do seu domínio personalizado. Para testar a funcionalidade, desative o ponto final primário do CDN e verifique se o pedido é corretamente transferido para o ponto final secundário do CDN. 

## <a name="next-steps"></a>Passos seguintes
Também pode configurar outros métodos de encaminhamento, como geográficos, para equilibrar a carga entre diferentes pontos finais cdn. Para obter mais informações, consulte [configurar o método de encaminhamento de tráfego geográfico utilizando o Gestor de Tráfego.](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)



