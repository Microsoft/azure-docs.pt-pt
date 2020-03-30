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
ms.openlocfilehash: d58e11551c2857c269e8985e81f84138f6d389ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370816"
---
# <a name="classroom-lab-setup-guide"></a>Guia de configuração de laboratório de sala de aula

O processo de publicação de um laboratório aos seus alunos pode demorar até várias horas, dependendo do número de máquinas virtuais (VMs) que serão criadas no seu laboratório.  Devia dar-se pelo menos um dia para criar um laboratório para garantir que está a funcionar corretamente e para dar tempo suficiente para publicar vMs dos alunos.

## <a name="understand-your-classs-lab-requirements"></a>Compreenda os requisitos de laboratório da sua turma

Antes de montar um novo laboratório, deve considerar as seguintes perguntas:

**Que requisitos de software tem a classe?**

Com base nos objetivos de aprendizagem da sua classe, deve decidir qual o SISTEMA, aplicações, ferramentas, etc. precisam de ser instalados nos VMs do laboratório.   Para configurar vMs de laboratório, você tem três opções:

- **Utilize uma imagem do Azure Marketplace** – o Marketplace fornece centenas de imagens que pode utilizar ao criar um laboratório.  Para algumas aulas, uma imagem de marketplace pode já conter tudo o que precisa para a sua aula.

- **Crie uma nova imagem personalizada** - Pode criar a sua própria imagem personalizada usando uma imagem de mercado como ponto de partida e personalizando-a instalando software adicional, fazendo alterações de configuração, etc.

- **Utilize uma imagem personalizada existente** - Pode reutilizar as imagens personalizadas existentes que criou anteriormente ou que foram criadas por outros administradores ou docentes na sua escola; isto requer que os seus administradores tenham configurado uma Galeria de Imagem Partilhada que é um repositório para guardar imagens personalizadas.

> [!NOTE]
> Os seus administradores são responsáveis por ativar o Marketplace e imagens personalizadas para que possa utilizá-las; terá de coordenar com o seu departamento de TI para garantir que as imagens de que necessita estão ativadas.  As imagens personalizadas que cria são automaticamente ativadas para serem utilizadas em laboratórios que possuam.

**Que requisitos de hardware tem a classe?**

Há uma variedade de tamanhos de computação que você pode escolher que inclui:

- Tamanhos de virtualização aninhados para que possa dar acesso aos alunos a um VM capaz de hospedar vários VMs aninhados; por exemplo, este tamanho da computação é frequentemente usado para cursos de Networking.

- Tamanhos de GPU para que os seus alunos possam usar tipos de aplicações intensivas em computador, como para Inteligência Artificial e Machine Learning.

Consulte o guia sobre o tamanho do [VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) para ver a lista completa dos tamanhos de cálculo disponíveis.

> [!NOTE]
> Dependendo da região em que o seu laboratório será criado, poderá ver menos tamanhos de computação disponíveis, uma vez que este varia por região.  A nossa recomendação geral é selecionar o menor tamanho de computação que está mais próximo das suas necessidades.  Com o Azure Lab Services, pode criar um novo laboratório com uma capacidade de computação diferente mais tarde, se necessário.

**Que dependências tem a classe em recursos externos do Azure ou da rede?**

Se os seus VMs de laboratório precisarem de usar recursos externos, tais como uma base de dados, partilha de ficheiros, servidor de licenciamento, etc., terá de coordenar com os seus administradores para garantir que o seu laboratório tem acesso a esses recursos.

Para acesso a recursos Azure que *não* são garantidos por uma rede virtual, então pode aceder a esses recursos através da internet pública sem qualquer configuração adicional pelos seus administradores.

> [!NOTE]
> Deve considerar se pode reduzir as dependências do seu laboratório para recursos externos, fornecendo o recurso diretamente no VM.  Por exemplo, para eliminar a necessidade de ler dados a partir de uma base de dados externa, pode instalar a base de dados diretamente no VM.  

**Como é que os custos serão controlados?**

Os Serviços de Laboratório usam um modelo de preços pago, o que significa que só pagas pelo tempo que um VM de laboratório está a funcionar.  Para controlar os custos, tem três opções que são normalmente utilizadas em conjunto entre si:

- **Horário** - Um horário permite controlar automaticamente quando os VMs dos seus laboratórios são iniciados e desligados.
- **Quota** - A quota controla o número de horas que os alunos terão acesso a um VM fora do horário programado.  Se a quota for atingida enquanto um aluno a utiliza, o VM é automaticamente desligado e o aluno não poderá reiniciar o VM a menos que a quota seja aumentada.
- **Desativação automática** - Quando ativada, a definição de desligamento automático faz com que os VMs do Windows desliguem automaticamente após um certo período de tempo, uma vez que um aluno tenha desligado da sua sessão de RDP.  Por predefinição, esta definição está desativada.  

    > [!NOTE]
    > Esta definição existe atualmente apenas para windows.

