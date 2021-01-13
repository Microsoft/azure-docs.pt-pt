---
title: Começar com os Serviços de Laboratório Azure
description: Este artigo descreve como começar com os Serviços Azure Lab.
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: d260ace7d7819c3ca2db96d6c4984bf834e170da
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165047"
---
# <a name="get-started-with-lab-services"></a>Começar com os Serviços de Laboratório 

A Azure Lab Services fornece aos alunos e professores acesso a laboratórios de computadores virtuais diretamente dos seus próprios computadores.

Os professores precisam de saber como ensinar os alunos/pais a utilizarem os Serviços de Laboratório na sua instrução através de um para um aluno emitido hardware. Como resultado, os estudantes seriam capazes de aceder ao software padrão da indústria necessário para os seus programas de estudo através de Máquinas Virtuais (VM). 

Um VM é um ambiente virtual que funciona como um computador virtual. Os VMs têm o seu próprio processador, memória e armazenamento. Os VMs fornecem um substituto para uma máquina real e podem dar aos utilizadores acesso a sistemas operativos e software sem a necessidade de os ter no seu próprio dispositivo. A Azure Lab Services fornece uma ferramenta para os alunos acederem e navegarem em VMs e para que os funcionários gerem os seus laboratórios de computadores virtuais. 

Este artigo fornece informações para o pessoal docente sobre como aceder, gerir e ensinar alunos/pais a utilizarem os Serviços do Laboratório Azure.

## <a name="key-concepts"></a>Conceitos-chave

### <a name="quota-hours"></a>Horário de quota

Os alunos podem aceder aos seus VM a qualquer momento durante o horário de aulas sem afetar as suas horas de quota. As quotas são definidas para todo o semestre e determinam o número de horas que um aluno pode utilizar o seu VM fora do horário regular de aulas.

8 Hrs por semana, resets no domingo - não cumulativo.

