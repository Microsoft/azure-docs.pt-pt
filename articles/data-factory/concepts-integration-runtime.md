---
title: Integration runtime no Azure Data Factory | Microsoft Docs
description: Saiba mais sobre integration runtime no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: jingwang
ms.openlocfilehash: ab6da597457fa94c171a1e2547c1d48cf35cd0f9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57893055"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration runtime no Azure Data Factory
O Integration Runtime (IR) é a infraestrutura de computação que o Azure Data Factory utiliza para proporcionar as seguintes capacidades de integração de dados em diferentes ambientes de rede:

- **Movimento de dados**: Copie dados em armazenamentos de dados nos arquivos de dados e de rede públicos numa rede privada (no local ou rede privada virtual). Oferece suporte para conectores incorporados, conversão de formatos, mapeamento de colunas e transferência de dados dimensionável e de desempenho elevado.
- **Distribuição de atividades**:  Distribuir e monitorizar atividades de transformação em execução num vasto leque de serviços de computação, como o Azure HDInsight, Azure Machine Learning, base de dados do Azure SQL, SQL Server e muito mais.
- **Execução de pacotes do SSIS**: Execute nativamente pacotes do SQL Server Integration Services (SSIS) num ambiente de computação gerida do Azure.

No Data Factory, as atividades definem as ações que vão ser realizadas. Os serviços ligados definem um arquivo de dados ou um serviço de computação de destino. Os runtimes de integração estabelecem a ponte entre a atividade e os serviços ligados.  São referenciados pelo serviço ligado e fornecem o ambiente de computação em que a atividade é executada ou a partir do qual é distribuída.  Desta forma, a atividade pode ser realizada na região mais perto possível do arquivo de dados ou do serviço de computação de destino com o melhor desempenho possível, satisfazendo as necessidades de segurança e conformidade.

## <a name="integration-runtime-types"></a>Tipos de runtimes de integração
O Data Factory oferece três tipos de runtimes de integração e deve escolher aquele que melhor se adequa às capacidades de integração de dados e às necessidades de ambiente de rede de que está à procura.  Estes três tipos são:

- Azure
- Autoalojado
- Azure-SSIS

A tabela seguinte descreve as capacidades e o suporte de rede para cada um dos tipos de runtimes de integração:

Tipo de IR | Rede pública | Rede privada
------- | -------------- | ---------------
Azure | Movimento de dados<br/>Distribuição de atividades | &nbsp;
Autoalojado | Movimento de dados<br/>Distribuição de atividades | Movimento de dados<br/>Distribuição de atividades
Azure-SSIS | Execução de pacotes do SSIS | Execução de pacotes do SSIS

O diagrama seguinte mostra como os diferentes runtimes de integração podem ser utilizados em combinação para oferecer suporte de rede e capacidades de integração de dados avançadas:

