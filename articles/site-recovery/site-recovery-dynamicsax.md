---
title: Recuperação de desastres da Dynamics AX com recuperação do site Azure
description: Saiba como configurar a recuperação de desastres para a Dynamics AX com a Recuperação do Site Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 0b32f00374aa8ce6c41415e28f319e3e7d5abddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941594"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Configurar a recuperação de desastres para uma aplicação Multitier Dynamics AX   




 A Dynamics AX é uma das soluções ERP mais populares usadas pelas empresas para normalizar processos em locais, gerir recursos e simplificar a conformidade. Como a aplicação é fundamental para uma organização, em caso de desastre, a aplicação deve estar em funcionamento no tempo mínimo.

Hoje, a Dynamics AX não fornece nenhuma capacidade de recuperação de desastres fora da caixa. O Dynamics AX é composto por muitos componentes do servidor, tais como O Servidor de Objetos de Aplicação do Windows, Diretório Ativo Azure, Base de Dados Azure SQL, SharePoint Server e Serviços de Reportagem. Gerir a recuperação de desastres de cada um destes componentes manualmente não só é caro como também é propenso a erros.

Este artigo explica como pode criar uma solução de recuperação de desastres para a sua aplicação Dynamics AX utilizando a Recuperação do [Site Azure](site-recovery-overview.md). Também cobre falhas de teste planeadas/não planeadas utilizando um plano de recuperação de um clique, configurações suportadas e pré-requisitos.



## <a name="prerequisites"></a>Pré-requisitos

Implementar a recuperação de desastres para a aplicação Dynamics AX utilizando a Recuperação do Site requer os seguintes pré-requisitos:

• Instale uma implementação no local Dynamics AX.

• Crie um cofre de recuperação de sítios numa subscrição Azure.

• Se o Azure for o seu local de recuperação, execute a ferramenta de avaliação de prontidão de máquina seleção azul nos VMs. Devem ser compatíveis com os serviços de Recuperação de Máquinas Virtuais Azure e De recuperação do local.

## <a name="site-recovery-support"></a>Suporte do Site Recovery

Com o objetivo de criar este artigo, usamos máquinas virtuais VMware com Dynamics AX 2012 R3 no Windows Server 2012 R2 Enterprise. Como a replicação da Recuperação do Site é agnóstica da aplicação, esperamos que as recomendações aqui fornecidas se mantenham para os seguintes cenários.

### <a name="source-and-target"></a>Fonte e alvo

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Permitir a recuperação de desastres da aplicação Dynamics AX utilizando a Recuperação do Site
### <a name="protect-your-dynamics-ax-application"></a>Proteja a sua aplicação Dynamics AX
Para permitir a replicação e recuperação completas da aplicação, cada componente da Dynamics AX deve ser protegido.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Configurar a replicação de Diretório Ativo e DNS

O Diretório Ativo é necessário no local de recuperação de desastres para que a aplicação Dynamics AX funcione. Recomendamos as duas escolhas seguintes com base na complexidade do ambiente no local do cliente.

**Opção 1**

O cliente tem um pequeno número de aplicações e um único controlador de domínio para todo o site no local e planeia falhar em todo o site em conjunto. Recomendamos que utilize a replicação de Recuperação do Site para replicar a máquina do controlador de domínio para um local secundário (aplicável tanto para cenários site-to-site como local-para-azure).

**Opção 2**

O cliente tem um grande número de aplicações e está a gerir uma floresta de Diretório Ativo e planeia falhar em algumas aplicações de cada vez. Recomendamos que instale um controlador de domínio adicional no local de recuperação de desastres (um local secundário ou em Azure).

 Para mais informações, consulte [O controlador de domínio disponível num local](site-recovery-active-directory.md)de recuperação de desastres . Para o resto deste documento, assumimos que um controlador de domínio está disponível no local de recuperação de desastres.