Para mais informações, consulte [a quota definida.](how-to-configure-student-usage.md#set-quotas-for-users)

### <a name="automatic-shut-down"></a>Paragem automática

Para ajudar a reduzir os custos e poupar as horas de quota dos alunos, as paragens automáticas são ativadas para os laboratórios. As paragens automáticas desligam os VMs após um período de inatividade (sem entradas de rato ou teclado). As paragens automáticas funcionam em duas fases, primeiro um aluno será desligado do VM após um período de inatividade. Neste momento, o VM ainda está **em execução** e os alunos são capazes de se conectar. Após outro período de inatividade desligado, o VM desliga-se sozinho.

As paragens automáticas são uma importante ferramenta de poupança de custos, no entanto representam um desafio para os estudantes no que diz respeito a salvar o seu trabalho e a renderizar grandes ficheiros de projetos. Se os seus alunos estiverem frequentemente desligados ou os VMs estiverem a desligar-se demasiado rapidamente, contacte o administrador do CTE. 

Para obter mais informações, consulte [configurar o encerramento automático de VMs para uma conta de laboratório](how-to-configure-lab-accounts.md).

### <a name="managing-virtual-machines"></a>Gerir Máquinas Virtuais

A gestão do laboratório permite que os professores controlem coisas como a capacidade de laboratório (o número de VMs disponíveis para os alunos) e iniciar, parar ou repor os VMs manualmente. os professores também podem ligar-se aos VMs para experimentar a interface dos alunos, aceder a ficheiros e problemas de resolução de problemas com o software ou com o próprio VM.

A coisa mais importante a lembrar na gestão dos VMs é que sempre que uma máquina está **a funcionar,** estamos a incorrer em custos, mesmo que ninguém esteja ligado ao VM.

## <a name="lab-dashboards"></a>Painéis de laboratório

### <a name="overview"></a>Descrição geral

Os dashboards para laboratórios em Azure Lab Services fornecem uma imagem instantânea de diferentes aspetos de um determinado laboratório, incluindo informações VM, número de VMs atribuídos e não atribuídos, número de utilizadores registados e não registados e informações sobre horários de laboratório. 

> [!NOTE]
> Embora a maioria dos aspetos administrativos do dashboard e do [website Azure Lab Services](https://labs.azure.com/) sejam visíveis para os professores, permissões específicas ao seu papel podem afetar a sua capacidade de modificar determinados critérios no painel de instrumentos. Se encontrar algum problema com a sua configuração de laboratório em particular, contacte o administrador do CTE.

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="o portal Azure Lab Services":::

### <a name="examine-a-dashboard"></a>Examine um painel

1. Navegue e inscreva-se no site da [Azure Lab Services](https://labs.azure.com/).
1. Selecione o seu laboratório.
1. Verá um **painel no** lado esquerdo da janela. Clique no **Dashboard** e verá uma série de azulejos no seu painel de instrumentos.
1. Abaixo dos custos & azulejo **de faturação, existem** também azulejos para Modelos, Piscinas de Máquinas Virtuais, Utilizadores e Horários, que permitem modificar aspetos e ver mais detalhes no Laboratório de Sala de Aula.

    * Modelo - descreve a data em que o modelo foi criado, e publicado pela última vez. 
    * Piscina de Máquinas Virtuais - número de VMs atribuídos e não atribuídos.
    * Utilizadores - número de utilizadores registados e utilizadores que foram adicionados ao laboratório, mas não registados.
    * Horários - apresenta os próximos eventos agendados para o laboratório e um link para ver mais eventos.

Para obter mais informações, consulte [o painel de instrumentos .](use-dashboard.md)

### <a name="manually-starting-vms"></a>VMs de arranque manual

1. A partir da página **de piscina de máquina virtual,** pode iniciar todos os VMs em um laboratório clicando no botão Iniciar **tudo** no topo da página.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="Inicie os seus VMs":::
1. Os VM individuais podem ser iniciados clicando no alternância do estado. 

    O toggle irá **ler-se A partir** do início do VM e, em seguida, **funcionando** uma vez iniciado o VM.
1. Também pode selecionar uma série de VMs utilizando os controlos à esquerda da coluna **Nome.** 

    Uma vez selecionados os VMs desejados, clique no botão **Iniciar** na parte superior do ecrã.
1. Uma vez iniciado, pode clicar no botão **Stop all** para parar todos os VMs.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="Pare os seus VMs":::

### <a name="stopping-and-resetting-vms"></a>Parar e repor VMs

* Pode parar os VM individuais clicando no alternância do estado.
* Também pode parar vários VMs utilizando as verificações e clicando no botão "Stop" na parte superior do ecrã.

Se um aluno estiver com dificuldades de ligação ao VM, ou se o VM precisar de ser reiniciado por qualquer outra razão, pode utilizar a função de reset.
1. Para reiniciar um ou mais VMs, selecione-os utilizando as verificações e, em seguida, clique no botão **Reset** na parte superior da página.
1. Na janela pop-up, clique em **Reset**.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="Repôs o seu VM":::

    > [!NOTE]
    > Ligar um VM estudantil não afetará a quota para o aluno. As quotas para os utilizadores especificam o número de horas de laboratório disponíveis para o utilizador fora do horário de aula programado.

### <a name="connect-to-vms"></a>Ligar aos VMs

Os professores podem ligar-se a um aluno VM desde que seja ligado, e o aluno NÃO está ligado ao VM. Ao ligar-se ao VM, poderá aceder aos ficheiros locais sobre o VM e ajudar os alunos a resolver problemas.

1. Para ligar ao VM do aluno, passe o rato na VM na lista e clique no botão **Ligar.** 
1. Em seguida, siga o guia de arranque para estudantes para Chromebooks, Macs ou PCs

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="Ligue-se ao botão VM do aluno":::

## <a name="manage-users-in-a-lab"></a>Gerir utilizadores em laboratório

Os professores são capazes de adicionar utilizadores de estudantes a um laboratório e monitorizar as suas quotas horárias. Para obter detalhes sobre como adicionar utilizadores por endereço de e-mail ou utilizando uma lista de folhas de cálculo e registar utilizadores, consulte [Adicionar e gerir utilizadores de laboratório](how-to-configure-student-usage.md).

Depois de ter convidado utilizadores ou partilhado o link, poderá monitorizar quais os utilizadores que se registaram com sucesso na página **de Utilizadores** na coluna **Status.** 

## <a name="clean-up-resources"></a>Limpar os recursos

Se não vai continuar a usar recursos que criou neste quickstart, apague os recursos.

## <a name="next-steps"></a>Passos seguintes

[Configurar uma conta de laboratório](tutorial-setup-lab-account.md)
