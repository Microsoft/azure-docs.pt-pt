---
title: Runtime de integração
description: Saiba mais sobre integration runtime no Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: 911674a80b531a50cfb429c5dc0ff41f1aaceb08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389948"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration runtime no Azure Data Factory 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O Integration Runtime (IR) é a infraestrutura de computação que o Azure Data Factory utiliza para proporcionar as seguintes capacidades de integração de dados em diferentes ambientes de rede:

- **Fluxo de dados**: Execute um [fluxo de dados](concepts-data-flow-overview.md) em ambiente computacional gerido do Azure.  
- **Movimento de dados**: Copiar dados em lojas de dados em redes públicas e lojas de dados em rede privada (no local ou rede privada virtual). Oferece suporte para conectores incorporados, conversão de formatos, mapeamento de colunas e transferência de dados dimensionável e de desempenho elevado.
- **Despacho de atividades**: Despachar e monitorizar as atividades de transformação em execução numa variedade de serviços de computação, tais como Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server, entre outros.
- **Execução de pacotes do SSIS**: executar, nativamente, pacotes do SQL Server Integration Services (SSIS) num ambiente de computação gerida do Azure.

No Data Factory, as atividades definem as ações que vão ser realizadas. Os serviços ligados definem um arquivo de dados ou um serviço de computação de destino. Os runtimes de integração estabelecem a ponte entre a atividade e os serviços ligados.  É referenciado pelo serviço ou atividade ligado, e fornece o ambiente computacional onde a atividade funciona ou é despachada. Desta forma, a atividade pode ser realizada na região mais perto possível do arquivo de dados ou do serviço de computação de destino com o melhor desempenho possível, satisfazendo as necessidades de segurança e conformidade.

Os tempos de integração podem ser criados no Azure Data Factory UX através do [centro de gestão](author-management-hub.md) e quaisquer atividades, conjuntos de dados ou fluxos de dados que os refiram.

## <a name="integration-runtime-types"></a>Tipos de runtimes de integração

A Data Factory oferece três tipos de Tempo de Integração (IR), e deve escolher o tipo que melhor serve as capacidades de integração de dados e as necessidades de ambiente de rede que procura.  Estes três tipos são:

- Azure
- Autoalojado
- Azure-SSIS

A tabela seguinte descreve as capacidades e o suporte de rede para cada um dos tipos de runtimes de integração:

Tipo de IR | Rede pública | Rede privada
------- | -------------- | ---------------
Azure | Fluxo de Dados<br/>Movimento de dados<br/>Distribuição de atividades | Fluxo de Dados<br/>Movimento de dados<br/>Distribuição de atividades
Autoalojado | Movimento de dados<br/>Distribuição de atividades | Movimento de dados<br/>Distribuição de atividades
Azure-SSIS | Execução de pacotes do SSIS | Execução de pacotes do SSIS


## <a name="azure-integration-runtime"></a>Runtime de integração do Azure

Um tempo de integração Azure pode:

- Executar fluxos de dados em Azure 
- Executar atividade de cópia entre lojas de dados em nuvem
- Despachar as seguintes atividades de transformação em rede pública: Databricks Notebook/ Jar/ Python atividade, Atividade de Hive HDInsight, atividade do Porco HDInsight, atividade de HDInsight MapReduce, atividade hdInsight Spark, atividade de streaming HDInsight, Azure Machine Learning Studio (clássico) Atividade de execução de lote, atividade de Azure Machine Learning Studio (clássico) A atividade de Recurso de atualização, atividade de procedimento armazenado, atividade U-SQL de Data Lake Analytics, atividade personalizada .NET, atividade web, atividade de Lookup e atividade de Metudos.

### <a name="azure-ir-network-environment"></a>Ambiente de rede de IR do Azure

O Azure Integration Runtime suporta a ligação a lojas de dados e serviços de cálculo com pontos finais acessíveis ao público. Ativando a Rede Virtual Gerida, o Azure Integration Runtime suporta a ligação a lojas de dados utilizando o serviço de ligações privadas em ambiente de rede privada.

### <a name="azure-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure
Os runtimes de integração do Azure fornecem uma computação totalmente gerida e sem servidor no Azure.  Não tem de se preocupar com a oferta de infraestruturas, instalação de software, remendos ou dimensionamento de capacidade.  Além disso, apenas paga durante a utilização efetiva.

Os runtimes de integração do Azure proporcionam a computação nativa para mover dados entre arquivos de dados na cloud de forma segura, fiável e de elevado desempenho.  Pode definir o número de unidades de integração de dados a utilizar na atividade de cópia e o tamanho da computação do IR do Azure é aumentado verticalmente de forma elástica em conformidade, sem que tenha de ajustar explicitamente o tamanho do Runtime de Integração do Azure. 

