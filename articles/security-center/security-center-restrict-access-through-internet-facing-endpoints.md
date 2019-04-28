---
title: Restringir o acesso através de pontos finais de acesso à Internet no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento mostra como implementar a recomendação do Centro de segurança do Azure **restringir o acesso através da Internet destinada ao ponto final**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: b736bb5549b7d236e746ba7b161cde79209e927b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60906586"
---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Restringir o acesso através de pontos finais de acesso à Internet no Centro de segurança do Azure
Centro de segurança do Azure recomendará que restringir o acesso através de pontos finais de acesso à Internet se qualquer um dos seus grupos de segurança de rede (NSGs) tem um ou mais regras de entrada que permitem o acesso de "qualquer" endereço IP de origem. Abrir o acesso a "todos" pode permitir que atacantes acedam aos seus recursos. Centro de segurança irá recomendar que editar estas regras de entrada para restringir o acesso a endereços IP de origem que realmente precisam de acesso.

Esta recomendação é gerada para qualquer porta não web que tenha "qualquer" como origem.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação. Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na **painel recomendações**, selecione **restringir o acesso através da Internet destinada ao ponto final**.

   ![Restringir o acesso através de um ponto final com acesso à Internet][1]
2. Esta ação abre o painel **restringir o acesso através da Internet destinada ao ponto final**. Este painel apresenta uma lista as máquinas virtuais (VMs) com as regras de entrada que criam um potencial problema de segurança. Selecione uma VM.

   ![Selecione uma VM][2]
3. O **NSG** painel apresenta informações de grupo de segurança de rede, regras de entrada relacionadas e a VM associada. Selecione **editar regras de entrada** para continuar com a edição de uma regra de entrada.

   ![Painel do grupo de segurança de rede][3]
4. Sobre o **regras de segurança de entrada** painel, selecione a regra de entrada para editar. Neste exemplo, vamos selecionar **AllowWeb**.

   ![Regras de segurança de entrada][4]

   Observe, também pode selecionar **regras predefinidas** para ver o conjunto de regras predefinidas contidos por todos os NSGs. Não não possível eliminar as regras predefinidas, mas como lhes é atribuída uma prioridade mais baixa, podem ser substituídas pelas regras que criar. Saiba mais sobre [regras predefinidas](../virtual-network/security-overview.md#default-security-rules).

   ![Regras predefinidas][5]
5. Sobre o **AllowWeb** painel, editar as propriedades da regra de entrada para que o **origem** é um endereço IP ou o bloco de endereços IP. Para saber mais sobre as propriedades da regra de entrada, consulte [regras do NSG](../virtual-network/security-overview.md#security-rules).

   ![Editar regra de entrada][6]

## <a name="see-also"></a>Consulte também
Este artigo mostrou como implementar a recomendação do Centro de segurança "Restringir o acesso através de ponto de final com acesso à Internet." Para saber mais sobre como ativar os NSGs e regras, consulte o seguinte:

* [O que é um Grupo de Segurança de Rede (NSG)? (What is a Network Security Group (NSG)?)](../virtual-network/security-overview.md)
* [Gerir um grupo de segurança de rede](../virtual-network/manage-network-security-group.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de Segurança do Azure](security-center-recommendations.md) – Saiba como as recomendações o ajudam a proteger os seus recursos do Azure.
* [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – Obtenha as notícias e informações mais recentes de segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
