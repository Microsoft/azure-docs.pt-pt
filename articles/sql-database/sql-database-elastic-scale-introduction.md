---
title: Aumentar horizontalmente com a Base de Dados SQL do Azure
description: Os desenvolvedores de SaaS (software como serviço) podem criar facilmente bancos de dados elásticos e escalonáveis na nuvem usando essas ferramentas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 56556576dffd4e022f919af89459d92e48c6c895
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690252"
---
# <a name="scaling-out-with-azure-sql-database"></a>Aumentar horizontalmente com a Base de Dados SQL do Azure
Você pode expandir facilmente os bancos de dados SQL do Azure usando as ferramentas de **banco de dados elástico** . Essas ferramentas e recursos permitem que você use os recursos de banco de dados do banco de dados **SQL do Azure** para criar soluções para cargas de trabalho transacionais e especialmente aplicativos SaaS (software como serviço). Os recursos de banco de dados elástico são compostos por:

* [Biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md): a biblioteca de cliente é um recurso que permite criar e manter bancos de dados fragmentados.  Consulte [introdução às ferramentas de banco de dados elástico](sql-database-elastic-scale-get-started.md).
* [Ferramenta de divisão/mesclagem de banco de dados elástico](sql-database-elastic-scale-overview-split-and-merge.md): move dados entre bancos de dado fragmentados. Essa ferramenta é útil para mover dados de um banco de dado de vários locatários para um único (ou vice-versa). Consulte o [tutorial da ferramenta de divisão/mesclagem do banco de dados elástico](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Trabalhos de banco de dados elástico](elastic-jobs-overview.md): Use trabalhos para gerenciar um grande número de bancos de dados SQL do Azure. Realize facilmente operações administrativas, como alterações de esquema, gerenciamento de credenciais, atualizações de dados de referência, coleta de dados de desempenho ou coleta de telemetria de locatário (cliente) usando trabalhos.
* [Consulta de banco de dados elástico](sql-database-elastic-query-overview.md) (visualização): permite que você execute uma consulta TRANSACT-SQL que abrange vários bancos de dados. Isso permite a conexão com ferramentas de relatório, como Excel, Power BI, tableau, etc.
* [Transações elásticas](sql-database-elastic-transactions-overview.md): esse recurso permite que você execute transações que abrangem vários bancos de dados no Azure SQL Database. As transações de banco de dados elástico estão disponíveis para aplicativos .NET usando ADO .NET e se integram à experiência de programação familiar usando as [classes System. Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

O elemento gráfico a seguir mostra uma arquitetura que inclui os **recursos de banco de dados elástico** em relação a uma coleção de banco de dados.

Neste gráfico, as cores do banco de dados representam esquemas. Os bancos de dados com a mesma cor compartilham o mesmo esquema.

1. Um conjunto de **bancos de dados SQL do Azure** é hospedado no Azure usando a arquitetura de fragmentação.
2. A **biblioteca de cliente do banco de dados elástico** é usada para gerenciar um conjunto de fragmentos.
3. Um subconjunto dos bancos de dados é colocado em um **pool elástico**. (Consulte [o que é um pool?](sql-database-elastic-pool.md)).
4. Um **trabalho de banco de dados elástico** executa scripts T-SQL agendados ou ad hoc em todos os bancos de dados.
5. A **ferramenta de divisão/mesclagem** é usada para mover dados de um fragmento para outro.
6. A **consulta de banco de dados elástico** permite que você escreva uma consulta que abranja todos os bancos de dados no conjunto de fragmentos.
7. **As transações elásticas** permitem que você execute transações que abrangem vários bancos de dados. 

![Ferramentas de Bases de Dados Elásticas][1]

## <a name="why-use-the-tools"></a>Por que usar as ferramentas?
A obtenção de elasticidade e escala para aplicativos em nuvem é simples para VMs e armazenamento de BLOBs – basta adicionar ou subtrair unidades ou aumentar a energia. Mas permanecia um desafio para o processamento de dados com estado em bancos de dados relacionais. Os desafios surgiram nestes cenários:

* Aumento e redução da capacidade para a parte do banco de dados relacional de sua carga de trabalho.
* Gerenciamento de hotspots que podem surgir afetando um subconjunto específico de dados, como um cliente final ocupado (locatário).

Tradicionalmente, cenários como esses foram resolvidos investindo em servidores de banco de dados de grande escala para dar suporte ao aplicativo. No entanto, essa opção é limitada na nuvem onde todo o processamento ocorre em um hardware de mercadoria predefinido. Em vez disso, a distribuição de dados e o processamento em vários bancos de dado estruturados de forma idêntica (um padrão de expansão conhecido como "fragmentação") fornece uma alternativa a abordagens tradicionais de expansão em termos de custo e elasticidade.

## <a name="horizontal-and-vertical-scaling"></a>Dimensionamento horizontal e vertical
A figura a seguir mostra as dimensões horizontal e vertical de dimensionamento, que são as maneiras básicas pelas quais os bancos de dados elásticos podem ser dimensionados.

![Expansão horizontal versus vertical][2]

O dimensionamento horizontal refere-se à adição ou remoção de bancos de dados a fim de ajustar a capacidade ou o desempenho geral, também chamado de "expansão horizontal". A fragmentação, na qual os dados são particionados em uma coleção de bancos de dados estruturados de forma idêntica, é uma maneira comum de implementar o dimensionamento horizontal.  

O dimensionamento vertical refere-se ao aumento ou redução do tamanho de computação de um banco de dados individual, também conhecido como "escalar verticalmente".

A maioria dos aplicativos de banco de dados de escala de nuvem usa uma combinação dessas duas estratégias. Por exemplo, um aplicativo de software como serviço pode usar o dimensionamento horizontal para provisionar novos clientes finais e o dimensionamento vertical para permitir que o banco de dados de cada cliente final aumente ou reduza os recursos conforme a necessidade da carga de trabalho.

* O dimensionamento horizontal é gerenciado usando a [biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md).
* O dimensionamento vertical é feito usando cmdlets Azure PowerShell para alterar a camada de serviço ou colocando bancos de dados em um pool elástico.

## <a name="sharding"></a>Fragmentação
A *fragmentação* é uma técnica para distribuir grandes quantidades de dados estruturados de forma idêntica entre vários bancos de dado independentes. Ele é especialmente popular com os desenvolvedores de nuvem que criam ofertas de SAAS (software como serviço) para clientes finais ou empresas. Esses clientes finais costumam ser chamados de "locatários". A fragmentação pode ser necessária por vários motivos:  

* A quantidade total de dados é muito grande para caber nas restrições de um banco de dado individual
* A taxa de transferência de transação da carga de trabalho geral excede os recursos de um banco de dados individual
* Os locatários podem exigir isolamento físico um do outro, portanto, bancos de dados separados são necessários para cada locatário
* Seções diferentes de um banco de dados podem precisar residir em geografias diferentes por motivos de conformidade, desempenho ou geopolítica.

Em outros cenários, como a ingestão de dados de dispositivos distribuídos, a fragmentação pode ser usada para preencher um conjunto de bancos que são organizados de forma temporal. Por exemplo, um banco de dados separado pode ser dedicado a cada dia ou semana. Nesse caso, a chave de fragmentação pode ser um inteiro que representa a data (presente em todas as linhas das tabelas fragmentadas) e as consultas que recuperam informações para um intervalo de datas devem ser roteadas pelo aplicativo para o subconjunto de bancos de dados que abrangem o intervalo em questão.

A fragmentação funciona melhor quando cada transação em um aplicativo pode ser restrita a um único valor de uma chave de fragmentação. Isso garante que todas as transações sejam locais para um banco de dados específico.

## <a name="multi-tenant-and-single-tenant"></a>Multilocatário e locatário único
Alguns aplicativos usam a abordagem mais simples de criar um banco de dados separado para cada locatário. Essa abordagem é o **padrão de fragmentação de locatário único** que fornece isolamento, capacidade de backup/restauração e dimensionamento de recursos na granularidade do locatário. Com a fragmentação de locatário único, cada banco de dados é associado a um valor de ID de locatário específico (ou valor de chave do cliente), mas essa chave não precisa estar sempre presente nos dados em si. É responsabilidade do aplicativo rotear cada solicitação para o banco de dados apropriado – e a biblioteca de cliente pode simplificar isso.

![Locatário único versus multilocatário][4]

Outros cenários pacote vários locatários juntos em bancos de dados, em vez de isolá-los em bancos de dados separados. Esse padrão é um padrão típico de **fragmentação multilocatário** -e pode ser orientado pelo fato de que um aplicativo gerencia grandes números de locatários pequenos. Na fragmentação multilocatário, as linhas nas tabelas de banco de dados são projetadas para transportar uma chave que identifica a ID de locatário ou a chave de fragmentação. Novamente, a camada de aplicativo é responsável por rotear a solicitação de um locatário para o banco de dados apropriado e isso pode ser suportado pela biblioteca de cliente do banco de dados elástico. Além disso, a segurança em nível de linha pode ser usada para filtrar quais linhas cada locatário pode acessar-para obter detalhes, consulte [aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md). A redistribuição de dados entre bancos de dado pode ser necessária com o padrão de fragmentação multilocatário e é facilitada pela ferramenta de divisão/mesclagem do banco de dados elástico. Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Mover dados de múltiplos para bancos de dados de locação única
Ao criar um aplicativo SaaS, é comum oferecer aos clientes potenciais uma versão de avaliação do software. Nesse caso, ele é econômico para usar um banco de dados multilocatário para o dado. No entanto, quando um cliente potencial se torna um usuário, um banco de dados de locatário único é melhor, pois fornece um melhor desempenho. Se o cliente tiver criado dados durante o período de avaliação, use a [ferramenta de divisão e mesclagem](sql-database-elastic-scale-overview-split-and-merge.md) para mover os dados do multilocatário para o novo banco de dados de locatário único.

## <a name="next-steps"></a>Passos seguintes
Para um aplicativo de exemplo que demonstra a biblioteca de cliente, consulte Introdução [às ferramentas de banco de dados elástico](sql-database-elastic-scale-get-started.md).

Para converter os bancos de dados existentes para usar as ferramentas, consulte [migrar bancos de dados existentes para escalar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md).

Para ver as especificidades do pool elástico, consulte [considerações de preço e desempenho para um pool elástico](sql-database-elastic-pool.md)ou criar um novo pool com [pools elásticos](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

