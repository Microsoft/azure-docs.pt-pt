---
title: Failover em vários pontos finais azure CDN com Gestor de Tráfego
description: Saiba como configurar o Gestor de Tráfego Azure com pontos finais azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 8d44e53520481e4ada5c2f16f0c56a4a6a724ec1
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892483"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Configurar falha em vários pontos finais do Azure CDN com o Gestor de Tráfego Azure

Quando configurar a Rede de Entrega de Conteúdos Azure (CDN), pode selecionar o fornecedor ideal e o nível de preços para as suas necessidades. A Azure CDN, com a sua infraestrutura distribuída globalmente, por padrão cria redundância local e geográfica e equilíbrio de carga global para melhorar a disponibilidade e desempenho do serviço. Se uma localização não estiver disponível para servir conteúdo, os pedidos são automaticamente encaminhados para outro local e o POP ideal (com base em fatores como a localização do pedido e a carga do servidor) é usado para atender cada pedido de cliente. 
 
Se tiver vários perfis de CDN, poderá melhorar ainda mais a disponibilidade e o desempenho com o Gestor de Tráfego azure. Pode utilizar o Azure Traffic Manager com o Azure CDN para carregar o equilíbrio entre vários pontos finais da CDN para a falha, o equilíbrio geo-carregador e outros cenários. Num cenário típico de failover, todos os pedidos de cliente são direcionados primeiro para o perfil cdn primário; se o perfil não estiver disponível, os pedidos são então transmitidos para o perfil secundário de CDN até que o seu perfil cdn primário esteja novamente on-line. A utilização do Gestor de Tráfego Azure desta forma garante que a sua aplicação web está sempre disponível. 

Este artigo fornece orientação e um exemplo de como configurar o failover com **o Azure CDN Standard da Verizon** e **Azure CDN Standard a partir de** perfis Akamai.

## <a name="set-up-azure-cdn"></a>Configurar O CDN Azure 
Crie dois ou mais perfis de CDN Azure e pontos finais com diferentes fornecedores.

1. Crie um **Padrão CDN Azure a partir de Verizon** e **Azure CDN Standard a partir do** perfil akamai seguindo os passos em Criar um novo perfil [CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![Perfis múltiplos da CDN](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Em cada um dos novos perfis, crie pelo menos um ponto final seguindo os passos em [Criar um novo ponto final do CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Criar o Gestor de Tráfego Azure
Crie um perfil de Gestor de Tráfego Azure e crie o equilíbrio de carga nos seus pontos finais da CDN. 

1. Crie um perfil de Gestor de Tráfego Azure seguindo os passos no [perfil Criar um Gestor](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile)de Tráfego . 

    Para **o método de encaminhamento,** selecione **Prioridade**.

2. Adicione os seus pontos finais cdN no seu perfil de Gestor de Tráfego seguindo os passos em [Add Traffic Manager endpoints](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    Para **Tipo,** selecione **pontos finais externos**. Para **Prioridade,** insira um número.

    Por exemplo, criar *cdndemo101akamai.azureedge.net* com prioridade de *1* e *cdndemo101verizon.azureedge.net* com prioridade de *2*.

   ![Pontos finais do gestor de tráfego da CDN](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Configurar domínio personalizado em Azure CDN e Azure Traffic Manager
Depois de configurar os perfis de CDN e Traffic Manager, siga estes passos para adicionar mapeamento DNS e registar domínio personalizado nos pontos finais do CDN. Para este exemplo, o nome de domínio personalizado é *cdndemo101.dustydogpetcare.online*.

1. Vá ao site para o fornecedor de domínio do seu domínio personalizado, como goDaddy, e crie duas entradas DNS CNAME. 

    a. Para a primeira entrada CNAME, mapeie o seu domínio personalizado, com o subdomínio cdncheck, para o seu ponto final cdN. Esta entrada é um passo necessário para registar o domínio personalizado no ponto final da CDN que adicionou ao Traffic Manager no passo 2.

      Por exemplo: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Para a segunda entrada CNAME, mapeie o seu domínio personalizado, sem o subdomínio de cdncheck, para o seu ponto final cdN. Esta entrada mapeia o domínio personalizado para o Gestor de Tráfego. 

      Por exemplo: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Se o seu domínio estiver atualmente ao vivo e não puder ser interrompido, faça este passo por último. Verifique se os pontos finais da CDN e os domínios do gestor de tráfego estão ao vivo antes de atualizar o seu dNS de domínio personalizado para o Traffic Manager.
    >


2.  A partir do seu perfil Azure CDN, selecione o primeiro ponto final do CDN (Akamai). Selecione **Adicionar domínio personalizado** e entrada *cdndemo101.dustydogpetcare.online*. Verifique se a marca de verificação para validar o domínio personalizado é verde. 

    O Azure CDN utiliza o subdomínio *cdncheck* para validar o mapeamento dNS para completar este processo de registo. Para mais informações, consulte [Criar um registo CNAME DNS](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Este passo permite ao Azure CDN reconhecer o domínio personalizado para que possa responder aos seus pedidos.
    
    > [!NOTE]
    > Para ativar o TLS num **CDN Azure a partir de** perfis Akamai, deve indicar diretamente o domínio personalizado ao seu ponto final. Cdnverificar para permitir o TLS ainda não é suportado. 
    >

3.  Volte ao site para o fornecedor de domínio do seu domínio personalizado e atualize o primeiro mapeamento DNS que criou para que o domínio personalizado seja mapeado para o seu segundo ponto final do CDN.
                             
    Por exemplo: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. A partir do seu perfil Azure CDN, selecione o segundo ponto final do CDN (Verizon) e repita o passo 2. Selecione **Adicionar domínio personalizado**, e entrada *cdndemo101.dustydogpetcare.online*.
 
Depois de completar estes passos, o seu serviço multi-CDN com capacidades de failover é configurado com o Azure Traffic Manager. Poderá aceder aos URLs de teste do seu domínio personalizado. Para testar a funcionalidade, desative o ponto final do CDN primário e verifique se o pedido é corretamente transferido para o ponto final secundário do CDN. 

## <a name="next-steps"></a>Passos seguintes
Também pode configurar outros métodos de encaminhamento, como o geográfico, para equilibrar a carga entre diferentes pontos finais de CDN. Para mais informações, consulte [Configure o método de encaminhamento de tráfego geográfico utilizando o Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



