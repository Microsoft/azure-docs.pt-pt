---
title: Quais as cargas de trabalho que podem ser protegidas com o Azure Site Recovery? | Microsoft Docs
description: Descreve as cargas de trabalho que podem ser protegidas com a recuperação após desastre, com o serviço do Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: raynew
ms.openlocfilehash: 6a0e07f338da039be1660e59510b24bcd44eed0c
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491745"
---
# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Quais as cargas de trabalho que podem ser protegidas com o Azure Site Recovery?

Este artigo descreve as cargas de trabalho e aplicações que pode proteger para recuperação após desastre com o [do Azure Site Recovery](site-recovery-overview.md) serviço.



## <a name="overview"></a>Descrição geral

As organizações necessitam de uma estratégia de continuidade empresarial e de recuperação após desastre (BCDR) para manter cargas de trabalho e dados seguros e disponíveis durante o período de indisponibilidade planeado e imprevisto, e como retomar as condições de trabalho normais com a maior brevidade possível.

A Recuperação de Sites é um serviço do Azure que contribui para a sua estratégia de BCDR. Através da Recuperação de Sites, pode implementar a replicação com reconhecimento de aplicações para a nuvem ou para um site secundário. Se as aplicações se baseiam em Windows ou Linux, e se executam em servidores físicos, VMware ou Hyper-V, pode utilizar a Recuperação de Sites para orquestrar a replicação, efetuar o teste de recuperação após desastres e executar a ativação e reativação pós-falha.

A Recuperação de Sites integra-se com aplicações da Microsoft, incluindo o SharePoint, Exchange, Dynamics, SQL Server e Active Directory. A Microsoft também trabalha diretamente com fornecedores importantes, incluindo a Oracle, a SAP e a Red Hat. Pode personalizar soluções de replicação nas aplicações de forma individual.

## <a name="why-use-site-recovery-for-application-replication"></a>Porquê utilizar a Recuperação de Sites para replicação de aplicações?

A Recuperação de Sites contribui para a proteção e recuperação a nível de aplicação da seguinte forma:

* Aplicação desconhecidas, fornecendo a replicação de quaisquer cargas de trabalho em execução num computador suportado.
* A replicação quase síncrona com os RPOs em apenas 30 segundos para satisfazer as necessidades das aplicações empresariais mais críticas.
* Instantâneos consistentes com aplicações para aplicações com uma ou múltiplas camadas.
* Integração com o AlwaysOn do SQL Server e parceria com outras tecnologias de replicação ao nível da aplicação, incluindo grupos de disponibilidade de base de dados de Exchange de replicação, o AlwaysOn de SQL, do AD (DAGs).
* Planos de recuperação flexíveis que lhe permitem recuperar toda uma pilha de aplicações com um único clique e incluem scripts externos e ações manuais no plano.
* A gestão de rede avançada na Recuperação de Sites e no Azure para simplificar os requisitos de rede de aplicação, incluindo a capacidade de reserva de endereços IP, configurar o balanceamento de carga e integração com o Gestor de Tráfego do Azure o que reduz as mudanças de rede de RTO.
* Uma biblioteca de Automatização do Azure completa que fornece scripts específicos de aplicação, prontos para produção, que podem ser transferidos e integrados com os planos de recuperação.

## <a name="workload-summary"></a>Resumo da carga de trabalho
A Recuperação de Sites pode replicar qualquer aplicação em execução numa máquina. Além disso, fizemos parcerias com equipas de produto para executar testes adicionais específicos da aplicação.

| **Carga de trabalho** |**Replicar VMs do Azure para o Azure** |**Replicar VMs Hyper-V para um site secundário** | **Replicar VMs Hyper-V para o Azure** | **Replicar VMs VMware para um site secundário** | **Replicar VMs VMware para o Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |S |S |S |S |S|
| Web Apps (IIS, SQL) |S |S |S |S |S|
| System Center Operations Manager |S |S |S |S |S|
| SharePoint |S |S |S |S |S|
| SAP<br/><br/>Replicar site SAP para o Azure de não cluster |S (testado pela Microsoft) |S (testado pela Microsoft) |S (testado pela Microsoft) |S (testado pela Microsoft) |S (testado pela Microsoft)|
| Exchange (não DAG) |S |S |S |S |S|
| Ambiente de Trabalho Remoto/VDI |S |S |S |S |S|
| Linux (sistema operativo e aplicações) |S (testado pela Microsoft) |S (testado pela Microsoft) |S (testado pela Microsoft) |S (testado pela Microsoft) |S (testado pela Microsoft)|
| Dynamics AX |S |S |S |S |S|
| Servidor de Ficheiros do Windows |S |S |S |S |S|
| Citrix XenApp e XenDesktop |S|N/A |S |N/A |S |

## <a name="replicate-active-directory-and-dns"></a>Replicar o Active Directory e o DNS
As infraestruturas de DNS e Active Directory são essenciais para a maioria das aplicações da empresa. Durante a recuperação após desastre terá de proteger e recuperar estes componentes da infraestrutura antes de recuperar as cargas de trabalho e aplicações.

