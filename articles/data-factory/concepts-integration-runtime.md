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
ms.date: 05/31/2019
ms.openlocfilehash: 40660c0397f8b7fd7c370e2e0f697cae26b9bb48
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927155"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration runtime no Azure Data Factory
O Integration Runtime (IR) é a infraestrutura de computação que o Azure Data Factory utiliza para proporcionar as seguintes capacidades de integração de dados em diferentes ambientes de rede:

- **Fluxo de dados**: execute um [fluxo de dados](concepts-data-flow-overview.md) no ambiente de computação gerenciado do Azure.  
- **Movimentação de dados**: copiar dados entre armazenamentos de dados em rede pública e armazenamentos de dados na rede privada (rede virtual privada ou local). Oferece suporte para conectores incorporados, conversão de formatos, mapeamento de colunas e transferência de dados dimensionável e de desempenho elevado.
- **Expedição de atividade**: despache e monitore atividades de transformação em execução em uma variedade de serviços de computação, como Azure Databricks, Azure HDInsight, Azure Machine Learning, banco de dados SQL do azure, SQL Server e muito mais.
- **Execução de pacotes do SSIS**: executar, nativamente, pacotes do SQL Server Integration Services (SSIS) num ambiente de computação gerida do Azure.

No Data Factory, as atividades definem as ações que vão ser realizadas. Os serviços ligados definem um arquivo de dados ou um serviço de computação de destino. Os runtimes de integração estabelecem a ponte entre a atividade e os serviços ligados.  Ele é referenciado pelo serviço vinculado ou pela atividade e fornece o ambiente de computação no qual a atividade é executada ou expedida do. Desta forma, a atividade pode ser realizada na região mais perto possível do arquivo de dados ou do serviço de computação de destino com o melhor desempenho possível, satisfazendo as necessidades de segurança e conformidade.

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

- Executando fluxos de dados no Azure 
- Executar atividades de cópia entre arquivos de dados na cloud
- Expedindo as seguintes atividades de transformação na rede pública: bloco de anotações do databricks/jar/Python atividade, atividade de hive do hdinsight, atividade de Pig do HDInsight, atividade de MapReduce do hdinsight, atividade do HDInsight Spark, atividade de streaming do HDInsight, atividade de Data Lake Analytics Machine Learning execução de Machine Learning, atividade da Web, atividade de pesquisa e atividade de obtenção de metadados.

### <a name="azure-ir-network-environment"></a>Ambiente de rede de IR do Azure
O Azure Integration Runtime dá suporte à conexão com armazenamentos de dados e serviços de computação com pontos de extremidade acessíveis públicos. Utilize um runtime de integração autoalojado para o ambiente Rede Virtual do Azure.

### <a name="azure-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure
Os runtimes de integração do Azure fornecem uma computação totalmente gerida e sem servidor no Azure.  Não tem de se preocupar com o aprovisionamento da infraestrutura, a instalação de software, as correções ou o dimensionamento de capacidades.  Além disso, apenas paga durante a utilização efetiva.

Os runtimes de integração do Azure proporcionam a computação nativa para mover dados entre arquivos de dados na cloud de forma segura, fiável e de elevado desempenho.  Pode definir o número de unidades de integração de dados a utilizar na atividade de cópia e o tamanho da computação do IR do Azure é aumentado verticalmente de forma elástica em conformidade, sem que tenha de ajustar explicitamente o tamanho do Runtime de Integração do Azure. 

A distribuição de atividades é uma operação simples para encaminhar a atividade para o serviço de computação de destino, para que não seja necessário aumentar verticalmente o tamanho da computação neste cenário.

Para obter informações sobre como criar e configurar um runtime de integração do Azure, veja How to create and configure Azure IR (Como criar e configurar um runtime de integração do Azure) nos guias de procedimentos. 

> [!NOTE] 
> O tempo de execução de integração do Azure tem propriedades relacionadas ao tempo de execução de fluxo de dados, que definem a infraestrutura de computação subjacente que seria usada para executar os fluxos de dados no. 

## <a name="self-hosted-integration-runtime"></a>Runtime de integração autoalojado
Os runtimes de integração autoalojados podem:

- Executar a atividade de cópia entre arquivos de dados na cloud e um arquivo de dados numa rede privada.
- Expedindo as seguintes atividades de transformação em relação aos recursos de computação no local ou na rede virtual do Azure: atividade de hive do HDInsight (BYOC-traga seu próprio cluster), BYOC (Pig de atividade do HDInsight), atividade de MapReduce do HDInsight (BYOC), HDInsight Spark atividade (BYOC), atividade de streaming do HDInsight (BYOC), atividade de execução de Machine Learning em lote, Machine Learning atualizar atividades de recurso, atividade de procedimento armazenado, atividade de Data Lake Analytics U-SQL, atividade personalizada (executada no lote do Azure), pesquisa atividade e obter atividades de metadados.

