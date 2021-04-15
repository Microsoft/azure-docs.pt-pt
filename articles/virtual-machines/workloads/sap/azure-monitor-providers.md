---
title: Monitor Azure para fornecedores de soluções SAP| Microsoft Docs
description: Este artigo fornece respostas a perguntas frequentes sobre o monitor Azure para fornecedores de soluções SAP.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 93e97f1f04aea2a31b62b2014a88a5aaa998ed2d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376091"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Monitor Azure para fornecedores de soluções SAP (pré-visualização)

## <a name="overview"></a>Descrição Geral  

No contexto do Azure Monitor for SAP Solutions, um *tipo de fornecedor* refere-se a um *fornecedor* específico. Por *exemplo, SAP HANA*, que é configurado para um componente específico dentro da paisagem SAP, como base de dados SAP HANA. Um fornecedor contém as informações de ligação para o componente correspondente e ajuda a recolher dados de telemetria a partir desse componente. Um recurso Azure Monitor for SAP Solutions (também conhecido como recurso de monitor SAP) pode ser configurado com vários fornecedores do mesmo tipo de fornecedor ou vários fornecedores de vários tipos de fornecedores.
   
Os clientes podem optar por configurar diferentes tipos de fornecedores para permitir a recolha de dados a partir da componente correspondente na sua paisagem SAP. Por exemplo, os clientes podem configurar um fornecedor para o tipo de fornecedor SAP HANA, outro fornecedor para o tipo de fornecedor de cluster de alta disponibilidade e assim por diante.  

Os clientes também podem optar por configurar vários fornecedores de um tipo de fornecedor específico para reutilizar o mesmo recurso de monitor SAP e grupo gerido associado. Incline-se mais sobre o grupo de recursos geridos. Para visualização pública, os seguintes tipos de fornecedores são suportados:   
- SAP HANA
- Cluster de elevada disponibilidade
- Microsoft SQL Server
- SAP NetWeaver

![Monitor Azure para fornecedores de soluções SAP](./media/azure-monitor-sap/azure-monitor-providers.png)

Recomenda-se aos clientes a configuração de pelo menos um fornecedor dos tipos de fornecedores disponíveis no momento da implantação do recurso SAP Monitor. Ao configurar um fornecedor, os clientes iniciam a recolha de dados a partir do componente correspondente para o qual o fornecedor está configurado.   

Se os clientes não configurarem nenhum fornecedor no momento da implantação do recurso do monitor SAP, embora o recurso do monitor SAP seja implementado com sucesso, não serão recolhidos dados de telemetria. Os clientes têm a opção de adicionar fornecedores após a implementação através do recurso monitor SAP dentro do portal Azure. Os clientes podem adicionar ou eliminar fornecedores do recurso do monitor SAP a qualquer momento.

> [!Tip]
> Se quiser que a Microsoft implemente um fornecedor específico, por favor deixe o feedback através do link no final deste documento ou contacte a sua equipa de conta.  

## <a name="provider-type-sap-hana"></a>Fornecedor tipo SAP HANA

Os clientes podem configurar um ou mais fornecedores do tipo de fornecedor *SAP HANA* para permitir a recolha de dados a partir da base de dados SAP HANA. O fornecedor SAP HANA conecta-se à base de dados SAP HANA sobre a porta SQL, retira dados de telemetria da base de dados e empurra-os para o espaço de trabalho Log Analytics na subscrição do cliente. O fornecedor SAP HANA recolhe dados a cada 1 minuto da base de dados SAP HANA.  

Na pré-visualização pública, os clientes podem esperar ver os seguintes dados com o fornecedor SAP HANA: Utilização subjacente à infraestrutura, estado do anfitrião SAP HANA, replicação do sistema SAP HANA e dados de telemetria de backup SAP HANA. Para configurar o fornecedor SAP HANA, o endereço IP anfitrião, o número de porta HANA SQL e o nome de utilizador e palavra-passe SYSTEMDB são necessários. Recomenda-se aos clientes que configurem o fornecedor SAP HANA contra o SYSTEMDB, no entanto, mais fornecedores podem ser configurados contra outros inquilinos da base de dados.

