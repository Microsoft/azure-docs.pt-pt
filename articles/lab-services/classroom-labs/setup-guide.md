---
title: Guia acelerado de configuração de laboratório de sala de aula para serviços de laboratório azure
description: Este guia ajuda os criadores de laboratório a criar rapidamente uma conta de laboratório para uso dentro da escola.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: e00b6dd5af1cb489aee9e8b4c9f3337eb02e4b14
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878107"
---
# <a name="classroom-lab-setup-guide"></a>Guia de configuração de laboratório de sala de aula

O processo de publicação de um laboratório aos seus alunos pode demorar até várias horas, dependendo do número de máquinas virtuais (VMs) que serão criadas no seu laboratório. Dê pelo menos um dia para criar um laboratório, para garantir que está a funcionar corretamente e para dar tempo suficiente para publicar vMs dos alunos.

## <a name="understand-the-lab-requirements-of-your-class"></a>Compreenda os requisitos laboratoriais da sua classe

Antes de montar um novo laboratório, deve considerar as seguintes perguntas.

### <a name="what-software-requirements-does-the-class-have"></a>Que requisitos de software tem a classe?

Com base nos objetivos de aprendizagem da sua classe, decida qual o sistema operativo, aplicações e ferramentas que precisam de ser instalados nos VMs do laboratório. Para configurar vMs de laboratório, você tem três opções:

- **Use uma imagem do Azure Marketplace**: O Azure Marketplace fornece centenas de imagens que pode usar quando está a criar um laboratório. Para algumas aulas, uma destas imagens pode já conter tudo o que precisa para a sua aula.

- **Crie uma nova imagem personalizada:** Pode criar a sua própria imagem personalizada utilizando uma imagem do Azure Marketplace como ponto de partida e personalizando-a instalando software adicional e fazendo alterações de configuração.

- **Utilize uma imagem personalizada existente:** Pode reutilizar as imagens personalizadas existentes que criou anteriormente, ou que foram criadas por outros administradores ou docentes da sua escola. Isto requer que os seus administradores tenham configurado uma galeria de imagens partilhada, que é um repositório para guardar imagens personalizadas.

> [!NOTE]
> Os seus administradores são responsáveis por permitir imagens do Azure Marketplace e imagens personalizadas para que possa utilizá-las. Coordene com o seu departamento de TI para garantir que as imagens de que necessita estão ativadas. As imagens personalizadas que cria são automaticamente ativadas para serem utilizadas em laboratórios que possuam.

### <a name="what-hardware-requirements-does-the-class-have"></a>Que requisitos de hardware tem a classe?

Há uma variedade de tamanhos de computação que você pode escolher:

- Tamanhos de virtualização aninhados, para que você possa dar acesso aos alunos a um VM que é capaz de hospedar múltiplos VMs aninhados. Por exemplo, pode utilizar este tamanho de computação para cursos de networking.

- Tamanhos de GPU, para que os seus alunos possam usar tipos de aplicações intensivas por computador. Por exemplo, esta escolha pode ser apropriada para inteligência artificial e aprendizagem automática.

Consulte o guia sobre o tamanho do [VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) para ver a lista completa dos tamanhos de cálculo disponíveis.

> [!NOTE]
> Dependendo da região do seu laboratório, você pode ver menos tamanhos de computação disponíveis, porque isso varia por região. Geralmente, deve selecionar o menor tamanho de computação que está mais próximo das suas necessidades. Com o Azure Lab Services, pode criar um novo laboratório com uma capacidade de computação diferente mais tarde, se necessário.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Que dependências tem a classe em recursos externos do Azure ou da rede?

Se os seus VMs de laboratório precisarem de usar recursos externos, como uma base de dados, partilha de ficheiros ou servidor de licenciamento, coordene com os seus administradores para garantir que o seu laboratório tem acesso a esses recursos.

Para acesso aos recursos Do Azure que *não* são garantidos por uma rede virtual, não precisa de procurar uma configuração adicional pelos seus administradores. Pode aceder a estes recursos através da internet pública.

> [!NOTE]
> Deve considerar se pode reduzir as dependências do seu laboratório para recursos externos, fornecendo o recurso diretamente no VM. Por exemplo, para eliminar a necessidade de ler dados a partir de uma base de dados externa, pode instalar a base de dados diretamente no VM.  

### <a name="how-will-costs-be-controlled"></a>Como é que os custos serão controlados?

Os Serviços de Laboratório usam um modelo de preços pago, o que significa que só se paga o tempo que um VM de laboratório está a funcionar. Para controlar os custos, tem três opções que são normalmente utilizadas em conjunto entre si:

