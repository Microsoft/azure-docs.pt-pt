---
title: Tutorial SaaS de base de dados por inquilino
description: Implemente e explore a aplicação multiarrendatária Wingtip Tickets SaaS que demonstra o padrão de base de dados por inquilino e outros padrões SaaS utilizando a Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 3182daa4ebf3becc824b600d1e487e12b875b275
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80529661"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Implemente e explore uma aplicação SaaS multiarrendatária que utiliza o padrão de base de dados por inquilino com base de dados SQL

Neste tutorial, você implanta e explora a aplicação de bases de dados SaaS de bilhetes de base de dados SaaS (Wingtip). A aplicação utiliza um padrão de base de dados por inquilino para armazenar os dados de vários inquilinos. A aplicação foi concebida para apresentar funcionalidades da Base de Dados Azure SQL que simplificam a forma de ativar cenários SaaS.

Cinco minutos depois de selecionar **O Deploy para o Azure,** tem uma aplicação SaaS multiarrendatária. A aplicação inclui uma base de dados SQL que funciona na nuvem. A aplicação é implantada com três inquilinos de amostra, cada um com a sua própria base de dados. Todas as bases de dados estão implantadas numa piscina elástica SQL. A aplicação está implantada para a sua subscrição Azure. Tem acesso total para explorar e trabalhar com os componentes individuais da app. O código fonte c# da aplicação e os scripts de gestão estão disponíveis no [repo WingtipTicketsSaaS-DbPerTenant GitHub][github-wingtip-dpt].

Neste tutorial, ficará a saber:

> [!div class="checklist"]
> - Como implementar a aplicação Wingtip SaaS.
> - Onde obter o código fonte da aplicação e scripts de gestão.
> - Sobre os servidores, piscinas e bases de dados que compõem a aplicação.
> - Como os inquilinos são mapeados para os seus dados com o *catálogo.*
> - Como fornecer um novo inquilino.
> - Como monitorizar a atividade do inquilino na app.

Uma [série de tutoriais relacionados](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) oferece para explorar vários padrões de design e gestão saaS. Os tutoriais constroem para além desta implantação inicial. Quando utiliza os tutoriais, pode examinar os scripts fornecidos para ver como os diferentes padrões SaaS são implementados. Os scripts demonstram como as funcionalidades da Base de Dados SQL simplificam o desenvolvimento de aplicações SaaS.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, certifique-se de que o Azure PowerShell está instalado. Para obter mais informações, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Implementar a aplicação Wingtip Tickets SaaS

### <a name="plan-the-names"></a>Planeie os nomes

Nos passos desta secção, fornece um valor de utilizador que é usado para garantir que os nomes de recursos são globalmente únicos. Também fornece um nome para o grupo de recursos que contém todos os recursos criados por uma implementação da app. Para uma pessoa fictícia chamada Ann Finley, sugerimos:

- **Utilizador**: *af1* é composta por iniciais de Ann Finley mais um dígito. Se implementar a aplicação uma segunda vez, use um valor diferente. Um exemplo é aAF2.
- **Grupo**de recursos : *wingtip-dpt-af1* indica que esta é a aplicação base de dados por inquilino. Anexar o nome de utilizador af1 para correlacionar o nome do grupo de recursos com os nomes dos recursos que contém.

Escolha os seus nomes agora, e escreva-os.

### <a name="steps"></a>Passos

1. Para abrir o modelo de implantação de bases de dados SaaS de bilhetes de ala No portal Azure, selecione **Deploy para Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Introduza valores no modelo para os parâmetros necessários.

    > [!IMPORTANT]
    > Algumas firewalls de autenticação e servidor não estão intencionalmente seguras para fins de demonstração. Recomendamos que crie um novo grupo de recursos. Não utilize grupos de recursos, servidores ou piscinas existentes. Não utilize esta aplicação, scripts ou recursos implantados para produção. Elimine este grupo de recursos quando terminar a aplicação para parar a faturação relacionada.

    - **Grupo de recursos**: Selecione **Criar novo,** e forneça o nome único que escolheu anteriormente para o grupo de recursos.
    - **Localização**: Selecione uma localização da lista de lançamentos.
    - **Utilizador**: Utilize o valor do nome de utilizador que escolheu anteriormente.

1. Implemente a aplicação.

    a. Selecione para concordar com os termos e condições.

    b. Selecione **Comprar**.

