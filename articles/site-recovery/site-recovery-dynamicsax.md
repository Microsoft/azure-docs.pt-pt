---
title: Configurar a recuperação após desastre para uma implementação de Dynamics AX com várias camadas com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como configurar a recuperação após desastre para o Dynamics AX com o Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: b97bf56c23dfa96acf7cb5af5ac28b4270de117d
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56879786"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Configurar a recuperação após desastre para uma aplicação com várias camadas do Dynamics AX   




 Dynamics AX é uma das mais populares soluções ERP utilizadas por empresas a padronizar processos em localizações, a gerir os recursos e a simplificar a conformidade. Uma vez que o aplicativo é fundamental para uma organização, em caso de desastre, a aplicação deve estar em execução no tempo mínimo.

Hoje em dia, Dynamics AX não fornece qualquer desastre out-of-the-box capacidades de recuperação. Dynamics AX é composta por vários componentes de servidor, como o servidor de objeto de aplicativo do Windows, do Azure Active Directory, a base de dados do Azure SQL, SharePoint Server e Reporting Services. Para gerir o desastre recuperação de cada um desses componentes manualmente não só é dispendiosa, mas também propensas.

Este artigo explica como pode criar uma solução de recuperação após desastre para a sua aplicação do Dynamics AX, utilizando [do Azure Site Recovery](site-recovery-overview.md). Também aborda de ativações pós-falha de teste planeada/não planeada através de um plano de recuperação de um clique, configurações suportadas e pré-requisitos.



## <a name="prerequisites"></a>Pré-requisitos

A implementação de recuperação após desastre para aplicações do Dynamics AX utilizando a recuperação de Site requer os seguintes pré-requisitos:

• Configurar uma implementação de Dynamics AX no local.

• Crie um cofre de recuperação de Site uma subscrição do Azure.

• Se o Azure é o seu site de recuperação, execute a ferramenta de avaliação de preparação de máquinas virtuais do Azure nas VMs. Têm de ser compatíveis com os serviços de máquinas virtuais do Azure e o Site Recovery.

## <a name="site-recovery-support"></a>Suporte do Site Recovery

Com o objetivo de criar este artigo, nós usamos máquinas virtuais VMware com o Dynamics AX 2012 R3 no Windows Server 2012 R2 Enterprise. Como a replicação do site recovery é independente de aplicativo, esperamos que as recomendações fornecidas aqui para manter-se para os seguintes cenários.

### <a name="source-and-target"></a>Origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Ativar a recuperação após desastre da aplicação do Dynamics AX utilizando a recuperação de Site
### <a name="protect-your-dynamics-ax-application"></a>Proteja as suas aplicações do Dynamics AX
Para ativar a aplicação completa de replicação e recuperação, cada componente do Dynamics AX têm de ser protegida.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Configurar a replicação do Active Directory e DNS

Do Active Directory é necessário no site de recuperação após desastre para a aplicação do Dynamics AX para a função. Recomendamos as seguintes duas opções, com base na complexidade de ambiente de no local do cliente.

**Opção 1**

O cliente tem um pequeno número de aplicativos e um controlador de domínio único para todo o site local e planos efetuar a ativação pós-falha de todo o site em conjunto. Recomendamos que utilize replicação do Site Recovery para replicar a máquina de controlador de domínio para um site secundário (aplicável para cenários de site a site e site para o Azure).

**Opção 2**

O cliente tem um grande número de aplicativos e está a executar uma floresta do Active Directory e os planos para a ativação pós-falha alguns aplicativos cada vez. Recomendamos que configure um controlador de domínio adicionais no site de recuperação após desastre (um site secundário ou no Azure).

 Para obter mais informações, consulte [disponibilizar um controlador de domínio num site de recuperação após desastre](site-recovery-active-directory.md). Para o restante deste documento, partimos do princípio de que um controlador de domínio está disponível no site de recuperação após desastre.

