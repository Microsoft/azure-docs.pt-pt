---
title: Runtime de integração
description: Saiba mais sobre integration runtime no Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/26/2020
ms.openlocfilehash: ffa348c796a4d9d4e3bdb8e7ce18ba0eb82e17ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418393"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration runtime no Azure Data Factory 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O Integration Runtime (IR) é a infraestrutura de computação que o Azure Data Factory utiliza para proporcionar as seguintes capacidades de integração de dados em diferentes ambientes de rede:

- **Fluxo de Dados**: Execute um [Fluxo de Dados](concepts-data-flow-overview.md) em ambiente computacional azure gerido.  
- **Movimento de dados**: Copiar dados através de lojas de dados em redes públicas e lojas de dados em rede privada (no local ou rede privada virtual). Oferece suporte para conectores incorporados, conversão de formatos, mapeamento de colunas e transferência de dados dimensionável e de desempenho elevado.
- **Despacho de atividades**: Despachar e monitorizar atividades de transformação em execução de uma variedade de serviços de computação como Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server, e muito mais.
- **Execução de pacotes do SSIS**: executar, nativamente, pacotes do SQL Server Integration Services (SSIS) num ambiente de computação gerida do Azure.

No Data Factory, as atividades definem as ações que vão ser realizadas. Os serviços ligados definem um arquivo de dados ou um serviço de computação de destino. Os runtimes de integração estabelecem a ponte entre a atividade e os serviços ligados.  É referenciado pelo serviço ou atividade ligado, e fornece o ambiente de computação onde a atividade funciona ou é despachada. Desta forma, a atividade pode ser realizada na região mais perto possível do arquivo de dados ou do serviço de computação de destino com o melhor desempenho possível, satisfazendo as necessidades de segurança e conformidade.

## <a name="integration-runtime-types"></a>Tipos de runtimes de integração

Data Factory oferece três tipos de Tempo de Integração (IR), e você deve escolher o tipo que melhor serve as capacidades de integração de dados e as necessidades ambientais de rede que você procura.  Estes três tipos são:

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

Um tempo de execução de integração Azure pode:

- Fluxos de dados em funcionamento em Azure 
- Executar atividades de cópia entre arquivos de dados na cloud
- Envio das seguintes atividades de transformação em rede pública: Databricks Notebook/ Jar/ Python activity, HDInsight Hive activity, HDInsight Pig activity, HDInsight MapReduce activity, HDInsight Spark activity, HDInsight Streaming activity, Machine Learning Batch Execution activity, Machine Learning Update Resource activity, Stored Procedure activity, Data Lake Analytics U-SQL activity, .NET custom activity, Web activity, Lookup activity, e Get Metadata activity.

### <a name="azure-ir-network-environment"></a>Ambiente de rede de IR do Azure

O Azure Integration Runtime suporta a ligação a lojas de dados e serviços de computação com pontos finais acessíveis ao público. Utilize um runtime de integração autoalojado para o ambiente Rede Virtual do Azure.

### <a name="azure-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure
Os runtimes de integração do Azure fornecem uma computação totalmente gerida e sem servidor no Azure.  Não tem de se preocupar com a prestação de infraestruturas, instalação de software, remendos ou escalação de capacidade.  Além disso, apenas paga durante a utilização efetiva.

Os runtimes de integração do Azure proporcionam a computação nativa para mover dados entre arquivos de dados na cloud de forma segura, fiável e de elevado desempenho.  Pode definir o número de unidades de integração de dados a utilizar na atividade de cópia e o tamanho da computação do IR do Azure é aumentado verticalmente de forma elástica em conformidade, sem que tenha de ajustar explicitamente o tamanho do Runtime de Integração do Azure. 

O despacho de atividade é uma operação leve para encaminhar a atividade para o serviço de computação alvo, por isso não é necessário aumentar o tamanho da computação para este cenário.

Para obter informações sobre a criação e configuração de um IR Azure, consulte como criar e configurar o Azure IR sob a forma de orientar. 

> [!NOTE] 
> O tempo de execução da Integração Azure tem propriedades relacionadas com o tempo de execução do Fluxo de Dados, que define a infraestrutura de cálculo subjacente que seria usada para executar os fluxos de dados. 