Pode utilizar a Recuperação de Sites para criar um plano de recuperação após desastre automatizado completa para Active Directory e DNS. Para o exemplo, se pretende efetuar a ativação pós-falha do SharePoint e SAP a partir de um site principal para um site secundário, pode configurar um plano de recuperação com ativação pós-falha, primeiro, do Active Directory e, depois, um plano de recuperação para cada aplicação adicional específico da aplicação para a ativação pós-falha das outras aplicações que dependem do Active Directory.

[Saiba mais](site-recovery-active-directory.md) sobre como proteger o Active Directory e DNS.

## <a name="protect-sql-server"></a>Proteger o SQL Server
O SQL Server proporciona uma base de serviços de dados para os serviços de dados de muitas aplicações empresariais num datacenter no local.  A Recuperação de Sites pode ser utilizado em conjunto com as tecnologias de SQL Server HA/DR, para proteger aplicações empresariais de várias camadas que utilizam o SQL Server. A Recuperação de Sites proporciona:

* Uma solução de recuperação após desastre simples e rentável para o SQL Server. Replique várias versões e edições de servidores independentes e clusters do SQL Server, para o Azure ou para um site secundário.  
* Integração com grupos de disponibilidade AlwaysOn de SQL Server, para gerir a ativação e a reativação pós-falha com planos de recuperação do Azure Site Recovery.
* Planos de recuperação completos para todas as camadas numa aplicação, incluindo as bases de dados do SQL Server.
* Dimensionamento do SQL Server para cargas máximas com a Recuperação de Sites “disparando-as” em tamanhos de máquina virtual IaaS maiores no Azure.
* Testes fáceis de recuperação após desastres do SQL Server. Pode executar as ativações pós-falha de teste para analisar os dados e executar verificações de compatibilidade, sem afetar o seu ambiente de produção.

[Saiba mais](site-recovery-sql.md) sobre como proteger o SQL server.

## <a name="protect-sharepoint"></a>Proteger o SharePoint
O Azure Site Recovery ajuda a proteger as implementações do SharePoint da seguinte forma:

* Elimina a necessidade e os custos da infraestrutura associada a um farm autónomo na recuperação após desastre. Utilize a Recuperação de Sites para replicar todo o farm (camadas da Web, da aplicação e da base de dados) para o Azure ou para um site secundário.
* Simplifica a gestão e implementação das aplicações. As atualizações implementadas no site primário replicam-se automaticamente e, por conseguinte, estão disponíveis depois da ativação e recuperação pós-falha de um farm de um site secundário. Também reduz a complexidade da gestão e os custos associados com o facto de manter atualizado um farm em espera.
* Simplifica o desenvolvimento e o teste de aplicações de SharePoint através da criação de um ambiente de réplica de cópia a pedido do tipo produção para realizar testes e depuração.
* Simplifica a transição para a nuvem utilizando a Recuperação de Sites para migrar as implementações do SharePoint para o Azure.

[Saiba mais](site-recovery-sharepoint.md) sobre como proteger o SharePoint.

## <a name="protect-dynamics-ax"></a>Proteger Dynamics AX
O Azure Site Recovery ajuda a proteger a sua solução ERP de Dynamics AX:

* Organização da replicação do ambiente completo do Dynamics AX (camadas Web e de AOS, camadas de base de dados e SharePoint) para o Azure ou para um site secundário.
* Simplificação da migração de implementações de Dynamics AX para a nuvem (Azure).
* Simplificação do desenvolvimento e teste de aplicações do Dynamics AX através de uma cópia do tipo produção a pedido para teste e depuração.

[Saiba mais](site-recovery-dynamicsax.md) sobre como proteger o Dynamic AX.

## <a name="protect-rds"></a>Proteger Serviços de Ambiente de Trabalho Remoto
Os Serviços de Ambiente de Trabalho Remoto (RDS) ativa a infraestrutura de ambiente de trabalho virtual (VDI), ambientes de trabalho baseados em sessão e aplicações, permitindo aos utilizadores trabalhar em qualquer lugar. Com o Azure Site Recovery, pode:

* Replicar ambientes de trabalho virtuais agrupados, geridos ou não geridos, para um site secundário, e aplicações remotas e sessões para um site secundário ou Azure.

* Eis o que é possível replicar:

| **RDS** |**Replicar VMs do Azure para o Azure** | **Replicar VMs Hyper-V para um site secundário** | **Replicar VMs Hyper-V para o Azure** | **Replicar VMs VMware para um site secundário** | **Replicar VMs VMware para o Azure** | **Replicar servidores físicos para um site secundário** | **Replicar servidores físicos para o Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Ambiente de Trabalho Virtual Agrupado (não gerido)** |Não|Sim |Não |Sim |Não |Sim |Não |
| **Ambiente de Trabalho Virtual Agrupado (gerido e sem UDP)** |Não|Sim |Não |Sim |Não |Sim |Não |
| **Aplicações remotas e sessões de Ambiente de Trabalho (sem UDP)** |Sim|Sim |Sim |Sim |Sim |Sim |Sim |

