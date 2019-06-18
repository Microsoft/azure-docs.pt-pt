---
title: Integração de Gateway de aplicação com o Centro de segurança do Azure | Documentos da Microsoft
description: Esta página fornece informações sobre como o Gateway de aplicação se integra ao centro de segurança do Azure.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: victorh
ms.openlocfilehash: 10f115b64f0bd3f7e557da2bedbf3327d0ef483d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122302"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Descrição geral da integração entre o Gateway de aplicação e o Centro de segurança do Azure

Saiba como o Gateway de aplicação e o Centro de segurança ajudam a proteger os seus recursos de aplicação web. Firewall de aplicações da web de gateway de aplicação (WAF) integra [Centro de segurança](../security-center/security-center-intro.md) para fornecer uma vista homogénea para evitar, detetar e responder a ameaças de aplicações web desprotegidas no seu ambiente.

## <a name="overview"></a>Descrição geral

WAF do Gateway de aplicação é uma recomendação no Centro de segurança para proteger aplicações web contra vulnerabilidades e exploits. Mostrar recursos da Web ativada que não estão protegida pela WAF no Centro de segurança, como recomendações de gravidade elevada. Recomendações para firewalls de aplicações web são mostradas a **descrição geral** página, em **aplicativos**.

![integração com o Centro de segurança][1]

Clicar em quaisquer recomendações sobre firewall de aplicações web abre uma nova página que mostra os detalhes da recomendação.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Adicionar uma firewall de aplicações web para um recurso existente

Navegue para **todos os serviços** > **segurança + identidade** > **Centro de segurança** e, no **Centro de segurança - descrição geral do**, clique em **aplicativos**. No **Centro de segurança - aplicativos**, a tabela contém uma lista de aplicações que o Centro de segurança detetado na sua subscrição.

![Aplicações Web][3]

Ao clicar num aplicativo web com um problema crítico, obtém a **estado de funcionamento de segurança de aplicação** página. Na imagem abaixo, o aplicativo web que não está protegido por uma firewall de aplicações web. 

![recursos da Web não protegida][2]

Clique em **adicionar uma firewall de aplicações web** sob **recomendações** para abrir o **adicionar uma Firewall de aplicações Web** página.

Se não tiver um Gateway de aplicação existente, ou pretende criar uma nova, clique em **criar novo** e, no **criar uma nova Firewall de aplicações Web**e clique em **Microsoft - Gateway de aplicação** . Isto leva-o pelos passos para criar um gateway de aplicação. Neste momento, seu aplicativo web é adicionado como um recurso protegido, o Centro de segurança agora controla que este recurso está protegido por uma firewall de aplicações web. Isso não adicioná-lo como um membro do conjunto de back-end.

Se tiver um gateway de aplicação existente, pode selecioná-la em **utilizar solução existente**

![página de adicionar firewall de aplicações Web][4]

Adicionar uma aplicação web para um gateway de aplicação através do Centro de segurança não adiciona o recurso como um membro do conjunto de back-end. Isso deve ser feito no recurso de gateway de aplicação diretamente.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Adicionar um recurso para um firewall de aplicações web existente

Navegue para **todos os serviços** > **segurança + identidade** > **Centro de segurança** e, no **Centro de segurança - descrição geral do**, clique em **soluções de parceiros**. Os gateways de aplicação com suporte para o Centro de segurança existentes mostram os **soluções de parceiros** página.

![Soluções de parceiros][7]

Clique em **ligar aplicação** para abrir **ligar aplicações**, aqui tem as opções para selecionar os aplicativos existentes. Escolher as aplicações para proteger e clique em **OK**. Isso não adiciona a aplicação web para o conjunto de back-end do gateway de aplicação. Isso define os recursos como um recurso protegido para que o Centro de segurança pode controlá-lo. Para adicionar o recurso como um membro do conjunto de back-end, isso deve ser feito no gateway de aplicação, a partir da página atual, pode clicar em **consola de soluções** para ir para o recurso de gateway de aplicação onde pode adicionar a aplicação web para o conjunto de back-end.

![aplicações de soluções de parceiros][6]

## <a name="finalize-configuration"></a>Finalizar a configuração

Centro de segurança controla os aplicativos adicionados a um gateway de aplicação como um recurso protegido.  Ele monitora o estado de funcionamento deste recurso e garante que está protegido por um gateway de aplicação. A próxima etapa é adicionar o IP privado, o IP público ou o NIC da sua máquina virtual para o conjunto de back-end do gateway de aplicação. Até que isso é feito uma recomendação adicional do **finalizar proteção de aplicação** é mostrado até que o recurso seja adicionado.

![página de adicionar firewall de aplicações Web][5]

## <a name="security-alerts"></a>Alertas de segurança

No Centro de segurança, navegue até **detecção** > **alertas de segurança**.  Aqui, poderá encontrar alertas de WAF dos seus gateways de aplicação. Alertas são divididos por regra de WAF.

![Alertas de segurança][8]

Clicar numa regra de fornecerá uma lista de alertas para essa regra específica do WAF. Cada alerta mostra detalhes adicionais sobre como a localizar. Os detalhes fornecem uma ligação para o gateway de aplicação.
 
![Detalhes do alerta][9]

## <a name="next-steps"></a>Passos Seguintes

Para saber como ativar a firewall de aplicações web num gateway de aplicação existente, visite [criar ou atualizar um Gateway de aplicação do Azure com a firewall de aplicações web](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png