## <a name="self-hosted-integration-runtime"></a>Runtime de integração autoalojado

Os runtimes de integração autoalojados podem:

- Executar a atividade de cópia entre arquivos de dados na cloud e um arquivo de dados numa rede privada.
- Envio das seguintes atividades de transformação contra recursos computacionais em instalações ou Rede Virtual Azure: Atividade de Hive HDInsight (BYOC-Bring Your Own Cluster), atividade de Suíno HDInsight (BYOC), HDInsight MapReduce atividade (BYOC), atividade de Faísca HDInsight (BYOC), atividade de streaming HDInsight (BYOC), atividade de execução de lotes de aprendizagem automática, Atividades de recursos de atualização de aprendizagem automática, atividade de procedimento armazenado, atividade u-SQL do Lago de Dados, atividade personalizada (executa em Lote Azure) , Atividade de procura e obter atividade de Metadados.

> [!NOTE] 
> Utilize o tempo de execução de integração auto-hospedado para suportar lojas de dados que requeiram trazer o seu próprio motorista, como SAP Hana, MySQL, etc.  Para mais informações, consulte as lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

> [!NOTE] 
> Java Runtime Environment (JRE) é uma dependência do IR auto-hospedado. Por favor, certifique-se de que tem JRE instalado no mesmo hospedeiro.

### <a name="self-hosted-ir-network-environment"></a>Ambiente de rede de IR autoalojado

Se quiser realizar a integração de dados de forma segura num ambiente de rede privada, que não tenha uma linha de visão direta do ambiente de nuvem pública, pode instalar um IR auto-hospedado no ambiente das instalações atrás da sua firewall corporativa, ou dentro de uma rede privada virtual.  O runtime de integração autoalojado só faz ligações de saída baseadas em HTTP para a Internet aberta.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR autoalojado

Instale o IV auto-hospedado numa máquina no local ou numa máquina virtual dentro de uma rede privada. Atualmente, só suportamos a execução dos runtimes de integração autoalojados em sistemas operativos Windows.  

Para elevada disponibilidade e escalabilidade, pode aumentar horizontalmente o runtime de integração autoalojado ao associar a instância lógica a vários computadores no local no modo ativo-ativo.  Para mais informações, consulte como criar e configurar o artigo de [IR auto-hospedado](create-self-hosted-integration-runtime.md) sob a forma de orientar para mais detalhes.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime

Para fazer a migração lift and shift de cargas de trabalho do SSIS existentes, pode criar um runtime de integração Azure-SSIS para executar nativamente pacotes do SSIS.

### <a name="azure-ssis-ir-network-environment"></a>Ambiente de rede de IR do Azure-SSIS

O runtime de integração Azure-SSIS pode ser aprovisionado na rede pública ou numa rede privada.  O acesso aos dados no local é suportado mediante a associação do runtime de integração Azure-SSIS a uma Rede Virtual que esteja ligada à sua rede no local.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure-SSIS

O runtime de integração Azure-SSIS é um cluster totalmente gerido de VMs do Azure dedicadas à execução dos pacotes do SSIS. Pode trazer o seu próprio Azure SQL Database ou servidor Managed Instance para acolher o catálogo de projetos/pacotes SSIS (SSISDB) que lhe serão anexados. Pode aumentar verticalmente o poder da computação ao especificar o tamanho do nó e aumentá-lo horizontalmente ao definir o número de nós no cluster. Pode gerir o custo da execução do runtime de integração Azure-SSIS ao pará-lo e iniciá-lo, conforme achar mais adequado.

Para obter mais informações, veja o artigo “how to create and configure Azure-SSIS IR” (“Como criar e configurar o runtime de integração Azure-SSIS”) nos guias de procedimentos.  Depois de criado, pode implementar e gerir os seus pacotes do SSIS com poucas ou nenhumas alterações através de ferramentas familiares, como o SQL Server Data Tools (SSDT) e o SQL Server Management Studio (SSMS), tal e qual como utilizaria o SSIS no local.

Para obter mais informações sobre o runtime Azure-SSIS, veja os artigos seguintes: 

- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo fornece instruções passo a passo para criar um IR Azure-SSIS e utiliza uma Base de Dados Azure SQL para acolher o catálogo SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). Este artigo expande-se no tutorial e fornece instruções sobre a utilização da Base de Dados Azure SQL Managed Instance e a junção do IR a uma rede virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Monitorizar um IR Azure-SSIS) Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentá-lo horizontalmente mediante a adição de mais nós ao mesmo. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure. Também descreve os passos para utilizar o portal do Azure para configurar uma rede virtual, de modo a que o IR Azure-SSIS se possa associar à mesma. 

## <a name="integration-runtime-location"></a>Localização do runtime de integração

É na localização do Data Factory que os metadados da fábrica de dados são armazenados e a partir de onde o acionamento do pipeline é iniciado. Entretanto, uma fábrica de dados pode aceder aos arquivos de dados e serviços de computação noutras regiões do Azure para mover dados entre os arquivos de dados ou processar dados com serviços de computação. Este comportamento é realizado através do [IR globalmente disponível](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade dos dados, a eficiência e custos de saída de rede reduzidos.

A localização do runtime de integração define a localização da respetiva computação de back-end e, essencialmente, a localização onde são realizados o movimento de dados, a distribuição de atividades e a execução de pacotes do SSIS. A localização do runtime de integração pode ser diferente da localização da fábrica de dados à qual pertence. 

### <a name="azure-ir-location"></a>Localização do IR do Azure

- Para a atividade de cópia, a ADF fará o melhor esforço para detetar automaticamente a localização do seu depósito de dados afundado, em seguida, utilizar o IR na mesma região, se disponível ou o mais próximo na mesma geografia; se a região da loja de dados do lavatório não for detetável, o IR na região da fábrica de dados como alternativa é utilizado.

  Por exemplo, tem a sua fábrica criada no Leste dos EUA, 
  
  - Quando copiam os dados para o Azure Blob, no Oeste dos EUA, se a ADF detetou com sucesso que a Blob está nos EUA Ocidentais, a atividade de cópia é executada em IR nos EUA Ocidentais; se a deteção da região falhar, a atividade de cópia é executada em IR no Leste dos EUA.
  - Quando copiam dados para a Salesforce de que a região não é detetável, a atividade de cópia é executada em IR no Leste dos EUA.

- Para a atividade de cópia, a ADF faz o melhor esforço para detetar automaticamente o seu depósito de dados de sumidouro e fonte para escolher a melhor localização, seja na mesma região (se disponível), ou a mais próxima na mesma geografia, ou se não for detetável para usar a região da fábrica de dados como alternativa.

- Para a execução de atividade supérbio/GetMetadata/Eliminar (também conhecida como atividades de Pipeline), despachação de atividades de transformação (também conhecidas como atividades externas) e operações de autoria (ligação de teste, lista de pastas de navegação e lista de tabelas, dados de pré-visualização), a ADF utiliza o IR na região da fábrica de dados.

- Para o Data Flow, a ADF utiliza o IR na região da fábrica de dados. 

  > [!TIP] 
  > Uma boa prática seria garantir que o fluxo de dados corre na mesma região que as suas lojas de dados correspondentes (se possível). Pode conseguir isso através da resolução automática do Azure IR (se a localização da loja de dados for igual à localização da Data Factory), ou através da criação de uma nova instância de Ir Azure na mesma região que as suas lojas de dados e, em seguida, executar o fluxo de dados no mesmo. 

Pode monitorizar que localização de IR entra em efeito durante a execução da atividade na vista de monitorização da atividade do pipeline no payload de monitorização de atividade ou na IU.

### <a name="self-hosted-ir-location"></a>Localização do IR autoalojado

O runtime de integração autoalojado é registado logicamente no Data Factory e a computação utilizada para suportar as funcionalidades do mesmo é fornecida por si. Por esse motivo, não existe uma propriedade de localização explícita para o runtime de integração autoalojado. 

Quando é utilizado para realizar o movimento de dados, o runtime de integração autoalojado extrai dados da origem e escreve-os no destino.

### <a name="azure-ssis-ir-location"></a>Localização do IR do Azure-SSIS

Selecionar a localização certa para o runtime de integração Azure-SSIS é fundamental para obter um elevado desempenho nos seus fluxos de trabalho extract-transform-load (ETL).

- A localização do seu IR Azure-SSIS não precisa de ser a mesma que a localização da sua fábrica de dados, mas deve ser a mesma que a localização da sua própria Base de Dados Azure SQL ou servidor De instância gerida onde o SSISDB está hospedado. Desta forma, o runtime de integração Azure-SSIS pode aceder facilmente ao SSISDB sem incorrer em tráfegos excessivos entre diferentes localizações.
- Se não tiver uma base de dados Azure SQL ou servidor De instância gerida existente para hospedar o SSISDB, mas tiver fontes/destinos de dados no local, deverá criar uma nova Base de Dados Azure SQL ou servidor De instância gerida na mesma localização de uma rede virtual ligada à sua rede no local.  Desta forma, pode criar o seu IR Azure-SSIS utilizando o novo servidor Azure SQL ou Managed Instance e juntando-se a essa rede virtual, tudo no mesmo local, minimizando efetivamente os movimentos de dados em diferentes locais.
- Se a localização da sua base de dados Azure SQL existente ou servidor De instância gerida onde o SSISDB está hospedado não for a mesma que a localização de uma rede virtual ligada à sua rede no local, primeiro crie o seu IR Azure-SSIS utilizando uma base de dados Azure SQL existente ou servidor De instância gerida e junte-se a outra rede virtual no mesmo local e, em seguida, configure uma rede virtual para a ligação de rede virtual entre diferentes locais.

O diagrama seguinte mostra as definições de localização do Data Factory e os respetivos runtimes de integração:

![Localização do runtime de integração](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Determinar o runtime de integração a utilizar

### <a name="copy-activity"></a>Atividade Copiar

Para a atividade Cópia, precisa que os serviços ligados de origem e sink definam a direção do fluxo de dados. É utilizada a lógica seguinte para determinar que instância do runtime de integração é utilizada para fazer a cópia: 

- **Copiando entre duas fontes**de dados em nuvem : quando tanto os serviços ligados à origem como a sink estão a utilizar o Azure IR, a ADF utiliza o Iv Regional se especificar, ou determinar automaticamente uma localização do IR Azure se escolher o IR de resolução automática (padrão) conforme descrito na secção de localização de tempo de [execução da Integração.](#integration-runtime-location)
- **Copiar entre uma origem de dados na cloud e uma origem de dados numa rede privada**: se o serviço ligado de origem ou sink apontar para um runtime de integração autoalojado, a atividade Cópia é executada no mesmo.
- **Copiar entre duas fontes**de dados em rede privada : tanto a fonte como o serviço ligado devem apontar para o mesmo caso de tempo de integração e que o tempo de execução da integração é utilizado para executar a atividade de cópia.

### <a name="lookup-and-getmetadata-activity"></a>Atividade de Pesquisa e GetMetadata

A atividade de Pesquisa e de GetMetadata é executada no runtime de integração associado ao serviço ligado ao arquivo de dados.

### <a name="external-transformation-activity"></a>Atividade de transformação externa

Cada atividade de transformação externa que utiliza um motor de computação externo tem um serviço de computação-alvo Linked Service, que aponta para um tempo de execução de integração. Esta instância de integração determina o local onde essa atividade externa de transformação codificada à mão é despachada.

### <a name="data-flow-activity"></a>Atividade de Fluxo de Dados

As atividades de Fluxo de Dados são executadas no tempo de funcionamento da integração azure associado ao mesmo. A computação Spark utilizada pelos Fluxos de Dados é determinada pelas propriedades de fluxo de dados no seu Tempo de Funcionamento de Integração Azure e é totalmente gerida pela ADF.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos:

- [Criar o tempo de execução da integração Azure](create-azure-integration-runtime.md)
- [Create self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Criar o runtime de integração autoalojado)
- Criar um tempo de execução de [integração Azure-SSIS.](create-azure-ssis-integration-runtime.md) Este artigo expande-se no tutorial e fornece instruções sobre a utilização da Base de Dados Azure SQL Managed Instance e a junção do IR a uma rede virtual. 
