---
title: Resolver problemas de conectividade de SMTP de saída no Azure | Documentos da Microsoft
description: Saiba como resolver problemas de conectividade de SMTP de saída no Azure.
services: virtual-network
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 385163d791bff0c02a05ee1b27afd82c3afd0ac3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57997176"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Resolver problemas de conectividade de SMTP saídos no Azure

A partir de 15 de Novembro de 2017, mensagens de e-mail de saída que são enviadas diretamente para domínios externos (por exemplo, outlook.com e gmail.com) a partir de uma máquina virtual (VM) estão disponíveis apenas para determinados tipos de subscrição no Microsoft Azure. Conexões SMTP de saída que utilizam a porta 25 TCP foram bloqueados. (Porta 25 é usada principalmente para entrega de correio eletrónico não autenticadas.)

Esta alteração no comportamento aplica-se apenas a novas subscrições e novas implementações desde de 15 de Novembro de 2017.

## <a name="recommended-method-of-sending-email"></a>Método recomendado para o envio de e-mail
Recomendamos que utilize serviços de reencaminhamento de SMTP autenticados (que normalmente se conectar por meio da porta TCP 587 ou 443, mas suportam outras portas, demasiado) para enviar e-mails de VMs do Azure ou de serviços aplicacionais do Azure. Estes serviços são utilizados para manter a reputação de IP ou de domínio para minimizar a possibilidade de que os fornecedores de e-mail de terceiros rejeitará a mensagem. Esses serviços de reencaminhamento de SMTP incluem, mas não está limitados a [SendGrid](https://sendgrid.com/partners/azure/). Também é possível que tem um serviço de reencaminhamento de SMTP seguro, que está em execução no local que pode utilizar.

Utilizar estes serviços de entrega de e-mail não está restringida no Azure, independentemente do tipo de subscrição.

## <a name="enterprise-agreement"></a>Contrato Enterprise
Para os utilizadores do Azure do contrato Enterprise, não há nenhuma alteração na capacidade técnica para enviar e-mail sem utilizar um reencaminhamento autenticado. Os utilizadores de Enterprise Agreement novos e existentes podem experimentar a entrega de e-mails de saída a partir de VMs do Azure diretamente para fornecedores de e-mail externos sem quaisquer restrições de plataforma do Azure. Embora não é garantido que Fornecedores de e-mail aceitarão e-mails recebidos a partir de qualquer usuário, tentativas de entrega não serão bloqueadas pela plataforma do Azure para as VMs nas subscrições de contrato Enterprise. Terá de trabalhar diretamente com fornecedores de e-mail para corrigir qualquer entrega de mensagens ou problemas de filtragem que envolvem provedores específicos de SPAM.

## <a name="pay-as-you-go"></a>"Pay As You Go"
Se se inscreveu no antes de 15 de Novembro de 2017 a pay as you go ou não ofertas de subscrição do Microsoft Partner Network, haverá nenhuma alteração na capacidade técnica para experimentar a entrega de e-mails de saída. Irá continuar a ser possível experimentar a entrega de e-mails de saída VMS do Azure dentro destas subscrições diretamente para fornecedores de e-mail externos sem quaisquer restrições de plataforma do Azure. Novamente, não é garantido que os fornecedores de e-mail aceitarão e-mails recebidos a partir de qualquer usuário e os utilizadores terão de trabalhar diretamente com fornecedores de e-mail para corrigir qualquer entrega de mensagens ou problemas de filtragem que envolvem provedores específicos de SPAM.

Para subscrições pay as you go ou Microsoft Partner Network criadas após 15 de Novembro de 2017, existirão restrições técnicas que bloqueiam o e-mail que é enviado diretamente a partir de VMs dentro destas subscrições. Se pretender que a capacidade de enviar e-mail a partir de VMs do Azure diretamente para fornecedores de e-mail externos (sem utilizar um reencaminhamento de SMTP autenticado), pode fazer um pedido para remover a restrição. Pedidos serão analisados e aprovados à discrição da Microsoft e vai ser concedidas apenas depois de serem efetuadas a adicionais as verificações contra fraudes. Para fazer um pedido, abra um pedido de suporte através do seguinte tipo de problema: **Técnicas** > **rede Virtual** > **conectividade** > **não é possível enviar correio eletrónico (SMTP/porta 25)**. Certifique-se de que adicione detalhes sobre por que a sua implementação tem que enviar e-mails diretamente para fornecedores de e-mail em vez de utilizar um reencaminhamento autenticado.

Quando uma subscrição pay as you go ou Microsoft Partner Network for isenta, VMs dentro dessa subscrição apenas irão ser excluídas no futuro.

> [!NOTE]
> A Microsoft se reserva o direito de revogar este isenção se for determinado que ocorreu uma violação dos termos de serviço.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, do Azure Pass, Azure no Open, Education, BizSpark e avaliação gratuita
Se criou um MSDN, Azure Pass, Azure no Open, Education, BizSpark, Azure Sponsorship, estudante do Azure, versão de avaliação gratuita ou qualquer subscrição do Visual Studio após 15 de Novembro de 2017, terá restrições técnicas esse e-mail de bloco que é enviada a partir de VMs nestes assinaturas diretamente para fornecedores de e-mail. As restrições são efetuadas para evitar abusos. Não existem pedidos para remover esta restrição vão ser concedidos.

Se estiver a utilizar estes tipos de subscrição, é recomendado que utilize serviços de reencaminhamento de SMTP, conforme descrito anteriormente no artigo.

## <a name="cloud-service-provider-csp"></a>Fornecedor de serviços cloud (CSP)

Se estiver a utilizar os recursos do Azure através do CSP, pode criar um incidente de suporte por meio de sua escolha de CSP e pode solicitar o CSP para criar um caso de desbloqueio em seu nome, se não é possível utilizar um reencaminhamento de SMTP seguro.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, ainda [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.
