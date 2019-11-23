---
title: Tutorial de SaaS de banco de dados por locatário
description: Implante e explore o aplicativo de multilocatário Wingtip tickets SaaS que demonstra o padrão de banco de dados por locatário e outros padrões de SaaS usando o banco de dados SQL do Azure.
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
ms.openlocfilehash: 31f712f80ee2492e4bbaec99bd093d46f9d04e6d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824002"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Implantar e explorar um aplicativo SaaS multilocatário que usa o padrão de banco de dados por locatário com o banco de dados SQL

Neste tutorial, você implanta e explora o aplicativo de banco de dados por locatário SaaS Wingtip tickets (Wingtip). O aplicativo usa um padrão de banco de dados por locatário para armazenar os dados de vários locatários. O aplicativo foi projetado para demonstrar recursos do banco de dados SQL do Azure que simplificam como habilitar cenários de SaaS.

Cinco minutos depois de selecionar **implantar no Azure**, você terá um aplicativo SaaS multilocatário. O aplicativo inclui um banco de dados SQL que é executado na nuvem. O aplicativo é implantado com três locatários de exemplo, cada um com seu próprio banco de dados. Todos os bancos de dados são implantados em um pool elástico do SQL. O aplicativo é implantado em sua assinatura do Azure. Você tem acesso completo para explorar e trabalhar com os componentes individuais do aplicativo. O código C# -fonte do aplicativo e os scripts de gerenciamento estão disponíveis no [repositório GitHub repositório wingtipticketssaas-DbPerTenant][github-wingtip-dpt].

Neste tutorial, ficará a saber:

> [!div class="checklist"]
> - Como implantar o aplicativo SaaS Wingtip.
> - Onde obter o código-fonte do aplicativo e os scripts de gerenciamento.
> - Sobre os servidores, pools e bancos de dados que compõem o aplicativo.
> - Como os locatários são mapeados para seus dados com o *Catálogo*.
> - Como provisionar um novo locatário.
> - Como monitorar a atividade de locatário no aplicativo.

Uma [série de tutoriais relacionados](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) oferece a exploração de vários padrões de design e gerenciamento de SaaS. Os tutoriais são criados além dessa implantação inicial. Ao usar os tutoriais, você pode examinar os scripts fornecidos para ver como os diferentes padrões de SaaS são implementados. Os scripts demonstram como os recursos do banco de dados SQL simplificam o desenvolvimento de aplicativos SaaS.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se Azure PowerShell está instalado. Para obter mais informações, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Implantar o aplicativo de SaaS Wingtip tickets

### <a name="plan-the-names"></a>Planejar os nomes

Nas etapas desta seção, você fornece um valor de usuário que é usado para garantir que os nomes de recursos sejam globalmente exclusivos. Você também fornece um nome para o grupo de recursos que contém todos os recursos criados por uma implantação do aplicativo. Para uma pessoa fictícia chamada Ana Alinen, sugerimos:

- **Usuário**: *AF1* é composto de iniciais de Ana alinen, além de um dígito. Se você implantar o aplicativo uma segunda vez, use um valor diferente. Um exemplo é AF2.
- **Grupo de recursos**: *Wingtip-DPT-AF1* indica que este é o aplicativo de banco de dados por locatário. Acrescente o nome de usuário AF1 para correlacionar o nome do grupo de recursos com os nomes dos recursos que ele contém.

Escolha seus nomes agora e anote-os.

### <a name="steps"></a>Passos

1. Para abrir o modelo de implantação de banco de dados por locatário do Wingtip tickets SaaS no portal do Azure, selecione **implantar no Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Insira valores no modelo para os parâmetros necessários.

    > [!IMPORTANT]
    > Alguns firewalls de autenticação e de servidor são intencionalmente desprotegidos para fins de demonstração. Recomendamos que você crie um novo grupo de recursos. Não use grupos de recursos, servidores ou pools existentes. Não use este aplicativo, scripts ou qualquer recurso implantado para produção. Exclua esse grupo de recursos quando tiver terminado com o aplicativo para interromper a cobrança relacionada.

    - **Grupo de recursos**: selecione **criar novo**e forneça o nome exclusivo que você escolheu anteriormente para o grupo de recursos.
    - **Local**: selecione um local na lista suspensa.
    - **Usuário**: Use o valor de nome de usuário que você escolheu anteriormente.

1. Implante o aplicativo.

    a. Selecione para concordar com os termos e condições.

    b. Selecione **Comprar**.

