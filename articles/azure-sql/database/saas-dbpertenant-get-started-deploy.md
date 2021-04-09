---
title: Tutorial de SaaS de base de dados por inquilino
description: Implemente e explore a aplicação multitenant de ingressos saaS da Wingtip que demonstra o padrão de base de dados por inquilino e outros padrões SaaS utilizando a Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 497e714289c834e026c6b9b767ed2b7af5442783
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780840"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Implementar e explorar uma app SaaS multitenant que utiliza o padrão de base de dados por inquilino com Base de Dados Azure SQL

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você implementa e explora a aplicação de base de dados SaaS por inquilino (Wingtip). A aplicação usa um padrão de base de dados por inquilino para armazenar os dados de vários inquilinos. A aplicação foi concebida para mostrar funcionalidades da Base de Dados Azure SQL que simplificam a forma de ativar cenários saaS.

Cinco minutos depois de selecionar **Implementar para Azure,** tem uma aplicação SaaS multitenante. A aplicação inclui uma base de dados que funciona na Base de Dados Azure SQL. A aplicação é implementada com três inquilinos de amostra, cada um com a sua própria base de dados. Todas as bases de dados são implantadas numa piscina elástica SQL. A aplicação é implantada na sua subscrição Azure. Tem acesso total para explorar e trabalhar com os componentes individuais da app. O código fonte da aplicação C# e os scripts de gestão estão disponíveis no [WingtipTicketsSaAS-DbPerTenant GitHub repo][github-wingtip-dpt].

Neste tutorial, ficará a saber:

> [!div class="checklist"]
> - Como implementar a aplicação Wingtip SaaS.
> - Onde obter o código fonte de aplicação e scripts de gestão.
> - Sobre os servidores, piscinas e bases de dados que compõem a aplicação.
> - Como os inquilinos são mapeados para os seus dados com o *catálogo.*
> - Como providenciar um novo inquilino.
> - Como monitorizar a atividade do inquilino na app.

Uma [série de tutoriais relacionados](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) oferece-se para explorar vários padrões de design e gestão saaS. Os tutoriais constroem para além desta implantação inicial. Quando você usa os tutoriais, você pode examinar os scripts fornecidos para ver como os diferentes padrões SaaS são implementados. Os scripts demonstram como as funcionalidades da SQL Database simplificam o desenvolvimento de aplicações SaaS.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, certifique-se de que o Azure PowerShell está instalado. Para obter mais informações, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Implementar a aplicação Wingtip Tickets SaaS

### <a name="plan-the-names"></a>Planeie os nomes

Nos passos desta secção, fornece um valor de utilizador que é usado para garantir que os nomes de recursos são globalmente únicos. Também fornece um nome para o grupo de recursos que contém todos os recursos criados por uma implementação da app. Para uma pessoa fictícia chamada Ann Finley, sugerimos:

- **Utilizador:** *af1* é composto por iniciais de Ann Finley mais um dígito. Se implementar a aplicação uma segunda vez, use um valor diferente. Um exemplo é af2.
- **Grupo de recursos**: *wingtip-dpt-af1* indica que esta é a aplicação base de dados por inquilino. Anexar o nome de utilizador af1 para correlacionar o nome do grupo de recursos com os nomes dos recursos que contém.

Escolham os vossos nomes agora e escrevam-nos.

### <a name="steps"></a>Passos

