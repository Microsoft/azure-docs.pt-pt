---
title: Adicionar um firewall da próxima geração na central de segurança do Azure | Microsoft Docs
description: Este documento mostra como implementar as recomendações da central de segurança do Azure **Adicionar um firewall da próxima geração** e rotear o **tráfego somente por meio de NGFW**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 731102037b596091b80fbdfa02a8ff3c111b556e
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "60707079"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Adicionar um firewall da próxima geração na central de segurança do Azure
A central de segurança do Azure pode recomendar que você adicione um firewall de última geração (NGFW) de um parceiro da Microsoft para aumentar suas proteções de segurança. Este documento orienta você por um exemplo de como fazer isso.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na folha **recomendações** , selecione **Adicionar um firewall da próxima geração**.
   ![Adicionar uma Firewall da Próxima Geração][1]
2. Na folha **Adicionar um firewall da próxima geração** , selecione um ponto de extremidade.
   ![Selecionar um ponto de extremidade][2]
3. Uma segunda folha **Adicionar um firewall** de última geração é aberta. Você pode optar por usar uma solução existente, se disponível, ou pode criar uma nova. Neste exemplo, não há nenhuma solução existente disponível, portanto, criamos um NGFW.
   ![Criar firewall da próxima geração][3]
4. Para criar um NGFW, selecione uma solução na lista de parceiros integrados. Neste exemplo, selecionamos **ponto de verificação**.
   ![Selecione a solução de firewall da próxima geração][4]
5. A folha **Check Point** é aberta, fornecendo informações sobre a solução de parceiro. Selecione **criar** na folha informações.
   ![Folha informações do firewall][5]
6. A folha **criar máquina virtual** é aberta. Aqui você pode inserir as informações necessárias para criar uma VM (máquina virtual) que executa o NGFW. Siga as etapas e forneça as informações de NGFW necessárias. Selecione OK para aplicar.
   ![Criar máquina virtual para executar NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Encaminhar o tráfego apenas através da NGFW
Retorne à folha **recomendações** . Uma nova entrada foi gerada depois que você adicionou um NGFW por meio da central de segurança, chamado **rotear tráfego somente por meio de NGFW**. Essa recomendação será criada somente se você tiver instalado o NGFW por meio da central de segurança. Se você tiver pontos de extremidade voltados para a Internet, a central de segurança recomenda que você configure as regras do grupo de segurança de rede que forçam o tráfego de entrada para sua VM por meio de seu NGFW.

1. Na **folha recomendações**, selecione **rotear tráfego somente por meio de NGFW**.
   ![Encaminhar o tráfego apenas através da NGFW][7]
2. Isso abre o tráfego de rota da folha **somente por meio de NGFW**, que lista as VMs para as quais você pode rotear o tráfego. Selecione uma VM na lista.
   ![Selecionar uma VM][8]
3. Uma folha para a VM selecionada é aberta, exibindo as regras de entrada relacionadas. Uma descrição fornece mais informações sobre as próximas etapas possíveis. Selecione **Editar regras de entrada** para continuar com a edição de uma regra de entrada. A expectativa é que a **origem** não seja definida como **qualquer** para os pontos de extremidade voltados para a Internet vinculados à NGFW. Para saber mais sobre as propriedades da regra de entrada, consulte [regras de segurança](../virtual-network/security-overview.md#security-rules).
   ![Configurar regras para limitar o][9]
   acesso![editar regra de entrada][10]

## <a name="see-also"></a>Consulte também
Este documento mostrou como implementar a recomendação da central de segurança "adicionar um firewall da próxima geração". Para saber mais sobre o NGFWs e a solução de parceiro do Check Point, consulte o seguinte:

* [Firewall de última geração](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [VSEC do Check Point](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Configurando políticas de segurança na central de segurança do Azure](tutorial-security-policy.md) – saiba como configurar políticas de segurança.
* [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger seus recursos do Azure.
* [Monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) --encontre postagens no blog sobre a segurança e a conformidade do Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
