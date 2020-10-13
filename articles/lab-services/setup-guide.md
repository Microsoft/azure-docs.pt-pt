---
title: Guia de configuração de laboratório de sala de aula acelerado para serviços de laboratório Azure
description: Este guia ajuda os criadores de laboratório a criar rapidamente uma conta de laboratório para uso dentro da escola.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 510aa97a0a47e62f627203495c601bb2538e19ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91652215"
---
# <a name="classroom-lab-setup-guide"></a>Guia de configuração do laboratório de sala de aula

O processo de publicação de um laboratório aos seus alunos pode demorar até várias horas, dependendo do número de máquinas virtuais (VMs) que serão criadas no seu laboratório. Permitir pelo menos um dia para criar um laboratório, para garantir que está funcionando corretamente e para dar tempo suficiente para publicar VMs dos alunos.

## <a name="understand-the-lab-requirements-of-your-class"></a>Compreenda os requisitos laboratoriais da sua classe

Antes de criar um novo laboratório, deve considerar as seguintes perguntas.

### <a name="what-software-requirements-does-the-class-have"></a>Que requisitos de software tem a classe?

Com base nos objetivos de aprendizagem da sua turma, decida qual o sistema operativo, aplicações e ferramentas que precisam de ser instalados nos VMs do laboratório. Para configurar vMs de laboratório, você tem três opções:

- **Use uma imagem do Azure Marketplace**: O Azure Marketplace fornece centenas de imagens que pode usar quando está a criar um laboratório. Para algumas aulas, uma destas imagens pode já conter tudo o que precisa para a sua aula.

- **Crie uma nova imagem personalizada**: Pode criar a sua própria imagem personalizada utilizando uma imagem do Azure Marketplace como ponto de partida e personalizando-a instalando software adicional e fazendo alterações de configuração.

- **Utilize uma imagem personalizada existente**: Pode reutilizar imagens personalizadas existentes que criou anteriormente, ou que foram criadas por outros administradores ou docentes da sua escola. Isto requer que os seus administradores tenham configurado uma galeria de imagens partilhada, que é um repositório para guardar imagens personalizadas.

> [!NOTE]
> Os seus administradores são responsáveis por ativar imagens do Azure Marketplace e imagens personalizadas para que possa usá-las. Coordene com o seu departamento de TI para garantir que as imagens de que precisa estão ativadas. As imagens personalizadas que cria são automaticamente ativadas para utilização dentro de laboratórios que possui.

### <a name="what-hardware-requirements-does-the-class-have"></a>Que requisitos de hardware tem a classe?

Há uma variedade de tamanhos de cálculo que você pode escolher:

- Tamanhos de virtualização aninhados, para que você possa dar acesso aos alunos a um VM que é capaz de hospedar vários VMs aninhados. Por exemplo, pode utilizar este tamanho de cálculo para cursos de networking.

- Tamanhos de GPU, para que os seus alunos possam usar tipos de aplicações intensivos em computador. Por exemplo, esta escolha pode ser apropriada para inteligência artificial e aprendizagem automática.

Consulte o guia sobre o [tamanho de VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) para ver a lista completa de tamanhos de cálculo disponíveis.

> [!NOTE]
> Dependendo da região do seu laboratório, poderá ver menos tamanhos de cálculo disponíveis, porque isso varia por região. Geralmente, deve selecionar o menor tamanho de cálculo que está mais próximo das suas necessidades. Com os Serviços Azure Lab, pode criar um novo laboratório com uma capacidade de computação diferente mais tarde, se necessário.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Que dependências tem a classe sobre o Azure externo ou os recursos de rede?

Se os seus VMs de laboratório precisarem de utilizar recursos externos, como uma base de dados, partilha de ficheiros ou servidor de licenciamento, coordene com os seus administradores para garantir que o seu laboratório tenha acesso a esses recursos.

Para aceder a recursos Azure que *não* são garantidos por uma rede virtual, não precisa de procurar configuração adicional pelos seus administradores. Pode aceder a estes recursos através da internet pública.

> [!NOTE]
> Deve considerar se pode reduzir as dependências do seu laboratório a recursos externos, fornecendo o recurso diretamente no VM. Por exemplo, para eliminar a necessidade de ler dados a partir de uma base de dados externa, pode instalar a base de dados diretamente no VM.  

### <a name="how-will-costs-be-controlled"></a>Como é que os custos serão controlados?

Os Serviços de Laboratório usam um modelo de preços pago, o que significa que só pagas pelo tempo que um VM de laboratório está a funcionar. Para controlar os custos, tem três opções que são normalmente usadas em conjunto entre si:

