---
title: Azure Monitor para soluções SAP e arquitetura| Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes sobre monitor Azure para soluções SAP
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 1c33011d947d6dc9dd9ee4dd6331c24c06d99b38
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693829"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Monitor Azure para soluções SAP (pré-visualização)

## <a name="overview"></a>Descrição geral

O Azure Monitor for SAP Solutions é um produto de monitorização nativo da Azure para os clientes, que gere as suas paisagens SAP em Azure. O produto funciona com tanto [o SAP em Azure Virtual Machines](./hana-get-started.md) [como com o SAP em Azure Large Instances](./hana-overview-architecture.md).
Com o Azure Monitor for SAP Solutions, os clientes podem recolher dados de telemetria a partir de infraestruturas e bases de dados Azure numa localização central e correlacionar visualmente os dados de telemetria para uma resolução mais rápida de problemas.

O Azure Monitor for SAP Solutions é oferecido através do Azure Marketplace. Proporciona uma experiência de configuração simples e intuitiva e leva apenas alguns cliques para implementar o recurso para O Azure Monitor para soluções SAP (conhecido como **recurso de monitor SAP).**

Os clientes podem monitorizar diferentes componentes de uma paisagem SAP, como máquinas virtuais Azure, cluster de alta disponibilidade, base de dados SAP HANA, e assim por diante, adicionando o **fornecedor** correspondente para esse componente.

Infraestrutura apoiada:

- Máquina Virtual do Azure
- Azure Grande Instância

Bases de dados suportadas:
- Base de Dados do SAP HANA
- Servidor Microsoft SQL

