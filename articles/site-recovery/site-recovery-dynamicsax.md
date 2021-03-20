---
title: Recuperação de desastres do Dynamics AX com recuperação do local de Azure
description: Saiba como configurar a recuperação de desastres para o Dynamics AX com a recuperação do site Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: dfa3c108d00aeba9c7d42e96e7a40736a087a508
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86133813"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Configurar a recuperação de desastres para uma aplicação AX dynamics multitier   




 A Dynamics AX é uma das soluções ERP mais populares usadas pelas empresas para normalizar processos em locais, gerir recursos e simplificar a conformidade. Como a aplicação é fundamental para uma organização, em caso de desastre, a aplicação deve estar em funcionamento no mínimo tempo.

Hoje, o Dynamics AX não fornece nenhuma capacidade de recuperação de desastres fora da caixa. O Dynamics AX é composto por muitos componentes do servidor, tais como Servidor de Objetos de Aplicação do Windows, Diretório Ativo Azure, Base de Dados Azure SQL, Servidor SharePoint e Serviços de Relatório. Gerir manualmente a recuperação de desastres de cada um destes componentes é não só dispendioso, mas também propenso a erros.

Este artigo explica como pode criar uma solução de recuperação de desastres para a sua aplicação Dynamics AX utilizando [a Recuperação do Sítio Azure](site-recovery-overview.md). Também cobre as falhas de teste planeadas/não planeadas utilizando um plano de recuperação de um clique, configurações suportadas e pré-requisitos.



## <a name="prerequisites"></a>Pré-requisitos

Implementar a recuperação de desastres para a aplicação Dynamics AX utilizando a Recuperação do Local requer os seguintes pré-requisitos:

• Configurar uma implementação AX Dynamics No local.

• Criar um cofre de recuperação do local numa subscrição do Azure.

• Se o Azure for o seu local de recuperação, execute a ferramenta de avaliação de preparação da máquina virtual Azure nos VMs. Devem ser compatíveis com as máquinas virtuais Azure e os serviços de Recuperação do Local.

## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para criar este artigo, utilizamos máquinas virtuais VMware com Dynamics AX 2012 R3 no Windows Server 2012 R2 Enterprise. Como a replicação da Recuperação do Local é agnóstica de aplicação, esperamos que as recomendações aqui fornecidas mantenham para os seguintes cenários.

### <a name="source-and-target"></a>Fonte e alvo

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Yes | Yes
**VMware** | Yes | Yes
**Servidor físico** | Yes | Yes

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Permitir a recuperação de desastres da aplicação Dynamics AX utilizando a Recuperação do Local
### <a name="protect-your-dynamics-ax-application"></a>Proteja a sua aplicação Dynamics AX
Para permitir a replicação e recuperação completas da aplicação, cada componente do Dynamics AX deve ser protegido.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Configurar o Diretório Ativo e a replicação de DNS

O Ative Directory é necessário no local de recuperação de desastres para que a aplicação Dynamics AX funcione. Recomendamos as seguintes duas escolhas baseadas na complexidade do ambiente no local do cliente.

**Opção 1**

O cliente tem um pequeno número de aplicações e um único controlador de domínio para todo o site no local e planeia falhar em todo o site em conjunto. Recomendamos que utilize a replicação da Recuperação do Local para replicar a máquina do controlador de domínio para um local secundário (aplicável tanto para cenários site-to-site como local-a-azure).

**Opção 2**

O cliente tem um grande número de aplicações e está a executar uma floresta de Diretório Ativo e planeia falhar em algumas aplicações de cada vez. Recomendamos que crie um controlador de domínio adicional no local de recuperação de desastres (um local secundário ou em Azure).

 Para obter mais informações, consulte [disponibilizar um controlador de domínio num local de recuperação de desastres.](site-recovery-active-directory.md) Para o resto deste documento, assumimos que um controlador de domínio está disponível no local de recuperação de desastres.

