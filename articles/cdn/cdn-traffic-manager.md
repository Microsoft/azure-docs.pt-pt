---
title: Falha em vários pontos finais com o Traffic Manager
titleSuffix: Azure Content Delivery Network
description: Aprenda a configurar o failover através de vários pontos finais da Rede de Entrega de Conteúdos Azure utilizando o Azure Traffic Manager.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 10/08/2020
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: a003becba0bc1e42d8fe0c0c5b199402a430a8e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034763"
---
# <a name="failover-across-multiple-endpoints-with-azure-traffic-manager"></a>Falha em vários pontos finais com Azure Traffic Manager

Ao configurar a Rede de Entrega de Conteúdos Azure (CDN), pode selecionar o fornecedor ideal e o nível de preços para as suas necessidades. 

O Azure CDN, com a sua infraestrutura distribuída globalmente, por defeito cria redundância local e geográfica e equilíbrio global de carga para melhorar a disponibilidade e desempenho do serviço. 

Se um local não estiver disponível para servir o conteúdo, os pedidos são automaticamente encaminhados para outro local. O ponto ideal de presença (POP) é usado para servir cada pedido do cliente. O encaminhamento automático baseia-se em fatores como localização de pedido e carga do servidor.
 
Se tiver vários perfis de CDN, pode melhorar ainda mais a disponibilidade e o desempenho com o Azure Traffic Manager. 

Utilize o Gestor de Tráfego Azure com a Azure CDN para carregar o equilíbrio entre vários pontos finais cdn para:
 
* Ativação pós-falha
* Equilíbrio geo-carga 

Num cenário típico de failover, todos os pedidos do cliente são direcionados para o perfil principal da CDN. 

Se o perfil não estiver disponível, os pedidos são direcionados para o perfil secundário.  Os pedidos retomam o seu perfil principal quando voltar a estar online.

A utilização do Azure Traffic Manager garante assim que a sua aplicação web está sempre disponível. 

Este artigo fornece orientação e um exemplo de como configurar o failover com perfis de: 

* **Azure CDN Standard de Verizon**
* **Azure CDN Standard da Akamai**

**O Azure CDN da Microsoft** também é suportado.

## <a name="create-azure-cdn-profiles"></a>Criar perfis de CDN Azure
Crie dois ou mais perfis de CDN Azure e pontos finais com diferentes fornecedores.

1. Criar dois perfis CDN:
    * **Azure CDN Standard de Verizon**
    * **Azure CDN Standard da Akamai** 

    Crie os perfis seguindo os passos na [Criação de um novo perfil CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CdN vários perfis](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Em cada um dos novos perfis, crie pelo menos um ponto final seguindo os passos na [Criação de um novo ponto final cdn](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="create-traffic-manager-profile"></a>Criar perfil de gestor de tráfego
Crie um perfil de Gestor de Tráfego Azure e configuure a carga equilibrando os seus pontos finais cdn. 

1. Crie um perfil de Gestor de Tráfego Azure seguindo os passos no [perfil Criar um Gestor de Tráfego.](../traffic-manager/quickstart-create-traffic-manager-profile.md) 

    * **Método de encaminhamento**, selecione **Priority**.

2. Adicione os seus pontos finais cdn no seu perfil de Gestor de Tráfego seguindo os passos nos [pontos finais do Add Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md#add-traffic-manager-endpoints)

    * **Tipo**, selecione **pontos finais externos**.
    * **Prioridade,** insira um número.

    Por exemplo, criar **cdndemo101akamai.azureedge.net** com uma prioridade de **1** e **cdndemo101verizon.azureedge.net** com uma prioridade de **2**.

   ![Pontos finais do gestor de tráfego da CDN](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="configure-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Configure o domínio personalizado em Azure CDN e Azure Traffic Manager
Depois de configurar os perfis do CDN e do Traffic Manager, siga estes passos para adicionar mapeamento DNS e registar o domínio personalizado nos pontos finais do CDN. Para este exemplo, o nome de domínio personalizado é **cdndemo101.dustydogpetcare.online**.

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
   
    > [!NOTE]
    > Para o empículo desta falha sobre o scenerio, ambos os pontos finais precisam de estar em perfis diferentes, e os diferentes perfis devem ser por diferentes fornecedores de CDN para evitar conflitos de nomes de domínio.
    > 

2.  A partir do seu perfil Azure CDN, selecione o primeiro ponto final CDN (Akamai). **Selecione Adicione o domínio personalizado** e a entrada **cdndemo101.dustydogpetcare.online**. Verifique se a marca de verificação para validar o domínio personalizado é verde. 

    O Azure CDN utiliza o subdomínio **cdnverify** para validar o mapeamento DNS para completar este processo de registo. Para obter mais informações, consulte [Criar um registo DE DNS CNAME](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Este passo permite ao Azure CDN reconhecer o domínio personalizado para que possa responder aos seus pedidos.
    
    > [!NOTE]
    > Para ativar o TLS num **CDN Azure a partir dos** perfis da Akamai, tem de cnamar diretamente o domínio personalizado para o seu ponto final. cdnverify para permitir tLS ainda não é suportado. 
    >

3.  Volte ao site para o fornecedor de domínio do seu domínio personalizado. Atualize o primeiro mapeamento DNS que criou. Mapear o domínio personalizado para o seu segundo ponto final CDN.
                             
    Por exemplo: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. A partir do seu perfil Azure CDN, selecione o segundo ponto final CDN (Verizon) e repita o passo 2. **Selecione Adicionar domínio personalizado** e introduza **cdndemo101.dustydogpetcare.online**.
 
Depois de completar estes passos, o seu serviço multi-CDN com capacidades de failover é configurado com O Gestor de Tráfego Azure. 

Pode aceder aos URLs de teste a partir do seu domínio personalizado. 

Para testar a funcionalidade, desative o ponto final primário do CDN e verifique se o pedido é corretamente transferido para o ponto final secundário do CDN. 

## <a name="next-steps"></a>Passos seguintes
Pode configurar outros métodos de encaminhamento, como geográficos, para equilibrar a carga entre diferentes pontos finais cdn. 

Para obter mais informações, consulte [configurar o método de encaminhamento de tráfego geográfico utilizando o Gestor de Tráfego.](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)
