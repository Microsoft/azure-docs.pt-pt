---
title: 'Azure Data Factory: Perguntas mais frequentes | Documentos da Microsoft'
description: Obtenha respostas para perguntas mais frequentes sobre o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: 112ff38ad4e35ac284501c5dd3881c4f340b5f9b
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984650"
---
# <a name="azure-data-factory-faq"></a>FAQ de fábrica de dados do Azure
Este artigo fornece respostas para perguntas freqüentes sobre o Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory? 
Data Factory é um serviço de integração de dados totalmente gerido, com base na cloud que automatiza o movimento e transformação de dados. Como uma fábrica que opera equipamento para transformar matérias-primas em bens acabados, o Azure Data Factory orquestra os serviços existentes que recolhem dados não processados e transformação-los em informações prontas a utilizar. 

Ao utilizar o Azure Data Factory, pode criar fluxos de trabalho condicionados por dados para mover dados entre aplicações no local e na cloud arquivos de dados. E pode processar e transformar dados através de serviços de computação como tempo de execução de integração do Azure HDInsight, o Azure Data Lake Analytics e o SQL Server Integration Services (SSIS). 

Com o Data Factory, pode executar o processamento de dados num serviço cloud baseado no Azure ou em seu próprio ambiente de computação hospedagem interna, como o SSIS, o SQL Server ou o Oracle. Depois de criar um pipeline que executa a ação que precisa, pode agendá-la para executar periodicamente (por exemplo, por hora, diário ou semanal), o agendamento da janela de tempo ou a acionar o pipeline de uma ocorrência do evento. Para obter mais informações, veja [Introdução ao Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Fluxos de controle e dimensionamento 
Para suportar os diversos fluxos e integração padrões no armazém de dados moderno, fábrica de dados permite modelagem de pipeline de dados flexível que inclui o fluxo de controlo total, incluindo a execução condicional, de paradigmas de programação de ramificação em pipelines de dados, e transmita, explicitamente, parâmetros dentro e transversalmente nesses fluxos. Fluxo de controle também abrange a transformar dados através de distribuição de atividades para os mecanismos de execução externos e os recursos de fluxo de dados, incluindo o movimento de dados em escala, através da atividade de cópia.

Data Factory fornece a liberdade de poder modelar qualquer estilo de fluxo que é necessário para integração de dados e que pode ser distribuído a pedido ou repetidamente com base numa agenda. Este modelo permite alguns fluxos comuns são:   

- Fluxos de controle:
    - Atividades de cadeia numa sequência dentro de um pipeline.
    - Atividades de ramificação dentro de um pipeline.
    - Parâmetros
        - Definir parâmetros ao nível do pipeline e passar argumentos enquanto invoca o pipeline a pedido ou a partir de um acionador.
        - As atividades podem consumir os argumentos transmitidos para o pipeline
    - Transmissão de estado personalizado
        - Os resultados das atividades, incluindo o estado, podem ser consumidos por uma atividade subsequente no pipeline.
    - Contentores de ciclo
        - For-each 
- Fluxos baseados em acionadores:
    - Os pipelines podem ser adicionados a pedido ou por hora do relógio.
- Fluxos delta:
    - Utilize parâmetros e defina o limite máximo para as cópias delta medida que move tabelas de dimensão ou de referência de um arquivo relacional, no local ou na cloud, para carregar os dados para o lake. 

Para obter mais informações, consulte [Tutorial: Controlar fluxos](tutorial-control-flow.md).

### <a name="transform-your-data-at-scale-with-code-free-pipelines"></a>Transforme os seus dados em escala com pipelines gratuito de código
A nova experiência de ferramentas baseadas no browser fornece pipeline sem código de criação e implementação com uma experiência de baseada na web moderna e interativa.

Para desenvolvedores de visual de dados e engenheiros de dados, a interface do Usuário de Web do ADF é o ambiente de design sem código que irá utilizar para criar pipelines. Ele é totalmente integrado com o Git Online do Visual Studio e fornece integração para CI/CD e desenvolvimento iterativo com opções de depuração.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Avançada em várias SDKs de plataforma para utilizadores avançados
Se for utilizador avançado e quiser uma interface programática, o ADF V2 fornece um conjunto avançado de SDKs que podem ser utilizados para criar, gerir, monitorizar pipelines com o seu IDE favorito
1.  SDK Python
2.  CLI do PowerShell
3.  C# SDK os utilizadores podem também aproveitar as APIs REST documentado, a interface com o ADF V2

### <a name="iterative-development-and-debugging-using-visual-tools"></a>Desenvolvimento iterativo e depuração usando o visual tools
Ferramentas de visual do Azure Data Factory (ADF) permitem-lhe fazer o desenvolvimento e depuração iterativos. Pode criar os seus pipelines e de teste com a capacidade de depuração na tela do pipeline sem ter de escrever uma única linha de código. Pode ver os resultados das suas execuções de teste na janela de saída da sua tela de pipeline. Assim que a execução de teste for bem sucedida, pode adicionar mais atividades ao seu pipeline e continuar a depuração de forma iterativa. Também pode cancelar suas execuções de teste quando estiverem em curso. Não tem de publicar as alterações para o serviço do data factory antes de clicar em Debug. Isso é útil em cenários em que pretende certificar-se de que a nova adições ou alterações conforme esperado antes de atualizar seus fluxos de trabalho de fábrica de dados no desenvolvimento, teste, ou ambientes de produção. 

### <a name="deploy-ssis-packages-to-azure"></a>Implementar pacotes do SSIS no Azure 
Se quiser mover as cargas de trabalho do SSIS, pode criar uma fábrica de dados e aprovisionar um runtime de integração Azure-SSIS. O runtime de integração Azure-SSIS é um cluster totalmente gerido de VMs do Azure (nós) dedicadas à execução dos pacotes SSIS na cloud. Para obter instruções passo a passo, consulte a [pacotes de SSIS implementar para o Azure](tutorial-create-azure-ssis-runtime-portal.md) tutorial. 
 
### <a name="sdks"></a>SDKs
Se for utilizador avançado e quiser uma interface programática, o ADF que fornece um conjunto avançado de SDKs que pode utilizar para criar, gerir ou monitorizar pipelines com o seu IDE preferido. Suporte de idiomas inclui o .NET, PowerShell, Python e REST.

### <a name="monitoring"></a>Monitorização
Pode monitorizar as suas fábricas de dados através do PowerShell, o SDK ou as ferramentas de monitorização Visual na interface de utilizador do browser. Pode monitorizar e gerir a pedido, o acionador com base e o relógio controlado por fluxos personalizados de maneira eficiente e eficaz. Cancelar tarefas de existentes, falhas de ver num relance, desagregar para obter mensagens de erro detalhadas e depurar os problemas tudo a partir de um único painel de vidro sem contexto mudar ou frente e para trás navegar entre ecrãs. 

### <a name="new-features-for-ssis-in-adf"></a>Novas funcionalidades do SSIS no ADF
Desde a primeira versão de pré-visualização pública em 2017, o Data Factory adicionou as seguintes funcionalidades para SSIS:

-   Suporte para três mais configurações/variantes da base de dados SQL do Azure (DB) para o catálogo de SSIS do anfitrião dos projetos/pacotes (SSISDB) do:
-   O Azure SQL DB, com pontos finais de serviço de VNet
-   Instância gerida (MI)
-   Conjunto elástico
-   Suporte para o Azure Resource Manager rede Virtual (VNet) sobre VNet clássica, que vão ser preteridos no futuro – isso permite que inserir/junção sua do Azure-SSIS Integration Runtime (IR) a uma VNet que está configurada para o Azure SQL DB com pontos finais de serviço de VNet/MI / no local acesso a dados, consulte: https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network 
-   Suporte para a autenticação do Azure Active Directory (AAD) em cima de autenticação do SQL para ligar ao seu SSISDB - este permitem-lhe utilizar a identidade de recursos do Azure gerido de autenticação do AAD com o ADF
-   Suporte para trazer a sua própria licença do SQL Server no local para obter poupanças de custos substancial da opção de Azure híbrido benefício (AHB)
-   Suporte para Enterprise Edition de IR Azure-SSIS que lhe permite utilizar as funcionalidades premium/avançadas, configuração personalizada para instalar componentes/extensões adicionais e 3º ecossistema de terceiros, consulte: https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/ 
-   Integração mais profunda do SSIS no ADF que lhe permite invocar/acionador de primeira classe atividades de executar o pacote do SSIS no ADF pipelines e agendá-las através do SSMS, consulte: https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/ 


## <a name="what-is-integration-runtime"></a>O que é o runtime de integração?
Runtime de integração é a infraestrutura de computação que é utilizada pelo Azure Data Factory para fornecer as seguintes capacidades de integração de dados em vários ambientes de rede:

- **Movimento de dados**: Para movimento de dados, o IR move os dados entre os arquivos de origem e destino dos dados, fornecendo suporte para conectores incorporados, conversão de formatos, mapeamento de colunas e transferência de dados eficiente e dimensionável.
- **Atividades de expedição**: Para transformação, o IR fornece a capacidade de executar pacotes SSIS de forma nativa.
- **Executar pacotes SSIS**: Executa nativamente pacotes do SSIS num ambiente de computação gerida do Azure. O IR também suporta a distribuição e monitorização das atividades de monitorização em execução em vários serviços de computação, com o Azure HDInsight, Azure Machine Learning, Base de Dados SQL do Azure, SQL Server, e mais.

Pode implementar uma ou mais instâncias do runtime de integração conforme necessário, para mover e transformar dados. Runtime de integração pode ser executado numa rede pública do Azure ou numa rede privada (no local, rede Virtual do Azure ou cloud privada virtual Amazon Web Services [VPC]). 

Para obter mais informações, veja [Integration runtime no Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>O que é o limite no número de runtimes de integração?
Não existe nenhum limite restritivo no número de instâncias de runtime de integração que pode ter numa fábrica de dados. No entanto, há um limite no número de núcleos VM que o runtime de integração, pode utilizar por subscrição para a execução de pacotes do SSIS. Para obter mais informações, consulte [limita a fábrica de dados](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Quais são os principais conceitos do Azure Data Factory?
Uma subscrição do Azure pode ter uma ou mais instâncias do Azure Data Factory (ou fábricas de dados). O Azure Data Factory contém quatro componentes principais que funcionam em conjunto como uma plataforma na qual pode compor fluxos de trabalho condicionados por dados com passos para mover e transformar dados.

### <a name="pipelines"></a>Pipelines
Uma fábrica de dados pode ter um ou mais pipelines. Os pipelines são agrupamentos lógicos de atividades para realizar uma unidade de trabalho. Em conjunto, as atividades num pipeline executam tarefas. Por exemplo, um pipeline pode conter um grupo de atividades que ingerem dados de um blob do Azure e, em seguida, executar uma consulta do Hive num cluster do HDInsight para particionar os dados. A vantagem é que pode utilizar um pipeline para gerir as atividades como um conjunto em vez de ter de gerir cada atividade individualmente. Em conjunto pode encadear atividades num pipeline para operá-los sequencialmente ou podem funcioná-los de forma independente, em paralelo.

### <a name="activity"></a>Atividade
As atividades representam uma fase de processamento num pipeline. Por exemplo, pode utilizar um *cópia* atividade para copiar dados de um arquivo de dados para outro armazenamento de dados. Da mesma forma, pode utilizar uma atividade de Hive, que executa uma consulta do Hive num cluster do Azure HDInsight para transformar ou analisar os seus dados. O Data Factory suporta três tipos de atividades: atividades de movimento de dados, atividades de transformação de dados e atividades de controlo.

### <a name="datasets"></a>Conjuntos de dados
Os conjuntos de dados representam estruturas de dados nos arquivos de dados, que simplesmente apontam ou referenciam os dados que pretende utilizar nas suas atividades como entrada ou saída. 

### <a name="linked-services"></a>Serviços ligados
Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Pense nesse assunto desta forma: um serviço ligado define a ligação à origem de dados e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço ligado do armazenamento do Azure Especifica a cadeia de ligação para ligar à conta de armazenamento do Azure. E um conjunto de dados de Blobs do Azure Especifica o contentor de BLOBs e a pasta que contém os dados.

Serviços ligados têm duas finalidades no Data Factory:

- Para representar uma *arquivo de dados* que inclui, mas não se limita a uma instância do SQL Server no local, uma instância de base de dados Oracle, uma partilha de ficheiros ou uma conta de armazenamento de Blobs do Azure. Para obter uma lista de arquivos de dados suportados, consulte [atividade de cópia no Azure Data Factory](copy-activity-overview.md).
- Para representar um *recurso de computação* que pode alojar a execução de uma atividade. Por exemplo, a atividade do Hive do HDInsight é executada num cluster do HDInsight Hadoop. Para obter uma lista de atividades de transformação e ambientes de computação suportados, consulte [transformar dados no Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Acionadores
Os acionadores representam unidades de processamento que determinam quando é iniciada uma execução de pipeline. Existem diferentes tipos de acionadores para diferentes tipos de eventos. 

### <a name="pipeline-runs"></a>Execuções de pipeline
Uma execução de pipeline é uma instância de uma execução de pipeline. Normalmente, instancia um passando argumentos para os parâmetros que são definidos no pipeline de execução do pipeline. Pode passar os argumentos manualmente ou na definição do acionador.

### <a name="parameters"></a>Parâmetros
Os parâmetros são pares de chave-valor numa configuração só de leitura. Definir parâmetros num pipeline e passar os argumentos para os parâmetros definidos durante a execução de um contexto de execução. O contexto de execução é criado por um acionador ou a partir de um pipeline que executar manualmente. As atividades dentro do pipeline consomem os valores dos parâmetros.

Um conjunto de dados é um parâmetro com rigidez de tipos e uma entidade que pode reutilizar ou fazer referência. Uma atividade pode referenciar conjuntos de dados e, ele possa consumir as propriedades definidas na definição do conjunto de dados.

Os serviços ligados também são um parâmetro inflexível que contém informações de ligação para um arquivo de dados ou a um ambiente de computação. Também é uma entidade que pode reutilizar ou fazer referência.

### <a name="control-flows"></a>Fluxos de controle
Fluxos de controlo orquestrar as atividades de pipeline que incluem o encadeamento de atividades numa sequência, ramificação, parâmetros que são definidos ao nível do pipeline, e argumentos que passar à medida que invocar o pipeline a pedido ou a partir de um acionador. Fluxos de controle também incluem passando de estado personalizado e contentores (ou seja, iteradores for-each) de ciclo.


Para obter mais informações sobre os conceitos do Data Factory, veja os artigos seguintes:

- [Conjunto de dados e serviços ligados](concepts-datasets-linked-services.md)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Integration runtime](concepts-integration-runtime.md) (Runtime de integração)

## <a name="what-is-the-pricing-model-for-data-factory"></a>O que é o modelo de preços do Data Factory?
Para o Azure Data Factory os detalhes dos preços, consulte [os detalhes dos preços do Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Como eu manter atualizado com informações sobre o Data Factory?
Para obter as informações mais atualizadas sobre o Azure Data Factory, vá para os seguintes sites:

- [Blogue](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Página inicial de documentação](/azure/data-factory)
- [Home page do produto](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Análise técnica aprofundada 

### <a name="how-can-i-schedule-a-pipeline"></a>Como posso agendar um pipeline? 
Pode utilizar o acionador de agendador ou acionador de janela de tempo para agendar um pipeline. O acionador utiliza uma agenda de calendário do relógio, e pode usá-lo para agendar pipelines periodicamente ou com o calendário com base em padrões recorrentes (por exemplo, semanalmente em segundas para as 18:00-feiras e Quintas-feiras 9 PM). Para mais informações, consulte [Execuções de pipelines e acionadores](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Pode passar parâmetros para uma execução de pipeline?
Sim, os parâmetros são um conceito de primeira classe, de nível superior no ADF. Pode definir parâmetros ao nível do pipeline e passar argumentos ao executar a pedido ou com um acionador de execução do pipeline.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Pode definir valores predefinidos para os parâmetros do pipeline? 
Sim. Pode definir os valores predefinidos para os parâmetros nos pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Uma atividade num pipeline pode consumir a argumentos que são transmitidos para uma execução de pipeline? 
Sim. Cada atividade no pipeline pode consumir o valor do parâmetro passado para o pipeline e executar com o `@parameter` construir. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Uma propriedade de saída da atividade pode ser consumida em outra atividade? 
Sim. Uma saída da atividade pode ser consumida numa atividade subsequente com o `@activity` construir.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Como posso resolver corretamente os valores nulos numa saída da atividade? 
Pode utilizar o `@coalesce` construir em expressões de lidar graciosamente com valores nulos. 

## <a name="mapping-data-flows"></a>Fluxos de dados de mapeamento

### <a name="which-adf-version-do-i-use-to-create-data-flows"></a>Qual é a versão ADF usar para criar fluxos de dados?
Utilize a versão do ADF V2 para criar fluxos de dados
  
### <a name="i-was-a-previous-private-preview-customer-using-data-flows-and-i-used-the-adf-v2-wdata-flows-preview-version"></a>Eu era um cliente de pré-visualização privada anterior usando os dados fluem e usei a versão de pré-visualização de fluxos do ADF V2 w/dados
Esta versão está obsoleta. Utilize o ADF V2 para fluxos de dados
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-data-flows"></a>O que mudou de pré-visualização privada para pré-visualização pública limitada nos fluxos de dados?
Já não terá de colocar os seus próprios clusters do Databricks. ADF irá gerenciar a criação do cluster e a desmontagem. Conjuntos de dados do blob e conjuntos de dados do ADLS são separados em conjuntos de dados de texto delimitado por e no Parquet. Pode continuar a utilizar ADLS & Store de BLOBs para armazenar esses arquivos. Utilize o serviço ligado apropriadas para esses mecanismos de armazenamento.

### <a name="can-i-migrate-my-private-preview-factories-to-adf-v2"></a>Posso migrar meu fábricas de pré-visualização privada para o ADF V2?

[Sim, siga as instruções aqui](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration)

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-do-you-need"></a>Preciso de ajuda de resolução de problemas de minha lógica de fluxo de dados, o que precisa?

Quando a Microsoft fornece ajuda ou resolução de problemas com os dados fluem, forneça o "DSL código Plan". Para tal, siga estes passos:

* No Designer de fluxo de dados, clique em "Código" no canto superior direito. Isso exibirá o código JSON editável para o fluxo de dados.
* A partir da vista de código, clique em "Planejar" no canto superior direito. O plano swtich de JSON para o plano de script DSL formatado.
* Copie e cole este script ou guardá-lo num arquivo de texto.

### <a name="how-do-i-access-data-using-the-other-80-dataset-types-in-adf"></a>Como posso aceder a dados com os outros 80 tipos de conjunto de dados no ADF?

Fluxo de dados atualmente permite BD SQL do Azure, Azure SQL DW, arquivos de texto delimitado por do Blob ou do ADLS e ficheiros Parquet de BLOBs ou ADLS nativamente para a origem e Sink. Utilize a atividade de cópia para testar dados n o partir de qualquer um dos outros conectores e, em seguida, executar uma atividade de fluxo de dados de mensagens em fila para transformar dados depois de ele é foi preparado. Por exemplo, o seu pipeline copiará pela primeira vez no Blob e, em seguida, uma atividade de fluxo de dados irá utilizar um conjunto de dados na origem para transformar dados.

## <a name="next-steps"></a>Passos Seguintes
Para obter instruções passo a passo Criar uma fábrica de dados, veja os tutoriais seguintes:

- [Quickstart: Criar uma fábrica de dados](quickstart-create-data-factory-dot-net.md)
- [Tutorial: Copiar dados na cloud](tutorial-copy-data-dot-net.md)
