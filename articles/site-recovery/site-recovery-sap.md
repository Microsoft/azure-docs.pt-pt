---
title: Configurar a recuperação de desastres da SAP NetWeaver com recuperação do local de Azure
description: Saiba como configurar a recuperação de desastres para o SAP NetWeaver com a Recuperação do Site Azure.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 7b4a622de142fd44b64015c8238f44dafc34ce72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86133694"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Configurar a recuperação de desastres para uma implementação de aplicativos SAP NetWeaver de vários níveis

A maioria das grandes e médias implementações de SAP utilizam alguma forma de solução de recuperação de desastres. A importância de soluções robustas e testáveis de recuperação de desastres aumentou à medida que mais processos de negócio fundamentais são transferidos para aplicações como a SAP. A Azure Site Recovery foi testada e integrada com aplicações SAP. A Recuperação do Site excede as capacidades da maioria das soluções de recuperação de desastres no local, e com um custo total de propriedade mais baixo do que as soluções concorrentes.

Com a Recuperação do Site, pode:
* Permitir a proteção das aplicações de produção SAP NetWeaver e não-NetWeaver que funcionam no local replicando componentes para o Azure.
* Permitir a proteção das aplicações de produção SAP NetWeaver e não-NetWeaver que funcionam no Azure replicando componentes para outro datacenter Azure.
* Simplificar a migração para a nuvem utilizando a Recuperação de Sites para migrar a implementação do SAP para o Azure.
* Simplificar as atualizações, testes e prototipagem do projeto SAP, criando um clone de produção a pedido para testar aplicações SAP.

Pode proteger as implementações da aplicação SAP NetWeaver utilizando [a Recuperação do Local de Azure.](site-recovery-overview.md) Este artigo abrange as melhores práticas para proteger uma implementação de TRÊS níveis SAP NetWeaver no Azure quando se replica para outro datacenter Azure utilizando a Recuperação do Site. O artigo descreve cenários e configurações suportados, e como fazer testes failovers (exercícios de recuperação de desastres) e falhas reais.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que sabe como fazer as seguintes tarefas:

