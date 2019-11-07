---
title: Laboratórios de sala de aula em Azure Lab Services – perguntas frequentes | Microsoft Docs
description: Encontre respostas para perguntas comuns sobre os laboratórios de sala de aula em Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 91c5c1480669829bad7f8d89ce360bb806d6c997
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646859"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Laboratórios da sala de aula em Azure Lab Services – perguntas frequentes (FAQ)
Obtenha respostas para algumas das perguntas mais comuns sobre os laboratórios de sala de aula em Azure Lab Services. 

## <a name="quotas"></a>Quotas

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>A cota por usuário ou por semana ou por toda a duração do laboratório? 
A cota que você definiu para um laboratório é para cada aluno toda a duração do laboratório. E o [tempo de execução agendado das VMs](how-to-create-schedules.md) não conta com a cota alocada para um usuário. A cota é para o tempo fora das horas de agendamento que um aluno gasta nas VMs.  Para obter mais informações sobre cotas, consulte [definir cotas para usuários](how-to-configure-student-usage.md#set-quotas-for-users).

## <a name="schedules"></a>Agendas

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Todas as VMs no laboratório são iniciadas automaticamente quando uma agenda é definida? 
Não. Nem todas as VMs. Somente as VMs que são atribuídas aos usuários em um agendamento. As VMs que não estão atribuídas a um usuário não são iniciadas automaticamente. É por design. 

## <a name="lab-accounts"></a>Contas de laboratório

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Por que não consigo criar um laboratório devido à indisponibilidade do intervalo de endereços? 
Os laboratórios de sala de aula podem criar VMs de laboratório dentro de um intervalo de endereços IP especificado ao criar sua conta de laboratório no portal do Azure. Quando um intervalo de endereços é fornecido, cada laboratório criado depois de ser alocado 512 endereços IP para VMs de laboratório. O intervalo de endereços para a conta do laboratório deve ser grande o suficiente para acomodar todos os laboratórios que você pretende criar na conta do laboratório. 

Por exemplo, se você tiver um bloco de/19-10.0.0.0/19, esse intervalo de endereços acomoda 8192 endereços IP e 16 laboratórios (8192/512 = 16 laboratórios). Nesse caso, a criação de laboratório falha na criação de 17 laboratórios.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Quais intervalos de porta devo abrir na configuração de firewall da minha organização para se conectar a máquinas virtuais de laboratório via RDP/SSH?

As portas são: 49152 – 65535. Os laboratórios de sala de aula ficam atrás de um balanceador de carga, portanto, todas as máquinas virtuais em um laboratório têm um único endereço IP e cada máquina virtual no laboratório tem uma porta exclusiva. Os números de porta e o endereço IP público podem ser alterados toda vez que o laboratório for republicado.

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Qual intervalo de endereços IP públicos devo abrir nas configurações de firewall da minha organização para se conectar às máquinas virtuais do laboratório via RDP/SSH?
Consulte [intervalos de IP do Azure e marcas de serviço – nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519), que fornece o intervalo de endereços IP públicos para data centers no Azure. Você pode abrir os endereços IP para as regiões nas quais suas contas de laboratório estão.

## <a name="blog-post"></a>Postagem no blog
Assine o [blog do Azure Lab Services](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Notificações de atualização
Assine [as atualizações do Lab Services](https://azure.microsoft.com/updates/?product=lab-services) para se manter informado sobre os novos recursos no Lab Services.

## <a name="general"></a>Geral
### <a name="what-if-my-question-isnt-answered-here"></a>E se minha pergunta não for respondida aqui?
Se sua pergunta não estiver listada aqui, informe-nos, para que possamos ajudá-lo a encontrar uma resposta.

- Poste uma pergunta no final das perguntas frequentes. 
- Para alcançar um público mais amplo, poste uma pergunta sobre o [Azure Lab Services – Stack Overflow Fórum](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- Para solicitações de recursos, envie suas solicitações e ideias para [Azure Lab Services – User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

