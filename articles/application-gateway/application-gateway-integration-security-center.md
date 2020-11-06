---
title: Integração de Application Gateway com O Centro de Segurança Azure / Microsoft Docs
description: Esta página fornece informações sobre como o Gateway de Aplicação está integrado no Centro de Segurança Azure.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: 1464c0c0b0d573711ed07332a76bb67e73dc0484
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397778"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Visão geral da integração entre o Application Gateway e o Azure Security Center

Saiba como o Application Gateway e o Security Center ajudam a proteger os recursos da sua aplicação web. A firewall da aplicação web de gateway de aplicação de aplicação (WAF) integra-se com [o Security Center](../security-center/security-center-introduction.md) para fornecer uma visão perfeita para prevenir, detetar e responder a ameaças a aplicações web desprotegidas no seu ambiente.

## <a name="overview"></a>Descrição geral

Application Gateway WAF é uma recomendação no Security Center para proteger aplicações web de explorações e vulnerabilidades. Os recursos web que não estão protegidos pela WAF mostram no centro de segurança como recomendações de alta gravidade. As recomendações para firewalls de aplicações web são apresentadas na página **'Visão Geral',** no âmbito das **Aplicações**.

![integração com o centro de segurança][1]

Clicar em quaisquer recomendações relativas ao firewall de aplicações web abre uma nova página mostrando os detalhes da recomendação.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Adicione uma firewall de aplicação web a um recurso existente

Navegar para **todos os serviços**  >  **Security + Identity** Security  >  **Center** e no Security **Center - Visão geral,** clique em **Aplicações**. No **Centro de Segurança - Aplicações,** a tabela contém uma lista de aplicações que o Centro de Segurança detetou na sua subscrição.

![aplicações web][3]

Ao clicar numa aplicação web com um problema crítico, obtém a página de saúde de segurança da **Aplicação.** Na imagem abaixo, a aplicação web que não está protegida por uma firewall de aplicação web. 

![recursos web não protegidos][2]

Clique **em Adicionar uma firewall de aplicação web** sob **recomendações** para abrir a página Adicionar uma firewall **de aplicação web.**

Se não tiver um Gateway de aplicações existente, ou quiser criar um novo, clique em **Criar Novo** e na  **Criação de uma nova Firewall de Aplicação Web** , e clique em Microsoft - Application **Gateway**. Isto leva-o através dos passos para criar uma porta de aplicação. Neste momento, a sua aplicação web é adicionada como um recurso protegido, o Security Center rastreia agora que este recurso está protegido por uma firewall de aplicação web. Isto não o adiciona como membro da piscina de backend.

Se tiver um gateway de aplicações existente, pode escolhê-lo sob **a solução existente**

![Screenshot da página Adicionar uma firewall de aplicação web. Sob a solução existente, é visível um gateway de aplicação.][4]

Adicionar uma aplicação web a uma porta de entrada de aplicação através do Security Center não adiciona o recurso como membro do pool backend. Isto deve ser feito diretamente no recurso de gateway de aplicação.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Adicione um recurso a uma firewall de aplicação web existente

Navegar para **todos os serviços**  >  **Security + Identity** Security  >  **Center** e no Security **Center - Visão geral,** clique em **Soluções partner**. As portas de aplicação do Centro de Segurança existentes mostram-se na página **Soluções de Parceiro.**

![soluções parceiras][7]

Clique **na aplicação Link** para abrir **aplicações de link,** aqui é-lhe dadas as opções para selecionar as aplicações existentes. Escolha as aplicações para proteger e clique **em OK.** Isto não adiciona a aplicação web ao pool de backend do gateway de aplicações. Isto define os recursos como um recurso protegido para que o Centro de Segurança possa rastreá-lo. Para adicionar o recurso como membro do pool backend, este deve ser feito no gateway de aplicações, a partir da página atual pode clicar na **consola 'Solução'** para ser levado para o recurso de gateway de aplicação onde pode adicionar a aplicação web ao pool backend.

![aplicações de soluções parceiras][6]

## <a name="finalize-configuration"></a>Finalizar a configuração

O Centro de Segurança rastreia as aplicações adicionadas a um gateway de aplicação como um recurso protegido.  Monitoriza a saúde deste recurso e garante que está protegido por uma porta de aplicação. O próximo passo é adicionar o IP privado, IP público ou NIC da sua máquina virtual ao pool backend do gateway de aplicações. Até que isto seja feito, é apresentada uma recomendação adicional de proteção da **aplicação finalização** até que o recurso seja adicionado.

![Screenshot da página de proteção da aplicação Finalizar, com uma aplicação visível. O texto explica quais os passos a tomar para proteger a aplicação.][5]

## <a name="security-alerts"></a>Alertas de Segurança

Dentro do Centro **DETECTION** de Segurança navegue para  >  **alertas de segurança de deteção.**  Aqui encontra alertas WAF para os seus gateways de aplicação. Os alertas são desabate-se pela regra da WAF.

![alertas de segurança][8]

A seleção de uma regra fornecerá uma lista de alertas para essa regra específica da WAF. Cada alerta mostra detalhes adicionais sobre a descoberta. Os detalhes fornecem um link para o gateway de aplicação.
 
![detalhes de alerta][9]

## <a name="next-steps"></a>Passos seguintes

Para saber como ativar a firewall de aplicações web em um gateway de aplicações existente, visite [Criar ou atualizar um Gateway de aplicações Azure com firewall de aplicações web](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png