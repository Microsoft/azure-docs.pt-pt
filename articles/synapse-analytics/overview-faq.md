---
title: FAQ - Azure Synapse Analytics
description: FAQ para Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: b28f34c6ee160f96a7b30ded274b030936ce3266
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701580"
---
# <a name="azure-synapse-analytics-workspace-preview-frequently-asked-questions"></a>Azure Synapse Analytics (pré-visualização do espaço de trabalho) frequentemente faz perguntas

Neste guia, encontrará as perguntas mais frequentes para a Synapse Analytics.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Geral

### <a name="q-what-is-azure-synapse-analytics"></a>P: O que é Azure Synapse Analytics

R: Azure Synapse é uma plataforma integrada de dados para BI, IA e inteligência contínua. Conecta vários tempos de análise, como sQL e Spark através de uma única plataforma que fornece uma forma unificada de:

- Proteja os seus recursos de análise, incluindo rede, gerindo acesso único a pool, dados e artefactos de desenvolvimento.
- Monitorize facilmente e otimize rapidamente, reaja e depura eventos que ocorram nas suas atividades no espaço de trabalho em qualquer camada.
- Gerencie os seus metadados através dos motores. Crie uma tabela Spark e estará automaticamente disponível nas suas bases de dados Azure Synapse.
- Interaja com os dados através de uma experiência unificada do utilizador. O Estúdio Synapse traz para a mesma plataforma desenvolvedores de big data, engenheiros de dados, DBAs, Analistas de Dados e Cientistas de Dados.

### <a name="q-how-do-i-get-started-with-azure-synapse-analytics"></a>P: Como começar com a Azure Synapse Analytics

