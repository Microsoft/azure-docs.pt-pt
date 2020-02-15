---
title: Laboratórios de sala de aula em Azure Lab Services – perguntas frequentes | Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes (FAQ) sobre laboratórios de sala de aula em Serviços de Laboratório Azure.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: a0361203f4a8a2e57d179b39ba6da2fb62f68720
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252064"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Laboratórios da sala de aula em Azure Lab Services – perguntas frequentes (FAQ)
Obtenha respostas para algumas das perguntas mais comuns sobre os laboratórios de sala de aula em Azure Lab Services. 

## <a name="quotas"></a>Quotas

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>A cota por usuário ou por semana ou por toda a duração do laboratório? 
A cota que você definiu para um laboratório é para cada aluno toda a duração do laboratório. E o [tempo de funcionamento programado dos VMs](how-to-create-schedules.md) não conta com a quota atribuída a um utilizador. A cota é para o tempo fora das horas de agendamento que um aluno gasta nas VMs.  Para obter mais informações sobre quotas, consulte [As quotas definidas para os utilizadores.](how-to-configure-student-usage.md#set-quotas-for-users)

## <a name="schedules"></a>Agendas

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Todas as VMs no laboratório são iniciadas automaticamente quando uma agenda é definida? 
Não. Nem todas as VMs. Somente as VMs que são atribuídas aos usuários em um agendamento. Os VMs que não são atribuídos a um utilizador não são automaticamente iniciados. É por design. 

## <a name="lab-accounts"></a>Contas de laboratório

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Por que não consigo criar um laboratório devido à indisponibilidade do intervalo de endereços? 
Os laboratórios de sala de aula podem criar VMs de laboratório dentro de um intervalo de endereços IP especificado ao criar sua conta de laboratório no portal do Azure. Quando um intervalo de endereços é fornecido, cada laboratório criado depois de ser alocado 512 endereços IP para VMs de laboratório. O intervalo de endereços para a conta do laboratório deve ser grande o suficiente para acomodar todos os laboratórios que você pretende criar na conta do laboratório. 

Por exemplo, se você tiver um bloco de/19-10.0.0.0/19, esse intervalo de endereços acomoda 8192 endereços IP e 16 laboratórios (8192/512 = 16 laboratórios). Nesse caso, a criação de laboratório falha na criação de 17 laboratórios.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Quais intervalos de porta devo abrir na configuração de firewall da minha organização para se conectar a máquinas virtuais de laboratório via RDP/SSH?

As portas são: 49152 – 65535. Os laboratórios de sala de aula ficam atrás de um balanceador de carga, portanto, todas as máquinas virtuais em um laboratório têm um único endereço IP e cada máquina virtual no laboratório tem uma porta exclusiva. Os números de porta e o endereço IP público podem ser alterados toda vez que o laboratório for republicado.

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Qual intervalo de endereços IP públicos devo abrir nas configurações de firewall da minha organização para se conectar às máquinas virtuais do laboratório via RDP/SSH?
Consulte [as gamas e etiquetas de serviço Azure IP — Public Cloud,](https://www.microsoft.com/download/details.aspx?id=56519)que fornece a gama pública de endereços IP para centros de dados em Azure. Você pode abrir os endereços IP para as regiões nas quais suas contas de laboratório estão.

## <a name="virtual-machine-images"></a>Imagens de máquinas virtuais

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Como criador de laboratório, por que não posso permitir opções de imagem adicionais nas imagens de máquinas virtuais quando se cria um novo laboratório?

Quando um administrador te adiciona como criador de laboratório a uma conta de laboratório, é-te dada permissão para criar laboratórios. Mas não tem permissão para editar quaisquer configurações dentro da conta do laboratório, incluindo a lista de imagens de máquinas virtuais ativadas. Para permitir imagens adicionais, contacte o administrador da sua conta de laboratório para o fazer por si, ou peça ao administrador que o adicione como colaborador na conta do laboratório. O papel do Colaborador irá dar-lhe as permissões para editar a lista de imagens de máquinavirtual na conta de laboratório.

## <a name="users"></a>Utilizadores

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Quantos usuários podem estar em um laboratório de sala de aula?
Você pode adicionar até 400 usuários a um laboratório de sala de aula. 

## <a name="blog-post"></a>Post de blog
Subscreva o [blog Azure Lab Services.](https://azure.microsoft.com/blog/tag/azure-lab-services/)

## <a name="update-notifications"></a>Notificações de atualização
Subscreva [as atualizações dos Serviços](https://azure.microsoft.com/updates/?product=lab-services) labiais para se manter informado sobre novas funcionalidades nos Serviços labiais.

## <a name="general"></a>Geral
### <a name="what-if-my-question-isnt-answered-here"></a>E se a minha pergunta não for respondida aqui?
Se a sua pergunta não estiver listada aqui, avise-nos, para que possamos ajudá-lo a encontrar uma resposta.

- Poste uma pergunta no final desta FAQ. 
- Para chegar a um público mais vasto, publique uma pergunta sobre o [Azure Lab Services — Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- Para pedidos de funcionalidades, envie os seus pedidos e ideias para os [Serviços do Laboratório Azure — Voz do Utilizador](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