[Configurar a recuperação após desastres para o RDS utilizando o Azure Site Recovery](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure).

[Saiba mais](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sobre como proteger o RDS.

## <a name="protect-exchange"></a>Proteger o Exchange
A Recuperação de Sites ajuda a proteger o Exchange da seguinte forma:

* Em pequenas implementações do Exchange, como servidores únicos ou autónomos, a Recuperação de Sites pode replicar e efetuar a ativação pós-falha para o Azure ou para um site secundário.
* Em grandes implementações, a Recuperação de Sites integra-se com os DAGs do Exchange.
* Os DAGs do Exchange são a solução recomendada para a recuperação após desastre do Exchange de uma empresa.  Os planos de recuperação da o Recuperação de Sites podem incluir DAGs para coordenar a ativação pós-falha do DAG entre sites.

[Saiba mais](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) sobre como proteger o Exchange.

## <a name="protect-sap"></a>Proteger o SAP
Utilize a Recuperação de Sites para proteger a sua implementação de SAP da seguinte forma:

* Ative a proteção das aplicações SAP NetWeaver e não NetWeaver Production em execução no local, através da replicação de componentes para o Azure.
* Ative a proteção das aplicações SAP NetWeaver e não NetWeaver Production em execução no Azure, através da replicação de componentes para outro datacenter do Azure.
* Simplificar a migração para a nuvem utilizando a Recuperação de Sites para migrar a implementação do SAP para o Azure.
* Simplifique as atualizações, testes e protótipos de projetos SAP ao criar um clone de produção a pedido para testar aplicações SAP.

[Saiba mais](site-recovery-sap.md) sobre como proteger o SAP.

## <a name="protect-iis"></a>Proteger o IIS
Utilize o Site Recovery para proteger a sua implementação de IIS da seguinte forma:

O Azure Site Recovery fornece a recuperação após desastre ao replicar os componentes críticos no seu ambiente para um site remoto sem interesse ou para uma cloud pública, como o Microsoft Azure. Uma vez que as máquinas virtuais com o servidor Web e a base de dados estão a ser replicados para o site de recuperação, não é necessária a cópia de segurança de ficheiros de configuração ou certificados em separado. Os mapeamentos de aplicação e enlaces dependentes em variáveis de ambiente que são alterados após a ativação pós-falha podem ser atualizados através de scripts integrados em planos de recuperação após desastre. As máquinas virtuais são colocadas no site de recuperação em caso de uma ativação pós-falha. Não só isto, o Azure Site Recovery também ajuda a orquestrar a ativação pós-falha de ponta a ponta ao fornecer-lhe as seguintes funcionalidades:

-   Sequenciação do encerramento e arranque das máquinas virtuais nas várias camadas.
-   Adição de scripts para permitir a atualização de dependências de aplicações e enlaces nas máquinas virtuais após terem sido iniciadas. Os scripts também podem ser utilizados para atualizar o servidor DNS que aponta para o site de recuperação.
-   Alocar endereços IP a máquinas virtuais de pré-ativação pós-falha ao mapear as redes principais e de recuperação e, consequentemente, utilizar scripts que não precisam de ser atualizados após a ativação pós-falha.
-   Capacidade de uma ativação pós-falha com um clique para várias aplicações Web nos servidores Web, eliminando, por conseguinte, o âmbito de confusão em caso de desastre.
-   Capacidade para testar os planos de recuperação num ambiente isolado para testes de recuperação após desastre.

[Saiba mais](https://aka.ms/asr-iis) sobre como proteger o Web farm IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Proteger o Citrix XenApp e o XenDesktop
Utilize o Site Recovery para proteger as suas implementações do Citrix XenApp e do XenDesktop deployments, da seguinte forma:

* Ative a proteção das implementações do Citrix XenApp e do XenDesktop ao replicar camadas de implementação diferentes (incluindo servidor DNS do AD, servidor da bases de dados SQL, Citrix Delivery Controller, servidor StoreFront, XenApp Master (VDA), Citrix XenApp License Server) para o Azure.
* Utilize o Site Recovery para migrar as suas implementações do Citrix XenApp e do XenDesktop para o Azure, de modo a simplificar a migração para a cloud.
* Crie uma cópia de tipo de produção e a pedido para testes e depuração, para simplificar os testes de Citrix XenApp/XenDesktop.
* Esta solução só é aplicável a ambientes de trabalho virtuais do sistema operativo do Windows Server e não a ambientes de trabalho virtuais cliente, uma vez que estes ainda não são suportados para licenciamento no Azure.
[Saiba mais](https://azure.microsoft.com/pricing/licensing-faq/) sobre o licenciamento para ambientes de trabalho de cliente/servidor no Azure.

[Saiba mais](site-recovery-citrix-xenapp-and-xendesktop.md) sobre como proteger implementações do Citrix XenApp e do XenDesktop. Como alternativa, pode fazer referência ao [documento técnico da Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr) com os mesmos detalhes.

## <a name="next-steps"></a>Passos Seguintes

[Introdução](azure-to-azure-quickstart.md) à replicação de VMs do Azure.