- **Horário**: Um horário permite controlar automaticamente quando os VM dos seus laboratórios são iniciados e desligados.
- **Quota**: A quota controla o número de horas que os alunos terão acesso a um VM fora do horário programado. Se a quota for alcançada enquanto um aluno a utiliza, o VM é automaticamente desligado. O aluno não pode reiniciar o VM a menos que a quota seja aumentada.
- **Paragem automática**: Quando ativado, a definição de paragem automática faz com que os VMs do Windows se desliguem automaticamente após um certo período de tempo, depois de um aluno ter desligado de uma sessão de Protocolo de Ambiente de Trabalho Remoto (PDR). Por predefinição, esta definição está desativada.  

### <a name="how-will-students-save-their-work"></a>Como é que os alunos vão salvar o seu trabalho?

Cada um dos alunos é atribuído o seu próprio VM, que lhes é atribuído para toda a vida do laboratório. Podem escolher:

- Guarde diretamente para o VM.
- Guarde para uma localização externa, como OneDrive ou GitHub.

É possível configurar o OneDrive automaticamente para os alunos nos seus VMs de laboratório.

> [!NOTE]
> Para garantir que os seus alunos tenham acesso continuado ao seu trabalho salvo fora do laboratório, e após o fim da aula, recomendamos que os alunos guardem o seu trabalho para um repositório externo.

### <a name="how-will-students-connect-to-their-vm"></a>Como é que os alunos se vão ligar aos seus VM?

Para RDP para Windows VMs, recomendamos que os estudantes utilizem o [cliente microsoft Remote Desktop](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). O cliente de desktop remoto suporta Macs, Chromebooks e Windows.

Para os VMs Linux, os alunos podem utilizar SSH ou RDP. Para que os alunos se conectem utilizando RDP, é necessário instalar e configurar os pacotes RDP e GUI necessários.

## <a name="set-up-your-lab"></a>Configurar o laboratório

Depois de entender os requisitos para o laboratório da sua turma, está pronto para o preparar. Siga os links desta secção para ver como configurar o seu laboratório.

1. **Criar um laboratório.** Consulte o tutorial sobre a [criação de um laboratório de sala de aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) para obter instruções.

    > [!NOTE]
    > Se a sua classe necessitar de virtualização aninhada, consulte os passos [para permitir a virtualização aninhada](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Personalize imagens e publique VMs de laboratório.** Ligue-se a um VM especial chamado VM modelo. Consulte os passos nos seguintes guias:
    - [Criar e gerir um modelo VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Utilizar uma galeria de imagens partilhadas](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Se estiver a utilizar o Windows, também deverá consultar as instruções na [preparação de um VM do modelo Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template). Estas instruções incluem passos para a criação do OneDrive e do Office para os seus alunos utilizarem.

1. **Gerir a piscina e a capacidade VM.** Pode escalar facilmente a capacidade de VM, conforme necessário pela sua classe. Tenha em mente que o aumento da capacidade de VM pode demorar várias horas, porque isso envolve a criação de novos VM. Veja os passos na [configuração e gestão de uma piscina VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords).

1. **Adicione e gere os utilizadores de laboratório.** Para adicionar utilizadores ao seu laboratório, consulte os passos nos seguintes tutoriais:
   - [Adicionar utilizadores ao laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Enviar convites aos utilizadores](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Para obter informações sobre os tipos de contas que os alunos podem utilizar, consulte [as contas do Estudante.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)
  
1. **Definir controlos de custos.** Para controlar os custos do seu laboratório, definir horários, quotas e encerramento automático. Veja os tutoriais seguintes:

   - [Definir um horário](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > Dependendo do tipo de sistema operativo que instalou, um VM pode demorar vários minutos a arrancar. Para garantir que um VM de laboratório esteja pronto para ser utilizado durante as horas programadas, recomendamos que inicie vMs com 30 minutos de antecedência.

   - [Definir quotas para utilizadores](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) e [fixar quota adicional para um utilizador específico](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Ativar encerramento automático ao desligar](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Os horários e quotas não se aplicam ao modelo VM, mas aplicam-se as definições de paragem automática. 
        > 
        > Quando se cria um laboratório, o modelo VM é criado, mas ainda não começou. Pode ligá-lo, ligar-se a ele e instalar qualquer software pré-requisito para o laboratório e depois publicá-lo. Quando publica o modelo VM, é automaticamente desligado para si se não o fez. 
        > 
        > Os VMs do modelo incorrem em **custos** ao correr, por isso certifique-se de que o modelo VM é desligado quando não precisa que esteja a funcionar. 


1. **Usa o painel.** Para obter instruções, consulte [a utilização do painel de instrumentos.](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)

    > [!NOTE]
    > O custo estimado mostrado no painel de instrumentos é o custo máximo que se pode esperar para o uso dos alunos do laboratório. Por exemplo, *não* será cobrado por horas de quota não-aproveitadas pelos seus alunos. Os custos estimados *não* refletem quaisquer encargos pela utilização do modelo VM, da galeria de imagens partilhada, ou quando o criador do laboratório inicia uma máquina de utilizador.

## <a name="next-steps"></a>Passos seguintes

- [Controlar a utilização de um laboratório de sala de aula](tutorial-track-usage.md)
  
- [Aceder a um laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)