O despacho de atividade é uma operação leve para encaminhar a atividade para o serviço de computação alvo, por isso não é necessário aumentar o tamanho do cálculo para este cenário.

Para obter informações sobre a criação e configuração de um Azure IR, consulte [Como criar e configurar o Tempo de Execução da Integração Azure](create-azure-integration-runtime.md). 

> [!NOTE] 
> O tempo de execução da Integração Azure tem propriedades relacionadas com o tempo de execução do Data Flow, que define a infraestrutura computacional subjacente que seria usada para executar os fluxos de dados. 

## <a name="self-hosted-integration-runtime"></a>Runtime de integração autoalojado

Os runtimes de integração autoalojados podem:

- Executar a atividade de cópia entre arquivos de dados na cloud e um arquivo de dados numa rede privada.
- Envio das seguintes atividades de transformação contra recursos computativos em instalações ou Rede Virtual Azure: atividade de HIVE HDInsight (BYOC-Bring Your Own Cluster), atividade de PORCO HDInsight (BYOC), atividade de HDInsight MapReduce (BYOC), atividade de HDInsight Spark Spark (BYOC), atividade de streaming HDInsight (BYOC), Azure Machine Learning Studio (classic) Batch Execution activity, Azure Machine Learning Studio (clássico) Atualização de atividades de recursos, atividade de procedimento armazenado, atividade U-SQL do Data Lake Analytics , atividade personalizada (corre em Azure Batch), atividade de procura e obter atividade de metadados.

> [!NOTE] 
> Utilize o tempo de integração auto-hospedado para suportar lojas de dados que requerem o seu próprio motorista, como SAP Hana, MySQL, etc.  Para mais informações, consulte [as lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

> [!NOTE] 
> Java Runtime Environment (JRE) é uma dependência do AUTO-Hosted IR. Por favor, certifique-se de que tem JRE instalado no mesmo anfitrião.

### <a name="self-hosted-ir-network-environment"></a>Ambiente de rede de IR autoalojado

Se quiser realizar a integração de dados de forma segura num ambiente de rede privada, que não tenha uma linha de visão direta do ambiente de nuvem pública, pode instalar um IR auto-hospedado em ambientes de instalações atrás da sua firewall corporativa, ou dentro de uma rede privada virtual.  O runtime de integração autoalojado só faz ligações de saída baseadas em HTTP para a Internet aberta.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR autoalojado

Instale o IR auto-hospedado numa máquina no local ou numa máquina virtual dentro de uma rede privada. Atualmente, só suportamos a execução dos runtimes de integração autoalojados em sistemas operativos Windows.  

Para elevada disponibilidade e escalabilidade, pode aumentar horizontalmente o runtime de integração autoalojado ao associar a instância lógica a vários computadores no local no modo ativo-ativo.  Para mais informações, consulte como [criar e configurar artigos de IR auto-hospedados](create-self-hosted-integration-runtime.md) sob a forma de orientar para mais detalhes.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime

Para fazer a migração lift and shift de cargas de trabalho do SSIS existentes, pode criar um runtime de integração Azure-SSIS para executar nativamente pacotes do SSIS.

### <a name="azure-ssis-ir-network-environment"></a>Ambiente de rede de IR do Azure-SSIS

O runtime de integração Azure-SSIS pode ser aprovisionado na rede pública ou numa rede privada.  O acesso aos dados no local é suportado mediante a associação do runtime de integração Azure-SSIS a uma Rede Virtual que esteja ligada à sua rede no local.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure-SSIS

O runtime de integração Azure-SSIS é um cluster totalmente gerido de VMs do Azure dedicadas à execução dos pacotes do SSIS. Pode trazer a sua própria Base de Dados Azure SQL ou SQL Managed Instance para o catálogo de projetos/pacotes SSIS (SSISDB). Pode aumentar verticalmente o poder da computação ao especificar o tamanho do nó e aumentá-lo horizontalmente ao definir o número de nós no cluster. Pode gerir o custo da execução do runtime de integração Azure-SSIS ao pará-lo e iniciá-lo, conforme achar mais adequado.

Para obter mais informações, veja o artigo “how to create and configure Azure-SSIS IR” (“Como criar e configurar o runtime de integração Azure-SSIS”) nos guias de procedimentos.  Depois de criado, pode implementar e gerir os seus pacotes do SSIS com poucas ou nenhumas alterações através de ferramentas familiares, como o SQL Server Data Tools (SSDT) e o SQL Server Management Studio (SSMS), tal e qual como utilizaria o SSIS no local.

Para obter mais informações sobre o runtime Azure-SSIS, veja os artigos seguintes: 

- [Tutorial: implementar pacotes do SSIS no Azure](./tutorial-deploy-ssis-packages-azure.md). Este artigo fornece instruções passo a passo para criar um Azure-SSIS IR e utiliza uma Base de Dados Azure SQL para hospedar o catálogo SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). Este artigo expande-se no tutorial e fornece instruções sobre a utilização da SQL Managed Instance e a adesão do IR a uma rede virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Monitorizar um IR Azure-SSIS) Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentá-lo horizontalmente mediante a adição de mais nós ao mesmo. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure. Também descreve os passos para utilizar o portal do Azure para configurar uma rede virtual, de modo a que o IR Azure-SSIS se possa associar à mesma. 

