---
title: Consultas de reporte ad hoc em várias bases de dados
description: Execute consultas de reporte ad hoc em várias bases de dados Azure SQL em um exemplo de aplicativo multi-inquilino.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/30/2018
ms.openlocfilehash: 8e4f042e162408fef44b5c525b3a737e37005fdd
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84042513"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-databases-azure-sql-database"></a>Executar consultas de análise ad hoc em várias bases de dados (Base de Dados Azure SQL)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você executa consultas distribuídas em todo o conjunto de bases de dados de inquilinos para permitir relatórios interativos ad hoc. Estas consultas podem extrair insights enterrados nos dados operacionais do dia-a-dia da app Wingtip Tickets SaaS. Para então, implementa uma base de dados de análise adicional para o servidor de catálogo e utiliza a Elástica Para permitir consultas distribuídas.


Neste tutorial, ficará a saber:

> [!div class="checklist"]
> 
> * Como implementar uma base de dados de relatórios ad hoc
> * Como executar consultas distribuídas em todas as bases de dados de inquilinos


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação de base de dados de bilhetes SaaS Multi-inquilinos SaaS está implementada. Para implantar em menos de cinco minutos, consulte Implementar e explorar a aplicação de [base de dados multi-inquilinos SaaS](saas-multitenantdb-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* O Estúdio de Gestão de Servidores SQL (SSMS) está instalado. Para descarregar e instalar SSMS, consulte [download SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Padrão de reporte ad hoc

![padrão de reporte adhoc](./media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

As aplicações SaaS podem analisar a vasta quantidade de dados de inquilinos que são armazenados centralmente na nuvem. As análises revelam insights sobre o funcionamento e utilização da sua aplicação. Estes insights podem orientar o desenvolvimento de funcionalidades, melhorias de usabilidade e outros investimentos nas suas apps e serviços.

Aceder a estes dados numa única base de dados multi-inquilinos é fácil, mas não será tão fácil se houver uma distribuição à escala através de potencialmente milhares de bases de dados. Uma abordagem é utilizar [a Consulta Elástica,](elastic-query-overview.md)que permite consultar um conjunto distribuído de bases de dados com esquemacomum comum. Estas bases de dados podem ser distribuídas por diferentes grupos de recursos e subscrições. No entanto, um login comum deve ter acesso a extrair dados de todas as bases de dados. A Consulta Elástica utiliza uma única base de dados de *cabeça* na qual são definidas tabelas externas que mesas de espelho ou pontos de vista nas bases de dados distribuídas (inquilino). As consultas submetidas para esta base de dados “head” são compiladas para produzir um plano de consultas distribuídas, com partes das consultas enviadas para as bases de dados inquilinas, conforme necessário. A Elástico Consulta usa o mapa de fragmentos na base de dados do catálogo para determinar a localização de todas as bases de dados dos inquilinos. Configuração e consulta são simples usando [o padrão Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference), e suporte a consulta ad hoc de ferramentas como Power BI e Excel.

Ao distribuir consultas pelas bases de dados dos inquilinos, a Elástico Consulta fornece informações imediatas sobre os dados de produção ao vivo. No entanto, à medida que a Pergunta Elástica retira dados de bases de dados potencialmente, a latência de consultas pode por vezes ser maior do que para consultas equivalentes submetidas a uma única base de dados multi-inquilinos. Certifique-se de que designa consultas para minimizar os dados que são devolvidos. A consulta elástica é muitas vezes mais adequada para consulta de pequenas quantidades de dados em tempo real, em oposição à construção de consultas ou relatórios de análise frequentemente usados ou complexos. Se as consultas não funcionarem bem, veja o plano de [execução](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) para ver que parte da consulta foi empurrada para a base de dados remota. E avaliar quantos dados estão a ser devolvidos. As consultas que requerem um processamento analítico complexo podem ser melhor servidas, guardando os dados extraídos do inquilino numa base de dados otimizada para consultas de análise. A Base de Dados SQL e o SQL Data Warehouse poderiam alojar essa base de dados de análise.

Este padrão de análise é explicado no tutorial de análise de [inquilinos.](saas-multitenantdb-tenant-analytics.md)

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obtenha os bilhetes De acesso saaS Multi-inquilinoCódigo fonte de aplicação e scripts

Os bilhetes Wingtip SaaS Scripts de base de dados multi-inquilinos e código fonte de aplicação estão disponíveis no repo [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub. Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para os passos para descarregar e desbloquear os scripts Wingtip Tickets SaaS.

## <a name="create-ticket-sales-data"></a>Criar dados de venda de bilhetes

Para executar consultas contra um conjunto de dados mais interessante, crie dados de venda de bilhetes executando o gerador de bilhetes.

1. No *PowerShell ISE,* abra o ... \\ Módulos de Aprendizagem \\ Operacional Analytics \\ Adhoc Reporting \\ *Demo-AdhocReporting.ps1* script e definir os seguintes valores:
   * **$DemoScenario** = 1, **Comprar bilhetes para eventos em todos os locais.**
2. Pressione **F5** para executar o script e gerar venda de bilhetes. Enquanto o guião está em execução, continue os passos neste tutorial. Os dados do bilhete são consultados na secção *de consultas distribuídas Run ad hoc,* por isso aguarde que o gerador de bilhetes esteja concluído.

## <a name="explore-the-tenant-tables"></a>Explore as mesas dos inquilinos 

Na aplicação de base de dados de bilhetes SaaS Multi-inquilinos Wingtip, os inquilinos são armazenados num modelo híbrido de gestão de inquilinos - onde os dados dos inquilinos são armazenados numa base de dados multi-inquilinos ou numa única base de dados de inquilinos e podem ser movidos entre os dois. Ao consultar todas as bases de dados dos inquilinos, é importante que a Elástico Consulta possa tratar os dados como se fizesse parte de uma única base de dados lógica espalhada pelo inquilino. 

Para alcançar este padrão, todas as mesas de inquilinos incluem uma coluna *VenueId* que identifica a qual inquilino os dados pertencem. O *VenueId* é calculado como um hash do nome Local, mas qualquer abordagem poderia ser usada para introduzir um valor único para esta coluna. Esta abordagem é semelhante à forma como a chave do inquilino é calculada para utilização no catálogo. As tabelas que contêm *o VenueId* são utilizadas pela Elástica Consulta para paralelar consultas e empurrá-las para a base de dados de inquilinos remotos apropriado. Isto reduz drasticamente a quantidade de dados que são devolvidos e resulta num aumento do desempenho, especialmente quando há vários inquilinos cujos dados são armazenados em bases de dados de inquilinos individuais.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Implementar a base de dados utilizada para consultas distribuídas ad hoc

Este exercício implementa a base de dados de *informação adhoc.* Esta é a base de dados principal que contém o esquema usado para consultar todas as bases de dados de inquilinos. A base de dados é implementada para o servidor de catálogo existente, que é o servidor utilizado para todas as bases de dados relacionadas com a gestão na aplicação da amostra.

1. Aberto... \\ Módulos de Aprendizagem \\ Operacional Analytics \\ Adhoc Reporting \\ *Demo-AdhocReporting.ps1* no *PowerShell ISE* e definir os seguintes valores:
   * **$DemoScenario** = 2, **Implementar base de dados de análise ad hoc**.

2. Pressione **F5** para executar o script e crie a base de dados de *adhocreporting.*

Na secção seguinte, adicione esquema à base de dados para que possa ser utilizado para executar consultas distribuídas.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configure a base de dados 'head' para executar consultas distribuídas

Este exercício adiciona esquema (a fonte de dados externa e definições de tabela externa) à base de dados de relatórios ad hoc que permite consultar todas as bases de dados dos inquilinos.

1. Abra o Estúdio de Gestão de Servidores SQL e ligue-se à base de dados de relatórios Adhoc que criou no passo anterior. O nome da base de dados é *adhocreporting*.
2. Open ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql* in SSMS.
3. Reveja o script SQL e note o seguinte:

   A Consulta Elástica utiliza uma credencial com um espaço de dados para aceder a cada uma das bases de dados dos inquilinos. Esta credencial tem de estar disponível em todas as bases de dados e deve normalmente ser-lhe concedidos os direitos mínimos necessários para permitir estas consultas ad hoc.

    ![Criar credencial](./media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Utilizando a base de dados do catálogo como fonte de dados externo, as consultas são distribuídas a todas as bases de dados registadas no catálogo quando a consulta é executada. Como os nomes do servidor são diferentes para cada implementação, este script de inicialização obtém a localização da base de dados do catálogo recuperando o servidor atual (@ @servername ) onde o script é executado.

    ![criar fonte de dados externas](./media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   As tabelas externas que referenciam tabelas de inquilinos são definidas com **DISTRIBUIÇÃO = SHARDED(VenueId)**. Isto encaminha uma consulta para um *determinado VenueId* para a base de dados apropriada e melhora o desempenho de muitos cenários, como mostrado na secção seguinte.

    ![criar tabelas externas](./media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   A mesa local *VenueTypes* que é criado e povoado. Esta tabela de dados de referência é comum em todas as bases de dados dos inquilinos, pelo que pode ser representada aqui como uma tabela local e povoada com os dados comuns. Para algumas consultas, isto pode reduzir a quantidade de dados deslocados entre as bases de dados dos inquilinos e a base de dados de *informação adhoc.*

    ![criar tabela](./media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Se incluir tabelas de referência desta forma, certifique-se de atualizar o esquema da tabela e os dados sempre que atualizar as bases de dados dos inquilinos.

4. Pressione **F5** para executar o script e inicialize a base de dados de *adhocreporting.* 

Agora você pode executar consultas distribuídas, e recolher insights em todos os inquilinos!

## <a name="run-ad-hoc-distributed-queries"></a>Executar consultas distribuídas ad hoc

Agora que a base de dados de *adhocreporting* está configurada, vá em frente e execute algumas consultas distribuídas. Inclua o plano de execução para uma melhor compreensão de onde o processamento de consulta está acontecendo. 

Ao inspecionar o plano de execução, passe sobre os ícones do plano para obter detalhes. 

1. No *SSMS,* aberto... \\ Módulos de Aprendizagem \\ Operacional Analytics \\ Adhoc Reporting \\ *Demo-AdhocReportingQueries.sql*.
2. Certifique-se de que está ligado à base de dados de **informação adhoc.**
3. Selecione o menu **De consulta** e clique em Incluir Plano de **Execução Real**
4. Destacar os **F5** *locais que estão atualmente registados?*

   A consulta devolve toda a lista do local, ilustrando como é rápido e fácil consultar todos os inquilinos e devolver dados de cada inquilino.

   Inspecione o plano e verifique se todo o custo é a consulta remota porque vamos simplesmente a cada base de dados de inquilinos e selecionamos a informação do local.

   ![SELECIONE * FROM dbo. Locais](./media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Selecione a próxima consulta e prima **F5**.

   Esta consulta junta dados das bases de dados dos inquilinos e da tabela *Local VenueTypes* (local, uma vez que é uma tabela na base de dados de *adhocreporting).*

   Inspecione o plano e verifique se a maioria do custo é a consulta remota porque consultamos as informações do local de cada inquilino (dbo. Locais), e, em seguida, fazer uma rápida reunião local com a mesa *Local VenueTypes* para mostrar o nome amigável.

   ![Junte-se a dados remotos e locais](./media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Agora selecione o Dia em que foram mais **F5** *vendidos?*

   Esta consulta faz um pouco mais complexo de junção e agregação. O que é importante notar é que a maior parte do processamento é feito remotamente, e mais uma vez, trazemos de volta apenas as linhas que precisamos, devolvendo apenas uma única linha para a contagem de venda de bilhetes agregado por dia de cada local.

   ![consulta](./media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Próximos passos

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> 
> * Executar consultas distribuídas em todas as bases de dados do inquilino
> * Implemente uma base de dados de relatórios ad hoc e adicione-lhe esquemas para executar consultas distribuídas.

Agora experimente o tutorial do [Tenant Analytics](saas-multitenantdb-tenant-analytics.md) para explorar a extração de dados para uma base de dados de análise separada para um processamento de análise mais complexo.

## <a name="additional-resources"></a>Recursos adicionais

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Consulta Elástica](elastic-query-overview.md)
