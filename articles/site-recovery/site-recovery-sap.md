---
title: Configurar a recuperação após desastre para implementação de aplicações SAP NetWeaver de várias camada com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como configurar a recuperação após desastre para as implementações de aplicações SAP NetWeaver com o Azure Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 68efc039c5de5d7f61b7ce34e74c6c2cf4bad027
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670881"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Configurar a recuperação após desastre para uma implementação de aplicação SAP NetWeaver com várias camada

A maioria das implementações de grande tamanho e tamanho médio da SAP utilizar alguma forma de solução de recuperação após desastre. A importância de soluções de recuperação após desastre robusta e que podem ser testados aumentou conforme mais principais processos de negócio são movidos para aplicativos, como o SAP. O Azure Site Recovery foi testado e integrado com aplicações SAP. Recuperação de sites excede as capacidades da maioria das soluções de recuperação de desastre no local e num custo mais baixo de total de propriedade (TCO) que as soluções de concorrentes.

Com o Site Recovery, pode:
* **Ativar a proteção de aplicações de produção SAP NetWeaver e não NetWeaver que são executadas no local** através da replicação de componentes para o Azure.
* **Ativar a proteção de aplicações de produção SAP NetWeaver e não NetWeaver que executar no Azure** através da replicação de componentes para outro datacenter do Azure.
* **Simplificar a migração para a cloud** com o Site Recovery para migrar a implementação de SAP no Azure.
* **Simplificar as atualizações do projeto SAP, testes e protótipos** através da criação de uma produção clonar a pedido para testar aplicações SAP.

Este artigo descreve como proteger implementações de aplicações SAP NetWeaver utilizando [do Azure Site Recovery](site-recovery-overview.md). O artigo abrange as melhores práticas para proteger uma implementação de SAP NetWeaver de três camadas no Azure ao replicar para outro datacenter do Azure com o Site Recovery. Ele descreve os cenários suportados e configurações e como executar ativações pós-falha de teste (testes de recuperação após desastre) e as ativações pós-falha real.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que sabe como fazer as seguintes tarefas:

