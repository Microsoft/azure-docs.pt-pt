---
title: Azure Synapse Analytics (anteriormente SQL DW) Perguntas frequentes
description: Este artigo lista perguntas frequentes sobre Azure Synapse Analytics (anteriormente SQL DW) de clientes e desenvolvedores
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a0d98a2c58e7629f988d0d3e8015355ba414c07a
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85201146"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics (anteriormente SQL DW) Perguntas frequentes

## <a name="general"></a>Geral

P. O que é o Azure Synapse?

R. Azure Synapse é um serviço de análise que reúne armazenamento de dados e análise de Big Data. A Azure Synapse junta estes dois mundos com uma experiência unificada para ingerir, preparar, gerir e servir dados para necessidades de BI e machine learning. Para mais informações, veja, [O que é Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

P. O que aconteceu ao Armazém de Dados Azure SQL?

R. Azure Synapse é Azure SQL Data Warehouse (SQL DW) evoluído. Levamos o mesmo armazém de dados líder da indústria para um novo nível de desempenho e capacidades. Pode continuar a executar as cargas de trabalho existentes no armazém de dados em produção com a Azure Synapse. Para mais informações, consulte [O que é Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

P. O que é a piscina Synapse SQL?

R. O pool SYNAPSE SQL refere-se às funcionalidades de armazenamento de dados da empresa que estão geralmente disponíveis com a Azure Synapse. Para mais informações, veja, [O que é Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

P. Como é que começo com o Azure Synapse?

R. Pode começar com uma [conta gratuita da Azure](https://azure.microsoft.com/free/sql-data-warehouse/) ou entrar em [contato com vendas para mais informações.](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)

P. O que a Azure Synapse oferece para a segurança dos dados?

R. A Azure Synapse oferece várias soluções para proteger dados como o TDE e a auditoria. Para mais informações, consulte [a Segurança.](sql-data-warehouse-overview-manage-security.md)

P. Onde posso descobrir com que normas legais ou empresariais a Azure Synapse está em conformidade?

R. Visite a página [Microsoft Compliance](https://www.microsoft.com/trustcenter/compliance/complianceofferings) para várias ofertas de conformidade por produtos como SOC e ISO. Primeiro, escolha por título Compliance. Em seguida, expanda o Azure na secção de serviços de nuvem em alcance da Microsoft no lado direito da página para ver que serviços estão em conformidade com o Azure Synapse.

P. Posso ligar o Power BI?

R. Sim! Embora o Power BI suporte consulta direta com a Azure Synapse, não se destina a um grande número de utilizadores ou dados em tempo real. Para otimizar ainda mais o desempenho do Power BI, considere a utilização do Power BI em cima dos Serviços de Análise ou Serviço de Análise da Azure.

P. Quais são os limites de capacidade da piscina Synapse SQL?

R. Consulte a nossa página de [limites de capacidade](sql-data-warehouse-service-capacity-limits.md) atuais.

P. Porque é que a minha escala/pausa/currículo está a demorar tanto tempo?

R. Vários fatores podem influenciar o tempo para operações de gestão de computação. Um caso comum para operações de longa duração é o revés transacional. Quando uma operação de escala ou pausa é iniciada, todas as sessões de entrada são bloqueadas e as consultas são drenadas. Para deixar o sistema num estado estável, as transações devem ser revoadas antes de uma operação poder iniciar-se. Quanto maior for o número e maior o tamanho do registo das transações, mais tempo a operação ficará parada, restaurando o sistema para um estado estável.

P: Como posso mudar o nome de um artefacto publicado (conjunto de dados, caderno, script sql e assim por diante) em Azure Synapse?

R: Para renomear um ficheiro de artefactos publicados, primeiro clonar o ficheiro e mudar o nome do novo ficheiro para o nome que preferir. Terá de atualizar manualmente todas as referências do artefacto ao novo nome do ficheiro e eliminar o antigo.

## <a name="user-support"></a>Suporte de utilizador

P. Tenho um pedido de recurso, onde o submeto?

R. Se tiver um pedido de funcionalidade, envie-o na nossa página [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse)

P. Como posso fazer x?

R. Para ajudar no desenvolvimento com a Azure Synapse, pode fazer perguntas na nossa página [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-sqldw)

P. Como submeto um bilhete de apoio?

R. [Os bilhetes de apoio](sql-data-warehouse-get-started-create-support-ticket.md) podem ser arquivados através do portal Azure.

## <a name="sql-languagefeature-support"></a>Suporte de linguagem/funcionalidade SQL

P. Quais os tipos de dados suportados?

R. Consulte [os tipos de dados.](sql-data-warehouse-tables-data-types.md)

P. Que funcionalidades de mesa suporta?

R. Muitas funcionalidades são suportadas. Funcionalidades que não são suportadas podem ser encontradas em [Funcionalidades de Tabela Não Suportadas.](sql-data-warehouse-tables-data-types.md)

## <a name="tooling-and-administration"></a>Ferramenta e administração

P. A piscina Synapse SQL suporta AS APIs de REST?

R. Sim. A maioria das funcionalidades REST que podem ser usadas com base de dados SQL também está disponível com piscina Sinaapse SQL. Pode encontrar informações da API nas páginas de documentação DO REST ou [bases de dados.](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="loading"></a>Carregamento

P. Que motoristas de clientes apoia?

R. Suporte do condutor para piscina Sinaapse SQL pode ser encontrado na página [de Cordas de Conexão](../sql/connection-strings.md)

P: Que formatos de ficheiros são suportados pela PolyBase?

R: Orc, RC, Parquet e texto delimitado plano

P: Que fontes de dados posso ligar-me à utilização do PolyBase?

R: [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) and [Azure Storage Blobs](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

P: A pressão de computação é possível quando se liga a Azure Storage Blobs ou ADLS?

R: Não, a PolyBase só interage com os componentes de armazenamento.

P: Posso ligar-me ao HDI?

R: O HDI pode utilizar a ADLS ou a WASB como camada HDFS. Se tiver como camada HDFS, pode carregar esses dados numa piscina Sinapse SQL. No entanto, não é possível gerar cálculo de pushdown para a instância HDI.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre a Azure Synapse como um todo, consulte a nossa página [geral.](sql-data-warehouse-overview-faq.md)
