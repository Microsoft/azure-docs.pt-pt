---
title: Runtime de integração
description: Saiba mais sobre integration runtime no Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/28/2020
ms.openlocfilehash: 194bc7983019a616d534a4146f86fff59f9719dc
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357216"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration runtime no Azure Data Factory
O Integration Runtime (IR) é a infraestrutura de computação que o Azure Data Factory utiliza para proporcionar as seguintes capacidades de integração de dados em diferentes ambientes de rede:

- **Fluxo de Dados**: Execute um [Fluxo de Dados](concepts-data-flow-overview.md) em ambiente computacional azure gerido.  
- **Movimento de dados**: Copiar dados através de lojas de dados em redes públicas e lojas de dados em rede privada (no local ou rede privada virtual). Oferece suporte para conectores incorporados, conversão de formatos, mapeamento de colunas e transferência de dados dimensionável e de desempenho elevado.
- **Despacho de atividades**: Despachar e monitorizar atividades de transformação em execução de uma variedade de serviços de computação como Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server, e muito mais.
- **Execução de pacotes do SSIS**: executar, nativamente, pacotes do SQL Server Integration Services (SSIS) num ambiente de computação gerida do Azure.

No Data Factory, as atividades definem as ações que vão ser realizadas. Os serviços ligados definem um arquivo de dados ou um serviço de computação de destino. Os runtimes de integração estabelecem a ponte entre a atividade e os serviços ligados.  É referenciado pelo serviço ou atividade ligado, e fornece o ambiente de computação onde a atividade funciona ou é despachada. Desta forma, a atividade pode ser realizada na região mais perto possível do arquivo de dados ou do serviço de computação de destino com o melhor desempenho possível, satisfazendo as necessidades de segurança e conformidade.

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

- Fluxos de dados em funcionamento em Azure 
- Executar atividades de cópia entre arquivos de dados na cloud
- Envio das seguintes atividades de transformação em rede pública: Databricks Notebook/ Jar/ Python activity, HDInsight Hive activity, HDInsight Pig activity, HDInsight MapReduce activity, HDInsight Spark activity, HDInsight Streaming activity, Machine Learning Batch Execution activity, Machine Learning Update Resource activity, Stored Procedure activity, Data Lake Analytics U-SQL activity, .NET custom activity, Web activity, Lookup activity, e Get Metadata activity.

### <a name="azure-ir-network-environment"></a>Ambiente de rede de IR do Azure
O Azure Integration Runtime suporta a ligação a lojas de dados e serviços de computação com pontos finais acessíveis ao público. Utilize um runtime de integração autoalojado para o ambiente Rede Virtual do Azure.

### <a name="azure-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure
Os runtimes de integração do Azure fornecem uma computação totalmente gerida e sem servidor no Azure.  Não tem de se preocupar com o aprovisionamento da infraestrutura, a instalação de software, as correções ou o dimensionamento de capacidades.  Além disso, apenas paga durante a utilização efetiva.

Os runtimes de integração do Azure proporcionam a computação nativa para mover dados entre arquivos de dados na cloud de forma segura, fiável e de elevado desempenho.  Pode definir o número de unidades de integração de dados a utilizar na atividade de cópia e o tamanho da computação do Azure IR é aumentado verticalmente de forma elástica em conformidade, sem que tenha de ajustar explicitamente o tamanho do Azure Integration Runtime. 

A distribuição de atividades é uma operação simples para encaminhar a atividade para o serviço de computação de destino, para que não seja necessário aumentar verticalmente o tamanho da computação neste cenário.

Para obter informações sobre como criar e configurar um runtime de integração do Azure, veja How to create and configure Azure IR (Como criar e configurar um runtime de integração do Azure) nos guias de procedimentos. 

> [!NOTE] 
> O tempo de execução da Integração Azure tem propriedades relacionadas com o tempo de execução do Fluxo de Dados, que define a infraestrutura de cálculo subjacente que seria usada para executar os fluxos de dados. 

## <a name="self-hosted-integration-runtime"></a>Runtime de integração autoalojado
Os runtimes de integração autoalojados podem:

- Executar a atividade de cópia entre arquivos de dados na cloud e um arquivo de dados numa rede privada.
- Envio das seguintes atividades de transformação contra recursos de computação em instalações ou Rede Virtual Azure: Atividade da Hive HDInsight (BYOC-Bring Your Own Cluster), atividade do Porco HDInsight (BYOC), HDInsight MapReduce atividade (BYOC), HDInsight Spark atividade (BYOC), atividade de streaming HDInsight (BYOC), atividade de execução de lotes de aprendizagem automática, atividades de recursos de atualização de aprendizagem automática, atividade de procedimento armazenado, atividade de Data Lake Analytics U-SQL, atividade personalizada (executa em Lote Azure), Lookup atividade, e obter atividade de metadados.