**Como é que os alunos vão salvar o seu trabalho?**

Os alunos têm cada um o seu próprio VM que lhes é atribuído para a vida do laboratório.  Podem escolher:

- Guarde diretamente para o VM.
- Guarde para um repositório externo, como OneDrive, GitHub, etc.

Para utilizar o OneDrive, pode optar por configurá-lo automaticamente para os alunos nos seus VMs de laboratório.  Informações adicionais sobre este relatório são fornecidas abaixo.

> [!NOTE]
> Para garantir que os seus alunos tenham acesso continuado ao seu trabalho guardado fora do laboratório, o que inclui após o final da aula, recomendamos que os alunos guardem o seu trabalho para um repositório externo.

**Como é que os alunos se ligam ao seu VM?**

Para RDP para VMs windows, recomendamos que os estudantes utilizem [o cliente do Microsoft Remote Desktop](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  O cliente Remote Desktop suporta Macs, Chromebooks e Windows.

Para os VMs Linux, os alunos podem utilizar sSH ou RDP.   Para ligar utilizando RDP, é responsável por instalar e configurar as embalagens de RDP e GUI necessárias.  Mais detalhes sobre este tem a diante.

## <a name="set-up-your-lab"></a>Configurar o laboratório

Assim que entenderes os requisitos para o laboratório da tua turma, estás pronto para o preparar.  Siga os links nesta secção para ver como configurar o seu laboratório.

1. **Criar um laboratório**

   Consulte o tutorial sobre [a criação](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) de um laboratório de sala de aula para instruções.

    > [!NOTE]
    > Se a sua aula necessitar de virtualização aninhada, consulte os passos do guia como orientar que demonstre [miseração.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)

1. **Personalize imagens e publique VMs de laboratório**

    Para personalizar imagens e publicar VMs no seu laboratório, conecta-se a um VM especial chamado Modelo VM; Consulte os passos para os seguintes guias:
    - [Criar e gerir um Modelo VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Use uma galeria de imagem partilhada](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Se estiver a utilizar o Windows, deve também consultar as instruções no guia de como preparar [um VM modelo do Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template).  Estas instruções incluem passos para a configuração do OneDrive e do Office para que os seus alunos utilizem.

1. **Gerir piscina e capacidade VM**

   Pode facilmente aumentar ou descer a capacidade vm conforme necessário pela sua classe.  Tenha em mente que o aumento da capacidade vm pode demorar várias horas, uma vez que isso envolve a criação de novos VMs.  Consulte os passos no guia de como [configurar e gerir uma piscina VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords).

1. **Adicionar e gerir utilizadores de laboratório**

   Para adicionar utilizadores ao seu laboratório, consulte os passos nos seguintes tutoriais:
   - [Adicione utilizadores ao laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Enviar convites aos utilizadores](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Consulte também o seguinte artigo para obter informações sobre os tipos de contas que os alunos podem utilizar:
    - [Contas de estudantes](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)
  
1. **Definir controlos de custos**

    Para controlar os custos do seu laboratório, estabeleça horários, quotas e encerramento automático; consulte as instruções nos seguintes tutoriais:

   - [Definir um horário](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > Dependendo do tipo de SISTEMA instalado, um VM pode demorar vários minutos a ser iniciado.  Para garantir que um VM de laboratório está pronto para ser utilizado durante as horas programadas, recomendamos que inicie Os VMs com 30 minutos de antecedência para garantir que os VMs estão a funcionar e prontos a ser utilizados.

   - [Definir quotas para utilizadores](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) e [definir quota adicional para um utilizador específico](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quota-for-a-specific-user)
  
   - [Ativar encerramento automático ao desligar](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Os horários, quotas e paragem automática *não* se aplicam ao modelo VM.  Como resultado, deve certificar-se de que desliga o modelo VM quando não está a ser utilizado, caso contrário continuará a incorrer em custos.  Além disso, por padrão, quando você cria um laboratório, o modelo VM é automaticamente iniciado, então você vai querer ter certeza de que você termina imediatamente de configurar o laboratório e desligar o modelo VM.

1. **Utilizar o dashboard**

    Consulte o guia como utilizar [o painel de instrumentos do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard) para obter instruções.

    > [!NOTE]
    > Os custos estimados mostrados no painel de instrumentos são o custo máximo que se pode esperar para o uso do laboratório pelos alunos.  Por exemplo, *não* será cobrado pelos seus alunos por horas de quotas não utilizadas.  Os custos estimados *não* refletem quaisquer encargos para a utilização do modelo VM ou da Galeria de Imagem Partilhada.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos:

- [Controlar a utilização de um laboratório de sala de aula](tutorial-track-usage.md)
  
- [Aceder a um laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)
