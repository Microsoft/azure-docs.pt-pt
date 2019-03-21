---
title: O Azure SQL Data Warehouse - arquitetura MPP | Documentos da Microsoft
description: Saiba como o Azure SQL Data Warehouse combina processamento paralelo em massa (MPP) com o armazenamento do Azure para alcançar a escalabilidade e de elevado desempenho.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0c2ad7e5a707c20db2773324e8047eedaad1a48b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835038"
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>O Azure SQL Data Warehouse - paralelo em grande escala (MPP) arquitetura de processamento
Saiba como o Azure SQL Data Warehouse combina processamento paralelo em massa (MPP) com o armazenamento do Azure para alcançar a escalabilidade e de elevado desempenho. 

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="mpp-architecture-components"></a>Componentes de arquitetura MPP
O SQL Data Warehouse tira partido de um aumento horizontal de arquitetura para distribuir o processamento de computacional de dados em vários nós. A unidade de escala é uma abstração da potência de computação que é conhecida como um [unidade do armazém de dados](what-is-a-data-warehouse-unit-dwu-cdwu.md). SQL Data Warehouse separa a computação do armazenamento que permite dimensionar computação independentemente dos dados no seu sistema.

![Arquitetura do SQL Data Warehouse](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Data Warehouse utiliza uma arquitetura baseada no nó. Os aplicativos se conectar e emitir comandos T-SQL para um nó de controlo, o que é o único ponto de entrada para o armazém de dados. O nó de controlo é executado o mecanismo de MPP otimiza consultas para processamento paralelo e, em seguida, transmite operações em nós de computação para trabalhar em paralelo. Os nós de computação armazenam todos os dados de utilizador no armazenamento do Azure e executam as consultas paralelas. O serviço de movimento de dados (DMS) é um serviço interno ao nível do sistema que move os dados em todos os nós conforme necessário para executar consultas em paralelo e retornar resultados precisos. 

Com armazenamento e computação desacoplados, o SQL Data Warehouse pode:

* Independentemente tamanho poder de computação, independentemente de suas necessidades de armazenamento.
* Aumentar ou diminuir o poder de computação sem mover dados.
* Colocar em pausa a capacidade de computação, mantendo os dados intactos, pelo que paga apenas armazenamento.
* Retomar a capacidade de computação durante as horas de funcionamento.

### <a name="azure-storage"></a>Storage do Azure
SQL Data Warehouse utiliza o armazenamento do Azure para manter os dados de usuário protegidos.  Uma vez que os seus dados são armazenados e gerenciados pelo armazenamento do Azure, o SQL Data Warehouse cobra em separado para o consumo de armazenamento. Os dados em si são em partição horizontal em **distribuições** para otimizar o desempenho do sistema. Pode escolher qual padrão de fragmentação a utilizar para distribuir os dados ao definir a tabela. SQL Data Warehouse suporta estes padrões de fragmentação:

* Hash
* Round Robin
* Replicar

### <a name="control-node"></a>Nó de controlo

O nó de controlo é o cérebro do armazém de dados. É o front-end que interage com todas as ligações e aplicações. O motor do MPP é executado no nó de controlo para otimizar e coordenar as consultas paralelas. Quando submete uma consulta T-SQL ao SQL Data Warehouse, o nó de controlo transforma-a em consultas que são executadas em relação a cada distribuição em paralelo.

### <a name="compute-nodes"></a>Nós de computação

Os nós de computação fornecem a capacidade computacional. Mapa de distribuições para nós de computação para processamento. Como paga para obter mais recursos de computação, o SQL Data Warehouse mapeia novamente as distribuições para os nós de computação disponíveis. O número de intervalos de nós de 1 a 60 de computação e é determinado pelo nível de serviço para o armazém de dados.

Cada nó de computação tem um ID de nó que está visível nas vistas de sistema. Pode ver o ID de nó de computação para a coluna de node_id nas vistas de sistema cujos nomes comecem com sys.pdw_nodes procurar. Para obter uma lista uma destas vistas de sistema, consulte [vistas de sistema MPP](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Serviço de movimento de dados
Serviço de movimento de dados (DMS) é a tecnologia de transporte de dados que coordena o movimento de dados entre os nós de computação. Algumas consultas requerem movimento de dados para garantir que as consultas paralelas devolvem resultados precisos. Quando for necessário o movimento de dados, o DMS garante que os dados corretos obtém para o local correto. 

## <a name="distributions"></a>Distribuições

Uma distribuição é a unidade básica de armazenamento e processamento para paralelo consulta funcionando em dados distribuídos. Quando o SQL Data Warehouse executa uma consulta, o trabalho é dividido em 60 consultas mais pequenas que são executadas em paralelo. Cada uma das consultas mais pequenas 60 é executado em um das distribuições de dados. Cada nó de computação gere um ou mais de 60 distribuições. Um armazém de dados com recursos de computação máximo tem uma distribuição por nó de computação. Um armazém de dados com recursos de computação mínimo tem todas as distribuições no nó de computação.  

## <a name="hash-distributed-tables"></a>Tabelas distribuídas de hash
Uma tabela distribuída por hash pode entregar o mais elevado desempenho de consulta para associações e agregações em tabelas grandes. 

Dividir os dados numa tabela distribuída por hash, o SQL Data Warehouse utiliza uma função de hash para determinística atribuir cada linha a uma distribuição. Na definição da tabela, uma das colunas é designada como a coluna de distribuição. A função de hash usa os valores da coluna de distribuição para atribuir a cada linha a uma distribuição.

O diagrama seguinte ilustra como um inteiro (tabela não distribuído) é armazenado como uma tabela distribuída por hash. 

![Tabela de Distributed](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "tabela distribuídas")  

* Cada linha pertence a uma distribuição.  
* Um algoritmo de hash determinística atribui cada linha a uma distribuição.  
* O número de linhas de tabela por distribuição varia conforme exibido nos diferentes tamanhos de tabelas.

Existem considerações de desempenho para a seleção de uma coluna de distribuição, como distinctness, distorção de dados e os tipos de consultas que são executadas no sistema.

## <a name="round-robin-distributed-tables"></a>Tabelas distribuídas de round robin
Uma tabela round robin é a tabela mais simples para criar e oferece um desempenho rápido quando utilizado como uma tabela de teste para cargas.

Uma tabela distribuída round robin distribui uniformemente os dados por uma tabela, mas sem qualquer otimização adicional. Uma distribuição primeiro é escolhida aleatoriamente e, em seguida, em que buffers de linhas são atribuídos em seqüência para distribuições. É rápido carregar dados para uma tabela round robin, mas o desempenho da consulta, muitas vezes, pode ser melhor com tabelas distribuídas com hash. Necessitam de junções em tabelas round robin reshuffling dados e isto demora mais tempo.


## <a name="replicated-tables"></a>Tabelas replicadas
Tabelas replicadas fornece o desempenho de consulta mais rápido para tabelas pequenas.

Uma tabela que é replicada armazena em cache uma cópia completa da tabela em cada nó de computação. Conseqüentemente, a replicação de uma tabela remove a necessidade de transferir dados entre nós de computação antes de uma associação ou agregação. Tabelas replicadas são melhor utilizadas com tabelas pequenas. É necessário armazenamento adicional e existe overhead adicional que é incorrido durante a escrita de dados que tornam impraticável a tabelas grandes.  

O diagrama seguinte mostra uma tabela replicada. Para o SQL Data Warehouse, a tabela replicada é colocado em cache no primeiro de distribuição em cada nó de computação.  

![Tabela de replicado](media/sql-data-warehouse-distributed-data/replicated-table.png "replicado tabela") 

## <a name="next-steps"></a>Passos Seguintes
Agora que já sabe um pouco sobre o SQL Data Warehouse, saiba como [criar um SQL Data Warehouse][create a SQL Data Warehouse] e [carregar dados de exemplo][load sample data] rapidamente. Se não estiver familiarizado com o Azure, poderá achar útil o [Glossário do Azure][Azure glossary] quando se deparar com terminologia nova. Em alternativa, veja alguns destes outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogues]
* [Pedidos de funcionalidades]
* [Vídeos]
* [Blogues da Equipa Customer Advisory]
* [Criar pedido de suporte]
* [Fórum do MSDN]
* [Fórum do Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Criar pedido de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogues da Equipa Customer Advisory]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Pedidos de funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Fórum do Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
