---
title: Integração do gateway de aplicativo com a central de segurança do Azure | Microsoft Docs
description: Esta página fornece informações sobre como o gateway de aplicativo é integrado à central de segurança do Azure.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: f5ecd2334ca80f5561c0611239b5bb00d222112a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277192"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Visão geral da integração entre o gateway de aplicativo e a central de segurança do Azure

Saiba como o gateway de aplicativo e a central de segurança ajudam a proteger os recursos do aplicativo Web. O Firewall do aplicativo Web do gateway de aplicativo (WAF) integra-se à [central de segurança](../security-center/security-center-intro.md) para fornecer uma visão direta para prevenir, detectar e responder a ameaças a aplicativos Web desprotegidos em seu ambiente.

## <a name="overview"></a>Visão geral

O WAF do gateway de aplicativo é uma recomendação na central de segurança para proteger aplicativos Web contra explorações e vulnerabilidades. Recursos da Web habilitados que não são protegidos pelo WAF mostram na central de segurança como recomendações de severidade alta. As recomendações para firewalls do aplicativo Web são mostradas na página **visão geral** , em **aplicativos**.

![integração com a central de segurança][1]

Clicar em qualquer recomendação sobre o Firewall do aplicativo Web abre uma nova página mostrando os detalhes da recomendação.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Adicionar um firewall do aplicativo Web a um recurso existente

Navegue até **todos os serviços** > **segurança + identidade** **central de segurança** > e na **central de segurança-visão geral**, clique em **aplicativos**. Na **central de segurança-aplicativos**, a tabela contém uma lista de aplicativos que a central de segurança detectou em sua assinatura.

![aplicativos Web][3]

Ao clicar em um aplicativo Web com um problema crítico, você obtém a página **integridade da segurança do aplicativo** . Na imagem abaixo, o aplicativo Web que não está protegido por um firewall do aplicativo Web. 

![recursos da Web não protegidos][2]

Clique em **Adicionar um firewall do aplicativo Web** em **recomendações** para abrir a página **Adicionar um firewall do aplicativo Web** .

Se você não tiver um gateway de aplicativo existente ou desejar criar um novo, clique em **criar novo** e em **criar um novo Firewall do aplicativo Web**e clique em **Microsoft-gateway de aplicativo**. Isso o levará pelas etapas para criar um gateway de aplicativo. Neste ponto, seu aplicativo Web é adicionado como um recurso protegido, a central de segurança agora controla que esse recurso está protegido por um firewall do aplicativo Web. Isso não o adiciona como um membro do pool de back-end.

Se você tiver um gateway de aplicativo existente, poderá escolher em **usar solução existente**

![Página para adicionar um firewall do aplicativo Web][4]

A adição de um aplicativo Web a um gateway de aplicativo por meio da central de segurança não adiciona o recurso como um membro do pool de back-end. Isso deve ser feito diretamente no recurso de gateway de aplicativo.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Adicionar um recurso a um firewall do aplicativo Web existente

Navegue até **todos os serviços** > segurança + identidade **central de segurança** > e na **central de segurança-visão geral**, clique em **soluções de parceiros**. Gateways de aplicativo com reconhecimento da central de segurança existentes são mostrados na página de **soluções de parceiros** .

![soluções de parceiros][7]

Clique em **vincular aplicativo** para abrir **vincular aplicativos**, aqui você receberá as opções para selecionar os aplicativos existentes. Escolha os aplicativos a serem protegidos e clique em **OK**. Isso não adiciona o aplicativo Web ao pool de back-end do gateway de aplicativo. Isso define os recursos como um recurso protegido para que a central de segurança possa controlá-lo. Para adicionar o recurso como um membro do pool de back-end, isso deve ser feito no gateway de aplicativo, na página atual, você pode clicar em **console de solução** para ser levado ao recurso de gateway de aplicativo, no qual você pode adicionar o aplicativo Web ao pool de back-end.

![aplicativos de soluções de parceiros][6]

## <a name="finalize-configuration"></a>Finalizar configuração

A central de segurança rastreia os aplicativos adicionados a um gateway de aplicativo como um recurso protegido.  Ele monitora a integridade desse recurso e garante que ele seja protegido por um gateway de aplicativo. A próxima etapa é adicionar o IP privado, o IP público ou a NIC de sua máquina virtual ao pool de back-end do gateway de aplicativo. Até que isso seja feito, uma recomendação adicional de **finalizar a proteção do aplicativo** é mostrada até que o recurso seja adicionado.

![Página para adicionar um firewall do aplicativo Web][5]

## <a name="security-alerts"></a>Alertas de Segurança

Na central de segurança, navegue até **detecção** > **alertas de segurança**.  Aqui você encontra alertas do WAF para seus gateways de aplicativo. Os alertas são divididos por regra de WAF.

![alertas de segurança][8]

A seleção de uma regra fornecerá uma lista de alertas para essa regra WAF específica. Cada alerta mostra detalhes adicionais sobre a localização. Os detalhes fornecem um link para o gateway de aplicativo.
 
![detalhes do alerta][9]

## <a name="next-steps"></a>Passos seguintes

Para saber como habilitar o Firewall do aplicativo Web em um gateway de aplicativo existente, visite [criar ou atualizar um gateway de aplicativo Azure com o Firewall do aplicativo Web](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png