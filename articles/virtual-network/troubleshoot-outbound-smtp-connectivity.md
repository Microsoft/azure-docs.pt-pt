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
ms.openlocfilehash: d2e5996da5a1fe3f5b154d57ee509f25e54e30ac
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862365"
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

Se se inscreveu antes de 15 de novembro de 2017 para a subscrição Pay-As-You-Go, não haverá alterações na capacidade técnica de experimentar a entrega de emails de saída. Continuará a ser capaz de experimentar a entrega de emails de saída da Azure VMs dentro destas subscrições diretamente a fornecedores de e-mail externos sem quaisquer restrições da plataforma Azure. Mais uma vez, não é garantido que os fornecedores de e-mail aceitarão e-mails recebidos de qualquer utilizador, e os utilizadores terão de trabalhar diretamente com os fornecedores de e-mail para corrigir qualquer entrega de mensagens ou problemas de filtragem de SPAM que envolvam fornecedores específicos.

Para as subscrições Pay-As-You-Go que foram criadas após 15 de novembro de 2017, haverá restrições técnicas que bloqueiam o e-mail que é enviado diretamente de VMs dentro destas subscrições. Se pretender enviar e-mails de VMs da Azure diretamente para fornecedores de e-mail externos (não utilizando um relé SMTP autenticado) e tiver uma conta em boas condições com um histórico de pagamento, pode fazer um pedido para remover a restrição na secção **de Conectividade** da lâmina de Diagnóstico **e Resolução** para um recurso de Rede Virtual Azure no portal Azure. Se for qualificado, a sua subscrição será ativada ou receberá instruções sobre os próximos passos. 

Depois de uma subscrição Pay-As-You-Go ser isenta e os VMs terem sido interrompidos e iniciados no portal Azure, todos os VMs nessa subscrição estão isentos em curso. A isenção aplica-se apenas à subscrição solicitada e apenas ao tráfego VM que é encaminhado diretamente para a internet.

> [!NOTE]
> A Microsoft reserva-se o direito de revogar esta isenção se for determinado que ocorreu uma violação dos termos de serviço.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-vistual-studio-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, Education, Azure for Students, Vistual Studio e Free Trial

Se criou uma subscrição de MSDN, Azure Pass, Azure in Open, Education, Azure Student, Free Trial, ou qualquer subscrição do Visual Studio após 15 de novembro de 2017, terá restrições técnicas que bloqueiam o e-mail que é enviado de VMs dentro destas subscrições diretamente para fornecedores de e-mail. As restrições são feitas para evitar abusos. Não serão concedidos pedidos para eliminar esta restrição.

Se estiver a utilizar estes tipos de subscrição, é encorajado a utilizar os serviços de retransmissão SMTP, conforme descrito anteriormente neste artigo ou a alterar o seu tipo de subscrição.

## <a name="cloud-service-provider-csp"></a>Fornecedor de serviços na nuvem (CSP)

Se estiver a utilizar recursos Azure através de CSP, pode fazer um pedido para remover a restrição na secção de **Conectividade** da lâmina **De Diagnóstico e Resolução** para um recurso de Rede Virtual no Portal Azure. Se for qualificado, a sua subscrição será ativada ou receberá instruções sobre os próximos passos.

## <a name="microsoft-partner-network-mpn-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network (MPN), BizSpark Plus ou Patrocínio da Azure

Para as subscrições da Microsoft Partner Network (MPN), BizSpark Plus ou Azure Sponsorship que foram criadas após 15 de novembro de 2017, haverá restrições técnicas que bloqueiam o e-mail que é enviado diretamente de VMs dentro destas subscrições. Se pretender enviar e-mails de VMs da Azure diretamente para fornecedores de e-mail externos (não utilizando um relé SMTP autenticado), pode fazer um pedido abrindo um caso de suporte utilizando o seguinte tipo de problema: Conectividade de rede virtual **técnica**  >  **Virtual Network**  >  **Connectivity**  >  **Não pode enviar e-mail (SMTP/Porta 25)**. Certifique-se de que adiciona detalhes sobre o motivo pelo qual a sua implementação tem de enviar correio diretamente para os fornecedores de correio em vez de usar um retransmissor autenticado. Os pedidos serão revistos e aprovados à discrição da Microsoft. Os pedidos só podem ser concedidos após a conclusão de controlos adicionais antifraude. 

Depois de uma subscrição ser isenta e os VMs terem sido interrompidos e iniciados no portal Azure, todos os VMs nessa subscrição estão isentos daqui para a frente. A isenção aplica-se apenas à subscrição solicitada e apenas ao tráfego VM que é encaminhado diretamente para a internet.

## <a name="restrictions-and-limitations"></a>Restrições e limitações

- O tráfego do porto de encaminhamento 25 através dos serviços Azure PaaS, como [o Azure Firewall,](https://azure.microsoft.com/services/azure-firewall/) não é suportado.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver rapidamente o seu problema utilizando o seguinte tipo de problema: Conectividade de rede virtual **técnica** Não  >  **Virtual Network**  >  **Connectivity**  >  **pode enviar e-mail (SMTP/Porta 25)**.
