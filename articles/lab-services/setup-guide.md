---
title: Guia de configuração de laboratório acelerado para serviços de laboratório Azure
description: Se és um criador de laboratório, este guia pode ajudar-te a criar rapidamente uma conta de laboratório na tua escola.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07f0d92ebd926616f1318b430bec2de32f753f7c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021736"
---
# <a name="lab-setup-guide"></a>Guia de configuração do laboratório

Neste guia, aprenderá a criar um laboratório para os alunos da sua escola.

O processo de publicação de um laboratório aos seus alunos pode demorar várias horas. A quantidade de tempo de configuração depende do número de máquinas virtuais (VMs) que pretende criar no seu laboratório. Permita pelo menos um dia para garantir que o laboratório está funcionando corretamente e para dar tempo suficiente para publicar os VM dos seus alunos.

## <a name="understand-the-lab-requirements-of-your-class"></a>Compreenda os requisitos laboratoriais da sua classe

Antes de criar um novo laboratório, deve considerar as seguintes perguntas.

### <a name="what-software-requirements-does-the-class-have"></a>Que requisitos de software tem a classe?

Consulte os objetivos de aprendizagem da sua turma à medida que decide qual o sistema operativo, aplicações e ferramentas que precisa de instalar nos VMs de laboratório. Para configurar vMs de laboratório, você tem três opções:

- **Use uma imagem do Azure Marketplace**: O Azure Marketplace fornece centenas de imagens que pode usar quando está a criar um laboratório. Para algumas aulas, uma destas imagens pode já conter tudo o que precisa para a sua aula.

- **Crie uma nova imagem personalizada**: Pode criar a sua própria imagem personalizada usando uma imagem do Azure Marketplace como ponto de partida. Em seguida, pode personalizá-lo instalando software adicional e fazendo alterações de configuração.

- **Utilize uma imagem personalizada existente**: Pode reutilizar imagens personalizadas que criou anteriormente, ou imagens que foram criadas por outros administradores ou docentes da sua escola. Para utilizar imagens personalizadas, os seus administradores precisam de configurar uma Galeria de Imagens Partilhadas.  A Shared Image Gallery é um repositório que é usado para guardar imagens personalizadas.

> [!NOTE]
> Os seus administradores são responsáveis por ativar imagens do Azure Marketplace e imagens personalizadas para que possa usá-las. Coordene com o seu departamento de TI para garantir que as imagens de que necessita estão ativadas. As imagens personalizadas que cria são automaticamente ativadas para utilização dentro de laboratórios que possui.

### <a name="what-hardware-requirements-does-the-class-have"></a>Que requisitos de hardware tem a classe?

Pode escolher entre uma variedade de tamanhos de cálculo:

- **Tamanhos de virtualização aninhados**: Permite-lhe dar aos alunos acesso a um VM que pode hospedar vários VMs aninhados. Por exemplo, você pode usar este tamanho de computação para aulas de networking ou hacking ético.

- **Tamanhos gpu**: Permite que os seus alunos utilizem tipos de aplicações intensivos em computador. Por exemplo, esta escolha é frequentemente usada com inteligência artificial e machine learning.

