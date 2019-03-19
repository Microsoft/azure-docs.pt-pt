---
title: Implementar uma aplicação de SaaS de base de dados em partição horizontal multi-inquilino que utiliza a base de dados SQL do Azure | Documentos da Microsoft
description: Implementar e explorar a aplicação de base de dados do multi-inquilino de Wingtip Tickets SaaS em partição horizontal, o que demonstra os padrões SaaS ao utilizar a base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, stein
manager: craigg
ms.date: 10/16/2018
ms.openlocfilehash: 8f19303dba7fcf2a9edb325464fdd3748220903e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57994911"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Implementar e explorar uma aplicação em partição horizontal do multi-inquilino

Neste tutorial, implementar e explorar uma aplicação SaaS multi-inquilino de exemplo com o nome Wingtip Tickets. A aplicação Wingtip Tickets foi concebida para demonstrar as funcionalidades da base de dados do Azure SQL que simplificam a implementação de cenários SaaS.

Esta implementação da aplicação Wingtip Tickets utiliza um padrão de base de dados de multi-inquilino em partição horizontal. A fragmentação é pelo identificador de inquilino. Dados de inquilino são distribuídos para um determinado banco de dados, de acordo com os valores de identificador do inquilino. 

Este padrão de base de dados permite-lhe armazenar um ou mais inquilinos em cada partição horizontal ou a base de dados. Pode otimizar o custo mais baixo fazendo com que cada base de dados a ser partilhado por vários inquilinos. Ou pode otimizar o isolamento fazendo com que cada base de dados armazenar apenas a um inquilino. À sua escolha de otimização pode ser feita de forma independente para cada inquilino específico. Pode ser feita à sua escolha quando o inquilino é armazenado em primeiro lugar, ou pode mudar de ideias mais tarde. O aplicativo foi projetado para funcionar bem de qualquer forma.

## <a name="app-deploys-quickly"></a>Aplicação implementa rapidamente

A aplicação é executada na cloud do Azure e utiliza a base de dados do Azure SQL. A secção de implementação que se segue fornece a azul **implementar no Azure** botão. Quando é premido o botão, a aplicação é totalmente implementada a sua subscrição do Azure dentro de cinco minutos. Tem acesso total ao trabalhar com os componentes de aplicativos individuais.

A aplicação é implementada com os dados para três inquilinos de exemplo. Os inquilinos são armazenados num banco de dados do multi-inquilino.

Qualquer pessoa pode transferir o código-fonte c# e o PowerShell para a Wingtip Tickets de [seu repositório do GitHub][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Saiba mais neste tutorial

> [!div class="checklist"]
> - Como implementar a aplicação Wingtip Tickets SaaS.
> - Onde obter o código de origem do aplicativo e os scripts de gestão.
> - Sobre os servidores e bases de dados que constituem a aplicação.
> - Como os inquilinos são mapeados para os dados com o *catálogo*.
> - Como aprovisionar um novo inquilino.
> - Como monitorizar a atividade de inquilino na aplicação.

Uma série de tutoriais relacionados está disponível que são criados após esta implementação inicial. Os tutoriais explore padrões de conceção e gestão de intervalo de SaaS. Quando completar os tutoriais, são incentivadas a analise os scripts fornecidos para ver como os diferentes padrões SaaS são implementados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

