---
title: Recuperação de desastre do Dynamics AX com Azure Site Recovery
description: Saiba como configurar a recuperação de desastre para o Dynamics AX com o Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 0b32f00374aa8ce6c41415e28f319e3e7d5abddb
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941594"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Configurar a recuperação de desastre para um aplicativo do Dynamics AX de várias camadas   




 O Dynamics AX é uma das soluções de ERP mais populares usadas pelas empresas para padronizar processos entre locais, gerenciar recursos e simplificar a conformidade. Como o aplicativo é essencial para uma organização, no caso de um desastre, o aplicativo deve estar em execução no mínimo tempo.

Hoje, o Dynamics AX não fornece nenhum recurso de recuperação de desastres pronto para uso. O Dynamics AX consiste em muitos componentes de servidor, como o Windows Application Object Server, Azure Active Directory, banco de dados SQL do Azure, SharePoint Server e Reporting Services. Gerenciar a recuperação de desastres de cada um desses componentes manualmente não é apenas caro, mas também propenso a erros.

Este artigo explica como você pode criar uma solução de recuperação de desastre para seu aplicativo Dynamics AX usando [Azure site Recovery](site-recovery-overview.md). Ele também aborda failovers de teste planejados/não planejados usando um plano de recuperação com um clique, configurações com suporte e pré-requisitos.



## <a name="prerequisites"></a>Pré-requisitos

A implementação da recuperação de desastre para o aplicativo Dynamics AX usando Site Recovery requer os seguintes pré-requisitos:

• Configurar uma implantação do Dynamics AX local.

• Criar um cofre de Site Recovery em uma assinatura do Azure.

• Se o Azure for seu site de recuperação, execute a ferramenta de avaliação de prontidão de máquina virtual do Azure nas VMs. Eles devem ser compatíveis com as máquinas virtuais do Azure e os serviços de Site Recovery.

## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para a finalidade de criar este artigo, usamos máquinas virtuais VMware com o Dynamics AX 2012 R3 no Windows Server 2012 R2 Enterprise. Como Site Recovery replicação é independente de aplicativo, esperamos que as recomendações fornecidas aqui sejam mantidas para os cenários a seguir.

### <a name="source-and-target"></a>Origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Habilitar a recuperação de desastre do aplicativo Dynamics AX usando Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Proteger seu aplicativo Dynamics AX
Para habilitar a replicação e recuperação completas do aplicativo, cada componente do Dynamics AX deve ser protegido.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. configurar Active Directory e replicação de DNS

Active Directory é necessário no site de recuperação de desastre para que o aplicativo do Dynamics AX funcione. Recomendamos as duas opções a seguir com base na complexidade do ambiente local do cliente.

**Opção 1**

O cliente tem um pequeno número de aplicativos e um único controlador de domínio para todo o site local e planeja fazer failover de todo o site juntos. Recomendamos que você use Site Recovery replicação para replicar o computador do controlador de domínio para um site secundário (aplicável a cenários site a site e site a Azure).

**Opção 2**

O cliente tem um grande número de aplicativos e está executando uma floresta Active Directory e planeja fazer failover de alguns aplicativos por vez. Recomendamos que você configure um controlador de domínio adicional no site de recuperação de desastre (um site secundário ou no Azure).

 Para obter mais informações, consulte [tornar um controlador de domínio disponível em um site de recuperação de desastre](site-recovery-active-directory.md). Para o restante deste documento, presumimos que um controlador de domínio esteja disponível no site de recuperação de desastre.