Para obter orientações sobre a seleção do tamanho de VM apropriado, consulte:
- [Tamanho de VM](./administrator-guide.md#vm-sizing)
- [Mude-se de um laboratório físico para os Serviços de Laboratório Azure](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> Como a disponibilidade do tamanho do cálculo varia por região, menos tamanhos podem estar disponíveis para o seu laboratório. Geralmente, deve selecionar o menor tamanho de cálculo que se adequa às suas necessidades. Com os Serviços Azure Lab, pode criar um novo laboratório com maior capacidade de computação mais tarde, se for necessário.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Que dependências tem a classe sobre o Azure externo ou os recursos de rede?
Os seus VMs de laboratório podem precisar de acesso a recursos externos, como uma base de dados, uma partilha de ficheiros ou um servidor de licenciamento.  Para permitir que os seus VMs de laboratório utilizem recursos externos, coordene com os seus administradores de TI.

> [!NOTE]
> Deve considerar se pode reduzir a dependência do seu laboratório em recursos externos, fornecendo recursos de rede diretamente no VM. Por exemplo, para eliminar a necessidade de ler dados a partir de uma base de dados externa, pode instalar a base de dados diretamente no VM.  

### <a name="how-will-you-control-costs"></a>Como vai controlar os custos?
Os Serviços de Laboratório usam um modelo de preços pago, o que significa que só pagas pelo tempo que um VM de laboratório está a funcionar. Para controlar os custos, utilize todas ou todas as seguintes opções:

- **Horário**: Utilize horários para controlar automaticamente quando os seus VMs de laboratório forem iniciados e desligados.
- **Quota**: Utilize quotas para controlar o número de horas a que os alunos tenham acesso a um VM fora do horário programado.  Quando um aluno usa um VM e atinge uma quota, o VM desliga-se automaticamente.  O aluno não pode reiniciar o VM a menos que aumente a quota.
- **Paragem automática**: Quando ativar a definição de paragem automática, os VMs do Windows desligam-se automaticamente depois de um aluno ter desligado de uma sessão de Protocolo de Ambiente de Trabalho Remoto (PDR). Por predefinição, esta definição está desativada.

Para obter mais informações sobre o controlo dos custos, consulte:
- [Cálculo de custos](./cost-management-guide.md#estimate-the-lab-costs)
- [Gerir os custos](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>Como é que os alunos vão salvar o seu trabalho?
Cada aluno individual é atribuído um VM para a vida do laboratório. Os alunos podem salvar o seu trabalho:

- Para o VM.
- Para uma localização externa, como OneDrive ou GitHub. É possível configurar o OneDrive automaticamente para os alunos nos seus VMs de laboratório.

> [!NOTE]
> Para garantir que os seus alunos tenham acesso continuado ao seu trabalho salvo fora do laboratório e após o fim da aula, recomendamos que guardem o seu trabalho para um repositório externo.

### <a name="how-will-students-connect-to-their-vms"></a>Como é que os alunos se vão ligar aos seus VMs?
Para ligações RDP a VMs do Windows, recomendamos que os estudantes utilizem o [cliente de ambiente de trabalho remoto](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)da Microsoft . O cliente remote desktop suporta dispositivos Mac, Chromebook e Windows.

Para os VMs Linux, os alunos podem utilizar o protocolo Secure Shell (SSH) ou RDP. Para que os alunos se conectem utilizando RDP, tem de instalar e configurar os pacotes necessários de DDP e interface gráfica de utilizador (GUI).

### <a name="will-students-also-use-microsoft-teams"></a>Os alunos também vão usar as Equipas microsoft?
O Azure Lab Services integra-se com as Equipas microsoft para que os membros do corpo docente possam criar e gerir os seus laboratórios em Equipas.  Da mesma forma, os alunos podem aceder aos seus laboratórios em Equipas.

Para mais informações, consulte [os Serviços Azure Lab em Microsoft Teams](./lab-services-within-teams-overview.md).

## <a name="set-up-your-lab"></a>Configurar o laboratório

Depois de entender os requisitos para o laboratório da sua turma, está pronto para o preparar. Para saber como, siga os links desta secção. Também são fornecidas instruções para a criação de laboratórios em Equipas.

1. **Criar um laboratório.** Veja os tutoriais seguintes:
    - [Criar um laboratório de sala de aula](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [Criar um laboratório em Equipas](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > Se a sua classe necessitar de virtualização aninhada, consulte [Ativar a virtualização aninhada](./how-to-enable-nested-virtualization-template-vm.md).

1. **Personalize as imagens e publique VMs de laboratório.** Para ligar a um VM especial chamado VM modelo, consulte:
    - [Criar e gerir um modelo VM](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [Utilizar uma galeria de imagens partilhadas](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Se estiver a utilizar o Windows, consulte também [configurar um VM do modelo Windows](./how-to-prepare-windows-template.md). Estas instruções incluem passos para a configuração do OneDrive e do Microsoft Office para os seus alunos.

1. **Gerir a piscina e a capacidade VM**. Pode escalar facilmente a capacidade de VM, conforme necessário pela sua classe. Tenha em mente que o aumento da capacidade de VM pode demorar várias horas porque estão a ser criados novos VMs. Consulte os seguintes artigos:
    - [Configurar e gerir uma piscina VM](./how-to-set-virtual-machine-passwords.md)
    - [Gerir uma piscina VM em Lab Services em Equipas](./how-to-manage-vm-pool-within-teams.md)

1. **Adicione e gere os utilizadores de laboratório.** Para adicionar utilizadores ao seu laboratório, consulte:
   - [Adicionar utilizadores ao laboratório](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [Enviar convites aos utilizadores](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [Gerir listas de utilizadores de Serviços de Laboratório em Equipas](./how-to-manage-user-lists-within-teams.md)

    Para obter informações sobre os tipos de contas que os alunos podem utilizar, consulte [as contas do Estudante.](./how-to-configure-student-usage.md#student-accounts)
  
1. **Definir controlos de custos**. Para definir um horário, estabelecer quotas e permitir o encerramento automático, consulte os seguintes tutoriais:

   - [Definir um horário](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > Dependendo do sistema operativo que instalou, um VM pode demorar vários minutos a começar. Para garantir que um VM de laboratório esteja pronto para ser utilizado durante as horas programadas, recomendamos que o inicie com 30 minutos de antecedência.

   - [Definir quotas para utilizadores](./how-to-configure-student-usage.md#set-quotas-for-users) e [fixar quotas adicionais para utilizadores específicos](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [Ativar encerramento automático ao desligar](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > Os horários e quotas não se aplicam ao modelo VM, mas aplicam-se as definições automáticas de paragem. 
        > 
        > Quando se cria um laboratório, o modelo VM é criado, mas não iniciado. Pode iniciar o modelo VM, ligar-lhe, instalar qualquer software pré-requisito para o laboratório e, em seguida, publicá-lo. Quando publica o modelo VM, este é automaticamente desligado para si se não o tiver feito manualmente. 
        > 
        > Os VMs do modelo incorrem no *custo* quando estão em execução, por isso certifique-se de que o modelo VM é desligado quando não precisa dele para estar em execução.

    - [Criar e gerir horários de Serviços de Laboratório em Equipas](./how-to-create-schedules-within-teams.md) 

1. **Utilize o painel de instrumentos.** Para obter instruções, consulte [utilize o painel de instrumentos da sala de aula.](./use-dashboard.md)

    > [!NOTE]
    > O custo estimado mostrado no painel de instrumentos é o custo máximo que se pode esperar para o uso do laboratório estudantil. Por exemplo, *não* será cobrado por horas de quota não-aproveitadas pelos seus alunos. Os custos estimados *não* refletem quaisquer encargos pela utilização do modelo VM, da galeria de imagens partilhada, ou quando o criador do laboratório inicia uma máquina de utilizador.

## <a name="next-steps"></a>Próximos passos

Como parte da gestão dos seus laboratórios, consulte os seguintes artigos:
- [Rastrear o uso do laboratório de sala de aula](tutorial-track-usage.md)  
- [Aceder a um laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)
