---
title: Utilizar planos de recuperação na recuperação após desastre com o Azure Site Recovery | Documentos da Microsoft
description: Saiba como utilizar planos de recuperação para recuperação após desastre com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 1824c0a4c129fa5612c990447a13aef6b230e71f
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310171"
---
# <a name="about-recovery-plans"></a>Acerca dos planos de recuperação

Este artigo descreve os planos de recuperação no [do Azure Site Recovery](site-recovery-overview.md).

Um plano de recuperação reúne máquinas em grupos de recuperação. Pode personalizar um plano com a adição de ordem, instruções e tarefas. Depois de um plano é definido, pode executar uma ativação pós-falha no mesmo.



## <a name="why-use-a-recovery-plan"></a>Por que usar um plano de recuperação?

Um plano de recuperação ajuda-o a definir um processo de recuperação sistemática, através da criação de pequenas unidades independentes que pode efetuar a ativação pós-falha. Uma unidade representa, normalmente, uma aplicação no seu ambiente. Um plano de recuperação define como os computadores efetuar a ativação pós-falha e a sequência em que iniciar após a ativação pós-falha. Utilize planos de recuperação para:

* Modele uma aplicação em torno de suas dependências.
* Automatize tarefas de recuperação para reduzir o RTO.
* Certifique-se de que está preparada para a recuperação de migração ou desastre, garantindo que as suas aplicações são parte de um plano de recuperação.
* Execute a ativação pós-falha de teste nos planos de recuperação, para garantir a recuperação após desastre ou a migração está a funcionar conforme esperado.


## <a name="model-apps"></a>Aplicações de modelo

Planear e criar um grupo de recuperação para capturar as propriedades específicas da aplicação. Por exemplo, vamos considerar um aplicativo típico de três camadas com um servidor SQL back-end, middleware e um front-end da web. Normalmente, personalizar o plano de recuperação para que as máquinas em cada escalão de começar, na ordem correta, após a ativação pós-falha.

    - O back-end do SQL deve começar em primeiro lugar, o middleware da próxima e, finalmente, o front-end da web.
    - Esta ordem de início garante que a aplicação está a funcionar quando a última máquina é iniciado.
    - Esta ordem garante que quando o middleware é iniciado e tenta estabelecer ligação com o escalão do SQL Server, o escalão do SQL Server já está em execução. 
    - Esta ordem também ajuda a garantir que o servidor front-end é iniciado último, para que os utilizadores finais não ligar para o URL da aplicação antes de todos os componentes estão operacionais e em execução e a aplicação está pronto para aceitar pedidos.

Para criar esta ordem, adicionar grupos ao grupo de recuperação e adicionar máquinas em grupos.
- Em que ordem for especificada, é utilizada a sequenciação. Ações executam em paralelo quando apropriado, para melhorar a recuperação de aplicativos RTO.
- As máquinas num único grupo de ativação pós-falha em paralelo.
- As máquinas em grupos diferentes a ativação pós-falha por ordem de grupo, para que máquinas do grupo 2 começar sua ativação pós-falha depois de todas as máquinas num grupo 1 têm de efetuar a ativação pós-falha e iniciado.

    ![Plano de recuperação de exemplo](./media/recovery-plan-overview/rp.png)

Com essa personalização no local, este é o que acontece quando executa uma ativação pós-falha no plano de recuperação: 

1. Um passo de encerramento tenta desative as máquinas no local. A exceção é se executar uma ativação pós-falha de teste, caso em que o site primário continua a ser executado. 
2. O encerramento aciona uma ativação pós-falha paralela de todas as máquinas no plano de recuperação.
3. A ativação pós-falha prepara discos de máquinas virtuais com os dados replicados.
4. Os grupos de arranque são executados pela ordem e iniciar as máquinas em cada grupo. Em primeiro lugar, grupo 1 é executado, em seguida, grupo 2 e, por fim, grupo 3. Se houver mais de uma máquina em qualquer grupo, em seguida, começar a todas as máquinas em paralelo.


## <a name="automate-tasks"></a>Automatizar tarefas

Recuperação de aplicativos grandes pode ser uma tarefa complexa. Passos manuais tornam o processo propenso a erro e a pessoa a executar a ativação pós-falha possa não ter conhecimento de todos os pormenores de aplicação. Pode utilizar um plano de recuperação para impor a ordem e automatizar as ações necessárias em cada etapa, através de runbooks de automatização do Azure para a ativação pós-falha para o Azure ou scripts. Para tarefas que não podem ser automatizadas, pode inserir interrupções para ações manuais em planos de recuperação. Existem dois tipos de tarefas que pode configurar:

* **Tarefas na VM do Azure após a ativação pós-falha**: Quando estiver a fazer failover para o Azure, normalmente, tem de executar ações para que possam ligar à VM após a ativação pós-falha. Por exemplo: 
    * Crie um endereço IP público na VM do Azure.
    * Atribua um grupo de segurança de rede para o adaptador de rede da VM do Azure.
    * Adicione um balanceador de carga a um conjunto de disponibilidade.
* **Tarefas de VM após a ativação pós-falha**: Estas tarefas, normalmente, reconfigure a aplicação em execução na máquina, para que continuam a funcionar corretamente no novo ambiente. Por exemplo:
    * Modificar a cadeia de ligação de base de dados dentro da máquina.
    * Altere a configuração de servidor web ou regras.


## <a name="test-failover"></a>Ativação pós-falha de teste

Pode utilizar um plano de recuperação para acionar uma ativação pós-falha de teste. Utilize as seguintes práticas recomendadas:

- Sempre execute uma ativação pós-falha de teste num aplicativo, antes de executar uma ativação pós-falha completa. Ativações pós-falha de teste ajudá-lo para verificar se a aplicação é exibido no site de recuperação.
- Se que tivesse deixado passar alguma coisa, acionar um limpa a cópia de segurança e, em seguida, volte a executar a ativação pós-falha de teste. 
- Execute uma ativação pós-falha de teste várias vezes, até que tem a certeza de que a aplicação recupera sem problemas.
- Uma vez que cada aplicação é exclusiva, terá de criar planos de recuperação que são personalizados para cada aplicativo e executar uma ativação pós-falha de teste em cada um.
- Aplicações e as respetivas dependências mudam frequentemente. Para garantir que os planos de recuperação estão atualizados, execute uma ativação pós-falha de teste para cada aplicação a cada trimestre.

    ![Plano de recuperação de teste de captura de ecrã de um exemplo no Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Ver o vídeo

Ver um vídeo de exemplo rápido que mostra um só clique ativação pós-falha para uma aplicação do WordPress de duas camadas.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Passos Seguintes

- [Criar](site-recovery-create-recovery-plans.md) um plano de recuperação.
- Saiba mais sobre [executar as ativações pós-falha](site-recovery-failover.md).  