R: Para começar a utilizar o Azure Synapse Analytics, registe o fornecedor de [recursos Azure Synapse](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types) selecionando o *Microsoft.Synapse* da lista de fornecedores de recursos. Em seguida, crie um espaço de [trabalho Synapse](https://portal.azure.com) (é gratuito!) e crie os recursos que deseja sob esse espaço de trabalho. Você pode seguir um dos nossos tutoriais quickstart, como [Criar uma piscina SYnapse SQL](quickstart-create-sql-pool-portal.md) ou Criar um espaço de [trabalho,](quickstart-create-workspace.md)que irá acompanhá-lo através de uma caixa de uso simples. Também pode encontrar cadernos de amostrae scripts SQL no nosso [repositório.](https://github.com/Azure/azure-synapse-analytics/tree/master/samples) Se precisar de se ligar a um conjunto de dados público, crie um novo serviço ligado com os seguintes atributos:

- azure_storage_account_name = "armazenamento de dados em azureopen"
- azure_storage_sas_token = "" (escrever **""**)

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>P: Quais são os principais componentes da Azure Synapse Analytics

R: Azure Synapse tem as seguintes capacidades:

- As capacidades de análise são oferecidas através de piscina SQL ou SQL on-demand (pré-visualização) (Servidor).
- Piscina Apache Spark (pré-visualização) com suporte total para Scala, Python, SparkSQL e C #
- Fluxo de Dados oferecendo uma experiência de transformação de big data livre de código
- Integração de Dados & Orquestração para integrar os seus dados e operacionalizar todo o desenvolvimento do seu código
- Estúdio para aceder a todas estas capacidades através de uma única Web UI

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>P: Como é que a Azure Synapse Analytics se relaciona com o Armazém de Dados Azure SQL

R: Azure Synapse Analytics é uma evolução do Azure SQL Data Warehouse numa plataforma de análise, que inclui o pool SQL como solução de armazém de dados. Esta plataforma combina exploração de dados, ingestão, transformação, preparação e uma camada de analítico de serviço.

## <a name="use-cases"></a>Casos de utilização

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>Q: O que é um caso de bom uso para piscina Synapse SQL

R: A piscina SQL é o coração das necessidades do seu armazém de dados. É a solução líder do armazém de dados no [preço/desempenho.](https://azure.microsoft.com/services/sql-data-warehouse/compare/) O pool SQL é a solução líder do armazém de dados em nuvem do setor porque você pode:

- servir uma grande e mista variedade de cargas de trabalho sem impacto no desempenho graças à alta conmoedação e isolamento da carga de trabalho
- proteger os seus dados facilmente através de funcionalidades avançadas que vão da segurança da rede ao acesso de grãos finos
- beneficiar de uma ampla gama de ecossistemas

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>P: O que é um caso de bom uso para Spark em Synapse

R: O nosso primeiro objetivo é proporcionar uma grande experiência de Engenharia de Dados para transformar dados sobre o lago em lote ou riacho. A sua integração apertada e simples à nossa orquestração de dados torna a operacionalização do seu desenvolvimento simples.

Outro caso de utilização para a Spark é para um cientista de dados:

- extrair uma característica
- explorar dados
- treinar um modelo

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>P: O que é um bom caso de uso para a SQL on-demand em Synapse

R: SQL on-demand é um serviço de consulta sobre os dados no seu lago de dados. Permite democratizar o acesso a todos os seus dados, fornecendo uma sintaxe T-SQL familiar para consultar dados no lugar, sem necessidade de copiar ou carregar dados numa loja especializada.

Os exemplos de casos de utilização incluem o seguinte:

- descoberta e exploração básicas - fornece analistas de dados, cientistas de dados emergentes e engenheiros de dados com um caminho fácil para primeiro conhecimento de dados que vivem no seu lago de dados com esquema sino em consultas T-SQL lidas
- armazém de dados lógicos - os analistas de dados podem executar a total expressividade da linguagem T-SQL para consultar diretamente e analisar os dados residentes no Armazenamento Azure e usar ferramentas bi familiares (por exemplo, Serviços de Análises Azure, Power BI Premium, etc.) para refrescar dashboards refuncionando consultas de consulta starlight
- "Consulta única" ETL - permite que engenheiros de dados transformem dados baseados em Armazenamento Azure de um formato para outro, filtro, agregado, etc. em forma de processamento massivamente paralelo, persistir resultados de consulta ao Armazenamento Azure e torná-los imediatamente disponíveis para posterior processamento em Starlight Query ou outros serviços de interesse

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>P: O que é um bom caso de uso para o fluxo de dados em Synapse

R: O fluxo de dados permite que os engenheiros de dados desenvolvam lógica de transformação gráfica de dados sem código de escrita. Os fluxos de dados resultantes são executados como atividades no âmbito da Integração de Dados & Orquestração. As atividades de fluxo de dados podem ser operacionalizadas através das capacidades de agendamento, controlo, fluxo e monitorização existentes.

## <a name="security-and-access"></a>Segurança e Acesso

R: Experiência de inscrição única de ponta a ponta é um importante processo de autenticação na Synapse Analytics. Gerir e passar a identidade através de uma integração aAD completa é imprescindível.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adls-gen2"></a>P: Como posso ter acesso a ficheiros e pastas no ADLS Gen2

R: O acesso a ficheiros e pastas é atualmente gerido através do ADLS Gen2. Para mais informações, consulte o controlo de [acesso ao armazenamento do Data Lake](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>P: Posso usar ferramentas de inteligência empresarial de terceiros para aceder à Azure Synapse Analytics

R: Sim, você pode usar suas aplicações de negócio de terceiros, como Tableau e Power BI, para ligar à piscina SQL e SQL on-demand. A Faísca apoia o IntelliJ.

## <a name="next-steps"></a>Próximos passos

- [Criar uma área de trabalho](quickstart-create-workspace.md)
- [Use o Estúdio Synapse](quickstart-synapse-studio.md)
- [Criar uma piscina SQL](quickstart-create-sql-pool-portal.md)
- [Utilizar o SQL a pedido](quickstart-sql-on-demand.md)
- [Crie uma piscina apache spark](quickstart-create-apache-spark-pool-portal.md) 