### <a name="2-set-up-sql-server-replication"></a>2. configurar a replicação do SQL Server
Para obter orientações técnicas sobre a opção recomendada para proteger a camada SQL, consulte [replicar aplicativos com SQL Server e Azure site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. habilitar a proteção para as VMs do cliente do Dynamics AX e do servidor de objetos de aplicativo
Execute a configuração de Site Recovery relevante com base em se as VMs são implantadas no [Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou no [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Recomendamos que você configure a frequência consistente com falhas para 15 minutos.
>

O instantâneo a seguir mostra o status de proteção das VMs do componente Dynamics em um cenário de proteção de site a Azure de VMware.

![Itens protegidos](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. configurar a rede
**Definir as configurações de rede e de computação da VM**

Para as VMs do cliente do Dynamics AX e do servidor de objetos de aplicativo, defina as configurações de rede no Site Recovery para que as redes VM sejam anexadas à rede de recuperação de desastres correta após o failover. Certifique-se de que a rede de recuperação de desastre para essas camadas seja roteável para a camada SQL.

Você pode selecionar a VM nos itens replicados para definir as configurações de rede, conforme mostrado no seguinte instantâneo:

* Para servidores de servidor de objetos de aplicativo, selecione o conjunto de disponibilidade correto.

* Se você estiver usando um IP estático, especifique o IP que você deseja que a VM execute na caixa de texto **IP de destino** .

    ![Definições de rede](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. criar um plano de recuperação

Você pode criar um plano de recuperação no Site Recovery para automatizar o processo de failover. Adicione uma camada de aplicativo e uma camada da Web no plano de recuperação. Ordene-os em grupos diferentes para que o front-end seja desligado antes da camada de aplicativo.

1. Selecione o cofre de Site Recovery em sua assinatura e selecione o bloco **planos de recuperação** .

2. Selecione **+ plano de recuperação**e especifique um nome.

3. Selecione a **origem** e o **destino**. O destino pode ser o Azure ou um site secundário. Se você escolher Azure, deverá especificar o modelo de implantação.

    ![Criar plano de recuperação](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Selecione o servidor de objetos de aplicativo e as VMs de cliente para o plano de recuperação e selecione o ✓.

    ![Selecionar itens](./media/site-recovery-dynamics-ax/selectvms.png)

    Exemplo de plano de recuperação:

    ![Detalhes do plano de recuperação](./media/site-recovery-dynamics-ax/recoveryplan.png)

Você pode personalizar o plano de recuperação para o aplicativo Dynamics AX adicionando as etapas a seguir. O instantâneo anterior mostra o plano de recuperação completo depois de adicionar todas as etapas.


* **SQL Server etapas de failover**: para obter informações sobre as etapas de recuperação específicas do SQL Server, consulte [aplicativos de replicação com SQL Server e Azure site Recovery](site-recovery-sql.md).

* **Grupo de failover 1**: fazer failover das VMs do servidor de objetos de aplicativo.
Verifique se o ponto de recuperação selecionado está o mais próximo possível do PIT do banco de dados, mas não antes dele.

* **Script**: Adicionar balanceador de carga (somente E-a).
Adicione um script (por meio da automação do Azure) depois que o grupo de VMs do servidor de objetos de aplicativo vier a adicionar um balanceador de carga a ele. Você pode usar um script para executar essa tarefa. Para obter mais informações, consulte [como adicionar um balanceador de carga para recuperação de desastre de aplicativos multicamadas](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Grupo de failover 2**: fazer failover das VMs do cliente do Dynamics AX. Faça failover das VMs da camada da Web como parte do plano de recuperação.


### <a name="perform-a-test-failover"></a>Executar um failover de teste

Para obter mais informações específicas para Active Directory durante o failover de teste, consulte o guia complementar "solução de recuperação de desastre Active Directory".

Para obter mais informações específicas do SQL Server durante o failover de teste, consulte [replicar aplicativos com SQL Server e Azure site Recovery](site-recovery-sql.md).

1. Vá para a portal do Azure e selecione seu cofre de Site Recovery.

2. Selecione o plano de recuperação criado para o Dynamics AX.

3. Selecione **Ativação Pós-falha de Teste**.

4. Selecione a rede virtual para iniciar o processo de failover de teste.

5. Depois que o ambiente secundário estiver ativo, você poderá executar suas validações.

6. Depois que as validações forem concluídas, selecione **validações concluídas** e o ambiente de failover de teste será limpo.

Para obter mais informações sobre como executar um failover de teste, consulte [failover de teste para o Azure em site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Executar um failover

1. Vá para a portal do Azure e selecione seu cofre de Site Recovery.

2. Selecione o plano de recuperação criado para o Dynamics AX.

3. Selecione **failover**e selecione **failover**.

4. Selecione a rede de destino e selecione **✓** para iniciar o processo de failover.

Para obter mais informações sobre como fazer um failover, consulte [failover em site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Executar uma reativação pós-falha

Para obter considerações específicas para SQL Server durante o failback, consulte [replicar aplicativos com SQL Server e Azure site Recovery](site-recovery-sql.md).

1. Vá para a portal do Azure e selecione seu cofre de Site Recovery.

2. Selecione o plano de recuperação criado para o Dynamics AX.

3. Selecione **failover**e selecione **failover**.

4. Selecione **alterar direção**.

5. Selecione as opções apropriadas: sincronização de dados e criação de VM.

6. Selecione **✓** para iniciar o processo de failback.


Para saber mais sobre como fazer um failback, confira [VMs VMware de failback do Azure para o local](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Resumo
Usando Site Recovery, você pode criar um plano de recuperação de desastre automatizado completo para seu aplicativo do Dynamics AX. No caso de uma interrupção, você pode iniciar o failover em segundos de qualquer lugar e colocar o aplicativo em funcionamento em minutos.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre como proteger cargas de trabalho corporativas com Site Recovery, confira [quais cargas de trabalho posso proteger?](site-recovery-workload.md).
