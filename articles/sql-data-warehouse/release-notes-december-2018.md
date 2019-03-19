---
title: Notas de versão de armazém de dados SQL do Azure, Dezembro de 2018 | Documentos da Microsoft
description: Notas de versão do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 0fa7004b9b0dd1ecf087566e7ce8a7d6ebf9be93
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842112"
---
# <a name="whats-new-in-azure-sql-data-warehouse-december-2018"></a>O que há de novo no Azure SQL Data Warehouse? Dezembro de 2018
O Azure SQL Data Warehouse recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidas em Dezembro de 2018.

## <a name="virtual-network-service-endpoints-generally-available"></a>Rede virtual pontos finais de serviço disponíveis em geral
Esta versão inclui a disponibilidade geral de pontos finais de serviço de rede Virtual (VNet) do Azure SQL Data Warehouse em todas as regiões do Azure. Pontos finais de serviço de VNet permitem-lhe isolar conectividade ao seu servidor lógico de uma determinada sub-rede ou um conjunto de sub-redes na sua rede virtual. O tráfego para o Azure SQL Data Warehouse da sua VNet sempre irá permanecer na rede backbone do Azure. Esta rota direta terá preferência sobre quaisquer rotas específicas que assumem o tráfego de Internet através de aplicações virtuais ou no local. Uma faturação sem adicional é cobrada para acesso de rede virtual através de pontos finais de serviço. Atual modelo para de preços [do Azure SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) aplica-se como está.

Com esta versão, ativámos também conectividade PolyBase [Gen2 de armazenamento do Azure Data Lake](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ADLS) via [sistema de ficheiros de Blob do Azure](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) driver (ABFS). Geração de armazenamento 2 do Azure Data Lake traz as qualidades que são necessárias para o ciclo de vida dos dados de análise para o armazenamento do Azure. Funcionalidades dos dois armazenamento do Azure serviços existentes, armazenamento de Blobs do Azure e de geração 1 de armazenamento do Azure Data Lake são convergidas. Funcionalidades do [Gen1 de armazenamento do Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/index), como semântica do sistema de ficheiros, ao nível do ficheiro segurança e dimensionamento são combinados com armazenamento em camadas e de baixo custo e capacidades de recuperação de alta disponibilidade/desastre de [ Armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). 

Com o Polybase pode também importar dados para o Azure SQL Data Warehouse do armazenamento do Azure obtidos para a VNet. Da mesma forma, o exportar dados do Azure SQL Data Warehouse para o armazenamento do Azure obtidos para a VNet também é suportada através do Polybase. 

Para obter mais informações sobre pontos finais de serviço de VNet no Azure SQL Data Warehouse, consulte a [mensagem de blogue](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) ou o [documentação](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json).

## <a name="automatic-performance-monitoring-preview"></a>Monitorização (pré-visualização) de desempenho automática
[Consulta Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017) está agora disponível em pré-visualização do Azure SQL Data Warehouse. Consulta Store foi concebido para ajudá-lo com resolução de problemas através da monitorização de consultas, planos de consulta, estatísticas de tempo de execução e histórico de consulta para ajudar a monitorizar a atividade e o desempenho do seu armazém de dados de desempenho de consulta. Consulta Store é um conjunto de arquivos internos e vistas de gestão (DMVs) dinâmica que lhe permitem:

- Identificar e ajustar os recursos principais consultas de consumo
- Identificar e melhorar as cargas de trabalho ad hoc
- Avaliar o desempenho da consulta e o impacto para o plano pelas alterações no estatísticas, índices ou tamanho do sistema (definição de DWU)
- Ver texto de consulta completa para todas as consultas executadas

O Store de consulta contém três armazenamentos reais:  
- Um arquivo de plano para manter as informações de plano de execução 
- Um arquivo de estatísticas de tempo de execução para manter as informações de estatísticas de execução
- Um arquivo de estatísticas de espera para manter informações de estatísticas de espera. 

