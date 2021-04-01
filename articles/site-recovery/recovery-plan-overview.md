---
title: Sobre planos de recuperação na Recuperação do Local de Azure
description: Saiba mais sobre os planos de recuperação na Recuperação do Local de Azure.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 8d191781cacc37242dd1be31d6cb87ef196e5e7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84343919"
---
# <a name="about-recovery-plans"></a>Acerca dos planos de recuperação

Este artigo fornece uma visão geral dos planos de recuperação na [Recuperação do Sítio Azure.](site-recovery-overview.md)

Um plano de recuperação reúne máquinas em grupos de recuperação com o propósito de falhar. Um plano de recuperação ajuda-o a definir um processo de recuperação sistemática, criando pequenas unidades independentes que pode falhar. Uma unidade normalmente representa uma aplicação no seu ambiente.

- Um plano de recuperação define como as máquinas falham e a sequência em que começam após o fracasso.
- Os planos de recuperação podem ser usados tanto para o failover como para o failback do Azure.
- Até 100 casos protegidos podem ser adicionados a um plano de recuperação.
- Pode personalizar um plano adicionando-lhe encomendas, instruções e tarefas.
- Depois de definido um plano, pode executar um fracasso nele.
- As máquinas podem ser referenciadas em múltiplos planos de recuperação, nos quais os planos subsequentes saltam a implantação/arranque de uma máquina se foram previamente implementadas usando outro plano de recuperação.



### <a name="why-use-a-recovery-plan"></a>Por que usar um plano de recuperação?

Utilize planos de recuperação para:

* Modele uma aplicação em torno das suas dependências.
* Automatizar tarefas de recuperação para reduzir o objetivo do tempo de recuperação (RTO).
* Verifique se está preparado para a migração ou recuperação de desastres, garantindo que as suas aplicações fazem parte de um plano de recuperação.
* Executar os testes de failovers em planos de recuperação, para garantir que a recuperação de desastres ou migração está funcionando como esperado.


## <a name="model-apps"></a>Aplicativos de modelo 
Você pode planear e criar um grupo de recuperação para capturar propriedades específicas de aplicações. Como exemplo, vamos considerar uma aplicação típica de três níveis com um backend de servidor SQL, middleware e um frontend web. Normalmente, personaliza-se o plano de recuperação de modo a que as máquinas em cada nível comecem na ordem correta após o failover.

- O backend SQL deve começar primeiro, o middleware em seguida, e finalmente o frontend web.
- Esta ordem de início garante que a aplicação está a funcionar até ao início da última máquina.
- Esta ordem garante que quando o middleware começa e tenta ligar-se ao nível sql Server, o nível sql server já está em execução. 
- Esta ordem também ajuda a garantir que o servidor frontal começa por último, de modo a que os utilizadores finais não se conectem ao URL da aplicação antes de todos os componentes estarem em funcionamento, e a aplicação está pronta para aceitar pedidos.

Para criar esta ordem, adicione grupos ao grupo de recuperação e adicione máquinas aos grupos.
- Quando a ordem é especificada, é utilizada a sequência. As ações são executadas paralelamente, conforme adequado, para melhorar a recuperação de aplicações RTO.
- As máquinas de um único grupo falham em paralelo.
- As máquinas de diferentes grupos falham por ordem de grupo, de modo que as máquinas do Grupo 2 só iniciam o seu fracasso depois de todas as máquinas do Grupo 1 terem falhado e iniciadas.

    ![Plano de recuperação de exemplo](./media/recovery-plan-overview/rp.png)

Com esta personalização no lugar, eis o que acontece quando se corre um fracasso no plano de recuperação: 

1. Um passo de paragem tenta desligar as máquinas no local. A exceção é se fizer um teste de falha, caso em que o local principal continua a funcionar. 
2. A paragem desencadeia uma falha paralela de todas as máquinas do plano de recuperação.
3. O failover prepara discos de máquina virtuais utilizando dados replicados.
4. Os grupos de arranque funcionam em ordem e iniciam as máquinas em cada grupo. Primeiro, o Grupo 1 corre, depois o Grupo 2 e, finalmente, o Grupo 3. Se há mais de uma máquina em qualquer grupo, então todas as máquinas começam paralelamente.


## <a name="automate-tasks-in-recovery-plans"></a>Automatizar tarefas em planos de recuperação

Recuperar grandes aplicações pode ser uma tarefa complexa. Os passos manuais tornam o processo propenso a erros, e a pessoa que executa o failover pode não estar ciente de todas as complexidades da aplicação. Você pode usar um plano de recuperação para impor a ordem, e automatizar as ações necessárias em cada passo, usando os runbooks da Azure Automation para failover para Azure, ou scripts. Para tarefas que não podem ser automatizadas, pode inserir pausas para ações manuais em planos de recuperação. Existem alguns tipos de tarefas que pode configurar:

* **Tarefas no Azure VM após o failover**: Quando está a falhar em Azure, normalmente precisa de executar ações para que possa ligar-se ao VM após o failover. Por exemplo: 
    * Crie um endereço IP público no Azure VM.
    * Atribua um grupo de segurança de rede ao adaptador de rede do Azure VM.
    * Adicione um equilibrador de carga a um conjunto de disponibilidade.
* **Tarefas dentro do VM após o failover**: Estas tarefas normalmente reconfiguram a aplicação em execução na máquina, de modo a que continue a funcionar corretamente no novo ambiente. Por exemplo:
    * Modifique o fio de ligação da base de dados dentro da máquina.
    * Altere a configuração ou regras do servidor web.


### <a name="run-a-test-failover-on-recovery-plans"></a>Executar um teste de failover em planos de recuperação

Pode usar um plano de recuperação para desencadear um teste falhado. Utilize as seguintes boas práticas:

- Complete sempre um teste de falha numa aplicação, antes de executar um failover completo. As falhas de teste ajudam-no a verificar se a aplicação aparece no site de recuperação.
- Se descobrir que perdeu alguma coisa, desencadeie uma limpeza e, em seguida, re-faça o teste falhar. 
- Faça um teste de s falha várias vezes, até ter a certeza de que a aplicação recupera suavemente.
- Como cada aplicação é única, é necessário construir planos de recuperação personalizados para cada aplicação e executar um teste de failover em cada uma.
- As aplicações e as suas dependências mudam frequentemente. Para garantir que os planos de recuperação estão atualizados, execute um teste de failover para cada app a cada trimestre.

    ![Screenshot de um plano de recuperação de teste de exemplo na Recuperação do Local](./media/recovery-plan-overview/rptest.png)

## <a name="watch-a-recovery-plan-video"></a>Veja um vídeo de plano de recuperação

Veja um vídeo de exemplo rápido mostrando um failover no clique para um plano de recuperação para uma aplicação WordPress de duas camadas.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Passos seguintes

- [Criar](site-recovery-create-recovery-plans.md) um plano de recuperação.
- [Executar](site-recovery-failover.md) os fracassos. 