### <a name="2-set-up-sql-server-replication"></a>2. Configurar a replicação do SQL Server
Para obter orientações técnicas sobre a opção recomendada para proteger o nível SQL, consulte [aplicações Replicate com SQL Server e Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Permitir a proteção para os VMs do servidor de servidor de objetos dinâmicos EX e de objetos de aplicação
Execute uma configuração de recuperação de sítio relevante com base no facto de os VMs serem implantados em [Hiper-V](./hyper-v-azure-tutorial.md) ou [VMware](./vmware-azure-tutorial.md).

> [!TIP]
> Recomendamos que configuure a frequência consistente com o acidente para 15 minutos.
>

O instantâneo que se segue mostra o estado de proteção dos VMs componentes dinâmicos num cenário de proteção local-a-azure VMware.

![Itens protegidos](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Configure rede
**Configurar configurar configurações de computação VM e rede**

Para o cliente Dynamics AX e o Servidor de Objetos de Aplicação VMs, configurar as definições de rede na Recuperação do Site para que as redes VM se adexem à rede de recuperação de desastres certa após o failover. Certifique-se de que a rede de recuperação de desastres para estes níveis é encaminhável para o nível SQL.

Pode selecionar o VM nos itens replicados para configurar as definições de rede, como mostra o seguinte instantâneo:

* Para servidores do Servidor de Objetos de Aplicação, selecione o conjunto de disponibilidade correto.

* Se estiver a utilizar um IP estático, especifique o IP que pretende que o VM tenha na caixa de texto **IP target.**

    ![Definições de rede](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. Criar um plano de recuperação

Pode criar um plano de recuperação na Recuperação do Local para automatizar o processo de failover. Adicione um nível de aplicação e um nível web no plano de recuperação. Encomende-os em diferentes grupos para que a extremidade frontal se desligue antes do nível da aplicação.

1. Selecione o cofre de recuperação do site na sua subscrição e selecione o azulejo **planos de recuperação.**

2. Selecione **+ plano de recuperação** e especifique um nome.

3. Selecione a **Fonte** e **o Alvo**. O alvo pode ser Azure ou um local secundário. Se escolher O Azure, tem de especificar o modelo de implantação.

    ![Criar plano de recuperação](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Selecione o Servidor de Objetos de Aplicação e os VMs do cliente para o plano de recuperação e selecione o ✓.

    ![Selecione itens](./media/site-recovery-dynamics-ax/selectvms.png)

    Exemplo do plano de recuperação:

    ![Detalhes do plano de recuperação](./media/site-recovery-dynamics-ax/recoveryplan.png)

Pode personalizar o plano de recuperação da aplicação Dynamics AX adicionando os seguintes passos. O instantâneo anterior mostra o plano de recuperação completo depois de adicionar todos os passos.


* **Sql Server etapas de falha**: Para obter informações sobre etapas de recuperação específicas do servidor SQL, consulte [aplicações de replicação com o SQL Server e a Recuperação do Site Azure](site-recovery-sql.md).

* **Failover Group 1**: Falha sobre os VMs do servidor de objetos de aplicação.
Certifique-se de que o ponto de recuperação selecionado está o mais próximo possível da base de dados PIT, mas não à sua frente.

* **Script**: Adicionar equilibrador de carga (apenas E-A).
Adicione um script (via Azure Automation) depois de o grupo VM do Servidor de Objetos de Aplicação chegar para lhe adicionar um equilibrador de carga. Pode usar um script para fazer esta tarefa. Para obter mais informações, consulte [Como adicionar um equilibrador de carga para a recuperação de desastres de aplicações multifunções](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Failover Group 2**: Falha sobre os VM do cliente Dynamics AX. Falha nos VMs de nível web como parte do plano de recuperação.


### <a name="perform-a-test-failover"></a>Realizar um teste de failover

Para obter mais informações específicas do Ative Directory durante o teste de failover, consulte o guia de acompanhante "Ative Directory Disaster Recovery" (Solução de recuperação de desastres ativo).

Para obter mais informações específicas do servidor SQL durante a falha de teste, consulte [aplicações Replicate com SQL Server e Azure Site Recovery](site-recovery-sql.md).

1. Vá ao portal Azure e selecione o cofre de recuperação do local.

2. Selecione o plano de recuperação criado para Dynamics AX.

3. Selecione **Ativação Pós-falha de Teste**.

4. Selecione a rede virtual para iniciar o processo de falha do teste.

5. Depois de o ambiente secundário terminar, pode realizar as suas validações.

6. Depois de concluídas as validações, selecione **Validações completas** e o ambiente de failover de teste é limpo.

Para obter mais informações sobre a execução de um teste de failover, consulte [test failover to Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Executar um failover

1. Vá ao portal Azure e selecione o cofre de recuperação do local.

2. Selecione o plano de recuperação criado para Dynamics AX.

3. Selecione **Failover** e selecione **Failover**.

4. Selecione a rede alvo e **selecione ✓** para iniciar o processo de failover.

Para obter mais informações sobre a falha, consulte [Failover na Recuperação do Local.](site-recovery-failover.md)

### <a name="perform-a-failback"></a>Executar uma reativação pós-falha

Para obter considerações específicas do SQL Server durante o failback, consulte [aplicações Replicate com SQL Server e Azure Site Recovery](site-recovery-sql.md).

1. Vá ao portal Azure e selecione o cofre de recuperação do local.

2. Selecione o plano de recuperação criado para Dynamics AX.

3. Selecione **Failover** e selecione **Failover**.

4. Selecione **Mudar a direção**.

5. Selecione as opções adequadas: sincronização de dados e criação de VM.

6. **Selecione ✓** para iniciar o processo de failback.


Para obter mais informações sobre a falha, consulte [VMs Failback VMware de Azure para o local](./vmware-azure-failback.md).

## <a name="summary"></a>Resumo
Ao utilizar a Recuperação do Site, pode criar um plano completo de recuperação de desastres automatizado para a sua aplicação Dynamics AX. Em caso de interrupção, pode iniciar a falha em segundos a partir de qualquer lugar e pôr a aplicação a funcionar em minutos.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como proteger as cargas de trabalho da empresa com a Recuperação do Local, veja [que cargas de trabalho posso proteger?](site-recovery-workload.md)
