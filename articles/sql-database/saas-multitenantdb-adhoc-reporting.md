---
title: Executar consultas de geração de relatórios ad hoc em várias bases de dados SQL do Azure | Documentos da Microsoft
description: Execute consultas de geração de relatórios ad hoc em várias bases de dados SQL num exemplo de aplicação multi-inquilino.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: sstein
manager: craigg
ms.date: 10/30/2018
ms.openlocfilehash: d4c5a2ca88f982626c8c2a8b37e4a7d6dfdbe599
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076365"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Executar consultas de análise ad hoc em várias bases de dados SQL do Azure

Neste tutorial, executa consultas distribuídas em todo o conjunto de inquilino bases de dados para ativar relatórios interativos ad hoc. Estas consultas podem extrair informações escondidas nos dados operacionais da aplicação Wingtip Tickets SaaS. Para fazer estas extrações, implementar uma base de dados de análise adicionais para o servidor de catálogo e utilizar a consulta elástica para ativar consultas distribuídas.


Neste tutorial, ficará a saber:

> [!div class="checklist"]
> 
> * Como implementar uma base de dados de relatórios ad hoc
> * Como executar consultas distribuídas em todas as bases de dados de inquilinos


Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* A aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino é implementada. Para implementar em menos de cinco minutos, veja [implementar e explorar a aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino](saas-multitenantdb-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) é instalado. Para transferir e instalar o SSMS, consulte [Baixe o SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Relatórios ad hoc padrão

![padrão de geração de relatórios ad hoc](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

Aplicações SaaS podem analisar grandes quantidades de dados de inquilino que são armazenados centralmente na cloud. As análises revelam informações sobre a operação e a utilização da sua aplicação. Estas informações podem ajudá-desenvolvimento de funcionalidade, usabilidade e a outros investimentos nas suas aplicações e serviços.

Aceder a estes dados numa única base de dados multi-inquilinos é fácil, mas não será tão fácil se houver uma distribuição à escala através de potencialmente milhares de bases de dados. Uma abordagem é usar [consulta elástica](sql-database-elastic-query-overview.md), que permite consultas entre um conjunto distribuído de bases de dados com esquema comum. Esses bancos de dados podem ser distribuídos por diferentes grupos de recursos e subscrições. Ainda um início de sessão comum tem de ter acesso para extrair dados de todas as bases de dados. Consulta elástica utiliza uma única *head* base de dados em que são definidas tabelas externas que espelham tabelas ou vistas nos bancos de dados distribuídas (inquilino). As consultas submetidas para esta base de dados “head” são compiladas para produzir um plano de consultas distribuídas, com partes das consultas enviadas para as bases de dados inquilinas, conforme necessário. Consulta elástica utiliza o mapa de partições horizontais da base de dados de catálogo para determinar a localização de todas as bases de dados do inquilino. Configuração e a consulta são muito simples de utilizar o padrão [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)e suporte a consultas ad hoc de ferramentas, como o Power BI e Excel.

Ao distribuir consultas entre bases de dados de inquilino, a consulta elástica fornece informações imediatas sobre os dados de produção em direto. No entanto, como consulta elástica extrai dados da potencialmente muitas bases de dados, latência de consulta pode às vezes, ser superior de consultas equivalentes submetidas para uma única base de dados do multi-inquilino. Certifique-se de que a consultas de design para minimizar os dados que são devolvidos. Consulta elástica seja muitas vezes, mais adequada para a consulta de pequenas quantidades de dados em tempo real, em vez de construção usados com freqüência ou consultas de análise complexas ou relatórios. Se as consultas não funcionam tão bem, observe a [plano de execução](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) para ver que parte da consulta foi reduzido para a base de dados remota. E avaliar a quantidade de dados está a ser devolvido. Consultas que exigem processamento analítico complexo pode ser melhor fornecidas ao guardar os dados de inquilino extraído para uma base de dados otimizado para consultas de análise. Base de dados SQL e SQL Data Warehouse podem alojar tal a análise da base de dados.

Este padrão para a análise é explicado no [tutorial de análise de inquilinos](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obter o código de origem da aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino e os scripts

Os scripts de base de dados do Wingtip Tickets SaaS multi-inquilino e o código de origem da aplicação estão disponíveis no [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repositório do GitHub. Veja a [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip Tickets SaaS de desbloqueio.

## <a name="create-ticket-sales-data"></a>Criar dados de vendas de pedido de suporte

Para executar consultas em relação a um conjunto de dados mais interessante, crie dados de vendas de pedido de suporte ao executar o gerador de pedido de suporte.

1. Na *ISE do PowerShell*, abra o... \\Módulos de aprendizagem\\análise operacional\\relatórios ad hoc\\*demonstração AdhocReporting.ps1* criar scripts e defina os seguintes valores:
   * **$DemoScenario** = 1, **comprar bilhetes para eventos em todos os locais**.
2. Prima **F5** para executar o script e gerar vendas de bilhetes. Enquanto estiver a executar o script, continue os passos neste tutorial. Os dados de pedido de suporte são consultados no *execução ad hoc distribuído consultas* secção, por isso, aguarde que o gerador de pedido de suporte concluir.

## <a name="explore-the-tenant-tables"></a>Explore as tabelas de inquilino 

No aplicativo de base de dados do Wingtip Tickets SaaS multi-inquilino, os inquilinos são armazenados num modelo de gestão híbrida de inquilino – onde os dados de inquilino são um armazenados num banco de dados do multi-inquilino ou um banco de dados de inquilino único e podem ser movido entre os dois. Ao consultar em todas as bases de dados do inquilino, é importante que a consulta elástica pode tratar os dados, como se faz parte de um único banco de dados lógico em partição horizontal pelo inquilino. 

Para atingir esse padrão, todas as tabelas de inquilinos incluem uma *VenueId* coluna que identifica que os dados de inquilino pertence. O *VenueId* é computado como um hash do nome do local, mas qualquer abordagem poderia ser usado para introduzir um valor exclusivo para esta coluna. Essa abordagem é semelhante à forma como a chave de inquilino é calculada para utilização no catálogo. Tabelas contendo *VenueId* são utilizados por consulta elástica para paralelização de consultas e enviá-las para baixo para a base de dados de inquilinos remotos apropriado. Isso reduz drasticamente a quantidade de dados que são devolvidos e resulta num aumento no desempenho, especialmente se existirem vários inquilinos cujos dados são armazenados em bases de dados de inquilino único.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Implementar a base de dados utilizado para consultas distribuídas ad hoc

Este exercício implementa a *adhocreporting* base de dados. Esta é a base de dados principal que contém o esquema usado para consultar em todas as bases de dados do inquilino. A base de dados é implementada para o servidor de catálogo existente, o que é o servidor utilizado para todas as bases de dados relacionados com a gestão na aplicação de exemplo.

1. Abra... \\Módulos de aprendizagem\\análise operacional\\relatórios ad hoc\\*demonstração AdhocReporting.ps1* no *ISE do PowerShell* e defina o valores seguintes:
   * **$DemoScenario** = 2, **base de dados do implementar Ad hoc analytics**.

2. Prima **F5** para executar o script e criar o *adhocreporting* base de dados.

Na próxima seção, adicionar esquema para a base de dados para que possa ser utilizada para executar consultas distribuídas.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configurar a base de dados "head" para a execução de consultas distribuídas

Neste exercício adiciona esquema (a origem de dados externa e definições de tabela externa) à base de dados de relatórios ad hoc que permite consultas em todas as bases de dados do inquilino.

1. Abra o SQL Server Management Studio e ligue-se para o ad hoc relatórios que criou no passo anterior. O nome da base de dados é *adhocreporting*.
2. Abrir ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *Initialize AdhocReportingDB.sql* no SSMS.
3. Reveja o script SQL e tenha em atenção o seguinte:

   Consulta elástica utiliza uma credencial com âmbito de base de dados para aceder a cada uma das bases de dados do inquilino. Esta credencial tem de estar disponíveis em todas as bases de dados e devem normalmente ser concedidos os direitos mínimo necessário para permitir estas consultas ad hoc.

    ![Criar credencial](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Ao utilizar a base de dados de catálogo como origem de dados externa, as consultas são distribuídas para todas as bases de dados registadas no catálogo, quando a consulta é executada. Como os nomes de servidor são diferentes para cada implementação, este script de inicialização é a localização da base de dados do catálogo ao obter o servidor atual (@@servername) em que o script é executado.

    ![Criar origem de dados externa](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   As tabelas externas que fazem referência a tabelas de inquilino são definidas com **distribuição = SHARDED(VenueId)**. Isso encaminha uma consulta para um determinado *VenueId* na base de dados apropriadas e melhora o desempenho para muitos cenários conforme mostrado na próxima seção.

    ![criar tabelas externas](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   A tabela local *VenueTypes* que é criado e preenchido. Esta tabela de dados de referência é comum em todas as bases de dados de inquilinos, pelo que pode ser representado aqui como uma tabela local e preenchida com o common data. Para algumas consultas, isso pode reduzir a quantidade de dados movidos entre as bases de dados do inquilino e o *adhocreporting* base de dados.

    ![Criar tabela](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Se incluir as tabelas de referência desta forma, certifique-se de que atualizar o esquema de tabela e os dados sempre que atualiza os bancos de dados do inquilino.

4. Prima **F5** para executar o script e inicializar o *adhocreporting* base de dados. 

Agora pode executar consultas distribuídas e recolher informações de todos os inquilinos!

## <a name="run-ad-hoc-distributed-queries"></a>Executar consultas distribuídas ad hoc

Agora que o *adhocreporting* base de dados está a configurar, vá em frente e executar algumas consultas distribuídas. Incluem o plano de execução para uma melhor compreensão de onde o processamento de consulta está a acontecer. 

Quando inspecionar o plano de execução, Paire o rato sobre os ícones de plano para obter detalhes. 

1. Na *SSMS*, abra... \\Módulos de aprendizagem\\análise operacional\\relatórios ad hoc\\*demonstração AdhocReportingQueries.sql*.
2. Certifique-se de que está ligado para o **adhocreporting** base de dados.
3. Selecione o **consulta** menu e clique em **Include Actual Execution Plan**
4. Realce o *quais locais estão atualmente registados?* consulta e prima **F5**.

   A consulta devolve a lista de todo local, que ilustra quão rápido e fácil é consultar em todos os inquilinos e devolver dados de cada inquilino.

   Inspecione o plano e ver que o custo total é a consulta remota porque vamos simplesmente para cada base de dados de inquilinos e selecionar as informações do local.

   ![SELECT * FROM dbo.Venues](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Selecione a próxima consulta e prima **F5**.

   Esta consulta une dados de bases de dados inquilinas e local *VenueTypes* tabela (local, como ele é uma tabela *adhocreporting* base de dados).

   Inspecione o plano e ver que a maior parte do custo é a consulta remota porque podemos consultar informações do local de cada inquilino (dbo. Os locais), e, em seguida, fazer uma rápida associação local com o local *VenueTypes* tabela para exibir o nome amigável.

   ![Junte-se no dados remotos e locais](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Agora, selecione o *no qual o dia foram mais pedidos de suporte vendidos?* consulta e prima **F5**.

   Esta consulta faz associar um pouco mais complexo e a agregação. O que é importante observar é que a maior parte do processamento seja efetuada remotamente, e mais uma vez, trazemos novamente apenas as linhas que precisamos, retornando apenas uma única linha de contagem de venda de bilhetes agregada de cada local por dia.

   ![consulta](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> 
> * Executar consultas distribuídas em todas as bases de dados do inquilino
> * Implementar uma base de dados de relatórios ad hoc e adicione-o para executar consultas distribuídas esquema.

Experimente agora o [tutorial de análise de inquilinos](saas-multitenantdb-tenant-analytics.md) para explorar a extração de dados para uma base de dados de análise separada para processamento de análises mais complexo.

## <a name="additional-resources"></a>Recursos adicionais

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Consulta Elástica](sql-database-elastic-query-overview.md)
