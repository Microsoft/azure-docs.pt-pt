---
title: FAQ - Azure Synapse Analytics
description: FAQ para Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 9a0fb8ed8ac54fa866b6db7d8f808c011c0c6758
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695826"
---
# <a name="azure-synapse-analytics-frequently-asked-questions"></a>Azure Synapse Analytics frequentemente fez perguntas

Neste guia, encontrará as perguntas mais frequentes para a Azure Synapse Analytics.

## <a name="general"></a>Geral

### <a name="q-how-can-i-use-rbac-roles-to-secure-my-workspace"></a>P: Como posso usar os papéis do RBAC para garantir o meu espaço de trabalho?

R: A azure Synapse introduz uma série de funções e âmbitos para atribuí-los que simplificarão a segurança do seu espaço de trabalho.

Funções DE Sinapse RBAC:
* Administrador da Sinapse
* Administrador Sinapse SQL
* Administrador de faíscas da Sinapse
* Contribuidor de Sinapse (pré-visualização)
* Editor de Artefactos De Sinapse (pré-visualização)
* Utilizador de artefactos de sinapse (pré-visualização)
* Operador de computação de sinapse (pré-visualização)
* Utilizador credencial de sinapse (pré-visualização)

Para garantir o seu espaço de trabalho synapse, atribua as Funções RBAC a estes âmbitos RBAC:
* Áreas de Trabalho
* Piscinas de faíscas
* Tempos de integração
* Serviços ligados
* Credenciais

Além disso, com piscinas SQL dedicadas você tem todas as mesmas funcionalidades de segurança que você conhece e ama.

### <a name="q-how-do-i-control-dedicated-sql-pools-serverless-sql-pools-and-serverless-spark-pools"></a>P: Como controlo piscinas SQL dedicadas, piscinas SQL sem servidor e piscinas de faíscas sem servidor?

R: Como ponto de partida, a Azure Synapse trabalha com a análise de custos incorporada e os alertas de custos disponíveis ao nível da subscrição do Azure.

- Piscinas SQL dedicadas - você tem visibilidade direta sobre o custo e controlo sobre o custo, porque você cria e especifica os tamanhos de piscinas SQL dedicadas. Pode controlar ainda mais o que os utilizadores podem criar ou escalar piscinas SQL dedicadas com funções Azure RBAC.

- Piscinas SQL sem servidor - você tem controlos de monitorização e gestão de custos que lhe permitem limitar os gastos a um nível diário, semanal e mensal. [Consulte a gestão de custos para a piscina SQL sem servidor](./sql/data-processed.md) para obter mais informações. 

- Serverless Spark pools - você pode restringir quem pode criar piscinas Spark com funções DE RBAC Synapse.  

### <a name="q-will-synapse-workspace-support-folder-organization-of-objects-and-granularity-at-ga"></a>P: Irá o espaço de trabalho Synapse apoiar a organização de pastas de objetos e granularidade na GA?

R: Os espaços de trabalho da Sinapse suportam pastas definidas pelo utilizador.

### <a name="q-can-i-link-more-than-one-power-bi-workspace-to-a-single-azure-synapse-workspace"></a>P: Posso ligar mais do que um espaço de trabalho power BI a um único espaço de trabalho Azure Synapse?
    
R: Atualmente, só é possível ligar um espaço de trabalho power BI a um espaço de trabalho Azure Synapse. 

### <a name="q-is-synapse-link-to-cosmos-db-ga"></a>P: A Synapse Link para Cosmos DB GA?

R: Synapse Link for Apache Spark is GA. Synapse Link para piscina SQL sem servidor está em Visualização Pública.

### <a name="q-does-azure-synapse-workspace-support-cicd"></a>P: A azure Synapse workspace Suporta CI/CD? 

A: Sim! Todos os artefactos do Pipeline, cadernos, scripts SQL e definições de trabalho spark vão residir em Git. Todas as definições de piscina serão armazenadas em Git como Modelos ARM. Os objetos de piscina SQL dedicados (esquemas, mesas, vistas, etc.) serão geridos com projetos de base de dados com suporte ci/CD.

## <a name="pipelines"></a>Pipelines

### <a name="q-how-do-i-ensure-i-know-what-credential-is-being-used-to-run-a-pipeline"></a>P: Como posso garantir que sei que credencial está a ser usada para executar um oleoduto? 

R: Cada atividade num Gasoduto De Sinapse é executada utilizando a credencial especificada dentro do serviço ligado.

### <a name="q-are-ssis-irs-supported-in-synapse-integrate"></a>P: As SSIS IRs são suportadas na Integração do Sinapse?

A: Não neste momento. 

### <a name="q-how-do-i-migrate-existing-pipelines-from-azure-data-factory-to-an-azure-synapse-workspace"></a>P: Como posso migrar os oleodutos existentes da Azure Data Factory para um espaço de trabalho Azure Synapse?

R: Neste momento, deve recriar manualmente os seus oleodutos Azure Data Factory e artefactos conexos exportando o JSON do oleoduto original e importando-o para o seu espaço de trabalho da Sinapse.

## <a name="apache-spark"></a>Apache Spark

