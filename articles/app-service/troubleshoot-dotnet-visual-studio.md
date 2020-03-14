---
title: Resolver problemas com o Visual Studio
description: Aprenda a resolver problemas com uma aplicação do App Service utilizando ferramentas remotas de depuração, rastreio e registo que são incorporadas no Visual Studio 2013.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: seodec18
ms.openlocfilehash: 3305cfb81980984574961b2a84a056f5d1879ead
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280056"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Troubleshoot uma aplicação no Azure App Service usando o Visual Studio
## <a name="overview"></a>Descrição geral
Este tutorial mostra como usar as ferramentas do Estúdio Visual para ajudar a desinpurar uma aplicação no [App Service,](https://go.microsoft.com/fwlink/?LinkId=529714)executando remotamente o [modo de depuração](https://docs.microsoft.com/visualstudio/debugger/) ou visualizando registos de aplicações e registos de servidores web.

Irá aprender a:

* Quais as funções de gestão de aplicações disponíveis no Estúdio Visual.
* Como utilizar a vista remota do Estúdio Visual para fazer mudanças rápidas numa aplicação remota.
* Como executar o modo de depuração remotamente enquanto um projeto está em execução em Azure, tanto para uma aplicação como para um WebJob.
* Como criar registos de rastreio de aplicações e vê-los enquanto a aplicação os está a criar.
* Como visualizar os registos do servidor web, incluindo mensagens de erro detalhadas e rastreio de pedidos falhados.
* Como enviar registos de diagnóstico para uma conta de Armazenamento Azure e vê-los lá.

Se tiver o Visual Studio Ultimate, também pode utilizar o [IntelliTrace](/visualstudio/debugger/intellitrace) para depurar. O IntelliTrace não está coberto neste tutorial.

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial trabalha com o ambiente de desenvolvimento, o projeto web e a app App Service que criou na [Create uma aplicação ASP.NET no Azure App Service.](app-service-web-get-started-dotnet-framework.md) Para as secções WebJobs, você precisará da aplicação que você cria em [Get Started com o Azure WebJobs SDK][GetStartedWJ].

As amostras de código mostradas C# neste tutorial são para uma aplicação web MVC, mas os procedimentos de resolução de problemas são os mesmos para aplicações de Formas Visuais básicas e Web.

O tutorial assume que está a usar o Visual Studio 2019. 

A função de registos de streaming funciona apenas para aplicações que visam o .NET Framework 4 ou posterior.

## <a name="sitemanagement"></a>Configuração e gestão de aplicativos
O Visual Studio fornece acesso a um subconjunto das funções de gestão de aplicações e definições de configuração disponíveis no [portal Azure](https://go.microsoft.com/fwlink/?LinkId=529715). Nesta secção, verá o que está disponível utilizando o **Server Explorer**. Para ver as mais recentes funcionalidades de integração do Azure, experimente também o **Cloud Explorer.** Pode abrir ambas as janelas do menu **'Ver'.**

1. Se ainda não tiver assinado no Azure no Estúdio Visual, clique no **Azure** e selecione Connect to **Microsoft Azure Subscription** in **Server Explorer**.

    Uma alternativa é instalar um certificado de gestão que permita o acesso à sua conta. Se optar por instalar um certificado, clique no nó **Azure** no **Server Explorer**e, em seguida, selecione **'Gerir e Filtrar Assinaturas'** no menu de contexto. Na caixa de diálogo **de assinaturas Manage Microsoft Azure,** clique no separador **Certificados** e, em seguida, clique **em Importar**. Siga as instruções para descarregar e, em seguida, importe um ficheiro de subscrição (também chamado de ficheiro *.publishsettings)* para a sua conta Azure.

   > [!NOTE]
   > Se descarregar um ficheiro de subscrição, guarde-o para uma pasta fora dos diretórios do código fonte (por exemplo, na pasta Downloads) e, em seguida, elimine-o assim que a importação estiver concluída. Um utilizador malicioso que obtém acesso ao ficheiro de subscrição pode editar, criar e eliminar os seus serviços Azure.
   >
   >

    Para obter mais informações sobre a ligação aos recursos do Azure a partir do Estúdio Visual, consulte [Gerir Contas, Subscrições e Funções Administrativas.](https://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert)
2. No **Server Explorer,** expanda **o Azure** e expanda o Serviço de **Aplicações.**
3. Expanda o grupo de recursos que inclui a app que criou na [Create a ASP.NET app no Azure App Service](app-service-web-get-started-dotnet-framework.md), e, em seguida, clique no nó da aplicação e clique em **Definições de Visualização**.

    ![Ver Definições no Explorador do Servidor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    O separador **Azure Web App** aparece e pode ver lá as tarefas de gestão e configuração de aplicações que estão disponíveis no Estúdio Visual.

    ![Janela da aplicação web azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    Neste tutorial, você usará a exploração madeireira e rastreio de quedas. Também utilizará depuração remota, mas utilizará um método diferente para o ativar.

    Para obter informações sobre as definições de aplicações e caixas de cordas de ligação nesta janela, consulte o Serviço de [Aplicações Azure: Como funcionam as cordas de aplicação e as cordas](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)de ligação .

    Se quiser realizar uma tarefa de gestão de aplicações que não possa ser feita nesta janela, clique **em Open in Management Portal** para abrir uma janela de navegador para o portal Azure.

## <a name="remoteview"></a>Aceder a ficheiros de aplicativos no Server Explorer
Normalmente, você implementa um projeto web com a bandeira `customErrors` no ficheiro Web.config definido para `On` ou `RemoteOnly`, o que significa que você não recebe uma mensagem de erro útil quando algo corre mal. Para muitos erros, tudo o que obtém é uma página como uma das seguintes:

**Erro do servidor na aplicação '/':**

![Página de erro inútil](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Ocorreu um erro:**

![Página de erro inútil](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**O site não pode exibir a página**

![Página de erro inútil](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Frequentemente, a forma mais fácil de encontrar a causa do erro é ativar mensagens de erro detalhadas, o que a primeira das imagens anteriores explica como fazer. Isto requer uma alteração no ficheiro Web.config implantado. Pode editar o ficheiro *Web.config* no projeto e reimplantar o projeto, ou criar uma [transformação web.config](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) e implementar uma construção de depuração, mas há uma maneira mais rápida: no **Solution Explorer,** pode visualizar e editar ficheiros diretamente na aplicação remota utilizando a funcionalidade de *visualização remota.*

1. No **Server Explorer,** expanda o **Azure,** expanda o **App Service,** expanda o grupo de recursos em que a sua aplicação está localizada e, em seguida, expanda o nó para a sua app.

    Vê sosdes que lhe dão acesso aos ficheiros de conteúdo da aplicação e ficheiros de registo.
2. Expanda o nó **dos Ficheiros** e clique duas vezes no ficheiro *Web.config.*

    ![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    O Visual Studio abre o ficheiro Web.config a partir da aplicação remota e mostra [Remote] ao lado do nome do ficheiro na barra de título.
3. Adicione a seguinte linha ao elemento `system.web`:

    `<customErrors mode="Off"></customErrors>`

    ![Editar Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Atualize o navegador que está a mostrar a mensagem de erro inútil, e agora recebe uma mensagem de erro detalhada, como o seguinte exemplo:

    ![Mensagem de erro detalhada](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (O erro mostrado foi criado adicionando a linha mostrada a vermelho a *Views\Home\Index.cshtml*.)

Editar o ficheiro Web.config é apenas um exemplo de cenários em que a capacidade de ler e editar ficheiros na aplicação do Serviço de Aplicações facilita a resolução de problemas.

## <a name="remotedebug"></a>Aplicativos de depuração remoto
Se a mensagem de erro detalhada não fornecer informações suficientes e não conseguir recriar o erro localmente, outra forma de resolver problemas é correr remotamente no modo de depuração. Pode definir pontos de rutura, manipular a memória diretamente, passar pelo código e até alterar o código.

A depuração remota não funciona nas edições express do Visual Studio.

Esta secção mostra como depurar remotamente o projeto que cria na [Create a ASP.NET app no Azure App Service](app-service-web-get-started-dotnet-framework.md).

1. Abra o projeto web que criou na [Create a ASP.NET app no Azure App Service.](app-service-web-get-started-dotnet-framework.md)

1. *Controladores abertos\HomeController.cs*.

1. Elimine o método `About()` e insira o seguinte código no seu lugar.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. [Coloque um ponto de rutura](https://docs.microsoft.com/visualstudio/debugger/) na linha `ViewBag.Message`.

1. No **Solution Explorer,** clique no projeto e clique em **Publicar**.

1. Na lista de drop-down do **Profile,** selecione o mesmo perfil que utilizou na [Create a ASP.NET app no Azure App Service](app-service-web-get-started-dotnet-framework.md). Em seguida, clique em Definições.

1. No diálogo **Publicar,** clique no separador **Definições** e, em seguida, mude a **Configuração** para **Debug**, e, em seguida, clique em **Guardar**.

    ![Publicar em modo dedepura](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Clique em **Publicar**. Depois de terminar a implementação e o seu navegador abrir para o URL Azure da sua aplicação, feche o navegador.

1. No **Server Explorer,** clique na sua aplicação e clique em **Attach Debugger**.

    ![Anexar o debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    O navegador abre automaticamente para a sua página inicial em funcionamento em Azure. Pode ter de esperar 20 segundos enquanto o Azure configura o servidor para depuração. Este atraso só acontece na primeira vez que corre em modo dedepura numa aplicação num período de 48 horas. Quando se volta a depurar no mesmo período, não há atraso.

    > [!NOTE] 
    > Se tiver algum problema em iniciar o desordeiro, tente fazê-lo utilizando o **Cloud Explorer** em vez do **Server Explorer**.
    >

1. Clique no menu.

    O Visual Studio para no ponto de rutura, e o código está a funcionar em Azure, não no computador local.

1. Pairar sobre a variável `currentTime` para ver o valor do tempo.

    ![Ver variável no modo de depuração em funcionamento em Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    O tempo que vê é o tempo do servidor Azure, que pode estar num fuso horário diferente do seu computador local.

1. Introduza um novo valor para a variável `currentTime`, como "Agora a correr em Azure".

1. Pressione F5 para continuar a correr.

     A página Sobre em execução em Azure exibe o novo valor que introduziu na variável actualTime.

     ![Sobre página com novo valor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a>WebJobs de depuração remoto
Esta secção mostra como depurar remotamente o projeto e a aplicação que cria no [Get Started com o Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

As funcionalidades apresentadas nesta secção estão disponíveis apenas no Visual Studio 2013 com o Update 4 ou mais tarde.

A depuração remota só funciona com WebJobs contínuos. WebJobs programado e a pedido não suporta mato.

1. Abra o projeto web que criou no [Get Started com o Azure WebJobs SDK][GetStartedWJ].

2. No projeto ContosoAdsWebJob, abra *Functions.cs.*

3. [Detete um ponto de rutura](https://docs.microsoft.com/visualstudio/debugger/) na primeira declaração no método `GnerateThumbnail`.

    ![Definir ponto de rutura](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. No **Solution Explorer,** clique no projeto web (não no projeto WebJob) e clique em **Publicar**.

5. Na lista de drop-down do **Profile,** selecione o mesmo perfil que utilizou no [Get Started com o Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Clique no separador **Definições** e mude de **configuração** para **Debug,** e depois clique em **Publicar**.

    O Visual Studio implementa os projetos web e WebJob, e o seu navegador abre-se para o URL Azure da sua aplicação.

7. No **Server Explorer,** expanda **o Azure > App Service > o seu grupo de recursos > a sua app > WebJobs > Continuous,** e depois clique à direita **ContosoAdsWebJob**.

8. Clique **em prender debugger**.

    ![Anexar o debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    O navegador abre automaticamente para a sua página inicial em funcionamento em Azure. Pode ter de esperar 20 segundos enquanto o Azure configura o servidor para depuração. Este atraso só acontece na primeira vez que corre em modo dedepura numa aplicação num período de 48 horas. Quando se volta a depurar no mesmo período, não há atraso.

9. No navegador web que é aberto à página inicial dos Anúncios Contoso, crie um novo anúncio.

    A criação de um anúncio faz com que seja criada uma mensagem de fila, que é captada pelo WebJob e processada. Quando o WebJobs SDK chama a função para processar a mensagem de fila, o código atinge o seu ponto de rutura.

10. Quando o debugger quebra no seu ponto de rutura, você pode examinar e alterar valores variáveis enquanto o programa está executando a nuvem. Na ilustração seguinte, o debugger mostra o conteúdo do objeto blobInfo que foi passado para o método `GenerateThumbnail`.

     ![objeto blobInfo em debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Pressione F5 para continuar a correr.

     O método `GenerateThumbnail` termina criando a miniatura.

12. No navegador, refresque a página Do Índice e veja a miniatura.

13. No Estúdio Visual, prima SHIFT+F5 para parar de depurar.

14. No **Server Explorer,** clique à direita no nó ContosoAdsWebJob e clique em **Ver Dashboard**.

15. Inscreva-se com as suas credenciais Azure e, em seguida, clique no nome WebJob para ir à página para o seu WebJob.

     ![Clique em ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     O Dashboard mostra que a função `GenerateThumbnail` executada recentemente.

     (Da próxima vez que clicar no **View Dashboard,** não tem de iniciar sessão e o navegador vai diretamente para a página do seu WebJob.)

16. Clique no nome da função para ver detalhes sobre a execução da função.

     ![Detalhes da função](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Se a sua função [escreveu registos,](https://github.com/Azure/azure-webjobs-sdk/wiki)pode clicar em **ToggleOutput** para os ver.

## <a name="notes-about-remote-debugging"></a>Notas sobre depuração remota

* Não é aconselhável funcionar em modo de depuração em produção. Se a sua aplicação de produção não for dimensionada para várias instâncias do servidor, a depuração impede que o servidor web responda a outros pedidos. Se tiver várias instâncias de servidor web, quando se liga ao desbugger, obtém uma instância aleatória, e não tem como garantir que os pedidos de navegador subsequentes vão para a mesma instância. Além disso, normalmente não implementa uma construção de depuração para a produção, e otimizações de compiladores para construções de lançamento podem tornar impossível mostrar o que está a acontecer linha a linha no seu código fonte. Para problemas de resolução de problemas de produção, o seu melhor recurso é o rastreio de aplicações e registos de servidores web.
* Evite longas paragens nos pontos de rutura quando depurar remotamente. O Azure trata um processo que é interrompido por mais de alguns minutos como um processo sem resposta, e desliga-o.
* Enquanto está a depurar, o servidor está a enviar dados para o Visual Studio, o que pode afetar as cargas de largura de banda. Para obter informações sobre as tarifas de largura de banda, consulte [o Preço Do Azure](https://azure.microsoft.com/pricing/calculator/).
* Certifique-se de que o atributo `debug` do elemento `compilation` no ficheiro *Web.config* está definido como verdadeiro. É definido para verdade por padrão quando publica uma configuração de construção de depuração.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Se descobrires que o debugger não entra no código que queres depurar, talvez tenhas de mudar a definição do Just My Code.  Para mais informações, consulte [Especifique se deve depurar apenas](https://docs.microsoft.com/visualstudio/debugger/just-my-code)o código do utilizador usando just o meu código em estúdio visual .
* Um temporizador começa no servidor quando ativa a função de depuração remota e, após 48 horas, a funcionalidade é desligada automaticamente. Este limite de 48 horas é feito por razões de segurança e desempenho. Pode facilmente voltar a ligar a funcionalidade quantas vezes quiser. Recomendamos deixá-lo desativado quando não estiver a depurar ativamente.
* Pode ligar manualmente o debugger a qualquer processo, não apenas o processo da aplicação (w3wp.exe). Para obter mais informações sobre como usar o modo dedepura no Estúdio Visual, consulte [Debugging no Visual Studio](/visualstudio/debugger/debugging-in-visual-studio).

## <a name="logsoverview"></a>Visão geral dos registos de diagnóstico
Uma aplicação ASP.NET que funciona numa aplicação do App Service pode criar os seguintes tipos de registos:

* **Registos de rastreio de aplicação**<br/>
  A aplicação cria estes registos chamando métodos do [Sistema.Diagnósticos.Trace](/dotnet/api/system.diagnostics.trace) class.
* **Registos de servidores web**<br/>
  O servidor web cria uma entrada de registo para cada pedido http para a aplicação.
* **Registos de mensagens de erro detalhados**<br/>
  O servidor web cria uma página HTML com algumas informações adicionais para pedidos DE HTTP falhados (pedidos que resultam em código de estado 400 ou superior).
* **Registos de rastreio de pedidos falhados**<br/>
  O servidor web cria um ficheiro XML com informações detalhadas de rastreio para pedidos HTTP falhados. O servidor web também fornece um ficheiro XSL para formatar o XML num browser.

O registo afeta o desempenho da aplicação, pelo que o Azure lhe dá a capacidade de ativar ou desativar cada tipo de registo, conforme necessário. Para os registos de aplicação, pode especificar que apenas devem ser escritos registos acima de um determinado nível de gravidade. Quando cria uma nova aplicação, por padrão, toda a exploração madeireira está desativada.

Os registos são escritos para ficheiros numa pasta *LogFiles* no sistema de ficheiros da sua aplicação e são acessíveis via FTP. Os registos e registos de aplicações do servidor web também podem ser escritos numa conta de Armazenamento Azure. Pode reter um maior volume de registos numa conta de armazenamento do que é possível no sistema de ficheiros. Está limitado a um máximo de 100 megabytes de registos quando utiliza o sistema de ficheiros. (Os registos do sistema de ficheiros são apenas para retenção a curto prazo. O Azure elimina ficheiros de registo antigos para dar espaço aos novos após o limite.)  

## <a name="apptracelogs"></a>Criar e ver registos de rastreios de aplicações
Nesta secção, você faz as seguintes tarefas:

* Adicione declarações de rastreio ao projeto web que criou em [Get started com Azure e ASP.NET](app-service-web-get-started-dotnet-framework.md).
* Veja os registos quando executar o projeto localmente.
* Veja os registos como são gerados pela aplicação em funcionamento em Azure.

Para obter informações sobre como criar registos de aplicações no WebJobs, consulte como trabalhar com o armazenamento de [fila Azure utilizando o WebJobs SDK - Como escrever registos](https://github.com/Azure/azure-webjobs-sdk/wiki). As seguintes instruções para visualizar registos e controlar a forma como são armazenados em Azure aplicam-se também aos registos de aplicações criados pela WebJobs.

### <a name="add-tracing-statements-to-the-application"></a>Adicione declarações de rastreio à aplicação
1. Controladores *abertos\HomeController.cs*, e substitua os métodos `Index`, `About`e `Contact` com o seguinte código, a fim de adicionar declarações `Trace` e uma declaração `using` para `System.Diagnostics`:

    ```csharp
    public ActionResult Index()
    {
        Trace.WriteLine("Entering Index method");
        ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
        Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Index method");
        return View();
    }
    
    public ActionResult About()
    {
        Trace.WriteLine("Entering About method");
        ViewBag.Message = "Your app description page.";
        Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
        Trace.WriteLine("Leaving About method");
        return View();
    }
    
    public ActionResult Contact()
    {
        Trace.WriteLine("Entering Contact method");
        ViewBag.Message = "Your contact page.";
        Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Contact method");
        return View();
    }        
    ```

1. Adicione uma declaração `using System.Diagnostics;` ao topo do ficheiro.

### <a name="view-the-tracing-output-locally"></a>Veja a saída de rastreio localmente
1. Pressione F5 para executar a aplicação em modo dedepura.

    O ouvinte de rastreio predefinido escreve todos os vestígios para a janela **de saída,** juntamente com outra saída de Debug. A ilustração seguinte mostra a saída a partir das declarações de vestígios que adicionou ao método `Index`.

    ![Rastreio na janela de Debug](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Os seguintes passos mostram como visualizar a saída de vestígios numa página web, sem compilar em modo dedepura.
1. Abra o ficheiro Web.config da aplicação (o que está localizado na pasta do projeto) e adicione um elemento `<system.diagnostics>` na extremidade do ficheiro pouco antes do elemento `</configuration>` de fecho:

    ``` xml
    <system.diagnostics>
    <trace>
      <listeners>
        <add name="WebPageTraceListener"
            type="System.Web.WebPageTraceListener,
            System.Web,
            Version=4.0.0.0,
            Culture=neutral,
            PublicKeyToken=b03f5f7f11d50a3a" />
      </listeners>
    </trace>
    </system.diagnostics>
    ```

O `WebPageTraceListener` permite-lhe visualizar a saída de vestígios navegando para `/trace.axd`.
1. Adicione um <a href="https://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">elemento de traço</a> sob `<system.web>` no ficheiro Web.config, como o seguinte exemplo:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Prima CTRL+F5 para executar a aplicação.
1. Na barra de endereços da janela do navegador, adicione *trace.axd* ao URL e, em seguida, prima Enter (o URL é semelhante a `http://localhost:53370/trace.axd`).
1. Na página 'Rastreio de **Aplicação',** clique em **Ver Detalhes** na primeira linha (não na linha BrowserLink).

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    A página **Dedados** de Pedidos aparece e na secção **Informação** de Rastreio vê a saída a partir das declarações de vestígios que adicionou ao método `Index`.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Por predefinição, `trace.axd` só está disponível localmente. Se quiser disponibilizá-lo a partir de uma aplicação remota, pode adicionar `localOnly="false"` ao elemento `trace` no ficheiro *Web.config,* como mostra o seguinte exemplo:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    No entanto, permitir `trace.axd` numa aplicação de produção não é recomendado por razões de segurança. Nas seguintes secções, verá uma forma mais fácil de ler registos de rastreio numa aplicação do App Service.

### <a name="view-the-tracing-output-in-azure"></a>Veja a saída de rastreio em Azure
1. No **Solution Explorer,** clique no projeto web e clique em **Publicar**.
2. Na caixa de diálogo **Publicar Web,** clique em **Publicar**.

    Depois de o Visual Studio publicar a sua atualização, abre uma janela de navegador para a sua página inicial (assumindo que não limpou o URL de **Destino** no separador **Ligação).**
3. No **Server Explorer,** clique na sua aplicação e selecione **'Ver Registos de Streaming'.**

    ![Ver Registos de Streaming no menu de contexto](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    A janela **Saída** mostra que está ligado ao serviço de streaming de log e adiciona uma linha de notificação a cada minuto que passa sem um registo para exibir.

    ![Ver Registos de Streaming no menu de contexto](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. Na janela do navegador que mostra a página inicial da sua aplicação, clique em **Contato**.

    Dentro de alguns segundos, a saída do traço de nível de erro que adicionou ao método `Contact` aparece na janela **de saída.**

    ![Traço de erro na janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    O Visual Studio só está a mostrar vestígios de nível de erro porque é a definição padrão quando ativa o serviço de monitorização de registos. Quando cria uma nova aplicação do App Service, todo o registo é desativado por padrão, como viu quando abriu a página de definições mais cedo:

    ![Registo de inscrição](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    No entanto, quando selecionou **registos**de streaming de visualização, o Visual Studio alterou automaticamente o registo de registos de **aplicação (Sistema de Ficheiros)** para **Erro**, o que significa que os registos de nível de erro são reportados. Para ver todos os seus registos de rastreio, pode alterar esta definição para **Verbose**. Quando se leciona um nível de gravidade inferior ao erro, todos os registos para níveis de gravidade mais elevados também são relatados. Assim, quando seleciona verboso, também vê registos de informação, aviso e erro.  

5. No **Server Explorer,** clique na aplicação e clique em **'Ver Definições',** como fez anteriormente.
6. Alterar a **registo de aplicações (Sistema de Ficheiros)** para **Verbose,** e depois clicar em **Guardar**.

    ![Definição do nível do vestígio para Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. Na janela do navegador que agora mostra a sua página de **Contacto,** clique em **Casa,** clique em **About**, e, em seguida, clique em **Contato**.

    Dentro de alguns segundos, a janela **de saída** mostra toda a sua saída de rastreio.

    ![Saída de vestígios verbosos](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    Nesta secção, ativou e desativou o registo através das definições da aplicação. Também pode ativar e desativar os ouvintes modificando o ficheiro Web.config. No entanto, modificar o ficheiro Web.config faz com que o domínio da aplicação recicle, ao mesmo tempo que permitir o registo através da configuração da aplicação não o faz. Se o problema demorar muito tempo a reproduzir-se, ou for intermitente, reciclar o domínio da aplicação pode "consertá-lo" e forçá-lo a esperar até que volte a acontecer. Ativar diagnósticos em Azure permite-lhe começar a capturar informações de erro imediatamente sem reciclar o domínio da aplicação.

### <a name="output-window-features"></a>Características da janela de saída
O separador **de registos Microsoft Azure** da Janela de **Saída** tem vários botões e uma caixa de texto:

![Botões de separador de logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Estes desempenham as seguintes funções:

* Limpe a janela **de saída.**
* Ativar ou desativar o embrulho de palavras.
* Inicie ou pare de monitorizar os registos.
* Especifique quais os registos a monitorizar.
* Descarregue os registos.
* Filtrar os registos com base numa cadeia de pesquisa ou numa expressão regular.
* Feche a janela **de saída.**

Se introduzir uma cadeia de pesquisa ou expressão regular, o Visual Studio filtra o registo de informações no cliente. Isto significa que pode introduzir os critérios após a visualização dos registos na janela **Saída** e pode alterar os critérios de filtragem sem ter de regenerar os registos.

## <a name="webserverlogs"></a>Ver registos de servidores web
Os registos do servidor web registam todas as atividades do HTTP para a aplicação. Para vê-los na janela **Output,** deve ative-os para a aplicação e diga ao Visual Studio que pretende monitorizá-los.

1. No separador de configuração da **aplicação Web Azure** que abriu do **Server Explorer,** altere o Registo do Servidor Web para **On**e, em seguida, clique em **Guardar**.

    ![Ativar o registo do servidor web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. Na janela **de saída,** clique no **especte do microsoft Azure para monitorizar o** botão.

    ![Especificar quais os registos azure a monitorizar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. Na caixa de diálogo **Microsoft Azure Logging Options,** selecione **registos**do servidor Web e, em seguida, clique em **OK**.

    ![Monitorize os registos do servidor web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. Na janela do navegador que mostra a aplicação, clique em **Casa,** depois clique em **About**, e depois clique em **Contato**.

    Os registos de aplicação geralmente aparecem primeiro, seguidos dos registos do servidor web. Talvez tenha que esperar um pouco para que os troncos apareçam.

    ![Registos do servidor web na janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Por predefinição, quando ativa os registos do servidor web utilizando o Visual Studio, o Azure escreve os registos para o sistema de ficheiros. Como alternativa, pode utilizar o portal Azure para especificar que os registos do servidor web devem ser escritos a um recipiente de blob numa conta de armazenamento.

Se utilizar o portal para permitir o registo do servidor web numa conta de armazenamento Azure e, em seguida, desativar o registo no Estúdio Visual, quando reativa o registo no Estúdio Visual, as definições da sua conta de armazenamento são restauradas.

## <a name="detailederrorlogs"></a>Ver registos de mensagens de erro detalhados
Os registos de erros detalhados fornecem algumas informações adicionais sobre pedidos http que resultam em códigos de resposta a erros (400 ou mais). Para vê-los na janela **Output,** tem de os ativar para a aplicação e dizer ao Visual Studio que pretende monitorizá-los.

1. No separador de configuração da **aplicação Web Azure** que abriu do **Server Explorer,** altere **as mensagens de erro detalhadas** para **on**e, em seguida, clique em **Guardar**.

    ![Ativar mensagens de erro detalhadas](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Na janela **de saída,** clique no **especte do microsoft Azure para monitorizar o** botão.

3. Na caixa de diálogo **Microsoft Azure Logging Options,** clique em **todos os registos,** e clique em **OK**.

    ![Monitorize todos os registos](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Na barra de endereços da janela do navegador, adicione um carácter extra ao URL para causar um erro 404 (por exemplo, `http://localhost:53370/Home/Contactx`) e prima Enter.

    Após vários segundos, o registo de erros detalhado aparece na janela de **saída** do Estúdio Visual.

    ![Registo de erro detalhado - Janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Controle+clique no link para ver a saída de log formatada num browser:

    ![Registo de erro detalhado - janela do navegador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Descarregue registos do sistema de ficheiros
Quaisquer registos que possa monitorizar na janela **Saída** também podem ser descarregados como um ficheiro *.zip.*

1. Na janela **Saída,** clique em **Download Streaming Logs**.

    ![Botões de separador de logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    O File Explorer abre-se para a pasta *Downloads* com o ficheiro descarregado selecionado.

    ![Ficheiro descarregado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Extraio o ficheiro *.zip* e vê a seguinte estrutura de pasta:

    ![Ficheiro descarregado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Os registos de rastreio de aplicações estão em ficheiros *.txt* na pasta *LogFiles\Application.*
   * Os registos do servidor web estão em *ficheiros .log* na pasta *LogFiles\http\RawLogs.* Pode utilizar uma ferramenta como [log Parser](https://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) para visualizar e manipular estes ficheiros.
   * Os registos de mensagens de erro detalhados estão em ficheiros *.html* na pasta *LogFiles\DetailedErrors.*

     (A pasta de implementações destina-se a *ficheiros* criados pela publicação de controlo de fontes; não tem nada relacionado com a publicação do Visual Studio. A pasta *Git* destina-se a vestígios relacionados com a publicação do controlo de fonte e o serviço de streaming de ficheiros de registo.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-create-storage-account.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](https://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="failedrequestlogs"></a>Ver registos de rastreio de pedidos falhados
Os registos de rastreio de pedidos falhados são úteis quando precisa de compreender os detalhes de como o IIS está a lidar com um pedido http, em cenários como reescrita de URL ou problemas de autenticação.

As aplicações do Serviço de Aplicações utilizam a mesma funcionalidade de rastreio de rastreio de pedidos falhado que foi disponibilizada com o IIS 7.0 e posteriormente. No entanto, não tem acesso às definições do IIS que configuram quais os erros que são registados. Quando ativa o rastreio de pedidos falhado, todos os erros são capturados.

Pode ativar o rastreio de pedidos falhadoutilizando o Visual Studio, mas não pode vê-los no Visual Studio. Estes registos são ficheiros XML. O serviço de registo de streaming apenas monitoriza ficheiros que são considerados legíveis no modo de texto simples: *.txt*, *.html*, e *.log* ficheiros.

Pode visualizar registos de rastreio de pedidos falhados num navegador diretamente via FTP ou localmente depois de usar uma ferramenta FTP para descarregá-los para o seu computador local. Nesta secção, irá vê-los diretamente num navegador.

1. No separador **de configuração** da janela Web **App Azure** que abriu do **Server Explorer,** altere o **Rastreio de Pedido Falhado** para **On**e, em seguida, clique em **Guardar**.

    ![Ativar rastreio de pedido falhado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Na barra de endereços da janela do navegador que mostra a aplicação, adicione um personagem extra ao URL e clique em Enter para causar um erro de 404.

    Isto faz com que seja criado um registo de rastreio de rastreio de pedidos falhado, e os seguintes passos mostram como visualizar ou descarregar o registo.

3. No Estúdio Visual, no separador **de configuração** da janela **Da Web App Azure,** clique **em Open in Management Portal**.

4. Na página **definições** do [portal Azure](https://portal.azure.com) para a sua aplicação, clique em **credenciais de implementação**e, em seguida, introduza um novo nome de utilizador e palavra-passe.

    ![Novo nome de utilizador ftp e senha](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Quando iniciar sessão, tem de utilizar o nome completo do utilizador com o nome da aplicação prefixado. Por exemplo, se introduzir "myid" como nome de utilizador e o site for "o meu exemplo", você entra como "myexample\myid".
    >

5. Numa nova janela de navegador, vá ao URL que é mostrado sob o nome de **anfitrião FTP** ou **ftps** na página **'Overview'** para a sua aplicação.

6. Inscreva-se utilizando as credenciais FTP que criou anteriormente (incluindo o prefixo de nome da aplicação para o nome do utilizador).

    O navegador mostra a pasta raiz da aplicação.

7. Abra a pasta *LogFiles.*

    ![Abrir a pasta LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Abra a pasta que se chama W3SVC mais um valor numérico.

    ![Abrir pasta W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    A pasta contém ficheiros XML para quaisquer erros que tenham sido registados após o rastreio de pedido falhado ativado, e um ficheiro XSL que um navegador pode usar para formatar o XML.

    ![Pasta W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Clique no ficheiro XML para obter o pedido falhado para o qual pretende ver informações de rastreio.

    A ilustração que se segue mostra parte da informação de rastreio para um erro de amostra.

    ![Falha no rastreio de pedidos no navegador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>Passos seguintes
Já viu como o Visual Studio facilita a visualização de registos criados por uma aplicação de Serviço de Aplicações. As seguintes secções fornecem ligações a mais recursos sobre temas relacionados:

* Resolução de problemas do Serviço de Aplicações
* Depuração em Estúdio Visual
* Depuração remota em Azure
* Rastreio em aplicações ASP.NET
* Analisar registos de servidores web
* Analisar registos de rastreio de pedidos falhados
* Depuração de serviços de nuvem

### <a name="app-service-troubleshooting"></a>Resolução de problemas do Serviço de Aplicações
Para obter mais informações sobre aplicações de resolução de problemas no Azure App Service, consulte os seguintes recursos:

* [Como monitorizar aplicações](web-sites-monitor.md)
* Investigar fugas de [memória no Serviço de Aplicações Azure com o Estúdio Visual 2013](https://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Post de blog da Microsoft ALM sobre funcionalidades do Estúdio Visual para analisar problemas de memória geridos.
* [Ferramentas online do Azure App Service que deve conhecer.](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/) Post de blog de Amit Apple.

Para ajudar com uma questão específica de resolução de problemas, inicie um fio num dos seguintes fóruns:

* [O fórum Azure no local ASP.NET.](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET)
* [O fórum Azure sobre a Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-webapps.html).
* [StackOverflow.com.](https://www.stackoverflow.com)

### <a name="debugging-in-visual-studio"></a>Depuração em Estúdio Visual
Para obter mais informações sobre como usar o modo dedepura no Visual Studio, consulte [Debugging no Visual Studio](/visualstudio/debugger/debugging-in-visual-studio) e [Debugging Tips com Visual Studio 2010](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Depuração remota em Azure
Para obter mais informações sobre depuração remota para aplicações de Serviço saem e WebJobs, consulte os seguintes recursos:

* Introdução ao Serviço de [Aplicações Azure Debugging Remoto.](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/)
* [Introdução ao Serviço de Aplicações Azure Debugging Remoto parte 2 - Depuração remota](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introdução à Depuração Remota no Serviço de Aplicações Azure parte 3 - Ambiente multi-instância e GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Depuração webJobs (vídeo)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Se a sua aplicação utilizar um Back-end Azur API ou Mobile Services e tiver de o desmarcar, consulte [Debugging .NET Backend em Visual Studio](https://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Rastreio em aplicações ASP.NET
Não existem introduções completas e atualizadas para ASP.NET rastreio disponível na Internet. O melhor que pode fazer é começar com materiais introdutórios antigos escritos para Formulários Web porque o MVC ainda não existia, e complementá-lo com publicações de blogmais recentes que se focam em questões específicas. Alguns bons lugares para começar são os seguintes recursos:

* [Monitorização e Telemetria (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  Capítulo do e-book com recomendações para rastreio em aplicações em nuvem Azure.
* [ASP.NET Rastreio](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Antigo, mas ainda um bom recurso para uma introdução básica ao assunto.
* [Rastrear ouvintes](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Informações sobre os ouvintes de rastreio, mas não menciona o [WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener).
* [Walkthrough: Integração ASP.NET Rastreio com Sistema.Rastreio de Diagnósticos](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Este artigo também é antigo, mas inclui algumas informações adicionais que o artigo introdutório não cobre.
* [Rastreio em ASP.NET Vistas da Navalha MVC](https://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Além de rastrear nas vistas da Razor, o post também explica como criar um filtro de erro para registar todas as exceções não tratadas numa aplicação MVC. Para obter informações sobre como registar todas as exceções não tratadas numa aplicação de Formulários Web, consulte o exemplo Global.asax em [Completo Exemplo para Manipuladores](/previous-versions/bb397417(v=vs.140)) de Erros na MSDN. Em MVC ou Formulários Web, se pretender registar certas exceções, mas deixar que o manuseamento de quadros predefinidos faça efeito para eles, pode capturar e relançar como no seguinte exemplo:

    ```csharp
    try
    {
       // Your code that might cause an exception to be thrown.
    }
    catch (Exception ex)
    {
        Trace.TraceError("Exception: " + ex.ToString());
        throw;
    }
    ```

* [Diagnósticos de streaming Trace Logging from the Azure Command Line (plus Glimpse!)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Como usar a linha de comando para fazer o que este tutorial mostra como fazer no Estúdio Visual. [O vislumbre](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) é uma ferramenta para depurar aplicações ASP.NET.
* [Usando web apps Logging and Diagnostics - com David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) e [Streaming Logs de Web Apps - com David Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Vídeos de Scott Hanselman e David Ebbo.

Para a exploração por erros, uma alternativa à escrita do seu próprio código de rastreio é utilizar uma estrutura de registo de código aberto, como [elmah](https://nuget.org/packages/elmah/). Para mais informações, consulte as [publicações de Scott Hanselman sobre a ELMAH.](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx)

Além disso, não precisa de utilizar ASP.NET ou `System.Diagnostics` rastreio para obter registos de streaming do Azure. O serviço de streaming de ficheiros de streaming da aplicação App Service transmite qualquer *.txt*, *.html*, ou *.log* file que encontre na pasta *LogFiles.* Por isso, poderá criar o seu próprio sistema de registo que escreve para o sistema de ficheiros da aplicação, e o seu ficheiro é automaticamente transmitido e descarregado. Tudo o que tem de fazer é escrever código de aplicação que cria ficheiros na pasta *d:\home\logfiles.*

### <a name="analyzing-web-server-logs"></a>Analisar registos de servidores web
Para obter mais informações sobre a análise de registos de servidores web, consulte os seguintes recursos:

* [LogParser](https://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Uma ferramenta para visualizar dados em registos de servidores web *(ficheiros .log).*
* [Resolução de problemas Problemas de Problemas problemas de desempenho ou erros de aplicação usando LogParser](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Uma introdução à ferramenta Log Parser que pode utilizar para analisar registos de servidores web.
* [Posts de blog de Robert McMurray sobre a utilização de LogParser](https://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [O código de estado HTTP no IIS 7.0, IIS 7.5 e IIS 8.0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analisar registos de rastreio de pedidos falhados
O website da Microsoft TechNet inclui uma secção de Rastreio de [Pedidos Falhados,](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) que pode ser útil para entender como usar estes registos. No entanto, esta documentação centra-se principalmente na configuração do rastreio de pedidos falhados no IIS, o que não pode fazer no Azure App Service.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
