---
title: Integração de Gateway de aplicações com o Azure Security Center [ Microsoft Docs
description: Esta página fornece informações sobre como o Application Gateway é integrado no Centro de Segurança Azure.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: f5ecd2334ca80f5561c0611239b5bb00d222112a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277192"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Visão geral da integração entre application Gateway e Azure Security Center

Saiba como o Application Gateway e o Security Center ajudam a proteger os seus recursos de aplicação web. A firewall de aplicação web de gateway de aplicação (WAF) integra-se com o [Security Center](../security-center/security-center-intro.md) para fornecer uma visão perfeita para prevenir, detetar e responder a ameaças a aplicações web desprotegidas no seu ambiente.

## <a name="overview"></a>Descrição geral

Application Gateway WAF é uma recomendação no Security Center para proteger aplicações web de explorações e vulnerabilidades. Recursos ativados pela Web que não são protegidos pela WAF mostram no centro de segurança como recomendações de alta gravidade. As recomendações para firewalls de aplicações web são apresentadas na página **'Visão Geral',** no âmbito das **Aplicações**.

![integração com centro de segurança][1]

Clicar em quaisquer recomendações relativas à firewall da aplicação web abre uma nova página mostrando os detalhes da recomendação.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Adicione uma firewall de aplicação web a um recurso existente

Navegue para **todos os serviços** > Segurança + Centro de**Segurança** **de Identidade** > e no Centro de **Segurança - Visão geral,** clique em **Aplicações**. No Centro de **Segurança - Aplicações,** a tabela contém uma lista de aplicações que o Security Center detetou na sua subscrição.

![aplicações web][3]

Ao clicar numa aplicação web com um problema crítico, obtém-se a página de saúde de segurança da **Aplicação.** Na imagem abaixo, a aplicação web que não está protegida por uma firewall de aplicação web. 

![recursos web não protegidos][2]

Clique em Adicionar uma firewall de **aplicação web** sob **recomendações** para abrir a página **firewall de adicionar uma aplicação web.**

Se não tiver um Gateway de Aplicação existente, ou pretender criar um novo, clique em **Criar Novo** e em Criar uma nova Firewall de **aplicação web**, e clique na Microsoft - Application **Gateway**. Isto leva-o através dos passos para criar um gateway de aplicação. Neste ponto, a sua aplicação web é adicionada como um recurso protegido, o Security Center agora rastreia que este recurso está protegido por uma firewall de aplicação web. Isto não o adiciona como membro da piscina de backend.

Se tiver um portal de aplicação existente, pode escolhê-lo em **solução existente use**

![Página para adicionar uma firewall de aplicação web][4]

Adicionar uma aplicação web a uma porta de aplicação através do Security Center não adiciona o recurso como membro do pool backend. Isto deve ser feito diretamente no recurso de gateway da aplicação.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Adicione um recurso a uma firewall de aplicação web existente

Navegue para **todos os serviços** > Segurança +**Centro** de Segurança**de Identidade** > e no Centro de **Segurança - Visão geral,** clique em **soluções de parceiro**. Os gateways de aplicação conscientes do Centro de Segurança existentes mostram na página Soluções de **Parceiro.**

![soluções parceiras][7]

Clique na **aplicação Link** para abrir aplicações de **link,** aqui é dada a opção de selecionar as aplicações existentes. Escolha as aplicações para proteger e clique **OK**. Isto não adiciona a aplicação web ao backend pool do gateway da aplicação. Isto define os recursos como um recurso protegido para que o Centro de Segurança possa rastreá-lo. Para adicionar o recurso como membro do pool backend, isto deve ser feito no gateway da aplicação, a partir da página atual pode clicar na **consola Solution** para ser levado para o recurso gateway da aplicação onde pode adicionar a aplicação web ao pool backend.

![aplicações de soluções parceiras][6]

## <a name="finalize-configuration"></a>Finalizar a configuração

O Centro de Segurança rastreia as aplicações adicionadas a uma porta de aplicação como recurso protegido.  Monitoriza a saúde deste recurso e garante que está protegido por uma porta de entrada de aplicação. O próximo passo é adicionar o IP privado, IP público ou NIC da sua máquina virtual ao backend pool do gateway de aplicação. Até que isto seja feito, uma recomendação adicional da proteção de **aplicações finalé** mostrada até que o recurso seja adicionado.

![Página para adicionar uma firewall de aplicação web][5]

## <a name="security-alerts"></a>Alertas de Segurança

Dentro do Centro de Segurança navegar para**alertas**de segurança **de DETEÇÃO** > .  Aqui encontra alertas WAF para os seus gateways de aplicação. Os alertas são desfeito pela regra waf.

![alertas de segurança][8]

A seleção de uma regra fornecerá uma lista de alertas para essa regra waf específica. Cada alerta mostra detalhes adicionais sobre a descoberta. Os detalhes fornecem um link para o gateway da aplicação.
 
![detalhes de alerta][9]

## <a name="next-steps"></a>Passos seguintes

Para saber como ativar a firewall da aplicação web num gateway de aplicação existente, visite Criar ou atualizar um Portal de [Aplicações Azure com firewall](application-gateway-web-application-firewall-portal.md)de aplicação web .

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png