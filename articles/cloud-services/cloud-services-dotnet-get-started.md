---
title: Introdução ao Cloud Services do Azure e ao ASP.NET | Microsoft Docs
description: Saiba como criar uma aplicação de várias camadas com o MVC do ASP.NET e o Azure. A aplicação é executada num serviço em nuvem, com a função da Web e a função de trabalho. Utiliza as filas e os blobs do Entity Framework, da SQL Database e do Storage do Azure.
services: cloud-services, storage
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: d7aa440d-af4a-4f80-b804-cc46178df4f9
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: jeconnoc
ms.openlocfilehash: aa62db0948ffa036b37736477b872d694d14836b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762615"
---
# <a name="get-started-with-azure-cloud-services-and-aspnet"></a>Introdução ao Cloud Services do Azure e ao ASP.NET

## <a name="overview"></a>Descrição geral
Este tutorial mostra como criar uma aplicação do .NET de várias camadas com um front-end do MVC do ASP.NET e como implementá-lo num [serviço em nuvem do Azure](cloud-services-choose-me.md). A aplicação utiliza a [SQL Database](https://msdn.microsoft.com/library/azure/ee336279), o [serviço Blob do Azure](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) e o [serviço Fila do Azure](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern). Pode [transferir o projeto do Visual Studio](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) da Galeria de Códigos do MSDN.

O tutorial mostra como compilar e executar a aplicação localmente, como implementá-la no Azure e executá-la na cloud e como compilá-la do zero. Pode começar por compilar do zero e, posteriormente, realizar os passos de teste e implementação, se preferir.

## <a name="contoso-ads-application"></a>Aplicação de Anúncios da Contoso
A aplicação é um BBS de publicidade. Os utilizadores criam um anúncio através da introdução de texto e carregamento de uma imagem. Podem ver uma lista de anúncios com imagens em miniatura e ver a imagem de tamanho completo quando selecionarem um anúncio para ver os detalhes.

![Lista de Anúncios](./media/cloud-services-dotnet-get-started/list.png)

A aplicação utiliza o [padrão de trabalho centrado em filas](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) para transferir o trabalho intensivo da CPU de criar miniaturas para um processo de back-end.

## <a name="alternative-architecture-app-service-and-webjobs"></a>Arquitetura alternativa: Serviço de aplicações e WebJobs
Este tutorial mostra como executar front-end e back-end num serviço em nuvem do Azure. Uma alternativa é executar o front-end no [App Service do Azure](/azure/app-service/) e utilizar os [WebJobs](https://go.microsoft.com/fwlink/?LinkId=390226) funcionalidade para o back-end. Para obter um tutorial que utiliza WebJobs, consulte [Introdução ao SDK de WebJobs do Azure](https://github.com/Azure/azure-webjobs-sdk/wiki). Para obter informações sobre como escolher os serviços que melhor se adequam ao seu cenário, consulte [comparação do serviço de aplicações do Azure, serviços Cloud e máquinas virtuais](../app-service/overview-compare.md).

## <a name="what-youll-learn"></a>O que irá aprender
* Como ativar o computador para a programação do Azure instalando o Azure SDK.
* Como criar um projeto de serviço em nuvem do Visual Studio com uma função da Web e uma função de trabalho do MVC do ASP.NET.
* Como testar o projeto de serviço em nuvem localmente, utilizando o emulador do Storage do Azure.
* Como publicar o projeto de nuvem num serviço em nuvem do Azure e testá-lo utilizando uma conta do Storage do Azure.
* Como carregar ficheiros e armazená-los no serviço Blob do Azure.
* Como utilizar o serviço Fila do Azure para a comunicação entre camadas.

## <a name="prerequisites"></a>Pré-requisitos
O tutorial parte do princípio de que compreende os [conceitos básicos dos Cloud Services do Azure](cloud-services-choose-me.md) como a terminologia *função da Web* e *função de trabalho*.  Também parte do princípio de que sabe como trabalhar com o [MVC do ASP.NET](https://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) ou com projetos de [Formulários Web](https://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview) no Visual Studio. A aplicação de exemplo utiliza o MVC, mas a maioria do tutorial também se aplica aos Formulários Web.

Pode executar a aplicação localmente sem precisar de uma subscrição do Azure, mas necessitará de uma para implementar a aplicação na cloud. Se não tiver uma conta, pode [ativar os seus benefícios de subscritor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A55E3C668) ou [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A55E3C668).

As instruções do tutorial funcionam com qualquer um dos seguintes produtos:

* Visual Studio 2013
* Visual Studio 2015
* Visual Studio 2017

Se não tiver nenhum destes produtos, o Visual Studio poderá ser instalado automaticamente ao instalar o Azure SDK.

## <a name="application-architecture"></a>Arquitetura da aplicação
A aplicação armazena anúncios numa SQL Database, utilizando o Entity Framework Code First para criar as tabelas e aceder aos dados. Para cada anúncio, a base de dados armazena dois URLs, um para a imagem de tamanho completo e outro para a miniatura.

![Tabela de anúncios](./media/cloud-services-dotnet-get-started/adtable.png)

Quando um utilizador carrega uma imagem, o front-end em execução numa função da Web armazena a imagem num [blob do Azure](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) e armazena as informações do anúncio na base de dados com um URL que aponta para o blob. Ao mesmo tempo, escreve uma mensagem para uma fila do Azure. Um processo de back-end em execução numa função de trabalho consulta periodicamente a fila para verificar se existem novas mensagens. Quando for apresentada uma nova mensagem, a função de trabalho cria uma miniatura dessa imagem e atualiza o campo da base de dados de URLs de miniaturas desse anúncio. O diagrama seguinte mostra como interagem as partes da aplicação.

![Arquitetura dos Anúncios da Contoso](./media/cloud-services-dotnet-get-started/apparchitecture.png)

[!INCLUDE [install-sdk](../../includes/install-sdk-2017-2015-2013.md)]

## <a name="download-and-run-the-completed-solution"></a>Transferir e executar a solução concluída
1. Transfira e deszipe a [solução concluída](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4).
2. Inicie o Visual Studio.
3. No menu **Ficheiro**, escolha **Abrir Projeto**, navegue para onde transferiu a solução e, em seguida, abra o ficheiro da solução.
4. Prima CTRL+SHIFT+B para compilar a solução.

    Por predefinição, o Visual Studio restaura automaticamente o conteúdo do pacote NuGet, que não estava incluído no ficheiro *.zip*. Se os pacotes não forem restaurados, instale-os manualmente. Para tal, vá para a caixa de diálogo **Gerir Pacotes NuGet da Solução** e clique no botão **Restaurar** na parte superior direita.
5. No **Explorador de Soluções**, certifique-se de que **ContosoAdsCloudService** é selecionado como o projeto de arranque.
6. Se estiver a utilizar o Visual Studio 2015 ou superior, altere a cadeia de ligação do SQL Server no ficheiro *Web.config* da aplicação do projeto ContosoAdsWeb e no ficheiro*ServiceConfiguration.Local.cscfg* do projeto ContosoAdsCloudService. Em cada caso, altere “(localdb)\v11.0” para “(localdb)\MSSQLLocalDB”.
7. Prima CTRL+F5 para executar a aplicação.

    Quando executa localmente um projeto do serviço em nuvem, o Visual Studio invoca automaticamente o *emulador de computação* do Azure e o *emulador de armazenamento* do Azure. O emulador de computação utiliza os recursos do computador para simular os ambientes da função da Web e da função de trabalho. O emulador de armazenamento utiliza uma base de dados [SQL Server Express LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) para simular o armazenamento na nuvem do Azure.

    Quando executar um projeto do serviço em nuvem pela primeira vez, demorará um minuto ou mais até que os emuladores arranquem. Quando o arranque dos emuladores estiver concluído, o browser predefinido é aberto na home page da aplicação.

    ![Arquitetura dos Anúncios da Contoso](./media/cloud-services-dotnet-get-started/home.png)
8. Clique em **Criar um Anúncio**.
9. Introduza alguns dados de teste, selecione uma imagem *.jpg* para carregar e clique em **Criar**.

    ![Criar página](./media/cloud-services-dotnet-get-started/create.png)

    A aplicação acede à página Índice, mas não mostra nenhuma miniatura do novo anúncio, porque esse processamento ainda não ocorreu.
10. Aguarde um momento e, em seguida, atualize a página Índice para ver a miniatura.

     ![Página Índice](./media/cloud-services-dotnet-get-started/list.png)
11. Clique em **Detalhes** do anúncio para ver a imagem de tamanho completo.

     ![Página Detalhes](./media/cloud-services-dotnet-get-started/details.png)

A aplicação tem estado a ser executada inteiramente no computador local, sem ligação com a nuvem. O emulador de armazenamento armazena os dados de fila e blob numa base de dados do SQL Server Express LocalDB. A aplicação armazena os dados do anúncio noutra base de dados LocalDB. O Entity Framework Code First criou automaticamente a base de dados de anúncios da primeira vez que a aplicação Web tentou aceder à mesma.

Na secção seguinte, irá configurar a solução para utilizar recursos em nuvem do Azure para filas, blobs e para base de dados de aplicações quando for executada na nuvem. Se quisesse continuar a executar a aplicação localmente, mas utilizar recursos da base de dados e do armazenamento na cloud, poderia fazê-lo. Para tal ,bastaria definir cadeias de ligação (instruções a seguir).

## <a name="deploy-the-application-to-azure"></a>Implementar a aplicação no Azure
Terá de efetuar os passos seguintes para executar a aplicação na nuvem:

* Crie um serviço em nuvem do Azure.
* Crie uma SQL Database do Azure.
* Crie uma conta do Storage do Azure.
* Configure a solução para utilizar a SQL Database do Azure, quando é executada no Azure.
* Configure a solução para utilizar a sua conta do Storage do Azure, quando é executada no Azure.
* Implemente o projeto no serviço em nuvem do Azure.

### <a name="create-an-azure-cloud-service"></a>Criar um serviço em nuvem do Azure
Um serviço em nuvem do Azure é o ambiente onde a aplicação irá ser executada.

1. No browser, abra o [portal do Azure](https://portal.azure.com).
2. Clique em **Criar um recurso > Computação > Serviço Cloud**.

3. Na caixa de entrada do nome DNS, introduza um prefixo de URL para o serviço cloud.

    Este URL tem de ser exclusivo.  Obterá uma mensagem de erro se o prefixo que escolher já estiver a ser utilizado.
4. Especifique um novo grupo de Recursos para o serviço. Clique em **Criar novo** e, em seguida, escreva um nome na caixa de entrada do grupo de Recursos, como CS_contososadsRG.

5. Selecione a região onde pretende implementar a aplicação.

    Este campo especifica em qual datacenter será alojado o serviço em nuvem. Para uma aplicação de produção, deverá escolher a região mais próxima dos seus clientes. Para este tutorial, escolha a região mais próxima de si.
5. Clique em **Criar**.

    Na imagem seguinte, é criado um serviço cloud com o URL CSvccontosoads.cloudapp.net.

    ![Novo Serviço em Nuvem](./media/cloud-services-dotnet-get-started/newcs.png)

### <a name="create-an-azure-sql-database"></a>Criar uma SQL Database do Azure
Quando a aplicação é executada na nuvem, utilizará uma base de dados baseada na nuvem.

1. No [portal do Azure](https://portal.azure.com), clique em **Criar um recurso > Bases de Dados > Base de Dados SQL**.
2. Na caixa **Nome da Base de Dados**, introduza *contosoads*.
3. No **grupo de Recursos**, clique em **Utilizar existente** e selecione o grupo de recursos utilizado para o serviço cloud.
4. Na imagem seguinte, clique em **Servidor - configurar definições obrigatórias** e **Criar um novo servidor**.

    ![Túnel para o servidor de bases de dados](./media/cloud-services-dotnet-get-started/newdb.png)

    Em alternativa, se a sua subscrição já tiver um servidor, pode selecionar esse servidor na lista pendente.
5. Na caixa **Nome do servidor** , introduza *csvccontosodbserver*.

6. Introduza um **Nome de Início de Sessão** e **Palavra-Passe** de administrador.

    Se tiver selecionado **Criar um novo servidor**, não introduzirá um nome e palavra-passe existentes aqui. Deverá introduzir um novo nome e palavra-passe, que definirá agora para utilizar posteriormente quando aceder à base de dados. Se tiver selecionado um servidor criado anteriormente, será solicitada a palavra-passe da conta de utilizador administrativo criada anteriormente.
7. Escolha a mesma **Localização** que escolheu para o serviço cloud.

    Se o serviço em nuvem e a base de dados estiverem em datacenters diferentes (regiões diferentes), a latência aumentará e será ser-lhe-á debitada a largura de banda fora do datacenter. A largura de banda dentro de um datacenter é gratuita.
8. Marque **Permitir o acesso dos serviços do Azure ao servidor**.
9. Clique em **Selecionar** para o novo servidor.

    ![Novo servidor de Base de Dados SQL](./media/cloud-services-dotnet-get-started/newdbserver.png)
10. Clique em **Criar**.

### <a name="create-an-azure-storage-account"></a>Criar uma conta do Storage do Azure
Uma conta do Storage do Azure fornece recursos para armazenar dados de fila e blob na nuvem.

Numa aplicação real, normalmente criaria contas separadas para os dados da aplicação versus os dados de registo e contas separadas para os dados de teste versus os dados de produção. Para este tutorial, utilizará apenas uma conta.

1. No [portal do Azure](https://portal.azure.com), clique em **Criar um recurso > Armazenamento > Conta de armazenamento - blob, ficheiro, tabela, fila**.
2. Na caixa **Nome**, introduza um prefixo de URL.

    Este prefixo e o texto que vê por baixo da caixa será o URL exclusivo para a sua conta do Storage. Se o prefixo que introduzir já tiver sido utilizado por outra pessoa, terá de escolher um diferente.
3. Defina o **Modelo de implementação** como *Clássico*.

4. Defina a lista pendente **Replicação** para **Armazenamento localmente redundante**.

    Quando a georreplicação está ativada para uma conta de armazenamento, o conteúdo armazenado é replicado para um datacenter secundário para ativar a ativação pós-falha, se ocorrer um desastre grave na localização primária. A georreplicação pode implicar custos adicionais. Para contas de teste e de desenvolvimento, normalmente não deseja pagar a georreplicação. Para obter mais informações, consulte [Criar, gerir ou eliminar uma conta de armazenamento](../storage/common/storage-create-storage-account.md).

5. No **grupo de Recursos**, clique em **Utilizar existente** e selecione o grupo de recursos utilizado para o serviço cloud.
6. Defina a **Localização** na lista pendente para a mesma região que selecionou para o serviço cloud.

    Se o serviço em nuvem e a conta do Storage estiverem em datacenters diferentes (regiões diferentes), a latência aumentará e será ser-lhe-á debitada a largura de banda fora do datacenter. A largura de banda dentro de um datacenter é gratuita.

    Os grupos de afinidades do Azure fornecem um mecanismo para minimizar a distância entre os recursos num data center, o que poderá reduzir a latência. Este tutorial não utiliza grupos de afinidades. Para obter mais informações, consulte [Como Criar um Grupo de Afinidades no Azure](https://msdn.microsoft.com/library/azure/gg715317.aspx).
7. Clique em **Criar**.

    ![Nova conta do Storage](./media/cloud-services-dotnet-get-started/newstorage.png)

    Na imagem, é criada uma conta do Storage com o URL `csvccontosoads.core.windows.net`.

### <a name="configure-the-solution-to-use-your-azure-sql-database-when-it-runs-in-azure"></a>Configurar a solução para utilizar a SQL Database do Azure quando for executada no Azure
O projeto Web e o projeto de função de trabalho têm a sua própria cadeia de ligação de base de dados, e cada uma tem de apontar para a SQL Database do Azure quando a aplicação for executada no Azure.

Utilizará uma [transformação Web.config](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) para a função da Web e uma definição de ambiente de serviço em nuvem para a função de trabalho.

> [!NOTE]
> Nesta secção e na secção seguinte, as credenciais serão armazenadas nos ficheiros de projeto. [Não armazene dados confidenciais em repositórios de código-fonte públicos](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets).
>
>

1. No projeto ContosoAdsWeb, abra o ficheiro de transformação *Web.Release.config* para o ficheiro *Web.config* da aplicação, elimine o bloco de comentários que contém um elemento `<connectionStrings>` e cole o seguinte código no seu lugar.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="{connectionstring}"
        providerName="System.Data.SqlClient" xdt:Transform="SetAttributes" xdt:Locator="Match(name)"/>
    </connectionStrings>
    ```

    Deixe o ficheiro aberto para edição.
2. No [portal do Azure](https://portal.azure.com), clique em **Bases de Dados SQL** no painel esquerdo, clique na base de dados criada para este tutorial e, em seguida, clique em **Mostrar cadeias de ligação**.

    ![Mostrar cadeias de ligação](./media/cloud-services-dotnet-get-started/showcs.png)

    O portal apresenta as cadeias de ligação, com um marcador de posição para a palavra-passe.

    ![Cadeias de ligação](./media/cloud-services-dotnet-get-started/connstrings.png)
3. No ficheiro de transformação *Web.Release.config*, elimine `{connectionstring}` e cole no seu lugar a cadeia de ligação ADO.NET a partir do portal do Azure.
4. Na cadeia de ligação que colou no ficheiro de transformação *Web.Release.config*, substitua `{your_password_here}` pela palavra-passe que criou para a nova SQL Database.
5. Guarde o ficheiro.  
6. Selecione e copie a cadeia de ligação (sem as aspas) para utilização nos passos seguintes para configurar o projeto de função de trabalho.
7. No **Explorador de Soluções**, em **Funções** no projeto do serviço em nuvem, clique com o botão direito do rato em **ContosoAdsWorker** e, em seguida, em **Propriedades**.

    ![Propriedades da função](./media/cloud-services-dotnet-get-started/rolepropertiesworker.png)
8. Clique no separador **Definições**.
9. Altere **Configuração do Serviço** para **Nuvem**.
10. Selecione o campo **Valor** para a definição `ContosoAdsDbConnectionString` e, em seguida, cole a cadeia de ligação que copiou na secção anterior do tutorial.

     ![Cadeia de ligação da base de dados para a função de trabalho](./media/cloud-services-dotnet-get-started/workerdbcs.png)
11. Guarde as alterações.  

### <a name="configure-the-solution-to-use-your-azure-storage-account-when-it-runs-in-azure"></a>Configurar a solução para utilizar a conta do Storage do Azure quando for executada no Azure
As cadeias de ligação da conta do Storage do Azure para o projeto da função da Web e para o projeto da função de trabalho são armazenadas nas definições de ambiente no projeto do serviço em nuvem. Para cada projeto existe um conjunto separado de definições para serem utilizadas quando a aplicação é executada localmente e quando é executada na cloud. Deverá atualizar as definições do ambiente de nuvem para os projetos da função da Web e de trabalho.

1. No **Explorador de Soluções**, clique com o botão direito do rato em **ContosoAdsWeb**, em **Funções** no projeto **ContosoAdsCloudService**, e clique em **Propriedades**.

    ![Propriedades da função](./media/cloud-services-dotnet-get-started/roleproperties.png)
2. Clique no separador **Definições**. Na caixa pendente **Configuração do serviço**, selecione **Nuvem**.

    ![Configuração da nuvem](./media/cloud-services-dotnet-get-started/sccloud.png)
3. Selecione a entrada **StorageConnectionString**, verá um botão de reticências (**…**) na extremidade direita da linha. Clique no botão de reticências para abrir a caixa de diálogo **Criar Cadeia de Ligação da Conta do Storage**.

    ![Abrir a caixa Criar cadeia de ligação](./media/cloud-services-dotnet-get-started/opencscreate.png)
4. Na caixa de diálogo **Criar Cadeia de Ligação de Armazenamento**, clique em **Sua subscrição**, selecione a conta do Storage que criou anteriormente e, em seguida, clique em **OK**. Se ainda não tiver sessão iniciada, ser-lhe-ão solicitadas as credenciais da conta do Azure.

    ![Criar Cadeia de Ligação de Armazenamento](./media/cloud-services-dotnet-get-started/createstoragecs.png)
5. Guarde as alterações.
6. Siga o mesmo procedimento utilizado para a cadeia de ligação `StorageConnectionString` para definir a cadeia de ligação `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

    Esta cadeia de ligação é utilizada para registo.
7. Siga o mesmo procedimento utilizado para a função **ContosoAdsWeb** para definir ambas as cadeias de ligação para a função **ContosoAdsWorker**. Não se esqueça de definir a **Configuração do Serviço** para **Nuvem**.

As definições do ambiente das funções configuradas utilizando a IU do Visual Studio são armazenadas nos seguintes ficheiros do projeto ContosoAdsCloudService:

* *ServiceDefinition.csdef* – define os nomes de definição.
* *ServiceConfiguration.Cloud.cscfg* – fornece valores para quando a aplicação é executada na nuvem.
* *ServiceConfiguration.Cloud.cscfg* – fornece valores para quando a aplicação é executada localmente.

Por exemplo, ServiceDefinition.csdef inclui as seguintes definições:

```xml
<ConfigurationSettings>
    <Setting name="StorageConnectionString" />
    <Setting name="ContosoAdsDbConnectionString" />
</ConfigurationSettings>
```

E o ficheiro *ServiceConfiguration.Cloud.cscfg* inclui os valores que introduziu para essas definições no Visual Studio.

```xml
<Role name="ContosoAdsWorker">
    <Instances count="1" />
    <ConfigurationSettings>
        <Setting name="StorageConnectionString" value="{yourconnectionstring}" />
        <Setting name="ContosoAdsDbConnectionString" value="{yourconnectionstring}" />
        <!-- other settings not shown -->

    </ConfigurationSettings>
    <!-- other settings not shown -->

</Role>
```

A definição `<Instances>` especifica o número de máquinas virtuais em que o Azure executará o código da função de trabalho. A secção [Passos seguintes](#next-steps) inclui ligações para mais informações sobre como ampliar um serviço em nuvem.

### <a name="deploy-the-project-to-azure"></a>Implementar o projeto no Azure
1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto de nuvem **ContosoAdsCloudService** e selecione **Publicar**.

   ![Menu Publicar](./media/cloud-services-dotnet-get-started/pubmenu.png)
2. No passo **Iniciar sessão** do assistente **Publicar Aplicação Azure**, clique em **Seguinte**.

    ![Passo Iniciar sessão](./media/cloud-services-dotnet-get-started/pubsignin.png)
3. No passo **Definições** do assistente, clique em **Seguinte**.

    ![Passo Definições](./media/cloud-services-dotnet-get-started/pubsettings.png)

    As predefinições no separador **Avançado** podem ser utilizadas neste tutorial. Para obter informações sobre o separador Avançado, consulte [Publicar Assistente da Aplicação Azure](https://docs.microsoft.com/azure/vs-azure-tools-publish-azure-application-wizard).
4. No passo **Resumo**, clique em **Publicar**.

    ![Passo Resumo](./media/cloud-services-dotnet-get-started/pubsummary.png)

   A janela **Registo de Atividade do Azure** é aberta no Visual Studio.
5. Clique no ícone de seta para a direita para expandir os detalhes da implementação.

    A implementação pode demorar 5 minutos ou mais para ser concluída.

    ![Janela Registo de Atividade do Azure](./media/cloud-services-dotnet-get-started/waal.png)
6. Quando o estado de implementação estiver concluído, clique no **URL da aplicação Web** para iniciar a aplicação.
7. Pode agora testar a aplicação através da criação, visualização e edição de alguns anúncios, como fez quando executou a aplicação localmente.

> [!NOTE]
> Quando tiver terminado o teste, elimine ou pare o serviço em nuvem. Mesmo se não estiver a utilizar o serviço em nuvem, este estará a acumular encargos, porque os recursos de máquinas virtuais estão reservados para o mesmo. E, se o deixar a funcionar, qualquer pessoa que encontre o URL pode criar e ver anúncios. No [portal do Azure](https://portal.azure.com), aceda ao separador **Descrição Geral** do serviço cloud e clique no botão **Eliminar** na parte superior da página. Se apenas pretender impedir temporariamente que outras pessoas acedam ao site, clique em **Parar**. Nesse caso, os encargos irão continuar a acumular. Pode seguir um procedimento semelhante para eliminar a SQL Database e a conta do Storage quando já não necessitar delas.
>
>

## <a name="create-the-application-from-scratch"></a>Criar a aplicação do zero
Se ainda não transferiu [a aplicação concluída](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4), faça-o agora. Deverá copiar os ficheiros do projeto transferido para o novo projeto.

A criação da aplicação de Anúncios da Contoso envolve os seguintes passos:

* Criar uma solução do Visual Studio do serviço em nuvem.
* Atualizar e adicionar pacotes NuGet.
* Definir referências do projeto.
* Configurar cadeias de ligação.
* Adicionar ficheiros de código.

Após criar a solução, deverá consultar o código que é exclusivo para os projetos do serviço em nuvem e para os blobs e filas do Azure.

### <a name="create-a-cloud-service-visual-studio-solution"></a>Criar uma solução do Visual Studio do serviço em nuvem
1. No Visual Studio, selecione **Novo Projeto** no menu **Ficheiro**.
2. No painel esquerdo da caixa de diálogo **Novo Projeto**, expanda **Visual c#**, escolha os modelos **Nuvem** e, em seguida, escolha o modelo **Serviço em Nuvem do Azure**.
3. Nomeie o projeto e a solução ContosoAdsCloudService e clique em **OK**.

    ![Novo Projeto](./media/cloud-services-dotnet-get-started/newproject.png)
4. Na caixa de diálogo **Novo Serviço em Nuvem do Azure**, adicione uma função da Web e uma função de trabalho. Nomeie a função da Web ContosoAdsWeb e a função de trabalho ContosoAdsWorker. (Utilize o ícone de lápis no painel direito para alterar os nomes predefinidos das funções.)

    ![Novo Projeto do Serviço em Nuvem](./media/cloud-services-dotnet-get-started/newcsproj.png)
5. Quando vir a caixa de diálogo **Novo Projeto ASP.NET** para a função da Web, selecione o modelo MVC e clique em **Alterar Autenticação**.

    ![Alterar Autenticação](./media/cloud-services-dotnet-get-started/chgauth.png)
6. Na caixa de diálogo **Alterar Autenticação**, escolha **Sem Autenticação** e clique em **OK**.

    ![Sem Autenticação](./media/cloud-services-dotnet-get-started/noauth.png)
7. Na caixa de diálogo **Novo Projeto ASP.NET**, clique em **OK**.
8. No **Explorador de Soluções**, clique com o botão direito do rato na solução (não num dos projetos) e selecione **Adicionar – Novo Projeto**.
9. Na caixa de diálogo **Adicionar Novo Projeto**, selecione **Windows** em **Visual c#** no painel esquerdo e, em seguida, clique no modelo **Biblioteca de Classes**.  
10. Nomeie o projeto *ContosoAdsCommon* e clique em **OK**.

    Tem de referenciar o contexto do Entity Framework e o modelo de dados dos projetos da função Web e de trabalho. Como alternativa, pode definir as classes relacionadas a EF no projeto da função da Web e fazer referência a esse projeto no projeto da função de trabalho. Mas, na abordagem alternativa, o projeto da função de trabalho terá uma referência a assemblagens Web desnecessária.

### <a name="update-and-add-nuget-packages"></a>Atualizar e adicionar pacotes NuGet
1. Abra a caixa de diálogo **Gerir Pacotes NuGet** da solução.
2. Na parte superior da janela, selecione **Atualizações**.
3. Procure o pacote *WindowsAzure.Storage* e, se estiver na lista, selecione-o e selecione os projetos Web e de trabalho a atualizar e, em seguida, clique em **Atualizar**.

    A biblioteca de clientes do armazenamento é atualizada com mais frequência do que os modelos do projeto do Visual Studio, por isso, muitas vezes, a versão num projeto criado recentemente precisará ser atualizada.
4. Na parte superior da janela, selecione **Procurar**.
5. Localize o pacote NuGet *EntityFramework* e instale-o nos três projetos.
6. Localize o pacote NuGet *Microsoft.WindowsAzure.ConfigurationManager* e instale-o num projeto da função de trabalho.

### <a name="set-project-references"></a>Definir referências do projeto
1. No projeto ContosoAdsWeb, defina uma referência para o projeto ContosoAdsCommon. Clique com o botão direito do rato no projeto ContosoAdsWeb e, em seguida, em **Referências** - **Adicionar Referências**. Na caixa de diálogo **Gestor de Referências**, selecione **Solução – Projetos** no painel esquerdo, selecione **ContosoAdsCommon** e clique em **OK**.
2. No projeto ContosoAdsWorker, defina uma referência ao projeto ContosoAdsCommon.

    O ContosoAdsCommon conterá o modelo de dados e a classe de contexto do Entity Framework, que serão utilizados no front-end e back-end.
3. No projeto ContosoAdsWorker, defina uma referência para `System.Drawing`.

    Esta assemblagem é utilizada pelo back-end para converter imagens em miniaturas.

### <a name="configure-connection-strings"></a>Configurar cadeias de ligação
Nesta secção, deverá configurar o Armazenamento do Azure e as cadeias de ligação SQL para testar localmente. As instruções de implementação anteriormente descritas no tutorial explicam como configurar as cadeias de ligação para quando a aplicação é executada na nuvem.

1. No projeto ContosoAdsWeb, abra o ficheiro Web.config da aplicação e insira o seguinte elemento `connectionStrings` após o elemento `configSections`.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Se estiver a utilizar o Visual Studio 2015 ou superior, substitua “v11.0” por “MSSQLLocalDB”.
2. Guarde as alterações.
3. No projeto ContosoAdsCloudService, clique com o botão direito do rato em ContosoAdsWeb em **Funções** e, em seguida, clique em **Propriedades**.

    ![Propriedades da função](./media/cloud-services-dotnet-get-started/roleproperties.png)
4. Na **ContosoAdsWeb [função]** janela Propriedades, clique no **definições** separador e, em seguida, clique em **Adicionar definição**.

    Deixe **Configuração do Serviço** definida para **Todas as Configurações**.
5. Adicione uma definição denominada *StorageConnectionString*. Defina o **Tipo** para *ConnectionString* e o **Valor** para *UseDevelopmentStorage = true*.

    ![Nova cadeia de ligação](./media/cloud-services-dotnet-get-started/scall.png)
6. Guarde as alterações.
7. Siga o mesmo procedimento para adicionar uma cadeia de ligação de armazenamento nas propriedades da função ContosoAdsWorker.
8. Ainda na janela de propriedades **ContosoAdsWorker [Função]**, adicione outra cadeia de ligação:

   * Nome: ContosoAdsDbConnectionString
   * Tipo: String
   * Valor: Cole a mesma cadeia de ligação utilizada para o projeto de função da web. (O exemplo seguinte é para o Visual Studio 2013. Não se esqueça de alterar a Origem de Dados se copiar este exemplo e estiver a utilizar o Visual Studio 2015 ou superior.)

       ```
       Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;
       ```

### <a name="add-code-files"></a>Adicionar ficheiros de código
Nesta secção, deverá copiar os ficheiros de código da solução transferida para a solução nova. As secções seguintes irão mostrar e explicar as partes principais deste código.

Para adicionar ficheiros a um projeto ou a uma pasta, clique com o botão direito do rato no projeto ou na pasta e clique em **Adicionar** - **Item Existente**. Selecione os ficheiros desejados e clique em **Adicionar**. Caso lhe seja perguntado se pretende substituir os ficheiros existentes, clique em **Sim**.

1. No projeto ContosoAdsCommon, elimine o ficheiro *Class1.cs* e adicione no seu lugar os ficheiros *Ad.cs* e *ContosoAdscontext.cs* do projeto transferido.
2. No projeto ContosoAdsWeb, adicione os seguintes ficheiros do projeto transferido.

   * *Global.asax.cs*.  
   * Na *Views\Shared* pasta: *\_Layout.cshtml*.
   * Na *vistas \ início* pasta: *Index. cshtml*.
   * Na *controladores* pasta: *AdController.cs*.
   * Na pasta *Vistas\Anúncio* (crie a pasta primeiro): cinco ficheiros *.cshtml*.
3. No projeto ContosoAdsWorker, adicione *WorkerRole.cs* do projeto transferido.

Agora pode compilar e executar a aplicação conforme instruído anteriormente no tutorial. A aplicação utilizará a base de dados local e os recursos do emulador de armazenamento.

As secções seguintes explicam o código relacionado para trabalhar com o ambiente, blobs e filas do Azure. Este tutorial não explica como criar controladores do MVC e vistas utilizando andaimes, como escrever código do Entity Framework que funcione com bases de dados do SQL Server nem as noções básicas de programação assíncrona no ASP.NET 4.5. Para obter informações sobre estes tópicos, consulte os recursos seguintes:

* [Introdução ao MVC 5](https://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Introdução ao EF 6 e ao MVC 5](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc)
* [Introdução à programação assíncrona no .NET 4.5](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).

### <a name="contosoadscommon---adcs"></a>ContosoAdsCommon – Ad.cs
O ficheiro Ad.cs define uma enumeração de categorias de anúncio e uma classe de entidade POCO para as informações do anúncio.

```csharp
public enum Category
{
    Cars,
    [Display(Name="Real Estate")]
    RealEstate,
    [Display(Name = "Free Stuff")]
    FreeStuff
}

public class Ad
{
    public int AdId { get; set; }

    [StringLength(100)]
    public string Title { get; set; }

    public int Price { get; set; }

    [StringLength(1000)]
    [DataType(DataType.MultilineText)]
    public string Description { get; set; }

    [StringLength(1000)]
    [DisplayName("Full-size Image")]
    public string ImageURL { get; set; }

    [StringLength(1000)]
    [DisplayName("Thumbnail")]
    public string ThumbnailURL { get; set; }

    [DataType(DataType.Date)]
    [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
    public DateTime PostedDate { get; set; }

    public Category? Category { get; set; }
    [StringLength(12)]
    public string Phone { get; set; }
}
```

### <a name="contosoadscommon---contosoadscontextcs"></a>ContosoAdsCommon – ContosoAdsContext.cs
A classe ContosoAdsContext especifica que a classe Anúncio é utilizada numa coleção DbSet, que o Entity Framework armazenará numa SQL Database.

```csharp
public class ContosoAdsContext : DbContext
{
    public ContosoAdsContext() : base("name=ContosoAdsContext")
    {
    }
    public ContosoAdsContext(string connString)
        : base(connString)
    {
    }
    public System.Data.Entity.DbSet<Ad> Ads { get; set; }
}
```

A classe tem dois construtores. O primeiro é utilizado pelo projeto Web e especifica o nome de uma cadeia de ligação que está armazenada no ficheiro Web.config. O segundo construtor permite-lhe transmitir a cadeia de ligação atual utilizada pelo projeto de função de trabalho, uma vez que não tem um ficheiro Web.config. Vimos anteriormente onde foi armazenada esta cadeia de ligação e verá posteriormente como o código recupera a cadeia de ligação quando instanciar a classe DbContext.

### <a name="contosoadsweb---globalasaxcs"></a>ContosoAdsWeb – Global.asax.cs
O código que é chamado pelo método `Application_Start` cria um contentor de blob de *imagens* e uma fila de *imagens* se ainda não existirem. Isto garante que sempre que começar a utilizar uma nova conta do Storage ou começar a utilizar o emulador de armazenamento num novo computador, a fila e contentor de blob necessários serão criados automaticamente.

O código obtém acesso à conta do Storage utilizando a cadeia de ligação de armazenamento no ficheiro *.cscfg*.

```csharp
var storageAccount = CloudStorageAccount.Parse
    (RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
```

Em seguida, obtém uma referência para o contentor de blob de *imagens*, cria o contentor, se ainda não existir, e define as permissões de acesso no novo contentor. Por predefinição, os novos contentores permitem apenas que os clientes com credenciais de conta do Storage acedam aos blobs. O site necessita de blobs para ser público, para poder apresentar imagens com URLs que apontem para os blobs de imagens.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var imagesBlobContainer = blobClient.GetContainerReference("images");
if (imagesBlobContainer.CreateIfNotExists())
{
    imagesBlobContainer.SetPermissions(
        new BlobContainerPermissions
        {
            PublicAccess =BlobContainerPublicAccessType.Blob
        });
}
```

Um código semelhante obtém uma referência para a fila de *imagens* e cria uma nova fila. Neste caso, não é necessário alterar as permissões.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
var imagesQueue = queueClient.GetQueueReference("images");
imagesQueue.CreateIfNotExists();
```

### <a name="contosoadsweb---layoutcshtml"></a>ContosoAdsWeb – \_Layout.cshtml
O ficheiro *_Layout.cshtml* define o nome da aplicação no cabeçalho e no rodapé e cria uma entrada de menu “Anúncios”.

### <a name="contosoadsweb---viewshomeindexcshtml"></a>ContosoAdsWeb – Views\Home\Index.cshtml
O ficheiro *Views\Home\Index.cshtml* apresenta ligações das categorias na página inicial. As ligações passam o valor inteiro da enumeração `Category` numa variável querystring para a página Índice de Anúncios.

```razor
<li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
<li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
<li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
<li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>
```

### <a name="contosoadsweb---adcontrollercs"></a>ContosoAdsWeb – AdController.cs
No ficheiro *AdController.cs*, o construtor chama o método `InitializeStorage` para criar objetos de Biblioteca de Clientes do Armazenamento do Azure que fornecem uma API para trabalhar com blobs e filas.

Em seguida, o código obtém uma referência para o contentor de blob de *imagens*, conforme mostrado anteriormente no *Global.asax.cs*. Ao fazer isso, define uma [política de repetição](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) predefinida adequada para uma aplicação Web. A política de repetição de término exponencial predefinida poderá suspender a aplicação Web durante mais de um minuto em tentativas repetidas quando ocorrer um erro transitório. A política de repetição especificada aqui aguarda três segundos após cada tentativa (até três tentativas).

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesBlobContainer = blobClient.GetContainerReference("images");
```

Um código semelhante obtém uma referência para a fila de *imagens*.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesQueue = queueClient.GetQueueReference("images");
```

A maior parte do código do controlador é típica para trabalhar com um modelo de dados do Entity Framework utilizando uma classe DbContext. Uma exceção é o método HttpPost `Create`, que carrega um ficheiro e guarda-o no Blob Storage. A conversão de modelos fornece um objeto [HttpPostedFileBase](https://msdn.microsoft.com/library/system.web.httppostedfilebase.aspx) para o método.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Create(
    [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
    HttpPostedFileBase imageFile)
```

Se o utilizador tiver selecionado um ficheiro para carregar, o código carrega o ficheiro, guarda-o num blob e atualiza o registo da base de dados do Anúncio com um URL que aponta para o blob.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    blob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = blob.Uri.ToString();
}
```

O código que suporta o carregamento está no método `UploadAndSaveBlobAsync`. Cria um nome de GUID para o blob, carrega e guarda o ficheiro e devolve uma referência para o blob guardado.

```csharp
private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
{
    string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
    CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
    using (var fileStream = imageFile.InputStream)
    {
        await imageBlob.UploadFromStreamAsync(fileStream);
    }
    return imageBlob;
}
```

Depois do método HttpPost `Create` carregar um blob e atualizar a base de dados, cria uma mensagem de fila para informar esse processo de back-end de que uma imagem está pronta para conversão para uma miniatura.

```csharp
string queueMessageString = ad.AdId.ToString();
var queueMessage = new CloudQueueMessage(queueMessageString);
await queue.AddMessageAsync(queueMessage);
```

O código do método HttpPost `Edit` é semelhante, à exceção de que se o utilizador selecionar um novo ficheiro de imagem, todos os blobs existentes terão de ser eliminados.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    await DeleteAdBlobsAsync(ad);
    imageBlob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = imageBlob.Uri.ToString();
}
```

O exemplo seguinte mostra o código que elimina os blobs quando elimina um anúncio.

```csharp
private async Task DeleteAdBlobsAsync(Ad ad)
{
    if (!string.IsNullOrWhiteSpace(ad.ImageURL))
    {
        Uri blobUri = new Uri(ad.ImageURL);
        await DeleteAdBlobAsync(blobUri);
    }
    if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
    {
        Uri blobUri = new Uri(ad.ThumbnailURL);
        await DeleteAdBlobAsync(blobUri);
    }
}
private static async Task DeleteAdBlobAsync(Uri blobUri)
{
    string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
    CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
    await blobToDelete.DeleteAsync();
}
```

### <a name="contosoadsweb---viewsadindexcshtml-and-detailscshtml"></a>ContosoAdsWeb – Views\Ad\Index.cshtml e Details.cshtml
O ficheiro *Index.cshtml* apresenta miniaturas com os outros dados do anúncio.

```razor
<img src="@Html.Raw(item.ThumbnailURL)" />
```

O ficheiro *Details.cshtml* apresenta a imagem de tamanho completo.

```razor
<img src="@Html.Raw(Model.ImageURL)" />
```

### <a name="contosoadsweb---viewsadcreatecshtml-and-editcshtml"></a>ContosoAdsWeb – Views\Ad\Create.cshtml e Edit.cshtml
Os ficheiros *Create.cshtml* e *Edit.cshtml* especificam a codificação de formulário que permite que o controlador obtenha o objeto `HttpPostedFileBase`.

```razor
@using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))
```

Um elemento `<input>` indica ao browser para fornecer uma caixa de diálogo de seleção de ficheiros.

```razor
<input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />
```

### <a name="contosoadsworker---workerrolecs---onstart-method"></a>ContosoAdsWorker – WorkerRole.cs – método OnStart
O ambiente da função de trabalho do Azure chama o método `OnStart` na classe `WorkerRole` quando a função de trabalho está a iniciar e chama o método `Run` quando o método `OnStart` terminar.

O método `OnStart` obtém a cadeia de ligação da base de dados no ficheiro *.cscfg* e passa-a para a classe DbContext do Entity Framework. O fornecedor SQLClient é utilizado por predefinição, para que o fornecedor não tenha de ser especificado.

```csharp
var dbConnString = CloudConfigurationManager.GetSetting("ContosoAdsDbConnectionString");
db = new ContosoAdsContext(dbConnString);
```

Depois disso, o método obtém uma referência para a conta de armazenamento e cria o contentor de blobs e a fila, caso não existam. O código é semelhante ao que já vimos no método `Application_Start` da função da Web.

### <a name="contosoadsworker---workerrolecs---run-method"></a>ContosoAdsWorker - WorkerRole.cs - método Run
O método `Run` é chamado quando o método `OnStart` termina o trabalho de inicialização. O método executa um ciclo infinito que controla a existência de novas mensagens de fila e processa-as quando chegarem.

```csharp
public override void Run()
{
    CloudQueueMessage msg = null;

    while (true)
    {
        try
        {
            msg = this.imagesQueue.GetMessage();
            if (msg != null)
            {
                ProcessQueueMessage(msg);
            }
            else
            {
                System.Threading.Thread.Sleep(1000);
            }
        }
        catch (StorageException e)
        {
            if (msg != null && msg.DequeueCount > 5)
            {
                this.imagesQueue.DeleteMessage(msg);
            }
            System.Threading.Thread.Sleep(5000);
        }
    }
}
```

Após cada iteração do ciclo, se não for encontrada nenhuma mensagem de fila, o programa permanecerá suspenso durante um segundo. Isto impede que a função de trabalho incorra em custos excessivos de tempo de CPU e de transações de armazenamento. A Equipa de Consultadora dos Clientes da Microsoft conta uma história de um programador, que se esqueceu de incluir isto, implementou para produção e partiu para férias. Quando regressou, o seu esquecimento custou-lhe mais do que as férias.

Por vezes, o conteúdo de uma mensagem de fila causa um erro no processamento. Esta mensagem é designada *mensagem não processável*, e se acabou de registar um erro e reiniciou o ciclo, poderá ter de processar essa mensagem vezes sem conta.  Por conseguinte, o bloco catch inclui uma instrução Se que verifica o número de ocorrências que a aplicação tentou processar a mensagem atual e, se o número ultrapassar as 5 tentativas, a mensagem é eliminada da fila.

`ProcessQueueMessage` é chamado quando é encontrada uma mensagem de fila.

```csharp
private void ProcessQueueMessage(CloudQueueMessage msg)
{
    var adId = int.Parse(msg.AsString);
    Ad ad = db.Ads.Find(adId);
    if (ad == null)
    {
        throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", adId.ToString()));
    }

    CloudBlockBlob inputBlob = this.imagesBlobContainer.GetBlockBlobReference(ad.ImageURL);

    string thumbnailName = Path.GetFileNameWithoutExtension(inputBlob.Name) + "thumb.jpg";
    CloudBlockBlob outputBlob = this.imagesBlobContainer.GetBlockBlobReference(thumbnailName);

    using (Stream input = inputBlob.OpenRead())
    using (Stream output = outputBlob.OpenWrite())
    {
        ConvertImageToThumbnailJPG(input, output);
        outputBlob.Properties.ContentType = "image/jpeg";
    }

    ad.ThumbnailURL = outputBlob.Uri.ToString();
    db.SaveChanges();

    this.imagesQueue.DeleteMessage(msg);
}
```

Este código lê a base de dados para obter o URL da imagem, converte a imagem numa miniatura, guarda a miniatura num blob, atualiza a base de dados com o URL do blob de miniaturas e elimina a mensagem da fila.

> [!NOTE]
> O código no método `ConvertImageToThumbnailJPG` utiliza classes no espaço de nomes System.Drawing por uma questão de simplicidade. No entanto, as classes neste espaço de nomes foram concebidas para utilização com o Windows Forms. Não são suportadas para utilização num serviço Windows ou ASP.NET. Para obter mais informações sobre as opções de processamento de imagens, consulte [Geração de Imagens Dinâmicas](https://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx) e [Redimensionamento de Imagens Profundas](https://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na).
>
>

## <a name="troubleshooting"></a>Resolução de problemas
No caso de algo não funcionar enquanto está a seguir as instruções deste tutorial, apresentamos a seguir alguns erros comuns e como resolvê-los.

### <a name="serviceruntimeroleenvironmentexception"></a>ServiceRuntime.RoleEnvironmentException
O objeto `RoleEnvironment` é fornecido pelo Azure quando executa uma aplicação no Azure ou quando é executada localmente utilizando o emulador de computação do Azure.  Se ocorrer este erro quando a aplicação estiver a ser executada localmente, certifique-se de que definiu o projeto ContosoAdsCloudService como o projeto de arranque. Isto configura o projeto para ser executado utilizando o emulador de computação do Azure.

Uma das coisas para as quais a aplicação utiliza o RoleEnvironment do Azure é para obter os valores da cadeia de ligação que estão armazenados nos ficheiros *.cscfg*. Assim, outra causa desta exceção é uma cadeia de ligação em falta. Certifique-se de que criou a definição StorageConnectionString para ambas as configurações Nuvem e Local no projeto ContosoAdsWeb e de que criou ambas as cadeias de ligação para ambas as configurações que criou no projeto ContosoAdsWorker. Se fizer uma pesquisa **Localizar Tudo** para StorageConnectionString na solução toda, deve vê-la 9 vezes nos 6 ficheiros.

### <a name="cannot-override-to-port-xxx-new-port-below-minimum-allowed-value-8080-for-protocol-http"></a>Não é possível substituir a porta xxx. Nova porta abaixo do valor mínimo permitido 8080 para o protocolo http
Experimente alterar o número da porta utilizado pelo projeto Web. Clique com o botão direito do rato no projeto ContosoAdsWeb e, em seguida, clique em **Propriedades**. Clique no separador **Web** e altere o número da porta na definição **URL do Projeto**.

Para obter outra alternativa para resolver o problema, consulte a secção seguinte.

### <a name="other-errors-when-running-locally"></a>Outros erros ao executar localmente
Por predefinição, os novos projetos do serviço em nuvem utilizam o emulador de computação do Azure expresso para simular o ambiente do Azure. Esta é uma versão simples do emulador de computação completo e, em algumas condições, o emulador completo funcionará enquanto a versão expresso não.  

Para alterar o projeto para utilizar o emulador completo, clique com o botão direito do rato no projeto ContosoAdsCloudService e, em seguida, clique em **Propriedades**. Na janela **Propriedades**, clique no separador **Web** e, em seguida, no botão de opção **Utilizar Emulador Completo**.

Para executar a aplicação com o emulador completo, terá de abrir o Visual Studio com privilégios de administrador.

## <a name="next-steps"></a>Passos seguintes
A aplicação Anúncios da Contoso foi intencionalmente mantida simples para um tutorial de introdução. Por exemplo, não implementa a [inserção de dependências](https://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection) nem o [repositório e unidade de padrões de trabalho](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), não [utiliza uma interface para registo](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log), não utiliza as [Migrações do EF Code First](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) para gerir as alterações dos modelos de dados nem [Resiliência da Ligação do EF](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) para gerir erros de rede transitórios e etc.

Apresentamos a seguir algumas aplicações de exemplo do serviço em nuvem que demonstram mais práticas de codificação do mundo real, das menos complexas à mais complexas:

* [PhluffyFotos](https://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31). É semelhante no conceito aos Anúncios da Contoso, mas implementa mais funcionalidades e mais práticas de codificação do mundo real.
* [Aplicação de Várias Camadas do Serviço em Nuvem do Azure com Tabelas, Filas e Blobs](https://code.msdn.microsoft.com/windowsazure/Windows-Azure-Multi-Tier-eadceb36). Apresenta as tabelas do Storage do Azure, bem como os blobs e as filas. Com base na versão anterior do Azure SDK para .NET, serão necessárias algumas modificações para trabalhar com a versão atual.

Para obter informações gerais sobre como desenvolver para a nuvem, consulte [Compilar Aplicações na Nuvem Reais com o Azure](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction).

Para obter uma introdução em vídeo aos padrões e melhores práticas do Storage do Azure, consulte [Armazenamento do Microsoft Azure – Novidades, Melhores Práticas e Padrões](https://channel9.msdn.com/Events/Build/2014/3-628).

Para obter mais informações, consulte os seguintes recursos:

* [Parte de serviços Cloud do Azure 1: Introdução](https://justazure.com/microsoft-azure-cloud-services-part-1-introduction/)
* [Como gerir Serviços Cloud](cloud-services-how-to-manage-portal.md)
* [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/)
* [Como escolher um fornecedor de serviços cloud](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/)
