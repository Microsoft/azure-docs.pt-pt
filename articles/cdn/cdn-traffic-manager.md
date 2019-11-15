---
title: Failover entre vários pontos de extremidade da CDN do Azure com o Gerenciador de tráfego
description: Saiba mais sobre como configurar o Gerenciador de tráfego do Azure com pontos de extremidade da CDN do Azure.
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
ms.openlocfilehash: de91f61385942db077bc98721eabe9f3f0b8624c
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083008"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Configurar o failover em vários pontos de extremidade da CDN do Azure com o Gerenciador de tráfego do Azure

Ao configurar a CDN (rede de distribuição de conteúdo) do Azure, você pode selecionar o provedor ideal e o tipo de preço para suas necessidades. Por padrão, a CDN do Azure, com sua infraestrutura distribuída globalmente, cria redundância local e geográfica e balanceamento de carga global para melhorar a disponibilidade e o desempenho do serviço. Se um local não estiver disponível para fornecer conteúdo, as solicitações serão roteadas automaticamente para outro local e o POP ideal (com base em fatores como o local da solicitação e a carga do servidor) será usado para atender a cada solicitação do cliente. 
 
Se você tiver vários perfis CDN, poderá melhorar ainda mais a disponibilidade e o desempenho com o Gerenciador de tráfego do Azure. Você pode usar o Gerenciador de tráfego do Azure com a CDN do Azure para balancear a carga entre vários pontos de extremidade CDN para failover, balanceamento de carga geográfica e outros cenários. Em um cenário típico de failover, todas as solicitações de cliente são direcionadas primeiro para o perfil CDN primário; Se o perfil não estiver disponível, as solicitações serão passadas para o perfil da CDN secundária até que o perfil da CDN principal fique online novamente. Usando o Gerenciador de tráfego do Azure dessa forma, você garante que seu aplicativo Web esteja sempre disponível. 

Este artigo fornece orientações e um exemplo de como configurar o failover com a **CDN standard do Azure da Verizon** e o **Azure CDN Standard de perfis do Akamai** .

## <a name="set-up-azure-cdn"></a>Configurar a CDN do Azure 
Crie dois ou mais perfis de CDN do Azure e pontos de extremidade com provedores diferentes.

1. Crie um perfil do **Azure CDN standard do Verizon** e do **Azure CDN standard do Akamai** seguindo as etapas em [criar um novo perfil de CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![Vários perfis da CDN](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Em cada um dos novos perfis, crie pelo menos um ponto de extremidade seguindo as etapas em [criar um novo ponto de extremidade CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Configurar o Gerenciador de tráfego do Azure
Crie um perfil do Gerenciador de tráfego do Azure e configure o balanceamento de carga em seus pontos de extremidade CDN. 

1. Crie um perfil do Gerenciador de tráfego do Azure seguindo as etapas em [criar um perfil do Gerenciador de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    Para o **método de roteamento**, selecione **prioridade**.

2. Adicione seus pontos de extremidade CDN em seu perfil do Gerenciador de tráfego seguindo as etapas em [Adicionar pontos de extremidade do Gerenciador de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    Para **tipo**, selecione **pontos de extremidade externos**. Para **prioridade**, insira um número.

    Por exemplo, crie *cdndemo101akamai.azureedge.net* com uma prioridade de *1* e *cdndemo101verizon.azureedge.net* com uma prioridade de *2*.

   ![Pontos de extremidade do Gerenciador de tráfego da CDN](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Configurar o domínio personalizado na CDN do Azure e no Gerenciador de tráfego do Azure
Depois de configurar seus perfis de CDN e do Gerenciador de tráfego, siga estas etapas para adicionar o mapeamento de DNS e registrar o domínio personalizado nos pontos de extremidade da CDN. Para este exemplo, o nome de domínio personalizado é *cdndemo101. dustydogpetcare. online*.

1. Vá para o site do provedor de domínio do seu domínio personalizado, como GoDaddy, e crie duas entradas DNS CNAME. 

    a. Para a primeira entrada CNAME, mapeie seu domínio personalizado, com o subdomínio cdnverify, para o ponto de extremidade da CDN. Essa entrada é uma etapa necessária para registrar o domínio personalizado no ponto de extremidade da CDN que você adicionou ao Gerenciador de tráfego na etapa 2.

      Por exemplo: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Para a segunda entrada CNAME, mapeie seu domínio personalizado, sem o subdomínio cdnverify, para o ponto de extremidade da CDN. Essa entrada mapeia o domínio personalizado para o Gerenciador de tráfego. 

      Por exemplo: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Se o seu domínio estiver atualmente ativo e não puder ser interrompido, execute esta etapa por último. Verifique se os pontos de extremidade CDN e os domínios do Traffic Manager estão ativos antes de atualizar o DNS do domínio personalizado para o Gerenciador de tráfego.
    >


2.  Em seu perfil de CDN do Azure, selecione o primeiro ponto de extremidade CDN (Akamai). Selecione **Adicionar domínio personalizado** e insira *cdndemo101. dustydogpetcare. online*. Verifique se a marca de seleção para validar o domínio personalizado está verde. 

    A CDN do Azure usa o subdomínio *cdnverify* para validar o mapeamento de DNS para concluir esse processo de registro. Para obter mais informações, consulte [criar um registro DNS CNAME](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Esta etapa permite que a CDN do Azure reconheça o domínio personalizado para que ele possa responder às suas solicitações.
    
    > [!NOTE]
    > Para habilitar o SSL em um **Azure CDN do Akamai** Profiles, você deve fazer o CNAME direto do domínio personalizado para seu ponto de extremidade. Ainda não há suporte para o cdnverify para habilitar o SSL. 
    >

3.  Retorne ao site do provedor de domínio do seu domínio personalizado e atualize o primeiro mapeamento DNS criado no para que o domínio personalizado seja mapeado para o segundo ponto de extremidade da CDN.
                             
    Por exemplo: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. No perfil da CDN do Azure, selecione o segundo ponto de extremidade CDN (Verizon) e repita a etapa 2. Selecione **Adicionar domínio personalizado**e insira *cdndemo101. dustydogpetcare. online*.
 
Depois de concluir essas etapas, o serviço de várias CDN com recursos de failover será configurado com o Gerenciador de tráfego do Azure. Você poderá acessar as URLs de teste de seu domínio personalizado. Para testar a funcionalidade, desabilite o ponto de extremidade da CDN primária e verifique se a solicitação foi movida corretamente para o ponto de extremidade da CDN secundária. 

## <a name="next-steps"></a>Passos seguintes
Você também pode configurar outros métodos de roteamento, como geográfico, para balancear a carga entre diferentes pontos de extremidade da CDN. Para obter mais informações, consulte [Configurar o método de roteamento de tráfego geográfico usando o Gerenciador de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



