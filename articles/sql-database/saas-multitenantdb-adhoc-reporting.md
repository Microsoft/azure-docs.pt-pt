---
title: Executar consultas de relatórios ad hoc em vários bancos de dados SQL do Azure | Microsoft Docs
description: Execute consultas de relatório ad hoc em vários bancos de dados SQL em um exemplo de aplicativo multilocatário.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
manager: craigg
ms.date: 10/30/2018
ms.openlocfilehash: e17f09c3fa031f5dc55fabaf638e67ab62f84f4c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875810"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Executar consultas de análise ad hoc em vários bancos de dados SQL do Azure

Neste tutorial, você executa consultas distribuídas em todo o conjunto de bancos de dados de locatário para habilitar relatórios interativos ad hoc. Essas consultas podem extrair informações enterradas nos dados operacionais diários do aplicativo SaaS Wingtip tickets. Para fazer essas extrações, você implanta um banco de dados de análise adicional no servidor de catálogo e usa a consulta elástica para habilitar consultas distribuídas.


Neste tutorial, ficará a saber:

> [!div class="checklist"]
> 
> * Como implantar um banco de dados de relatórios ad hoc
> * Como executar consultas distribuídas em todos os bancos de dados de locatário


Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* O aplicativo de banco de dados multilocatário do Wingtip tickets SaaS foi implantado. Para implantar em menos de cinco minutos, consulte [implantar e explorar o aplicativo de banco de dados multilocatário do Wingtip tickets SaaS](saas-multitenantdb-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* O SQL Server Management Studio (SSMS) está instalado. Para baixar e instalar o SSMS, consulte [baixar SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Padrão de relatório ad hoc

![padrão de relatórios ad hoc](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

Os aplicativos SaaS podem analisar a vasta quantidade de dados de locatário que são armazenados centralmente na nuvem. As análises revelam informações sobre a operação e o uso do seu aplicativo. Essas informações podem guiar o desenvolvimento de recursos, melhorias de usabilidade e outros investimentos em seus aplicativos e serviços.

Aceder a estes dados numa única base de dados multi-inquilinos é fácil, mas não será tão fácil se houver uma distribuição à escala através de potencialmente milhares de bases de dados. Uma abordagem é usar a [consulta elástica](sql-database-elastic-query-overview.md), que permite a consulta em um conjunto distribuído de bancos de dados com esquema comum. Esses bancos de dados podem ser distribuídos em diferentes grupos de recursos e assinaturas. No entanto, um logon comum deve ter acesso para extrair dados de todos os bancos de dado. A consulta elástica usa um único banco de dados *principal* no qual são definidas tabelas externas que espelham tabelas ou exibições nos bancos de dados distribuídos (locatário). As consultas submetidas para esta base de dados “head” são compiladas para produzir um plano de consultas distribuídas, com partes das consultas enviadas para as bases de dados inquilinas, conforme necessário. A consulta elástica usa o mapa de fragmentos no banco de dados de catálogo para determinar o local de todos os bancos de dados de locatário. A instalação e a consulta são diretas usando o [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)padrão e dão suporte à consulta ad hoc de ferramentas como Power bi e Excel.

Ao distribuir consultas entre os bancos de dados de locatários, a consulta elástica fornece informações imediatas sobre o Live Production Data. No entanto, como a consulta elástica efetua pull de dados de potencialmente muitos bancos de dado, às vezes a latência de consulta pode ser maior do que para consultas equivalentes enviadas a um único banco de dados multilocatário. Certifique-se de criar consultas para minimizar os dados retornados. A consulta elástica geralmente é mais adequada para a consulta de pequenas quantidades de dados em tempo real, em oposição à criação de consultas ou relatórios de análise complexos ou usados com frequência. Se as consultas não tiverem um bom desempenho, examine o [plano de execução](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) para ver qual parte da consulta foi enviada por push para o banco de dados remoto. E avaliar a quantidade de dados que está sendo retornada. As consultas que exigem processamento analítico complexo podem ser melhor servidas salvando os dados extraídos do locatário em um banco de dado que é otimizado para consultas de análise. O banco de dados SQL e o SQL Data Warehouse podem hospedar esse banco de dados de análise.

Esse padrão para análise é explicado no [tutorial de análise de locatário](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obtenha o código-fonte e os scripts do aplicativo de banco de dados multilocatário SaaS Wingtip tickets

Os scripts de banco de dados multilocatário do Wingtip tickets SaaS e o código-fonte do aplicativo estão disponíveis no repositório GitHub [repositório wingtipticketssaas-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) . Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip tickets.

## <a name="create-ticket-sales-data"></a>Criar dados de vendas de tíquetes

Para executar consultas em um conjunto de dados mais interessante, crie dados de vendas de tíquetes executando o gerador de tíquetes.

1. No *ISE do PowerShell*, abra o... \\ Módulos de aprendizagemanálise\\operacional ad hoc Reporting demo-adhocreporting. ps1 script e defina os seguintes valores:\\ \\
   * **$DemoScenario** = 1, **comprar tíquetes para eventos em todos os locais**.
2. Pressione **F5** para executar o script e gerar vendas de tíquetes. Enquanto o script estiver em execução, continue as etapas neste tutorial. Os dados do tíquete são consultados na seção *executar consultas ad hoc distribuídas* , portanto, aguarde até que o gerador de tíquetes seja concluído.

## <a name="explore-the-tenant-tables"></a>Explorar as tabelas de locatário 

No aplicativo de banco de dados multilocatário do Wingtip tickets SaaS, os locatários são armazenados em um modelo de gerenciamento de locatário híbrido, em que os dados de locatário são armazenados em um banco de dados multilocatário ou um banco de dado de locatário único e podem ser movidos entre os dois. Ao consultar em todos os bancos de dados de locatário, é importante que a consulta elástica possa tratar os dados como se ele fosse parte de um único banco de dado lógico fragmentado por locatário. 

Para obter esse padrão, todas as tabelas de locatário  incluem uma coluna de foroid que identifica a qual locatário os dados pertencem. O *foroid* é calculado como um hash do nome do local, mas qualquer abordagem pode ser usada para introduzir um valor exclusivo para essa coluna. Essa abordagem é semelhante à forma como a chave de locatário é computada para uso no catálogo. As tabelas  que contêm o LocalId são usadas pela consulta elástica para paralelizar consultas e enviá-las por push para o banco de dados de locatário remoto apropriado. Isso reduz drasticamente a quantidade de dados retornados e resulta em um aumento no desempenho, especialmente quando há vários locatários cujos dados são armazenados em bancos de dado de locatário único.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Implantar o banco de dados usado para consultas distribuídas ad hoc

Este exercício implanta o banco de dados *adhocreporting* . Esse é o banco de dados principal que contém o esquema usado para consultar em todos os bancos de dados de locatário. O banco de dados é implantado no servidor de catálogo existente, que é o servidor usado para todos os bancos de dados relacionados ao gerenciamento no aplicativo de exemplo.

1. Abrir... \\Módulos\\   de aprendizado análise operacionalrelatórios\\ad hoc demo-adhocreporting. ps1 no ISE do PowerShell e defina os seguintes valores:\\
   * **$DemoScenario** = 2, **implantar o banco de dados de análise ad hoc**.

2. Pressione **F5** para executar o script e criar o banco de dados *adhocreporting* .

Na próxima seção, você adiciona o esquema ao banco de dados para que ele possa ser usado para executar consultas distribuídas.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configurar o banco de dados ' Head ' para executar consultas distribuídas

Este exercício adiciona o esquema (a fonte de dados externa e as definições de tabela externa) ao banco de dados de relatórios ad hoc que permite a consulta em todos os bancos de dado de locatário.

1. Abra SQL Server Management Studio e conecte-se ao banco de dados de relatórios ad hoc criado na etapa anterior. O nome do banco de dados é *adhocreporting*.
2. Abra. ..\Learning Modules\Operational Analytics\Adhoc Reporting \ *Initialize-AdhocReportingDB. SQL* no SSMS.
3. Examine o script SQL e observe o seguinte:

   A consulta elástica usa uma credencial no escopo do banco de dados para acessar cada um dos bancos de dados de locatário. Essa credencial precisa estar disponível em todos os bancos de dados e normalmente deve receber os direitos mínimos necessários para habilitar essas consultas ad hoc.

    ![Criar credencial](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Ao usar o banco de dados de catálogo como fonte externa, as consultas são distribuídas para todos os bancos de dado registrados no catálogo quando a consulta é executada. Como os nomes de servidor são diferentes para cada implantação, esse script de inicialização Obtém o local do banco de dados de catálogo recuperando o@servernameservidor atual (@) em que o script é executado.

    ![criar fonte de dados externa](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   As tabelas externas que fazem referência a tabelas de locatário são definidas com **Distribution = fragmented (foroid)** . Isso roteia uma consulta para um *foroid* específico para o banco de dados apropriado e melhora o desempenho para muitos cenários, conforme mostrado na próxima seção.

    ![criar tabelas externas](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   A tabela local *VenueTypes* que é criada e populada. Essa tabela de dados de referência é comum em todos os bancos de dado de locatário, para que possa ser representada aqui como uma tabela local e preenchida com os dados comuns. Para algumas consultas, isso pode reduzir a quantidade de dados movidos entre os bancos de dado de locatário e o banco de *adhocreporting* .

    ![criar tabela](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Se você incluir tabelas de referência dessa maneira, certifique-se de atualizar o esquema de tabela e os dados sempre que atualizar os bancos de dado de locatário.

4. Pressione **F5** para executar o script e inicializar o banco de dados *adhocreporting* . 

Agora você pode executar consultas distribuídas e coletar informações em todos os locatários!

## <a name="run-ad-hoc-distributed-queries"></a>Executar consultas distribuídas ad hoc

Agora que o banco de dados *adhocreporting* está configurado, vá em frente e execute algumas consultas distribuídas. Inclua o plano de execução para uma melhor compreensão de onde o processamento de consulta está ocorrendo. 

Ao inspecionar o plano de execução, passe o mouse sobre os ícones de plano para obter detalhes. 

1. No *SSMS*, abra... \\Módulos\\de aprendizado análise operacionalrelatórios\\ad hoc demo-adhocreportingqueries. Sql.\\
2. Verifique se você está conectado ao banco de dados **adhocreporting** .
3. Selecione o menu **consulta** e clique em **incluir plano de execução real**
4. Realce os *locais que estão registrados atualmente?* consulte e pressione **F5**.

   A consulta retorna toda a lista de locais, ilustrando como é rápido e fácil consultar em todos os locatários e retornar dados de cada locatário.

   Inspecione o plano e veja que todo o custo é a consulta remota porque estamos simplesmente indo para cada banco de dados de locatário e selecionando as informações do local.

   ![SELECT * FROM dbo.Venues](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Selecione a próxima consulta e pressione **F5**.

   Essa consulta une dados dos bancos de dado de locatário e da tabela *VenueTypes* local (local, pois é uma tabela no banco de dados *adhocreporting* ).

   Inspecione o plano e veja que a maior parte do custo é a consulta remota, pois consultamos as informações de local de cada locatário (dbo. Locais) e, em seguida, faça uma junção local rápida com a tabela *VenueTypes* local para exibir o nome amigável.

   ![Ingressar em dados remotos e locais](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Agora, selecione o *em que dia foram os mais ingressos vendidos?* e pressione **F5**.

   Essa consulta faz uma junção e agregação mais complexas. O que é importante observar é que a maior parte do processamento é feita remotamente e, mais uma vez, retornamos apenas as linhas de que precisamos, voltando apenas uma única linha para a contagem de venda de tíquete agregado por dia de cada local.

   ![query](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> 
> * Executar consultas distribuídas em todas as bases de dados do inquilino
> * Implante um banco de dados de relatórios ad hoc e adicione o esquema a ele para executar consultas distribuídas.

Agora experimente o [tutorial de análise de locatário](saas-multitenantdb-tenant-analytics.md) para explorar a extração de dados para um banco de dado de análise separado para um processamento de análise mais complexo.

## <a name="additional-resources"></a>Recursos adicionais

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Consulta Elástica](sql-database-elastic-query-overview.md)
