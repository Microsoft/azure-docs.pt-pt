---
title: Adicionar uma firewall de aplicações web no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento mostra como implementar as recomendações do Centro de segurança do Azure **adicionar uma firewall de aplicações web** e **finalizar a proteção de aplicação**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: 63852ccab842f11f30bcbe695206fedf72931911
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706294"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Adicionar uma firewall de aplicações web no Centro de segurança do Azure
Centro de segurança do Azure poderá recomendar que adicionar uma firewall de aplicações web (WAF) a partir de um parceiro da Microsoft para proteger seus aplicativos web. Este documento explica como um exemplo de como aplicar esta recomendação.

Uma recomendação de WAF é apresentada para qualquer IP destinado ao público (IP de nível de instância ou IP com balanceamento de carga) que tenha um grupo de segurança de rede associado com portas de web de entrada aberta (80,443).

Centro de segurança recomenda que Aprovisiona uma WAF para ajudar na defesa contra ataques que visam as suas aplicações web em máquinas virtuais e em externo serviço ambientes aplicações (ASE) implementado no [Isolated](https://azure.microsoft.com/pricing/details/app-service/windows/) plano do serviço. O plano Isolado aloja as aplicações num ambiente privado e dedicado do Azure e é ideal para aplicações que exigem ligações seguras à rede no local ou para desempenho e dimensionamento adicionais. Além de seu aplicativo a ser num ambiente isolado, a sua aplicação tem de ter um endereço IP externo Balanceador de carga. Para saber mais sobre o ASE, veja a [documentação do ambiente de serviço de aplicações](../app-service/environment/intro.md).

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Este documento não é um guia passo a passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Sob **recomendações**, selecione **proteger a aplicação web com o firewall de aplicações web**.
   ![Aplicação de web seguro][1]
2. Sob **proteger seus aplicativos web usando o firewall de aplicações web**, selecione uma aplicação web. **Adicionar uma Firewall de aplicações Web** abre.
   ![Adicionar uma firewall de aplicação Web][2]
3. Pode optar por utilizar uma firewall de aplicações web existente, se estiver disponível ou pode criar um novo. Neste exemplo, existem não existentes WAFs disponíveis pelo que iremos criar uma WAF.
4. Para criar uma WAF, selecione uma solução na lista de parceiros integrados. Neste exemplo, selecionamos **Barracuda Web Application Firewall**.
5. **Barracuda Web Application Firewall** abre-se de fornecer-lhe informações sobre a solução de parceiro. Selecione **Criar**.

   ![Painel de informações de firewall][3]

6. **Nova Firewall de aplicações Web** é aberto, onde pode realizar **configuração de VM** passos de fornecer **informações da WAF**. Selecione **configuração da VM**.
7. Sob **configuração da VM**, introduzir informações necessárias para acelerar a máquina virtual que executa o WAF.

   ![Configuração da VM][4]
   
8. Volte ao **nova Firewall de aplicações Web** e selecione **informações da WAF**. Sob **informações da WAF**, configurar o WAF em si. Passo 7 permite-lhe configurar a máquina virtual no qual o WAF é executado e passo 8 permite-lhe aprovisionar o WAF em si.

## <a name="finalize-application-protection"></a>Finalizar a proteção das aplicações
1. Volte ao **recomendações**. Uma nova entrada foi gerada depois de criado o WAF, chamado **finalizar proteção de aplicação**. Esta entrada permite-lhe saber que precisa para concluir o processo de realmente conectando o WAF dentro da rede Virtual do Azure, para que possa proteger o aplicativo.

   ![Finalizar a proteção das aplicações][5]

2. Selecione **finalizar proteção de aplicação**. É aberto um painel novo. Pode ver que existe uma aplicação web que tem de ter o seu tráfego reencaminhado.
3. Selecione a aplicação web. É aberto um painel que lhe dá as etapas para finalizar a configuração de firewall de aplicações web. Conclua os passos e, em seguida, selecione **restringir o tráfego**. Centro de segurança, em seguida, faz a cópia de segurança com ligações para.

   ![Restringir tráfego][6]

> [!NOTE]
> Pode proteger várias aplicações web no Centro de segurança com a adição desses aplicativos para as implementações existentes do WAF.
>
>

Os registos a partir desse WAF agora estão totalmente integrados. Centro de segurança pode começar a recolher e analisar os registos, para que ele pode solucionar o alertas de segurança importante para automaticamente.

## <a name="next-steps"></a>Passos Seguintes
Este documento de mostrar como implementar a recomendação do Centro de segurança "Adicionar uma aplicação web." Para saber mais sobre como configurar uma firewall de aplicações web, consulte o seguinte:

* [Configurar uma Firewall de Aplicações Web (WAF) para o Ambiente de Serviço de Aplicações](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como o recomendações o ajudam a proteger os seus recursos do Azure.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – encontre mensagens do Blogue acerca da segurança do Azure e de conformidade.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