![Azure Monitor para fornecedores de soluções SAP - SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>Grupo de alta disponibilidade do tipo fornecedor
Os clientes podem configurar um ou mais fornecedores de cluster de *alta disponibilidade* do tipo fornecedor para permitir a recolha de dados do cluster Pacemaker dentro da paisagem SAP. O fornecedor de cluster de alta disponibilidade conecta-se ao Pacemaker, utilizando [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) ponto final, retira dados de telemetria da base de dados e empurra-os para o espaço de trabalho do Log Analytics na subscrição do cliente. O fornecedor de cluster de alta disponibilidade recolhe dados a cada 60 segundos da Pacemaker.  

Na pré-visualização pública, os clientes podem esperar ver os seguintes dados com o fornecedor de cluster de alta disponibilidade:   
 - Estatuto de cluster representado como roll-up do nó e do estado dos recursos 
 - [outros](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Monitor Azure para fornecedores de soluções SAP - Cluster de Alta Disponibilidade](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Para configurar um fornecedor de cluster de alta disponibilidade, estão envolvidos dois passos primários:

1. Instale [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) em *cada* nó dentro do cluster Pacemaker.

   Tem duas opções para instalar ha_cluster_exporter:
   
   - Utilize scripts Azure Automation para implementar um cluster de alta disponibilidade. Os scripts instalam [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) em cada nó de cluster.  
   - Faça uma [instalação manual.](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build) 

2. Configure um fornecedor de cluster de alta disponibilidade para *cada* nó dentro do cluster Pacemaker.

   Para configurar o fornecedor de clusters de alta disponibilidade, são necessárias as seguintes informações:
   
   - **Nome**. Um nome para este provedor. Deve ser único para este Azure Monitor para soluções SAP.
   - **Ponto Final Prometheus.** \: // \<servername or ip address\> http:9664/métricas.
   - **SID**. Para sistemas SAP, utilize o SAP SID. Para outros sistemas (por exemplo, clusters NFS), utilize um nome de três caracteres para o cluster. O SID deve ser distinto dos outros aglomerados que são monitorizados.   
   - **Nome do cluster**. O nome do cluster usado na criação do cluster. O nome do cluster pode ser encontrado na propriedade do `cluster-name` cluster.
   - **Nome de anfitrião.** O nome de anfitrião Linux do VM.  


## <a name="provider-type-os-linux"></a>Tipo de fornecedor OS (Linux)
Os clientes podem configurar um ou mais fornecedores do tipo OS (Linux) para permitir a recolha de dados a partir do Nó BareMetal ou VM. O fornecedor de OS (Linux) conecta-se a Nodes BareMetal ou VM, utilizando [Node_Exporter](https://github.com/prometheus/node_exporter)   ponto final, retira dados de telemetria dos Nós e empurra-o para o espaço de trabalho do Log Analytics na subscrição do cliente. O fornecedor de OS (Linux) recolhe dados a cada 60 segundos para a maioria das métricas dos Nó. 

Na pré-visualização do Público, os clientes podem esperar ver os seguintes dados com o fornecedor de OS (Linux): 
   - Utilização do CPU, Utilização do CPU por Processo 
   - Utilização do disco, I/O Ler & escrever 
   - Distribuição de memória, utilização da memória, utilização de memória de troca 
   - Utilização de rede, entrada de rede & detalhes de tráfego de saída. 

Para configurar um fornecedor de SISTEMA (Linux), estão envolvidos dois passos primários:
1. Instale [Node_Exporter](https://github.com/prometheus/node_exporter)   em cada nó BareMetal ou VM.
   Tem duas opções para instalar [Node_exporter:](https://github.com/prometheus/node_exporter) 
      - Para a instalação de automatização com utilização Ansible [Node_Exporter](https://github.com/prometheus/node_exporter) em cada Nó BareMetal ou VM para instalar o fornecedor OS (Linux).  
      - Faça uma [instalação manual.](https://prometheus.io/docs/guides/node-exporter/)

2. Configure um fornecedor de OS (Linux) para cada exemplo de nó BareMetal ou VM no seu ambiente. 
   Para configurar o fornecedor DES (Linux), são necessárias as seguintes informações: 
      - O nome. Um nome para este provedor. Deve ser único para este Azure Monitor para soluções SAP. 
      - Ponto final do exportador de nó. Normalmente http:// <servername or ip address> :9100/métricas 

> [!NOTE]
> 9100 é um Porto Exposto para Node_Exporter Endpoint.

> [!Warning]
> Certifique-se de que o Exportador de Nó continua a funcionar após o reinício do nó. 


## <a name="provider-type-microsoft-sql-server"></a>Tipo fornecedor servidor Microsoft SQL

Os clientes podem configurar um ou mais fornecedores de fornecedores do tipo *Microsoft SQL Server* para permitir a recolha de dados do [SQL Server em Máquinas Virtuais.](https://azure.microsoft.com/services/virtual-machines/sql-server/) O fornecedor de servidor SQL conecta-se ao Microsoft SQL Server sobre a porta SQL, retira dados de telemetria da base de dados e empurra-os para o espaço de trabalho do Log Analytics na subscrição do cliente. O SQL Server deve ser configurado para a autenticação SQL e deve ser criado um login do SQL Server, com o DB SAP como base de dados padrão para o fornecedor. O fornecedor sql Server recolhe dados entre cada 60 segundos até cada hora a partir do servidor SQL.  

Na pré-visualização pública, os clientes podem esperar ver os seguintes dados com o fornecedor SQL Server: utilização subjacente da infraestrutura, declarações de SQL de topo, tabela de topo, problemas registados nos registos de erro do SQL Server, processos de bloqueio e outros.  

Para configurar o fornecedor do Microsoft SQL Server, são necessários o ID do Sistema SAP, o endereço IP do anfitrião, o número da porta do Servidor SQL e o nome de login e palavra-passe do SQL Server.

![Azure Monitor para fornecedores de soluções SAP - SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-sap-netweaver"></a>Tipo de fornecedor SAP NetWeaver

Os clientes podem configurar um ou mais fornecedores do tipo de fornecedor SAP NetWeaver para permitir a recolha de dados a partir da camada SAP NetWeaver. O fornecedor AMS NetWeaver aproveita a interface [de webservice SAPControl](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html) existente para recuperar as informações de telemetria apropriadas.

Para a versão atual, abaixo estão os métodos web padrão fora de caixa soap invocados pela AMS.
|Método web|    ABAP|   JAVA|   Métricas|
|--|--|--|--|
|GetSystemInstanceList| X|  X|  Disponibilidade de exemplo, Servidor de Mensagens, Gateway, ICM, Disponibilidade ABAP|
|Obter Lista de Processamento|    X|  X|  Se a lista de casos for RED, podemos obter o que o processo faz com que o servidor seja VERMELHO|
|GetQueueStatistic| X|  X|  Estatísticas de fila (DIA/BATCH/UPD)|
|ABAPGetWPTable|    X|   -| Utilização do processo de trabalho|
|EnqGetStatistic|   X   |X  |Bloqueios|

Na pré-visualização pública, os clientes podem esperar ver os seguintes dados com o fornecedor SAP NetWeaver: 
- Disponibilidade de sistema e de instâncias
- Utilização do processo de trabalho
- Utilização de filas
- Estatísticas de bloqueio enqueue.

![image](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="next-steps"></a>Passos seguintes

- Crie o seu primeiro recurso Azure Monitor para soluções SAP.
- Tem alguma dúvida sobre o Azure Monitor para soluções SAP? Consulte a secção [de PERGUNTAS Frequentes](./azure-monitor-faq.md)