O SQL Data Warehouse gere automaticamente estes arquivos e forneça um número ilimitado de consultas storied durante os últimos sete dias sem encargos adicionais. Ativar consulta Store é tão simples quanto executar uma instrução ALTER DATABASE T-SQL:

```sql
ALTER DATABASE [Database Name] SET QUERY_STORE = ON;
```
Para obter mais informações sobre Store de consulta no Azure SQL Data Warehouse, consulte o artigo [a monitorização de desempenho utilizando a Store de consulta](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)e DMVs de Store consulta, como [sys.query_store_query](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?view=sql-server-2017). Aqui está o [mensagem de blogue](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/) anunciando o lançamento.

## <a name="lower-compute-tiers-for-azure-sql-data-warehouse-gen2"></a>Camadas inferiores de computação para a geração 2 de armazém de dados SQL do Azure
Azure SQL Data Warehouse Gen2 suporta agora as camadas inferiores de computação. Os clientes poderão experimentar o do Azure SQL Data Warehouse desempenho líder, flexibilidade e recursos de segurança a partir do 100 cDWU ([unidades do Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu)) e o dimensionamento para 30 000 cDWU em minutos. A partir de meados de Dezembro de 2018, os clientes podem beneficiar do desempenho de geração 2 e camadas de computação de flexibilidade com menor [regiões](https://docs.microsoft.com/azure/sql-data-warehouse/gen2-lower-tier-regions), com o restante das regiões disponíveis durante 2019.

Ao remover o ponto de entrada para armazenamento de dados de próxima geração, Microsoft abre as portas para os clientes baseadas no valor que pretendem avaliar todas as vantagens de um armazém de dados segura de alto desempenho sem adivinhar qual ambiente de avaliação é melhor para eles. Os clientes podem começar como tão baixos como 100 cDWU, para baixo do ponto de entrada de 500 cDWU atual. SQL Data Warehouse Gen2 continua a suportar a colocação em pausa e retomar as operações e vai para além de apenas a flexibilidade na computação. Geração 2 também suporta a capacidade de armazenamento de arquivo de colunas ilimitado, juntamente com mais de 2,5 vezes mais memória por consulta, até a 128 consultas em simultâneo e [colocação em cache adaptável](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/) funcionalidades. Esses recursos em média, colocar mais de cinco vezes desempenho em comparação comparada a mesma unidade do armazém de dados na geração 1 ao mesmo preço. As cópias de segurança georredundante são padrão para Gen2 com proteção de dados garantida incorporada. Azure SQL Data Warehouse Gen2 está pronto para ser dimensionado quando estiver.

## <a name="columnstore-background-merge"></a>Intercalação de plano de fundo de Columnstore
Por predefinição, Azure SQL Data Warehouse (Azure SQL DW) armazena dados no formato colunar, com chamada de partições de micro [grupos de linhas](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression). Às vezes, devido a memória restringe no índice de compilação ou dados de tempo de carga, os grupos de linhas podem ser comprimidos com menos do que o tamanho ideal de um milhão de linhas. Grupos de linhas também podem se tornar fragmentados devido a elimina. Grupos de linhas de pequenos ou fragmentados resultam em maior consumo de memória, bem como a execução da consulta ineficiente. Com esta versão do Azure SQL DW, a tarefa de manutenção do plano de fundo de columnstore mescla pequenos grupos de linhas comprimidos para criar grupos de linhas maiores melhor utilizar memória e acelerar a execução da consulta.

## <a name="next-steps"></a>Passos Seguintes
Agora que já sabe um pouco sobre o SQL Data Warehouse, saiba como rapidamente [criar um SQL Data Warehouse][create a SQL Data Warehouse]. Se estiver familiarizado com o Azure, pode achar a [Glossário do Azure] [ Azure glossary] útil medida que aprende a terminologia nova. Em alternativa, veja alguns destes outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogues]
* [Pedidos de funcionalidades]
* [Vídeos]
* [Blogues da Equipa Customer Advisory]
* [Fórum do Stack Overflow]
* [Twitter]


[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogues da Equipa Customer Advisory]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Pedidos de funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
