---
title: Resolução de problemas da conectividade SMTP em Azure Microsoft Docs
description: Aprenda o método recomendado para o envio de e-mail e como resolver problemas com a conectividade SMTP de saída em Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: genli
ms.openlocfilehash: e1e7e78cab1f3a240737b5e25e0dff28c420add8
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883117"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Problemas de conectividade SMTP de resolução de problemas em Azure

A partir de 15 de novembro de 2017, as mensagens de correio de saída que são enviadas diretamente para domínios externos (como outlook.com e gmail.com) de uma máquina virtual (VM) são disponibilizadas apenas a certos tipos de subscrição em Azure. As ligações SMTP de saída que utilizam a porta TCP 25 foram bloqueadas. (O Porto 25 é utilizado principalmente para a entrega de email não autenticado.)

Esta mudança de comportamento aplica-se apenas a subscrições e implementações que foram criadas após 15 de novembro de 2017.

## <a name="recommended-method-of-sending-email"></a>Método recomendado de envio de e-mail

Recomendamos que utilize serviços de retransmissão SMTP autenticados para enviar e-mails da Azure VMs ou do Azure App Service. (Estes serviços de retransmissão normalmente ligam-se através da porta TCP 587 ou 443, mas suportam outras portas.) Estes serviços são utilizados para manter a reputação de IP ou de domínio para minimizar a possibilidade de os fornecedores de e-mail de terceiros rejeitarem mensagens. [SendGrid](https://sendgrid.com/partners/azure/) é um desses serviços de retransmissão SMTP, mas há outros. Também pode ter um serviço de retransmissão SMTP seguro que pode utilizar no local.

A utilização destes serviços de entrega de e-mail não é restrita no Azure, independentemente do tipo de subscrição.

## <a name="enterprise-agreement"></a>Contrato Enterprise

Para os utilizadores do Enterprise Agreement Azure, não há qualquer alteração na capacidade técnica de enviar e-mails sem usar um retransmissor autenticado. Tanto os utilizadores do Novo e existente Enterprise Agreement podem experimentar a entrega de email de saída da Azure VMs diretamente para fornecedores de e-mail externos sem quaisquer restrições da plataforma Azure. Não há garantias de que os fornecedores de e-mail aceitem e-mails recebidos de qualquer utilizador. Mas a plataforma Azure não bloqueia as tentativas de entrega de VMs dentro das subscrições do Enterprise Agreement. Terá de trabalhar diretamente com os fornecedores de e-mail para corrigir qualquer entrega de mensagens ou problemas de filtragem de SPAM que envolvam fornecedores específicos.

## <a name="pay-as-you-go"></a>Pay-as-you-go

Se se inscreveu antes de 15 de novembro de 2017, para uma subscrição pay-as-you-go, não haverá alterações na sua capacidade técnica para experimentar a entrega de emails de saída. Ainda poderá experimentar a entrega de email saindo da Azure VMs dentro destas subscrições diretamente a fornecedores de e-mail externos sem quaisquer restrições da plataforma Azure. Mais uma vez, não há garantias de que os fornecedores de e-mail aceitarão o e-mail de qualquer utilizador. Os utilizadores terão de trabalhar diretamente com os fornecedores de e-mail para corrigir qualquer entrega de mensagens ou problemas de filtragem de SPAM que envolvam fornecedores específicos.

Para as subscrições pay-as-you-go que foram criadas após 15 de novembro de 2017, haverá restrições técnicas que bloqueiam o e-mail que é enviado diretamente de VMs dentro das subscrições. Se quiser enviar e-mail da Azure VMs diretamente para fornecedores de e-mail externos (sem utilizar um relé SMTP autenticado) e tiver uma conta em boas condições com um histórico de pagamento, pode solicitar a remoção da restrição. Pode fazê-lo na secção de **Conectividade** da lâmina **De Diagnóstico e Resolução** para um recurso de Rede Virtual Azure no portal Azure. Se o seu pedido for aceite, a sua subscrição estará ativada ou receberá instruções para os próximos passos. 

Depois de uma subscrição pay-as-you-go ser isenta e os VMs são parados e reiniciados no portal Azure, todos os VMs nessa subscrição estão isentos daqui para a frente. A isenção aplica-se apenas à subscrição solicitada e apenas ao tráfego VM que é encaminhado diretamente para a internet.

> [!NOTE]
> A Microsoft reserva-se o direito de revogar estas isenções se for determinado que ocorreu uma violação dos termos de serviço.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, Education, Azure for Students, Visual Studio e Free Trial

Se criou um dos seguintes tipos de subscrição depois de 15 de novembro de 2017, terá restrições técnicas que bloqueiam o e-mail enviado de VMs dentro da subscrição diretamente para fornecedores de e-mail:
- MSDN
- Passe de Azure
- Azure no Licenciamento Open
- Education
- Azure for Students
- Avaliação Gratuita
- Qualquer subscrição do Estúdio Visual  

As restrições estão em vigor para evitar abusos. Os pedidos para remover estas restrições não serão concedidos.

Se estiver a utilizar estes tipos de subscrição, encorajamo-lo a utilizar os serviços de retransmissão SMTP, conforme descrito anteriormente neste artigo, ou a alterar o seu tipo de subscrição.

## <a name="cloud-solution-provider"></a>Provedor de solução cloud

Se estiver a utilizar recursos Azure através de um Fornecedor de Solução Cloud, pode fazer um pedido para remover a restrição na secção de **Conectividade** do painel **de Diagnóstico e Resolução** para um recurso de rede virtual no portal Azure. Se o seu pedido for aceite, a sua subscrição estará ativada ou receberá instruções para os próximos passos.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network, BizSpark Plus ou Patrocínio Azure

Para subscrições dos seguintes tipos que foram criados após 15 de novembro de 2017, haverá restrições técnicas que bloqueiam o e-mail que é enviado diretamente de VMs dentro das subscrições:

- Microsoft Partner Network (MPN)
- BizSpark Plus
- Azure Sponsorship

Se pretender enviar e-mails da Azure VMs diretamente para fornecedores de e-mail externos (sem utilizar um relé SMTP autenticado), pode fazer um pedido abrindo um caso de suporte utilizando o seguinte tipo de problema: Conectividade de rede virtual **técnica**  >    >    >  **Não pode enviar e-mail (SMTP/Porta 25)**. Certifique-se de adicionar detalhes sobre o motivo pelo qual a sua implementação tem de enviar correio diretamente para os fornecedores de correio em vez de usar um retransmissor autenticado. Os pedidos serão revistos e aprovados à discrição da Microsoft. Os pedidos só serão concedidos após a conclusão de controlos antifraundo adicionais. 

Depois de uma subscrição ser isenta e os VMs terem sido interrompidos e reiniciados no portal Azure, todos os VMs nessa subscrição estão isentos daqui para a frente. A isenção aplica-se apenas à subscrição solicitada e apenas ao tráfego VM que é encaminhado diretamente para a internet.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente. Utilize este tipo de problema: **Conectividade de** rede virtual técnica Não  >    >    >  **pode enviar e-mail (SMTP/Porta 25)**.