## <a name="integration-runtime-location"></a>Localização do runtime de integração

### <a name="relationship-between-factory-location-and-ir-location"></a>Relação entre localização da fábrica e localização de IR

Quando o cliente cria uma instância de fábrica de dados, eles precisam de especificar a localização para a fábrica de dados. É na localização do Data Factory que os metadados da fábrica de dados são armazenados e a partir de onde o acionamento do pipeline é iniciado. Os metadados para a fábrica só são armazenados na região de escolha do cliente e não serão armazenados noutras regiões.

Entretanto, uma fábrica de dados pode aceder aos arquivos de dados e serviços de computação noutras regiões do Azure para mover dados entre os arquivos de dados ou processar dados com serviços de computação. Este comportamento é realizado através do [IR globalmente disponível](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade dos dados, a eficiência e custos de saída de rede reduzidos.

A localização do runtime de integração define a localização da respetiva computação de back-end e, essencialmente, a localização onde são realizados o movimento de dados, a distribuição de atividades e a execução de pacotes do SSIS. A localização do runtime de integração pode ser diferente da localização da fábrica de dados à qual pertence. 

### <a name="azure-ir-location"></a>Localização do IR do Azure

Pode definir uma determinada localização de um Azure IR, caso em que a execução ou expedição da atividade ocorrerá nessa região específica.

Se optar por utilizar o Azure IR de resolução automática na rede pública, que é o padrão,

- Para a atividade de cópia, a ADF fará um melhor esforço para detetar automaticamente a localização da sua loja de dados de sumidouros e, em seguida, utilizar o IR na mesma região, se disponível ou o mais próximo da mesma geografia; se a região da loja de dados do lavatório não for detetável, o IR na região da fábrica de dados como alternativa é utilizado.

  Por exemplo, tem a sua fábrica criada no Leste dos EUA, 
  
  - Ao copiar dados para Azure Blob no Oeste dos EUA, se a ADF detetou com sucesso que a Blob está nos EUA Ocidentais, a atividade de cópia é executada em IR nos EUA ocidentais; se a deteção da região falhar, a atividade de cópia é executada em IR no Leste dos EUA.
  - Quando copiam dados para a Salesforce dos quais a região não é detetável, a atividade de cópia é executada em IR no Leste dos EUA.

  >[!TIP] 
  >Se tiver requisitos de conformidade de dados estritos e precisar de garantir que os dados não saem de uma detemrinada geografia, pode criar explicitamente um IR do Azure numa determinada região e apontar o Serviço Ligado a esse RI ao utilizar a propriedade ConnectVia. Por exemplo, se quiser copiar dados da Blob no Reino Unido Sul para a Azure Synapse Analytics no Reino Unido Sul e quiser garantir que os dados não saem do Reino Unido, crie um Azure IR no Reino Unido Sul e ligue ambos os Serviços Ligados a este IR.

- Para a execução de atividades De Procura/GetMetadata/Delete (também conhecidas como atividades pipeline), despacho de atividades de transformação (também conhecidas como atividades externas) e operações de autoria (ligação de teste, lista de pastas de navegação e lista de tabelas, dados de pré-visualização), a ADF utiliza o IR na região da fábrica de dados.

- Para o Fluxo de Dados, a ADF utiliza o IR na região da fábrica de dados. 

  > [!TIP] 
  > Uma boa prática seria garantir que o fluxo de dados é executado na mesma região que as suas lojas de dados correspondentes (se possível). Pode fazê-lo resolvendo automaticamente o Azure IR (se a localização da data store for igual à localização da Data Factory), ou criando uma nova instância Azure IR na mesma região que as suas lojas de dados e, em seguida, executar o fluxo de dados sobre ele. 

Se ativar a Rede Virtual Gerida para resolver automaticamente o Azure IR, a ADF utiliza o IR na região da fábrica de dados. 

Pode monitorizar que localização de IR entra em efeito durante a execução da atividade na vista de monitorização da atividade do pipeline no payload de monitorização de atividade ou na IU.

### <a name="self-hosted-ir-location"></a>Localização do IR autoalojado

O runtime de integração autoalojado é registado logicamente no Data Factory e a computação utilizada para suportar as funcionalidades do mesmo é fornecida por si. Por esse motivo, não existe uma propriedade de localização explícita para o runtime de integração autoalojado. 

Quando é utilizado para realizar o movimento de dados, o runtime de integração autoalojado extrai dados da origem e escreve-os no destino.

### <a name="azure-ssis-ir-location"></a>Localização do IR do Azure-SSIS

Selecionar a localização certa para o runtime de integração Azure-SSIS é fundamental para obter um elevado desempenho nos seus fluxos de trabalho extract-transform-load (ETL).

- A localização do seu Azure-SSIS IR não precisa de ser a mesma que a localização da sua fábrica de dados, mas deve ser a mesma que a localização da sua própria Base de Dados Azure SQL ou SQL Managed Instance onde o SSISDB. Desta forma, o runtime de integração Azure-SSIS pode aceder facilmente ao SSISDB sem incorrer em tráfegos excessivos entre diferentes localizações.
- Se não tiver uma Base de Dados SQL ou uma Instância Gerida SQL existente, mas tiver no local fontes de dados/destinos, deverá criar uma nova Base de Dados SQL ou SQL Gestd Instance no mesmo local de uma rede virtual ligada à sua rede de instalações.  Desta forma, pode criar o seu Azure-SSIS IR utilizando a nova Base de Dados Azure SQL ou SQL Managed Instance e juntar essa rede virtual, tudo na mesma localização, minimizando efetivamente os movimentos de dados em diferentes locais.
- Se a localização da sua base de dados Azure SQL ou sql Managed Instance não for a mesma que a localização de uma rede virtual ligada à sua rede no local, primeiro crie o seu Azure-SSIS IR utilizando uma Base de Dados Azure SQL existente ou SQL Managed Instance e juntando outra rede virtual no mesmo local, e depois configurar uma rede virtual para a ligação virtual entre diferentes localizações.

O diagrama seguinte mostra as definições de localização do Data Factory e os respetivos runtimes de integração:

![Localização do runtime de integração](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Determinar o runtime de integração a utilizar

### <a name="copy-activity"></a>Atividade Copiar

Para a atividade Cópia, precisa que os serviços ligados de origem e sink definam a direção do fluxo de dados. É utilizada a lógica seguinte para determinar que instância do runtime de integração é utilizada para fazer a cópia: 

- **Cópia entre duas fontes de dados em nuvem**: quando os serviços ligados à fonte e ao lavatório estão a utilizar o Azure IR, a ADF utiliza o Azure IR regional se especificado, ou determina automaticamente a localização do Azure IR se escolher a auto-base IR (predefinição) como descrito na secção [de localização de tempo de integração.](#integration-runtime-location)
- **Copiar entre uma origem de dados na cloud e uma origem de dados numa rede privada**: se o serviço ligado de origem ou sink apontar para um runtime de integração autoalojado, a atividade Cópia é executada no mesmo.
- **Cópia entre duas fontes de dados na rede privada**: tanto o Serviço Ligado de origem como o sinkdes devem apontar para o mesmo caso de tempo de integração, e que o tempo de integração é utilizado para executar a atividade de cópia.

### <a name="lookup-and-getmetadata-activity"></a>Atividade de Pesquisa e GetMetadata

A atividade de Pesquisa e de GetMetadata é executada no runtime de integração associado ao serviço ligado ao arquivo de dados.

### <a name="external-transformation-activity"></a>Atividade de transformação externa

Cada atividade de transformação externa que utiliza um motor de computação externa tem um serviço linked de computação alvo, que aponta para um tempo de execução de integração. Esta instância de execução de integração determina o local do qual essa atividade de transformação codificada à mão externa é despachada.

### <a name="data-flow-activity"></a>Atividade do Fluxo de Dados

As atividades do Data Flow são executadas no tempo de integração do Azure que lhe está associado. O computamento Spark utilizado pelos Data Flows é determinado pelas propriedades de fluxo de dados no seu Tempo de Execução de Integração Azure e são totalmente geridos pela ADF.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos:

- [Criar tempo de integração Azure](create-azure-integration-runtime.md)
- [Create self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Criar o runtime de integração autoalojado)
- [Criar um tempo de integração Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande-se no tutorial e fornece instruções sobre a utilização da SQL Managed Instance e a adesão do IR a uma rede virtual.