- O Azure PowerShell mais recente está instalado. Para obter detalhes, consulte [introdução ao Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Implemente a Wingtip Tickets de aplicação

### <a name="plan-the-names"></a>Planear os nomes

Os passos desta secção, fornecer uma *usuário* valor que é utilizado para garantir que os nomes de recursos são globalmente exclusivos e um nome para o *grupo de recursos* que contém todos os recursos criados por uma implementação da aplicação. Para uma pessoa com o nome *Ann Finley*, sugerimos:
- *Utilizador:* **af1**  *(seus iniciais, além de um dígito. Utilize um valor diferente (por exemplo, af2) se implementar a aplicação uma segunda vez.)*
- *Grupo de recursos:* **wingtip-mt-af1** *(wingtip mt indica esta é a aplicação multi-inquilino em partição horizontal. Acrescentar o af1 de nome de utilizador relacionada com o nome do grupo de recursos com os nomes de recursos que contém.)*

Escolha os nomes de agora e anotá-las. 

### <a name="steps"></a>Passos

1. Clique a seguinte azul **implementar no Azure** botão.
   - Ele é aberto o portal do Azure com o modelo de implementação de Wingtip Tickets SaaS.

     [![Botão para implementar no Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Introduza os valores de parâmetro necessário para a implementação.

    > [!IMPORTANT]
    > Para esta demonstração, não utilize quaisquer grupos de recursos, servidores ou conjuntos já existente. Em vez disso, escolha **criar um novo grupo de recursos**. Elimine este grupo de recursos quando tiver terminado com a aplicação para parar a faturação relacionada.
    > Não utilize esta aplicação nem quaisquer recursos por que ela criados para produção. Alguns aspetos de autenticação e as definições de firewall do servidor são intencionalmente inseguros na aplicação para facilitar a demonstração.

    - Para **grupo de recursos** - selecione **criar nova**e, em seguida, forneça um **nome** para o grupo de recursos (sensível a maiúsculas e minúsculas).
        - Selecione um **localização** na lista pendente.
    - Para **usuário** -que recomendamos que escolha um curto período **utilizador** valor.

1. **Implemente a aplicação**.

    - Clique para aceitar os termos e condições.
    - Clique em **Comprar**.

1. Monitorizar o estado da implementação ao clicar em **notificações**, que é o ícone de campainha à direita da caixa de pesquisa. Implementar a aplicação Wingtip demora cerca de cinco minutos.

   ![implementação concluída com êxito](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Transferir e os scripts de gestão de desbloqueio

Enquanto o aplicativo é implementado, transferir os scripts de gestão e de código de origem do aplicativo.

> [!NOTE]
> Conteúdo executável (scripts, DLLs) pode estar bloqueado por Windows quando arquivos zip são transferidos a partir de uma origem externa e extraídos. Quando extrair os scripts de um arquivo zip, utilize os seguintes passos para desbloquear o ficheiro. zip antes de extrair. Ao desbloquear o ficheiro. zip, garante que os scripts podem ser executados.

1. Navegue até [o repositório do GitHub WingtipTicketsSaaS MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Clique em **clonar ou transferir**.
3. Clique em **transferir ZIP** e guarde o ficheiro.
4. Com o botão direito a **WingtipTicketsSaaS-MultiTenantDb-Master** do ficheiro e selecione **propriedades**.
5. Sobre o **gerais** separador, selecione **desbloqueio**e clique em **aplicar**.
6. Clique em **OK**.
7. Extraia os ficheiros.

Os scripts estão localizados no *.... \\WingtipTicketsSaaS-MultiTenantDb-mestre\\módulos de aprendizagem\\*  pasta.

## <a name="update-the-configuration-file-for-this-deployment"></a>Atualizar o ficheiro de configuração para esta implementação

Antes de executar quaisquer scripts, defina o *grupo de recursos* e *utilizador* valores na **UserConfig.psm1**. Defina estas variáveis com os mesmos valores que definir durante a implementação.

1. Abra... \\Módulos de aprendizagem\\*UserConfig.psm1* no *ISE do PowerShell*.
2. Atualização *ResourceGroupName* e *nome* com os valores específicos para a sua implementação (em linhas 10 e 11 apenas).
3. Guarde as alterações.

Os valores definidos nesse arquivo são utilizados por todos os scripts, pelo que é importante estão corretas. Se voltar a implementar a aplicação, tem de escolher valores diferentes para o utilizador e grupo de recursos. Em seguida, atualize o ficheiro de UserConfig.psm1 novamente com os novos valores.

## <a name="run-the-application"></a>Executar a aplicação

Na aplicação Wingtip, os inquilinos são locais. Quando um local pode ser hall do conjunto, um clubes desportivos ou qualquer outro local que aloja a eventos. Os locais, registrar no Wingtip, como os clientes, e um identificador de inquilino é gerado para cada local. Cada local lista seus eventos futuros no Wingtip, para que os pedidos de suporte para os eventos pode comprar o público.

Cada local obtém uma aplicação web personalizada para listar os seus eventos e vender bilhetes. Cada aplicação web é independente e isolada de outros inquilinos. Internamente no Azure SQL Database, cada os dados para cada inquilino são armazenados num banco de dados em partição horizontal do multi-inquilino, por predefinição. Todos os dados é marcado com o identificador do inquilino.

Uma central **Hub de eventos** página da Web fornece uma lista de links para os inquilinos na sua implementação específica. Utilize os seguintes passos para experimentar os **Hub de eventos** página da Web e uma aplicação individual web:

1. Abra o **Hub de eventos** no seu browser:
   - http://events.wingtip-mt.&lt; usuário&gt;. trafficmanager.net &nbsp; *(substitua &lt;utilizador&gt; com valor de utilizador da sua implementação.)*

     ![hub de eventos](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Clique em **Fabrikam Jazz Club** no **Hub de Eventos**.

   ![Eventos](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Traffic Manager do Azure

Para controlar a distribuição de pedidos recebidos, utiliza a aplicação Wingtip [Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md). A página de eventos para cada inquilino inclui o nome do inquilino no URL correspondente. Cada URL também inclui o valor de utilizador específico. Cada URL obedeça o formato mostrado ao utilizar os seguintes passos:

- http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net/*fabrikamjazzclub*

1. A aplicação de eventos analisa o nome do inquilino da URL. É o nome do inquilino *fabrikamjazzclub* no URL do exemplo anterior.
2. A aplicação hashes, em seguida, o nome do inquilino para criar uma chave para aceder um catálogo com [gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md).
3. A aplicação localiza a chave no catálogo e obtém a localização correspondente da base de dados do inquilino.
4. A aplicação utiliza as informações de localização para localizar e aceder a uma base de dados que contém todos os dados para o inquilino.

### <a name="events-hub"></a>Hub de eventos

1. O **Hub de eventos** apresenta uma lista de todos os inquilinos que estão registados no catálogo e seus locais.
2. O **Hub de eventos** utiliza metadados expandidos no catálogo para obter o nome do inquilino associado a cada mapeamento para construir os URLs.

Num ambiente de produção, normalmente, criar um registo CNAME DNS para [apontar um domínio de internet da empresa](../traffic-manager/traffic-manager-point-internet-domain.md) para o perfil do Gestor de tráfego.

## <a name="start-generating-load-on-the-tenant-databases"></a>Começar a gerar carga nas bases de dados de inquilinos

Agora que a aplicação é implementada, vamos colocar em prática! O *demonstração LoadGenerator* script do PowerShell é iniciado uma carga de trabalho em execução para cada inquilino. A carga de mundo real de muitas aplicações SaaS é, normalmente, esporádica e imprevisível. Para simular esse tipo de carga, o gerador de produz uma carga distribuída em todos os inquilinos. A carga inclui picos aleatório em cada inquilino que ocorrem em intervalos aleatório. Demora alguns minutos para o padrão de carga a surgir, então é melhor permitir que o gerador de executar durante, pelo menos, três ou quatro minutos antes da carga de monitorização.

1. Na *ISE do PowerShell*, abra o... \\Módulos de aprendizagem\\utilitários\\*demonstração LoadGenerator.ps1* script.
2. Prima **F5** para executar o script e iniciar o gerador de carga (deixe os valores predefinidos do parâmetro por agora).

O *demonstração LoadGenerator.ps1* script abre outra sessão do PowerShell em que o gerador de carga é executada. O gerador de carga é executado nessa sessão como uma tarefa de primeiro plano que invoca tarefas de geração de carga em segundo plano, um para cada inquilino.

Depois de inicia a tarefa de primeiro plano, permanece num Estado de invocar a tarefa. A tarefa inicia tarefas em segundo plano adicionais para quaisquer novos inquilinos aprovisionados subsequentemente.

Fechar a sessão do PowerShell deixa de todas as tarefas.

Pode querer reiniciar a sessão de gerador de carga para utilizar valores de parâmetros diferentes. Nesse caso, fechar o PowerShell a sessão de geração e, em seguida, volte a executar o *demonstração LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Aprovisionar um novo inquilino na base de dados em partição horizontal

A implementação inicial inclui três inquilinos de exemplo no *Tenants1* base de dados. Vamos criar outro inquilino e observe seus efeitos sobre a aplicação implementada. Neste passo, pressiona uma tecla para criar um novo inquilino:

1. Abra... \\Módulos de aprendizagem\\aprovisionar e catalogar\\*demonstração ProvisionTenants.ps1* no *ISE do PowerShell*.
2. Prima **F5** (não **F8**) para executar o script (deixe os valores predefinidos por agora).

   > [!NOTE]
   > Tem de executar os scripts do PowerShell apenas ao premir o **F5** chave, não ao premir **F8** para executar uma parte selecionada do script. O problema com **F8** é que o *$PSScriptRoot* variável não é avaliada. Esta variável é necessária ao muitos scripts para navegar de pastas, invocar outros scripts ou importar módulos.

O novo inquilino Red Maple Racing é adicionado para o *Tenants1* de base de dados e registadas no catálogo. O novo inquilino do pedido de suporte de vendas **eventos** site abre-se no seu browser:

![Novo inquilino](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Atualizar o **Hub de eventos**, e o novo inquilino é apresentado na lista.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Aprovisionar um inquilino novo na sua própria base de dados

O modelo de multi-inquilino em partição horizontal permite-lhe escolher se pretende aprovisionar um inquilino novo numa base de dados que contém outros inquilinos ou numa base de dados por conta própria. Um inquilino na sua própria base de dados em ambiente isolado usufrui dos benefícios seguintes:

- O desempenho da base de dados do inquilino pode ser gerido sem a necessidade de se comprometer com as necessidades de outros inquilinos.
- Se necessário, a base de dados pode ser restaurada para um ponto anterior no tempo, porque não existem outros inquilinos seriam afetados.

Pode optar por colocar os clientes de avaliação gratuita ou clientes de economia, em bases de dados do multi-inquilinos. Poderia colocar cada inquilino premium na sua própria base de dados dedicado. Se criar muitas bases de dados que contêm apenas a um inquilino, pode geri-los todos coletivamente num conjunto elástico para otimizar os custos de recursos.

Em seguida, vamos provisionar outro inquilino, desta vez na sua própria base de dados:

1. Em... \\Módulos de aprendizagem\\aprovisionar e catalogar\\*demonstração ProvisionTenants.ps1*, modificar *$TenantName* para **Salix Salsa**, *$VenueType* para **dança** e *$Scenario* para **2**.

2. Prima **F5** para executar o script novamente.
    - Isso **F5** press Aprovisiona o novo inquilino numa base de dados separado. A base de dados e de inquilino são registadas no catálogo. Em seguida, o navegador é aberto para a página de eventos do inquilino.

   ![Página de eventos de Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Desloque-se para a parte inferior da página. Lá na faixa verá o nome de base de dados em que os dados de inquilino são armazenados.

3. Atualizar o **Hub de eventos** e os dois inquilinos novo é apresentado na lista.

## <a name="explore-the-servers-and-tenant-databases"></a>Explorar os servidores e bases de dados de inquilinos

Agora vamos examinar alguns dos recursos que foram implementados:

1. Na [portal do Azure](https://portal.azure.com), navegue para a lista de grupos de recursos. Abra o grupo de recursos que criou quando implementou a aplicação.

   ![grupo de recursos](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Clique em **mt catálogo&lt;usuário&gt;**  server. O servidor de catálogo contém duas bases de dados com o nome *tenantcatalog* e *basetenantdb*. O *basetenantdb* base de dados é uma base de dados do modelo vazio. É copiado para criar uma nova base de dados de inquilino, se utilizados para muitos inquilinos ou apenas um inquilino.

   ![servidor de catálogo](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Volte para o grupo de recursos e selecione o *tenants1-mt* servidor que contém as bases de dados do inquilino.
    - A base de dados tenants1 é uma base de dados do multi-inquilino no qual os três inquilinos de originais, além de inquilino primeiro adicionadas, é armazenado. Ele é configurado como uma base de dados DTUS padrão 50.
    - O **salixsalsa** base de dados contém o local de dança Salix Salsa como seu único inquilino. Ele é configurado como uma base de dados Standard edition com 50 DTUs por predefinição.

   ![servidor de inquilinos](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Monitorizar o desempenho da base de dados

Se o gerador de carga tem de estar em execução há vários minutos, telemetria suficiente está disponível para examinar a base de dados incorporadas no portal do Azure de capacidades de monitorização.

1. Navegue para o **tenants1-mt&lt;usuário&gt;**  servidor e clique em **tenants1** para ver a utilização de recursos para a base de dados com quatro inquilinos na mesma. Cada inquilino está sujeito a uma carga pesada esporádica do gerador de carga:

   ![monitorizar tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   O gráfico de utilização de DTU ilustra bem como uma base de dados do multi-inquilino pode suportar uma carga de trabalho imprevisível em vários inquilinos. Neste caso, o gerador de carga está a ser aplicada uma carga esporádica de aproximadamente 30 DTUs a cada inquilino. Esta carga equivale a 60% de utilização de uma base de dados DTUS 50. Os picos que excedem os 60% são o resultado de carga que está a ser aplicada a mais do que um inquilino ao mesmo tempo.

2. Navegue para o **tenants1-mt&lt;usuário&gt;**  servidor e clique no **salixsalsa** base de dados. Pode ver a utilização de recursos nesta base de dados que contém apenas a um inquilino.

   ![salixsalsa base de dados](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

O gerador de carga está a aplicar a cada inquilino, independentemente de qual banco de dados, cada inquilino está numa carga semelhante. Com apenas um inquilino no **salixsalsa** base de dados, pode ver que a base de dados poderia sustentar um muito maior de carga da base de dados com vários inquilinos. 

### <a name="resource-allocations-vary-by-workload"></a>Alocações de recursos variam consoante a carga de trabalho

Às vezes, uma base de dados do multi-inquilino requer mais recursos para o bom desempenho do que uma base de dados de inquilino único, mas nem sempre. A alocação ideal de recursos depende as características da carga de trabalho específica para os inquilinos no seu sistema.

As cargas de trabalho geradas pelo script de gerador de carga são apenas para fins ilustrativos.

## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre aplicações SaaS multi-inquilino, veja [padrões de Design para aplicações SaaS multi-inquilino](saas-tenancy-app-design-patterns.md).

- Para saber mais sobre conjuntos elásticos, veja:

  - [O ajudam a gerir e dimensionar várias bases de dados SQL do Azure de conjuntos elásticos](sql-database-elastic-pool.md)
  - [Aumentar horizontalmente com a Base de Dados SQL do Azure](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> - Como implementar a aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino.
> - Sobre os servidores e bases de dados que constituem a aplicação.
> - Os inquilinos são mapeados para os dados com o *catálogo*.
> - Como aprovisionar novos inquilinos, numa linha de base de dados multi-inquilino e base de dados de inquilino único.
> - Como ver a utilização do conjunto para monitorizar a atividade de inquilino.
> - Como eliminar recursos de exemplo para parar a faturação relacionada.

Experimente agora o [tutorial de aprovisionamento e catalogação](sql-database-saas-tutorial-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Botão para implementar no Azure."

