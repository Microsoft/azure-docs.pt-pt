---
title: Laboratórios em Serviços de Laboratório Azure — FAQ / Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes (FAQ) sobre laboratórios em Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 757af8f30e9a71a3889d9f625c87a002af2e1302
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437189"
---
# <a name="labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Laboratórios em Serviços de Laboratório Azure — Perguntas frequentes (FAQ)
Obtenha respostas para algumas das perguntas mais comuns sobre laboratórios nos Serviços de Laboratório Azure. 

## <a name="quotas"></a>Quotas

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>A quota por utilizador ou por semana ou por duração total do laboratório? 
A quota que definiu para um laboratório é para cada aluno durante toda a duração do laboratório. E o [tempo de funcionamento programado dos VM não](how-to-create-schedules.md) conta com a quota atribuída a um utilizador. A quota é para o tempo fora do horário horário que um aluno gasta em VMs.  Para obter mais informações sobre quotas, consulte [Definição de quotas para utilizadores.](how-to-configure-student-usage.md#set-quotas-for-users)

### <a name="if-educator-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Se a educadora liga um VM estudantil, isso afeta a quota de estudante? 
Não. Nenhum. Quando a educadora liga o VM do aluno, não afeta a quota atribuída ao aluno. 

## <a name="schedules"></a>Agendas

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Todos os VMs no laboratório começam automaticamente quando um horário é definido? 
Não. Nem todos os VMs. Apenas os VMs que são atribuídos aos utilizadores num horário. Os VMs que não são atribuídos a um utilizador não são iniciados automaticamente. É por desígnio. 

## <a name="lab-accounts"></a>Contas de laboratório

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Por que não posso criar um laboratório por causa da indisponibilidade do intervalo de endereços? 

Os laboratórios podem criar VMs de laboratório dentro de um intervalo de endereços IP que especifique ao criar a sua conta de laboratório no portal Azure. Quando um intervalo de endereços é fornecido, cada laboratório que é criado depois de ter atribuído 512 endereços IP para VMs de laboratório. O intervalo de endereços para a conta do laboratório deve ser grande o suficiente para acomodar todos os laboratórios que pretende criar na conta do laboratório. 

Por exemplo, se tiver um bloco de /19 - 10.0.0.0/19, este intervalo de endereços acomoda 8192 endereços IP e 16 laboratórios (8192/512 = 16 laboratórios). Neste caso, a criação de laboratório falha na criação do 17º laboratório.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Que gamas de portas devo abrir na definição de firewall da minha organização para ligar às máquinas virtuais do Laboratório via RDP/SSH?

Os portos são: 49152-65535. Os laboratórios sentam-se atrás de um equilibrador de carga. Cada laboratório tem um único endereço IP público e cada máquina virtual no laboratório tem uma porta única. 

Também pode ver o endereço IP privado de cada máquina virtual no separador de piscina de **máquina virtual** da página inicial para laboratório no portal Azure. Se republica um laboratório, o endereço IP público do laboratório não mudará, mas o número de IP e porta privado de cada máquina virtual no laboratório pode mudar. Pode saber mais no artigo: [Firewall settings for Azure Lab Services](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Que intervalo de endereço IP público devo abrir nas definições de firewall da minha organização para ligar às máquinas virtuais do Laboratório via RDP/SSH?
Consulte [gamas IP Azure e Tags de Serviço — Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519), que fornece o intervalo de endereços IP público para centros de dados em Azure. Pode abrir os endereços IP para as regiões onde estão as suas contas de laboratório.

## <a name="virtual-machine-images"></a>Imagens de máquina virtual

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Como criador de laboratório, por que não posso permitir opções de imagem adicionais nas imagens de máquinas virtuais que caem ao criar um novo laboratório?

Quando um administrador te adiciona como criador de laboratório a uma conta de laboratório, é-te dada a permissão para criar laboratórios. Mas não tem as permissões para editar quaisquer definições dentro da conta do laboratório, incluindo a lista de imagens de máquinas virtuais ativadas. Para ativar imagens adicionais, contacte o administrador da conta de laboratório para o fazer por si, ou peça ao administrador para o adicionar como contribuinte à conta de laboratório. A função Colaborador dará-lhe as permissões para editar a lista de imagens de máquina virtual na conta de laboratório.

### <a name="can-i-attach-additional-disks-to-a-virtual-machine"></a>Posso anexar discos adicionais a uma máquina virtual?
Não. não é possível anexar discos adicionais a um VM num laboratório de sala de aula. 

## <a name="users"></a>Utilizadores

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Quantos utilizadores podem estar num laboratório de sala de aula?
Pode adicionar até 400 utilizadores a um laboratório de sala de aula. 

## <a name="blog-post"></a>Blog post
Subscreva o [blog Azure Lab Services](https://aka.ms/azlabs-blog).

## <a name="update-notifications"></a>Notificações de atualização
Subscreva [as atualizações dos Serviços de Laboratório](https://azure.microsoft.com/updates/?product=lab-services) para se manter informado sobre novas funcionalidades nos Serviços de Laboratório.

## <a name="general"></a>Geral
### <a name="what-if-my-question-isnt-answered-here"></a>E se a minha pergunta não for respondida aqui?
Se a sua pergunta não estiver listada aqui, avise-nos, para que possamos ajudá-lo a encontrar uma resposta.

- Publique uma pergunta no final desta FAQ. 
- Para chegar a um público mais vasto, publique uma pergunta sobre os [Serviços Azure Lab — Fórum comunitário tecnológico](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices). 
- Para pedidos de funcionalidades, envie os seus pedidos e ideias para os [Serviços Azure Lab — User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

