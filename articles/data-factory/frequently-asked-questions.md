---
title: 'Azure Data Factory: Perguntas frequentes '
description: Obtenha respostas para perguntas frequentes sobre a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: ba3cc376edef1e6dc8fbf859e456219a1fd3ca60
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635767"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory FAQ (FAQ do Azure Data Factory)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo fornece respostas a perguntas frequentes sobre a Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory? 
Data Factory é um serviço ETL totalmente gerido, baseado na nuvem, que automatiza o movimento e a transformação de dados. Como uma fábrica que gere equipamentos para transformar matérias-primas em bens acabados, a Azure Data Factory orquestra os serviços existentes que recolhem dados brutos e os transformam em informação pronta a usar. 

Ao utilizar a Azure Data Factory, pode criar fluxos de trabalho baseados em dados para mover dados entre as lojas de dados no local e na nuvem. E pode processar e transformar dados com fluxos de dados. A ADF também suporta motores de computação externos para transformações codificadas à mão, utilizando serviços de computação como Azure HDInsight, Azure Databricks e o tempo de execução de integração dos Serviços de Integração de Servidores SQL (SSIS). 

Com a Data Factory, pode executar o seu processamento de dados num serviço de nuvem baseado em Azure ou no seu próprio ambiente de computação auto-hospedado, como SSIS, SQL Server ou Oracle. Depois de criar um oleoduto que execute a ação que necessita, pode programar periodicamente (hora, dia ou semanais, por exemplo), agendamento de janelas de tempo ou desencadear o gasoduto a partir de uma ocorrência de evento. Para obter mais informações, veja [Introdução ao Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Fluxos de controlo e escala 
Para suportar os diversos fluxos e padrões de integração no moderno armazém de dados, a Data Factory permite uma modelação flexível do pipeline de dados. Isto implica paradigmas de programação de fluxo de controlo completo, que incluem execução condicional, ramificação em gasodutos de dados, e a capacidade de passar explicitamente parâmetros dentro e através destes fluxos. O fluxo de controlo também abrange a transformação de dados através da expedição de atividade para motores de execução externa e capacidades de fluxo de dados, incluindo o movimento de dados em escala, através da atividade Copy.

A Data Factory oferece liberdade para modelar qualquer estilo de fluxo necessário para a integração de dados e que pode ser despachado a pedido ou repetidamente em um horário. Alguns fluxos comuns que este modelo permite são:   

- Fluxos de controlo:
    - As atividades podem ser acorrentadas numa sequência dentro de um oleoduto.
    - As atividades podem ser ramificadas dentro de um oleoduto.
    - Parâmetros:
        - Os parâmetros podem ser definidos ao nível do gasoduto e os argumentos podem ser aprovados enquanto invoca o gasoduto a pedido ou a partir de um gatilho.
        - As atividades podem consumir os argumentos transmitidos para o pipeline
    - Passagem de estado personalizada:
        - As saídas de atividade, incluindo o estado, podem ser consumidas por uma atividade subsequente no oleoduto.
    - Recipientes em loop:
        - A atividade forecach irá iterar sobre uma determinada coleção de atividades em loop. 
- Fluxos baseados no gatilho:
    - Os gasodutos podem ser acionados a pedido ou por tempo de relógio de parede.
- Fluxos delta:
    - Os parâmetros podem ser usados para definir a sua marca de alta água para cópia delta enquanto se movem a dimensão ou tabelas de referência de uma loja relacional, no local ou na nuvem, para carregar os dados no lago. 

Para obter mais informações, consulte [Tutorial: Fluxos de controlo](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Dados transformados em escala com gasodutos sem código
A nova experiência de ferramentas baseada no navegador proporciona a autoria e implementação de gasodutos sem código com uma experiência moderna e interativa baseada na web.

Para desenvolvedores de dados visuais e engenheiros de dados, a UI web data Factory é o ambiente de design livre de códigos que você usará para construir oleodutos. Está totalmente integrado com o Visual Studio Online Git e proporciona integração para o CI/CD e desenvolvimento iterativo com opções de depurar.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>SDKs transversais ricos para utilizadores avançados
A Data Factory V2 fornece um rico conjunto de SDKs que podem ser usados para autor, gestão e monitorização de oleodutos utilizando o seu IDE favorito, incluindo:
* SDK Python
* PowerShell CLI
* SDK C#

Os utilizadores também podem utilizar as APIs de REST documentadas para interagir com a Data Factory V2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Desenvolvimento iterativo e depuragem utilizando ferramentas visuais
As ferramentas visuais da Azure Data Factory permitem o desenvolvimento iterativo e a depuração. Pode criar os seus oleodutos e fazer ensaios utilizando a capacidade **de Debug** na tela do gasoduto sem escrever uma única linha de código. Pode ver os resultados dos seus testes na janela **de saída** da sua tela de pipeline. Após o seu teste ter sucesso, pode adicionar mais atividades ao seu oleoduto e continuar a depurar de forma iterativa. Também pode cancelar os seus testes depois de estarem em curso. 

Não é obrigado a publicar as suas alterações no serviço de fábrica de dados antes de selecionar a **Debug** . Isto é útil em cenários em que pretende certificar-se de que as novas adições ou alterações funcionarão como esperado antes de atualizar os fluxos de trabalho da sua fábrica de dados em ambientes de desenvolvimento, teste ou produção. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Capacidade de implantar pacotes SSIS para a Azure 
Se quiser mover as suas cargas de trabalho SSIS, pode criar uma Fábrica de Dados e providenciar um tempo de integração Azure-SSIS. Um tempo de integração Azure-SSIS é um cluster totalmente gerido de VMs (nóns) Azure que se dedicam a executar os seus pacotes SSIS na nuvem. Para obter instruções passo a passo, consulte os [pacotes SSIS de implantação para o tutorial de Azure.](./tutorial-deploy-ssis-packages-azure.md) 
 
### <a name="sdks"></a>SDKs
Se é um utilizador avançado e procura uma interface programática, a Data Factory fornece um rico conjunto de SDKs que pode utilizar para autor, gerir ou monitorizar os oleodutos utilizando o seu IDE favorito. O suporte linguístico inclui .NET, PowerShell, Python e REST.

### <a name="monitoring"></a>Monitorização
Pode monitorizar as suas Fábricas de Dados através do PowerShell, SDK ou das Ferramentas de Monitorização Visual na interface do utilizador do navegador. Pode monitorizar e gerir fluxos personalizados a pedido, baseados em gatilhos e orientados pelo relógio de forma eficiente e eficaz. Cancelar as tarefas existentes, ver falhas num ápice, perfurar para obter mensagens de erro detalhadas e depurar os problemas, tudo a partir de um único painel de vidro sem o contexto de mudar ou navegar para trás e para a frente entre ecrãs. 

### <a name="new-features-for-ssis-in-data-factory"></a>Novas funcionalidades para SSIS na Data Factory
Desde o lançamento inicial de pré-visualização pública em 2017, a Data Factory adicionou as seguintes funcionalidades para sSIS:

-    Suporte para mais três configurações/variantes da Base de Dados Azure SQL para acolher a base de dados SSIS (SSISDB) de projetos/pacotes:
-    Base de Dados SQL com pontos finais de serviço de rede virtual
-    Instância Gerida do SQL
-    Conjunto elástico
-    Suporte para uma rede virtual Azure Resource Manager em cima de uma rede virtual clássica a ser depreciada no futuro, que permite injetar/aderir ao seu tempo de integração Azure-SSIS a uma rede virtual configurada para base de dados SQL com acesso de dados de serviço de rede virtual/MI/no local. Para obter mais informações, consulte também [um tempo de integração Azure-SSIS para uma rede virtual.](join-azure-ssis-integration-runtime-virtual-network.md)
-    Suporte para autenticação do Azure Ative Directory (Azure AD) e autenticação SQL para ligar ao SSISDB, permitindo a autenticação AZURE AD com a sua Data Factory identidade gerida para recursos Azure
-    Suporte para trazer a sua licença de Servidor SQL existente para obter economias de custos substanciais a partir da opção Azure Hybrid Benefit
-    Suporte para a Enterprise Edition do tempo de integração Azure-SSIS que permite utilizar funcionalidades avançadas/premium, uma interface de configuração personalizada para instalar componentes/extensões adicionais e um ecossistema parceiro. Para mais informações, consulte também [Enterprise Edition, Configuração Personalizada e Extensibilidade de SSIS em ADF.](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/) 
-    Integração mais profunda do SSIS na Data Factory que permite invocar/desencadear atividades de pacote SSIS de primeira classe em pipelines data factory e agendar através de SSMS. Para obter mais informações, consulte também [a Modernização e ampliação dos seus fluxos de trabalho ETL/ELT com atividades SSIS em oleodutos ADF.](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)


## <a name="what-is-the-integration-runtime"></a>Qual é o tempo de integração?
O tempo de integração é a infraestrutura de computação que a Azure Data Factory utiliza para fornecer as seguintes capacidades de integração de dados em vários ambientes de rede:

- **Movimento de dados** : Para o movimento de dados, o tempo de integração move os dados entre as lojas de dados de origem e destino, ao mesmo tempo que fornece suporte para conectores incorporados, conversão de formato, mapeamento de colunas e transferência de dados performante e escalável.
- **Atividades de** expedição : Para transformação, o tempo de integração proporciona capacidade para executar pacotes SSIS de forma nativa.
- **Executar pacotes SSIS** : O tempo de integração executa de forma nativa pacotes SSIS num ambiente computacional gerido do Azure. O tempo de integração também suporta o envio e monitorização de atividades de transformação em execução em uma variedade de serviços de computação, tais como Azure HDInsight, Azure Machine Learning, SQL Database e SQL Server.

Pode implementar um ou muitos casos do tempo de execução da integração, conforme necessário para mover e transformar dados. O tempo de integração pode funcionar numa rede pública Azure ou numa rede privada (no local, rede virtual Azure ou nuvem privada virtual da Amazon Web Services [VPC]). 

Para obter mais informações, veja [Integration runtime no Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Qual é o limite para o número de períodos de integração?
Não há um limite rígido para o número de casos de integração que se possa ter numa fábrica de dados. Existe, no entanto, um limite para o número de núcleos VM que o tempo de execução de integração pode utilizar por subscrição para a execução do pacote SSIS. Para mais informações, consulte [os limites da Data Factory.](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Quais são os conceitos de alto nível da Azure Data Factory?
Uma subscrição do Azure pode ter uma ou mais instâncias do Azure Data Factory (ou fábricas de dados). A Azure Data Factory contém quatro componentes-chave que funcionam em conjunto como uma plataforma na qual pode compor fluxos de trabalho baseados em dados com passos para mover e transformar dados.

### <a name="pipelines"></a>Pipelines
Uma fábrica de dados pode ter um ou mais pipelines. Um oleoduto é um agrupamento lógico de atividades para realizar uma unidade de trabalho. Em conjunto, as atividades num pipeline executam uma tarefa. Por exemplo, um pipeline pode conter um grupo de atividades que ingerem dados de uma bolha Azure e, em seguida, executar uma consulta de Hive em um cluster HDInsight para dividir os dados. O benefício é que você pode usar um oleoduto para gerir as atividades como um conjunto em vez de ter que gerir cada atividade individualmente. Pode acorrentar as atividades num oleoduto para as operar sequencialmente, ou pode operá-las de forma independente, em paralelo.

### <a name="data-flows"></a>Fluxos de dados
Os fluxos de dados são objetos que constrói visualmente na Data Factory que transformam dados em escala nos serviços backend Spark. Não é preciso compreender a programação ou os internos da Spark. Basta desenhar a sua intenção de transformação de dados usando gráficos (Mapeamento) ou folhas de cálculo (Wrangling).

### <a name="activities"></a>Atividades
As atividades representam uma fase de processamento num pipeline. Por exemplo, pode utilizar uma atividade Copy para copiar dados de uma loja de dados para outra loja de dados. Da mesma forma, você pode usar uma atividade de Hive, que executa uma consulta de Hive em um cluster Azure HDInsight para transformar ou analisar seus dados. O Data Factory suporta três tipos de atividades: atividades de movimento de dados, atividades de transformação de dados e atividades de controlo.

### <a name="datasets"></a>Conjuntos de dados
Os conjuntos de dados representam estruturas de dados nos arquivos de dados, que simplesmente apontam ou referenciam os dados que pretende utilizar nas suas atividades como entrada ou saída. 

### <a name="linked-services"></a>Serviços ligados
Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Pense desta forma: Um serviço ligado define a ligação à fonte de dados, e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço ligado ao Azure Storage especifica o fio de ligação para ligar à conta de Armazenamento Azure. E um conjunto de dados de bolhas Azure especifica o recipiente blob e a pasta que contém os dados.

Os serviços ligados têm duas finalidades na Data Factory:

- Para representar uma *loja de dados* que inclui, mas não se limita a, uma instância do SQL Server, uma instância de base de dados da Oracle, uma partilha de ficheiros ou uma conta de armazenamento Azure Blob. Para obter uma lista de lojas de dados suportadas, consulte [a Copy Activity na Azure Data Factory](copy-activity-overview.md).
- Para representar um *recurso de computação* que pode alojar a execução de uma atividade. Por exemplo, a atividade da Colmeia HDInsight funciona num cluster hdInsight Hadoop. Para obter uma lista de atividades de transformação e ambientes de computação suportados, consulte [os dados da Transform na Azure Data Factory.](transform-data.md)

### <a name="triggers"></a>Acionadores
Os gatilhos representam unidades de processamento que determinam quando uma execução do gasoduto é iniciada. Existem diferentes tipos de acionadores para diferentes tipos de eventos. 

### <a name="pipeline-runs"></a>Execuções de pipeline
Um gasoduto é um exemplo de execução de gasoduto. Normalmente, instantaneamente, um gasoduto funciona através da passagem de argumentos para os parâmetros definidos no oleoduto. Pode passar os argumentos manualmente ou dentro da definição de gatilho.

### <a name="parameters"></a>Parâmetros
Os parâmetros são pares de valor-chave numa configuração apenas de leitura.Define-se os parâmetros num oleoduto e passa-se os argumentos para os parâmetros definidos durante a execução a partir de um contexto de execução. O contexto de execução é criado por um gatilho ou a partir de um gasoduto que executa manualmente. As atividades dentro do pipeline consomem os valores dos parâmetros.

Um conjunto de dados é um parâmetro fortemente digitado e uma entidade que pode reutilizar ou referenciar. Uma atividade pode referenciar conjuntos de dados, e pode consumir as propriedades que são definidas na definição de conjunto de dados.

Um serviço ligado é também um parâmetro fortemente digitado que contém informações de ligação a uma loja de dados ou a um ambiente computacional. É também uma entidade que pode reutilizar ou referenciar.

### <a name="control-flows"></a>Fluxos de controlo
Os fluxos de controlo orquestram atividades de gasoduto que incluem atividades de acorrentamento numa sequência, ramificação, parâmetros que define ao nível do gasoduto, e argumentos que você passa enquanto invoca o pipeline a pedido ou a partir de um gatilho. Os fluxos de controlo também incluem recipientes de passagem e loop de estado personalizados (isto é, iteradores de foreach).


Para obter mais informações sobre os conceitos do Data Factory, veja os artigos seguintes:

- [Dataset and linked services](concepts-datasets-linked-services.md) (Conjuntos de dados e serviços ligados)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Runtime de integração](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Qual é o modelo de preços para a Data Factory?
Para obter detalhes sobre os preços da Azure Data Factory, consulte [os detalhes dos preços da Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Como posso manter-me atualizado com informações sobre a Data Factory?
Para obter as informações mais atualizadas sobre a Azure Data Factory, aceda aos seguintes sites:

- [Blogue](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Página inicial da documentação](./index.yml)
- [Página inicial do produto](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Mergulho profundo técnico 

### <a name="how-can-i-schedule-a-pipeline"></a>Como posso agendar um oleoduto? 
Pode utilizar o gatilho do programador ou o gatilho da janela de tempo para agendar um gasoduto. O gatilho utiliza um calendário de relógio de parede, que pode agendar os gasodutos periodicamente ou em padrões recorrentes baseados no calendário (por exemplo, às segundas-feiras às 18:00 e quintas-feiras às 21:00). Para mais informações, consulte [Execuções de pipelines e acionadores](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Posso passar os parâmetros para uma corrida de gasodutos?
Sim, os parâmetros são um conceito de primeira classe, de alto nível na Data Factory. Pode definir parâmetros ao nível do gasoduto e passar argumentos à medida que executa o gasoduto a pedido ou utilizando um gatilho.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Posso definir valores padrão para os parâmetros do pipeline? 
Sim. Pode definir valores predefinidos para os parâmetros nos oleodutos. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Pode uma atividade num oleoduto consumir argumentos que são passados para um gasoduto? 
Sim. Cada atividade dentro do oleoduto pode consumir o valor do parâmetro que é passado para o oleoduto e correr com a `@parameter` construção. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Pode uma propriedade de produção de atividade ser consumida em outra atividade? 
Sim. Uma produção de atividade pode ser consumida numa atividade subsequente com a `@activity` construção.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Como lido graciosamente com valores nulos numa produção de atividade? 
Pode utilizar a `@coalesce` construção das expressões para lidar graciosamente com valores nulos. 

## <a name="mapping-data-flows"></a>Fluxos de dados de mapeamento

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Preciso de ajuda para resolver a minha lógica de fluxo de dados. Que informação preciso de fornecer para conseguir ajuda?

Quando a Microsoft fornecer ajuda ou resolução de problemas com fluxos de dados, por favor forneça o Script do Fluxo de Dados. Este é o script por trás do código do seu gráfico de fluxo de dados. A partir do UI ADF, abra o fluxo de dados e, em seguida, clique no botão "Script" no canto superior direito. Copie e cole este script ou guarde-o num ficheiro de texto.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Como posso aceder aos dados utilizando os outros 90 tipos de conjuntos de dados na Data Factory?

A funcionalidade de fluxo de dados de mapeamento permite atualmente a Azure SQL Database, Azure Synapse Analytics (anteriormente SQL Data Warehouse), ficheiros de texto delimitados do armazenamento de Azure Blob ou Azure Data Lake Storage Gen2, e ficheiros Parquet do armazenamento blob ou data lake storage Gen2 nautócton para fonte e pia. 

Utilize a atividade Copy para encenar dados de qualquer um dos outros conectores e, em seguida, execute uma atividade de Fluxo de Dados para transformar dados depois de encenado. Por exemplo, o seu pipeline irá primeiro copiar para o armazenamento blob, e então uma atividade de Fluxo de Dados usará um conjunto de dados na fonte para transformar esses dados.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>O tempo de integração auto-organizado está disponível para fluxos de dados?

O IR auto-hospedado é uma construção de gasoduto ADF que pode utilizar com a Atividade copy para adquirir ou mover dados de e para fontes de dados on-prem ou VM baseadas em sumidouros. Encenar os dados primeiro com uma Cópia, depois Fluxo de Dados para transformação e, em seguida, uma cópia subsequente se precisar de mover esses dados transformados de volta para a loja on-prem.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>O motor de computação de fluxo de dados serve vários inquilinos?
Os agrupamentos nunca são partilhados. Garantimos o isolamento de cada trabalho executado em execuções de produção. Em caso de cenário de depuração, uma pessoa recebe um cluster, e todos os depurados irão para aquele cluster que são iniciados por esse utilizador.

## <a name="wrangling-data-flows"></a>Fluxos de dados de estrangulamento

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Quais são as regiões apoiadas para o fluxo de dados?

O fluxo de dados de estrangulamento é atualmente suportado em fábricas de dados criadas nas seguintes regiões:

* Leste da Austrália
* Canadá Central
* Índia Central
* E.U.A. Leste
* E.U.A. Leste 2
* Leste do Japão
* Europa do Norte
* Sudeste Asiático
* E.U.A. Centro-Sul
* Sul do Reino Unido
* E.U.A. Centro-Oeste
* Europa Ocidental
* E.U.A. Oeste
* E.U.A. Oeste 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Quais são as limitações e constrangimentos com o fluxo de dados?

Os nomes do conjunto de dados só podem conter caracteres alfanuméricos. As seguintes lojas de dados são suportadas:

* Conjunto de dados detexto delimitado no Armazenamento Azure Blob usando a autenticação da chave da conta
* Conjunto de dados deslimitedText no Azure Data Lake Storage gen2 usando a chave de conta ou a autenticação principal do serviço
* Conjunto de dados detexto delimitado no Azure Data Lake Storage gen1 usando a autenticação principal do serviço
* Azure SQL Database and Data Warehouse utilizando a autenticação sql. Consulte os tipos de SQL suportados abaixo. Não existe polibase ou suporte de encenação para armazém de dados.

Neste momento, a integração do Key Vault de serviço ligado não é suportada em fluxos de dados de estrangulamento.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>Qual é a diferença entre o mapeamento e os fluxos de dados?

Os fluxos de dados de mapeamento fornecem uma forma de transformar dados em escala sem que seja necessário codificar. Você pode projetar um trabalho de transformação de dados na tela do fluxo de dados construindo uma série de transformações. Comece com qualquer número de transformações de origem seguidas por passos de transformação de dados. Complete o fluxo de dados com uma pia para aterrar os seus resultados num destino. O fluxo de dados de mapeamento é ótimo para mapear e transformar dados com esquemas conhecidos e desconhecidos nos lavatórios e fontes.

Os fluxos de dados de escoamento permitem-lhe fazer uma preparação e exploração ágeis de dados usando o editor de mashup on-line de Power Query em escala através da execução de faíscas. Com o aumento dos lagos de dados, às vezes basta explorar um conjunto de dados ou criar um conjunto de dados no lago. Não estás a mapear um alvo conhecido. Os fluxos de dados de estrangulamento são utilizados para cenários de análise menos formais e baseados em modelos.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Qual é a diferença entre fluxos de dados da Plataforma de Energia e fluxos de dados?

Os fluxos de dados da Plataforma de Energia permitem que os utilizadores importem e transformem dados de uma vasta gama de fontes de dados para o Serviço Comum de Dados e para o Lago de Dados Azure para construir aplicações PowerApps, relatórios Power BI ou automações de fluxo. Os fluxos de dados da Plataforma de Energia utilizam as experiências de preparação de dados da Power Query, semelhantes às do Power BI e do Excel. Os fluxos de dados da Plataforma de Energia também permitem uma reutilização fácil dentro de uma organização e lidam automaticamente com a orquestração (por exemplo, refrescantes automaticamente fluxos de dados que dependem de outro fluxo de dados quando o primeiro é atualizado).

A Azure Data Factory (ADF) é um serviço gerido de integração de dados que permite aos engenheiros de dados e ao integrador de dados dos cidadãos criar fluxos de trabalho complexos de extração híbrida de transformação de extrato (ETL) e de transformação de carga extraída (ELT). O fluxo de dados em ADF capacita os utilizadores com um ambiente sem código e sem servidor que simplifica a preparação de dados na nuvem e escala a qualquer tamanho de dados sem necessidade de gestão de infraestruturas. Utiliza a tecnologia de preparação de dados power query (também utilizada nos fluxos de dados da Power Platform, Excel, Power BI) para preparar e moldar os dados. Construído para lidar com todas as complexidades e desafios de escala da integração de big data, os fluxos de dados de luta permitem que os utilizadores preparem rapidamente dados em escala através da execução de faíscas. Os utilizadores podem construir oleodutos de dados resilientes num ambiente visual acessível com a nossa interface baseada no navegador e deixar a ADF lidar com as complexidades da execução do Spark. Construa horários para os seus oleodutos e monitorize as suas execuções de fluxo de dados a partir do portal de monitorização ADF. Gerir facilmente a disponibilidade de dados SLAs com a rica monitorização e alertas de disponibilidade da ADF e alavancar as capacidades de integração e implementação contínuas incorporadas para economizar e gerir os seus fluxos num ambiente gerido. Estabeleça alertas e veja os planos de execução para validar que a sua lógica está a funcionar conforme planeado à medida que afina os fluxos de dados.

### <a name="supported-sql-types"></a>Tipos SQL suportados

O fluxo de dados de escriminada suporta os seguintes tipos de dados em SQL. Obterá um erro de validação para usar um tipo de dados que não é suportado.

* curto
* double
* real
* float
* char
* nchar
* varchar
* nvarchar
* número inteiro
* int
* bit
* boolean
* smallint
* tinyint
* bigint
* long
* texto
* date
* datetime
* datetime2
* hora pequena
* carimbo de data/hora
* uniqueidentifier
* xml

Outros tipos de dados serão suportados no futuro.

## <a name="next-steps"></a>Passos seguintes
Para instruções passo a passo para criar uma fábrica de dados, consulte os seguintes tutoriais:

- [Quickstart: Criar uma fábrica de dados](quickstart-create-data-factory-dot-net.md)
- [Tutorial: Copiar dados na nuvem](tutorial-copy-data-dot-net.md)