O Azure Monitor for SAP Solutions utiliza o poder das capacidades existentes do [Azure Monitor,](../../../azure-monitor/overview.md) tais como Log Analytics e [Workbooks](../../../azure-monitor/platform/workbooks-overview.md) para fornecer mais capacidades de monitorização. Os clientes podem criar [visualizações personalizadas](../../../azure-monitor/platform/workbooks-overview.md#getting-started) editando os livros de trabalho predefinidos fornecidos pelo Azure Monitor para soluções SAP, escrever [consultas personalizadas](../../../azure-monitor/log-query/log-analytics-tutorial.md) e criar [alertas personalizados](../../../azure-monitor/learn/tutorial-response.md) utilizando o espaço de trabalho do Azure Log Analytics, aproveitar o período de [retenção flexível](../../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) e ligar dados de monitorização com o seu sistema de bilhética.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Que dados o Azure Monitor para soluções SAP recolhe?

A recolha de dados no Azure Monitor para soluções SAP depende dos fornecedores configurados pelos clientes. Durante a Visualização Pública, estão a ser recolhidos os seguintes dados.

Telemetria de cluster Pacemaker de alta disponibilidade:
- Nó, recurso e estado do dispositivo SBD
- Restrições de localização do pacemaker
- Votos de quórum e estatuto de anel
- [Outros](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

Telemetria SAP HANA:
- CPU, memória, disco e utilização da rede
- Replicação do sistema HANA (HSR)
- Apoio hana
- Estatuto de anfitrião HANA
- Servidor de índice e funções de servidor de nome

Telemetria do servidor Microsoft SQL:
- CPU, memória, utilização do disco
- Nome de anfitrião, nome sql instance, ID do sistema SAP
- Pedidos de lote, compilações e página esperança de vida ao longo do tempo
- Top 10 declarações SQL mais caras ao longo do tempo
- Top 12 maior tabela no sistema SAP
- Problemas registados no registo de erro do servidor SQL
- Processos de bloqueio e estatísticas de espera SQL ao longo do tempo

Sistema operativo Telemetria (Linux) 
- Utilização do CPU, contagem do garfo, processos em execução e bloqueio. 
- Utilização da memória e distribuição entre os utilizados, em cache, tamponados. 
- Troque a utilização, o paging e a taxa de swap. 
- Utilização de sistemas de ficheiros, Número de bytes lidos e escritos por dispositivo de bloco. 
- Leia/escreva latência por dispositivo de bloco. 
- Contagem contínua de E/O, Memória Persistente ler/escrever bytes. 
- Pacotes de rede dentro/fora, Bytes de rede dentro/fora 

## <a name="data-sharing-with-microsoft"></a>Partilha de dados com a Microsoft

O Azure Monitor for SAP Solutions recolhe metadados do sistema para fornecer um melhor suporte aos nossos clientes SAP em Azure. Não é recolhido NENHUM PII/EUII.
Os clientes podem permitir a partilha de dados com a Microsoft no momento de criar o recurso Azure Monitor para soluções SAP, escolhendo *o Share* a partir da queda.
Recomenda-se vivamente que os clientes permitam a partilha de dados, uma vez que dá às equipas de suporte e engenharia da Microsoft mais informações sobre o ambiente do cliente e fornece um melhor suporte à nossa sap crítica de missão aos clientes Azure.

## <a name="architecture-overview"></a>Descrição geral da arquitetura

A um nível elevado, o diagrama seguinte explica como o Azure Monitor for SAP Solutions recolhe telemetria a partir da base de dados SAP HANA. A arquitetura é agnóstica para se o SAP HANA é implantado em Azure Virtual Machines ou Azure Large Instances.

![Azure Monitor para arquitetura de soluções SAP](./media/azure-monitor-sap/azure-monitor-architecture.png)

Os componentes-chave da arquitetura são:
- Portal Azure – o ponto de partida para os clientes. Os clientes podem navegar para o mercado dentro do portal Azure e descobrir o Azure Monitor para soluções SAP
- Azure Monitor for SAP Solutions recurso – um local de aterragem para os clientes verem a telemetria de monitorização
- Grupo de recursos geridos – implantado automaticamente como parte do Azure Monitor para implementação de recursos SAP Solutions. Os recursos mobilizados no âmbito da ajuda do grupo de recursos geridos na recolha de telemetria. Os recursos-chave utilizados e o seu propósito são:
   - Máquina Virtual Azure: Também conhecida como *Colecionador VM*. Este é um VM Standard_B2ms. O principal objetivo deste VM é acolher a *carga de monitorização.* A carga útil de monitorização refere-se à lógica de recolher a telemetria dos sistemas de origem e transferir os dados recolhidos para o quadro de monitorização. No diagrama acima, a carga útil de monitorização contém a lógica de ligação à base de dados SAP HANA sobre a porta SQL.
   - [Cofre chave Azure](../../../key-vault/general/basic-concepts.md): Este recurso é implantado para deter de forma segura as credenciais de base de dados SAP HANA e para armazenar informações sobre [fornecedores](./azure-monitor-providers.md).
   - Log Analytics Workspace: o destino onde residem os dados da telemetria.
      - A visualização é construída em cima da telemetria em Log Analytics usando [livros de trabalho Azure](../../../azure-monitor/platform/workbooks-overview.md). Os clientes podem personalizar a visualização. Os clientes também podem fixar os seus Livros de Trabalho ou visualização específica dentro de Livros de Trabalho para o painel Azure para capacidade de autorefresh com menor granularidade de 30 minutos.
      - Os clientes podem utilizar o seu espaço de trabalho existente dentro da mesma subscrição que o recurso monitor SAP, escolhendo esta opção no momento da implementação.
      - Os clientes podem usar a linguagem de consulta Kusto (KQL) para executar [consultas](../../../azure-monitor/log-query/log-query-overview.md) contra as tabelas cruas dentro do espaço de trabalho log analytics. Veja os *Registos Personalizados.*

> [!Note]
> Os clientes são responsáveis pela remendação e manutenção do VM, implantado no grupo de recursos geridos.

> [!Tip]
> Os clientes podem optar por utilizar um espaço de trabalho log analytics existente para a recolha de telemetria, se for implantado dentro da mesma subscrição Azure que o recurso para o Azure Monitor para soluções SAP.

### <a name="architecture-highlights"></a>Destaques de Arquitetura

Seguem-se os principais destaques da arquitetura:
 - **Multi-instância** - Os clientes podem criar monitores para múltiplas instâncias de um determinado tipo de componente (por exemplo, HANA DB, cluster HA, servidor Microsoft SQL) através de vários SIDs SAP dentro de um VNET com um único recurso de Azure Monitor para soluções SAP.
 - **Multi-fornecedor** - O diagrama de arquitetura acima mostra o provedor SAP HANA como um exemplo. Da mesma forma, os clientes podem configurar mais fornecedores para componentes correspondentes (por exemplo, HANA DB, cluster HA, servidor Microsoft SQL) para recolher dados desses componentes.
 - **Fonte aberta** - O código fonte do Azure Monitor para soluções SAP está disponível no [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions). Os clientes podem consultar o código do fornecedor e saber mais sobre o produto, contribuir ou partilhar feedback.
 - **Estrutura de consulta extensível** - As consultas SQL para recolher dados de telemetria são escritas em [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json). Mais consultas SQL para recolher mais dados de telemetria podem ser facilmente adicionados. Os clientes podem solicitar dados específicos de telemetria a serem adicionados ao Azure Monitor para soluções SAP, deixando feedback através de link no final deste documento ou contactando a sua equipa de conta.

## <a name="pricing"></a>Preços
O Azure Monitor for SAP Solutions é um produto gratuito (sem taxa de licença). Os clientes são responsáveis pelo pagamento do custo dos componentes subjacentes no grupo de recursos geridos.

## <a name="next-steps"></a>Passos seguintes

Conheça os fornecedores e crie o seu primeiro recurso Azure Monitor para soluções SAP.
 - Saiba mais sobre [Fornecedores](./azure-monitor-providers.md)
 - [Implementar monitor de Azure para soluções SAP com Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Tem alguma dúvida sobre o Azure Monitor para soluções SAP? Consulte a secção [de PERGUNTAS Frequentes](./azure-monitor-faq.md)
