---
title: Implementar uma aplicação saas de base de dados de vários inquilinos com vários inquilinos
description: Implemente e explore a aplicação de base de dados multi-inquilinos Stas, que demonstra padrões SaaS utilizando a Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/16/2018
ms.openlocfilehash: 5363a1b7321bfcbb53b4494b51ee2ea2e7217782
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619649"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Implementar e explorar uma aplicação de multi-inquilinos com caco
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você implementa e explora uma aplicação SaaS multi-inquilino que se chama Wingtip Tickets. A aplicação Wingtip Tickets foi concebida para mostrar funcionalidades da Base de Dados Azure SQL que simplificam a implementação de cenários SaaS.

Esta implementação da aplicação Wingtip Tickets utiliza um padrão de base de dados de vários inquilinos. O fragmento é do identificador do inquilino. Os dados do inquilino são distribuídos numa determinada base de dados de acordo com os valores do identificador do arrendatário. 

Este padrão de base de dados permite armazenar um ou mais inquilinos em cada fragmento ou base de dados. Pode otimizar pelo menor custo, tendo cada base de dados partilhada por vários inquilinos. Ou pode otimizar para o isolamento, tendo cada loja de dados apenas um inquilino. A sua escolha de otimização pode ser feita de forma independente para cada inquilino específico. Sua escolha pode ser feita quando o inquilino é armazenado pela primeira vez, ou pode mudar de ideia mais tarde. A aplicação foi concebida para funcionar bem de qualquer forma.

## <a name="app-deploys-quickly"></a>App implementa rapidamente

A aplicação funciona na nuvem Azure e utiliza a Base de Dados Azure SQL. A secção de implantação que se segue fornece o botão **Azul Implementar para Azure.** Quando o botão é premido, a aplicação é totalmente implantada na sua subscrição Azure dentro de cinco minutos. Tem acesso total ao trabalho com os componentes de aplicação individuais.

O pedido é implementado com dados para três inquilinos da amostra. Os inquilinos estão guardados juntos numa base de dados de vários inquilinos.

Qualquer pessoa pode descarregar o código fonte C# e PowerShell para bilhetes Wingtip a partir do [seu repositório GitHub][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Aprenda neste tutorial

> [!div class="checklist"]
> - Como implementar a aplicação Wingtip Tickets SaaS.
> - Onde obter o código fonte de aplicação e scripts de gestão.
> - Sobre os servidores e bases de dados que compõem a aplicação.
> - Como os inquilinos são mapeados para os seus dados com o *catálogo.*
> - Como providenciar um novo inquilino.
> - Como monitorizar a atividade do inquilino na app.

Uma série de tutoriais relacionados está disponível que se baseiam nesta implementação inicial. Os tutoriais exploram uma gama de padrões de design e gestão saaS. Quando você trabalha através dos tutoriais, você é encorajado a passar através dos scripts fornecidos para ver como os diferentes padrões SaaS são implementados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