![Diferentes tipos de runtimes de integração](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Runtime de integração do Azure
Os runtimes de integração do Azure podem:

- Executar atividades de cópia entre arquivos de dados na cloud
- Expedir as atividades de transformação seguintes na rede pública: Atividade do Hive do HDInsight, atividade Pig do HDInsight, atividade MapReduce do HDInsight, atividade Spark do HDInsight, atividade Streaming do HDInsight, atividade execução de Batch do Machine Learning, atividades recursos de atualização do Machine Learning, atividade de procedimento armazenado, Atividade do Data Lake Analytics U-SQL, atividade personalizada .NET, atividade Web, atividade de pesquisa e atividade obter metadados.

### <a name="azure-ir-network-environment"></a>Ambiente de rede de IR do Azure
O Azure Integration Runtime suporta a ligação a arquivos de dados e a serviços de computação na rede pública com pontos finais públicos acessíveis. Utilize um runtime de integração autoalojado para o ambiente Rede Virtual do Azure.

### <a name="azure-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure
Os runtimes de integração do Azure fornecem uma computação totalmente gerida e sem servidor no Azure.  Não tem de se preocupar com o aprovisionamento da infraestrutura, a instalação de software, as correções ou o dimensionamento de capacidades.  Além disso, apenas paga durante a utilização efetiva.

Os runtimes de integração do Azure proporcionam a computação nativa para mover dados entre arquivos de dados na cloud de forma segura, fiável e de elevado desempenho.  Pode definir o número de unidades de integração de dados a utilizar na atividade de cópia e o tamanho da computação do Azure IR é aumentado verticalmente de forma elástica em conformidade, sem que tenha de ajustar explicitamente o tamanho do Azure Integration Runtime.

A distribuição de atividades é uma operação simples para encaminhar a atividade para o serviço de computação de destino, para que não seja necessário aumentar verticalmente o tamanho de computação neste cenário.

Para obter informações sobre como criar e configurar um runtime de integração do Azure, veja How to create and configure Azure IR (Como criar e configurar um runtime de integração do Azure) nos guias de procedimentos. 

## <a name="self-hosted-integration-runtime"></a>Runtime de integração autoalojado
Os runtimes de integração autoalojados podem:

- Executar a atividade de cópia entre arquivos de dados na cloud e um arquivo de dados numa rede privada.
- Expedir as seguintes atividades de transformação relativamente aos recursos de computação no local ou de rede Virtual do Azure: Atividade de Hive do HDInsight (BYOC-traga seu Cluster próprio), atividade de Pig do HDInsight (BYOC), atividade de MapReduce do HDInsight (BYOC), atividade do Spark do HDInsight (BYOC), transmissão em fluxo do HDInsight atividade (BYOC), atividade execução de lote do Machine Learning, Machine Learning Atividades do recurso, atividade de procedimento armazenado, atividade U-SQL do Data Lake Analytics, atividade personalizada .NET, atividade de pesquisa e atividade obter metadados de atualização.

> [!NOTE] 
> Utilize o runtime de integração autoalojado para suportar arquivos de dados que requeiram que utilize o seu próprio controlador, como SAP Hana, MySQL, etc.  Para obter mais informações, veja [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) (Arquivos de dados suportados).

### <a name="self-hosted-ir-network-environment"></a>Ambiente de rede de IR autoalojado
Se quiser efetuar a integração de dados de forma segura num ambiente de rede privada, que não tem um campo de visão a partir do ambiente de rede pública, pode instalar um IR autoalojado no ambiente no local atrás da firewall emprsarial ou dentro de uma rede privada virtual.  O runtime de integração autoalojado só faz ligações de saída baseadas em HTTP para a Internet aberta.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR autoalojado
O IR autoalojado tem de ser instalado num computador no local ou numa máquina virtual que esteja numa rede privada. Atualmente, só suportamos a execução dos runtimes de integração autoalojados em sistemas operativos Windows.  

Para elevada disponibilidade e escalabilidade, pode aumentar horizontalmente o IR autoalojado ao associar a instância lógica a vários computadores no local no modo ativo-ativo.  Para obter mais informações, veja o artigo sobre como criar e configurar um IR autoalojado nos guias de procedimentos.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime
Para fazer a migração lift and shift de cargas de trabalho do SSIS existentes, pode criar um runtime de integração Azure-SSIS para executar nativamente pacotes do SSIS.

### <a name="azure-ssis-ir-network-environment"></a>Ambiente de rede de IR do Azure-SSIS
O runtime de integração Azure-SSIS pode ser aprovisionado na rede pública ou numa rede privada.  O acesso aos dados no local é suportado mediante a associação do runtime de integração Azure-SSIS a uma Rede Virtual que esteja ligada à sua rede no local.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure-SSIS
O runtime de integração Azure-SSIS é um cluster totalmente gerido de VMs do Azure dedicadas à execução dos pacotes do SSIS. Pode dar o seu próprio servidor de base de dados do Azure SQL ou à instância gerida para alojar o catálogo dos projetos/pacotes SSIS (SSISDB) do que vai ser ligados ao mesmo. Pode aumentar verticalmente o poder da computação ao especificar o tamanho do nó e aumentá-lo horizontalmente ao definir o número de nós no cluster. Pode gerir o custo da execução do Azure-SSIS Integration Runtime ao pará-lo e iniciá-lo, conforme achar mais adequado.

Para obter mais informações, veja o artigo “how to create and configure Azure-SSIS IR” (“Como criar e configurar o runtime de integração Azure-SSIS”) nos guias de procedimentos.  Depois de criado, pode implementar e gerir os seus pacotes do SSIS com poucas ou nenhumas alterações através de ferramentas familiares, como o SQL Server Data Tools (SSDT) e o SQL Server Management Studio (SSMS), tal e qual como utilizaria o SSIS no local.

Para obter mais informações sobre o runtime Azure-SSIS, veja os artigos seguintes: 

- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo disponibiliza instruções passo a passo para criar um IR Azure-SSIS e utiliza uma base de dados da Base de Dados SQL do Azure para alojar o catálogo do SSIS. 
- [How to: Criar um runtime de integração Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo é uma continuação do tutorial e fornece instruções sobre como utilizar a instância gerida da base de dados SQL do Azure e associar o IR a uma rede virtual. 
- [Monitorizar um Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentá-lo horizontalmente mediante a adição de mais nós ao mesmo. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure. Também descreve os passos para utilizar o portal do Azure para configurar uma rede virtual, de modo a que o IR Azure-SSIS se possa associar à mesma. 

## <a name="integration-runtime-location"></a>Localização do runtime de integração
É na localização do Data Factory que os metadados da fábrica de dados são armazenados e a partir de onde o acionamento do pipeline é iniciado. Entretanto, uma fábrica de dados pode aceder aos arquivos de dados e serviços de computação noutras regiões do Azure para mover dados entre os arquivos de dados ou processar dados com serviços de computação. Este comportamento é realizado através do [IR globalmente disponível](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade dos dados, a eficiência e custos de saída de rede reduzidos.

A localização do runtime de integração define a localização da respetiva computação de back-end e, essencialmente, a localização onde são realizados o movimento de dados, a distribuição de atividades e a execução de pacotes do SSIS. A localização do runtime de integração pode ser diferente da localização da fábrica de dados à qual pertence. 

### <a name="azure-ir-location"></a>Localização do IR do Azure
Pode definir uma determinada localização de um IR do Azure, em cujo caso o movimento de dados ou emissão de atividade ocorrem nessa região específica. 

Se optar por utilizar o RI do Azure de resolução automática, que é a predefinição, 

- Para a atividade de cópia, o ADF vai fazer o melhor esforço para detetar automaticamente o seu sink e arquivo de dados de origem para escolher a melhor localização na mesma região, se disponível, ou a mais próxima na mesma geografia; ou se não for detetável, utilizar a mesma região de fábrica de dados como alternativa.
- Para a execução da atividade de Pesquisa/GetMetadata e envio da atividade de transformação, o ADF vai utilizar o IR na região da fábrica de dados.

Pode monitorizar que localização de IR entra em efeito durante a execução da atividade na vista de monitorização da atividade do pipeline no payload de monitorização de atividade ou na IU.

>[!TIP]
>Se tiver requisitos de conformidade de dados estritos e precisar de garantir que os dados não saem de uma detemrinada geografia, pode criar explicitamente um IR do Azure numa determinada região e apontar o Serviço Ligado a esse RI ao utilizar a propriedade ConnectVia. Por exemplo, se quiser copiar dados do Blob no Sul do Reino Unido para o SQL DW no Sul do Reino Unido e quiser garantir que os dados não saem do Reino Unido, crie um IR do Azure no Sul do Reino Unido e ligue ambos os Serviços Ligados a este IR.

### <a name="self-hosted-ir-location"></a>Localização do IR autoalojado
O IR autoalojado é registado logicamente no Data Factory e a computação utilizada para suportar as funcionalidades do mesmo é fornecida por si. Por esse motivo, não existe uma propriedade de localização explícita para o runtime de integração autoalojado. 

Quando é utilizado para realizar o movimento de dados, o IR autoalojado extrai dados da origem e escreve-os no destino.

### <a name="azure-ssis-ir-location"></a>Localização do IR do Azure-SSIS
Selecionar a localização certa para o runtime de integração Azure-SSIS é fundamental para obter um elevado desempenho nos seus fluxos de trabalho extract-transform-load (ETL).

- A localização do Runtime de integração Azure-SSIS não tem de ser a mesma da fábrica de dados, mas deve ser o mesmo que a localização do seu próprio servidor de instância de base de dados/gerida de SQL do Azure na qual o SSISDB vai ser alojado. Desta forma, o Azure-SSIS Integration Runtime pode aceder facilmente ao SSISDB sem incorrer em tráfegos excessivos entre diferentes localizações.
- Se não tiver um servidor de instância de base de dados/gerida de SQL do Azure existente para alojar o SSISDB, mas tiver origens/destinos de dados no local, deve criar um novo servidor do Azure SQL da base de dados/instância gerida na mesma localização de uma rede virtual ligada a sua rede no local.  Dessa forma, pode criar o Azure-SSIS Runtime de integração com o servidor de instância de base de dados/gerida de SQL do Azure novo e associar essa rede virtual, tudo na mesma localização, minimizando eficazmente os movimentos de dados em localizações diferentes.
- Se a localização do seu servidor do Azure SQL da base de dados/instância gerida existente onde o SSISDB está alojado não for o mesmo que a localização de uma rede virtual ligada à sua rede no local, crie primeiro o runtime de integração Azure-SSIS através de uma base de dados SQL do Azure / Geridos pelo servidor de instância e a adesão a outra rede virtual na mesma localização e, em seguida, configure uma rede virtual para a ligação de rede virtual entre diferentes localizações.

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

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Criar um integration runtime autoalojado](create-self-hosted-integration-runtime.md)
- [Criar um integration runtime do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo é uma continuação do tutorial e fornece instruções sobre como utilizar a instância gerida da base de dados SQL do Azure e associar o IR a uma rede virtual. 
