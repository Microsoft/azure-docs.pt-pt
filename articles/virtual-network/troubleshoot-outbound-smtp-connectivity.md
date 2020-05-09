---
title: Problemas de saída da conectividade SMTP em Azure Microsoft Docs
description: Aprenda a resolver problemas de conectividade SMTP de saída em Azure.
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
ms.openlocfilehash: e5a878c8108706c4a3a175c920708beeeaa4aa12
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801755"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Problemas de saída problemas de conectividade SMTP em Azure
<p class="alert is-flex is-primary"><span class="has-padding-left-medium has-padding-top-extra-small"><a class="button is-primary" href="https://azurevirtualsupportagent.services.microsoft.com?content=027fa865-2329-23de-3740-cfbe35359302" target='_blank'>Comece a</a></span>resolver o seu problema rapidamente usando o<span class="has-padding-small">nosso agente virtual para executar <b>diagnósticos automatizados.</b> </span> Declaração <sub>de Privacidade</sub> <span class="has-padding-small"> <a href="https://privacy.microsoft.com/privacystatement" target='_blank'> <div align="right"></div></a></span></p>
A partir de 15 de novembro de 2017, as mensagens de correio eletrónico de saída enviadas diretamente para domínios externos (como outlook.com e gmail.com) de uma máquina virtual (VM) são disponibilizadas apenas a certos tipos de subscrição no Microsoft Azure. As ligações SMTP de saída que utilizam a porta TCP 25 foram bloqueadas. (A porta 25 é usada principalmente para entrega de e-mail não autenticada.)

Esta mudança de comportamento aplica-se apenas a novas subscrições e novas implementações desde 15 de novembro de 2017.

## <a name="recommended-method-of-sending-email"></a>Método recomendado de envio de e-mail
Recomendamos que utilize serviços de retransmissão SMTP autenticados (que normalmente se ligam através da porta TCP 587 ou 443, mas também suportam outras portas) para enviar e-mail de VMs Azure ou de Serviços de Aplicações Azure. Estes serviços são utilizados para manter a reputação de IP ou domínio para minimizar a possibilidade de os fornecedores de e-mail de terceiros rejeitarem a mensagem. Tais serviços de retransmissão SMTP incluem, mas não se limitam a [SendGrid](https://sendgrid.com/partners/azure/). Também é possível que tenha um serviço de retransmissão SMTP seguro que esteja a funcionar no local que pode usar.

A utilização destes serviços de entrega de e-mail não é restrita em Azure, independentemente do tipo de subscrição.

## <a name="enterprise-agreement"></a>Contrato Enterprise
Para os utilizadores do Enterprise Agreement Azure, não existe qualquer alteração na capacidade técnica de enviar e-mail sem utilizar um retransmissor autenticado. Os utilizadores novos e existentes do Acordo Empresarial podem experimentar a entrega de e-mail saindo diretamente de VMs Azure para fornecedores externos de e-mail sem quaisquer restrições da plataforma Azure. Embora não seja garantido que os fornecedores de e-mail aceitem o e-mail de entrada de qualquer utilizador, as tentativas de entrega não serão bloqueadas pela plataforma Azure para VMs dentro das subscrições do Enterprise Agreement. Terá de trabalhar diretamente com fornecedores de e-mail para corrigir quaisquer problemas de entrega de mensagens ou filtragem de SPAM que envolvam fornecedores específicos.

## <a name="pay-as-you-go"></a>"Pay As You Go"
Se se inscreveu antes de 15 de novembro de 2017 para as ofertas de subscrição pay-As-You-Go ou Microsoft Partner Network, não haverá qualquer alteração na capacidade técnica de experimentar a entrega de e-mails de saída. Continuará a poder experimentar a entrega de e-mail saindo de VMs Azure dentro destas subscrições diretamente para fornecedores de e-mail externos sem quaisquer restrições da plataforma Azure. Mais uma vez, não é garantido que os fornecedores de e-mail aceitem o e-mail de entrada de qualquer utilizador, e os utilizadores terão de trabalhar diretamente com fornecedores de e-mail para corrigir qualquer entrega de mensagens ou problemas de filtragem de SPAM que envolvam fornecedores específicos.

Para subscrições pay-As-You-Go ou Microsoft Partner Network que foram criadas após 15 de novembro de 2017, haverá restrições técnicas que bloqueiam o e-mail que é enviado diretamente de VMs dentro destas subscrições. Se pretender enviar e-mail de VMs Azure diretamente a fornecedores de e-mail externos (não utilizando um relé SMTP autenticado), pode fazer um pedido para remover a restrição. Os pedidos serão revistos e aprovados a critério da Microsoft, e só serão concedidos depois de serem feitos controlos antifraude adicionais. Para fazer um pedido, abra um caso de suporte utilizando o seguinte tipo de problema: A**Conectividade** >  **Técnica** > **da Rede** > Virtual**não pode enviar e-mail (SMTP/Porta 25)**. Certifique-se de que adiciona detalhes sobre o porquê da sua implementação ter de enviar correio diretamente para os fornecedores de correio em vez de utilizar um retransmissor autenticado.

Depois de uma subscrição pay-As-You-Go ou Microsoft Partner Network estar isenta e os VMs foram 'Parados' & 'Iniciado' do portal Azure, todos os VMs dentro dessa subscrição serão isentos de avançar. A isenção só se aplica à subscrição solicitada e aplica-se apenas ao tráfego da Máquina Virtual encaminhado diretamente para a internet. O tráfego do porta de encaminhamento 25 através de serviços Azure PaaS, como [o Azure Firewall,](https://azure.microsoft.com/services/azure-firewall/) não suporta.

> [!NOTE]
> A Microsoft reserva-se o direito de revogar esta isenção se for determinado que ocorreu uma violação dos termos de serviço.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, Education, BizSpark e Free Trial
Se criou um MSDN, Azure Pass, Azure in Open, Education, BizSpark, Azure Sponsorship, Azure Student, Free Trial ou qualquer subscrição do Visual Studio depois de 15 de novembro de 2017, terá restrições técnicas que bloqueiam e-mails enviados a partir de VMs dentro destas subscrições diretamente para fornecedores de e-mail. As restrições são feitas para evitar abusos. Não serão concedidos pedidos para eliminar esta restrição.

Se estiver a utilizar estes tipos de subscrição, é encorajado a utilizar serviços de retransmissão SMTP, tal como descrito anteriormente neste artigo ou alterar o seu tipo de subscrição.

## <a name="cloud-service-provider-csp"></a>Provedor de Serviço sinuoso (CSP)

Se estiver a utilizar recursos Azure através de CSP, pode solicitar ao CSP que crie um pedido de isenção de desbloqueio com a Microsoft em seu nome se não for possível utilizar um relé SMTP seguro.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o suporte para resolver o seu problema rapidamente utilizando o seguinte tipo de problema: Tipo de Problema de **Gestão** de Assinaturas: Pedido para ativar o fluxo de **e-mail da Porta 25**.
