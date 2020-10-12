---
title: Resolução de problemas da conectividade SMTP em Azure Microsoft Docs
description: Aprenda o método recomendado para o envio de e-mail e como resolver problemas de conectividade SMTP de saída em Azure.
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
ms.openlocfilehash: e58f498ca254378354fb0e9e21d04fe9969588c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87265267"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Problemas de conectividade SMTP de saída de problemas no Azure

A partir de 15 de novembro de 2017, as mensagens de correio de saída que são enviadas diretamente para domínios externos (como outlook.com e gmail.com) de uma máquina virtual (VM) são disponibilizadas apenas a certos tipos de subscrição no Microsoft Azure. As ligações SMTP de saída que utilizam a porta TCP 25 foram bloqueadas. (O Porto 25 é utilizado principalmente para a entrega de email não autenticado.)

Esta mudança de comportamento aplica-se apenas a novas subscrições e novas implementações desde 15 de novembro de 2017.

## <a name="recommended-method-of-sending-email"></a>Método recomendado de envio de e-mail
Recomendamos que utilize serviços de retransmissão SMTP autenticados (que normalmente se ligam através da porta TCP 587 ou 443, mas também suportam outras portas) para enviar e-mails de VMs Azure ou de Azure App Services. Estes serviços são utilizados para manter a reputação de IP ou de domínio para minimizar a possibilidade de os fornecedores de e-mail de terceiros rejeitarem a mensagem. Estes serviços de retransmissão SMTP incluem, mas não se limitam a [SendGrid.](https://sendgrid.com/partners/azure/) Também é possível que tenha um serviço de retransmissão SMTP seguro que está a funcionar no local que pode usar.

A utilização destes serviços de entrega de e-mail não é restrita no Azure, independentemente do tipo de subscrição.

## <a name="enterprise-agreement"></a>Contrato Enterprise
Para os utilizadores do Enterprise Agreement Azure, não há qualquer alteração na capacidade técnica de enviar e-mails sem usar um retransmissor autenticado. Tanto os utilizadores do Novo e existente Enterprise Agreement podem experimentar a entrega de email de saída da Azure VMs diretamente para fornecedores de e-mail externos sem quaisquer restrições da plataforma Azure. Embora não seja garantido que os fornecedores de e-mail aceitarão o e-mail recebido de qualquer utilizador, as tentativas de entrega não serão bloqueadas pela plataforma Azure para VMs dentro das subscrições do Enterprise Agreement. Terá de trabalhar diretamente com os fornecedores de e-mail para corrigir qualquer entrega de mensagens ou problemas de filtragem de SPAM que envolvam fornecedores específicos.

## <a name="pay-as-you-go"></a>"Pay As You Go"
Se se inscreveu antes de 15 de novembro de 2017 para as ofertas de subscrição pay-as-You-Go ou Microsoft Partner Network, não haverá alterações na capacidade técnica de experimentar a entrega de emails de saída. Continuará a ser capaz de experimentar a entrega de emails de saída da Azure VMs dentro destas subscrições diretamente a fornecedores de e-mail externos sem quaisquer restrições da plataforma Azure. Mais uma vez, não é garantido que os fornecedores de e-mail aceitarão e-mails recebidos de qualquer utilizador, e os utilizadores terão de trabalhar diretamente com os fornecedores de e-mail para corrigir qualquer entrega de mensagens ou problemas de filtragem de SPAM que envolvam fornecedores específicos.

Para as subscrições Pay-As-You-Go ou Microsoft Partner Network que foram criadas após 15 de novembro de 2017, haverá restrições técnicas que bloqueiam o e-mail que é enviado diretamente de VMs dentro destas subscrições. Se pretender enviar e-mails da Azure VMs diretamente para fornecedores de e-mail externos (não utilizando um relé SMTP autenticado), pode fazer um pedido para remover a restrição. Os pedidos serão revistos e aprovados a critério da Microsoft, e serão concedidos apenas após verificações adicionais anti-fraude. Para fazer um pedido, abra um caso de suporte utilizando o seguinte tipo de problema: Conectividade de rede virtual **técnica**  >  **Virtual Network**  >  **Connectivity**  >  **não pode enviar e-mail (SMTP/Porta 25)**. Certifique-se de que adiciona detalhes sobre o motivo pelo qual a sua implementação tem de enviar correio diretamente para os fornecedores de correio em vez de usar um retransmissor autenticado.

Depois de uma subscrição pay-as-You-Go ou Microsoft Partner Network estar isenta e os VMs terem sido 'parados' & 'Iniciado' a partir do portal Azure, todos os VMs dentro dessa subscrição serão isentos daqui para a frente. A isenção só se aplica à subscrição solicitada e aplica-se apenas ao tráfego de Máquina Virtual encaminhado diretamente para a internet. O tráfego do porto de encaminhamento 25 através dos serviços Azure PaaS, como [o Azure Firewall,](https://azure.microsoft.com/services/azure-firewall/) não é suportado.

> [!NOTE]
> A Microsoft reserva-se o direito de revogar esta isenção se for determinado que ocorreu uma violação dos termos de serviço.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure em Open, Education, BizSpark e Free Trial
Se criou uma subscrição de MSDN, Azure Pass, Azure in Open, Education, BizSpark, Azure Sponsorship, Azure Student, Free Trial, ou qualquer subscrição do Visual Studio após 15 de novembro de 2017, terá restrições técnicas que bloqueiam o e-mail que é enviado de VMs dentro destas subscrições diretamente para fornecedores de e-mail. As restrições são feitas para evitar abusos. Não serão concedidos pedidos para eliminar esta restrição.

Se estiver a utilizar estes tipos de subscrição, é encorajado a utilizar os serviços de retransmissão SMTP, conforme descrito anteriormente neste artigo ou a alterar o seu tipo de subscrição.

## <a name="cloud-service-provider-csp"></a>Fornecedor de serviços na nuvem (CSP)

Se estiver a utilizar recursos Azure através de CSP, pode solicitar ao CSP que crie um pedido de isenção de desbloqueio com a Microsoft em seu nome se não puder ser utilizado um relé SMTP seguro.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver rapidamente o seu problema utilizando o seguinte tipo de problema: Tipo de problema de **gestão de subscrição:** **Solicite para ativar o fluxo de e-mail do Porto 25**.
