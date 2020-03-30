---
title: Configurar a recuperação de desastres da SAP NetWeaver com a recuperação do site azure
description: Saiba como configurar a recuperação de desastres para o SAP NetWeaver com a Recuperação do Site Azure.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 29acd1b00d23e4f1c2f241027dadbbb406e5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190784"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Configurar a recuperação de desastres para uma implementação de aplicações SAP NetWeaver de vários níveis

A maioria das implantações sAP de grande dimensão e médias utiliza alguma forma de solução de recuperação de desastres. A importância de soluções robustas e testáveis de recuperação de desastres aumentou à medida que mais processos de negócio são movidos para aplicações como a SAP. A Recuperação do Sítio Azure foi testada e integrada com aplicações SAP. A Recuperação do Site excede as capacidades da maioria das soluções de recuperação de desastres no local e com um custo total de propriedade mais baixo do que as soluções concorrentes.

Com a Recuperação do Site, pode:
* Permitir a proteção das aplicações de produção SAP NetWeaver e não NetWeaver que funcionam no local replicando componentes para o Azure.
* Permitir a proteção das aplicações de produção SAP NetWeaver e não NetWeaver que funcionam no Azure replicando componentes para outro datacenter Azure.
* Simplificar a migração para a nuvem utilizando a Recuperação de Sites para migrar a implementação do SAP para o Azure.
* Simplificar as atualizações, testes e prototipagem do projeto SAP, criando um clone de produção a pedido para testar aplicações SAP.

Pode proteger as implementações de aplicações SAP NetWeaver utilizando a Recuperação do [Site Azure](site-recovery-overview.md). Este artigo abrange as melhores práticas para proteger uma implementação sap NetWeaver de três níveis no Azure quando se replica para outro datacenter Azure utilizando a Recovery do Site. O artigo descreve cenários e configurações suportados, e como fazer falhas de teste (exercícios de recuperação de desastres) e falhas reais.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que sabe como fazer as seguintes tarefas:

* [Replicar uma máquina virtual para Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Conceber uma rede de recuperação](site-recovery-azure-to-azure-networking-guidance.md)
* [Faça um teste falhado para Azure](azure-to-azure-walkthrough-test-failover.md)
* [Faça uma falha com Azure](site-recovery-failover.md)
* [Replicar um controlador de domínio](site-recovery-active-directory.md)
* [Replicar uma instância de Servidor SQL](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Cenários suportados

Pode utilizar a Recuperação do Site para implementar uma solução de recuperação de desastres nos seguintes cenários:
* Tem sistemas SAP a funcionar num centro de dados Azure, e está a replicá-los para outro centro de dados Azure (recuperação de desastres Azure-to-Azure). 
   Para mais informações, consulte a [arquitetura de replicação Azure-to-Azure.](https://aka.ms/asr-a2a-architecture)
* Tem sistemas SAP em funcionamento em servidores VMware (ou físicos) no local. Também está a replicar os sistemas SAP para um local de recuperação de desastres num centro de dados Azure (recuperação de desastres VMware-to-Azure). 
   Este cenário requer alguns componentes adicionais. Para mais informações, consulte [a arquitetura de replicação VMware-to-Azure](https://aka.ms/asr-v2a-architecture).
* Tens sistemas SAP a funcionar no Hiper-V no local. Também está a replicar os sistemas SAP para um local de recuperação de desastres num centro de dados Azure (recuperação de desastres Hyper-V-to-Azure).
   Este cenário requer alguns componentes adicionais. Para mais informações, consulte a arquitetura de [replicação Hyper-V-to-Azure.](https://aka.ms/asr-h2a-architecture)

Neste artigo, usamos um cenário de recuperação de desastres **Azure-to-Azure.** O cenário mostra-lhe as capacidades de recuperação de desastres do SAP da Recuperação do Local. Como a replicação da recuperação do site não é específica da aplicação, o processo descrito também se aplica a outros cenários.

### <a name="required-foundation-services"></a>Serviços de fundação necessários
No cenário que discutimos neste artigo, são implementados os seguintes serviços de fundação:
* Azure ExpressRoute ou Azure VPN Gateway
* Pelo menos um controlador de domínio Ativo Azure e servidor DNS, em execução no Azure

Recomendamos que estabeleça esta infraestrutura antes de implementar a Recuperação do Local.

## <a name="reference-sap-application-deployment"></a>Implementação de aplicações SAP de referência

Esta arquitetura de referência está executando SAP NetWeaver em um ambiente Windows em Azure com alta disponibilidade. Esta arquitetura é implantada com tamanhos específicos de máquina virtual (VM) que você pode mudar para acomodar as necessidades da sua organização.

![Diagrama de um padrão típico de implantação sap](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Considerações sobre a recuperação após desastre

Para a recuperação de desastres, deve ser capaz de falhar numa região secundária. Cada nível utiliza uma estratégia diferente para fornecer proteção contra a recuperação de desastres.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>VMs executando piscinas Despachantes Web SAP

O componente Web Dispatcher funciona como um equilibrante de carga para o tráfego SAP entre os servidores de aplicações SAP. Para obter uma elevada disponibilidade para o componente Web Dispatcher, o Azure Load Balancer implementa a configuração paralela do Web Dispatcher. O Web Dispatcher utiliza uma configuração de rodada para distribuição de tráfego HTTP(S) entre os Web Dispatchers disponíveis na piscina de balanceadores.

#### <a name="vms-running-application-servers-pools"></a>VMs executando conjuntos de servidores de aplicações
A transação SMLG gere grupos de login para servidores de aplicações ABAP. Utiliza a função de equilíbrio de carga dentro do servidor de mensagens dos Serviços Centrais para distribuir carga de trabalho entre os conjuntos de servidores de aplicações SAP para tráfego SAPGUIs e RFC. Pode replicar esta gestão utilizando a Recuperação do Site.

#### <a name="vms-running-sap-central-services-clusters"></a>VMs executando clusters de Serviços Centrais SAP
Esta arquitetura de referência gere os Serviços Centrais em VMs no nível de aplicação. A Central Services é um potencial ponto único de falha quando num único VM. A implantação típica e a elevada disponibilidade não são requisitos.

Para implementar uma solução de alta disponibilidade, pode utilizar um cluster de disco partilhado ou um cluster de partilha de ficheiros. Para configurar VMs para um cluster de disco partilhado, utilize o Cluster failover do Servidor do Windows. Recomendamos que use a testemunha como testemunha de quórum.

 > [!NOTE]
 > Como a Recuperação do Site não replica a testemunha de nuvem, recomendamos que você implante a testemunha de nuvem na região de recuperação de desastres.

Para suportar o ambiente de cluster failover, a [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) faz a função de volume partilhado cluster. Na função, o Cluster SIOS DataKeeper replica discos independentes pertencentes aos nós do cluster. Como o Azure não suporta discos partilhados de forma nativa, requer soluções fornecidas pelo SIOS.

Também pode lidar com o clusteratravés da implementação de um cluster de partilha de ficheiros. A [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) modificou recentemente o padrão de implantação dos Serviços Centrais para aceder aos diretórios globais /sapmnt através de um caminho do CNU. Recomendamos ainda que garanta que a participação da UNC /sapmnt está altamente disponível. Pode verificar a sua instância de Serviços Centrais. Utilize o Cluster de Falha do Servidor do Windows com o Servidor de Ficheiros Scale out (SOFS) e a função Espaços de Armazenamento Direta (S2D) no Windows Server 2016.

 > [!NOTE]
 > Atualmente, a Recovery do Site suporta apenas a replicação de pontos consistentes com acidentes de máquinas virtuais que utilizam espaços de armazenamento direto e o nó passivo do Datakeeper SIOS.


## <a name="more-disaster-recovery-considerations"></a>Mais considerações de recuperação de desastres

Você pode usar a Recuperação do Site para orquestrar a falha de implantação completa do SAP em todas as regiões de Azure.
Seguem-se os passos para a instalação da recuperação do desastre:

1. Replicar máquinas virtuais
1. Conceber uma rede de recuperação
1. Replicar um controlador de domínio
1. Replicar o nível de base de dados
1. Fazer uma ativação pós-falha de teste
1. Fazer uma ativação pós-falha

Segue-se a recomendação para a recuperação de desastres de cada nível utilizado neste exemplo.

 **Níveis SAP** | **Recomendação**
 --- | ---
**Piscina de despacho web SAP** |  Replicar usando a recuperação do site 
**Piscina de servidor de aplicação SAP** |  Replicar usando a recuperação do site 
**Cluster sap serviços centrais** |  Replicar usando a recuperação do site 
**Máquinas virtuais de diretório ativo** |  Utilizar replicação de diretório ativo 
**Servidores da Base de Dados SQL** |  Use o servidor SQL sempre na replicação

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais

Para começar a replicar todas as máquinas virtuais da aplicação SAP para o centro de dados de recuperação de desastres do Azure, siga a orientação em [Replicar uma máquina virtual para o Azure](azure-to-azure-walkthrough-enable-replication.md).

* Para obter orientações sobre a proteção do Diretório Ativo e do DNS, aprenda [a proteger o Diretório Ativo e](site-recovery-active-directory.md)o DNS .

* Para obter orientações sobre a proteção do nível de base de dados em execução no Servidor SQL, aprenda [a proteger o Servidor SQL](site-recovery-sql.md).

## <a name="networking-configuration"></a>Configuração de rede

Se utilizar um endereço IP estático, pode especificar o endereço IP que pretende que a máquina virtual tome. Para definir o endereço IP, vá ao**cartão**de interface de rede **Compute e Network** > .

![Screenshot que mostra como definir um endereço IP privado no painel de cartão de interface da Rede de Recuperação do Site](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

Um plano de recuperação apoia a sequenciação de vários níveis numa aplicação de vários níveis durante uma falha. A sequenciação ajuda a manter a consistência da aplicação. Quando criar um plano de recuperação para uma aplicação web de vários níveis, complete os passos descritos no Create a recovery utilizando a [Recuperação](site-recovery-create-recovery-plans.md)do Site .

### <a name="add-virtual-machines-to-failover-groups"></a>Adicione máquinas virtuais a grupos de failover

1. Crie um plano de recuperação adicionando o servidor de aplicações, o despachante web e os VMs dos serviços SAP Central.
1. Selecione **Personalizar** para agrupar os VMs. Por padrão, todos os VMs fazem parte do Grupo 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Adicione scripts ao plano de recuperação
Para que as suas aplicações funcionem corretamente, poderá ser necessário efazer algumas operações nas máquinas virtuais Azure. Faça estas operações após a falha ou durante uma falha de teste. Também pode automatizar algumas operações pós-falha. Por exemplo, atualize a entrada do DNS e altere as ligações e ligações adicionando scripts correspondentes ao plano de recuperação.

Pode implantar os scripts de Recuperação do Site mais utilizados na sua conta De automação Azure selecionando **o Deploy para o Azure**. Quando utilizar qualquer guião publicado, siga a orientação no script.

[![Desdobre para Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione um script de pré-ação ao Grupo 1 para falhar sobre o grupo de disponibilidade do Servidor SQL. Utilize o script ASR-SQL-FailoverAG publicado nos scripts da amostra. Siga a orientação no script e faça as alterações necessárias no script adequadamente.
1. Adicione um script pós-acção para fixar um equilibrador de carga nas máquinas virtuais falhadas do nível Web (Grupo 1). Utilize o script ASR-AddSingleLoadBalancer publicado nos scripts da amostra. Siga a orientação no script e faça as alterações necessárias no script conforme necessário.

![Plano de Recuperação SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

1. No portal Azure, selecione o seu cofre de Serviços de Recuperação.
1. Selecione o plano de recuperação que criou para aplicações SAP.
1. Selecione **Ativação Pós-falha de Teste**.
1. Para iniciar o processo de failover do teste, selecione o ponto de recuperação e a rede virtual Azure.
1. Quando o ambiente secundário estiver em pé, execute validações.
1. Quando as validações estiverem completas, limpe o ambiente de failover selecionando o **failover**do teste de limpeza .

Para mais informações, consulte [Test failover to Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar uma ativação pós-falha

1. No portal Azure, selecione o seu cofre de Serviços de Recuperação.
1. Selecione o plano de recuperação que criou para aplicações SAP.
1. Selecione **Ativação pós-falha**.
1. Para iniciar o processo de failover, selecione o ponto de recuperação.

Para mais informações, consulte [Failover na Recuperação](site-recovery-failover.md)do Site .

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a construção de uma solução de recuperação de desastres para implementações SAP NetWeaver utilizando a Recuperação do Site. Consulte o papel branco descarregado [SAP NetWeaver: Construindo uma solução](https://aka.ms/asr_sap)de recuperação de desastres com recuperação do site . O Livro Branco discute recomendações para várias arquiteturas SAP. Pode ver aplicações suportadas e tipos de VM para SAP no Azure. Existem também opções de planopara testar a sua solução de recuperação de desastres.
* Saiba mais sobre a replicação de [outras cargas de trabalho](site-recovery-workload.md) utilizando a Recuperação do Site.
