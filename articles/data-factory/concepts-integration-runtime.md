---
title: Integration runtime no Azure Data Factory
description: Saiba mais sobre integration runtime no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: abnarain
ms.openlocfilehash: 0b137edbfb5ca439d4ba15614225ec0973511763
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218803"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration runtime no Azure Data Factory
O Integration Runtime (IR) é a infraestrutura de computação que o Azure Data Factory utiliza para proporcionar as seguintes capacidades de integração de dados em diferentes ambientes de rede:

- **Data Flow**: Execute a [Data Flow](concepts-data-flow-overview.md) in managed Azure compute environment.  
- **Data movement**: Copy data across data stores in public network and data stores in private network (on-premises or virtual private network). Oferece suporte para conectores incorporados, conversão de formatos, mapeamento de colunas e transferência de dados dimensionável e de desempenho elevado.
- **Activity dispatch**:  Dispatch and monitor transformation activities running on a variety of compute services such as Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server, and more.
- **Execução de pacotes do SSIS**: executar, nativamente, pacotes do SQL Server Integration Services (SSIS) num ambiente de computação gerida do Azure.

No Data Factory, as atividades definem as ações que vão ser realizadas. Os serviços ligados definem um arquivo de dados ou um serviço de computação de destino. Os runtimes de integração estabelecem a ponte entre a atividade e os serviços ligados.  It is referenced by the linked service or activity, and provides the compute environment where the activity either runs on or gets dispatched from. Desta forma, a atividade pode ser realizada na região mais perto possível do arquivo de dados ou do serviço de computação de destino com o melhor desempenho possível, satisfazendo as necessidades de segurança e conformidade.

## <a name="integration-runtime-types"></a>Tipos de runtimes de integração
O Data Factory oferece três tipos de runtimes de integração e deve escolher aquele que melhor se adequa às capacidades de integração de dados e às necessidades de ambiente de rede de que está à procura.  Estes três tipos são:

- Azure
- Autoalojado
- Azure-SSIS

A tabela seguinte descreve as capacidades e o suporte de rede para cada um dos tipos de runtimes de integração:

Tipo de IR | Rede pública | Rede privada
------- | -------------- | ---------------
Azure | Fluxo de Dados<br/>Movimento de dados<br/>Distribuição de atividades | &nbsp;
Autoalojado | Movimento de dados<br/>Distribuição de atividades | Movimento de dados<br/>Distribuição de atividades
Azure-SSIS | Execução de pacotes do SSIS | Execução de pacotes do SSIS

O diagrama seguinte mostra como os diferentes runtimes de integração podem ser utilizados em combinação para oferecer suporte de rede e capacidades de integração de dados avançadas:

![Diferentes tipos de runtimes de integração](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Runtime de integração do Azure
Os runtimes de integração do Azure podem:

- Running Data Flows in Azure 
- Executar atividades de cópia entre arquivos de dados na cloud
- Dispatching the following transform activities in public network: Databricks Notebook/ Jar/ Python activity, HDInsight Hive activity, HDInsight Pig activity, HDInsight MapReduce activity, HDInsight Spark activity, HDInsight Streaming activity, Machine Learning Batch Execution activity, Machine Learning Update Resource activities, Stored Procedure activity, Data Lake Analytics U-SQL activity, .NET custom activity, Web activity, Lookup activity, and Get Metadata activity.

### <a name="azure-ir-network-environment"></a>Ambiente de rede de IR do Azure
Azure Integration Runtime supports connecting to data stores and compute services with public accessible endpoints. Utilize um runtime de integração autoalojado para o ambiente Rede Virtual do Azure.

### <a name="azure-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure
Os runtimes de integração do Azure fornecem uma computação totalmente gerida e sem servidor no Azure.  Não tem de se preocupar com o aprovisionamento da infraestrutura, a instalação de software, as correções ou o dimensionamento de capacidades.  Além disso, apenas paga durante a utilização efetiva.

Os runtimes de integração do Azure proporcionam a computação nativa para mover dados entre arquivos de dados na cloud de forma segura, fiável e de elevado desempenho.  Pode definir o número de unidades de integração de dados a utilizar na atividade de cópia e o tamanho da computação do IR do Azure é aumentado verticalmente de forma elástica em conformidade, sem que tenha de ajustar explicitamente o tamanho do Runtime de Integração do Azure. 

A distribuição de atividades é uma operação simples para encaminhar a atividade para o serviço de computação de destino, para que não seja necessário aumentar verticalmente o tamanho da computação neste cenário.

Para obter informações sobre como criar e configurar um runtime de integração do Azure, veja How to create and configure Azure IR (Como criar e configurar um runtime de integração do Azure) nos guias de procedimentos. 

> [!NOTE] 
> Azure Integration runtime has properties related to Data Flow runtime, which defines the underlying compute infrastructure that would be used to run the data flows on. 

## <a name="self-hosted-integration-runtime"></a>Runtime de integração autoalojado
Os runtimes de integração autoalojados podem:

- Executar a atividade de cópia entre arquivos de dados na cloud e um arquivo de dados numa rede privada.
- Dispatching the following transform activities against compute resources in on-premises or Azure Virtual Network: HDInsight Hive activity (BYOC-Bring Your Own Cluster), HDInsight Pig activity (BYOC), HDInsight MapReduce activity (BYOC), HDInsight Spark activity (BYOC), HDInsight Streaming activity (BYOC), Machine Learning Batch Execution activity, Machine Learning Update Resource activities, Stored Procedure activity, Data Lake Analytics U-SQL activity, Custom activity (runs on Azure Batch), Lookup activity, and Get Metadata activity.

> [!NOTE] 
> Use self-hosted integration runtime to support data stores that requires bring-your-own driver such as SAP Hana, MySQL, etc.  For more information, see [supported data stores](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) is a dependency of Self Hosted IR. Please make sure you have JRE installed on the same host.

### <a name="self-hosted-ir-network-environment"></a>Ambiente de rede de IR autoalojado
Se quiser efetuar a integração de dados de forma segura num ambiente de rede privada, que não tem um campo de visão a partir do ambiente de rede pública, pode instalar um IR autoalojado no ambiente no local atrás da firewall emprsarial ou dentro de uma rede privada virtual.  O runtime de integração autoalojado só faz ligações de saída baseadas em HTTP para a Internet aberta.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR autoalojado
O IR autoalojado tem de ser instalado num computador no local ou numa máquina virtual que esteja numa rede privada. Atualmente, só suportamos a execução dos runtimes de integração autoalojados em sistemas operativos Windows.  

Para elevada disponibilidade e escalabilidade, pode aumentar horizontalmente o IR autoalojado ao associar a instância lógica a vários computadores no local no modo ativo-ativo.  For more information, see how to [create and configure self-hosted IR](create-self-hosted-integration-runtime.md) article under how to guides for details.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime
Para fazer a migração lift and shift de cargas de trabalho do SSIS existentes, pode criar um runtime de integração Azure-SSIS para executar nativamente pacotes do SSIS.

### <a name="azure-ssis-ir-network-environment"></a>Ambiente de rede de IR do Azure-SSIS
O runtime de integração Azure-SSIS pode ser aprovisionado na rede pública ou numa rede privada.  O acesso aos dados no local é suportado mediante a associação do runtime de integração Azure-SSIS a uma Rede Virtual que esteja ligada à sua rede no local.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure-SSIS
O runtime de integração Azure-SSIS é um cluster totalmente gerido de VMs do Azure dedicadas à execução dos pacotes do SSIS. You can bring your own Azure SQL Database or Managed Instance server to host the catalog of SSIS projects/packages (SSISDB) that is going to be attached to it. Pode aumentar verticalmente o poder da computação ao especificar o tamanho do nó e aumentá-lo horizontalmente ao definir o número de nós no cluster. Pode gerir o custo da execução do Azure-SSIS Integration Runtime ao pará-lo e iniciá-lo, conforme achar mais adequado.

Para obter mais informações, veja o artigo “how to create and configure Azure-SSIS IR” (“Como criar e configurar o runtime de integração Azure-SSIS”) nos guias de procedimentos.  Depois de criado, pode implementar e gerir os seus pacotes do SSIS com poucas ou nenhumas alterações através de ferramentas familiares, como o SQL Server Data Tools (SSDT) e o SQL Server Management Studio (SSMS), tal e qual como utilizaria o SSIS no local.

Para obter mais informações sobre o runtime Azure-SSIS, veja os artigos seguintes: 

- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo disponibiliza instruções passo a passo para criar um IR Azure-SSIS e utiliza uma base de dados da Base de Dados SQL do Azure para alojar o catálogo do SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). This article expands on the tutorial and provides instructions on using Azure SQL Database Managed Instance and joining the IR to a virtual network. 
- [Monitorizar um Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentá-lo horizontalmente mediante a adição de mais nós ao mesmo. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure. Também descreve os passos para utilizar o portal do Azure para configurar uma rede virtual, de modo a que o IR Azure-SSIS se possa associar à mesma. 

## <a name="integration-runtime-location"></a>Localização do runtime de integração
É na localização do Data Factory que os metadados da fábrica de dados são armazenados e a partir de onde o acionamento do pipeline é iniciado. Entretanto, uma fábrica de dados pode aceder aos arquivos de dados e serviços de computação noutras regiões do Azure para mover dados entre os arquivos de dados ou processar dados com serviços de computação. Este comportamento é realizado através do [IR globalmente disponível](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade dos dados, a eficiência e custos de saída de rede reduzidos.

A localização do runtime de integração define a localização da respetiva computação de back-end e, essencialmente, a localização onde são realizados o movimento de dados, a distribuição de atividades e a execução de pacotes do SSIS. A localização do runtime de integração pode ser diferente da localização da fábrica de dados à qual pertence. 

### <a name="azure-ir-location"></a>Localização do IR do Azure
Pode definir uma determinada localização de um IR do Azure, em cujo caso o movimento de dados ou emissão de atividade ocorrem nessa região específica. 

If you choose to use the **auto-resolve Azure IR** which is the default, 

- Para a atividade de cópia, o ADF vai fazer o melhor esforço para detetar automaticamente o seu sink e arquivo de dados de origem para escolher a melhor localização na mesma região, se disponível, ou a mais próxima na mesma geografia; ou se não for detetável, utilizar a mesma região de fábrica de dados como alternativa.

- For Lookup/GetMetadata/Delete activity execution (also known as Pipeline activities), transformation activity dispatching (also known as External activities), and authoring operations (test connection, browse folder list and table list, preview data), ADF will use the IR in the data factory region.

- For Data Flow, ADF will use the IR in the data factory region. 

  > [!TIP] 
  > A good practice would be to ensure Data flow runs in the same region as your corresponding data stores (if possible). You can either achieve this by auto-resolve Azure IR (if data store location is same as Data Factory location), or by creating a new Azure IR instance in the same region as your data stores and then execute the data flow on it. 

Pode monitorizar que localização de IR entra em efeito durante a execução da atividade na vista de monitorização da atividade do pipeline no payload de monitorização de atividade ou na IU.

>[!TIP]
>Se tiver requisitos de conformidade de dados estritos e precisar de garantir que os dados não saem de uma detemrinada geografia, pode criar explicitamente um IR do Azure numa determinada região e apontar o Serviço Ligado a esse RI ao utilizar a propriedade ConnectVia. Por exemplo, se quiser copiar dados do Blob no Sul do Reino Unido para o SQL DW no Sul do Reino Unido e quiser garantir que os dados não saem do Reino Unido, crie um IR do Azure no Sul do Reino Unido e ligue ambos os Serviços Ligados a este IR.

### <a name="self-hosted-ir-location"></a>Localização do IR autoalojado
O IR autoalojado é registado logicamente no Data Factory e a computação utilizada para suportar as funcionalidades do mesmo é fornecida por si. Por esse motivo, não existe uma propriedade de localização explícita para o runtime de integração autoalojado. 

Quando é utilizado para realizar o movimento de dados, o IR autoalojado extrai dados da origem e escreve-os no destino.

### <a name="azure-ssis-ir-location"></a>Localização do IR do Azure-SSIS
Selecionar a localização certa para o runtime de integração Azure-SSIS é fundamental para obter um elevado desempenho nos seus fluxos de trabalho extract-transform-load (ETL).

- The location of your Azure-SSIS IR does not need be the same as the location of your data factory, but it should be the same as the location of your own Azure SQL Database/Managed Instance server where SSISDB is to be hosted. Desta forma, o Azure-SSIS Integration Runtime pode aceder facilmente ao SSISDB sem incorrer em tráfegos excessivos entre diferentes localizações.
- If you do not have an existing Azure SQL Database/Managed Instance server to host SSISDB, but you have on-premises data sources/destinations, you should create a new Azure SQL Database/Managed Instance server in the same location of a virtual network connected to your on-premises network.  This way, you can create your Azure-SSIS IR using the new Azure SQL Database/Managed Instance server and joining that virtual network, all in the same location, effectively minimizing data movements across different locations.
- If the location of your existing Azure SQL Database/Managed Instance server where SSISDB is hosted is not the same as the location of a virtual network connected to your on-premises network, first create your Azure-SSIS IR using an existing Azure SQL Database/Managed Instance server and joining another virtual network in the same location, and then configure a virtual network to virtual network connection between different locations.

O diagrama seguinte mostra as definições de localização do Data Factory e os respetivos runtimes de integração:

![Localização do runtime de integração](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Determinar o runtime de integração a utilizar

### <a name="copy-activity"></a>Atividade Copiar

Para a atividade Cópia, precisa que os serviços ligados de origem e sink definam a direção do fluxo de dados. É utilizada a lógica seguinte para determinar que instância do runtime de integração é utilizada para fazer a cópia: 

- **Copiar entre duas origens de dados na cloud**: quando ambos os serviços ligados de origem e de sink estiverem a utilizar o IR do Azure, o ADF utiliza o IR do Azure regional, se tiver sido especificado ou determina automaticamente uma localização do IR do Azure se escolher a resolução automática (predefinição) conforme descrito na secção de [Localização do runtime de integração](#integration-runtime-location).
- **Copiar entre uma origem de dados na cloud e uma origem de dados numa rede privada**: se o serviço ligado de origem ou sink apontar para um IR autoalojado, a atividade Cópia é executada no mesmo.
- **Copiar entre duas origens de dados numa rede privada**: tanto o serviço ligado de origem, como sink, têm de apontar para a mesma instância do runtime de integração, o qual é utilizado para executar a atividade Cópia.

### <a name="lookup-and-getmetadata-activity"></a>Atividade de Pesquisa e GetMetadata

A atividade de Pesquisa e de GetMetadata é executada no runtime de integração associado ao serviço ligado ao arquivo de dados.

### <a name="transformation-activity"></a>Atividade de transformação

Cada atividade de transformação tem um Serviço Ligado de destino, que aponta para um runtime de integração. É a partir desta instância de runtime de integração que a atividade de transformação é distribuída.

### <a name="data-flow-activity"></a>Data Flow activity

Data Flow activity is executed on the integration runtime associated to it. 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Create Azure integration runtime](create-azure-integration-runtime.md)
- [Criar um integration runtime autoalojado](create-self-hosted-integration-runtime.md)
- [Criar um integration runtime do Azure-SSIS](create-azure-ssis-integration-runtime.md). This article expands on the tutorial and provides instructions on using Azure SQL Database Managed Instance and joining the IR to a virtual network. 