* [Replicar uma máquina virtual para o Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Criar uma rede de recuperação](site-recovery-azure-to-azure-networking-guidance.md)
* [Fazer uma ativação pós-falha de teste para o Azure](azure-to-azure-walkthrough-test-failover.md)
* [Fazer uma ativação pós-falha para o Azure](site-recovery-failover.md)
* [Replicar um controlador de domínio](site-recovery-active-directory.md)
* [Replicação do SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Cenários suportados
Pode utilizar o Site Recovery para implementar uma solução de recuperação após desastre nos seguintes cenários:
* Sistemas SAP em execução num único datacenter do Azure que são replicados para outro datacenter do Azure (recuperação de desastres do Azure para o Azure). Para obter mais informações, consulte [arquitetura de replicação do Azure para o Azure](https://aka.ms/asr-a2a-architecture).
* Sistemas SAP em execução no VMware (ou físico) servidores no local que replicar para um site de recuperação após desastre no datacenter do Azure (recuperação de desastre do VMware para o Azure). Este cenário requer alguns componentes adicionais. Para obter mais informações, consulte [arquitetura de replicação de VMware para o Azure](https://aka.ms/asr-v2a-architecture).
* Sistemas SAP em execução no Hyper-V no local que são replicados para um site de recuperação após desastre no datacenter do Azure (recuperação após desastre de Hyper-V para o Azure). Este cenário requer alguns componentes adicionais. Para obter mais informações, consulte [arquitetura de replicação de Hyper-V para o Azure](https://aka.ms/asr-h2a-architecture).

Neste artigo, vamos utilizar um **do Azure para o Azure** cenário de recuperação após desastre para demonstrar as capacidades de recuperação de desastres SAP da recuperação de sites. Uma vez que a replicação do Site Recovery não é específico do aplicativo, o processo descrito é esperado que também se aplicam a outros cenários.

### <a name="required-foundation-services"></a>Serviços fundamentais necessária
O cenário em que discutimos neste artigo, são implementados os seguintes serviços de base:
* Gateway de VPN do Azure ExpressRoute ou no Azure
* Pelo menos um controlador de domínio do Active Directory e o servidor DNS, em execução no Azure

Recomendamos que estabeleça esta infraestrutura antes de implementar o Site Recovery.

## <a name="reference-sap-application-deployment"></a>Implementação de aplicação SAP de referência

Esta arquitetura de referência mostra com o SAP NetWeaver num ambiente do Windows no Azure com elevada disponibilidade.  Esta arquitetura é implementada com tamanhos de máquina virtual específica (VM) que podem ser alterados para acomodar as necessidades da sua organização.

![Diagrama de um padrão de implementação de SAP típico](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Considerações sobre a recuperação após desastre

Para a recuperação após desastre (DR), tem de ser capaz de fazer a ativação pós-falha para uma região secundária. Cada camada utiliza uma estratégia diferente para fornecer proteção de recuperação após desastre (DR).

#### <a name="vms-running-sap-web-dispatcher-pool"></a>VMs que executam o conjunto de SAP Web Dispatcher 
O componente Web Dispatcher é utilizado como um balanceador de carga para tráfego SAP entre os servidores de aplicações SAP. Para alcançar a elevada disponibilidade para o componente Web Dispatcher, o Balanceador de carga do Azure é utilizado para implementar a configuração do Web Dispatcher paralela numa configuração de round robin para distribuição de tráfego HTTP (S) entre os Dispatchers de Web disponíveis no conjunto de Balanceador. Isso será replicado através do Azure Recovery (ASR) de Site e scripts de automatização serão utilizados para configurar o Balanceador de carga na região de recuperação após desastre. 

#### <a name="vms-running-application-servers-pool"></a>VMs que executam o agrupamento de servidores de aplicações
Para gerir grupos de logon para servidores de aplicações ABAP, a transação de SMLG é usada. Ele usa a função dentro do servidor de mensagens dos serviços de Central de balanceamento de carga para distribuir a carga de trabalho entre o agrupamento de servidores de aplicações SAP para SAPGUIs e RFC tráfego. Isso será replicado com o Azure Site Recovery 

#### <a name="vms-running-sap-central-services-cluster"></a>VMs que executam o cluster de SAP Central Services
Esta arquitetura de referência é executada Central de serviços em VMs na camada de aplicativos. Os serviços Central é um potencial ponto único de falha (SPOF) quando implantado numa única VM — uma implementação típica quando a elevada disponibilidade não é um requisito.<br>

Para implementar uma solução de elevada disponibilidade, pode ser utilizado um cluster de disco partilhado ou um cluster de partilha de ficheiros. Para configurar as VMs para um cluster de disco partilhado, utilize o Cluster de ativação pós-falha do Windows Server. Testemunho de nuvem é recomendado como um testemunho de quórum. 
 > [!NOTE]
 > O Azure Site Recovery não replica o testemunho de nuvem, portanto, recomenda-se para implementar o testemunho de nuvem na região de recuperação após desastre.

Para suportar o ambiente de cluster de ativação pós-falha [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) executa a função de volume partilhado de cluster através da replicação de discos independentes pertencentes nós do cluster. O Azure não suporta nativamente discos partilhados e, portanto, requer soluções fornecidas pelo SIOS. 

Outra forma de lidar com clustering é implementar um cluster de partilha de ficheiros. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) modificados recentemente o padrão de implementação de serviços Central para aceder os diretórios globais /sapmnt através de um caminho UNC. No entanto, ainda é recomendável para se certificar de que a partilha UNC /sapmnt é de elevada disponibilidade. Isso pode ser feito na instância de serviços Central, utilizando o Cluster de ativação pós-falha do Windows Server com o dimensionamento o servidor de ficheiros (SOFS) e a funcionalidade espaços de armazenamento direto (S2D) no Windows Server 2016. 
 > [!NOTE]
 > Suporte do Azure Site Recovery atualmente apenas ponto consistente com a replicação de máquinas virtuais utilizando espaços diretos e passivo do nó armazenamento da SIOS Datakeeper falhas


## <a name="disaster-recovery-considerations"></a>Considerações sobre a recuperação após desastre

Pode utilizar o Azure Site Recovery para orquestrar a ativação através de implementação de SAP completa entre regiões do Azure.
Seguem-se os passos para configurar a recuperação após desastre 

1. Replicar máquinas virtuais 
2. Criar uma rede de recuperação
3.  Replicar um controlador de domínio
4.  Replicar a camada de base de dados 
5.  Fazer uma ativação pós-falha de teste 
6.  Fazer uma ativação pós-falha 

Segue-se a recomendação para recuperação após desastre de cada escalão utilizado neste exemplo. 

 **Escalões SAP** | **Recomendação**
 --- | ---
**Agrupamento do SAP Web Dispatcher** |  Replicar com o Site recovery 
**Agrupamento de servidores de aplicação SAP** |  Replicar com o Site recovery 
**Cluster do SAP Central Services** |  Replicar com o Site recovery 
**Máquinas de virtuais do Active Directory** |  Replicação do Active Directory 
**Servidores de base de dados SQL** |  SQL sempre em replicação

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais

Para começar a replicar todas as SAP aplicação máquinas virtuais para o Centro de dados de recuperação após desastre do Azure, siga as orientações no [replicar uma máquina virtual para o Azure](azure-to-azure-walkthrough-enable-replication.md).


* Para obter orientações sobre como proteger o Active Directory e DNS, consulte [proteger o Active Directory e DNS](site-recovery-active-directory.md) documento.

* Para obter orientações sobre a proteção de camada de base de dados em execução no SQL server, consulte [proteger o SQL Server](site-recovery-active-directory.md) documento.

## <a name="networking-configuration"></a>Configuração de rede

Se utilizar um endereço IP estático, pode especificar o endereço IP que pretende que a máquina virtual a tomar. Para definir o endereço IP, aceda a **computação e as definições de rede** > **placa de interface de rede**.

![Captura de ecrã que mostra como definir um endereço IP privado no painel de cartão de interface de rede de recuperação de Site](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação
Um plano de recuperação suporta a sequenciação de vários escalões num aplicativo de várias camado durante uma ativação pós-falha. Sequenciamento ajuda a manter a consistência de aplicação. Quando cria um plano de recuperação para uma aplicação web de vários escalões, complete os passos descritos na [criar um plano de recuperação utilizando a recuperação de Site](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>A adicionar máquinas virtuais para grupos de ativação pós-falha

1.  Crie um plano de recuperação ao adicionar o servidor de aplicativos, o dispatcher de web e o SAP Central services VMs.
2.  Clique em "Personalizar" para agrupar as VMs. Por predefinição, todas as VMs são parte do 'Grupo 1'.



### <a name="add-scripts-to-the-recovery-plan"></a>Adicionar scripts para o plano de recuperação
Para as suas aplicações a funcionar corretamente, poderá ter de fazer algumas operações nas máquinas virtuais do Azure após a ativação pós-falha ou durante uma ativação pós-falha de teste. Pode automatizar algumas operações após a ativação pós-falha. Por exemplo, pode atualizar a entrada DNS e alterar as ligações e enlaces adicionando scripts correspondentes para o plano de recuperação.


Pode implementar os scripts mais utilizados do Azure Site Recovery para a sua conta de automatização, clicar no botão "Implementar para o Azure" abaixo. Quando estiver a utilizar qualquer script publicado, certifique-se de que siga as orientações no script.

[![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione um script de pré-ação para 'Grupo 1' ao grupo de disponibilidade de SQL de ativação pós-falha. Utilize o script de "ASR-SQL-FailoverAG" publicado nos scripts de exemplo. Certifique-se de que siga as orientações no script e faça as alterações necessárias no script adequadamente.
2. Adicionar um script de ação de postagem para anexar um balanceador de carga na sobre as máquinas virtuais de camada Web (grupo 1). Utilize o script de "ASR AddSingleLoadBalancer" publicado nos scripts de exemplo. Certifique-se de que siga as orientações no script e faça as alterações necessárias no script adequadamente.

![Plano de recuperação do SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

1.  No portal do Azure, selecione o Cofre de serviços de recuperação.
2.  Selecione o plano de recuperação que criou para aplicações SAP.
3.  Selecione **Ativação Pós-falha de Teste**.
4.  Para iniciar o processo de ativação pós-falha de teste, selecione o ponto de recuperação e a rede virtual do Azure.
5.  Quando o ambiente secundário está ativo, realizar validações.
6.  Quando validações forem concluídas, para limpar o ambiente de ativação pós-falha, selecione **ativação pós-falha de teste de limpeza**.

Para obter mais informações, consulte [ativação pós-falha de teste para o Azure no Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar uma ativação pós-falha

1.  No portal do Azure, selecione o Cofre de serviços de recuperação.
2.  Selecione o plano de recuperação que criou para aplicações SAP.
3.  Selecione **Ativação pós-falha**.
4.  Para iniciar o processo de ativação pós-falha, selecione o ponto de recuperação.

Para obter mais informações, consulte [ativação pós-falha no Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre a criação de uma solução de recuperação após desastre para implementações do SAP NetWeaver utilizando a recuperação de Site, consulte o white paper que pode ser baixado [SAP NetWeaver: Criar uma solução de recuperação após desastre com o Azure Site Recovery](https://aka.ms/asr-sap). O white paper aborda recomendações para várias arquiteturas SAP, apresenta uma lista de aplicativos suportados e tipos de VM para o SAP no Azure e descreve as opções do plano de teste para a sua solução de recuperação após desastre.
* Saiba mais sobre [replicar outras cargas de trabalho](site-recovery-workload.md) utilizando a recuperação de Site.