- **Horário**: Um horário permite controlar automaticamente quando os VMs dos seus laboratórios são iniciados e desligados.
- **Quota**: A quota controla o número de horas que os alunos terão acesso a um VM fora do horário programado. Se a quota for atingida enquanto um aluno a utiliza, o VM é automaticamente desligado. O aluno não é capaz de reiniciar o VM a menos que a quota seja aumentada.
- **Desativação automática**: Quando ativada, a definição de desligamento automático faz com que os VMs do Windows desliguem automaticamente após um certo período de tempo, depois de um aluno ter desligado de uma sessão do Protocolo de Ambiente de Trabalho Remoto (RDP). Por predefinição, esta definição está desativada.  

    > [!NOTE]
    > Esta definição existe atualmente apenas para windows.

### <a name="how-will-students-save-their-work"></a>Como é que os alunos vão salvar o seu trabalho?

Os alunos têm cada um o seu próprio VM, que lhes é atribuído para a vida do laboratório. Podem escolher:

- Guarde diretamente para o VM.
- Guarde para um local externo, como OneDrive ou GitHub.

É possível configurar o OneDrive automaticamente para os alunos nos seus VMs de laboratório.

> [!NOTE]
> Para garantir que os seus alunos tenham continuado o acesso ao seu trabalho guardado fora do laboratório, e após o final da aula, recomendamos que os alunos salvem o seu trabalho para um repositório externo.

### <a name="how-will-students-connect-to-their-vm"></a>Como é que os alunos se ligam ao seu VM?

Para RDP para VMs windows, recomendamos que os estudantes utilizem o [cliente do Microsoft Remote Desktop](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). O cliente Remote Desktop suporta Macs, Chromebooks e Windows.

Para Os VMs Linux, os alunos podem usar SSH ou RDP. Para que os alunos se conectem utilizando RDP, tem de instalar e configurar os pacotes RDP e GUI necessários.

## <a name="set-up-your-lab"></a>Configurar o laboratório

Depois de entender os requisitos para o laboratório da sua turma, está pronto para o preparar. Siga os links nesta secção para ver como configurar o seu laboratório.

1. **Criar um laboratório.** Consulte o tutorial sobre [a criação](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) de um laboratório de sala de aula para instruções.

    > [!NOTE]
    > Se a sua classe necessitar de virtualização aninhada, veja os passos para [permitir a virtualização aninhada.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)

1. **Personalize imagens e publique VMs de laboratório.** Ligue-se a um VM especial chamado modelo VM. Consulte os passos nos seguintes guias:
    - [Criar e gerir um Modelo VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Utilizar uma galeria de imagens partilhadas](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Se estiver a utilizar o Windows, deve também ver as instruções para [preparar um VM modelo do Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template). Estas instruções incluem passos para a configuração do OneDrive e do Office para que os seus alunos utilizem.

1. **Gerir a piscina e a capacidade vM.** Pode facilmente aumentar ou descer a capacidade vm, conforme necessário pela sua classe. Tenha em mente que o aumento da capacidade vm pode demorar várias horas, porque isso envolve a criação de novos VMs. Consulte os passos na [configuração e gestão](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)de uma piscina VM .

1. **Adicione e gerencie os utilizadores de laboratório.** Para adicionar utilizadores ao seu laboratório, consulte os passos nos seguintes tutoriais:
   - [Adicione utilizadores ao laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Enviar convites aos utilizadores](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Para obter informações sobre os tipos de contas que os alunos podem utilizar, consulte [as contas dos alunos.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)
  
1. **Detete os controlos de custos.** Para controlar os custos do seu laboratório, estabeleça horários, quotas e encerramento automático. Veja os tutoriais seguintes:

   - [Definir um horário](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > Dependendo do tipo de sistema operativo instalado, um VM pode demorar vários minutos a ser iniciado. Para garantir que um VM de laboratório está pronto para ser utilizado durante as horas programadas, recomendamos que inicie VMs com 30 minutos de antecedência.

   - [Definir quotas para utilizadores](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) e [definir quota adicional para um utilizador específico](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Ativar encerramento automático ao desligar](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Horários, quotas e paragem automática não se aplicam ao modelo VM. Como resultado, deve certificar-se de que desliga o modelo VM quando não está a ser utilizado. Caso contrário, continua a incorrer em custos. Além disso, por padrão, quando se cria um laboratório, o modelo VM é automaticamente iniciado. Certifique-se de que termina imediatamente a instalação do laboratório e desligue o modelo VM.

1. **Usa o painel de instrumentos.** Para obter instruções, consulte [a utilização do painel de instrumentos do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard).

    > [!NOTE]
    > O custo estimado mostrado no painel de instrumentos é o custo máximo que se pode esperar para o uso do laboratório pelos alunos. Por exemplo, *não* será cobrado pelos seus alunos por horas de quotas não utilizadas. Os custos estimados *não* refletem quaisquer encargos para a utilização do modelo VM ou da galeria de imagens partilhadas.

## <a name="next-steps"></a>Passos seguintes

- [Controlar a utilização de um laboratório de sala de aula](tutorial-track-usage.md)
  
- [Aceder a um laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)