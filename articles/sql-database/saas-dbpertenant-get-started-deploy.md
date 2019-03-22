---
title: Tutorial de SaaS de base de dados por inquilino - base de dados SQL do Azure | Documentos da Microsoft
description: Implementar e explorar a aplicação multi-inquilino de Wingtip Tickets SaaS que demonstra o padrão de base de dados por inquilino e outros padrões de SaaS ao utilizar a base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 97570a16c7d87a3c8182909b61c04fde30b3fe9b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58000201"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Implementar e explorar uma aplicação SaaS multi-inquilino que utiliza o padrão de base de dados por inquilino com a base de dados SQL

Neste tutorial, implementar e explorar a aplicação de base de dados por inquilino Wingtip Tickets SaaS (Wingtip). A aplicação utiliza um padrão de base de dados por inquilino para armazenar os dados de vários inquilinos. A aplicação foi concebida para demonstrar as funcionalidades da base de dados do Azure SQL que simplificam como ativar cenários SaaS.

Cinco minutos depois de selecionar **implementar no Azure**, terá uma aplicação SaaS multi-inquilino. A aplicação inclui uma base de dados do SQL que é executado na cloud. A aplicação é implementada com três inquilinos de exemplo, cada um com sua própria base de dados. Todas as bases de dados são implementadas num conjunto elástico do SQL. A aplicação é implementada a sua subscrição do Azure. Tem acesso total para explorar e trabalhar com os componentes individuais da aplicação. O código de origem de c# da aplicação e os scripts de gestão estão disponíveis no [repositório do GitHub WingtipTicketsSaaS DbPerTenant][github-wingtip-dpt].

Neste tutorial, ficará a saber:

> [!div class="checklist"]
> - Como implementar a aplicação Wingtip SaaS.
> - Onde obter a origem da aplicação código e scripts de gestão.
> - Sobre os servidores, conjuntos e bases de dados que constituem a aplicação.
> - Como os inquilinos são mapeados para os dados com o *catálogo*.
> - Como aprovisionar um novo inquilino.
> - Como monitorizar a atividade de inquilino na aplicação.

R [série de tutoriais relacionados](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) oferece para explorar vários padrões de conceção e gestão de SaaS. Os tutoriais de compilação para além desta implementação inicial. Quando utiliza os tutoriais, pode examinar os scripts fornecidos para ver como os diferentes padrões SaaS são implementados. Os scripts de demonstram como funcionalidades de base de dados SQL simplificam o desenvolvimento de aplicações SaaS.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, certifique-se de que o Azure PowerShell está instalado. Para obter mais informações, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Implementar a aplicação Wingtip Tickets SaaS

### <a name="plan-the-names"></a>Planear os nomes

Os passos desta secção, é fornecer um valor de utilizador que é utilizado para fazer os nomes de recursos-se de que são globalmente exclusivo. Também fornecer um nome para o grupo de recursos que contém todos os recursos criados por uma implementação da aplicação. Para uma pessoa fictícia com o nome Ann Finley, sugerimos:

- **Usuário**: *af1* é constituído por iniciais de Ann Finley mais um dígito. Se implementar a aplicação uma segunda vez, utilize um valor diferente. Um exemplo é af2.
- **Grupo de recursos**: *wingtip-dpt-af1* indica que esta é a aplicação de base de dados por inquilino. Acrescente a af1 de nome de utilizador para correlacionar o nome do grupo de recursos com os nomes de recursos que contém.

Escolha os nomes de agora e anotá-las.

### <a name="steps"></a>Passos

1. Para abrir o modelo de implementação de base de dados por inquilino Wingtip Tickets SaaS no portal do Azure, selecione **implementar no Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Introduza os valores no modelo para os parâmetros necessários.

    > [!IMPORTANT]
    > Alguns firewalls de autenticação e o servidor são intencionalmente não seguras para fins de demonstração. Recomendamos que crie um novo grupo de recursos. Não utilize grupos de recursos, servidores ou conjuntos existentes. Não utilize esta aplicação, scripts ou quaisquer recursos implementados para produção. Elimine este grupo de recursos quando tiver terminado com a aplicação para parar a faturação relacionada.

    - **Grupo de recursos**: Selecione **criar novo**e forneça o nome exclusivo que escolheu anteriormente para o grupo de recursos.
    - **Localização**: Selecione uma localização da lista pendente.
    - **Utilizador**: Utilize o valor de nome de utilizador que selecionou anteriormente.

1. Implemente a aplicação.

    a. Selecione a concordar com os termos e condições.

    b. Selecione **Comprar**.

