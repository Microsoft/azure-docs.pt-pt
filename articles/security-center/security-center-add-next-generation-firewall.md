---
title: Adicionar uma firewall da próxima geração no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento mostra como implementar as recomendações do Centro de segurança do Azure **adicionar uma Firewall da próxima geração** e **encaminhar o tráfego apenas através da NGFW**.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60707079"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Adicionar uma Firewall da próxima geração no Centro de segurança do Azure
Centro de segurança do Azure poderá recomendar que adicionar uma firewall da próxima geração (NGFW) de um parceiro da Microsoft para aumentar sua proteções de segurança. Este documento explica como um exemplo de como fazer isso.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na **recomendações** painel, selecione **adicionar uma Firewall da próxima geração**.
   ![Adicionar uma Firewall da Próxima Geração][1]
2. Na **adicionar uma Firewall da próxima geração** painel, selecione um ponto final.
   ![Selecione um ponto final][2]
3. Um segundo **adicionar uma Firewall da próxima geração** é aberto o painel. Pode optar por utilizar uma solução existente, se estiver disponível ou pode criar um novo. Neste exemplo, existem não existem soluções existentes disponíveis pelo que iremos criar uma NGFW.
   ![Criar a Firewall da próxima geração][3]
4. Para criar uma NGFW, selecione uma solução na lista de parceiros integrados. Neste exemplo, selecionamos **ponto de verificação**.
   ![Selecione a solução de Firewall da próxima geração][4]
5. O **ponto de verificação** é aberto o painel fornecer-lhe informações sobre a solução de parceiro. Selecione **criar** no painel de informações.
   ![Painel de informações de firewall][5]
6. O **criar máquina virtual** é aberto o painel. Aqui pode inserir informações necessárias para acelerar uma máquina virtual (VM) que é executada da NGFW. Siga os passos e forneça as informações da NGFW necessárias. Selecione OK para aplicar.
   ![Criar a máquina virtual para executar da NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Encaminhar o tráfego apenas através da NGFW
Retorno para o **recomendações** painel. Uma nova entrada foi gerada depois adicionado uma NGFW através do Centro de segurança, chamado **encaminhar o tráfego apenas através da NGFW**. Esta recomendação é criada apenas se tiver instalado a NGFW através do Centro de segurança. Se tiver pontos finais de acesso à Internet, o Centro de segurança recomenda que configure regras do grupo de segurança de rede que imponham o tráfego de entrada para a sua VM através da sua NGFW.

1. Na **painel recomendações**, selecione **encaminhar o tráfego apenas através da NGFW**.
   ![Encaminhar o tráfego apenas através da NGFW][7]
2. Esta ação abre o painel **encaminhar o tráfego apenas através da NGFW**, que apresenta uma lista de VMs que pode encaminhar o tráfego. Selecione uma VM na lista.
   ![Selecione uma VM][8]
3. É aberto um painel para a VM selecionada, a exibição de regras de entrada relacionadas. Uma descrição fornece-lhe obter mais informações sobre as próximas etapas possíveis. Selecione **editar regras de entrada** para continuar com a edição de uma regra de entrada. A expectativa é que **origem** não estiver definido como **qualquer** para os pontos finais de acesso à Internet está associados da NGFW. Para saber mais sobre as propriedades da regra de entrada, consulte [regras de segurança](../virtual-network/security-overview.md#security-rules).
   ![Configurar regras para limitar o acesso][9]
   ![Editar regra de entrada][10]

## <a name="see-also"></a>Consulte também
Este documento de mostrar como implementar a recomendação do Centro de segurança "Adicionar uma Firewall da próxima geração." Para saber mais sobre NGFWs e a solução de parceiro de ponto de verificação, consulte o seguinte:

* [Firewall de próxima geração](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Check Point vSEC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de segurança do Azure](tutorial-security-policy.md) – Saiba como configurar as políticas de segurança.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como o recomendações o ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – encontre mensagens do Blogue acerca da segurança do Azure e de conformidade.

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