### <a name="2-set-up-sql-server-replication"></a>2. Configurar a replicação do SQL Server
Para obter orientação técnica sobre a opção recomendada para proteger a camada SQL, consulte [replicar aplicações com o SQL Server e o Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Ativar a proteção para o cliente do Dynamics AX e VMs de servidor de objeto de aplicação
Efetuar a configuração de recuperação de sites relevante com base no facto das VMs são implementadas num [Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Recomendamos que configure a frequência consistente de falhas para 15 minutos.
>

O instantâneo seguinte mostra o estado de proteção de VMs de componente do Dynamics num cenário de proteção de site para o Azure de VMware.

![Itens protegidos](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Configurar rede
**Configurar computação VM e as definições de rede**

Para o cliente do Dynamics AX e VMs de servidor de objeto de aplicação, configure as definições de rede na recuperação de Site para que as redes VM são anexadas para a rede de recuperação após desastre correta após a ativação pós-falha. Certifique-se de que a rede de recuperação após desastre para estas camadas é encaminhável para o escalão SQL.

Pode selecionar a VM nos itens replicados para configurar as definições de rede, conforme mostrado no seguinte instantâneo:

* Para os servidores do servidor de objeto de aplicações, selecione o conjunto de disponibilidade correto.

* Se estiver a utilizar um IP estático, especifique o IP que pretende que a VM para tirar o **IP de destino** caixa de texto.

    ![Definições de rede](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. Criar um plano de recuperação

Pode criar um plano de recuperação no Site Recovery para automatizar o processo de ativação pós-falha. Adicione uma camada de aplicação e uma camada web no plano de recuperação. Ordená-las em grupos diferentes, para que o front-end encerrado antes da camada de aplicação.

1. Selecione o Cofre de recuperação de sites na sua subscrição e selecione o **planos de recuperação** mosaico.

2. Selecione **+ o plano de recuperação**e especifique um nome.

3. Selecione o **origem** e **destino**. O destino pode ser Azure ou um site secundário. Se escolhe o Azure, tem de especificar o modelo de implementação.

    ![Criar plano de recuperação](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Selecione o servidor de objeto de aplicativo e o cliente de VMs para o plano de recuperação e selecione o ✓.

    ![Selecionar itens](./media/site-recovery-dynamics-ax/selectvms.png)

    Exemplo de plano de recuperação:

    ![Detalhes do plano de recuperação](./media/site-recovery-dynamics-ax/recoveryplan.png)

Pode personalizar o plano de recuperação para a aplicação do Dynamics AX, adicionando os seguintes passos. O instantâneo anterior mostra o plano de recuperação completa depois de adicionar todas as etapas.


* **Passos de ativação pós-falha do SQL Server**: Para obter informações sobre passos de recuperação específicos para o SQL server, consulte [aplicativos de replicação com o SQL Server e o Azure Site Recovery](site-recovery-sql.md).

* **Grupo de ativação pós-falha 1**: Efetuar a ativação pós-falha de VMs de servidor de objeto de aplicação.
Certifique-se de que o ponto de recuperação selecionado está mais próximo possível PIT, na base de dados, mas não um passo à frente ele.

* **script**: Adicionar balanceador de carga (só E-A).
Adicione um script (através da automatização do Azure) após o grupo de VM de servidor de objeto do aplicativo é exibido para adicionar um balanceador de carga à mesma. Pode utilizar um script para executar esta tarefa. Para obter mais informações, consulte [como adicionar um balanceador de carga para recuperação após desastre de aplicações com múltiplas camadas](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Grupo de ativação pós-falha 2**: Efetuar a ativação pós-falha de VMs de clientes do Dynamics AX. Efetuar a ativação pós-falha de VMs de camada de web como parte do plano de recuperação.


### <a name="perform-a-test-failover"></a>Executar uma ativação pós-falha de teste

Para obter mais informações específicas para o Active Directory durante a ativação pós-falha de teste, consulte o guia de complemento de "Solução de recuperação de desastres do Active Directory".

Para obter mais informações específicas para o SQL server durante a ativação pós-falha de teste, consulte [replicar aplicações com o SQL Server e o Azure Site Recovery](site-recovery-sql.md).

1. Vá ao portal do Azure e selecione o Cofre de recuperação de sites.

2. Selecione o plano de recuperação criado para o Dynamics AX.

3. Selecione **Ativação Pós-falha de Teste**.

4. Selecione a rede virtual para iniciar o processo de ativação pós-falha de teste.

5. Depois do ambiente secundário está ativo, pode realizar as validações.

6. Depois das validações forem concluídas, selecione **validações concluir** e o ambiente de ativação pós-falha de teste sejam limpos.

Para obter mais informações sobre a execução de uma ativação pós-falha de teste, consulte [testar a ativação pós-falha para o Azure no Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Execute uma ativação pós-falha

1. Vá ao portal do Azure e selecione o Cofre de recuperação de sites.

2. Selecione o plano de recuperação criado para o Dynamics AX.

3. Selecione **ativação pós-falha**e selecione **ativação pós-falha**.

4. Selecione a rede de destino e selecione **✓** para iniciar o processo de ativação pós-falha.

Para obter mais informações sobre efetuar uma ativação pós-falha, consulte [ativação pós-falha no Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Executar uma reativação pós-falha

Para considerações específicas para o SQL Server durante a reativação pós-falha, consulte [replicar aplicações com o SQL Server e o Azure Site Recovery](site-recovery-sql.md).

1. Vá ao portal do Azure e selecione o Cofre de recuperação de sites.

2. Selecione o plano de recuperação criado para o Dynamics AX.

3. Selecione **ativação pós-falha**e selecione **ativação pós-falha**.

4. Selecione **mudar a direção da**.

5. Selecione as opções apropriadas: sincronização de dados e a criação da VM.

6. Selecione **✓** para iniciar o processo de reativação pós-falha.


Para obter mais informações sobre a fazer uma reativação pós-falha, consulte [VMs de VMware de reativação pós-falha do Azure para o local](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Resumo
Ao utilizar o Site Recovery, pode criar um plano de recuperação após desastre automatizado completa para a sua aplicação do Dynamics AX. Em caso de uma interrupção, pode iniciar a ativação pós-falha dentro de segundos a partir de qualquer lugar e obtenha a aplicação em execução em poucos minutos.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre como proteger cargas de trabalho empresariais com o Site Recovery, veja [que cargas de trabalho posso proteger?](site-recovery-workload.md).
