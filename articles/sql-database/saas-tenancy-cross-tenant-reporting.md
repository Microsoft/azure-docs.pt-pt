---
title: Consultas de reporte em várias bases de dados
description: Relatórios de inquilinos cruzados usando consultas distribuídas.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewers: billgib,ayolubek
ms.date: 01/25/2019
ms.openlocfilehash: c863946934df9990c14e49ef1a0a82bbc55b27c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822085"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Relatórios de inquilinos cruzados usando consultas distribuídas

Neste tutorial, você executa consultas distribuídas em todo o conjunto de bases de dados de inquilinos para reporte. Estas consultas podem extrair insights enterrados nos dados operacionais do dia-a-dia dos inquilinos wingtip Tickets SaaS. Para tal, implementa uma base de dados adicional de reporte para o servidor de catálogo e utiliza a Elástico Para ativar consultas distribuídas.


Neste tutorial, ficará a saber:

> [!div class="checklist"]
> 
> * Como implementar uma base de dados de relatórios
> * Como executar consultas distribuídas em todas as bases de dados de inquilinos
> * Como as vistas globais em cada base de dados podem permitir consultas eficientes entre os inquilinos


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:


* A aplicação Wingtip Tickets SaaS Database Per Tenant está implementada. Para implantar em menos de cinco minutos, consulte Implementar e explorar a base de [dados saaS de bilhetes wingtip por pedido](saas-dbpertenant-get-started-deploy.md) de inquilino
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* O Estúdio de Gestão de Servidores SQL (SSMS) está instalado. Para descarregar e instalar SSMS, consulte [download SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Padrão de reporte de inquilinos cruzados

![padrão de consulta distribuída pelo inquilino cruzado](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Uma oportunidade com aplicações SaaS é usar a grande quantidade de dados de inquilinos armazenados na nuvem para obter informações sobre o funcionamento e uso da sua aplicação. Estes insights podem orientar o desenvolvimento de funcionalidades, melhorias de usabilidade e outros investimentos nas suas apps e serviços.

Aceder a estes dados numa única base de dados multi-inquilinos é fácil, mas não será tão fácil se houver uma distribuição à escala através de potencialmente milhares de bases de dados. Uma abordagem é utilizar [a Consulta Elástica,](sql-database-elastic-query-overview.md)que permite consultar um conjunto distribuído de bases de dados com esquemacomum comum. Estas bases de dados podem ser distribuídas por diferentes grupos de recursos e subscrições, mas precisam de partilhar um login comum. A Consulta Elástica utiliza uma única base de dados de *cabeça* na qual são definidas tabelas externas que mesas de espelho ou pontos de vista nas bases de dados distribuídas (inquilino). As consultas submetidas para esta base de dados “head” são compiladas para produzir um plano de consultas distribuídas, com partes das consultas enviadas para as bases de dados inquilinas, conforme necessário. A Elástico Consulta usa o mapa de fragmentos na base de dados do catálogo para determinar a localização de todas as bases de dados dos inquilinos. A configuração e consulta da base de dados principal são simples usando o [padrão Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference), e suporte a consulta de ferramentas como Power BI e Excel.

Ao distribuir consultas pelas bases de dados dos inquilinos, a Elástico Consulta fornece informações imediatas sobre os dados de produção ao vivo. À medida que a Pergunta Elástica retira dados de bases de dados potencialmente, a latência de consultas pode ser superior às consultas equivalentes submetidas a uma única base de dados multi-inquilinos. Desenhe consultas para minimizar os dados que são devolvidos à base de dados principal. A consulta elástica é muitas vezes mais adequada para consulta de pequenas quantidades de dados em tempo real, em oposição à construção de consultas ou relatórios de análise frequentemente usados ou complexos. Se as consultas não funcionarem bem, veja o plano de [execução](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) para ver que parte da consulta é empurrada para a base de dados remota e quantos dados estão a ser devolvidos. As consultas que requerem agregação complexa ou o processamento analítico podem ser melhor tratadas através da extração de dados dos inquilinos numa base de dados ou num armazém de dados otimizado para consultas de análise. Este padrão é explicado no tutorial de análise do [inquilino.](saas-tenancy-tenant-analytics.md) 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenha os bilhetes de ala SaaS Database per Tenant scripts de aplicação

Os bilhetes Wingtip SaaS Scripts de base de dados multi-inquilinos e código fonte de aplicação estão disponíveis no repo [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para os passos para descarregar e desbloquear os scripts Wingtip Tickets SaaS.

## <a name="create-ticket-sales-data"></a>Criar dados de venda de bilhetes

Para executar consultas contra um conjunto de dados mais interessante, crie dados de venda de bilhetes executando o gerador de bilhetes.

1. No *PowerShell ISE,* abra o ... \\Módulos\\de\\Aprendizagem Operacional\\Analytics Adhoc Reporting*Demo-AdhocReporting.ps1* script e definir o seguinte valor:
   * **$DemoScenario** = 1, **Comprar bilhetes para eventos em todos os locais.**
2. Pressione **F5** para executar o script e gerar venda de bilhetes. Enquanto o guião está em execução, continue os passos neste tutorial. Os dados do bilhete são consultados na secção *de consultas distribuídas Run ad hoc,* por isso aguarde que o gerador de bilhetes esteja concluído.

## <a name="explore-the-global-views"></a>Explore as vistas globais

Na aplicação Wingtip Tickets SaaS Database Per Tenant, cada inquilino recebe uma base de dados. Assim, os dados contidos nas tabelas de bases de dados são traçados na perspetiva de um único inquilino. No entanto, ao consultar todas as bases de dados, é importante que a Elástico Consulta possa tratar os dados como se fizesse parte de uma única base de dados lógica espalhada pelo inquilino. 

Para simular este padrão, um conjunto de pontos de vista 'globais' são adicionados à base de dados dos inquilinos que projetam uma identificação de inquilino em cada uma das tabelas que são consultadas globalmente. Por exemplo, a vista *VenueEvents* adiciona um *LocalId* computado às colunas projetadas a partir da tabela *Eventos.* Da mesma forma, as vistas *do VenueTicketBuys* e *VenueTickets* adicionam uma coluna *Computação VenueId* projetada a partir das respetivas tabelas. Estas vistas são usadas pela Elástica Consulta para paralelo sibalizar consultas e empurrá-las para a base de dados de inquilinos remotos apropriado quando uma coluna *VenueId* está presente. Isto reduz drasticamente a quantidade de dados que são devolvidos e resulta num aumento substancial do desempenho para muitas consultas. Estas opiniões globais foram pré-criadas em todas as bases de dados dos inquilinos.

1. Abra o SSMS e [ligue-se&lt;aos inquilinos1- servidor USER&gt; ](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Expandir Bases de **Dados,** clique à direita _contosoconcerthall,_ e **selecione New Query**.
1. Execute as seguintes consultas para explorar a diferença entre as mesas de inquilino único e as vistas globais:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

Nestas vistas, o *VenueId* é calculado como um hash do nome Local, mas qualquer abordagem poderia ser usada para introduzir um valor único. Esta abordagem é semelhante à forma como a chave do inquilino é calculada para utilização no catálogo.

Para examinar a definição da visão dos *Locais:*

1. No **Object Explorer,** expanda**os pontos**de vista **contosoconcerthall:** > 

   ![Modos de exibição](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Dbo de clique **direito. Locais.**
3. Selecione **script view como** > **CREATE para** > **nova janela de editor de consulta**

Script qualquer uma das outras vistas *do Local* para ver como eles adicionam o *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Implementar a base de dados utilizada para consultas distribuídas

Este exercício implementa a base de dados de _informação adhoc._ Esta é a base de dados principal que contém o esquema usado para consultar todas as bases de dados de inquilinos. A base de dados é implementada para o servidor de catálogo existente, que é o servidor utilizado para todas as bases de dados relacionadas com a gestão na aplicação da amostra.

1. no *PowerShell ISE,* aberto... \\Módulos\\de\\Aprendizagem Operacional\\Analytics Adhoc Reporting*Demo-AdhocReporting.ps1*. 

1. Definir **$DemoScenario = 2**, Implementar base de dados de _relatórios ad hoc_.

1. Pressione **F5** para executar o script e crie a base de dados de *adhocreporting.*

Na secção seguinte, adicione esquema à base de dados para que possa ser utilizado para executar consultas distribuídas.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configure a base de dados 'head' para executar consultas distribuídas

Este exercício adiciona esquema (a fonte de dados externa e definições de tabela externa) à base de dados de _adhocreporting_ para permitir a consulta em todas as bases de dados dos inquilinos.

1. Abra o Estúdio de Gestão de Servidores SQL e ligue-se à base de dados de Relatórios Adhoc que criou no passo anterior. O nome da base de dados é *adhocreporting*.
2. Open ...\Learning Modules\Operational Analytics\Adhoc Reporting\ _Initialize-AdhocReportingDB.sql_ in SSMS.
3. Reveja o script E nota SQL:

   A Consulta Elástica utiliza uma credencial com um espaço de dados para aceder a cada uma das bases de dados dos inquilinos. Esta credencial tem de estar disponível em todas as bases de dados e deve normalmente ser-lhe concedidos os direitos mínimos necessários para permitir essas consultas.

    ![Criar credencial](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Com a base de dados do catálogo como fonte de dados externo, as consultas são distribuídas a todas as bases de dados registadas no catálogo no momento em que a consulta é executado. Como os nomes do servidor são diferentes para cada implementação,@servernameeste script obtém a localização da base de dados do catálogo a partir do servidor atual (@ ) onde o script é executado.

    ![criar fonte de dados externas](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   As tabelas externas que referem as opiniões globais descritas na secção anterior, e definidas com **DISTRIBUIÇÃO = SHARDED(VenueId)**. Como cada *VenueId* mapeia para uma base de dados individual, isto melhora o desempenho de muitos cenários, como mostrado na secção seguinte.

    ![criar tabelas externas](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   A mesa local _VenueTypes_ que é criado e povoado. Esta tabela de dados de referência é comum em todas as bases de dados dos inquilinos, pelo que pode ser representada aqui como uma tabela local e povoada com os dados comuns. Para algumas consultas, ter esta tabela definida na base de dados da cabeça pode reduzir a quantidade de dados que precisa de ser transferido para a base de dados principal.

    ![criar tabela](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Se incluir tabelas de referência desta forma, certifique-se de atualizar o esquema da tabela e os dados sempre que atualizar as bases de dados dos inquilinos.

4. Pressione **F5** para executar o script e inicialize a base de dados de *adhocreporting.* 

Agora você pode executar consultas distribuídas, e recolher insights em todos os inquilinos!

## <a name="run-distributed-queries"></a>Executar consultas distribuídas

Agora que a base de dados de *adhocreporting* está configurada, vá em frente e execute algumas consultas distribuídas. Inclua o plano de execução para uma melhor compreensão de onde o processamento de consulta está acontecendo. 

Ao inspecionar o plano de execução, passe sobre os ícones do plano para obter detalhes. 

É importante notar que a definição **de DISTRIBUIÇÃO = SHARDED(VenueId)** quando a fonte de dados externa é definida melhora o desempenho para muitos cenários. À medida que cada *VenueId* mapeia uma base de dados individual, a filtragem é facilmente feita remotamente, devolvendo apenas os dados necessários.

1. Aberto... \\\\Módulos de\\Aprendizagem Operacional\\Analytics Adhoc Reporting*Demo-AdhocReportingQueries.sql* em SSMS.
2. Certifique-se de que está ligado à base de dados de **informação adhoc.**
3. Selecione o menu **De consulta** e clique em Incluir Plano de **Execução Real**
4. Destacar os **F5** *locais que estão atualmente registados?*

   A consulta devolve toda a lista do local, ilustrando a rapidez e facilidade com que é consultar todos os inquilinos e devolver dados de cada inquilino.

   Inspecione o plano e verifique se todo o custo está na consulta remota. Cada base de dados de inquilinoexecuta a consulta remotamente e devolve as informações do local à base de dados principal.

   ![SELECIONE * FROM dbo. Locais](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Selecione a próxima consulta e prima **F5**.

   Esta consulta junta dados das bases de dados dos inquilinos e da tabela *Local VenueTypes* (local, uma vez que é uma tabela na base de dados de *adhocreporting).*

   Inspecione o plano e verifique se a maioria dos custos é a consulta remota. Cada base de dados de inquilinos devolve as informações do local e realiza uma reunião local com a mesa *Local VenueTypes* para mostrar o nome amigável.

   ![Junte-se a dados remotos e locais](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Agora selecione o Dia em que foram mais **F5** *vendidos?*

   Esta consulta faz um pouco mais complexo de junção e agregação. A maior parte do processamento ocorre remotamente.  Apenas filas individuais, contendo a contagem diária de venda de bilhetes por dia de cada local, são devolvidas à base de dados principal.

   ![consulta](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> 
> * Executar consultas distribuídas em todas as bases de dados do inquilino
> * Implementar uma base de dados de reporte e definir o esquema necessário para executar consultas distribuídas.


Agora experimente o tutorial do [Tenant Analytics](saas-tenancy-tenant-analytics.md) para explorar a extração de dados para uma base de dados de análise separada para um processamento de análise mais complexo.

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam na base de dados SaaS de Bilhetes De Ponta Por Inquilino](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Consulta Elástica](sql-database-elastic-query-overview.md)
