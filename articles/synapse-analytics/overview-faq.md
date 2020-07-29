---
title: FAQ - Azure Synapse Analytics (pré-visualização de espaços de trabalho)
description: FAQ para Azure Synapse Analytics (pré-visualização de espaços de trabalho)
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: ba6f79fffe5287be7574d422f026489d4da2795e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287493"
---
# <a name="azure-synapse-analytics-workspaces-preview-frequently-asked-questions"></a>Azure Synapse Analytics (pré-visualização de espaços de trabalho) frequentemente perguntas

Neste guia, encontrará as perguntas mais frequentes para a Synapse Analytics.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Geral

### <a name="q-what-is-azure-synapse-analytics"></a>P: O que é Azure Synapse Analytics

R: Azure Synapse é uma plataforma de dados integrada para BI, IA e inteligência contínua. Conecta vários tempos de análise, tais como SQL e Spark através de uma única plataforma que fornece uma forma unificada de:

- Proteja os seus recursos de análise, incluindo rede, gerindo o acesso único ao pool, dados e artefactos de desenvolvimento.
- Monitorize e otimize rapidamente, reaja e depure eventos que ocorram nas suas atividades no espaço de trabalho em qualquer camada.
- Gerencie os seus metadados através dos motores. Crie uma tabela Spark e estará automaticamente disponível nas suas bases de dados Azure Synapse.
- Interaja com os dados através de uma experiência unificada do utilizador. O Synapse Studio traz grandes desenvolvedores de dados, engenheiros de dados, DBAs, Analistas de Dados e Cientistas de Dados para a mesma plataforma.

Para mais informações, consulte [O que é Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics/overview-what-is).

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>P: Quais são os principais componentes da Azure Synapse Analytics

R: A Azure Synapse tem as seguintes capacidades:

- As capacidades de análise são oferecidas através da piscina SQL ou sql on demand (pré-visualização) (Serverless).
- Piscina Apache Spark (pré-visualização) com apoio total para Scala, Python, SparkSQL e C #
- Data Flow oferecendo uma experiência de transformação de big data sem código
- Integração de Dados & Orquestração para integrar os seus dados e operacionalizar todo o seu desenvolvimento de código
- Estúdio para aceder a todas estas capacidades através de um único UI Web

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>P: Como é que o Azure Synapse Analytics se relaciona com o Azure SQL Data Warehouse

R: Azure Synapse Analytics é uma evolução do Azure SQL Data Warehouse numa plataforma de análise, que inclui o pool SQL como solução de armazém de dados. Esta plataforma combina a exploração de dados, ingestão, transformação, preparação e uma camada de analítica de serviço.

## <a name="use-cases"></a>Casos de utilização

### <a name="q-how-do-i-rename-a-published-artifact-dataset-notebook-sql-script-and-so-on-in-azure-synapse"></a>P: Como posso mudar o nome de um artefacto publicado (conjunto de dados, caderno, script sql e assim por diante) em Azure Synapse?

R: Para renomear um ficheiro de artefactos publicados, primeiro clonar o ficheiro e mudar o nome do novo ficheiro para o nome que preferir. Terá de atualizar manualmente todas as referências do artefacto ao novo nome do ficheiro e eliminar o antigo.

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>Q: O que é um bom caso de uso para piscina Sinaapse SQL

R: A piscina SQL é o coração das suas necessidades de armazém de dados. É a principal solução de armazém de dados em [preço/desempenho.](https://azure.microsoft.com/services/sql-data-warehouse/compare/) O pool SQL é a solução líder do armazém de dados em nuvem líder do setor porque você pode:

- servir uma grande e mista variedade de cargas de trabalho sem impacto no desempenho graças à alta concordância e ao isolamento da carga de trabalho
- garantir os seus dados facilmente através de funcionalidades avançadas que vão desde a segurança da rede até ao acesso a grãos finos
- beneficiar de uma ampla gama de ecossistemas

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>P: O que é um bom caso de uso para faísca em Sinapse

R: O nosso primeiro objetivo é proporcionar uma grande experiência de Engenharia de Dados para transformar dados sobre o lago em lote ou riacho. A sua integração estreita e simples à nossa orquestração de dados torna a operacionalização do seu trabalho de desenvolvimento simples.

Outro caso de uso para Spark é para um Cientista de Dados para:

- extrair uma característica
- explorar dados
- treinar um modelo

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>P: O que é um bom caso de uso para SQL on-demand em Synapse

R: SQL on demand é um serviço de consulta sobre os dados no seu lago de dados. Permite democratizar o acesso a todos os seus dados, fornecendo uma sintaxe T-SQL familiar para consultar os dados em vigor, sem necessidade de copiar ou carregar dados numa loja especializada.

Os exemplos de casos de utilização incluem:

- descoberta e exploração básicas - fornece analistas de dados, cientistas de dados emergentes e engenheiros de dados com um caminho fácil para primeiro conhecimento dos dados que vivem no seu lago de dados com esquemas sobre consultas T-SQL lidas
- armazém de dados lógico - os analistas de dados podem executar a total expressividade da linguagem T-SQL para consultar e analisar diretamente os dados que residem no Azure Storage e usar ferramentas bi familiares (por exemplo, Serviços de Análises Azure, Power BI Premium, etc.) para refrescar dashboards reativando consultas de consultas starlight
- "consulta única" ETL - permite que os engenheiros de dados transformem dados baseados em Azure Storage de um formato para outro, filtro, agregado, etc. em forma de processamento massivamente paralelo, persista resultados de consulta para o Azure Storage e os disponibilize imediatamente para posterior processamento em Consulta starlight ou outros serviços de interesse

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>P: Qual é um bom caso de uso para o fluxo de dados na Sinapse

R: O fluxo de dados permite que os engenheiros de dados desenvolvam lógica de transformação de dados gráficos sem escrever código. Os fluxos de dados resultantes são executados como atividades no âmbito da Integração de Dados & Orquestração. As atividades de fluxo de dados podem ser operacionalizadas através das capacidades de agendamento, controlo, fluxo e monitorização existentes.

## <a name="security-and-access"></a>Segurança e Acesso

R: A experiência de assinatura única de ponta a ponta é um importante processo de autenticação no Synapse Analytics. Gerir e passar a identidade através de uma integração completa da AAD é uma obrigação.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adls-gen2"></a>P: Como posso ter acesso a ficheiros e pastas na ADLS Gen2

R: O acesso a ficheiros e pastas é atualmente gerido através da ADLS Gen2. Para obter mais informações, consulte o [controlo de acesso ao armazenamento de Data Lake.](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>P: Posso usar ferramentas de inteligência empresarial de terceiros para aceder ao Azure Synapse Analytics

R: Sim, você pode usar suas aplicações de negócio de terceiros, como Tableau e Power BI, para ligar à piscina SQL e SQL on-demand. Spark apoia IntelliJ.

## <a name="next-steps"></a>Próximos passos

- [Criar uma área de trabalho](quickstart-create-workspace.md)
- [Use o Estúdio Synapse](quickstart-synapse-studio.md)
- [Criar uma piscina SQL](quickstart-create-sql-pool-portal.md)
- [Utilizar o SQL a pedido](quickstart-sql-on-demand.md)
- [Criar uma piscina Apache Spark](quickstart-create-apache-spark-pool-portal.md) 
