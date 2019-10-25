---
title: 'Azure Data Factory: perguntas frequentes | Microsoft Docs'
description: Obtenha respostas para perguntas frequentes sobre Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 764a4dd31125dad20f6ef23e3628d7710dba2b85
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880131"
---
# <a name="azure-data-factory-faq"></a>Perguntas frequentes Azure Data Factory
Este artigo fornece respostas para perguntas frequentes sobre Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory? 
O Data Factory é um serviço de integração de dados baseado em nuvem, totalmente gerenciado, que automatiza a movimentação e a transformação de dados. Como uma fábrica que executa equipamentos para transformar matérias-primas em bens acabados, Azure Data Factory orquestra os serviços existentes que coletam dados brutos e os transforma em informações prontas para uso. 

Usando Azure Data Factory, você pode criar fluxos de trabalho controlados por dados para mover dados entre armazenamentos de dados locais e na nuvem. E você pode processar e transformar dados usando serviços de computação, como o Azure HDInsight, Azure Data Lake Analytics e o tempo de execução de integração do SQL Server Integration Services (SSIS). 

Com o Data Factory, você pode executar o processamento de dados em um serviço de nuvem baseado no Azure ou em seu próprio ambiente de computação hospedado internamente, como SSIS, SQL Server ou Oracle. Depois de criar um pipeline que executa a ação de que você precisa, você pode agendá-lo para execução periódica (por hora, diária ou semanal, por exemplo), agendamento de janela de tempo ou disparar o pipeline de uma ocorrência de evento. Para obter mais informações, veja [Introdução ao Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Fluxos de controle e escala 
Para dar suporte a diversos fluxos e padrões de integração no data warehouse moderno, Data Factory permite a modelagem de pipeline de dados flexível. Isso envolve os paradigmas de programação de fluxo de controle total, que incluem a execução condicional, a ramificação em pipelines de dados e a capacidade de passar explicitamente os parâmetros dentro e entre esses fluxos. O fluxo de controle também abrange a transformação de dados por meio da expedição de atividades para mecanismos de execução externos e recursos de fluxo de dados, incluindo a movimentação de dados em escala, por meio da atividade de cópia.

Data Factory fornece liberdade para modelar qualquer estilo de fluxo necessário para a integração de dados e que pode ser expedido sob demanda ou repetidamente em um agendamento. Alguns fluxos comuns que esse modelo habilita são:   

- Fluxos de controle:
    - As atividades podem ser encadeadas em uma sequência dentro de um pipeline.
    - As atividades podem ser ramificadas em um pipeline.
    - Parâmetros:
        - Os parâmetros podem ser definidos no nível do pipeline e os argumentos podem ser passados enquanto você invoca o pipeline sob demanda ou de um gatilho.
        - As atividades podem consumir os argumentos transmitidos para o pipeline
    - Passagem de estado personalizado:
        - Saídas de atividade, incluindo State, podem ser consumidas por uma atividade subsequente no pipeline.
    - Contêineres de loop:
        - A atividade ForEach será iterada em uma coleção especificada de atividades em um loop. 
- Fluxos baseados em gatilho:
    - Os pipelines podem ser disparados sob demanda ou por tempo de relógio de parede.
- Fluxos Delta:
    - Os parâmetros podem ser usados para definir sua marca de água alta para cópia Delta ao mover tabelas de dimensão ou referência de um relational store, localmente ou na nuvem, para carregar os dados no Lake. 

Para obter mais informações, consulte [tutorial: fluxos de controle](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Dados transformados em escala com pipelines sem código
A nova experiência de ferramentas baseada em navegador fornece criação e implantação de pipeline sem código com uma experiência moderna e interativa baseada na Web.

Para desenvolvedores de dados visuais e engenheiros de dados, o Data Factory interface do usuário da Web é o ambiente de design sem código que você usará para criar pipelines. Ele é totalmente integrado ao Visual Studio online git e fornece integração para CI/CD e desenvolvimento iterativo com opções de depuração.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>SDKs avançados de plataforma cruzada para usuários avançados
O Data Factory v2 fornece um conjunto avançado de SDKs que podem ser usados para criar, gerenciar e monitorar pipelines usando o IDE favorito, incluindo:
* Python SDK
* CLI do PowerShell
* SDK C#

Os usuários também podem usar as APIs REST documentadas para fazer interface com Data Factory v2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Desenvolvimento iterativo e depuração usando ferramentas visuais
Azure Data Factory ferramentas visuais permitem o desenvolvimento e a depuração iterativos. Você pode criar seus pipelines e executar testes usando o recurso de **depuração** na tela de pipeline sem escrever uma única linha de código. Você pode exibir os resultados das execuções de teste na janela **saída** da tela do pipeline. Depois que a execução de teste for realizada com sucesso, você poderá adicionar mais atividades ao seu pipeline e continuar a depuração de maneira iterativa. Você também pode cancelar suas execuções de teste depois que elas estiverem em andamento. 

Não é necessário publicar suas alterações no serviço de data factory antes de selecionar **depurar**. Isso é útil em cenários em que você deseja garantir que as novas adições ou alterações funcionem conforme o esperado antes de atualizar seus data factory fluxos de trabalho em ambientes de desenvolvimento, teste ou produção. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Capacidade de implantar pacotes do SSIS no Azure 
Se você quiser mover suas cargas de trabalho do SSIS, poderá criar um Data Factory e provisionar um tempo de execução de integração do Azure-SSIS. Um tempo de execução de integração do Azure-SSIS é um cluster totalmente gerenciado de VMs do Azure (nós) que são dedicados a executar seus pacotes SSIS na nuvem. Para obter instruções passo a passo, consulte o tutorial [implantar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md) . 
 
### <a name="sdks"></a>SDKs
Se você for um usuário avançado e estiver procurando uma interface programática, Data Factory fornecerá um rico conjunto de SDKs que você pode usar para criar, gerenciar ou monitorar pipelines usando seu IDE favorito. O suporte a idiomas inclui .NET, PowerShell, Python e REST.

### <a name="monitoring"></a>Monitorização
Você pode monitorar suas fábricas de dados por meio do PowerShell, do SDK ou das ferramentas de monitoramento Visual na interface do usuário do navegador. Você pode monitorar e gerenciar fluxos personalizados sob demanda, baseados em gatilho e orientados por relógio de maneira eficiente e eficaz. Cancele as tarefas existentes, veja as falhas rapidamente, faça uma busca detalhada para obter mensagens de erro detalhadas e depure os problemas, tudo a partir de um único painel sem alternância de contexto ou navegação entre telas. 

### <a name="new-features-for-ssis-in-data-factory"></a>Novos recursos para o SSIS no Data Factory
Desde a versão prévia pública inicial em 2017, Data Factory adicionou os seguintes recursos para SSIS:

-   Suporte para mais três configurações/variantes do banco de dados SQL do Azure para hospedar o SSISDB (banco de dados SSIS) de projetos/pacotes:
-   Banco de dados SQL com pontos de extremidade de serviço de rede virtual
-   Instância gerida
-   Conjunto elástico
-   Suporte para uma rede virtual Azure Resource Manager sobre uma rede virtual clássica a ser preterida no futuro, o que permite injetar/unir seu tempo de execução de integração do Azure-SSIS a uma rede virtual configurada para o banco de dados SQL com o serviço de rede virtual acesso a dados de pontos de extremidade/MI/local. Para obter mais informações, consulte também [unir um tempo de execução de integração do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).
-   Suporte para autenticação do Azure Active Directory (Azure AD) e autenticação do SQL para se conectar ao SSISDB, permitindo a autenticação do Azure AD com sua identidade Data Factory gerenciada para recursos do Azure
-   Suporte para trazer sua própria licença de SQL Server local para obter economias de custo substanciais da opção Benefício Híbrido do Azure
-   Suporte para Enterprise Edition do tempo de execução de integração do Azure-SSIS que permite usar recursos avançados/Premium, uma interface de instalação personalizada para instalar componentes/extensões adicionais e um ecossistema de parceiros. Para obter mais informações, consulte também [Enterprise Edition, instalação personalizada e extensibilidade de terceiros para SSIS no ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-   Uma integração mais profunda do SSIS no Data Factory que permite invocar/disparar atividades de execução de pacote SSIS de primeira classe em pipelines de Data Factory e agendá-las via SSMS. Para obter mais informações, consulte também [modernizar e estender seus fluxos de trabalho ETL/ELT com atividades do SSIS em pipelines do ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>O que é o Integration Runtime?
O Integration Runtime é a infraestrutura de computação que o Azure Data Factory usa para fornecer os seguintes recursos de integração de dados em vários ambientes de rede:

- **Movimentação de dados**: para a movimentação de dados, o Integration Runtime move os dados entre os armazenamentos de dados de origem e de destino, fornecendo suporte para conectores internos, conversão de formato, mapeamento de coluna e transferência de dados escalonável e de alto desempenho.
- **Atividades de expedição**: para transformação, o Integration Runtime fornece a capacidade de executar pacotes SSIS nativamente.
- **Executar pacotes SSIS**: o Integration Runtime executa nativamente pacotes do SSIS em um ambiente de computação gerenciado do Azure. O Integration Runtime também dá suporte a expedição e monitoramento de atividades de transformação em execução em uma variedade de serviços de computação, como Azure HDInsight, Azure Machine Learning, banco de dados SQL e SQL Server.

Você pode implantar uma ou várias instâncias do tempo de execução de integração conforme necessário para mover e transformar dados. O Integration Runtime pode ser executado em uma rede pública do Azure ou em uma rede privada (local, rede virtual do Azure ou Amazon Web Services nuvem privada virtual [VPC]). 

Para obter mais informações, veja [Integration runtime no Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Qual é o limite do número de tempos de execução de integração?
Não há nenhum limite rígido no número de instâncias do Integration Runtime que você pode ter em um data factory. No entanto, há um limite no número de núcleos de VM que o tempo de execução de integração pode usar por assinatura para a execução do pacote SSIS. Para obter mais informações, consulte [limites de data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Quais são os conceitos de nível superior do Azure Data Factory?
Uma subscrição do Azure pode ter uma ou mais instâncias do Azure Data Factory (ou fábricas de dados). O Azure Data Factory contém quatro componentes principais que funcionam juntos como uma plataforma na qual você pode compor fluxos de trabalho orientados a dados com etapas para mover e transformar dados.

### <a name="pipelines"></a>Pipelines
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um agrupamento lógico de atividades para executar uma unidade de trabalho. Em conjunto, as atividades num pipeline executam tarefas. Por exemplo, um pipeline pode conter um grupo de atividades que ingerir dados de um blob do Azure e, em seguida, executar uma consulta de Hive em um cluster HDInsight para particionar os dados. O benefício é que você pode usar um pipeline para gerenciar as atividades como um conjunto, em vez de ter que gerenciar cada atividade individualmente. Você pode encadear as atividades em um pipeline para operá-las em sequência ou pode operá-las de forma independente, em paralelo.

### <a name="activities"></a>Atividades
As atividades representam uma fase de processamento num pipeline. Por exemplo, você pode usar uma atividade de cópia para copiar dados de um armazenamento de dados para outro armazenamento de dados. Da mesma forma, você pode usar uma atividade de Hive, que executa uma consulta de Hive em um cluster do Azure HDInsight para transformar ou analisar seus dados. O Data Factory suporta três tipos de atividades: atividades de movimento de dados, atividades de transformação de dados e atividades de controlo.

### <a name="datasets"></a>Conjuntos de dados
Os conjuntos de dados representam estruturas de dados nos arquivos de dados, que simplesmente apontam ou referenciam os dados que pretende utilizar nas suas atividades como entrada ou saída. 

### <a name="linked-services"></a>Serviços ligados
Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Imagine dessa forma: um serviço vinculado define a conexão com a fonte de dados, e um conjunto de dados representa a estrutura do dado. Por exemplo, um serviço vinculado do armazenamento do Azure especifica a cadeia de conexão para se conectar à conta de armazenamento do Azure. E um conjunto de dados de blob do Azure especifica o contêiner de BLOBs e a pasta que contém o dado.

Os serviços vinculados têm duas finalidades no Data Factory:

- Para representar um *armazenamento de dados* que inclui, mas não está limitado a, uma instância de SQL Server local, uma instância de banco de dado Oracle, um compartilhamento de arquivos ou uma conta de armazenamento de BLOBs do Azure. Para obter uma lista de armazenamentos de dados com suporte, consulte [atividade de cópia no Azure data Factory](copy-activity-overview.md).
- Para representar um *recurso de computação* que pode alojar a execução de uma atividade. Por exemplo, a atividade hive do HDInsight é executada em um cluster Hadoop do HDInsight. Para obter uma lista de atividades de transformação e ambientes de computação com suporte, consulte [transformar dados em Azure data Factory](transform-data.md).

### <a name="triggers"></a>Acionadores
Os gatilhos representam unidades de processamento que determinam quando uma execução de pipeline é inicializada. Existem diferentes tipos de acionadores para diferentes tipos de eventos. 

### <a name="pipeline-runs"></a>Execuções de pipeline
Uma execução de pipeline é uma instância de uma execução de pipeline. Normalmente, você instancia uma execução de pipeline passando argumentos para os parâmetros que são definidos no pipeline. Você pode passar os argumentos manualmente ou dentro da definição do gatilho.

### <a name="parameters"></a>Parâmetros
Os parâmetros são pares chave-valor em uma configuração somente leitura. Você define parâmetros em um pipeline e passa os argumentos para os parâmetros definidos durante a execução de um contexto de execução. O contexto de execução é criado por um gatilho ou de um pipeline que você executa manualmente. As atividades dentro do pipeline consomem os valores dos parâmetros.

Um conjunto de um DataSet é um parâmetro fortemente tipado e uma entidade que você pode reutilizar ou fazer referência a ele. Uma atividade pode referenciar conjuntos de os e pode consumir as propriedades que são definidas na definição do conjunto de linhas.

Um serviço vinculado também é um parâmetro fortemente tipado que contém informações de conexão para um armazenamento de dados ou um ambiente de computação. Também é uma entidade que você pode reutilizar ou fazer referência a ela.

### <a name="control-flows"></a>Fluxos de controlo
Os fluxos de controle orquestram atividades de pipeline que incluem as atividades de encadeamento em uma sequência, ramificação, parâmetros que você define no nível de pipeline e argumentos que você passa conforme você invoca o pipeline sob demanda ou de um gatilho. Os fluxos de controle também incluem passagem de estado Personalizada e contêineres de looping (ou seja, iteradores foreach).


Para obter mais informações sobre os conceitos do Data Factory, veja os artigos seguintes:

- [Dataset and linked services](concepts-datasets-linked-services.md) (Conjuntos de dados e serviços ligados)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Integration runtime](concepts-integration-runtime.md) (Runtime de integração)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Qual é o modelo de preços para Data Factory?
Para Azure Data Factory detalhes de preços, consulte [Data Factory detalhes de preços](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Como posso manter-se atualizado com as informações sobre Data Factory?
Para obter as informações mais atualizadas sobre Azure Data Factory, acesse os seguintes sites:

- [Blogue](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [home page de documentação](/azure/data-factory)
- [home page do produto](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Aprofundamento técnico 

### <a name="how-can-i-schedule-a-pipeline"></a>Como posso agendar um pipeline? 
Você pode usar o gatilho do Agendador ou o gatilho de janela de tempo para agendar um pipeline. O gatilho usa um agendamento de calendário de relógio de parede, que pode agendar pipelines periodicamente ou em padrões recorrentes baseados em calendário (por exemplo, às segundas-feiras às 6:00 PM e quintas-feiras às 9:00 PM). Para mais informações, consulte [Execuções de pipelines e acionadores](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Posso passar parâmetros para uma execução de pipeline?
Sim, os parâmetros são um conceito de primeira classe e de nível superior em Data Factory. Você pode definir parâmetros no nível do pipeline e passar argumentos ao executar a execução do pipeline sob demanda ou usando um gatilho.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Posso definir valores padrão para os parâmetros de pipeline? 
Sim. Você pode definir valores padrão para os parâmetros nos pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Uma atividade em um pipeline pode consumir argumentos que são passados para uma execução de pipeline? 
Sim. Cada atividade dentro do pipeline pode consumir o valor do parâmetro que é passado para o pipeline e executado com a construção `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Uma propriedade de saída de atividade pode ser consumida em outra atividade? 
Sim. Uma saída de atividade pode ser consumida em uma atividade subsequente com a construção `@activity`.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Como fazer tratar normalmente valores nulos em uma saída de atividade? 
Você pode usar a construção `@coalesce` nas expressões para manipular valores nulos normalmente. 

## <a name="mapping-data-flows"></a>Fluxos de dados de mapeamento

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Preciso de ajuda para solucionar problemas de minha lógica de fluxo de dados. Quais informações eu preciso fornecer para obter ajuda?

Quando a Microsoft fornece ajuda ou solução de problemas com fluxos de dados, forneça o script de fluxo de dados. Este é o script code-behind do grafo de fluxo de dados. Na interface do usuário do ADF, abra o fluxo de dados e clique no botão "script" no canto superior direito. Copie e cole esse script ou salve-o em um arquivo de texto.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Como fazer acessar dados usando os outros tipos de conjunto de dados 90 no Data Factory?

No momento, o recurso de fluxo de dados de mapeamento permite que os arquivos de texto do Azure SQL, do Azure SQL Data Warehouse, delimitados do armazenamento de BLOBs do Azure ou Azure Data Lake Storage Gen2, e parquet os arquivos do armazenamento de BLOBs ou Data Lake Storage Gen2 nativamente para origem e coletor. 

Use a atividade de cópia para preparar dados de qualquer um dos outros conectores e, em seguida, execute uma atividade de fluxo de dados para transformar dados após sua preparação. Por exemplo, seu pipeline primeiro copiará para o armazenamento de BLOBs e, em seguida, uma atividade de fluxo de dados usará um conjunto de dados na origem para transformá-los.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>O tempo de execução de integração auto-hospedado está disponível para fluxos de dados?

O IR auto-hospedado é uma construção de pipeline do ADF que você pode usar com a atividade de cópia para adquirir ou mover dados de e para fontes de dados locais ou baseadas em VM e coletores. Prepare os dados primeiro com uma cópia, depois o fluxo de dados para transformação e, em seguida, uma cópia subsequente se você precisar mover esses dados transformados de volta para o repositório local.

## <a name="next-steps"></a>Passos seguintes
Para obter instruções detalhadas sobre como criar um data factory, consulte os seguintes tutoriais:

- [Início rápido: criar um data factory](quickstart-create-data-factory-dot-net.md)
- [Tutorial: copiar dados na nuvem](tutorial-copy-data-dot-net.md)
