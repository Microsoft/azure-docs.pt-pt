---
title: Adicionar um firewall do aplicativo Web na central de segurança do Azure | Microsoft Docs
description: Este documento mostra como implementar as recomendações da central de segurança do Azure **Adicionar um firewall do aplicativo Web** e finalizar a **proteção do aplicativo**.
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
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "60706294"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Adicionar um firewall do aplicativo Web na central de segurança do Azure
A central de segurança do Azure pode recomendar que você adicione um WAF (firewall do aplicativo Web) de um parceiro da Microsoft para proteger seus aplicativos Web. Este documento orienta você por um exemplo de como aplicar essa recomendação.

Uma recomendação WAF é mostrada para qualquer IP voltado para o público (IP de nível de instância ou IP com balanceamento de carga) que tenha um grupo de segurança de rede associado com portas da Web de entrada abertas (80.443).

A central de segurança recomenda que você provisione um WAF para ajudar a proteger contra ataques direcionados a seus aplicativos Web em máquinas virtuais e em ambientes de serviço de aplicativo externo (ASE) implantados no plano de serviço [isolado](https://azure.microsoft.com/pricing/details/app-service/windows/) . O plano Isolado aloja as aplicações num ambiente privado e dedicado do Azure e é ideal para aplicações que exigem ligações seguras à rede no local ou para desempenho e dimensionamento adicionais. Além de seu aplicativo estar em um ambiente isolado, seu aplicativo precisa ter um balanceador de carga de endereço IP externo. Para saber mais sobre o ASE, consulte a [documentação do ambiente do serviço de aplicativo](../app-service/environment/intro.md).

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Este documento não é um guia passo a passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Em **recomendações**, selecione **aplicativo Web seguro usando o Firewall do aplicativo Web**.
   ![Aplicativo Web seguro][1]
2. Em **proteger seus aplicativos Web usando o Firewall do aplicativo Web**, selecione um aplicativo Web. **Adicionar um firewall do aplicativo Web** é aberto.
   ![Adicionar uma firewall de aplicação Web][2]
3. Você pode optar por usar um firewall do aplicativo Web existente, se disponível, ou pode criar um novo. Neste exemplo, não há um WAFs existente disponível, portanto, criamos um WAF.
4. Para criar um WAF, selecione uma solução na lista de parceiros integrados. Neste exemplo, selecionamos o **Firewall do aplicativo Web Barracuda**.
5. O **Firewall do aplicativo Web do Barracuda** é aberto, fornecendo informações sobre a solução de parceiro. Selecione **Criar**.

   ![Folha informações do firewall][3]

6. O **novo Firewall do aplicativo Web** é aberto, onde você pode executar as etapas de **configuração da VM** e fornecer informações de **WAF**. Selecione **configuração da VM**.
7. Em **configuração da VM**, insira as informações necessárias para criar a máquina virtual que executa o WAF.

   ![Configuração da VM][4]
   
8. Retorne ao **novo Firewall do aplicativo Web** e selecione **informações de WAF**. Em **informações do WAF**, você configura o próprio WAF. A etapa 7 permite configurar a máquina virtual na qual o WAF é executado e a etapa 8 permite que você provisione o próprio WAF.

## <a name="finalize-application-protection"></a>Finalizar a proteção da aplicação
1. Retornar às **recomendações**. Uma nova entrada foi gerada depois que você criou o WAF, chamado finalizar a **proteção do aplicativo**. Essa entrada permite que você saiba que precisa concluir o processo de realmente conectar o WAF na rede virtual do Azure para que ele possa proteger o aplicativo.

   ![Finalizar a proteção da aplicação][5]

2. Selecione **finalizar a proteção do aplicativo**. Uma nova folha é aberta. Você pode ver que há um aplicativo Web que precisa ter seu tráfego redirecionado.
3. Selecione o aplicativo Web. Uma folha é aberta e fornece as etapas para finalizar a configuração do firewall do aplicativo Web. Conclua as etapas e, em seguida, selecione **restringir tráfego**. A central de segurança faz a conexão para você.

   ![Restringir tráfego][6]

> [!NOTE]
> Você pode proteger vários aplicativos Web na central de segurança adicionando esses aplicativos às suas implantações WAF existentes.
>
>

Os logs desse WAF agora estão totalmente integrados. A central de segurança pode iniciar a coleta e análise automática dos logs para que ele possa trazer alertas de segurança importantes para você.

## <a name="next-steps"></a>Passos seguintes
Este documento mostrou como implementar a recomendação da central de segurança "adicionar um aplicativo Web". Para saber mais sobre como configurar um firewall do aplicativo Web, consulte o seguinte:

* [Configurar uma Firewall de Aplicações Web (WAF) para o Ambiente de Serviço de Aplicações](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger seus recursos do Azure.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) --encontre postagens no blog sobre a segurança e a conformidade do Azure.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