- O mais recente Azure PowerShell está instalado. Para mais detalhes, consulte [Começar com a Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Implementar a app Wingtip Tickets

### <a name="plan-the-names"></a>Planeie os nomes

Nos passos desta secção, fornece um valor de *utilizador* que é usado para garantir que os nomes de recursos são globalmente únicos, e um nome para o grupo de *recursos* que contém todos os recursos criados por uma implementação da app. Para uma pessoa chamada *Ann Finley,* sugerimos:
- *Utilizador:* **af1**  *(Suas iniciais, mais um dígito. Utilize um valor diferente (por exemplo, af2) se implementar a aplicação uma segunda vez.)*
- *Grupo de recursos:* **wingtip-mt-af1** *(wingtip-mt indica que esta é a app multi-inquilinos. Appending o nome de utilizador af1 correlaciona o nome do grupo de recursos com os nomes dos recursos que contém.)*

Escolham os vossos nomes agora e escrevam-nos. 

### <a name="steps"></a>Passos

1. Clique no seguinte botão **'Implementar' para Azure.**
   - Abre o portal Azure com o modelo de implantação de Bilhetes SaaS wingtip.

     [![Botão para implantação para Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Introduza os parâmetros necessários para a implantação.

    > [!IMPORTANT]
    > Para esta demonstração, não utilize grupos de recursos, servidores ou piscinas pré-existentes. Em vez disso, escolha **Criar um novo grupo de recursos.** Elimine este grupo de recursos quando tiver terminado com a aplicação para parar a faturação relacionada.
    > Não utilize esta aplicação, nem quaisquer recursos que crie, para produção. Alguns aspetos da autenticação e as definições de firewall do servidor são intencionalmente inseguros na aplicação para facilitar a demonstração.

    - Para **o grupo de recursos** - Selecione Criar **novo**, e, em seguida, fornecer um **Nome** para o grupo de recursos (sensível a caso).
        - Selecione uma **localização** da lista de drop-down.
    - Para **o Utilizador** - Recomendamos que escolha um valor de **utilizador** curto.

1. **Implementar a aplicação**.

    - Clique para concordar com os termos e condições.
    - Clique em **Comprar**.

1. Monitorize o estado de implementação clicando em **Notificações**, que é o ícone da campainha à direita da caixa de pesquisa. A implementação da aplicação Wingtip demora aproximadamente cinco minutos.

   ![implementação concluída com êxito](./media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Descarregue e desbloqueie os scripts de gestão

Enquanto a aplicação estiver a ser implementada, descarregue o código fonte de aplicação e os scripts de gestão.

> [!NOTE]
> Os conteúdos executáveis (scripts, DLLs) podem ser bloqueados pelo Windows quando os ficheiros zip são descarregados de uma fonte externa e extraídos. Ao extrair os scripts de um ficheiro zip, utilize os seguintes passos para desbloquear o ficheiro .zip antes de extrair. Ao desbloquear o ficheiro .zip, certifique-se de que os scripts podem ser executados.

1. Navegue pelo [WingtipTicketsSaaS-MultiTenantDb GitHub repo](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Clique **em Clone ou descarregue.**
3. Clique **em Baixar ZIP** e guarde o ficheiro.
4. Clique com o botão direito no ficheiro **WingtipTicketsSaaS-MultiTenantDb-master.zip** e selecione **Propriedades**.
5. No separador **Geral,** selecione **Desbloqueie**e clique em **Aplicar**.
6. Clique em **OK**.
7. Extraia os ficheiros.

Os scripts estão localizados no *.. \\ Pasta de \\ aprendizagem \\ WingtipTicketsSa-MultiTenantDb-master.*

## <a name="update-the-configuration-file-for-this-deployment"></a>Atualizar o ficheiro de configuração para esta implementação

Antes de executar quaisquer scripts, desenfim o *grupo de recursos* e os valores do *utilizador* em **UserConfig.psm1**. Desa esta variável para os mesmos valores definidos durante a implantação.

1. Abrir... \\ Módulos de Aprendizagem \\ *UserConfig.psm1* no *PowerShell ISE*.
2. Atualizar O Nome e *Nome* *do Grupo de Recursos* com os valores específicos para a sua implantação (apenas nas linhas 10 e 11).
3. Guarde as alterações.

Os valores definidos neste ficheiro são utilizados por todos os scripts, por isso é importante que sejam precisos. Se recolocar a aplicação, deve escolher valores diferentes para o Grupo de Utilizador e Recursos. Em seguida, atualize novamente o ficheiro UserConfig.psm1 com os novos valores.

## <a name="run-the-application"></a>Executar a aplicação

Na aplicação Wingtip, os inquilinos são locais. Um local pode ser sala de concertos, um clube de esportes, ou qualquer outro local que acolhe eventos. Os locais registam-se em Wingtip como clientes, e um identificador de inquilinos é gerado para cada local. Cada local lista os seus próximos eventos em Wingtip, para que o público possa comprar bilhetes para os eventos.

Cada local recebe uma aplicação web personalizada para listar os seus eventos e vender bilhetes. Cada aplicação web é independente e isolada de outros inquilinos. Internamente na Base de Dados Azure SQL, cada um dos dados de cada inquilino é armazenado numa base de dados de vários inquilinos, por padrão. Todos os dados estão marcados com o identificador do inquilino.

Uma página central do Centro de **Eventos** fornece uma lista de links para os inquilinos na sua implantação particular. Use os seguintes passos para experimentar a página do **Events Hub** e uma aplicação web individual:

1. Abra o **Centro de Eventos** no seu navegador web:
   - http://events.wingtip-mt.&lt;user &gt; .trafficmanager.net &nbsp; *(Substitua &lt; o utilizador pelo valor do utilizador da sua &gt; implantação.)*

     ![hub de eventos](./media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Clique em **Fabrikam Jazz Club** no **Hub de Eventos**.

   ![Eventos](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Gestor de Tráfego do Azure

Para controlar a distribuição de pedidos de entrada, a aplicação Wingtip utiliza [o Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md). A página de eventos para cada inquilino inclui o nome do inquilino na sua URL. Cada URL também inclui o seu valor específico do Utilizador. Cada URL obedece ao formato apresentado utilizando os seguintes passos:

- http://events.wingtip-mt.&lt;user &gt; .trafficmanager.net/*fabrikamjazzclub*

1. A aplicação de eventos analisa o nome do inquilino da URL. O nome do inquilino é *fabrikamjazzclub* no exemplo anterior URL.
2. A aplicação então tem o nome do inquilino para criar uma chave para aceder a um catálogo usando [a gestão de mapas de fragmentos.](elastic-scale-shard-map-management.md)
3. A aplicação encontra a chave no catálogo e obtém a localização correspondente da base de dados do arrendatário.
4. A aplicação utiliza a informação de localização para encontrar e aceder à base de dados que contém todos os dados para o inquilino.

### <a name="events-hub"></a>Centro de Eventos

1. O **Centro de Eventos** lista todos os inquilinos que estão registados no catálogo, e seus locais.
2. O **Centro de Eventos** utiliza metadados estendidos no catálogo para recuperar o nome do inquilino associado a cada mapeamento para construir os URLs.

Num ambiente de produção, você normalmente cria um registo DE DNS CNAME para [apontar um domínio de internet](../../traffic-manager/traffic-manager-point-internet-domain.md) da empresa para o perfil de gestor de tráfego.

## <a name="start-generating-load-on-the-tenant-databases"></a>Começar a gerar carga nas bases de dados de inquilinos

Agora que a aplicação está implantada, vamos colocá-la a funcionar! O script *Demo-LoadGenerator* PowerShell inicia uma carga de trabalho em execução para cada inquilino. A carga real em muitas aplicações SaaS é tipicamente esporádica e imprevisível. Para simular este tipo de carga, o gerador produz uma carga distribuída por todos os inquilinos. A carga inclui rajadas aleatórias em cada inquilino que ocorram em intervalos aleatórios. Leva vários minutos para o padrão de carga emergir, por isso é melhor deixar o gerador funcionar durante pelo menos três ou quatro minutos antes de monitorizar a carga.

1. No *PowerShell ISE,* abra o ... \\ Módulos de Aprendizagem \\ Utilitários \\ *Demo-LoadGenerator.ps1* script.
2. Prima **F5** para executar o script e iniciar o gerador de carga (deixe os valores predefinidos do parâmetro por agora).

O *Demo-LoadGenerator.ps1* script abre outra sessão PowerShell onde o gerador de carga funciona. O gerador de carga funciona nesta sessão como uma tarefa de primeiro plano que invoca empregos de geração de carga de fundo, um para cada inquilino.

Após o início da tarefa em primeiro plano, permanece num estado de invocação de emprego. A tarefa inicia trabalhos de fundo adicionais para quaisquer novos inquilinos que sejam posteriormente a provisionados.

Fechar a sessão PowerShell para todos os empregos.

É melhor reiniciar a sessão do gerador de carga para utilizar diferentes valores de parâmetros. Em caso afirmativo, feche a sessão de geração PowerShell e, em seguida, reexame a *Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Provisione um novo inquilino na base de dados de fragmentos

A implantação inicial inclui três inquilinos de amostra na base de *dados dos Inquilinos1.* Vamos criar outro inquilino e observar os seus efeitos na aplicação implementada. Neste passo, você pressiona uma chave para criar um novo inquilino:

1. Abrir... \\ Fornecimento de Módulos de Aprendizagem \\ eDemo-ProvisionTenants.ps1de catálogo no \\ * * *PowerShell ISE*.
2. Prima **F5** (não **F8)** para executar o script (deixe os valores predefinidos por enquanto).

   > [!NOTE]
   > Só tens de executar os scripts PowerShell premindo a tecla **F5,** e não pressionando **o F8** para executar uma parte selecionada do script. O problema com **o F8** é que a variável *$PSScriptRoot* não é avaliada. Esta variável é necessária por muitos scripts para navegar em pastas, invocar outros scripts ou importar módulos.

O novo inquilino da Red Maple Racing é adicionado à base de *dados dos Inquilinos1* e registado no catálogo. O site de eventos de venda de **bilhetes** do novo inquilino abre no seu browser:

![Novo inquilino](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Refresque o **Centro de Eventos,** e o novo inquilino aparece agora na lista.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Provisiona um novo inquilino na sua própria base de dados

O modelo de multi-inquilinos em pedaços permite-lhe escolher se fornece um novo inquilino numa base de dados que contenha outros inquilinos, ou numa base de dados própria. Um inquilino isolado na sua própria base de dados goza dos seguintes benefícios:

- O desempenho da base de dados do arrendatário pode ser gerido sem a necessidade de se comprometer com as necessidades de outros inquilinos.
- Se necessário, a base de dados pode ser restaurada num momento mais precoce, porque nenhum outro inquilino seria afetado.

Você pode optar por colocar clientes de teste gratuito, ou clientes económicos, em bases de dados multi-inquilinos. Pode colocar cada inquilino premium na sua própria base de dados dedicada. Se criar muitas bases de dados que contenham apenas um inquilino, pode geri-las todas coletivamente numa piscina elástica para otimizar os custos de recursos.

Em seguida, nós providenciaremos outro inquilino, desta vez na sua própria base de dados:

1. Em ... \\ Módulos de Aprendizagem \\ Provisão e \\ * catálogoDemo-ProvisionTenants.ps1, *modificar *$TenantName* a **Salix Salsa,** *$VenueType* para **dançar** e *$Scenario* a **2**.

2. Prima **F5** para voltar a executar o guião.
    - Esta **imprensa F5** fornece o novo inquilino numa base de dados separada. A base de dados e o inquilino estão registados no catálogo. Em seguida, o navegador abre para a página eventos do inquilino.

   ![Página de eventos de Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Desloque-se até à parte inferior da página. Ali no banner você vê o nome da base de dados em que os dados do inquilino são armazenados.

3. Refresque o **Centro de Eventos** e os dois novos inquilinos agora aparecem na lista.

## <a name="explore-the-servers-and-tenant-databases"></a>Explore os servidores e bases de dados de inquilinos

Agora olhamos para alguns dos recursos que foram implantados:

1. No [portal Azure,](https://portal.azure.com)consulte a lista de grupos de recursos. Abra o grupo de recursos que criou quando implementou a aplicação.

   ![grupo de recursos](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Clique no servidor ** &lt; de utilizador &gt; catalog-mt.** O servidor de catálogo contém duas bases de dados chamadas *tenantcatalog* e *basetenantdb*. A base *de dados basetenantdb* é uma base de dados de modelos vazia. É copiado para criar uma nova base de dados de inquilinos, seja para muitos inquilinos ou apenas para um inquilino.

   ![servidor de catálogo](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Volte para o grupo de recursos e selecione o servidor *de inquilinos1 mt* que detém as bases de dados dos inquilinos.
    - A base de dados dos inquilinos1 é uma base de dados multi-inquilinos na qual os três inquilinos originais, mais o primeiro inquilino que acrescentou, estão armazenados. Está configurado como uma base de dados 50 DTU Standard.
    - A base **de dados de Salixsalsa** detém o local de dança Salix Salsa como seu único inquilino. É configurado como uma base de dados standard edition com 50 DTUs por padrão.

   ![servidor inquilinos](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Monitorizar o desempenho da base de dados

Se o gerador de carga estiver em funcionamento há vários minutos, está disponível telemetria suficiente para ver as capacidades de monitorização da base de dados incorporadas no portal Azure.

1. Navegue pelo servidor de ** &lt; utilizadores &gt; de inquilinos1 mt** e clique nos **inquilinos1** para ver a utilização de recursos para a base de dados que tem quatro inquilinos nele. Cada inquilino está sujeito a uma carga esporádica pesada do gerador de carga:

   ![monitorizar inquilinos1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   O gráfico de utilização do DTU ilustra bem como uma base de dados multi-inquilinos pode suportar uma carga de trabalho imprevisível em muitos inquilinos. Neste caso, o gerador de carga está a aplicar uma carga esporádica de cerca de 30 DTUs a cada inquilino. Esta carga equivale a 60% de utilização de uma base de dados de 50 DTU. Picos que ultrapassam os 60% são o resultado da carga aplicada a mais de um inquilino ao mesmo tempo.

2. Navegue pelo servidor **de &lt; &gt; utilizadores de 1 mt** dos inquilinos e clique na base de **dados salixsalsa.** Você pode ver a utilização de recursos nesta base de dados que contém apenas um inquilino.

   ![base de dados salixsalsa](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

O gerador de carga está a aplicar uma carga semelhante a cada inquilino, independentemente da base de dados em que cada inquilino se encontra. Com apenas um inquilino na base de **dados de salixsalsa,** você pode ver que a base de dados poderia sustentar uma carga muito maior do que a base de dados com vários inquilinos. 

### <a name="resource-allocations-vary-by-workload"></a>As alocações de recursos variam por carga de trabalho

Às vezes, uma base de dados multi-inquilino requer mais recursos para um bom desempenho do que uma base de dados de um único inquilino, mas nem sempre. A alocação ideal de recursos depende das características específicas da carga de trabalho para os inquilinos do seu sistema.

As cargas de trabalho geradas pelo script do gerador de carga são apenas para ilustração.

## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre aplicações SaaS multi-arrendatários, consulte [padrões de design para aplicações SaaS multi-arrendatários.](saas-tenancy-app-design-patterns.md)

- Para aprender sobre piscinas elásticas, consulte:

  - [Piscinas elásticas ajudam a gerir e escalar várias bases de dados na Base de Dados Azure SQL](elastic-pool-overview.md)
  - [Aumentar horizontalmente com a Base de Dados SQL do Azure](elastic-scale-introduction.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber:

> [!div class="checklist"]
> - Como implementar a aplicação Wingtip Tickets Multi-inquilino Database.
> - Sobre os servidores e bases de dados que compõem a aplicação.
> - Os inquilinos estão mapeados para os seus dados com o *catálogo.*
> - Como providenciar novos inquilinos, numa base de dados multi-inquilinos e numa base de dados de inquilinos únicos.
> - Como visualizar a utilização da piscina para monitorizar a atividade do inquilino.
> - Como eliminar recursos de amostra para parar a faturação relacionada.

Agora experimente o [tutorial de Provisão e catálogo.](saas-multitenantdb-provision-and-catalog.md)


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

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Botão para implantação para Azure."

