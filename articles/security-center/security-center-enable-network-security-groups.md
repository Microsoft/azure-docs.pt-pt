---
title: Habilitar grupos de segurança de rede na central de segurança do Azure | Microsoft Docs
description: Este documento mostra como implementar a recomendação da central de segurança do Azure **habilitar grupos de segurança de rede**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 14b7cc8f8162574380ca21ac515af8b7d3d5ded9
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "60911472"
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Habilitar grupos de segurança de rede na central de segurança do Azure
A central de segurança do Azure recomenda que você habilite um NSG (grupo de segurança de rede) se já não estiver habilitado. NSGs contém uma lista de regras de ACL (lista de controle de acesso) que permitem ou negam o tráfego de rede para suas instâncias de VM em uma rede virtual. Os NSGs podem ser associados a sub-redes ou a instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado a uma sub-rede, as regras da ACL são aplicadas a todas as instâncias de VM nessa sub-rede. Além disso, o tráfego para uma VM individual pode ser restringido ainda mais por meio da Associação de um NSG diretamente a essa VM. Para saber mais, veja [o que é um NSG (grupo de segurança de rede)?](../virtual-network/security-overview.md)

Se você não tiver o NSGs habilitado, a central de segurança apresentará duas recomendações para você: Habilite grupos de segurança de rede em sub-redes e habilite grupos de segurança de rede em máquinas virtuais. Você escolhe o nível, a sub-rede ou a VM a aplicar NSGs.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na folha **recomendações** , selecione **habilitar grupos de segurança de rede** em sub-redes ou em máquinas virtuais.
   ![Ativar os Grupos de Segurança de Rede][1]
2. Isso abre a folha **configurar grupos de segurança de rede ausentes** para sub-redes ou para máquinas virtuais, dependendo da recomendação que você selecionou. Selecione uma sub-rede ou uma máquina virtual para configurar um NSG.

   ![Configurar NSG para sub-rede][2]

   ![Configurar o NSG para a VM][3]
3. Na folha **escolher grupo de segurança de rede** , selecione um NSG existente ou selecione **criar novo** para criar um NSG.

   ![Escolher grupo de segurança de rede][4]

Se você criar um NSG, siga as etapas em [gerenciar um grupo de segurança de rede](../virtual-network/manage-network-security-group.md) para criar um NSG e definir regras de segurança.

## <a name="see-also"></a>Consulte também
Este artigo mostrou como implementar a recomendação da central de segurança "habilitar grupos de segurança de rede" para sub-redes ou máquinas virtuais. Para saber mais sobre como habilitar o NSGs, consulte o seguinte:

* [O que é um Grupo de Segurança de Rede (NSG)? (What is a Network Security Group (NSG)?)](../virtual-network/security-overview.md)
* [Gerenciar um grupo de segurança de rede](../virtual-network/manage-network-security-group.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger seus recursos do Azure.
* [Monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – obtenha as últimas notícias e informações de segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