1. Para monitorizar o estado de implementação, selecione **notificações** (o ícone de campainha à direita da caixa de pesquisa). Implementar a aplicação Wingtip Tickets SaaS demora cerca de cinco minutos.

   ![Implementação efetuada com êxito](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Transferir e os scripts de gestão da Wingtip Tickets de desbloqueio

Enquanto a aplicação é implementada, transferir os scripts de gestão e código de origem.

> [!IMPORTANT]
> Conteúdo executável (scripts e DLLs) poderá ser bloqueado pelo Windows quando os arquivos. zip são transferidos a partir de uma origem externa e extraídos. Siga os passos para desbloquear o ficheiro. zip antes de extrair os scripts. Desbloqueio torna-se de que os scripts podem ser executados.

1. Navegue para o [repositório do GitHub WingtipTicketsSaaS DbPerTenant][github-wingtip-dpt].
1. Selecione **Clone or download** (Clonar ou transferir).
1. Selecione **transferir ZIP**e, em seguida, guarde o ficheiro.
1. Com o botão direito a **WingtipTicketsSaaS-DbPerTenant-Master** do ficheiro e, em seguida, selecione **propriedades**.
1. Sobre o **gerais** separador, selecione **desbloqueio** > **aplicar**.
1. Selecione **OK**e extraia os ficheiros

Scripts estão localizados na... \\WingtipTicketsSaaS-DbPerTenant-mestre\\pasta de módulos de aprendizagem.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Atualizar o ficheiro de configuração de utilizador para esta implementação

Antes de executar quaisquer scripts, atualize os valores de utilizador e grupo de recursos no arquivo de configuração de utilizador. Defina essas variáveis como os valores utilizados durante a implementação.

1. No ISE do PowerShell, abra... \\Módulos de aprendizagem\\**UserConfig.psm1**
1. Atualização **ResourceGroupName** e **nome** com os valores específicos para a sua implementação (em linhas 10 e 11 apenas).
1. Guarde as alterações.

Estes valores são referenciados em quase todos os scripts.

## <a name="run-the-application"></a>Executar a aplicação

A aplicação apresenta os locais que alojam os eventos. Tipos de local incluem espetáculos, clubes de jazz e clubes desportivos. No Wingtip Tickets, os locais são registrados como os inquilinos. Sendo um inquilino oferece um local uma forma simples para listar os eventos e vender bilhetes aos clientes. Cada local obtém um Web site personalizado para listar os seus eventos e vender bilhetes.

Internamente na aplicação, cada inquilino obtém uma base de dados do SQL implementada num conjunto elástico do SQL.

Uma central **Hub de eventos** página fornece uma lista de links para os inquilinos na sua implementação.

1. Utilize o URL para abrir o Hub de eventos no seu browser: http://events.wingtip-dpt.&lt; utilizador&gt;. trafficmanager.net. Substitute &lt;utilizador&gt; com valor de utilizador da sua implementação.

    ![Hub de eventos](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Selecione **Fabrikam Jazz Club** no Hub de eventos.

    ![Eventos](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Traffic Manager do Azure

Utiliza a aplicação Wingtip [*Gestor de tráfego do Azure* ](../traffic-manager/traffic-manager-overview.md) para controlar a distribuição de pedidos recebidos. O URL para aceder à página de eventos para um inquilino específico utiliza o seguinte formato:

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

    As partes do formato anterior são explicadas na tabela seguinte.

    | Parte do URL        | Descrição       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | As partes de eventos da aplicação Wingtip.<br /><br /> *-dpt* distingue as *da base de dados por inquilino* implementação da Wingtip Tickets de outras implementações. Os exemplos são o *único* aplicação por inquilino (*-sa*) ou *base de dados multi-inquilino* (*- mt*) implementações. |
    | .*&lt;user&gt;* | *af1* no exemplo. |
    | .trafficmanager.net/ | Gestor de tráfego, o URL de base. |
    | fabrikamjazzclub | Identifica o inquilino com o nome Fabrikam Jazz Club. |
    | &nbsp; | &nbsp; |

- O nome do inquilino é analisado a partir do URL pela aplicação de eventos.
- O nome do inquilino é utilizado para criar uma chave.
- A chave é utilizada para aceder ao catálogo para obter a localização da base de dados do inquilino.
  - O catálogo é implementado usando *gestão de mapas de partições horizontais*.
- O Hub de eventos utiliza metadados expandidos no catálogo para construir os URLs de página de lista de eventos para cada inquilino.

Ambiente de produção, normalmente vai criar um registo CNAME DNS [*apontar um domínio de internet da empresa*](../traffic-manager/traffic-manager-point-internet-domain.md) para o nome DNS do Gestor de tráfego.

> [!NOTE]
> Pode não ser imediatamente óbvio o que é a utilização do Gestor de tráfego de neste tutorial. O objetivo desta série de tutoriais é mostrar os padrões que podem lidar com a escala de um ambiente de produção complexas. Nesse caso, por exemplo, teria de várias aplicações web distribuídas por todo o mundo, localizado conjuntamente com bancos de dados e terá do Gestor de tráfego para encaminhar entre essas instâncias.
Outro conjunto de tutoriais que ilustra a utilização do Gestor de tráfego porém são os [georrestauro](saas-dbpertenant-dr-geo-restore.md) e o [georreplicação](saas-dbpertenant-dr-geo-replication.md) tutoriais. Nestes tutoriais, Gestor de tráfego é utilizado para ajudar a mudar para uma instância de recuperação da aplicação SaaS em caso de uma falha regional.

## <a name="start-generating-load-on-the-tenant-databases"></a>Começar a gerar carga nas bases de dados de inquilinos

Agora que a aplicação é implementada, vamos colocar em prática.

O *demonstração LoadGenerator* script do PowerShell é iniciado uma carga de trabalho que é executada relativamente a todas as bases de dados de inquilinos. A carga de mundo real de muitas aplicações SaaS é esporádica e imprevisível. Para simular esse tipo de carga, o gerador de produz uma carga com picos aleatório ou rajadas de atividade em cada inquilino. A expansão ocorre em intervalos aleatório. Demora alguns minutos para o padrão de carga a surgir. Permitir que o gerador de executar durante, pelo menos, três ou quatro minutos antes de monitorar a carga.

1. No ISE do PowerShell, abra o... \\Módulos de aprendizagem\\utilitários\\*demonstração LoadGenerator.ps1* script.
2. Pressione F5 para executar o script e iniciar o gerador de carga. Deixe a predefinição valores de parâmetro por agora.
3. Inicie sessão na sua conta do Azure e selecione a subscrição que pretende utilizar, se necessário.

O script de gerador de carga inicia uma tarefa de segundo plano para cada base de dados no catálogo e, em seguida, pára. Se voltar a executar o script de gerador de carga, ele deixa de todas as tarefas em segundo plano que estejam a executar antes de iniciar novos.

### <a name="monitor-the-background-jobs"></a>Monitorizar as tarefas em segundo plano

Se quiser controlar e monitorizar as tarefas em segundo plano, utilize os seguintes cmdlets:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Ações de demonstração LoadGenerator.ps1

*Demonstração LoadGenerator.ps1* simula uma carga de trabalho ativa de transações do cliente. Os passos seguintes descrevem a sequência de ações que *demonstração LoadGenerator.ps1* inicia:

1. *Demonstração LoadGenerator.ps1* começa *LoadGenerator.ps1* em primeiro plano.

    - Ambos os ficheiros. ps1 são armazenados em pastas de módulos de aprendizagem\\utilitários\\.

2. *LoadGenerator.ps1* um loop por meio de todas as bases de dados de inquilinos no catálogo.

3. *LoadGenerator.ps1* inicia uma tarefa do PowerShell em segundo plano para cada base de dados do inquilino:

    - Por predefinição, as tarefas em segundo plano é executado para 120 minutos.
    - Cada tarefa faz com que uma carga de CPU com base na base de dados de um inquilino, executando *sp_CpuLoadGenerator*. A intensidade e a duração da carga varia consoante `$DemoScenario`.
    - *sp_CpuLoadGenerator* loops em torno de uma instrução SQL SELECT que faz com que uma carga de CPU elevada. O intervalo de tempo entre problemas de SELECT varia de acordo com os valores de parâmetro para criar uma carga de CPU controlável. Níveis de carga e os intervalos são randomizados para simular cargas mais realistas.
    - Este ficheiro. SQL é armazenado abaixo *WingtipTenantDB\\dbo\\StoredProcedures\\*.

4. Se `$OneTime = $false`, o gerador de carga inicia as tarefas em segundo plano e, em seguida, continua a ser executado. Cada 10 segundos, que monitoriza para quaisquer novos inquilinos aprovisionados. Se definir `$OneTime = $true`, o LoadGenerator inicia as tarefas em segundo plano e, em seguida, pára em execução em primeiro plano. Para este tutorial, deixe `$OneTime = $false`.

   Utilize Ctrl-C ou Ctrl-Break de operação de parar, se pretender parar ou reiniciar o gerador de carga.

   Se deixar o gerador de carga em execução em primeiro plano, utilize outra instância do ISE do PowerShell para executar outros scripts do PowerShell.

&nbsp;

Antes de continuar com a próxima seção, deixe o gerador de carga em execução no estado de invocar a tarefa.

## <a name="provision-a-new-tenant"></a>Aprovisionar um inquilino novo

A implementação inicial cria três inquilinos de exemplo. Agora, criar outro inquilino para ver o impacto sobre a aplicação implementada. Na aplicação Wingtip, o fluxo de trabalho para aprovisionar novos inquilinos é explicado no [tutorial de aprovisionamento e catalogação](saas-dbpertenant-provision-and-catalog.md). Nesta fase, vai criar um novo inquilino, o que leva menos de um minuto.

1. Abra um novo ISE do PowerShell.
2. Abra... \\Módulos de aprendizagem\aprovisionamento e catálogo\\*Demo-ProvisionAndCatalog.ps1*.
3. Para executar o script, pressione F5. Deixe os valores predefinidos por agora.

   > [!NOTE]
   > Utilizam muitos scripts de Wingtip SaaS *$PSScriptRoot* procurar nas pastas para chamar funções em outros scripts. Esta variável é avaliada apenas quando o script completo é executado ao premir F5. Destacar e executar uma seleção com F8 podem resultar em erros. Para executar os scripts, pressione F5.

A nova base de dados do inquilino é:

- Criado num conjunto elástico de SQL.
- Inicializado.
- Registado no catálogo.

Após o aprovisionamento com êxito, o *eventos* local do novo inquilino é apresentado no seu browser.

![Novo inquilino](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Atualize o Hub de eventos para tornar o novo inquilino são apresentados na lista.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorar os servidores, os conjuntos e as bases de dados de inquilinos

Agora que começou a executar uma carga na coleção de inquilinos, vamos analisar alguns dos recursos que foram implementados.

1. Na [portal do Azure](https://portal.azure.com), navegue até à sua lista de servidores SQL. Em seguida, abra a **catálogo-dpt -&lt;USUÁRIO&gt;** server.
    - O servidor de catálogo contém duas bases de dados **tenantcatalog** e **basetenantdb** (um modelo base de dados que é copiado para criar novos inquilinos).

   ![Bases de Dados](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Volte à sua lista de servidores SQL.

3. Abra o **tenants1-dpt -&lt;USUÁRIO&gt;** servidor que contém as bases de dados do inquilino.

4. Consulte os seguintes itens:

    - Cada base de dados do inquilino é um **padrão elástico** base de dados num conjunto standard de 50 Edtus.
    - A base de dados Red Maple Racing é a base de dados do inquilino aprovisionado anteriormente.

   ![Servidor de bases de dados](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorizar o conjunto

Após *LoadGenerator.ps1* é executado durante vários minutos, dados suficientes devem estar disponíveis para começar a ver algumas capacidades de monitorização. Esses recursos baseiam-se nos conjuntos e bases de dados.

Navegue para o servidor **tenants1-dpt -&lt;usuário&gt;** e selecione **Pool1** para ver a utilização de recursos para o conjunto. Os gráficos seguintes, o gerador de carga executou durante uma hora.

   ![Monitorização do conjunto](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- O primeiro gráfico, rotulado **utilização de recursos**, mostra a utilização de eDTU do conjunto.
- O segundo gráfico mostra a utilização de eDTU das cinco bases de dados mais ativos no conjunto.

Dois gráficos ilustram a que os conjuntos elásticos e base de dados SQL são adequados para cargas de trabalho de aplicação do SaaS imprevisíveis. Os gráficos mostram que quatro bases de dados são cada segurança para o máximo de 40 eDTUs e ainda todas as bases de dados confortavelmente são suportados por um conjunto de 50 Edtus. O conjunto de eDTU de 50 pode suportar cargas de trabalho ainda mais pesadas. Se as bases de dados forem provisionados como bases de dados individuais, cada um deles tem de ser uma S2 (50 DTUS) para suportar a expansão. O custo de quatro S2 bases de dados individuais é quase três vezes o preço do conjunto. Em situações do mundo real, os clientes da base de dados SQL executam até 500 bases de dados em conjuntos de 200 Edtus. Para obter mais informações, consulte a [tutorial de monitorização do desempenho](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionais

- Para obter mais informações, consulte adicionais [tutoriais que têm por base o aplicativo de banco de dados por inquilino Wingtip Tickets SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Para saber mais sobre conjuntos elásticos, veja [o que é um conjunto elástico de SQL do Azure?](sql-database-elastic-pool.md).
- Para saber mais sobre as tarefas elásticas, consulte [gerir bases de dados de cloud aumentadas horizontalmente](sql-database-elastic-jobs-overview.md).
- Para saber mais sobre aplicações SaaS multi-inquilino, veja [padrões de Design para aplicações SaaS multi-inquilino](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> - Como implementar a aplicação Wingtip Tickets SaaS.
> - Sobre os servidores, conjuntos e bases de dados que constituem a aplicação.
> - Como os inquilinos são mapeados para os dados com o *catálogo*.
> - Como aprovisionar novos inquilinos.
> - Como ver a utilização do conjunto para monitorizar a atividade de inquilino.
> - Como eliminar recursos de exemplo para parar a faturação relacionada.

Em seguida, tente o [tutorial de aprovisionamento e catalogação](saas-dbpertenant-provision-and-catalog.md).

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
