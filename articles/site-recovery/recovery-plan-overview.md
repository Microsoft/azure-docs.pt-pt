---
title: Sobre planos de recuperação na Recuperação do Site Azure
description: Conheça os planos de recuperação na Recuperação do Site Azure.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: beb92bd62d011ef8aaf304dbb769e7694e6d7e60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79257774"
---
# <a name="about-recovery-plans"></a>Acerca dos planos de recuperação

Este artigo fornece uma visão geral dos planos de recuperação na Recuperação do [Site Azure.](site-recovery-overview.md)

Um plano de recuperação reúne máquinas em grupos de recuperação com o propósito de falhar. Um plano de recuperação ajuda-o a definir um processo de recuperação sistemática, criando pequenas unidades independentes que pode falhar. Uma unidade normalmente representa uma aplicação no seu ambiente.

- Um plano de recuperação define como as máquinas falham, e a sequência em que começam após a falha.
- Os planos de recuperação são usados para falhar com o Azure, mas não podem ser usados para o failback do Azure.
- Até 100 instâncias protegidas podem ser adicionadas a um plano de recuperação.
- Pode personalizar um plano adicionando encomendas, instruções e tarefas.
- Depois de um plano ser definido, podes fazer uma falha nele.
- As máquinas podem ser referenciadas em múltiplos planos de recuperação, nos quais os planos subsequentes ignoram a implantação/arranque de uma máquina se esta foi previamente implantada utilizando outro plano de recuperação.



### <a name="why-use-a-recovery-plan"></a>Por que usar um plano de recuperação?

Utilize planos de recuperação para:

* Modele uma aplicação em torno das suas dependências.
* Automatizar tarefas de recuperação para reduzir o objetivo de tempo de recuperação (RTO).
* Verifique se está preparado para a migração ou recuperação de desastres, garantindo que as suas aplicações fazem parte de um plano de recuperação.
* Executar falhas nos planos de recuperação, para garantir que a recuperação de desastres ou a migração está a funcionar como esperado.


## <a name="model-apps"></a>Aplicativos de modelo 
Você pode planear e criar um grupo de recuperação para capturar propriedades específicas de aplicações. Como exemplo, vamos considerar uma aplicação típica de três camadas com um backend do servidor SQL, middleware e um frontend web. Normalmente, personaliza o plano de recuperação para que as máquinas de cada nível comecem na ordem correta após a falha.

- O backend SQL deve começar primeiro, o middleware seguinte, e finalmente o frontend web.
- Esta ordem de arranque garante que a aplicação está a funcionar quando a última máquina começar.
- Esta ordem garante que quando o middleware começa e tenta ligar-se ao nível SQL Server, o nível SQL Server já está em execução. 
- Esta encomenda também ajuda a garantir que o servidor frontal começa por último, para que os utilizadores finais não se conectem ao URL da aplicação antes de todos os componentes estarem em funcionamento, e a aplicação está pronta para aceitar pedidos.

Para criar esta ordem, adicione grupos ao grupo de recuperação e adicione máquinas aos grupos.
- Quando a ordem for especificada, a sequenciação é utilizada. As ações decorrem paralelamente, conforme adequado, para melhorar a recuperação da aplicação RTO.
- As máquinas de um único grupo falham em paralelo.
- As máquinas em diferentes grupos falham na ordem do grupo, de modo que as máquinas do Grupo 2 só iniciam a sua falha depois de todas as máquinas do Grupo 1 terem falhado e iniciado.

    ![Plano de recuperação de exemplo](./media/recovery-plan-overview/rp.png)

Com esta personalização no lugar, eis o que acontece quando se faz uma falha no plano de recuperação: 

1. Um passo de paragem tenta desligar as máquinas no local. A exceção é que se fizer um teste falhado, nesse caso o local principal continua a funcionar. 
2. A paragem desencadeia uma falha paralela de todas as máquinas do plano de recuperação.
3. A falha prepara discos de máquinas virtuais utilizando dados replicados.
4. Os grupos de startups funcionam em ordem e iniciam as máquinas em cada grupo. Primeiro, o Grupo 1 corre, depois o Grupo 2, e, finalmente, o Grupo 3. Se há mais de uma máquina em qualquer grupo, então todas as máquinas começam em paralelo.


## <a name="automate-tasks-in-recovery-plans"></a>Automatizar tarefas em planos de recuperação

Recuperar grandes aplicações pode ser uma tarefa complexa. Os passos manuais tornam o processo propenso a erros, e a pessoa que executa o failover pode não estar ciente de todos os meandros da aplicação. Você pode usar um plano de recuperação para impor a ordem, e automatizar as ações necessárias em cada passo, usando livros de execução Azure Automation para failover para Azure, ou scripts. Para tarefas que não podem ser automatizadas, pode inserir pausas para ações manuais em planos de recuperação. Existem alguns tipos de tarefas que pode configurar:

* **Tarefas no VM Azure após falha**: Quando estás a falhar com o Azure, normalmente precisas de realizar ações para que possas ligar-te ao VM após a falha. Por exemplo: 
    * Crie um endereço IP público no Azure VM.
    * Atribuir um grupo de segurança de rede ao adaptador de rede do Azure VM.
    * Adicione um equilibrador de carga a um conjunto de disponibilidade.
* **Tarefas dentro**do VM após a falha : Estas tarefas normalmente reconfiguram a aplicação em funcionamento na máquina, de modo que continua a funcionar corretamente no novo ambiente. Por exemplo:
    * Modificar a cadeia de ligação da base de dados dentro da máquina.
    * Altere a configuração ou as regras do servidor web.


### <a name="run-a-test-failover-on-recovery-plans"></a>Executar um teste failover em planos de recuperação

Pode usar um plano de recuperação para desencadear uma falha no teste. Utilize as seguintes boas práticas:

- Complete sempre uma falha de teste numa aplicação, antes de executar uma falha completa. Falhas nos testes ajudam-no a verificar se a aplicação aparece no site de recuperação.
- Se descobrires que falhaste alguma coisa, dispara uma limpeza e depois refaz o teste. 
- Faça uma falha de teste várias vezes, até ter a certeza de que a aplicação recupera sem problemas.
- Como cada aplicação é única, você precisa construir planos de recuperação que são personalizados para cada aplicação, e executar um teste failover em cada uma.
- As aplicações e as suas dependências mudam frequentemente. Para garantir que os planos de recuperação estão atualizados, ecorra um teste de falha para cada aplicação a cada trimestre.

    ![Screenshot de um plano de recuperação de teste de exemplo na recuperação do site](./media/recovery-plan-overview/rptest.png)

## <a name="watch-a-recovery-plan-video"></a>Veja um vídeo do plano de recuperação

Veja um vídeo de exemplo rápido mostrando uma falha no clique para um plano de recuperação para uma aplicação WordPress de dois níveis.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Passos seguintes

- [Criar](site-recovery-create-recovery-plans.md) um plano de recuperação.
- [Run](site-recovery-failover.md) failovers. 
