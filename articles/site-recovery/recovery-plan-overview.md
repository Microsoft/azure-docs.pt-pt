---
title: Usando planos de recuperação em recuperação de desastre com o Azure Site Recovery
description: Saiba mais sobre como usar planos de recuperação para recuperação de desastres com o serviço de Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: raynew
ms.openlocfilehash: 9e45787e7ae39b62605f5d8a54afd4ad95c9cca7
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331901"
---
# <a name="about-recovery-plans"></a>Acerca dos planos de recuperação

Este artigo descreve os planos de recuperação no [Azure site Recovery](site-recovery-overview.md).

Um plano de recuperação reúne computadores em grupos de recuperação. Você pode personalizar um plano adicionando uma ordem, instruções e tarefas a ele. Depois que um plano é definido, você pode executar um failover nele.  Os computadores podem ser referenciados em vários planos de recuperação, nos quais os planos subsequentes irão ignorar a implantação/inicialização do computador se ele foi implantado anteriormente por outro plano de recuperação.


## <a name="why-use-a-recovery-plan"></a>Por que usar um plano de recuperação?

Um plano de recuperação ajuda você a definir um processo de recuperação sistemático, criando pequenas unidades independentes nas quais você pode fazer failover. Uma unidade normalmente representa um aplicativo em seu ambiente. Um plano de recuperação define como os computadores fazem failover e a sequência na qual eles iniciam após o failover. Use planos de recuperação para:

* Modele um aplicativo em relação a suas dependências.
* Automatize as tarefas de recuperação para reduzir o RTO.
* Verifique se você está preparado para migração ou recuperação de desastres, garantindo que seus aplicativos façam parte de um plano de recuperação.
* Execute o failover de teste nos planos de recuperação para garantir que a recuperação ou a migração de desastres funcione conforme o esperado.


## <a name="model-apps"></a>Aplicativos de modelo

> [!NOTE]
> Até 100 instâncias protegidas podem ser adicionadas a um plano de recuperação.

Você pode planejar e criar um grupo de recuperação para capturar propriedades específicas do aplicativo. Por exemplo, vamos considerar um aplicativo típico de três camadas com um back-end do SQL Server, middleware e um front-end da Web. Normalmente, você personaliza o plano de recuperação para que os computadores em cada camada sejam iniciados na ordem correta após o failover.

- O back-end do SQL deve iniciar primeiro, o middleware em seguida e, por fim, o front-end da Web.
- Essa ordem de início garante que o aplicativo está funcionando na hora em que o último computador é iniciado.
- Essa ordem garante que quando o middleware iniciar e tentar se conectar à camada de SQL Server, a camada de SQL Server já estará em execução. 
- Essa ordem também ajuda a garantir que o servidor front-end seja iniciado por último, de modo que os usuários finais não se conectem à URL do aplicativo antes que todos os componentes estejam em funcionamento e o aplicativo esteja pronto para aceitar solicitações.

Para criar essa ordem, você adiciona grupos ao grupo de recuperação e adiciona computadores aos grupos.
- Quando Order é especificado, o sequenciamento é usado. As ações são executadas em paralelo, quando apropriado, para melhorar o RTO da recuperação de aplicativos.
- Os computadores em um único grupo fazem failover em paralelo.
- Os computadores em grupos diferentes fazem failover em ordem de grupo, para que os computadores do grupo 2 iniciem seu failover somente depois que todos os computadores no grupo 1 tiverem feito failover e iniciados.

    ![Exemplo de plano de recuperação](./media/recovery-plan-overview/rp.png)

Com essa personalização em vigor, veja o que acontece quando você executa um failover no plano de recuperação: 

1. Uma etapa de desligamento tenta desligar os computadores locais. A exceção é se você executar um failover de teste, caso em que o site primário continua a ser executado. 
2. O desligamento dispara um failover paralelo de todos os computadores no plano de recuperação.
3. O failover prepara os discos de máquina virtual usando os dados replicados.
4. Os grupos de inicialização são executados em ordem e iniciam os computadores em cada grupo. Primeiro, o grupo 1 é executado, depois o grupo 2 e, por fim, o grupo 3. Se houver mais de um computador em qualquer grupo, todos os computadores iniciarão em paralelo.


## <a name="automate-tasks"></a>Automatizar tarefas

A recuperação de aplicativos grandes pode ser uma tarefa complexa. As etapas manuais tornam o processo propenso a erros e a pessoa que executa o failover pode não estar ciente de todos os pormenores do aplicativo. Você pode usar um plano de recuperação para impor a ordem e automatizar as ações necessárias em cada etapa, usando runbooks de automação do Azure para failover para o Azure ou scripts. Para tarefas que não podem ser automatizadas, você pode inserir pausas para ações manuais em planos de recuperação. Há alguns tipos de tarefas que você pode configurar:

* **Tarefas na VM do Azure após o failover**: quando você estiver fazendo failover para o Azure, normalmente precisará executar ações para que você possa se conectar à VM após o failover. Por exemplo: 
    * Crie um endereço IP público na VM do Azure.
    * Atribua um grupo de segurança de rede ao adaptador de rede da VM do Azure.
    * Adicione um balanceador de carga a um conjunto de disponibilidade.
* **Tarefas dentro da VM após o failover**: essas tarefas normalmente reconfiguram o aplicativo em execução no computador, para que ele continue funcionando corretamente no novo ambiente. Por exemplo:
    * Modifique a cadeia de conexão do banco de dados dentro da máquina.
    * Altere a configuração ou as regras do servidor Web.


## <a name="test-failover"></a>Ativação pós-falha de teste

Você pode usar um plano de recuperação para disparar um failover de teste. Use as seguintes práticas recomendadas:

- Sempre conclua um failover de teste em um aplicativo antes de executar um failover completo. Os failovers de teste ajudam a verificar se o aplicativo aparece no local de recuperação.
- Se você achar que perdeu alguma coisa, dispare uma limpeza e execute novamente o failover de teste. 
- Execute um failover de teste várias vezes até ter certeza de que o aplicativo será recuperado sem problemas.
- Como cada aplicativo é exclusivo, você precisa criar planos de recuperação personalizados para cada aplicativo e executar um failover de teste em cada um deles.
- Os aplicativos e suas dependências mudam com frequência. Para garantir que os planos de recuperação estejam atualizados, execute um failover de teste para cada aplicativo a cada trimestre.

    ![Captura de tela de um exemplo de plano de recuperação de teste no Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Veja o vídeo

Assista a um vídeo de exemplo rápido mostrando um failover de clique para um aplicativo WordPress de duas camadas.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Passos seguintes

- [Crie](site-recovery-create-recovery-plans.md) um plano de recuperação.
- Saiba mais sobre [failovers em execução](site-recovery-failover.md).  