1. Para monitorar o status da implantação, selecione **notificações** (o ícone de sino à direita da caixa de pesquisa). A implantação do aplicativo Wingtip tickets SaaS leva aproximadamente cinco minutos.

   ![Implantação bem-sucedida](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Baixar e desbloquear os scripts de gerenciamento Wingtip tickets

Enquanto o aplicativo é implantado, baixe o código-fonte e os scripts de gerenciamento.

> [!IMPORTANT]
> O conteúdo do executável (scripts e DLLs) pode ser bloqueado pelo Windows quando arquivos. zip são baixados de uma fonte externa e extraídos. Siga as etapas para desbloquear o arquivo. zip antes de extrair os scripts. O desbloqueio garante que os scripts tenham permissão para serem executados.

1. Navegue até o [repositório GitHub repositório wingtipticketssaas-DbPerTenant][github-wingtip-dpt].
1. Selecione **Clone or download** (Clonar ou transferir).
1. Selecione **baixar zip**e salve o arquivo.
1. Clique com o botão direito do mouse no arquivo **WingtipTicketsSaaS-DbPerTenant-Master. zip** e selecione **Propriedades**.
1. Na guia **geral** , selecione **desbloquear** > **aplicar**.
1. Selecione **OK**e extraia os arquivos

Os scripts estão localizados na pasta...\\repositório wingtipticketssaas-DbPerTenant-Master\\Learning modules.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Atualizar o arquivo de configuração do usuário para esta implantação

Antes de executar qualquer script, atualize o grupo de recursos e os valores de usuário no arquivo de configuração do usuário. Defina essas variáveis para os valores que você usou durante a implantação.

1. No ISE do PowerShell, abra...\\módulos de aprendizado\\**userconfig. psm1**
1. Atualize **ResourceGroupName** e **nomeie** com os valores específicos para sua implantação (somente nas linhas 10 e 11).
1. Guarde as alterações.

Esses valores são referenciados em quase todos os scripts.

## <a name="run-the-application"></a>Executar a aplicação

O aplicativo apresenta locais que hospedam eventos. Os tipos de local incluem corredores de concerto, clubes de jazz e clubes esportivos. No Wingtip tickets, os locais são registrados como locatários. Ser um locatário dá a um local uma maneira fácil de listar eventos e de vender tíquetes para seus clientes. Cada local Obtém um site personalizado para listar seus eventos e para vender tíquetes.

Internamente no aplicativo, cada locatário Obtém um banco de dados SQL implantado em um pool elástico do SQL.

Uma página central de **eventos do Hub** fornece uma lista de links para os locatários em sua implantação.

1. Use a URL para abrir o Hub de eventos em seu navegador da Web: http://events.wingtip-dpt.&lt; usuário&gt;. trafficmanager.net. Substitua&gt; de usuário &lt;pelo valor de usuário da implantação.

    ![Hub de eventos](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Selecione **Fabrikam Jazz Club** no Hub de eventos.

    ![Eventos](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Traffic Manager do Azure

O aplicativo Wingtip usa o [*Gerenciador de tráfego do Azure*](../traffic-manager/traffic-manager-overview.md) para controlar a distribuição de solicitações de entrada. A URL para acessar a página de eventos para um locatário específico usa o seguinte formato:

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

    As partes do formato anterior são explicadas na tabela a seguir.

    | Parte da URL        | Descrição       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | As partes de eventos do aplicativo Wingtip.<br /><br /> *-DPT* distingue a implementação de *banco de dados por locatário* do Wingtip tickets de outras implementações. Os exemplos são as implementações *únicas* de aplicativo por locatário ( *-SA*) ou de *banco de dados multilocatário* ( *-MT*). |
    | . *&lt;&gt;de usuário* | *AF1* no exemplo. |
    | .trafficmanager.net/ | Gerenciador de tráfego, URL base. |
    | fabrikamjazzclub | Identifica o locatário chamado Fabrikam Jazz Club. |
    | &nbsp; | &nbsp; |

- O nome do locatário é analisado a partir da URL pelo aplicativo de eventos.
- O nome do locatário é usado para criar uma chave.
- A chave é usada para acessar o catálogo para obter o local do banco de dados do locatário.
  - O catálogo é implementado usando o *Gerenciamento de mapa de fragmentos*.
- O Hub de eventos usa metadados estendidos no catálogo para construir as URLs de página da lista de eventos para cada locatário.

Em um ambiente de produção, normalmente você cria um registro DNS CNAME para [*apontar um domínio de Internet da empresa*](../traffic-manager/traffic-manager-point-internet-domain.md) para o nome DNS do Gerenciador de tráfego.

> [!NOTE]
> Pode não ser imediatamente óbvio o que o uso do Gerenciador de tráfego está neste tutorial. O objetivo desta série de tutoriais é demonstrar padrões que podem lidar com a escala de um ambiente de produção complexo. Nesse caso, por exemplo, você teria vários aplicativos Web distribuídos em todo o mundo, colocalizado com bancos de dados e você precisaria do Gerenciador de tráfego para rotear entre essas instâncias.
Outro conjunto de tutoriais que ilustra o uso do Gerenciador de tráfego, embora sejam os tutoriais de [restauração geográfica](saas-dbpertenant-dr-geo-restore.md) e de [replicação geográfica](saas-dbpertenant-dr-geo-replication.md) . Nesses tutoriais, o Traffic Manager é usado para ajudar a alternar para uma instância de recuperação do aplicativo SaaS no caso de uma interrupção regional.

## <a name="start-generating-load-on-the-tenant-databases"></a>Começar a gerar carga nas bases de dados de inquilinos

Agora que o aplicativo foi implantado, vamos colocá-lo em funcionamento.

O script do PowerShell *demo-LoadGenerator* inicia uma carga de trabalho que é executada em todos os bancos de dados de locatário. A carga do mundo real em muitos aplicativos SaaS é esporádica e imprevisível. Para simular esse tipo de carga, o gerador produz uma carga com picos aleatórios ou intermitências de atividade em cada locatário. As intermitências ocorrem em intervalos aleatórios. Leva vários minutos para que o padrão de carga surja. Deixe o gerador ser executado por pelo menos três ou quatro minutos antes de monitorar a carga.

1. No ISE do PowerShell, abra o...\\módulos de aprendizado\\utilitários\\script *demo-LoadGenerator. ps1* .
2. Pressione F5 para executar o script e iniciar o gerador de carga. Deixe os valores de parâmetro padrão por enquanto.
3. Entre em sua conta do Azure e selecione a assinatura que você deseja usar, se necessário.

O script do gerador de carga inicia um trabalho em segundo plano para cada banco de dados no catálogo e, em seguida, para. Se você executar novamente o script do gerador de carga, ele interromperá todos os trabalhos em segundo plano em execução antes de iniciar novos.

### <a name="monitor-the-background-jobs"></a>Monitorar os trabalhos em segundo plano

Se você quiser controlar e monitorar os trabalhos em segundo plano, use os seguintes cmdlets:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Ações demo-LoadGenerator. ps1

*Demo-LoadGenerator. ps1* imita uma carga de trabalho ativa de transações do cliente. As etapas a seguir descrevem a sequência de ações que o *demo-LoadGenerator. ps1* inicia:

1. *Demo-LoadGenerator. ps1* inicia *LoadGenerator. ps1* em primeiro plano.

    - Ambos os arquivos. ps1 são armazenados nas pastas módulos de aprendizado\\utilitários\\.

2. O *LoadGenerator. ps1* executa loops em todos os bancos de dados de locatário no catálogo.

3. O *LoadGenerator. ps1* inicia um trabalho em segundo plano do PowerShell para cada banco de dados de locatário:

    - Por padrão, os trabalhos em segundo plano são executados por 120 minutos.
    - Cada trabalho causa uma carga baseada em CPU em um banco de dados de locatário executando *sp_CpuLoadGenerator*. A intensidade e a duração da carga variam de acordo com `$DemoScenario`.
    - *sp_CpuLoadGenerator* loops em uma instrução SQL SELECT que causa uma alta carga de CPU. O intervalo de tempo entre os problemas de SELECT varia de acordo com os valores de parâmetro para criar uma carga de CPU controlável. Os níveis de carga e os intervalos são aleatórios para simular cargas mais realistas.
    - Esse arquivo. SQL é armazenado em *WingtipTenantDB\\dbo\\storedprocedures\\* .

4. Se `$OneTime = $false`, o gerador de carga iniciará os trabalhos em segundo plano e continuará a ser executado. A cada 10 segundos, ele monitora os novos locatários que são provisionados. Se você definir `$OneTime = $true`, o LoadGenerator iniciará os trabalhos em segundo plano e, em seguida, interromperá a execução em primeiro plano. Para este tutorial, deixe `$OneTime = $false`.

   Use CTRL-C ou Stop Operation Ctrl-Break se você quiser parar ou reiniciar o gerador de carga.

   Se você deixar o gerador de carga em execução em primeiro plano, use outra instância do ISE do PowerShell para executar outros scripts do PowerShell.

&nbsp;

Antes de continuar com a próxima seção, deixe o gerador de carga em execução no estado de invocação de trabalho.

## <a name="provision-a-new-tenant"></a>Aprovisionar um inquilino novo

A implantação inicial cria três locatários de exemplo. Agora você cria outro locatário para ver o impacto no aplicativo implantado. No aplicativo Wingtip, o fluxo de trabalho para provisionar novos locatários é explicado no [tutorial provisionar e catalogar](saas-dbpertenant-provision-and-catalog.md). Nesta fase, você cria um novo locatário, que leva menos de um minuto.

1. Abra um novo ISE do PowerShell.
2. Abra...\\Learning módulos and Catalog\\*demo-provisionandcatalog. ps1*.
3. Para executar o script, pressione F5. Deixe os valores padrão por enquanto.

   > [!NOTE]
   > Muitos scripts de SaaS do Wingtip usam *$PSScriptRoot* para procurar as pastas para chamar funções em outros scripts. Essa variável é avaliada somente quando o script completo é executado pressionando F5. Realçar e executar uma seleção com F8 pode resultar em erros. Para executar os scripts, pressione F5.

O novo banco de dados de locatário é:

- Criado em um pool elástico do SQL.
- Inicializado.
- Registrado no catálogo.

Após o provisionamento bem-sucedido, o site de *eventos* do novo locatário aparece no navegador.

![Novo inquilino](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Atualize o Hub de eventos para fazer com que o novo locatário apareça na lista.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorar os servidores, os conjuntos e as bases de dados de inquilinos

Agora que você começou a executar uma carga em relação à coleção de locatários, vamos dar uma olhada em alguns dos recursos que foram implantados.

1. Na [portal do Azure](https://portal.azure.com), navegue até sua lista de servidores SQL. Em seguida, abra o **usuário do Catalog-DPT-&lt;&gt;** servidor.
    - O servidor de catálogo contém dois bancos de dados, **tenantcatalog** e **basetenantdb** (um modelo de banco que é copiado para criar novos locatários).

   ![Bases de Dados](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Volte para a lista de servidores SQL.

3. Abra o servidor de **&gt;de usuário tenants1-DPT-&lt;** que contém os bancos de dados de locatário.

4. Consulte os seguintes itens:

    - Cada banco de dados de locatário é um banco de dados **elástico padrão** em um pool padrão de 50-eDTU.
    - O banco de dados de corrida de bordo vermelho é o banco de dados de locatário que você provisionou anteriormente.

   ![Servidor com bancos de dados](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorizar o conjunto

Após o *LoadGenerator. ps1* ser executado por vários minutos, os dados suficientes devem estar disponíveis para começar a examinar alguns recursos de monitoramento. Esses recursos são criados em pools e bancos de dados.

Navegue até o servidor **tenants1-DPT-&lt;usuário&gt;** e selecione **Pool1** para exibir a utilização de recursos para o pool. Nos gráficos a seguir, o gerador de carga foi executado por uma hora.

   ![Monitorar pool](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- O primeiro gráfico, rotulado **utilização de recursos**, mostra a utilização de eDTU do pool.
- O segundo gráfico mostra a utilização de eDTU dos cinco bancos de dados mais ativos no pool.

Os dois gráficos ilustram que os pools elásticos e o banco de dados SQL são adequados para cargas de trabalho de aplicativos SaaS imprevisíveis. Os gráficos mostram que quatro bancos de dados são cada um com pico de 40 eDTUs e, ainda, todos os bancos de dados são confortavelmente suportados por um pool de 50-eDTU. O pool de 50-eDTU pode dar suporte a cargas de trabalho ainda mais pesadas. Se os bancos de dados forem provisionados como bancos de dados individuais, cada um precisará ser um S2 (50 DTU) para dar suporte aos picos. O custo de quatro bancos de dados S2 únicos é quase três vezes o preço do pool. Em situações do mundo real, os clientes do banco de dados SQL executam até 500 bancos de dados em pools de eDTU de 200. Para obter mais informações, consulte o [tutorial de monitoramento de desempenho](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionais

- Para obter mais informações, consulte [tutoriais adicionais que se baseiam no aplicativo de banco de dados por locatário SaaS Wingtip tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Para saber mais sobre pools elásticos, confira [o que é um pool elástico do SQL do Azure?](sql-database-elastic-pool.md).
- Para saber mais sobre os trabalhos elásticos, consulte [gerenciar bancos de dados de nuvem escalados](elastic-jobs-overview.md)horizontalmente.
- Para saber mais sobre aplicativos SaaS multilocatários, consulte [padrões de design para aplicativos SaaS multilocatários](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> - Como implantar o aplicativo SaaS Wingtip tickets.
> - Sobre os servidores, pools e bancos de dados que compõem o aplicativo.
> - Como os locatários são mapeados para seus dados com o *Catálogo*.
> - Como provisionar novos locatários.
> - Como exibir a utilização do pool para monitorar a atividade do locatário.
> - Como excluir recursos de exemplo para interromper a cobrança relacionada.

Em seguida, experimente o [tutorial provisionar e catalogar](saas-dbpertenant-provision-and-catalog.md).

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