> [!NOTE] 
> Use o tempo de execução de integração auto-hospedado para dar suporte a armazenamentos de dados que exigem o seu próprio Driver, como SAP Hana, MySQL etc.  Para obter mais informações, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> O Java Runtime Environment (JRE) é uma dependência do IR hospedado internamente. Certifique-se de que o JRE está instalado no mesmo host.

### <a name="self-hosted-ir-network-environment"></a>Ambiente de rede de IR autoalojado
Se quiser efetuar a integração de dados de forma segura num ambiente de rede privada, que não tem um campo de visão a partir do ambiente de rede pública, pode instalar um IR autoalojado no ambiente no local atrás da firewall emprsarial ou dentro de uma rede privada virtual.  O runtime de integração autoalojado só faz ligações de saída baseadas em HTTP para a Internet aberta.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR autoalojado
O IR autoalojado tem de ser instalado num computador no local ou numa máquina virtual que esteja numa rede privada. Atualmente, só suportamos a execução dos runtimes de integração autoalojados em sistemas operativos Windows.  

Para elevada disponibilidade e escalabilidade, pode aumentar horizontalmente o IR autoalojado ao associar a instância lógica a vários computadores no local no modo ativo-ativo.  Para obter mais informações, consulte o artigo como [criar e configurar o ir auto-hospedado](create-self-hosted-integration-runtime.md) em como guias para obter detalhes.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime
Para fazer a migração lift and shift de cargas de trabalho do SSIS existentes, pode criar um runtime de integração Azure-SSIS para executar nativamente pacotes do SSIS.

### <a name="azure-ssis-ir-network-environment"></a>Ambiente de rede de IR do Azure-SSIS
O runtime de integração Azure-SSIS pode ser aprovisionado na rede pública ou numa rede privada.  O acesso aos dados no local é suportado mediante a associação do runtime de integração Azure-SSIS a uma Rede Virtual que esteja ligada à sua rede no local.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Recurso de computação e dimensionamento do IR do Azure-SSIS
O runtime de integração Azure-SSIS é um cluster totalmente gerido de VMs do Azure dedicadas à execução dos pacotes do SSIS. Você pode colocar seu próprio banco de dados SQL do Azure ou Instância Gerenciada servidor para hospedar o catálogo de projetos/pacotes SSIS (SSISDB) que será anexado a ele. Pode aumentar verticalmente o poder da computação ao especificar o tamanho do nó e aumentá-lo horizontalmente ao definir o número de nós no cluster. Pode gerir o custo da execução do Azure-SSIS Integration Runtime ao pará-lo e iniciá-lo, conforme achar mais adequado.

Para obter mais informações, veja o artigo “how to create and configure Azure-SSIS IR” (“Como criar e configurar o runtime de integração Azure-SSIS”) nos guias de procedimentos.  Depois de criado, pode implementar e gerir os seus pacotes do SSIS com poucas ou nenhumas alterações através de ferramentas familiares, como o SQL Server Data Tools (SSDT) e o SQL Server Management Studio (SSMS), tal e qual como utilizaria o SSIS no local.

Para obter mais informações sobre o runtime Azure-SSIS, veja os artigos seguintes: 

- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo disponibiliza instruções passo a passo para criar um IR Azure-SSIS e utiliza uma base de dados da Base de Dados SQL do Azure para alojar o catálogo do SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). Este artigo expande o tutorial e fornece instruções sobre como usar Instância Gerenciada do Banco de Dados SQL do Azure e ingressar o IR em uma rede virtual. 
- [Monitorizar um Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentá-lo horizontalmente mediante a adição de mais nós ao mesmo. 
- [Associar um IR Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure. Também descreve os passos para utilizar o portal do Azure para configurar uma rede virtual, de modo a que o IR Azure-SSIS se possa associar à mesma. 

## <a name="integration-runtime-location"></a>Localização do runtime de integração
É na localização do Data Factory que os metadados da fábrica de dados são armazenados e a partir de onde o acionamento do pipeline é iniciado. Entretanto, uma fábrica de dados pode aceder aos arquivos de dados e serviços de computação noutras regiões do Azure para mover dados entre os arquivos de dados ou processar dados com serviços de computação. Este comportamento é realizado através do [IR globalmente disponível](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade dos dados, a eficiência e custos de saída de rede reduzidos.

A localização do runtime de integração define a localização da respetiva computação de back-end e, essencialmente, a localização onde são realizados o movimento de dados, a distribuição de atividades e a execução de pacotes do SSIS. A localização do runtime de integração pode ser diferente da localização da fábrica de dados à qual pertence. 

### <a name="azure-ir-location"></a>Localização do IR do Azure
Pode definir uma determinada localização de um IR do Azure, em cujo caso o movimento de dados ou emissão de atividade ocorrem nessa região específica. 

Se você optar por usar a **resolução automática Azure ir** que é o padrão, 

- Para a atividade de cópia, o ADF vai fazer o melhor esforço para detetar automaticamente o seu sink e arquivo de dados de origem para escolher a melhor localização na mesma região, se disponível, ou a mais próxima na mesma geografia; ou se não for detetável, utilizar a mesma região de fábrica de dados como alternativa.

- Para pesquisa/GetMetadata/excluir execução de atividade (também conhecida como atividades de pipeline), expedição de atividade de transformação (também conhecida como atividades externas) e operações de criação (testar conexão, procurar lista de pastas e lista de tabelas, Visualizar dados), o ADF usará o IR na região de data factory.

- Para o fluxo de dados, o ADF usará o IR na região de data factory. 

  > [!TIP] 
  > Uma prática recomendada seria garantir que o fluxo de dados seja executado na mesma região que os armazenamentos de dados correspondentes (se possível). Você pode fazer isso por meio da resolução automática de Azure IR (se o local do repositório de dados for o mesmo Data Factory local) ou criando uma nova instância Azure IR na mesma região que os armazenamentos de dados e, em seguida, executar o fluxo de dados nele. 

Pode monitorizar que localização de IR entra em efeito durante a execução da atividade na vista de monitorização da atividade do pipeline no payload de monitorização de atividade ou na IU.

>[!TIP]
>Se tiver requisitos de conformidade de dados estritos e precisar de garantir que os dados não saem de uma detemrinada geografia, pode criar explicitamente um IR do Azure numa determinada região e apontar o Serviço Ligado a esse RI ao utilizar a propriedade ConnectVia. Por exemplo, se quiser copiar dados do Blob no Sul do Reino Unido para o SQL DW no Sul do Reino Unido e quiser garantir que os dados não saem do Reino Unido, crie um IR do Azure no Sul do Reino Unido e ligue ambos os Serviços Ligados a este IR.

### <a name="self-hosted-ir-location"></a>Localização do IR autoalojado
O IR autoalojado é registado logicamente no Data Factory e a computação utilizada para suportar as funcionalidades do mesmo é fornecida por si. Por esse motivo, não existe uma propriedade de localização explícita para o runtime de integração autoalojado. 

Quando é utilizado para realizar o movimento de dados, o IR autoalojado extrai dados da origem e escreve-os no destino.

### <a name="azure-ssis-ir-location"></a>Localização do IR do Azure-SSIS
Selecionar a localização certa para o runtime de integração Azure-SSIS é fundamental para obter um elevado desempenho nos seus fluxos de trabalho extract-transform-load (ETL).

- O local do seu Azure-SSIS IR não precisa ser o mesmo que o local do seu data factory, mas deve ser o mesmo que o local do seu próprio servidor de banco de dados SQL/Instância Gerenciada do Azure em que o SSISDB será hospedado. Desta forma, o Azure-SSIS Integration Runtime pode aceder facilmente ao SSISDB sem incorrer em tráfegos excessivos entre diferentes localizações.
- Se você não tiver um servidor de banco de dados SQL do Azure/Instância Gerenciada Server para hospedar o SSISDB, mas tiver fontes/destinos locais, você deverá criar um novo servidor de Instância Gerenciada/banco de dados SQL do Azure no mesmo local de uma rede virtual conectada à sua rede local.  Dessa forma, você pode criar seu Azure-SSIS IR usando o novo servidor de banco de dados SQL/Instância Gerenciada do Azure e unindo essa rede virtual, tudo no mesmo local, minimizando efetivamente os movimentos de os seus locais de forma eficaz.
- Se o local do seu banco de dados SQL do Azure/servidor de Instância Gerenciada existente em que o SSISDB está hospedado não for o mesmo que o local de uma rede virtual conectada à sua rede local, primeiro crie seu Azure-SSIS IR usando um servidor de banco de dados SQL do Azure/Instância Gerenciada existente e unindo outra rede virtual no mesmo local e configure uma rede virtual para a conexão de rede virtual entre diferentes locais

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

### <a name="data-flow-activity"></a>Atividade de fluxo de dados

A atividade de fluxo de dados é executada no Integration Runtime associado a ela. 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Criar tempo de execução de integração do Azure](create-azure-integration-runtime.md)
- [Criar um integration runtime autoalojado](create-self-hosted-integration-runtime.md)
- [Criar um integration runtime do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar Instância Gerenciada do Banco de Dados SQL do Azure e ingressar o IR em uma rede virtual. 