* [Replicar uma máquina virtual para Azure](./azure-to-azure-tutorial-enable-replication.md)
* [Desenhe uma rede de recuperação](./azure-to-azure-about-networking.md)
* [Faça um teste de failover para Azure](./azure-to-azure-tutorial-dr-drill.md)
* [Faça um failover para Azure](site-recovery-failover.md)
* [Replicar um controlador de domínio](site-recovery-active-directory.md)
* [Replicar uma instância do Servidor SQL](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Cenários suportados

Pode utilizar a Recuperação do Local para implementar uma solução de recuperação de desastres nos seguintes cenários:
* Tem sistemas SAP a funcionar num datacenter Azure, e está a reproduzi-los para outro datacenter Azure (recuperação de desastres Azure-to-Azure). 
   Para mais informações, consulte [a arquitetura de replicação Azure-to-Azure.](https://aka.ms/asr-a2a-architecture)
* Tem sistemas SAP a funcionar em servidores VMware (ou físicos) no local. Também está a replicar os sistemas SAP para um local de recuperação de desastres num datacenter Azure (recuperação de desastres VMware-to-Azure). 
   Este cenário requer alguns componentes adicionais. Para mais informações, consulte [a arquitetura de replicação VMware-to-Azure](https://aka.ms/asr-v2a-architecture).
* Tem sistemas SAP a funcionar no hiper-V no local. Também está a replicar os sistemas SAP para um local de recuperação de desastres num datacenter Azure (recuperação de desastres Hiper-V-a-Azure).
   Este cenário requer alguns componentes adicionais. Para obter mais informações, consulte [a arquitetura de replicação Hyper-V-to-Azure.](https://aka.ms/asr-h2a-architecture)

Neste artigo, usamos um cenário de recuperação de desastres **Azure-to-Azure.** O cenário mostra-lhe as capacidades de recuperação de desastres SAP da Recuperação do Local. Como a replicação da Recuperação do Site não é específica da aplicação, espera-se que o processo descrito também se aplique a outros cenários.

### <a name="required-foundation-services"></a>Serviços de fundação necessários
No cenário que discutimos neste artigo, são implantados os seguintes serviços de fundação:
* Azure ExpressRoute ou Azure VPN Gateway
* Pelo menos um controlador de domínio Azure Ative Directory e servidor DNS, em execução em Azure

Recomendamos que estabeleça esta infraestrutura antes de implementar a Recuperação do Local.

## <a name="reference-sap-application-deployment"></a>Implantação de aplicações SAP de referência

Esta arquitetura de referência está executando SAP NetWeaver em um ambiente Windows em Azure com alta disponibilidade. Esta arquitetura é implantada com tamanhos específicos de máquina virtual (VM) que você pode alterar para acomodar as necessidades da sua organização.

![Diagrama de um padrão típico de implantação de SAP](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Considerações sobre a recuperação após desastre

Para a recuperação de desastres, deve ser capaz de falhar numa região secundária. Cada nível usa uma estratégia diferente para fornecer proteção de recuperação de desastres.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>VMs executando piscinas de despachantes web SAP

O componente Web Dispatcher funciona como um balanceador de carga para o tráfego SAP entre os servidores de aplicações SAP. Para obter uma elevada disponibilidade para o componente Web Dispatcher, o Azure Load Balancer implementa a configuração paralela do Web Dispatcher. O Web Dispatcher utiliza uma configuração de rodapé para distribuição de tráfego HTTP(S) entre os Despachantes Web disponíveis na piscina dos esquilibradores.

#### <a name="vms-running-application-servers-pools"></a>VMs executando conjuntos de servidores de aplicações
A transação SMLG gere grupos de login para servidores de aplicações ABAP. Utiliza a função de equilíbrio de carga dentro do servidor de mensagens dos Serviços Centrais para distribuir carga de trabalho entre os conjuntos de servidores de aplicações SAPGUIs e RFC. Pode replicar esta gestão utilizando a Recuperação do Local.

#### <a name="vms-running-sap-central-services-clusters"></a>VMs que gerem clusters de Serviços Centrais SAP
Esta arquitetura de referência gere os Serviços Centrais em VMs no nível de aplicação. Os Serviços Centrais são um potencial ponto único de falha quando num único VM. A implantação típica e a alta disponibilidade não são requisitos.

Para implementar uma solução de alta disponibilidade, pode utilizar um cluster de disco partilhado ou um cluster de partilha de ficheiros. Para configurar VMs para um cluster de disco partilhado, utilize o Windows Server Failover Cluster. Recomendamos que use a testemunha de nuvem como testemunha de quórum.

 > [!NOTE]
 > Como a Recuperação do Local não replica a testemunha em nuvem, recomendamos que implemente a testemunha em nuvem na região de recuperação de desastres.

Para suportar o ambiente de cluster failover, [o SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) faz a função de volume partilhado do cluster. Na função, o SiOS DataKeeper Cluster replica discos independentes pertencentes aos nós do cluster. Como o Azure não suporta de forma nativa discos partilhados, requer soluções fornecidas pelo SIOS.

Também pode lidar com o agrupamento implementando um cluster de partilha de ficheiros. [A SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) modificou recentemente o padrão de implantação dos Serviços Centrais para aceder aos diretórios globais /sapmnt através de um caminho unc. Recomendamos ainda que garanta que a parte /sapmnt UNC está altamente disponível. Pode verificar a sua instância dos Serviços Centrais. Utilize o Cluster de Falha do Servidor do Windows com o Servidor de Ficheiros Scale out (SOFS) e a funcionalidade De Espaços de Armazenamento Direta (S2D) no Windows Server 2016.

 > [!NOTE]
 > Atualmente, a Recuperação do Site suporta apenas a replicação de pontos consistentes por falhas de máquinas virtuais que utilizam espaços de armazenamento diretos e o nó passivo do SiOS Datakeeper.


## <a name="more-disaster-recovery-considerations"></a>Mais considerações de recuperação de desastres

Pode utilizar a Recuperação do Local para orquestrar o fracasso da implantação completa do SAP nas regiões de Azure.
Seguem-se os passos para a recuperação do desastre:

1. Replicar máquinas virtuais
1. Desenhe uma rede de recuperação
1. Replicar um controlador de domínio
1. Replicar nível de base de dados
1. Fazer uma ativação pós-falha de teste
1. Fazer uma ativação pós-falha

Segue-se a recomendação para a recuperação de catástrofes de cada nível utilizado neste exemplo.

 **Níveis SAP** | **Recomendação**
 --- | ---
**Piscina de despachante web SAP** |  Replicar usando a recuperação do site 
**Piscina de servidor de aplicação SAP** |  Replicar usando a recuperação do site 
**Aglomerado de Serviços Centrais SAP** |  Replicar usando a recuperação do site 
**Máquinas virtuais de diretório ativo** |  Use replicação de diretório ativo 
**Servidores da Base de Dados SQL** |  Use o servidor SQL sempre na replicação

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais

Para começar a replicar todas as máquinas virtuais da aplicação SAP para o centro de dados de recuperação de desastres Azure, siga a orientação em [Replicar uma máquina virtual para Azure](./azure-to-azure-tutorial-enable-replication.md).

* Para obter orientações sobre a proteção do Ative Directory e do DNS, aprenda [a proteger o Ative Directory e o DNS](site-recovery-active-directory.md).

* Para obter orientações sobre a proteção do nível de base de dados em execução no SQL Server, aprenda [a proteger o SQL Server](site-recovery-sql.md).

## <a name="networking-configuration"></a>Configuração da rede

Se utilizar um endereço IP estático, pode especificar o endereço IP que pretende que a máquina virtual seja tomada. Para definir o endereço IP, aceda ao cartão de interface de rede de **configurações de computação e rede**  >  **Network interface card**.

![Screenshot que mostra como definir um endereço IP privado no painel de interface da Rede de Recuperação do Site](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

Um plano de recuperação apoia a sequenciação de vários níveis numa aplicação multi-nível durante uma falha. A sequenciação ajuda a manter a consistência da aplicação. Quando criar um plano de recuperação para uma aplicação web multi-nível, complete os passos descritos na [Criar um plano de recuperação utilizando a Recuperação do Site](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Adicione máquinas virtuais a grupos de failover

1. Crie um plano de recuperação adicionando o servidor de aplicações, o despachante web e os VMs dos serviços CENTRAIS SAP.
1. **Selecione Personalizar** para agrupar os VMs. Por predefinição, todos os VMs fazem parte do Grupo 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Adicione scripts ao plano de recuperação
Para que as suas aplicações funcionem corretamente, poderá necessitar de fazer algumas operações nas máquinas virtuais Azure. Faça estas operações após a falha ou durante um teste de falha. Também pode automatizar algumas operações pós-falha. Por exemplo, atualize a entrada de DNS e altere as ligações e ligações adicionando scripts correspondentes ao plano de recuperação.

Pode implementar os scripts de Recuperação de Sítio mais utilizados na sua conta Azure Automation, selecionando **Implementar para Azure.** Quando utilizar qualquer script publicado, siga a orientação no script.

[![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione um script pré-ação ao Grupo 1 para falhar sobre o grupo de disponibilidade do SQL Server. Utilize o script ASR-SQL-FailoverAG publicado nos scripts da amostra. Siga a orientação no script e faça as alterações necessárias no script adequadamente.
1. Adicione um script pós-acção para fixar um equilibrador de carga nas máquinas virtuais falhadas do nível Web (Grupo 1). Utilize o ASR-AddSingleLoadBalancer script publicado nos scripts da amostra. Siga a orientação no script e faça as alterações necessárias no script, se necessário.

![Plano de Recuperação do SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

1. No portal Azure, selecione o cofre dos Serviços de Recuperação.
1. Selecione o plano de recuperação que criou para aplicações SAP.
1. Selecione **Ativação Pós-falha de Teste**.
1. Para iniciar o processo de failover do teste, selecione o ponto de recuperação e a rede virtual Azure.
1. Quando o ambiente secundário estiver em cima, execute validações.
1. Quando as validações estiverem completas, limpe o ambiente de failover selecionando **o teste de limpeza por falha**.

Para obter mais informações, consulte [test failover to Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar uma ativação pós-falha

1. No portal Azure, selecione o cofre dos Serviços de Recuperação.
1. Selecione o plano de recuperação que criou para aplicações SAP.
1. Selecione **Ativação pós-falha**.
1. Para iniciar o processo de failover, selecione o ponto de recuperação.

Para obter mais informações, consulte [Failover na Recuperação do Local.](site-recovery-failover.md)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a construção de uma solução de recuperação de desastres para implementações SAP NetWeaver utilizando a Recuperação do Site. Consulte o papel branco transferível [SAP NetWeaver: Construindo uma solução de recuperação de desastres com recuperação do local.](https://aka.ms/asr_sap) O livro branco discute recomendações para várias arquiteturas SAP. Pode ver aplicações suportadas e tipos de VM para SAP on Azure. Existem também opções de plano para testar a sua solução de recuperação de desastres.
* Saiba mais sobre [a replicação de outras cargas de trabalho](site-recovery-workload.md) utilizando a Recuperação do Site.
