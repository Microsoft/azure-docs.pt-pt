---
title: Azure Synapse Analytics (ex-SQL DW) Perguntas frequentes
description: Este artigo lista perguntas frequentes sobre a Azure Synapse Analytics (anteriormente SQL DW) de clientes e desenvolvedores
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1a4fbfa87756914f169bb20387702cac69d8736d
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759487"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics (ex-SQL DW) Frequentemente fez perguntas

## <a name="general"></a>Geral

P. O que é o Azure Synapse?

R. Azure Synapse é um serviço de análise ilimitado que reúne armazenamento de dados e análise de Big Data. Dá-lhe a liberdade de consultar dados nos seus termos, utilizando recursos a pedido ou aprovisionados sem servidores - em escala. O Azure Synapse alia estes dois universos a uma experiência unificada para ingerir, preparar, gerir e fornecer dados a necessidades imediatas de BI e machine learning. Para mais informações, consulte, [O que é Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

P. O que aconteceu ao Azure SQL Data Warehouse?

R. Azure Synapse é azure SQL Data Warehouse (SQL DW) evoluído. Levamos o mesmo armazém de dados líder da indústria para um novo nível de desempenho e capacidades. Pode continuar a executar as suas cargas de trabalho de armazém de dados existentes em produção com a Azure Synapse e beneficiar automaticamente das novas capacidades, que estão em pré-visualização. Para mais informações, consulte [o What is Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

P. O que é SQL Analytics?

R. O SQL Analytics refere-se às funcionalidades de armazenamento de dados da empresa que estão geralmente disponíveis com o Azure Synapse. Para mais informações, consulte, [O que é Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

P. Como posso começar com Azure Synapse?

R. Pode começar com uma [conta azure gratuita](https://azure.microsoft.com/free/sql-data-warehouse/) ou [vendas de contato para mais informações.](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html) 

P. O que o Azure Synapse oferece para a segurança de dados?

R. A Azure Synapse oferece várias soluções para proteger dados como o TDE e a auditoria. Para obter mais informações, consulte [segurança](sql-data-warehouse-overview-manage-security.md).

P. Onde posso descobrir quais os padrões legais ou empresariais com que a Azure Synapse está em conformidade?

R. Visite a página [de Conformidade](https://www.microsoft.com/trustcenter/compliance/complianceofferings) da Microsoft para várias ofertas de conformidade por produto como SOC e ISO. Primeiro, escolha pelo título de Compliance. Em seguida, expanda o Azure na secção de serviços de nuvem de âmbito da Microsoft no lado direito da página para ver quais os serviços que o Azure Synapse cumpre.

P. Posso ligar o Power BI?

R. Sim! Embora o Power BI suporte a consulta direta com o Azure Synapse, não se destina a um grande número de utilizadores ou dados em tempo real. Para otimizar ainda mais o desempenho do Power BI, considere utilizar o Power BI em cima dos Serviços de Análise do Azure ou do Serviço de Análise IaaS.

P. Quais são os limites de capacidade da SQL Analytics?

R. Consulte a nossa página de [limites](sql-data-warehouse-service-capacity-limits.md) de capacidade atual. 

P. Porque é que a minha Escala/Pausa/Currículo está a demorar tanto tempo?

R. Vários fatores podem influenciar o tempo para operações de gestão de cálculo. Um caso comum para operações de longo prazo é a reversão transacional. Quando uma operação de escala ou pausa é iniciada, todas as sessões de entrada são bloqueadas e as consultas são drenadas. Para deixar o sistema num estado estável, as transações devem ser relançadas antes de iniciar uma operação. Quanto maior for o número de registos das transações, mais tempo a operação será travada restaurando o sistema para um estado estável.

## <a name="user-support"></a>Suporte ao utilizador

P. Tenho um pedido de recurso, onde o submeto?

R. Se tiver um pedido de funcionalidade, envie-o na nossa página [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse)

P. Como posso fazer x?

R. Para ajudar no desenvolvimento com o Azure Synapse, pode fazer perguntas na nossa página [stack Overflow.](https://stackoverflow.com/questions/tagged/azure-sqldw) 

P. Como posso enviar um bilhete de apoio?

R. [Os bilhetes](sql-data-warehouse-get-started-create-support-ticket.md) de apoio podem ser arquivados através do portal Azure.

## <a name="sql-languagefeature-support"></a>Suporte de idioma/recurso SQL 

P. Que tipos de dados são suportados?

R. Ver tipos de [dados](sql-data-warehouse-tables-data-types.md).

P. Que características de mesa apoia?

R. Muitas funcionalidades são suportadas. As funcionalidades que não são suportadas podem ser encontradas em características de [mesa não suportadas](sql-data-warehouse-tables-data-types.md).

## <a name="tooling-and-administration"></a>Ferramentas e administração

P. O SQL Analytics suporta APIs REST?

R. Sim. A maior parte da funcionalidade REST que pode ser usada com base de dados SQL também está disponível com o SQL Analytics. Pode encontrar informações da API dentro das páginas de documentação REST ou [MSDN](https://msdn.microsoft.com/library/azure/mt163685.aspx).


## <a name="loading"></a>A carregar

P. Que clientes apoiam?

R. Suporte ao condutor para SQL Analytics pode ser encontrado na página [De conexão Strings](sql-data-warehouse-connection-strings.md)

P: Que formatos de ficheiro são suportados pela PolyBase?

R: Orc, RC, Parquet e texto delimitado plano

P: Que fontes de dados posso ligar à utilização da PolyBase? 

A: [Armazenamento de lagos de dados azure](sql-data-warehouse-load-from-azure-data-lake-store.md) e [blobs de armazenamento azure](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

P: A pressão da computação é possível quando se conecta a Blobs de Armazenamento Azure ou ADLS? 

R: Não, a PolyBase só interage com os componentes de armazenamento. 

P: Posso ligar-me ao HDI?

R: O HDI pode utilizar a ADLS ou a WASB como camada HDFS. Se tiver ou como camada HDFS, então pode carregar esses dados em DW SQL. No entanto, não é possível gerar cálculo de push down para a instância DeI. 

## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre o Azure Synapse como um todo, consulte a nossa página [de visão geral.](sql-data-warehouse-overview-faq.md)
