---
title: Configurar ativação pós-falha em vários pontos finais da CDN do Azure com o Gestor de tráfego do Azure | Documentos da Microsoft
description: Saiba mais sobre como configurar o Gestor de tráfego do Azure com pontos finais da CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: afadef8b29927f909af5be1e1204180724258b74
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58167070"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Configurar ativação pós-falha em vários pontos finais da CDN do Azure com o Gestor de tráfego do Azure

Quando configura a rede de entrega de conteúdos (CDN), pode selecionar o fornecedor ideal e o escalão de preço para as suas necessidades. A CDN do Azure, com a sua infra-estrutura de distribuição global, por predefinição cria redundância geográfica e local e para melhorar o desempenho e disponibilidade do serviço de balanceamento de carga global. Se uma localização não está disponível para servir de conteúdo, automaticamente os pedidos são encaminhados para outra localização e o POP ideal (com base em fatores como carga de localização e o servidor de solicitação) é usado para servir de cada pedido de cliente. 
 
Se tiver vários perfis da CDN, pode melhorar ainda mais a disponibilidade e desempenho com o Gestor de tráfego do Azure. Pode utilizar o Gestor de tráfego do Azure com a CDN do Azure para carregar o equilíbrio entre vários pontos finais de CDN para ativação pós-falha, geo-carga balanceamento e outros cenários. Num cenário típico de ativação pós-falha, todos os pedidos de cliente primeiro são direcionados para o perfil CDN primário; Se o perfil não estiver disponível, pedidos, em seguida, são transmitidos para o perfil CDN secundário, até que o perfil de CDN principal esteja novamente online. Utilizar o Gestor de tráfego do Azure desta forma garante que seu aplicativo web está sempre disponível. 

Este artigo fornece orientações e um exemplo de como configurar a ativação pós-falha com **CDN do Azure Standard da Verizon** e **CDN do Azure Standard da Akamai** perfis.

## <a name="set-up-azure-cdn"></a>Configurar a CDN do Azure 
Crie duas ou mais perfis de CDN do Azure e pontos de extremidade com provedores diferentes.

1. Criar uma **CDN do Azure Standard da Verizon** e **CDN do Azure Standard da Akamai** perfil ao seguir os passos [criar um novo perfil CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN vários perfis](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Em cada um dos perfis de novo, criar, pelo menos, um ponto final ao seguir os passos em [criar um novo ponto final da CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Configure o Gestor de tráfego do Azure
Criar um perfil do Gestor de tráfego do Azure e configurar o balanceamento de carga entre os pontos finais CDN. 

1. Criar um perfil do Gestor de tráfego do Azure ao seguir os passos em [criar um perfil do Gestor de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    Para **método de encaminhamento**, selecione **prioridade**.

2. Adicionar os pontos finais CDN no seu perfil do Gestor de tráfego ao seguir os passos no [pontos finais de adicionar o Gestor de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    Para **tipo**, selecione **pontos finais externos**. Para **prioridade**, introduza um número.

    Por exemplo, crie *cdndemo101akamai.azureedge.net* com prioridade *1* e *cdndemo101verizon.azureedge.net* com uma prioridade de *2*.

   ![Pontos finais do Gestor de tráfego da CDN](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Configurar domínio personalizado da CDN do Azure e o Gestor de tráfego do Azure
Depois de configurar os perfis de CDN e o Gestor de tráfego, siga estes passos para adicionar o mapeamento de DNS e registar o domínio personalizado para os pontos finais CDN. Neste exemplo, é o nome de domínio personalizado *cdndemo101.dustydogpetcare.online*.

1. Vá para o web site para o fornecedor de domínio do seu domínio personalizado, como a GoDaddy e criar duas entradas CNAME no DNS. 

    a. Para a primeira entrada CNAME, mapear o seu domínio personalizado, com o subdomínio cdnverify, para o ponto final da CDN. Esta entrada é uma etapa necessária para registar o domínio personalizado para o ponto final da CDN que foi adicionado ao Gestor de tráfego no passo 2.

      Por exemplo: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Para a segunda entrada CNAME, mapear o seu domínio personalizado, sem o subdomínio cdnverify, para o ponto final da CDN. Esta entrada mapeia o domínio personalizado para o Gestor de tráfego. 

      Por exemplo: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Se o seu domínio estiver atualmente em direto e não pode ser interrompido, execute este passo pela última vez. Certifique-se de que os pontos finais CDN e domínios de Gestor de tráfego em direto antes de atualizar o DNS de domínio personalizado para o Gestor de tráfego.
    >


2.  A partir do seu perfil da CDN do Azure, selecione o primeiro ponto final da CDN (Akamai). Selecione **Adicionar domínio personalizado** e entrada *cdndemo101.dustydogpetcare.online*. Certifique-se de que a marca de verificação para validar o domínio personalizado está verde. 

    A CDN do Azure utiliza o *cdnverify* subdomínio para validar o mapeamento de DNS para concluir este processo de registo. Para obter mais informações, consulte [criar um registo CNAME DNS](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Este passo ativa o CDN do Azure para reconhecer o domínio personalizado para que ele pode responder a suas solicitações.

3.  Regressar ao site da web para o fornecedor de domínio do seu domínio personalizado e atualize o mapeamento de DNS primeiro que criou para que o domínio personalizado está mapeado para o segundo ponto final da CDN.
                             
    Por exemplo: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. A partir do seu perfil da CDN do Azure, selecione o ponto de final CDN segundo (Verizon) e repita o passo 2. Selecione **Adicionar domínio personalizado**e entrada *cdndemo101.dustydogpetcare.online*.
 
Depois de concluir estes passos, seu serviço com vários CDNS com capacidades de ativação pós-falha é configurado com o Gestor de tráfego do Azure. Poderá acessar o teste de URLs do seu domínio personalizado. Para testar a funcionalidade, desative o ponto de final CDN primário e certifique-se de que o pedido corretamente será movido para o ponto de final CDN secundário. 

## <a name="next-steps"></a>Passos Seguintes
Também pode configurar outros métodos de encaminhamento, tais como geográfica, para equilibrar a carga entre diferentes pontos finais de CDN. Para obter mais informações, consulte [configurar o método de encaminhamento de tráfego geográfico utilizando o Gestor de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



