---
title: Laboratórios de sala de aula em Serviços de Laboratório Azure — FAQ  Microsoft Docs
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
ms.openlocfilehash: 8d1ed128181d036af0026ae273c2c5bf1d3a066e
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443504"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Laboratórios de sala de aula em Serviços de Laboratório Azure — Perguntas frequentes (FAQ)
Obtenha respostas para algumas das perguntas mais comuns sobre laboratórios de salas de aula em Serviços de Laboratório Azure. 

## <a name="quotas"></a>Quotas

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>A quota por utilizador ou por semana ou por toda a duração do laboratório? 
A quota que definiu para um laboratório é para cada aluno durante toda a duração do laboratório. E o [tempo de funcionamento programado dos VMs](how-to-create-schedules.md) não conta com a quota atribuída a um utilizador. A quota é para o tempo fora do horário horário que um aluno gasta em VMs.  Para obter mais informações sobre quotas, consulte [As quotas definidas para os utilizadores.](how-to-configure-student-usage.md#set-quotas-for-users)

### <a name="if-professor-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Se o professor se virar contra um estudante VM, isso afeta a quota do aluno? 
Não. Não é assim. Quando o professor se volta contra o VM do aluno, não afeta a quota atribuída ao aluno. 

## <a name="schedules"></a>Agendas

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Todos os VMs do laboratório começam automaticamente quando um horário é definido? 
Não. Nem todos os VMs. Apenas os VMs que são atribuídos aos utilizadores em horário. Os VMs que não são atribuídos a um utilizador não são automaticamente iniciados. É por desígnio. 

## <a name="lab-accounts"></a>Contas de laboratório

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Por que não sou capaz de criar um laboratório por causa da indisponibilidade do endereço? 
Os laboratórios de sala de aula podem criar VMs de laboratório dentro de um intervalo de endereçoip que especifique ao criar a sua conta de laboratório no portal Azure. Quando uma gama de endereços é fornecida, cada laboratório que é criado depois de ter sido atribuído 512 endereços IP para VMs de laboratório. O intervalo de endereços para a conta de laboratório deve ser grande o suficiente para acomodar todos os laboratórios que pretende criar sob a conta do laboratório. 

Por exemplo, se tiver um bloco de /19 - 10.0.0.0.0/19, esta gama de endereços acomoda 8192 endereços IP e 16 laboratórios (8192/512 = 16 laboratórios). Neste caso, a criação de laboratório falha na criação do 17º laboratório.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Que portas devo abrir na firewall da minha organização para ligar às máquinas virtuais do Laboratório via RDP/SSH?

Os portos são: 49152-65535. Os laboratórios da sala de aula sentam-se atrás de um equilibrador de carga. Cada laboratório tem um único endereço IP público e cada máquina virtual no laboratório tem uma porta única. 

Você também pode ver o endereço IP privado de cada máquina virtual no separador de piscina de **máquinavirtual** da página inicial para laboratório no portal Azure. Se publicar um laboratório, o endereço IP público do laboratório não mudará, mas o IP privado e o número de porta de cada máquina virtual no laboratório podem mudar. Pode saber mais no artigo: [Definições de Firewall para Serviços de Laboratório Azure](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Que gama de endereços IP públicos devo abrir nas definições de firewall da minha organização para ligar às máquinas virtuais do Laboratório via RDP/SSH?
Consulte [as gamas e etiquetas de serviço Azure IP — Public Cloud,](https://www.microsoft.com/download/details.aspx?id=56519)que fornece a gama pública de endereços IP para centros de dados em Azure. Pode abrir os endereços IP para as regiões onde estão as suas contas de laboratório.

## <a name="virtual-machine-images"></a>Imagens de máquinas virtuais

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Como criador de laboratório, por que não posso permitir opções de imagem adicionais nas imagens de máquinas virtuais quando se cria um novo laboratório?

Quando um administrador te adiciona como criador de laboratório a uma conta de laboratório, é-te dada permissão para criar laboratórios. Mas não tem permissão para editar quaisquer configurações dentro da conta do laboratório, incluindo a lista de imagens de máquinas virtuais ativadas. Para permitir imagens adicionais, contacte o administrador da sua conta de laboratório para o fazer por si, ou peça ao administrador que o adicione como colaborador na conta do laboratório. O papel do Colaborador irá dar-lhe as permissões para editar a lista de imagens de máquinavirtual na conta de laboratório.

## <a name="users"></a>Utilizadores

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Quantos utilizadores podem estar num laboratório de sala de aula?
Pode adicionar 400 utilizadores a um laboratório de sala de aula. 

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