### <a name="q-what-is-the-difference-between-apache-spark-for-synapse-and-apache-spark"></a>P: Qual é a diferença entre Apache Spark para Synapse e Apache Spark?

R: Apache Spark for Synapse é Apache Spark com suporte adicional para integrações com outros serviços (AAD, AzureML, etc.) e bibliotecas adicionais (mssparktuils, Hummingbird) e configurações de desempenho pré-afinadas.

Qualquer carga de trabalho que esteja atualmente a funcionar no Apache Spark será executada no Apache Spark para a Azure Synapse sem alterações. 

### <a name="q-what-versions-of-spark-are-available"></a>P: Que versões de Spark estão disponíveis?

R: Azure Synapse Apache Spark suporta totalmente a Faísca 2.4. Para obter uma lista completa de componentes principais e versão atualmente suportada consulte [o suporte da versão Apache Spark](./spark/apache-spark-version-support.md).

### <a name="q-is-there-an-equivalent-of-dbutils-in-azure-synapse-spark"></a>P: Existe um equivalente de DButils em Azure Synapse Spark?

R: Sim, Azure Synapse Apache Spark fornece a biblioteca **mssparkutils.** Para obter documentação completa do utilitário consulte [Introdução aos utilitários Microsoft Spark](./spark/microsoft-spark-utilities.md).

### <a name="q-how-do-i-set-session-parameters-in-apache-spark"></a>P: Como posso definir parâmetros de sessão em Apache Spark?

R: Para definir parâmetros de sessão, utilize a magia de configuração %%disponível. É necessário um reinício da sessão para que os parâmetros produzam efeitos. 

### <a name="q-how-do-i-set-cluster-level-parameters-in-a-serverless-spark-pool"></a>P: Como posso definir parâmetros de nível de cluster numa piscina de faísca sem servidor?

R: Para definir parâmetros de nível de cluster, pode fornecer um ficheiro spark.conf para a piscina Spark. Esta piscina honrará então os parâmetros passados no arquivo config. 

### <a name="q-can-i-run-a-multi-user-spark-cluster-in-azure-synapse-analytics"></a>P: Posso executar um cluster de faíscas multiutilizador em Azure Synapse Analytics?
 
R: A Azure Synapse fornece motores construídos de propósito para casos específicos de utilização. Apache Spark for Synapse é projetado como um serviço de trabalho e não um modelo de cluster. Há dois cenários em que as pessoas pedem um modelo de cluster multiutilizador.

**Cenário #1: Muitos utilizadores acedem a um cluster para servir dados para fins bi.**

A forma mais fácil de realizar esta tarefa é cozinhar os dados com a Spark e, em seguida, aproveitar as capacidades de serviço da Synapse SQL para que possam ligar o Power BI a esses conjuntos de dados.

**Cenário #2: Ter vários desenvolvedores num único cluster para economizar dinheiro.**
 
Para satisfazer este cenário, você deve dar a cada desenvolvedor uma piscina Spark sem servidor que está definida para usar um pequeno número de recursos Spark. Uma vez que as piscinas Spark sem servidor não custam nada, até que sejam usadas ativamente minimiza o custo quando existem vários desenvolvedores. As piscinas partilham metadados (mesas spark) para que possam trabalhar facilmente uns com os outros.

### <a name="q-how-do-i-include-manage-and-install-libraries"></a>P: Como incluo, gero e instalo bibliotecas?

R: Pode instalar pacotes externos através de um ficheiro requirements.txt enquanto cria a piscina Spark, a partir do espaço de trabalho da sinapse, ou a partir do portal Azure. Ver [Gerir bibliotecas para Apache Spark em Azure Synapse Analytics](./spark/apache-spark-azure-portal-add-libraries.md).

## <a name="dedicated-sql-pools"></a>Piscinas SQL dedicadas

### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-pools"></a>P: Quais são as diferenças funcionais entre piscinas SQL dedicadas e piscinas sem servidor?

R: Pode encontrar uma lista completa de diferenças nas [diferenças de características T-SQL no Synapse SQL](./sql/overview-features.md).

### <a name="q-now-that-azure-synapse-is-ga-how-do-i-move-my-dedicated-sql-pools-that-were-previously-standalone-into-azure-synapse"></a>P: Agora que a Azure Synapse é GA, como mudo as minhas piscinas SQL dedicadas que antes eram autónomas para a Azure Synapse? 

R: Não há "movimento" ou "migração". Pode optar por ativar novas funcionalidades de espaço de trabalho nas suas piscinas existentes. Se o fizer, não haverá alterações de rutura, em vez disso poderá utilizar novas funcionalidades como o Synapse Studio, Spark e piscinas SQL sem servidor.

### <a name="q-what-is-the-default-deployment-of-dedicated-sql-pools-now"></a>P: Qual é a implantação padrão de piscinas SQL dedicadas agora? 

R: Por padrão, todas as novas piscinas SQL dedicadas serão implantadas num espaço de trabalho; no entanto, se precisar ainda pode criar uma piscina SQL dedicada (anteriormente SQL DW) num fator de forma autónoma. 

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Synapse Analytics](get-started.md)
* [Criar uma área de trabalho](quickstart-create-workspace.md)
* [Utilizar conjunto de SQL sem servidor](quickstart-sql-on-demand.md)