> [!NOTE] 
> Utilize o tempo de execução de integração auto-hospedado para suportar lojas de dados que requeiram trazer o seu próprio motorista, como SAP Hana, MySQL, etc.  Para mais informações, consulte as lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

> [!NOTE] 
> Java Runtime Environment (JRE) é uma dependência do IR auto-hospedado. Por favor, certifique-se de que tem JRE instalado no mesmo hospedeiro.

### <a name="self-hosted-ir-network-environment"></a>Ambiente de rede de IR autoalojado
Se quiser efetuar a integração de dados de forma segura num ambiente de rede privada, que não tem um campo de visão a partir do ambiente de rede pública, pode instalar um IR autoalojado no ambiente no local atrás da firewall emprsarial ou dentro de uma rede privada virtual.  O runtime de integração autoalojado só faz ligações de saída baseadas em HTTP para a Internet aberta.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR autoalojado
O IR autoalojado tem de ser instalado num computador no local ou numa máquina virtual que esteja numa rede privada. Atualmente, só suportamos a execução dos runtimes de integração autoalojados em sistemas operativos Windows.  

Para elevada disponibilidade e escalabilidade, pode aumentar horizontalmente o IR autoalojado ao associar a instância lógica a vários computadores no local no modo ativo-ativo.  Para mais informações, consulte como criar e configurar o artigo de [IR auto-hospedado](create-self-hosted-integration-runtime.md) sob a forma de orientar para mais detalhes.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime
Para fazer a migração lift and shift de cargas de trabalho do SSIS existentes, pode criar um runtime de integração Azure-SSIS para executar nativamente pacotes do SSIS.

### <a name="azure-ssis-ir-network-environment"></a>Ambiente de rede de IR do Azure-SSIS
O runtime de integração Azure-SSIS pode ser aprovisionado na rede pública ou numa rede privada.  O acesso aos dados no local é suportado mediante a associação do runtime de integração Azure-SSIS a uma Rede Virtual que esteja ligada à sua rede no local.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure-SSIS
O runtime de integração Azure-SSIS é um cluster totalmente gerido de VMs do Azure dedicadas à execução dos pacotes do SSIS. Pode trazer o seu próprio Azure SQL Database ou servidor Managed Instance para acolher o catálogo de projetos/pacotes SSIS (SSISDB) que lhe serão anexados. Pode aumentar verticalmente o poder da computação ao especificar o tamanho do nó e aumentá-lo horizontalmente ao definir o número de nós no cluster. Pode gerir o custo da execução do Azure-SSIS Integration Runtime ao pará-lo e iniciá-lo, conforme achar mais adequado.

Para obter mais informações, veja o artigo “how to create and configure Azure-SSIS IR” (“Como criar e configurar o runtime de integração Azure-SSIS”) nos guias de procedimentos.  Depois de criado, pode implementar e gerir os seus pacotes do SSIS com poucas ou nenhumas alterações através de ferramentas familiares, como o SQL Server Data Tools (SSDT) e o SQL Server Management Studio (SSMS), tal e qual como utilizaria o SSIS no local.

Para obter mais informações sobre o runtime Azure-SSIS, veja os artigos seguintes: 

- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo disponibiliza instruções passo a passo para criar um IR Azure-SSIS e utiliza uma base de dados da Base de Dados SQL do Azure para alojar o catálogo do SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). Este artigo expande-se no tutorial e fornece instruções sobre a utilização da Base de Dados Azure SQL Managed Instance e a junção do IR a uma rede virtual. 
- [Monitorizar um Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentá-lo horizontalmente mediante a adição de mais nós ao mesmo. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure. Também descreve os passos para utilizar o portal do Azure para configurar uma rede virtual, de modo a que o IR Azure-SSIS se possa associar à mesma. 

## <a name="integration-runtime-location"></a>Localização do runtime de integração
É na localização do Data Factory que os metadados da fábrica de dados são armazenados e a partir de onde o acionamento do pipeline é iniciado. Entretanto, uma fábrica de dados pode aceder aos arquivos de dados e serviços de computação noutras regiões do Azure para mover dados entre os arquivos de dados ou processar dados com serviços de computação. Este comportamento é realizado através do [IR globalmente disponível](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade dos dados, a eficiência e custos de saída de rede reduzidos.

A localização do runtime de integração define a localização da respetiva computação de back-end e, essencialmente, a localização onde são realizados o movimento de dados, a distribuição de atividades e a execução de pacotes do SSIS. A localização do runtime de integração pode ser diferente da localização da fábrica de dados à qual pertence. 

### <a name="azure-ir-location"></a>Localização do IR do Azure
Pode definir uma determinada localização de um IR do Azure, em cujo caso o movimento de dados ou emissão de atividade ocorrem nessa região específica. 

Se optar por utilizar o **Auto-Resolve Azure IR,** que é o padrão, 

- Para a atividade de cópia, o ADF vai fazer o melhor esforço para detetar automaticamente o seu sink e arquivo de dados de origem para escolher a melhor localização na mesma região, se disponível, ou a mais próxima na mesma geografia; ou se não for detetável, utilizar a mesma região de fábrica de dados como alternativa.

- Para a execução de atividade supérbio/GetMetadata/Eliminar (também conhecida como atividades de Pipeline), despachação de atividades de transformação (também conhecidas como atividades externas) e operações de autoria (ligação de teste, lista de pastas de navegação e lista de tabelas, dados de pré-visualização), a ADF utilizará o IR na região da fábrica de dados.

- Para o Data Flow, a ADF utilizará o IR na região da fábrica de dados. 

  > [!TIP] 
  > Uma boa prática seria garantir que o fluxo de dados corre na mesma região que as suas lojas de dados correspondentes (se possível). Pode conseguir isso através da resolução automática do Azure IR (se a localização da loja de dados for igual à localização da Data Factory), ou através da criação de uma nova instância de Ir Azure na mesma região que as suas lojas de dados e, em seguida, executar o fluxo de dados no mesmo. 

Pode monitorizar que localização de IR entra em efeito durante a execução da atividade na vista de monitorização da atividade do pipeline no payload de monitorização de atividade ou na IU.

>[!TIP]
>Se tiver requisitos de conformidade de dados estritos e precisar de garantir que os dados não saem de uma detemrinada geografia, pode criar explicitamente um IR do Azure numa determinada região e apontar o Serviço Ligado a esse RI ao utilizar a propriedade ConnectVia. Por exemplo, se quiser copiar dados do Blob no Sul do Reino Unido para o SQL DW no Sul do Reino Unido e quiser garantir que os dados não saem do Reino Unido, crie um IR do Azure no Sul do Reino Unido e ligue ambos os Serviços Ligados a este IR.

### <a name="self-hosted-ir-location"></a>Localização do IR autoalojado
O IR autoalojado é registado logicamente no Data Factory e a computação utilizada para suportar as funcionalidades do mesmo é fornecida por si. Por esse motivo, não existe uma propriedade de localização explícita para o runtime de integração autoalojado. 

Quando é utilizado para realizar o movimento de dados, o IR autoalojado extrai dados da origem e escreve-os no destino.

### <a name="azure-ssis-ir-location"></a>Localização do IR do Azure-SSIS
Selecionar a localização certa para o runtime de integração Azure-SSIS é fundamental para obter um elevado desempenho nos seus fluxos de trabalho extract-transform-load (ETL).

- A localização do seu IR Azure-SSIS não precisa de ser a mesma que a localização da sua fábrica de dados, mas deve ser a mesma que a localização da sua própria Base de Dados Azure SQL ou servidor De instância gerida onde o SSISDB está hospedado. Desta forma, o Azure-SSIS Integration Runtime pode aceder facilmente ao SSISDB sem incorrer em tráfegos excessivos entre diferentes localizações.
- Se não tiver uma base de dados Azure SQL ou servidor De instância gerida existente para hospedar o SSISDB, mas tiver fontes/destinos de dados no local, deverá criar uma nova Base de Dados Azure SQL ou servidor De instância gerida na mesma localização de uma rede virtual ligada à sua rede no local.  Desta forma, pode criar o seu IR Azure-SSIS utilizando o novo servidor Azure SQL ou Managed Instance e juntando-se a essa rede virtual, tudo no mesmo local, minimizando efetivamente os movimentos de dados em diferentes locais.
- Se a localização da sua base de dados Azure SQL existente ou servidor de instância gerida onde o SSISDB está hospedado não for a mesma que a localização de uma rede virtual ligada à sua rede no local, crie primeiro o seu IR Azure-SSIS utilizando uma base de dados Azure SQL existente ou Gerido o servidor Instance e juntando-se a outra rede virtual no mesmo local, e, em seguida, configurar uma rede virtual para a ligação de rede virtual entre diferentes locais.

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

### <a name="external-transformation-activity"></a>Atividade de transformação externa

Cada atividade de transformação externa que utiliza um motor de computação externo tem um serviço de computação-alvo Linked Service, que aponta para um tempo de execução de integração. Esta instância de integração determina o local onde essa atividade externa de transformação codificada à mão é despachada.

### <a name="data-flow-activity"></a>Atividade de Fluxo de Dados

As atividades de Fluxo de Dados são executadas no tempo de funcionamento da integração azure associado ao mesmo. A computação Spark utilizada pelos Fluxos de Dados é determinada pelas propriedades de fluxo de dados no seu Tempo de Funcionamento de Integração Azure e é totalmente gerida pela ADF.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Criar o tempo de execução da integração Azure](create-azure-integration-runtime.md)
- [Criar um integration runtime autoalojado](create-self-hosted-integration-runtime.md)
- [Criar um integration runtime do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande-se no tutorial e fornece instruções sobre a utilização da Base de Dados Azure SQL Managed Instance e a junção do IR a uma rede virtual. 