### <a name="2-set-up-sql-server-replication"></a>2. Configurar a replicação do Servidor SQL
Para obter orientações técnicas sobre a opção recomendada para proteger o nível SQL, consulte [As aplicações Replicate com o Servidor SQL e a Recuperação](site-recovery-sql.md)do Site Azure .

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Ativar a proteção para os VMs do cliente Dynamics AX e do Servidor de Objetos de Aplicação
Execute a configuração de recuperação de site relevante com base no facto de os VMs estarem implantados em [Hiper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Recomendamos que configure a frequência consistente com o acidente até 15 minutos.
>

O instantâneo seguinte mostra o estado de proteção dos VMs componentes da Dynamics num cenário de proteção VMware site-to-Azure.

![Artigos protegidos](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Configurar a rede
**Configure as definições de computação VM e de rede**

Para os VMs do cliente Dynamics AX e do Servidor de Objetos de Aplicação, configure as definições de rede na Recuperação do Site para que as redes VM se adequem à rede de recuperação de desastres certa após a falha. Certifique-se de que a rede de recuperação de desastres para estes níveis é enredada para o nível SQL.

Pode selecionar o VM nos itens replicados para configurar as definições de rede, como mostra o seguinte instantâneo:

* Para servidores do Servidor de Objetos de Aplicação, selecione o conjunto de disponibilidade correto.

* Se estiver a utilizar um IP estático, especifique o IP que pretende que o VM utilize na caixa de texto **IP target.**

    ![Definições de rede](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. Criar um plano de recuperação

Pode criar um plano de recuperação na Recuperação do Site para automatizar o processo de failover. Adicione um nível de aplicativo e um nível web no plano de recuperação. Encomende-os em diferentes grupos para que a parte frontal se desligue antes do nível de aplicações.

1. Selecione o cofre de recuperação do site na sua subscrição e selecione o azulejo planos de **recuperação.**

2. Selecione + Plano de **Recuperação,** e especifique um nome.

3. Selecione a **Fonte** e **o Alvo**. O alvo pode ser Azure ou um local secundário. Se escolher o Azure, deve especificar o modelo de implantação.

    ![Criar plano de recuperação](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Selecione o Servidor de Objetos de Aplicação e os VMs do cliente para o plano de recuperação e selecione o ✓.

    ![Selecione itens](./media/site-recovery-dynamics-ax/selectvms.png)

    Exemplo do plano de recuperação:

    ![Detalhes do plano de recuperação](./media/site-recovery-dynamics-ax/recoveryplan.png)

Pode personalizar o plano de recuperação para a aplicação Dynamics AX adicionando os seguintes passos. O instantâneo anterior mostra o plano de recuperação completo depois de adicionar todos os passos.


* Etapas de falha do **Servidor SQL**: Para obter informações sobre etapas de recuperação específicas do servidor SQL, consulte [aplicações de replicação com o Servidor SQL e a Recuperação](site-recovery-sql.md)do Site Azure .

* **Failover Group 1**: Falha nos VMs do servidor de objetos de aplicação.
Certifique-se de que o ponto de recuperação selecionado está o mais próximo possível da base de dados PIT, mas não à sua frente.

* **Script**: Adicione o equilibrador de carga (apenas E-A).
Adicione um script (via Automatização Azure) depois de o grupo VM do Servidor de Objetos de Aplicação aparecer para adicionar-lhe um equilíbrio de carga. Pode usar um guião para fazer esta tarefa. Para mais informações, consulte [Como adicionar um equilibrador de carga para a recuperação de desastres de aplicação multimais.](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/)

* **Failover Group 2:** Fail over the Dynamics AX client VMs. Falhar sobre os VMs de nível web como parte do plano de recuperação.


### <a name="perform-a-test-failover"></a>Realizar uma falha de teste

Para obter mais informações específicas ao Ative Directory durante o failover do teste, consulte o guia de acompanhantes "Ative Directory disaster recovery guide".

Para obter mais informações específicas para o servidor SQL durante o failover do teste, consulte [as aplicações Replicate com o Servidor SQL e a Recuperação](site-recovery-sql.md)do Site Azure .

1. Vá ao portal Azure e selecione o seu cofre de recuperação do site.

2. Selecione o plano de recuperação criado para a Dynamics AX.

3. Selecione **Ativação Pós-falha de Teste**.

4. Selecione a rede virtual para iniciar o processo de failover do teste.

5. Depois do ambiente secundário acabar, pode realizar as suas validações.

6. Depois de as validações estarem completas, selecione **Validações completas** e o ambiente de failover do teste é limpo.

Para obter mais informações sobre a realização de um teste failover, consulte [Test failover to Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Realizar uma falha

1. Vá ao portal Azure e selecione o seu cofre de recuperação do site.

2. Selecione o plano de recuperação criado para a Dynamics AX.

3. Selecione **Failover**e selecione **Failover**.

4. Selecione a rede de destino e selecione **✓** para iniciar o processo de failover.

Para obter mais informações sobre como fazer uma falha, consulte [Failover in Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Executar uma reativação pós-falha

Para considerações específicas do Servidor SQL durante o failback, consulte [as aplicações Replicate com o Servidor SQL e a Recuperação](site-recovery-sql.md)do Site Azure .

1. Vá ao portal Azure e selecione o seu cofre de recuperação do site.

2. Selecione o plano de recuperação criado para a Dynamics AX.

3. Selecione **Failover**e selecione **Failover**.

4. Selecione **Mudar de Direção**.

5. Selecione as opções adequadas: sincronização de dados e criação de VM.

6. **Selecione ✓** para iniciar o processo de reinicialização.


Para obter mais informações sobre como fazer um failback, consulte [Failback VMware VMs de Azure para no local](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Resumo
Ao utilizar a Recuperação do Site, pode criar um plano completo de recuperação de desastres automatizado para a sua aplicação Dynamics AX. Em caso de perturbação, pode iniciar a falha em segundos a partir de qualquer lugar e colocar a aplicação em funcionamento em minutos.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como proteger as cargas de trabalho das empresas com a Recuperação do Site, veja que cargas de [trabalho posso proteger?](site-recovery-workload.md)
