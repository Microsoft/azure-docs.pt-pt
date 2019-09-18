---
title: Solucionar problemas de conectividade SMTP de saída no Azure | Microsoft Docs
description: Saiba como solucionar problemas de conectividade SMTP de saída no Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: b8acb50978c5932fe6e6838be86b65c12a0984ac
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058941"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Solucionar problemas de conectividade SMTP de saída no Azure

A partir de 15 de novembro de 2017, as mensagens de email de saída enviadas diretamente para domínios externos (como outlook.com e gmail.com) de uma VM (máquina virtual) são disponibilizadas somente para determinados tipos de assinatura no Microsoft Azure. As conexões SMTP de saída que usam a porta TCP 25 foram bloqueadas. (A porta 25 é usada principalmente para entrega de email não autenticado.)

Essa alteração no comportamento se aplica somente a novas assinaturas e novas implantações desde 15 de novembro de 2017.

## <a name="recommended-method-of-sending-email"></a>Método recomendado de envio de email
Recomendamos que você use os serviços de retransmissão SMTP autenticados (que normalmente se conectam por meio da porta TCP 587 ou 443, mas também oferecem suporte a outras portas) para enviar emails de VMs do Azure ou de serviços de Azure App. Esses serviços são usados para manter a reputação do IP ou do domínio para minimizar a possibilidade de os provedores de email de terceiros rejeitarem a mensagem. Esses serviços de retransmissão de SMTP incluem, mas não estão limitados a [SendGrid](https://sendgrid.com/partners/azure/). Também é possível ter um serviço de retransmissão SMTP seguro em execução no local que você pode usar.

O uso desses serviços de entrega de email não é restrito no Azure, independentemente do tipo de assinatura.

## <a name="enterprise-agreement"></a>Enterprise Agreement
Para Enterprise Agreement usuários do Azure, não há nenhuma alteração na capacidade técnica de enviar emails sem usar uma retransmissão autenticada. Os usuários novos e existentes do Enterprise Agreement podem tentar a entrega de email de saída de VMs do Azure diretamente para provedores de email externos, sem restrições da plataforma Azure. Embora não seja garantido que os provedores de email aceitem emails de entrada de qualquer usuário, as tentativas de entrega não serão bloqueadas pela plataforma do Azure para VMs dentro de assinaturas Enterprise Agreement. Você precisará trabalhar diretamente com os provedores de email para corrigir quaisquer problemas de entrega de mensagens ou de filtragem de SPAM que envolvam provedores específicos.

## <a name="pay-as-you-go"></a>"Pay As You Go"
Se você se inscreveu antes de 15 de novembro de 2017 para as ofertas de assinatura pago pelo uso ou Microsoft Partner Network, não haverá nenhuma alteração na capacidade técnica de experimentar a entrega de email de saída. Você continuará a ser capaz de tentar a entrega de email de saída de VMs do Azure dentro dessas assinaturas diretamente para provedores de email externos sem nenhuma restrição da plataforma Azure. Novamente, não é garantido que os provedores de email aceitem emails de entrada de qualquer usuário, e os usuários terão que trabalhar diretamente com os provedores de email para corrigir quaisquer problemas de entrega de mensagens ou de filtragem de SPAM que envolvam provedores específicos.

Para assinaturas pagas conforme o uso ou Microsoft Partner Network que foram criadas após 15 de novembro de 2017, haverá restrições técnicas que bloqueiam emails enviados diretamente de VMs dentro dessas assinaturas. Se você quiser a capacidade de enviar emails de VMs do Azure diretamente para provedores de email externos (não usando uma retransmissão de SMTP autenticado), poderá fazer uma solicitação para remover a restrição. As solicitações serão revisadas e aprovadas a critério da Microsoft, e elas serão concedidas somente após a realização de verificações antifraudes adicionais. Para fazer uma solicitação, abra um caso de suporte usando o seguinte tipo de problema: > A**conectividade**derede > virtual técnica**não pode enviar email (SMTP/porta 25).**  >  Certifique-se de adicionar detalhes sobre por que sua implantação tem que enviar email diretamente aos provedores de email em vez de usar uma retransmissão autenticada.

Depois que uma assinatura pré-paga ou Microsoft Partner Network for isenta e as VMs tiverem sido ' interrompidas ' & ' iniciado ' da portal do Azure, todas as VMs dentro dessa assinatura serão isentas no futuro. A isenção só é aplicável à assinatura solicitada e só se aplica ao tráfego de máquina virtual roteado diretamente para a Internet. Não há suporte para o tráfego da porta 25 de roteamento por meio dos serviços de PaaS do Azure, como o [Firewall do Azure](https://azure.microsoft.com/services/azure-firewall/) .

> [!NOTE]
> A Microsoft se reserva o direito de revogar essa isenção se for determinado que uma violação dos termos de serviço ocorreu.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure via Open, educação, BizSpark e avaliação gratuita
Se você criou um MSDN, Azure Pass, Azure via Open, educação, BizSpark, Azure Sponsorship, aluno do Azure, avaliação gratuita ou qualquer assinatura do Visual Studio depois de 15 de novembro de 2017, você terá restrições técnicas que bloqueiam emails enviados de VMs dentro delas assinaturas diretamente para provedores de email. As restrições são feitas para evitar abusos. Nenhuma solicitação para remover essa restrição será concedida.

Se você estiver usando esses tipos de assinatura, você será incentivado a usar os serviços de retransmissão de SMTP, conforme descrito anteriormente neste artigo ou alterar o tipo de assinatura.

## <a name="cloud-service-provider-csp"></a>Provedor de serviços de nuvem (CSP)

Se você estiver usando recursos do Azure por meio do CSP, poderá solicitar que o CSP crie uma solicitação de isenção de desbloqueio com a Microsoft em seu nome se uma retransmissão SMTP segura não puder ser usada.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte

Se você ainda precisar de ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema rapidamente usando o seguinte tipo de problema: **Gerenciamento de assinatura** Tipo de problema: **Solicitação para habilitar o fluxo de email da porta 25**.
