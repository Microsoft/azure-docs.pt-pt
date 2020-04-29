---
title: 'Fábrica de Dados Azure: Perguntas frequentes '
description: Obtenha respostas a perguntas frequentes sobre a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 8d0b49b73ef6b67653fbf32db1174880a51d432d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81412950"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory FAQ (FAQ do Azure Data Factory)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo fornece respostas a perguntas frequentes sobre a Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory? 
Data Factory é um serviço ETL totalmente gerido, baseado na nuvem, integração de dados que automatiza o movimento e transformação de dados. Como uma fábrica que gere equipamentos para transformar matérias-primas em bens acabados, a Azure Data Factory orquestra serviços existentes que recolhem dados brutos e os transformam em informações prontas a usar. 

Ao utilizar a Azure Data Factory, pode criar fluxos de trabalho baseados em dados para mover dados entre as lojas de dados no local e na nuvem. E pode processar e transformar dados com Fluxos de Dados. A ADF também suporta motores de computação externos para transformações codificadas à mão, utilizando serviços de computação como O Azure HDInsight, Azure Databricks e o tempo de integração da Integração sQL Server Integration Services (SSIS). 

Com a Data Factory, pode executar o seu processamento de dados num serviço de nuvem baseado no Azure ou no seu próprio ambiente de computação auto-hospedado, como SSIS, SQL Server ou Oracle. Depois de criar um pipeline que executa a ação de que necessita, pode programar para funcionar periodicamente (hora, diariamente ou semanalmente, por exemplo), agendamento de janelas de tempo ou desencadear o gasoduto a partir de uma ocorrência de evento. Para obter mais informações, veja [Introdução ao Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Fluxos de controlo e escala 
Para suportar os diversos fluxos e padrões de integração no moderno armazém de dados, a Data Factory permite uma modelação flexível do pipeline de dados. Isto implica paradigmas de programação de fluxo de controlo total, que incluem execução condicional, ramificação em gasodutos de dados, e a capacidade de passar explicitamente parâmetros dentro e através destes fluxos. O fluxo de controlo também engloba a transformação de dados através do despacho de atividade para motores de execução externos e capacidades de fluxo de dados, incluindo o movimento de dados em escala, através da atividade copy.

Data Factory fornece liberdade para modelar qualquer estilo de fluxo que seja necessário para a integração de dados e que pode ser despachado a pedido ou repetidamente em um horário. Alguns fluxos comuns que este modelo permite são:   

- Fluxos de controlo:
    - As atividades podem ser acorrentadas numa sequência dentro de um oleoduto.
    - As atividades podem ser ramificadas dentro de um oleoduto.
    - Parâmetros:
        - Os parâmetros podem ser definidos ao nível do gasoduto e os argumentos podem ser passados enquanto invoca o gasoduto a pedido ou a partir de um gatilho.
        - As atividades podem consumir os argumentos transmitidos para o pipeline
    - Passe de estado personalizado:
        - As saídas de atividade, incluindo o estado, podem ser consumidas por uma atividade subsequente no gasoduto.
    - Recipientes em loop:
        - A atividade forcada irá iterar sobre uma determinada coleção de atividades em loop. 
- Fluxos baseados no gatilho:
    - Os gasodutos podem ser acionados a pedido ou por hora de parede.
- Fluxos delta:
    - Os parâmetros podem ser usados para definir a sua marca de alta água para cópia delta enquanto se movem dimensão ou tabelas de referência de uma loja relacional, quer no local quer na nuvem, para carregar os dados no lago. 

Para mais informações, consulte [Tutorial: Control flows](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Dados transformados à escala com gasodutos isentos de código
A nova experiência de ferramentas baseada no navegador fornece a autoria e implementação de pipeline sem código com uma experiência moderna e interativa baseada na web.

Para desenvolvedores de dados visuais e engenheiros de dados, a Data Factory web UI é o ambiente de design sem código que você vai usar para construir oleodutos. Está totalmente integrado com o Visual Studio Online Git e proporciona integração para CI/CD e desenvolvimento iterativo com opções de depuração.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>SDKs de plataforma cruzada ricas para utilizadores avançados
Data Factory V2 fornece um conjunto rico de SDKs que podem ser usados para autor, gerir e monitorizar oleodutos usando o seu IDE favorito, incluindo:
* SDK Python
* PowerShell CLI
* SDK C#

Os utilizadores também podem utilizar as APIs REST documentadas para interagir com data Factory V2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Desenvolvimento iterativo e depuração utilizando ferramentas visuais
As ferramentas visuais da Azure Data Factory permitem o desenvolvimento iterativo e a depuração. Pode criar os seus oleodutos e fazer testes utilizando a capacidade **de Debug** na tela do gasoduto sem escrever uma única linha de código. Pode ver os resultados dos seus testes na janela **de saída** da sua tela de gasoduto. Após o sucesso do seu teste, pode adicionar mais atividades ao seu pipeline e continuar a depurar-se de forma iterativa. Também pode cancelar os testes depois de estarem em curso. 

Não é obrigado a publicar as suas alterações no serviço de fábrica de dados antes de selecionar **o Debug**. Isto é útil em cenários em que pretende certificar-se de que as novas adições ou alterações funcionarão como esperado antes de atualizar os fluxos de trabalho da sua fábrica de dados em ambientes de desenvolvimento, teste ou produção. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Capacidade de implantar pacotes SSIS para o Azure 
Se quiser mover as suas cargas de trabalho SSIS, pode criar uma Fábrica de Dados e fornecer um tempo de execução de integração Azure-SSIS. Um tempo de funcionamento de integração Azure-SSIS é um conjunto totalmente gerido de VMs Azure (nós) que se dedicam a executar os seus pacotes SSIS na nuvem. Para obter instruções passo a passo, consulte os [pacotes SSIS de implantação para o tutorial Azure.](tutorial-create-azure-ssis-runtime-portal.md) 
 
### <a name="sdks"></a>SDKs
Se é um utilizador avançado e procura uma interface programática, a Data Factory fornece um conjunto rico de SDKs que pode utilizar para autor, gerir ou monitorizar os gasodutos utilizando o seu IDE favorito. O suporte linguístico inclui .NET, PowerShell, Python e REST.

### <a name="monitoring"></a>Monitorização
Pode monitorizar as suas Fábricas de Dados através do PowerShell, SDK ou das Ferramentas de Monitorização Visual na interface do utilizador do navegador. Você pode monitorizar e gerir fluxos personalizados on-demand, baseados em gatilhos e orientados pelo relógio de forma eficiente e eficaz. Cancele as tarefas existentes, veja falhas num ápice, desperte para obter mensagens de erro detalhadas e desinveja os problemas, tudo a partir de um único painel de vidro sem mudar de contexto ou navegar para trás e para a frente entre ecrãs. 

### <a name="new-features-for-ssis-in-data-factory"></a>Novas funcionalidades para o SSIS na Fábrica de Dados
Desde o lançamento inicial da pré-visualização pública em 2017, data Factory adicionou as seguintes funcionalidades para o SSIS:

-    Suporte para mais três configurações/variantes da Base de Dados SQL Azure para acolher a base de dados SSIS (SSISDB) de projetos/pacotes:
-    Base de Dados SQL com pontos finais de serviço de rede virtual
-    Instância gerida
-    Conjunto elástico
-    Suporte para uma rede virtual do Gestor de Recursos Azure para além de uma rede virtual clássica a ser depreciada no futuro, o que permite injetar/aderir ao seu tempo de integração Azure-SSIS a uma rede virtual configurada para base de dados SQL com pontos finais de serviço de rede virtual/acesso de dados MI/on-local. Para mais informações, consulte também Adere a um tempo de execução de [integração Azure-SSIS para uma rede virtual.](join-azure-ssis-integration-runtime-virtual-network.md)
-    Suporte para autenticação de Diretório Ativo Azure (Azure AD) e autenticação SQL para ligar ao SSISDB, permitindo a autenticação adada com a sua Fábrica de Dados gerida identidade para recursos Azure
-    Suporte para trazer a sua própria licença SQL Server no local para obter poupanças de custos substanciais da opção Benefício Híbrido Azure
-    Suporte para A Edição Empresarial do tempo de execução de integração Azure-SSIS que lhe permite utilizar funcionalidades avançadas/premium, uma interface de configuração personalizada para instalar componentes/extensões adicionais e um ecossistema parceiro. Para mais informações, consulte também [a Enterprise Edition, Custom Setup e a 3ª Extensibility de Festas para O SSIS em ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-    Uma integração mais profunda do SSIS na Fábrica de Dados que permite invocar/desencadear atividades do Pacote Execute SSIS em pipelines de Fábrica de Dados e programá-las através de SSMS. Para mais informações, consulte também modernizar e alargar os seus fluxos de [trabalho ETL/ELT com atividades SSIS em pipelines ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Qual é o tempo de execução da integração?
O tempo de execução da integração é a infraestrutura computacional que a Azure Data Factory utiliza para fornecer as seguintes capacidades de integração de dados em vários ambientes de rede:

- **Movimento de dados**: Para o movimento de dados, o tempo de execução da integração move os dados entre as lojas de dados de origem e destino, ao mesmo tempo que fornece suporte para conectores incorporados, conversão de formato, mapeamento de colunas e transferência de dados executante e escalável.
- **Atividades de expedição**: Para transformação, o tempo de execução da integração proporciona capacidade para executar de forma nativa os pacotes SSIS.
- **Execute pacotes SSIS**: O tempo de integração executa de forma nativa os pacotes SSIS num ambiente de computação Azure gerido. O tempo de funcionamento da integração também suporta a despachação e monitorização de atividades de transformação em execução em uma variedade de serviços computacionais, tais como Azure HDInsight, Azure Machine Learning, SQL Database e SQL Server.

Pode implementar um ou muitos casos do tempo de execução da integração, conforme necessário para mover e transformar dados. O tempo de integração pode funcionar numa rede pública Azure ou numa rede privada (no local, Rede Virtual Azure ou nuvem privada virtual da Amazon Web Services [VPC]). 

Para obter mais informações, veja [Integration runtime no Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Qual é o limite do número de tempos de integração?
Não existe um limite difícil para o número de casos de tempo de integração que se pode ter numa fábrica de dados. Existe, no entanto, um limite para o número de núcleos VM que o tempo de execução de integração pode ser utilizado por subscrição para a execução de pacotes SSIS. Para mais informações, consulte [os limites da Fábrica](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)de Dados .

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Quais são os conceitos de topo da Azure Data Factory?
Uma subscrição do Azure pode ter uma ou mais instâncias do Azure Data Factory (ou fábricas de dados). A Azure Data Factory contém quatro componentes-chave que funcionam em conjunto como uma plataforma na qual pode compor fluxos de trabalho orientados por dados com passos para mover e transformar dados.

### <a name="pipelines"></a>Pipelines
Uma fábrica de dados pode ter um ou mais pipelines. Um oleoduto é um agrupamento lógico de atividades para realizar uma unidade de trabalho. Em conjunto, as atividades num pipeline executam tarefas. Por exemplo, um pipeline pode conter um grupo de atividades que ingebem dados de uma bolha Azure e, em seguida, executar uma consulta de Colmeia em um cluster HDInsight para dividir os dados. O benefício é que você pode usar um pipeline para gerir as atividades como um conjunto em vez de ter que gerir cada atividade individualmente. Pode acorrentar as atividades num oleoduto para as operar sequencialmente, ou pode operá-las de forma independente, paralelamente.

### <a name="data-flows"></a>Fluxos de dados
Os fluxos de dados são objetos que se constroem visualmente na Data Factory que transformam dados em escala nos serviços de Backend Spark. Não precisa entender a programação ou os internos spark. Basta desenhar a sua intenção de transformação de dados usando gráficos (Mapeamento) ou folhas de cálculo (Wrangling).

### <a name="activities"></a>Atividades
As atividades representam uma fase de processamento num pipeline. Por exemplo, pode utilizar uma atividade do Copy para copiar dados de uma loja de dados para outra loja de dados. Da mesma forma, você pode usar uma atividade da Colmeia, que executa uma consulta de Colmeia em um cluster Azure HDInsight para transformar ou analisar os seus dados. O Data Factory suporta três tipos de atividades: atividades de movimento de dados, atividades de transformação de dados e atividades de controlo.

### <a name="datasets"></a>Conjuntos de dados
Os conjuntos de dados representam estruturas de dados nos arquivos de dados, que simplesmente apontam ou referenciam os dados que pretende utilizar nas suas atividades como entrada ou saída. 

### <a name="linked-services"></a>Serviços ligados
Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Pense desta forma: Um serviço ligado define a ligação à fonte de dados, e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço ligado ao Armazenamento Azure especifica a cadeia de ligação para ligar à conta de Armazenamento Azure. E um conjunto de dados de blob Azure especifica o recipiente blob e a pasta que contém os dados.

Os serviços ligados têm duas finalidades na Fábrica de Dados:

- Para representar uma loja de *dados* que inclua, mas não se limita a, uma instância de SQL Server no local, uma instância de base de dados Oracle, uma partilha de ficheiros ou uma conta de armazenamento Azure Blob. Para obter uma lista de lojas de dados suportadas, consulte [copy activity in Azure Data Factory](copy-activity-overview.md).
- Para representar um *recurso de computação* que pode alojar a execução de uma atividade. Por exemplo, a atividade da Hive HDInsight funciona num cluster De Hadoop HDInsight. Para obter uma lista de atividades de transformação e ambientes computacionais suportados, consulte [a Transform dados na Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Acionadores
Os gatilhos representam unidades de processamento que determinam quando uma execução de gasoduto é iniciada. Existem diferentes tipos de acionadores para diferentes tipos de eventos. 

### <a name="pipeline-runs"></a>Execuções de pipeline
Uma execução de gasoduto é um exemplo de execução de um oleoduto. Normalmente, você instantaneamente um gasoduto executado passando argumentos para os parâmetros que são definidos no oleoduto. Pode passar os argumentos manualmente ou dentro da definição do gatilho.

### <a name="parameters"></a>Parâmetros
Os parâmetros são pares de valor-chave numa configuração apenas para leitura.Define-se parâmetros num oleoduto e passa-se os argumentos para os parâmetros definidos durante a execução a partir de um contexto de execução. O contexto de execução é criado por um gatilho ou a partir de um oleoduto que executa manualmente. As atividades dentro do pipeline consomem os valores dos parâmetros.

Um conjunto de dados é um parâmetro fortemente digitado e uma entidade que pode reutilizar ou fazer referência. Uma atividade pode referenciar conjuntos de dados, e pode consumir as propriedades que são definidas na definição de conjunto de dados.

Um serviço ligado é também um parâmetro fortemente dactilografado que contém informações de ligação a uma loja de dados ou a um ambiente de cálculo. É também uma entidade que pode reutilizar ou fazer referência.

### <a name="control-flows"></a>Fluxos de controlo
Os fluxos de controlo orquestram atividades de gasoduto que incluem atividades de acorrentar numa sequência, ramificação, parâmetros que define ao nível do gasoduto, e argumentos que você passa à medida que invoca o oleoduto a pedido ou a partir de um gatilho. Os fluxos de controlo também incluem contentores de passagem e looping de estado personalizados (isto é, forcadas iteradoras).


Para obter mais informações sobre os conceitos do Data Factory, veja os artigos seguintes:

- [Dataset and linked services](concepts-datasets-linked-services.md) (Conjuntos de dados e serviços ligados)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Runtime de integração](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Qual é o modelo de preços para data factory?
Para obter detalhes sobre os preços da Fábrica de Dados Azure, consulte os detalhes dos [preços da Fábrica](https://azure.microsoft.com/pricing/details/data-factory/)de Dados .

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Como posso manter-me atualizado com informações sobre a Fábrica de Dados?
Para obter as informações mais atualizadas sobre a Azure Data Factory, vá aos seguintes sites:

- [Blogue](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Página inicial da documentação](/azure/data-factory)
- [Página inicial do produto](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Mergulho profundo técnico 

### <a name="how-can-i-schedule-a-pipeline"></a>Como posso agendar um oleoduto? 
Pode utilizar o gatilho do programador ou o gatilho da janela de tempo para agendar um gasoduto. O gatilho usa um calendário de calendário sem horas, que pode agendar oleodutos periodicamente ou em padrões recorrentes baseados em calendário (por exemplo, às segundas-feiras às 18:00 e quintas-feiras às 21:00). Para mais informações, consulte [Execuções de pipelines e acionadores](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Posso passar parâmetros para uma corrida de gasodutos?
Sim, os parâmetros são um conceito de primeira classe, de alto nível na Fábrica de Dados. Pode definir parâmetros ao nível do gasoduto e passar argumentos à medida que executa o gasoduto executado a pedido ou utilizando um gatilho.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Posso definir valores padrão para os parâmetros do gasoduto? 
Sim. Pode definir valores predefinidos para os parâmetros dos gasodutos. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Pode uma atividade num oleoduto consumir argumentos que são passados para uma corrida de gasodutos? 
Sim. Cada atividade dentro do oleoduto pode consumir o valor do parâmetro `@parameter` que passou para o oleoduto e correr com a construção. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Uma propriedade de produção de atividade pode ser consumida em outra atividade? 
Sim. Uma saída de atividade pode ser consumida numa atividade subsequente com a `@activity` construção.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Como lido graciosamente com valores nulos numa saída de atividade? 
Pode utilizar `@coalesce` a construção nas expressões para lidar com valores nulos graciosamente. 

## <a name="mapping-data-flows"></a>Fluxos de dados de mapeamento

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Preciso de ajuda para resolver a minha lógica de fluxo de dados. Que informações preciso de dar para obter ajuda?

Quando a Microsoft fornecer ajuda ou resolução de problemas com fluxos de dados, por favor forneça o Script de Fluxo de Dados. Este é o script por trás do código do seu gráfico de fluxo de dados. A partir da UI ADF, abra o fluxo de dados e, em seguida, clique no botão "Script" no canto superior direito. Copie e cole este script ou guarde-o num ficheiro de texto.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Como posso aceder aos dados utilizando os outros 90 tipos de conjuntos de dados na Data Factory?

A funcionalidade de fluxo de dados de mapeamento permite atualmente a Base de Dados Azure SQL, o Azure SQL Data Warehouse, ficheiros de texto delimitados do armazenamento Azure Blob ou do Azure Data Lake Storage Gen2, e ficheiros Parquet do armazenamento Blob ou Data Lake Storage Gen2 de forma nativa para fonte e pia. 

Utilize a atividade copy para encenar dados de qualquer um dos outros conectores e, em seguida, execute uma atividade de Fluxo de Dados para transformar dados após a sua encenação. Por exemplo, o seu pipeline irá primeiro copiar para o armazenamento blob, e em seguida, uma atividade de Fluxo de Dados usará um conjunto de dados na fonte para transformar esses dados.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>O tempo de execução da integração auto-hospedado está disponível para fluxos de dados?

O IR auto-hospedado é uma construção de pipeline ADF que pode utilizar com a Atividade de Cópia para adquirir ou mover dados de e para fontes e pias de dados baseadas em VM ou VM. Encenar os dados primeiro com uma Cópia, depois Fluxo de Dados para transformação, e depois uma cópia subsequente se precisar de mover os dados transformados de volta para a loja on-prem.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>O motor computacional de fluxo de dados serve vários inquilinos?
Os agrupamentos nunca são partilhados. Garantimos o isolamento por cada trabalho executado em execução de produção. Em caso de depuração do cenário, uma pessoa obtém um cluster, e todos os debugs irão para aquele cluster que são iniciados por esse utilizador.

## <a name="wrangling-data-flows"></a>Fluxos de dados de estrangulamento

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Quais são as regiões apoiadas para a disputa do fluxo de dados?

O fluxo de dados de luta é atualmente suportado nas fábricas de dados criadas nas seguintes regiões:

* Leste da Austrália
* Canadá Central
* Índia Central
* E.U.A. Leste
* E.U.A. Leste 2
* Leste do Japão
* Europa do Norte
* Ásia Sudeste
* E.U.A. Centro-Sul
* Sul do Reino Unido
* E.U.A. Centro-Oeste
* Europa ocidental
* E.U.A. Oeste
* E.U.A.Oeste 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Quais são as limitações e constrangimentos com o fluxo de dados?

Os nomes do conjunto de dados só podem conter caracteres alfa-numéricos. As seguintes lojas de dados são suportadas:

* Conjunto de dados deTexto deslimitado no Armazenamento DeSlimited Blob utilizando autenticação da chave da conta
* Conjunto de dados deTexto deslimitado em Azure Data Lake Storage gen2 utilizando chave de conta ou autenticação principal de serviço
* Conjunto de dados DelimitedText em Azure Data Lake Storage gen1 usando a autenticação principal do serviço
* Base de Dados Azure SQL e Armazém de Dados utilizando a autenticação sql. Consulte os tipos SQL suportados abaixo. Não existe polyBase ou suporte de preparação para armazém de dados.

Neste momento, a integração do Key Vault de serviço ligado não é suportada na discussão de fluxos de dados.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>Qual é a diferença entre mapeamento e fluxos de dados?

Os fluxos de dados de mapeamento fornecem uma forma de transformar dados em escala sem qualquer codificação necessária. Você pode projetar um trabalho de transformação de dados na tela de fluxo de dados construindo uma série de transformações. Comece com qualquer número de transformações de origem seguidas por passos de transformação de dados. Complete o seu fluxo de dados com uma pia para aterrar os seus resultados num destino. Mapear o fluxo de dados é ótimo para mapear e transformar dados com esquemos conhecidos e desconhecidos nos lavatórios e fontes.

Os fluxos de dados de contorcer em escala permitem-lhe fazer a preparação e exploração ágeis de dados utilizando o editor de mashup Power Query Online em escala através da execução de faíscas. Com a ascensão dos lagos de dados, às vezes basta explorar um conjunto de dados ou criar um conjunto de dados no lago. Não estás a mapear um alvo conhecido. Os fluxos de dados de contorcer-se são utilizados para cenários de análise menos formais e baseados em modelos.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Qual é a diferença entre os Fluxos de Dados da Plataforma de Energia e os fluxos de dados?

Os dados da Plataforma de Energia permitem aos utilizadores importar e transformar dados de uma vasta gama de fontes de dados para o Common Data Service e o Azure Data Lake para construir aplicações PowerApps, relatórios Power BI ou automações de Fluxo. Os Dados da Plataforma de Energia utilizam as experiências de preparação de dados estabelecidas da Power Query, semelhantes ao Power BI e ao Excel. Os dados da Plataforma de Energia também permitem uma reutilização fácil dentro de uma organização e lidam automaticamente com a orquestração (por exemplo, refrescando automaticamente os fluxos de dados que dependem de outro fluxo de dados quando o anterior é atualizado).

A Azure Data Factory (ADF) é um serviço de integração de dados gerido que permite aos engenheiros de dados e integrador de dados do cidadão criar fluxos de trabalho complexos de extração híbrida de extracção-transformação (ETL) e de extração de carga (ELT). O fluxo de dados em ADF capacita os utilizadores com um ambiente livre de códigos e sem servidores que simplifica a preparação de dados na nuvem e escala para qualquer tamanho de dados sem necessidade de gestão de infraestruturas. Utiliza a tecnologia de preparação de dados power query (também utilizada em fluxos de dados da Power Platform, Excel, Power BI) para preparar e moldar os dados. Construído para lidar com todas as complexidades e desafios de escala da integração de big data, os fluxos de dados que disputam permitem aos utilizadores preparar rapidamente dados em escala através da execução de faíscas. Os utilizadores podem construir pipelines de dados resilientes num ambiente visual acessível com a nossa interface baseada no navegador e deixar a ADF lidar com as complexidades da execução da Spark. Construa horários para os seus oleodutos e monitorize as execuções de fluxo de dados a partir do portal de monitorização ADF. Gerir facilmente a disponibilidade de dados SLAs com a rica monitorização e alertas de disponibilidade da ADF e alavancar capacidades de integração e implementação contínuas incorporadas para salvar e gerir os seus fluxos num ambiente gerido. Estabeleça alertas e veja planos de execução para validar que a sua lógica está a funcionar tão planeada quanto afina os seus fluxos de dados.

### <a name="supported-sql-types"></a>Tipos SQL suportados

A confraternde fluxo de dados suporta os seguintes tipos de dados no SQL. Terá um erro de validação por usar um tipo de dados que não é suportado.

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
* tempo de data pequena
* carimbo de data/hora
* uniqueidentifier
* xml

Outros tipos de dados serão suportados no futuro.

## <a name="next-steps"></a>Passos seguintes
Para obter instruções passo a passo para criar uma fábrica de dados, consulte os seguintes tutoriais:

- [Quickstart: Criar uma fábrica de dados](quickstart-create-data-factory-dot-net.md)
- [Tutorial: Copiar dados na nuvem](tutorial-copy-data-dot-net.md)
