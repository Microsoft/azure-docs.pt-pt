---
title: Consultas de relatórios ad hoc em várias bases de dados
description: Execute consultas de relatórios ad hoc em várias bases de dados Azure SQL em um exemplo de app multi-inquilino.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/30/2018
ms.openlocfilehash: 800592b7a8b263fea2883fdd3e030f78f72647dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459922"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-databases-azure-sql-database"></a>Executar consultas ad hoc analíticas em várias bases de dados (Azure SQL Database)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você executou consultas distribuídas em todo o conjunto de bases de dados de inquilinos para permitir relatórios interativas ad hoc. Estas consultas podem extrair insights enterrados nos dados operacionais do dia-a-dia da aplicação Wingtip Tickets SaaS. Para fazer estas extrações, você implanta uma base de dados de análise adicional para o servidor de catálogo e utiliza a Consulta Elástica para permitir consultas distribuídas.


Neste tutorial, ficará a saber:

> [!div class="checklist"]
> 
> * Como implementar uma base de dados de relatórios ad hoc
> * Como executar consultas distribuídas em todas as bases de dados de inquilinos


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação Wingtip Tickets SaaS Multi-tenant Database está implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação de Base de Dados De Multi-inquilinos Wingtip SaaS](saas-multitenantdb-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps)
* O SQL Server Management Studio (SSMS) está instalado. Para descarregar e instalar sSMS, consulte [o Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Padrão de reporte ad hoc

![padrão de reporte de adhoc](./media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

As aplicações saaS podem analisar a vasta quantidade de dados do inquilino que são armazenados centralmente na nuvem. As análises revelam insights sobre o funcionamento e utilização da sua aplicação. Estes insights podem orientar o desenvolvimento de funcionalidades, melhorias de usabilidade e outros investimentos nas suas apps e serviços.

Aceder a estes dados numa única base de dados multi-inquilinos é fácil, mas não será tão fácil se houver uma distribuição à escala através de potencialmente milhares de bases de dados. Uma abordagem é usar [a Consulta Elástica,](elastic-query-overview.md)que permite consultar um conjunto distribuído de bases de dados com esquema comum. Estas bases de dados podem ser distribuídas por diferentes grupos de recursos e subscrições. No entanto, um login comum deve ter acesso a extrair dados de todas as bases de dados. A Consulta Elástica utiliza uma única base de dados de *cabeça* em que são definidas tabelas externas que espelham tabelas ou vistas nas bases de dados distribuídas (inquilinos). As consultas submetidas para esta base de dados “head” são compiladas para produzir um plano de consultas distribuídas, com partes das consultas enviadas para as bases de dados inquilinas, conforme necessário. A Consulta Elástica utiliza o mapa de fragmentos na base de dados do catálogo para determinar a localização de todas as bases de dados dos inquilinos. A configuração e consulta são simples usando [transact-SQL](/sql/t-sql/language-reference)padrão, e suportam consulta ad hoc de ferramentas como Power BI e Excel.

Ao distribuir consultas através das bases de dados dos inquilinos, a Elastic Query fornece informações imediatas sobre os dados de produção ao vivo. No entanto, à medida que a Consulta Elástica retira dados de potencialmente muitas bases de dados, a latência da consulta pode por vezes ser maior do que para consultas equivalentes submetidas a uma única base de dados multi-inquilino. Certifique-se de desenhar consultas para minimizar os dados que são devolvidos. A consulta elástica é frequentemente mais adequada para consultar pequenas quantidades de dados em tempo real, em oposição à construção de consultas ou relatórios de análise frequentemente utilizados ou complexos. Se as consultas não funcionarem bem, consulte o plano de [execução](/sql/relational-databases/performance/display-an-actual-execution-plan) para ver que parte da consulta foi empurrada para a base de dados remota. E avaliar a quantidade de dados que estão a ser devolvidos. Consultas que requerem processamento analítico complexo podem ser melhor servidas guardando os dados do inquilino extraído em uma base de dados que é otimizada para consultas de análise. A SQL Database e a Azure Synapse Analytics podem acolher esta base de dados de análise.

Este padrão de análise é explicado no tutorial de análise do [inquilino.](saas-multitenantdb-tenant-analytics.md)

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obtenha o código fonte de origem de aplicação de aplicação de pedidos de bases de dados de ponta da Wingtip SaaS Multi-inquilinos

Os scripts de base de dados de multi-inquilinos Wingtip SaaS e código fonte de aplicação estão disponíveis no [wingtipTicketsSaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub repo. Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para as etapas para descarregar e desbloquear os scripts SaaS dos Bilhetes Wingtip.

## <a name="create-ticket-sales-data"></a>Criar dados de venda de bilhetes

Para executar consultas contra um conjunto de dados mais interessante, crie dados de venda de bilhetes executando o gerador de bilhetes.

1. No *PowerShell ISE,* abra o ... \\ Módulos de Aprendizagem \\ Analytics \\ Adhoc Reportando \\ *Demo-AdhocReporting.ps1* script e definir os seguintes valores:
   * **$DemoScenario** = 1, **Compre bilhetes para eventos em todos os locais.**
2. Prima **F5** para executar o script e gerar venda de bilhetes. Enquanto o guião está em execução, continue os passos neste tutorial. Os dados dos bilhetes são consultados na secção *de consultas distribuídas ad hoc,* por isso aguarde que o gerador de bilhetes esteja completo.

## <a name="explore-the-tenant-tables"></a>Explore as mesas de inquilinos 

Na aplicação Wingtip Tickets SaaS Multi-tenant Database, os inquilinos são armazenados num modelo de gestão de inquilinos híbridos - onde os dados dos inquilinos são armazenados numa base de dados multi-inquilinos ou numa única base de dados de inquilinos e podem ser movidos entre os dois. Ao consultar todas as bases de dados dos inquilinos, é importante que a Consulta Elástica possa tratar os dados como se fizesse parte de uma única base de dados lógica, cacada pelo inquilino. 

Para atingir este padrão, todas as tabelas de inquilinos incluem uma coluna *VenueId* que identifica a que inquilino os dados pertencem. O *VenueId* é calculado como um haxixe do nome local, mas qualquer abordagem poderia ser usada para introduzir um valor único para esta coluna. Esta abordagem é semelhante à forma como a chave do inquilino é calculada para uso no catálogo. As tabelas que contêm *VenueId* são usadas pela Consulta Elástica para paralelizar consultas e empurrá-las para a base de dados de inquilinos remotos apropriado. Isto reduz drasticamente a quantidade de dados que são devolvidos e resulta num aumento de desempenho, especialmente quando há vários inquilinos cujos dados são armazenados em bases de dados de inquilinos únicos.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Implementar a base de dados utilizada para consultas distribuídas ad hoc

Este exercício implementa a base de dados *de adhocreporting.* Esta é a base de dados da cabeça que contém o esquema usado para consulta em todas as bases de dados de inquilinos. A base de dados é implantada no servidor de catálogo existente, que é o servidor utilizado para todas as bases de dados relacionadas com a gestão na aplicação da amostra.

1. Abrir... \\ Módulos \\ de Aprendizagem Analytics \\ Adhoc Reportando \\ *Demo-AdhocReporting.ps1* no *PowerShell ISE* e definir os seguintes valores:
   * **$DemoScenario** = 2, **implementar base de dados de análise ad hoc**.

2. Prima **F5** para executar o script e criar a base *de dados de adhocreporting.*

Na secção seguinte, adicione esquema à base de dados para que possa ser usado para executar consultas distribuídas.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configure a base de dados 'cabeça' para executar consultas distribuídas

Este exercício adiciona esquema (a fonte de dados externo e definições de tabela externa) à base de dados de relatórios ad hoc que permite consulta em todas as bases de dados dos inquilinos.

1. Abra o SQL Server Management Studio e ligue-se à base de dados de relatórios Adhoc que criou no passo anterior. O nome da base de dados é *adhocreporting*.
2. Abra ...\Módulos de aprendizagem\Análise Operacional\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql* em SSMS.
3. Reveja o script SQL e note o seguinte:

   A Consulta Elástica utiliza uma credencial de base de dados para aceder a cada uma das bases de dados dos inquilinos. Esta credencial deve estar disponível em todas as bases de dados e deve normalmente ser concedida aos direitos mínimos necessários para permitir estas consultas ad hoc.

    ![Criar credencial](./media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Utilizando a base de dados do catálogo como fonte externa de dados, as consultas são distribuídas em todas as bases de dados registadas no catálogo quando a consulta é executada. Como os nomes do servidor são diferentes para cada implementação, este script de inicialização obtém a localização da base de dados do catálogo recuperando o servidor atual (@ @servername ) onde o script é executado.

    ![criar fonte de dados externos](./media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   As tabelas externas que referenciam as tabelas dos inquilinos são definidas com **DISTRIBUIÇÃO = SHARDED(VenueId)**. Isto encaminha uma consulta para um *determinado VenueId* para a base de dados apropriada e melhora o desempenho para muitos cenários, como mostrado na secção seguinte.

    ![criar tabelas externas](./media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   A mesa local *VenueTypes* que é criada e povoada. Esta tabela de dados de referência é comum em todas as bases de dados dos inquilinos, pelo que pode ser representada aqui como uma tabela local e povoada com os dados comuns. Para algumas consultas, isto pode reduzir a quantidade de dados movidos entre as bases de dados dos inquilinos e a base de dados *de adhocreporting.*

    ![criar tabela](./media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Se incluir tabelas de referência desta forma, certifique-se de atualizar o esquema de tabela e os dados sempre que atualizar as bases de dados do inquilino.

4. Prima **F5** para executar o script e inicializar a base *de dados de adhocreporting.* 

Agora você pode executar consultas distribuídas, e recolher insights em todos os inquilinos!

## <a name="run-ad-hoc-distributed-queries"></a>Executar consultas distribuídas ad hoc

Agora que a base de dados *de adhocreporting* está configurada, vá em frente e execute algumas consultas distribuídas. Inclua o plano de execução para uma melhor compreensão de onde está a decorrer o processamento de consultas. 

Ao inspecionar o plano de execução, paire sobre os ícones do plano para obter detalhes. 

1. Em *SSMS,* aberto... \\ Módulos \\ de Aprendizagem Analíticos Operacionais \\ Adhoc \\ *Reportando Demo-AdhocReportingQueries.sql*.
2. Certifique-se de que está ligado à base de dados **de denúncias.**
3. Selecione o menu **de consulta** e clique **em Incluir Plano de Execução Real**
4. Destaque para quais os  *locais que estão registados atualmente?*

   A consulta devolve toda a lista de locais, ilustrando a rapidez e facilidade com que é consulta em todos os inquilinos e devolver dados de cada inquilino.

   Inspecione o plano e verifique se todo o custo é a consulta remota porque vamos simplesmente a cada base de dados de inquilinos e selecionamos a informação do local.

   ![SELECIONE * DO DBO. Locais](./media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Selecione a próxima consulta e prima **F5**.

   Esta consulta junta dados das bases de dados dos inquilinos e da tabela *Local VenueTypes* (local, uma vez que é uma tabela na base de dados *de adhocreporting).*

   Inspecione o plano e verifique se a maior parte do custo é a consulta remota porque consultamos a informação do local de cada inquilino (dbo. Locais), e, em seguida, fazer uma rápida junção local com a mesa *local VenueTypes* para mostrar o nome amigável.

   ![Junte-se a dados remotos e locais](./media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Agora selecione o *Em que dia foram mais vendidos?* Consulta, e pressione **F5**.

   Esta consulta faz uma união e agregação um pouco mais complexas. O que é importante notar é que a maior parte do processamento é feito remotamente, e mais uma vez, trazemos apenas as linhas que precisamos, devolvendo apenas uma linha para a contagem agregada de bilhetes de cada local por dia.

   ![consulta](./media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> 
> * Executar consultas distribuídas em todas as bases de dados do inquilino
> * Implemente uma base de dados de relatórios ad hoc e adicione-lhe esquema para executar consultas distribuídas.

Agora experimente o [tutorial do Tenant Analytics](saas-multitenantdb-tenant-analytics.md) para explorar a extração de dados para uma base de dados de análise separada para um processamento de análise mais complexo.

## <a name="additional-resources"></a>Recursos adicionais

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Consulta elástica](elastic-query-overview.md)