1. Para monitorizar o estado de implementação, selecione **Notificações** (o ícone do sino à direita da caixa de pesquisa). A implementação da aplicação Wingtip Tickets SaaS demora aproximadamente cinco minutos.

   ![Implantação conseguiu](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Descarregue e desbloqueie os scripts de gestão de bilhetes wingtip

Enquanto a aplicação se implanta, descarregue o código fonte e os scripts de gestão.

> [!IMPORTANT]
> Os conteúdos executáveis (scripts e DLLs) podem ser bloqueados pelo Windows quando os ficheiros .zip são descarregados de uma fonte externa e extraídos. Siga os passos para desbloquear o ficheiro .zip antes de extrair os scripts. Desbloquear certifica-se de que os scripts são autorizados a ser executados.

1. Navegue pelo [repo WingtipTicketsSaaS-DbPerTenant GitHub][github-wingtip-dpt].
1. Selecione **Clone or download** (Clonar ou transferir).
1. Selecione **Baixar ZIP**e, em seguida, guardar o ficheiro.
1. Clique à direita no ficheiro **WingtipTicketsSaaS-DbPerTenant-master.zip** e, em seguida, selecione **Properties**.
1. No separador **Geral,** selecione **Unblock** > **Apply**.
1. Selecione **OK,** e extrai os ficheiros

Os scripts estão localizados no ... \\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules pasta.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Atualize o ficheiro de configuração do utilizador para esta implementação

Antes de executar quaisquer scripts, atualize o grupo de recursos e os valores do utilizador no ficheiro User Config. Detete estas variáveis aos valores utilizados durante a implantação.

1. No PowerShell ISE, abra... \\Módulos\\de Aprendizagem**UserConfig.psm1**
1. Atualizar **O Nome** e **Nome** do Grupo de Recursos com os valores específicos para a sua implementação (apenas nas linhas 10 e 11).
1. Guarde as alterações.

Estes valores são referenciados em quase todos os scripts.

## <a name="run-the-application"></a>Executar a aplicação

A aplicação apresenta locais que acolhem eventos. Os tipos de locais incluem salas de concertos, clubes de jazz e clubes desportivos. Nos Bilhetes Wingtip, os locais estão registados como inquilinos. Ser inquilino dá a um local uma maneira fácil de listar eventos e vender bilhetes aos seus clientes. Cada local recebe um site personalizado para listar os seus eventos e vender bilhetes.

Internamente na aplicação, cada inquilino obtém uma base de dados SQL implantada numa piscina elástica SQL.

Uma página central do **Events Hub** fornece uma lista de links para os inquilinos na sua implantação.

1. Utilize o URL para abrir o Site http://events.wingtip-dpt.&ltde&gt;Eventos no seu navegador web: .utilizador .trafficmanager.net. &lt;Substitua&gt; o utilizador pelo valor de utilizador da sua implementação.

    ![Centro de Eventos](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Selecione **Fabrikam Jazz Club** no Centro de Eventos.

    ![Eventos](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Traffic Manager do Azure

A aplicação Wingtip utiliza o [*Gestor de Tráfego Azure*](../traffic-manager/traffic-manager-overview.md) para controlar a distribuição de pedidos de entrada. O URL para aceder à página de eventos para um inquilino específico utiliza o seguinte formato:

- http://events.wingtip-dpt.&lt;utilizador&gt;.trafficmanager.net/fabrikamjazzclub

    As partes do formato anterior são explicadas na tabela seguinte.

    | Parte URL        | Descrição       |
    | :-------------- | :---------------- |
    | eventos.wingtip-dpt | As partes dos eventos da app Wingtip.<br /><br /> *-dpt* distingue a implementação de *bilhetes wingtip de* outras implementações. Exemplos são as implementações *de* aplicações individuais por inquilino *(-sa*) ou base de *dados multiarrendatária* *(-mt).* |
    | . * &lt;utilizador&gt;* | *af1* no exemplo. |
    | .trafficmanager.net/ | Gestor de Tráfego, URL base. |
    | fabrikamjazzclub | Identifica o inquilino chamado Fabrikam Jazz Club. |
    | &nbsp; | &nbsp; |

- O nome do inquilino é analisado a partir do URL pela app de eventos.
- O nome do inquilino é usado para criar uma chave.
- A chave é usada para aceder ao catálogo para obter a localização da base de dados do inquilino.
  - O catálogo é implementado utilizando a gestão de *mapas de fragmentos.*
- O Events Hub utiliza metadados estendidos no catálogo para construir a página de eventos URLs para cada inquilino.

Num ambiente de produção, normalmente cria-se um registo CNAME DNS para [*apontar um domínio*](../traffic-manager/traffic-manager-point-internet-domain.md) de internet da empresa ao nome DNS do Gestor de Tráfego.

> [!NOTE]
> Pode não ser imediatamente óbvio qual é a utilização do gestor de tráfego neste tutorial. O objetivo desta série de tutoriais é mostrar padrões que possam lidar com a escala de um ambiente de produção complexo. Neste caso, por exemplo, teria várias aplicações web distribuídas por todo o mundo, co-localizadas com bases de dados e precisaria de gestor de tráfego para fazer percursos entre estes casos.
Outro conjunto de tutoriais que ilustra o uso do gestor de tráfego, no entanto, são os tutoriais [de geo-restauro](saas-dbpertenant-dr-geo-restore.md) e [geo-replicação.](saas-dbpertenant-dr-geo-replication.md) Nestes tutoriais, o gestor de tráfego é usado para ajudar a mudar para uma instância de recuperação da app SaaS em caso de paragem regional.

## <a name="start-generating-load-on-the-tenant-databases"></a>Começar a gerar carga nas bases de dados de inquilinos

Agora que a aplicação está implantada, vamos pô-la a funcionar.

O script *Demo-LoadGenerator* PowerShell inicia uma carga de trabalho que vai contra todas as bases de dados dos inquilinos. A carga real em muitas aplicações SaaS é esporádica e imprevisível. Para simular este tipo de carga, o gerador produz uma carga com picos aleatórios ou explosões de atividade em cada inquilino. As explosões ocorrem em intervalos aleatórios. Leva vários minutos para o padrão de carga emergir. Deixe o gerador funcionar durante pelo menos três ou quatro minutos antes de monitorizar a carga.

1. No PowerShell ISE, abra o ... \\Módulos\\de\\Aprendizagem Utilities*Demo-LoadGenerator.ps1* script.
2. Pressione F5 para executar o script e ligue o gerador de carga. Deixe os valores do parâmetro padrão por enquanto.
3. Faça o inserição na sua conta Azure e selecione a subscrição que pretende utilizar, se necessário.

O script do gerador de carga inicia um trabalho de fundo para cada base de dados do catálogo e para. Se reproduzir o script do gerador de carga, para quaisquer trabalhos de fundo que estejam a funcionar antes de iniciar novos.

### <a name="monitor-the-background-jobs"></a>Monitorizar os trabalhos de fundo

Se pretender controlar e monitorizar os trabalhos de fundo, utilize os seguintes cmdlets:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Ações de Demo-LoadGenerator.ps1

*Demo-LoadGenerator.ps1* imita uma carga de trabalho ativa das transações de clientes. Os seguintes passos descrevem a sequência de ações que *a Demo-LoadGenerator.ps1* inicia:

1. *Demo-LoadGenerator.ps1* inicia *LoadGenerator.ps1* em primeiro plano.

    - Ambos os ficheiros .ps1 são armazenados\\sob\\as pastas Learning Modules Utilities .

2. *LoadGenerator.ps1* passa por todas as bases de dados dos inquilinos do catálogo.

3. *LoadGenerator.ps1* inicia um trabalho de fundo PowerShell para cada base de dados de inquilinos:

    - Por padrão, os trabalhos de fundo duram 120 minutos.
    - Cada trabalho causa uma carga baseada em CPU numa base de dados de inquilinos executando *sp_CpuLoadGenerator*. A intensidade e duração da carga `$DemoScenario`variam consoante .
    - *sp_CpuLoadGenerator* dá a volta a uma declaração SQL SELECT que causa uma alta carga de CPU. O intervalo de tempo entre as questões do SELECT varia de acordo com os valores dos parâmetros para criar uma carga CPU controlável. Os níveis de carga e intervalos são aleatórios para simular cargas mais realistas.
    - Este ficheiro .sql é armazenado em *WingtipTenantDB\\\\dbo StoredProcedures\\*.

4. Se, `$OneTime = $false`o gerador de carga iniciar os trabalhos de fundo e, em seguida, continuar a funcionar. A cada 10 segundos, monitoriza os novos inquilinos que são provisionados. Se definir, `$OneTime = $true`o LoadGenerator inicia os trabalhos de fundo e, em seguida, deixa de funcionar em primeiro plano. Para este tutorial, saia. `$OneTime = $false`

   Utilize CTRL-C ou Stop Operation CTRL-Break se pretender parar ou reiniciar o gerador de carga.

   Se deixar o gerador de carga em funcionamento em primeiro plano, utilize outra instância powerShell ISE para executar outros scripts PowerShell.

&nbsp;

Antes de continuar com a próxima secção, deixe o gerador de carga em funcionamento no estado de invocação do trabalho.

## <a name="provision-a-new-tenant"></a>Aprovisionar um inquilino novo

A implantação inicial cria três inquilinos de amostra. Agora cria outro inquilino para ver o impacto na aplicação implementada. Na app Wingtip, o fluxo de trabalho para a provisionamento de novos inquilinos é explicado no [tutorial de Provision e catálogo.](saas-dbpertenant-provision-and-catalog.md) Nesta fase, cria-se um novo inquilino, que demora menos de um minuto.

1. Abra um novo PowerShell ISE.
2. Aberto... \\Módulos de Aprendizagem\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*.
3. Para executar o guião, prima F5. Deixe os valores padrão por enquanto.

   > [!NOTE]
   > Muitos scripts Wingtip SaaS usam *$PSScriptRoot* para navegar em pastas para chamar funções em outros scripts. Esta variável só é avaliada quando o script completo é executado pressionando F5.Destacar e executar uma seleção com F8 pode resultar em erros. Para executar os scripts, prima F5.

A nova base de dados de inquilinos é:

- Criado numa piscina elástica SQL.
- Inicializado.
- Registado no catálogo.

Após o fornecimento bem sucedido, o site *eventos* do novo inquilino aparece no seu navegador.

![Novo inquilino](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Refresque o Centro de Eventos para fazer o novo inquilino aparecer na lista.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorar os servidores, os conjuntos e as bases de dados de inquilinos

Agora que começou a carregar contra a coleção de inquilinos, vamos ver alguns dos recursos que foram distribuídos.

1. No [portal Azure,](https://portal.azure.com)navegue para a sua lista de servidores SQL. Em seguida, abra o servidor **de utilizador&lt;&gt; do catálogo dpt..**
    - O servidor de catálogo contém duas bases de dados, **catálogo de inquilinos** e **basetenantdb** (uma base de dados de modelos que é copiada para criar novos inquilinos).

   ![Bases de Dados](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Volte para a sua lista de servidores SQL.

3. Abra o servidor de utilizador de **inquilinos&lt;1-dpt-que&gt; ** detém as bases de dados dos inquilinos.

4. Consulte os seguintes itens:

    - Cada base de dados de inquilinos é uma base de dados **Elástico Standard** numa piscina padrão de 50 eDTU.
    - A base de dados Red Maple Racing é a base de dados de inquilinos que você disponibilizou anteriormente.

   ![Servidor com bases de dados](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorizar o conjunto

Após o *loadGenerator.ps1* correr durante vários minutos, devem estar disponíveis dados suficientes para começar a analisar algumas capacidades de monitorização. Estas capacidades são incorporadas em piscinas e bases de dados.

Navegue pelos **inquilinos do&lt;servidor1-dpt-user&gt;** e selecione **Pool1** para visualizar a utilização de recursos para a piscina. Nos gráficos seguintes, o gerador de carga funcionou durante uma hora.

   ![Monitorizar piscina](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- O primeiro gráfico, com a marca de **utilização de recursos,** mostra a utilização do pool eDTU.
- O segundo gráfico mostra a utilização do eDTU das cinco bases de dados mais ativas da piscina.

Os dois gráficos ilustram que as piscinas elásticas e a Base de Dados SQL são adequadas para cargas de trabalho imprevisíveis de aplicação SaaS. Os gráficos mostram que quatro bases de dados estão a rebentar até 40 eDTUs, e no entanto todas as bases de dados são confortavelmente suportadas por um pool de 50 eDTU. A piscina de 50 eDTU pode suportar cargas de trabalho ainda mais pesadas. Se as bases de dados forem aprovisionadas como bases de dados únicas, cada uma delas tem de ser um S2 (50 DTU) para suportar as explosões. O custo de quatro bases de dados Únicas S2 é quase o triplo do preço da piscina. Em situações reais, os clientes da Base de Dados SQL gerem até 500 bases de dados em 200 piscinas eDTU. Para mais informações, consulte o tutorial de [monitorização](saas-dbpertenant-performance-monitoring.md)do Desempenho .

## <a name="additional-resources"></a>Recursos adicionais

- Para mais informações, consulte [tutoriais adicionais que se baseiem na aplicação de bases de dados SaaS de bilhetes de ala.](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- Para aprender sobre piscinas elásticas, veja [o que é uma piscina elástica Azure SQL?](sql-database-elastic-pool.md)
- Para aprender sobre trabalhos elásticos, consulte [Gerir bases de dados em nuvem em escala.](elastic-jobs-overview.md)
- Para conhecer aplicações multiarrendatárias saaS, consulte [padrões de design para aplicações SaaS multiarrendatárias.](saas-tenancy-app-design-patterns.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> - Como implementar a aplicação Wingtip Tickets SaaS.
> - Sobre os servidores, piscinas e bases de dados que compõem a aplicação.
> - Como os inquilinos são mapeados para os seus dados com o *catálogo.*
> - Como fornecer novos inquilinos.
> - Como visualizar a utilização da piscina para monitorizar a atividade do inquilino.
> - Como eliminar os recursos da amostra para parar a faturação relacionada.

Em seguida, experimente o tutorial de [Provision e catálogo.](saas-dbpertenant-provision-and-catalog.md)

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