1. Para abrir o modelo de implantação da base de dados saaS por inquilino no portal Azure, selecione **Implementar para Azure**.

   [![Imagem mostrando um botão marcado "Implementar para Azure".](https://azuredeploy.net/deploybutton.png)](https://aka.ms/deploywingtipdpt)

1. Introduza valores no modelo para os parâmetros necessários.

    > [!IMPORTANT]
    > Algumas firewalls de autenticação e servidores estão intencionalmente despromocuradas para fins de demonstração. Recomendamos que crie um novo grupo de recursos. Não utilize grupos de recursos, servidores ou piscinas existentes. Não utilize esta aplicação, scripts ou quaisquer recursos implantados para produção. Elimine este grupo de recursos quando terminar com a aplicação para parar a faturação relacionada.

    - **Grupo de recursos**: Selecione **Criar novo**, e forneça o nome único que escolheu anteriormente para o grupo de recursos.
    - **Localização**: Selecione uma localização da lista de drop-down.
    - **Utilizador:** Utilize o valor do nome de utilizador que escolheu anteriormente.

1. Desdobre a aplicação.

    a. Selecione para concordar com os termos e condições.

    b. Selecione **Comprar**.

1. Para monitorizar o estado de implantação, **selecione Notificações** (o ícone da campainha à direita da caixa de pesquisa). A implementação da aplicação Wingtip Tickets SaaS demora aproximadamente cinco minutos.

   ![Implementação conseguiu](./media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Descarregue e desbloqueie os scripts de gestão de bilhetes wingtip

Enquanto a aplicação é implementada, descarregue os scripts de código fonte e gestão.

> [!IMPORTANT]
> Os conteúdos executáveis (scripts e DLLs) podem ser bloqueados pelo Windows quando .zip ficheiros são descarregados a partir de uma fonte externa e extraídos. Siga os passos para desbloquear o ficheiro .zip antes de extrair os scripts. Desbloqueio garante que os scripts podem ser executados.

1. Navegue pelo [wingtipTicketsSaaS-DbPerTenant GitHub repo][github-wingtip-dpt].
1. Selecione **Clone or download** (Clonar ou transferir).
1. Selecione **Baixar ZIP** e, em seguida, guardar o ficheiro.
1. Clique com o botão direito no ficheiro **WingtipTicketsSaaS-DbPerTenant-master.zip** e, em seguida, selecione **Propriedades**.
1. No **separador Geral,** selecione **'Desbloquear'**  >  **Aplicar**.
1. Selecione **OK**, e extraia os ficheiros

Os scripts estão localizados no ... \\ Pasta de módulos de aprendizagem WingtipTicketsSaa-DbPerTenant-master. \\

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Atualizar o ficheiro de configuração do utilizador para esta implementação

Antes de executar quaisquer scripts, atualize o grupo de recursos e os valores do utilizador no ficheiro User Config. Desa esta variável para os valores utilizados durante a implantação.

1. No PowerShell ISE, aberto... \\ Módulos de Aprendizagem \\ **UserConfig.psm1**
1. Atualizar O Nome e **Nome** **do Grupo de Recursos** com os valores específicos para a sua implantação (apenas nas linhas 10 e 11).
1. Guarde as alterações.

Estes valores são referenciados em quase todos os scripts.

## <a name="run-the-application"></a>Executar a aplicação

A aplicação apresenta locais que acolhem eventos. Os locais incluem salas de concertos, clubes de jazz e clubes de esportes. Em Wingtip Tickets, os locais estão registados como inquilinos. Ser inquilino dá a um local uma maneira fácil de listar eventos e vender bilhetes para seus clientes. Cada local recebe um site personalizado para listar os seus eventos e vender bilhetes.

Internamente na aplicação, cada inquilino recebe uma base de dados implantada numa piscina elástica.

Uma página central do Centro de **Eventos** fornece uma lista de links para os inquilinos na sua implantação.

1. Utilize o URL para abrir o Centro de Eventos no seu navegador web: http://events.wingtip-dpt.&lt .user &gt; .trafficmanager.net. Substitua &lt; o utilizador pelo valor de utilizador da sua &gt; implementação.

    ![Centro de Eventos](./media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Selecione **Fabrikam Jazz Club** no Centro de Eventos.

    ![Eventos](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Gestor de Tráfego do Azure

A aplicação Wingtip utiliza [*o Azure Traffic Manager*](../../traffic-manager/traffic-manager-overview.md) para controlar a distribuição de pedidos de entrada. O URL para aceder à página de eventos para um inquilino específico utiliza o seguinte formato:

- http://events.wingtip-dpt.&lt;user &gt; .trafficmanager.net/fabrikamjazzclub

    As partes do formato anterior são explicadas na tabela seguinte.

    | Parte url        | Description       |
    | :-------------- | :---------------- |
    | eventos.wingtip-dpt | Os eventos partes da aplicação Wingtip.<br /><br /> *-dpt* distingue a *implementação de bilhetes* wingtip por base de dados de outros. Exemplos são as implementações *de* uma única aplicação por inquilino *(-sa)* ou *uma base de dados multitenante* *(-mt).* |
    | . *&lt; utilizador &gt;* | *af1* no exemplo. |
    | .trafficmanager.net/ | Gestor de tráfego, URL base. |
    | fabrikamjazzclub | Identifica o inquilino chamado Fabrikam Jazz Club. |
    | &nbsp; | &nbsp; |

- O nome do inquilino é analisado a partir do URL pela aplicação de eventos.
- O nome do inquilino é usado para criar uma chave.
- A chave é usada para aceder ao catálogo para obter a localização da base de dados do inquilino.
  - O catálogo é implementado utilizando *a gestão de mapas de fragmentos.*
- O Centro de Eventos utiliza metadados estendidos no catálogo para construir a lista de eventos urLs de página para cada inquilino.

Num ambiente de produção, normalmente cria-se um registo DE DNS CNAME para [*apontar um domínio de internet*](../../traffic-manager/traffic-manager-point-internet-domain.md) da empresa para o nome DNS do Gestor de Tráfego.

> [!NOTE]
> Pode não ser imediatamente óbvio qual é o uso do gestor de tráfego neste tutorial. O objetivo desta série de tutoriais é mostrar padrões que possam lidar com a escala de um ambiente de produção complexo. Neste caso, por exemplo, teria várias aplicações web distribuídas por todo o mundo, co-localizadas com bases de dados e precisaria de um gestor de tráfego para fazer a rota entre estes casos.
Outro conjunto de tutoriais que ilustra o uso do gestor de tráfego são os [tutoriais de geo-restauração](./saas-dbpertenant-dr-geo-restore.md) e [geo-replicação.](./saas-dbpertenant-dr-geo-replication.md) Nestes tutoriais, o gestor de tráfego é usado para ajudar a mudar para uma instância de recuperação da app SaaS em caso de paragem regional.

## <a name="start-generating-load-on-the-tenant-databases"></a>Começar a gerar carga nas bases de dados de inquilinos

Agora que a aplicação está implantada, vamos colocá-la a funcionar.

O script *Demo-LoadGenerator* PowerShell inicia uma carga de trabalho que vai contra todas as bases de dados dos inquilinos. A carga real em muitas aplicações SaaS é esporádica e imprevisível. Para simular este tipo de carga, o gerador produz uma carga com picos aleatórios ou explosões de atividade em cada inquilino. As explosões ocorrem em intervalos aleatórios. Leva vários minutos para o padrão de carga emergir. Deixe o gerador funcionar durante pelo menos três ou quatro minutos antes de monitorizar a carga.

1. No PowerShell ISE, abra o ... \\ Módulos de Aprendizagem \\ Utilitários \\ *Demo-LoadGenerator.ps1* script.
2. Prima F5 para executar o script e ligar o gerador de carga. Deixe os valores de parâmetro padrão por enquanto.
3. Faça o sômis na sua conta Azure e selecione a subscrição que pretende utilizar, se necessário.

O script do gerador de carga inicia um trabalho de fundo para cada base de dados do catálogo e depois para. Se voltar a executar o script do gerador de carga, para os trabalhos de fundo que estão a funcionar antes de começar novos.

### <a name="monitor-the-background-jobs"></a>Monitorize os trabalhos de fundo

Se quiser controlar e monitorizar os trabalhos de fundo, utilize os seguintes cmdlets:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>ações Demo-LoadGenerator.ps1

*Demo-LoadGenerator.ps1* imita uma carga de trabalho ativa das transações de clientes. Os seguintes passos descrevem a sequência de ações que *Demo-LoadGenerator.ps1* inicia:

1. *Demo-LoadGenerator.ps1* começa *LoadGenerator.ps1* em primeiro plano.

    - Ambos os ficheiros .ps1 são armazenados sob as pastas De Aprendizagem Módulos \\ Utilities \\ .

2. *LoadGenerator.ps1* através de todas as bases de dados de inquilinos do catálogo.

3. *LoadGenerator.ps1* inicia um trabalho de fundo da PowerShell para cada base de dados de inquilinos:

    - Por defeito, os trabalhos de fundo duram 120 minutos.
    - Cada trabalho causa uma carga baseada em CPU numa base de dados de inquilinos executando *sp_CpuLoadGenerator*. A intensidade e a duração da carga variam consoante `$DemoScenario` .
    - *sp_CpuLoadGenerator* loops em torno de uma declaração SQL SELECT que causa uma alta carga de CPU. O intervalo de tempo entre as questões do SELECT varia de acordo com os valores dos parâmetros para criar uma carga cpu controlável. Os níveis de carga e os intervalos são aleatórios para simular cargas mais realistas.
    - Este ficheiro .sql é armazenado sob *\\ as medidas \\ \\ de detenção de da WingtipTenantDB.*

4. Se, `$OneTime = $false` o gerador de carga iniciar os trabalhos de fundo e, em seguida, continuar a funcionar. A cada 10 segundos, monitoriza todos os novos inquilinos que são a provisionados. Se `$OneTime = $true` definir, o LoadGenerator inicia os trabalhos de fundo e, em seguida, deixa de funcionar em primeiro plano. Para este tutorial, `$OneTime = $false` saia.

   Utilize ctrl-C ou stop Operation Ctrl-Break se quiser parar ou reiniciar o gerador de carga.

   Se deixar o gerador de carga em primeiro plano, utilize outra instância do PowerShell ISE para executar outros scripts PowerShell.

&nbsp;

Antes de continuar com a próxima secção, deixe o gerador de carga em funcionamento no estado de invocação de trabalho.

## <a name="provision-a-new-tenant"></a>Aprovisionar um inquilino novo

A implantação inicial cria três inquilinos de amostra. Agora cria outro inquilino para ver o impacto na aplicação implementada. Na aplicação Wingtip, o fluxo de trabalho para a provisionação de novos inquilinos é explicado no [Tutorial de Provisão e catálogo.](saas-dbpertenant-provision-and-catalog.md) Nesta fase, cria-se um novo inquilino, que demora menos de um minuto.

1. Abra um novo PowerShell ISE.
2. Abrir... \\ Módulos de Aprendizagem\Provisão eDemo-ProvisionAndCatalog.ps1de Catálogo \\ **.
3. Para executar o guião, prima F5. Deixe os valores predefinidos por enquanto.

   > [!NOTE]
   > Muitos scripts Wingtip SaaS usam *$PSScriptRoot* para navegar em pastas para chamar funções em outros scripts. Esta variável só é avaliada quando o script completo é executado pressionando F5. Realçar e executar uma seleção com F8 pode resultar em erros. Para executar os scripts, prima F5.

A nova base de dados dos inquilinos é:

- Criado numa piscina elástica SQL.
- Inicializado.
- Registado no catálogo.

Após o fornecimento bem sucedido, o site *eventos* do novo inquilino aparece no seu navegador.

![Novo inquilino](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Refresque o Centro de Eventos para fazer o novo inquilino aparecer na lista.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorar os servidores, os conjuntos e as bases de dados de inquilinos

Agora que começou a trabalhar contra a coleção de inquilinos, vamos ver alguns dos recursos que foram mobilizados.

1. No [portal Azure,](https://portal.azure.com)consulte a sua lista de servidores SQL. Em seguida, abra o servidor **user-dpt-do-catálogo. &lt; &gt;**
    - O servidor de catálogo contém duas bases de dados, **tenantcatalog** e **basetenantdb** (uma base de dados de modelos copiada para criar novos inquilinos).

   ![A screenshot mostra uma página geral do servidor de catálogo com as duas bases de dados.](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Volte para a sua lista de servidores SQL.

3. Abra o servidor **user-1-dpt-user &lt; &gt;** que detém as bases de dados dos inquilinos.

4. Consulte os seguintes itens:

    - Cada base de dados de inquilinos é uma base de dados **Elastic Standard** num pool padrão de 50 eDTU.
    - A base de dados Red Maple Racing é a base de dados de inquilinos que adquirou anteriormente.

   ![Servidor com bases de dados](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorizar o conjunto

Após *LoadGenerator.ps1* de funcionamento por vários minutos, devem estar disponíveis dados suficientes para começar a analisar algumas capacidades de monitorização. Estas capacidades são incorporadas em piscinas e bases de dados.

Navegue pelo utilizador do servidor **&lt; &gt; 1-dpt-dpt-** e selecione **Pool1** para visualizar a utilização do recurso para a piscina. Nas tabelas seguintes, o gerador de carga funcionou durante uma hora.

   ![Piscina monitor](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- O primeiro gráfico, marcado como **utilização de recursos,** mostra a utilização do pool eDTU.
- O segundo gráfico mostra a utilização do eDTU das cinco bases de dados mais ativas da piscina.

Os dois gráficos ilustram que piscinas elásticas e base de dados SQL são adequadas para cargas de trabalho imprevisíveis da aplicação SaaS. Os gráficos mostram que quatro bases de dados estão a rebentar até 40 eDTUs, e no entanto todas as bases de dados são confortavelmente suportadas por um pool de 50 eDTU. A piscina de 50 eDTU pode suportar cargas de trabalho ainda mais pesadas. Se as bases de dados forem a provisionadas como bases de dados únicas, cada uma tem de ser um S2 (50 DTU) para suportar as explosões. O custo de quatro bases de dados S2 individuais é quase o triplo do preço da piscina. Em situações reais, os clientes da SQL Database executam até 500 bases de dados em 200 piscinas eDTU. Para obter mais informações, consulte o [tutorial de monitorização do desempenho.](saas-dbpertenant-performance-monitoring.md)

## <a name="additional-resources"></a>Recursos adicionais

- Para mais informações, consulte [tutoriais adicionais que constroem na aplicação Wingtip Tickets SaaS base de dados por inquilino.](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- Para aprender sobre piscinas elásticas, veja [o que é uma piscina elástica Azure SQL?](elastic-pool-overview.md)
- Para aprender sobre trabalhos elásticos, consulte [Gerir bases de dados de nuvem escalonadas.](./elastic-jobs-overview.md)
- Para saber mais sobre aplicações SaaS multitenantes, consulte [padrões de design para aplicações SaaS multitenantes.](saas-tenancy-app-design-patterns.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> - Como implementar a aplicação Wingtip Tickets SaaS.
> - Sobre os servidores, piscinas e bases de dados que compõem a aplicação.
> - Como os inquilinos são mapeados para os seus dados com o *catálogo.*
> - Como providenciar novos inquilinos.
> - Como visualizar a utilização da piscina para monitorizar a atividade do inquilino.
> - Como eliminar recursos de amostra para parar a faturação relacionada.

Em seguida, experimente o [Tutorial de Provisão e catálogo.](saas-dbpertenant-provision-and-catalog.md)

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant