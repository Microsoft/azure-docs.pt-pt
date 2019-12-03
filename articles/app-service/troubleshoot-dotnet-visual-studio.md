---
title: Resolver problemas com o Visual Studio
description: Saiba como solucionar problemas de um aplicativo do serviço de aplicativo usando a depuração remota, o rastreamento e as ferramentas de log criadas no Visual Studio 2013.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: seodec18
ms.openlocfilehash: 5c0a236dc6ebf02c859d9db3f25f0e9016ac35ab
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688374"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Solucionar problemas de um aplicativo no serviço Azure App usando o Visual Studio
## <a name="overview"></a>Visão geral
Este tutorial mostra como usar as ferramentas do Visual Studio para ajudar a depurar um aplicativo no [serviço de aplicativo](https://go.microsoft.com/fwlink/?LinkId=529714), executando no [modo de depuração](https://docs.microsoft.com/visualstudio/debugger/) remotamente ou exibindo logs do aplicativo e logs do servidor Web.

Irá aprender:

* Quais funções de gerenciamento de aplicativo estão disponíveis no Visual Studio.
* Como usar a exibição remota do Visual Studio para fazer alterações rápidas em um aplicativo remoto.
* Como executar o modo de depuração remotamente enquanto um projeto está em execução no Azure, tanto para um aplicativo quanto para um WebJob.
* Como criar logs de rastreamento de aplicativo e exibi-los enquanto o aplicativo os está criando.
* Como exibir logs do servidor Web, incluindo mensagens de erro detalhadas e rastreamento de solicitação com falha.
* Como enviar logs de diagnóstico para uma conta de armazenamento do Azure e exibi-los lá.

Se você tiver Visual Studio Ultimate, também poderá usar o [IntelliTrace](/visualstudio/debugger/intellitrace) para depuração. O IntelliTrace não é abordado neste tutorial.

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial funciona com o ambiente de desenvolvimento, o projeto Web e o aplicativo do serviço de aplicativo que você configurou em [criar um aplicativo ASP.net no serviço Azure app](app-service-web-get-started-dotnet-framework.md). Para as seções de trabalhos Web, você precisará do aplicativo criado em introdução [ao SDK do Azure WebJobs][GetStartedWJ].

Os exemplos de código mostrados neste tutorial são para C# um aplicativo Web MVC, mas os procedimentos de solução de problemas são os mesmos para aplicativos Visual Basic e Web Forms.

O tutorial pressupõe que você esteja usando o Visual Studio 2019. 

O recurso de logs de streaming só funciona para aplicativos direcionados .NET Framework 4 ou posteriores.

## <a name="sitemanagement"></a>Configuração e gerenciamento de aplicativos
O Visual Studio fornece acesso a um subconjunto das funções de gerenciamento de aplicativo e das definições de configuração disponíveis no [portal do Azure](https://go.microsoft.com/fwlink/?LinkId=529715). Nesta seção, você verá o que está disponível usando **Gerenciador de servidores**. Para ver os recursos mais recentes de integração do Azure, experimente o **Cloud Explorer** também. Você pode abrir ambas as janelas no menu **Exibir** .

1. Se você ainda não tiver entrado no Azure no Visual Studio, clique com o botão direito do mouse em **Azure** e selecione conectar-se a **Microsoft Azure assinatura** no **Gerenciador de servidores**.

    Uma alternativa é instalar um certificado de gerenciamento que permita o acesso à sua conta. Se você optar por instalar um certificado, clique com o botão direito do mouse no nó **do Azure** em **Gerenciador de servidores**e, em seguida, selecione **gerenciar e filtrar assinaturas** no menu de contexto. Na caixa de diálogo **gerenciar assinaturas do Microsoft Azure** , clique na guia **certificados** e, em seguida, clique em **importar**. Siga as instruções para baixar e importar um arquivo de assinatura (também chamado de arquivo *. publishsettings* ) para sua conta do Azure.

   > [!NOTE]
   > Se você baixar um arquivo de assinatura, salve-o em uma pasta fora dos diretórios de código-fonte (por exemplo, na pasta downloads) e exclua-o após a conclusão da importação. Um usuário mal-intencionado que obtém acesso ao arquivo de assinatura pode editar, criar e excluir seus serviços do Azure.
   >
   >

    Para obter mais informações sobre como se conectar a recursos do Azure por meio do Visual Studio, consulte [gerenciar contas, assinaturas e funções administrativas](https://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. Em **Gerenciador de servidores**, expanda **Azure** e expanda **serviço de aplicativo**.
3. Expanda o grupo de recursos que inclui o aplicativo que você criou em [criar um aplicativo ASP.net no serviço Azure app](app-service-web-get-started-dotnet-framework.md)e clique com o botão direito do mouse no nó do aplicativo e clique em **exibir configurações**.

    ![Exibir configurações no Gerenciador de Servidores](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    A guia **aplicativo Web do Azure** é exibida e você pode ver lá as tarefas de configuração e gerenciamento de aplicativo que estão disponíveis no Visual Studio.

    ![Janela do aplicativo Web do Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    Neste tutorial, você usará os menus suspensos de log e rastreamento. Você também usará a depuração remota, mas usará um método diferente para habilitá-la.

    Para obter informações sobre as caixas configurações do aplicativo e cadeias de conexão nesta janela, consulte [Azure app Service: como as cadeias de caracteres do aplicativo e as cadeias de conexão funcionam](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Se você quiser executar uma tarefa de gerenciamento de aplicativo que não pode ser feita nesta janela, clique em **abrir no portal de gerenciamento** para abrir uma janela do navegador para o portal do Azure.

## <a name="remoteview"></a>Acessar arquivos de aplicativo no Gerenciador de Servidores
Normalmente, você implanta um projeto Web com o sinalizador `customErrors` no arquivo Web. config definido como `On` ou `RemoteOnly`, o que significa que você não receberá uma mensagem de erro útil quando algo der errado. Para muitos erros, tudo o que você obtém é uma página como uma das seguintes:

**Erro de servidor no aplicativo '/':**

![Página de erro não auxiliar](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Ocorreu um erro:**

![Página de erro não auxiliar](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**O site não pode exibir a página**

![Página de erro não auxiliar](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Frequentemente, a maneira mais fácil de encontrar a causa do erro é habilitar mensagens de erro detalhadas, que a primeira das capturas de tela anteriores explica como fazer. Isso requer uma alteração no arquivo Web. config implantado. Você pode editar o arquivo *Web. config* no projeto e reimplantar o projeto, ou criar uma [transformação de Web. config](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) e implantar uma compilação de depuração, mas há uma maneira mais rápida: em **Gerenciador de soluções**, você pode exibir e editar os arquivos diretamente no aplicativo remoto usando o recurso de *exibição remota* .

1. No **Gerenciador de servidores**, expanda **Azure**, expanda **serviço de aplicativo**, expanda o grupo de recursos em que seu aplicativo está localizado e, em seguida, expanda o nó para seu aplicativo.

    Você vê os nós que dão acesso aos arquivos de conteúdo do aplicativo e arquivos de log.
2. Expanda o nó **arquivos** e clique duas vezes no arquivo *Web. config* .

    ![Abrir Web. config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    O Visual Studio abre o arquivo Web. config do aplicativo remoto e mostra [remoto] ao lado do nome do arquivo na barra de título.
3. Adicione a seguinte linha ao elemento `system.web`:

    `<customErrors mode="Off"></customErrors>`

    ![Editar Web. config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Atualize o navegador que está mostrando a mensagem de erro não auxiliar e agora você recebe uma mensagem de erro detalhada, como o exemplo a seguir:

    ![Mensagem de erro detalhada](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (O erro mostrado foi criado adicionando a linha mostrada em vermelho a *Views\Home\Index.cshtml*.)

Editar o arquivo Web. config é apenas um exemplo de cenários nos quais a capacidade de ler e editar arquivos em seu aplicativo do serviço de aplicativo facilita a solução de problemas.

## <a name="remotedebug"></a>Aplicativos de depuração remota
Se a mensagem de erro detalhada não fornecer informações suficientes e você não puder recriar o erro localmente, outra maneira de solucionar problemas será executar no modo de depuração remotamente. Você pode definir pontos de interrupção, manipular a memória diretamente, percorrer o código e até mesmo alterar o caminho do código.

A depuração remota não funciona em edições Express do Visual Studio.

Esta seção mostra como depurar remotamente usando o projeto que você cria em [criar um aplicativo ASP.net no serviço Azure app](app-service-web-get-started-dotnet-framework.md).

1. Abra o projeto Web que você criou em [criar um aplicativo ASP.net no serviço Azure app](app-service-web-get-started-dotnet-framework.md).

1. Abra *Controllers\HomeController.cs*.

1. Exclua o método `About()` e insira o código a seguir em seu lugar.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. [Defina um ponto de interrupção](https://docs.microsoft.com/visualstudio/debugger/) na linha de `ViewBag.Message`.

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e clique em **publicar**.

1. Na lista suspensa **perfil** , selecione o mesmo perfil que você usou em [criar um aplicativo ASP.NET no serviço Azure app](app-service-web-get-started-dotnet-framework.md). Em seguida, clique em configurações.

1. Na caixa de diálogo **publicar** , clique na guia **configurações** e altere a **configuração** para **depurar**e, em seguida, clique em **salvar**.

    ![Publicar no modo de depuração](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Clique em **Publicar**. Após a conclusão da implantação e o navegador abrir na URL do Azure do seu aplicativo, feche o navegador.

1. Em **Gerenciador de servidores**, clique com o botão direito do mouse em seu aplicativo e clique em **anexar depurador**.

    ![Anexar depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    O navegador é aberto automaticamente no home page em execução no Azure. Talvez seja necessário aguardar 20 segundos ou, enquanto o Azure configura o servidor para depuração. Esse atraso ocorre apenas na primeira vez em que você executa o modo de depuração em um aplicativo em um período de 48 horas. Quando você inicia a depuração novamente no mesmo período, não há um atraso.

    > [!NOTE] 
    > Se você tiver problemas para iniciar o depurador, tente fazer isso usando o **Cloud Explorer** em vez de **Gerenciador de servidores**.
    >

1. Clique em **sobre** no menu.

    O Visual Studio para no ponto de interrupção, e o código está em execução no Azure, não no seu computador local.

1. Passe o mouse sobre a variável `currentTime` para ver o valor de hora.

    ![Exibir variável no modo de depuração em execução no Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    A hora que você vê é a hora do servidor do Azure, que pode estar em um fuso horário diferente do computador local.

1. Insira um novo valor para a variável de `currentTime`, como "agora em execução no Azure".

1. Pressione F5 para continuar a execução.

     A página sobre em execução no Azure exibe o novo valor que você inseriu na variável CurrentTime.

     ![Página sobre com novo valor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a>Depuração remota de trabalhos Web
Esta seção mostra como depurar remotamente usando o projeto e o aplicativo que você cria em introdução [ao SDK do Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Os recursos mostrados nesta seção estão disponíveis somente no Visual Studio 2013 com a atualização 4 ou posterior.

A depuração remota funciona apenas com trabalhos Web contínuos. Trabalhos Web agendados e sob demanda não dão suporte à depuração.

1. Abra o projeto Web que você criou em introdução [ao SDK do Azure WebJobs][GetStartedWJ].

2. No projeto ContosoAdsWebJob, abra *functions.cs*.

3. [Defina um ponto de interrupção](https://docs.microsoft.com/visualstudio/debugger/) na primeira instrução no método `GnerateThumbnail`.

    ![Definir ponto de interrupção](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto Web (não no projeto WebJob) e clique em **publicar**.

5. Na lista suspensa **perfil** , selecione o mesmo perfil que você usou em introdução [ao SDK do Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Clique na guia **configurações** e altere a **configuração** para **depurar**e, em seguida, clique em **publicar**.

    O Visual Studio implanta os projetos Web e WebJob e seu navegador é aberto na URL do Azure do seu aplicativo.

7. No **Gerenciador de servidores**, expanda **o serviço de aplicativo > do Azure > seu grupo de recursos > seu aplicativo > trabalhos Web > contínuos**e clique com o botão direito do mouse em **ContosoAdsWebJob**.

8. Clique em **anexar depurador**.

    ![Anexar depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    O navegador é aberto automaticamente no home page em execução no Azure. Talvez seja necessário aguardar 20 segundos ou, enquanto o Azure configura o servidor para depuração. Esse atraso ocorre apenas na primeira vez em que você executa o modo de depuração em um aplicativo em um período de 48 horas. Quando você inicia a depuração novamente no mesmo período, não há um atraso.

9. No navegador da Web que é aberto no home page de anúncios da Contoso, crie um novo anúncio.

    A criação de um anúncio faz com que uma mensagem da fila seja criada, que é selecionada pelo WebJob e processada. Quando o SDK de trabalhos Web chama a função para processar a mensagem da fila, o código atinge o ponto de interrupção.

10. Quando o depurador interromper o ponto de interrupção, você poderá examinar e alterar valores de variáveis enquanto o programa estiver executando a nuvem. Na ilustração a seguir, o depurador mostra o conteúdo do objeto blobInfo que foi passado para o método `GenerateThumbnail`.

     ![objeto blobInfo no depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Pressione F5 para continuar a execução.

     O método `GenerateThumbnail` conclui a criação da miniatura.

12. No navegador, atualize a página de índice e veja a miniatura.

13. No Visual Studio, pressione SHIFT + F5 para parar a depuração.

14. Em **Gerenciador de servidores**, clique com o botão direito do mouse no nó ContosoAdsWebJob e clique em **exibir painel**.

15. Entre com suas credenciais do Azure e clique no nome do WebJob para ir para a página de seu WebJob.

     ![Clique em ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     O painel mostra que a função `GenerateThumbnail` executada recentemente.

     (Na próxima vez que você clicar em **exibir painel**, não precisará entrar e o navegador vai diretamente para a página do WebJob.)

16. Clique no nome da função para ver detalhes sobre a execução da função.

     ![Detalhes da função](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Se sua função [escreveu logs](https://github.com/Azure/azure-webjobs-sdk/wiki), você pode clicar em **ToggleOutput** para vê-los.

## <a name="notes-about-remote-debugging"></a>Observações sobre a depuração remota

* Não é recomendável executar no modo de depuração em produção. Se seu aplicativo de produção não for dimensionado para várias instâncias de servidor, a depuração impedirá que o servidor Web responda a outras solicitações. Se você tiver várias instâncias de servidor Web, ao anexar ao depurador, você obterá uma instância aleatória e não terá como garantir que as solicitações subsequentes do navegador vão para a mesma instância. Além disso, normalmente você não implanta uma compilação de depuração para produção, e otimizações de compilador para compilações de versão podem tornar impossível mostrar o que está acontecendo linha por linha em seu código-fonte. Para solucionar problemas de produção, o melhor recurso é o rastreamento de aplicativos e os logs do servidor Web.
* Evite longas interrupções em pontos de interrupção durante a depuração remota. O Azure trata um processo que é interrompido por mais de alguns minutos como um processo sem resposta e o desliga.
* Enquanto você estiver Depurando, o servidor está enviando dados para o Visual Studio, o que pode afetar os encargos de largura de banda. Para obter informações sobre taxas de largura de banda, consulte [preços do Azure](https://azure.microsoft.com/pricing/calculator/).
* Verifique se o atributo `debug` do elemento `compilation` no arquivo *Web. config* está definido como true. Ele é definido como true por padrão quando você publica uma configuração de compilação de depuração.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Se você descobrir que o depurador não apresenta o código que você deseja depurar, talvez seja necessário alterar a configuração de Apenas Meu Código.  Para obter mais informações, consulte [especificar se deseja depurar apenas o código do usuário usando apenas meu código no Visual Studio](https://docs.microsoft.com/visualstudio/debugger/just-my-code).
* Um temporizador é iniciado no servidor quando você habilita o recurso de depuração remota e, após 48 horas, o recurso é desativado automaticamente. Esse limite de 48 horas é feito por motivos de segurança e desempenho. Você pode facilmente transformar o recurso de volta em quantas vezes desejar. É recomendável deixá-lo desabilitado quando você não estiver Depurando ativamente.
* Você pode anexar manualmente o depurador a qualquer processo, não apenas ao processo do aplicativo (w3wp. exe). Para obter mais informações sobre como usar o modo de depuração no Visual Studio, consulte [Depurando no Visual Studio](/visualstudio/debugger/debugging-in-visual-studio).

## <a name="logsoverview"></a>Visão geral dos logs de diagnóstico
Um aplicativo ASP.NET que é executado em um aplicativo do serviço de aplicativo pode criar os seguintes tipos de logs:

* **Logs de rastreamento de aplicativo**<br/>
  O aplicativo cria esses logs chamando métodos da classe [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace) .
* **Logs do servidor Web**<br/>
  O servidor Web cria uma entrada de log para cada solicitação HTTP para o aplicativo.
* **Logs de mensagens de erro detalhadas**<br/>
  O servidor Web cria uma página HTML com algumas informações adicionais para solicitações HTTP com falha (solicitações que resultam no código de status 400 ou superior).
* **Logs de rastreamento de solicitação com falha**<br/>
  O servidor Web cria um arquivo XML com informações detalhadas de rastreamento para solicitações HTTP com falha. O servidor Web também fornece um arquivo XSL para formatar o XML em um navegador.

O registro em log afeta o desempenho do aplicativo, de modo que o Azure oferece a capacidade de habilitar ou desabilitar cada tipo de log, conforme necessário. Para logs de aplicativo, você pode especificar que somente os logs acima de um determinado nível de severidade devem ser gravados. Quando você cria um novo aplicativo, por padrão, todo o log é desabilitado.

Os logs são gravados em arquivos em uma pasta *LogFiles* no sistema de arquivos do seu aplicativo e podem ser acessados via FTP. Logs de servidor Web e logs de aplicativo também podem ser gravados em uma conta de armazenamento do Azure. Você pode manter um volume maior de logs em uma conta de armazenamento do que é possível no sistema de arquivos. Você está limitado a um máximo de 100 megabytes de logs quando usa o sistema de arquivos. (Os logs do sistema de arquivos são apenas para retenção de curto prazo. O Azure exclui arquivos de log antigos para liberar espaço para os novos depois que o limite é atingido.)  

## <a name="apptracelogs"></a>Criar e exibir logs de rastreamento do aplicativo
Nesta seção, você executa as seguintes tarefas:

* Adicione instruções de rastreamento ao projeto Web que você criou em [introdução ao Azure e ASP.net](app-service-web-get-started-dotnet-framework.md).
* Exiba os logs quando você executar o projeto localmente.
* Exiba os logs conforme eles são gerados pelo aplicativo em execução no Azure.

Para obter informações sobre como criar logs de aplicativos em trabalhos Web, consulte [como trabalhar com o armazenamento de filas do Azure usando o SDK de trabalhos Web-como gravar logs](https://github.com/Azure/azure-webjobs-sdk/wiki). As instruções a seguir para exibir os logs e controlar como eles são armazenados no Azure se aplicam também aos logs de aplicativo criados pelo webjobs.

### <a name="add-tracing-statements-to-the-application"></a>Adicionar instruções de rastreamento ao aplicativo
1. Abra *Controllers\HomeController.cs*e substitua os métodos `Index`, `About`e `Contact` pelo código a seguir para adicionar instruções de `Trace` e uma instrução `using` para `System.Diagnostics`:

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

1. Adicione uma instrução `using System.Diagnostics;` à parte superior do arquivo.

### <a name="view-the-tracing-output-locally"></a>Exibir a saída de rastreamento localmente
1. Pressione F5 para executar o aplicativo no modo de depuração.

    O ouvinte de rastreamento padrão grava toda a saída de rastreamento na janela de **saída** , juntamente com outras saídas de depuração. A ilustração a seguir mostra a saída das instruções de rastreamento que você adicionou ao método `Index`.

    ![Rastreamento na janela de depuração](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    As etapas a seguir mostram como exibir a saída de rastreamento em uma página da Web, sem compilar no modo de depuração.
1. Abra o arquivo Web. config do aplicativo (aquele localizado na pasta do projeto) e adicione um elemento `<system.diagnostics>` no final do arquivo logo antes do elemento `</configuration>` de fechamento:

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

O `WebPageTraceListener` permite exibir a saída de rastreamento navegando até `/trace.axd`.
1. Adicione um <a href="https://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">elemento trace</a> em `<system.web>` no arquivo Web. config, como o exemplo a seguir:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Prima CTRL+F5 para executar a aplicação.
1. Na barra de endereços da janela do navegador, adicione *trace. axd* à URL e pressione Enter (a URL é semelhante a `http://localhost:53370/trace.axd`).
1. Na página de **rastreamento do aplicativo** , clique em **Exibir detalhes** na primeira linha (não na linha BrowserLink).

    ![Trace. axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    A página **detalhes da solicitação** é exibida e, na seção informações de **rastreamento** , você vê a saída das instruções de rastreamento que você adicionou ao método `Index`.

    ![Trace. axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Por padrão, `trace.axd` só está disponível localmente. Se você quisesse disponibilizá-lo a partir de um aplicativo remoto, poderá adicionar `localOnly="false"` ao elemento `trace` no arquivo *Web. config* , conforme mostrado no exemplo a seguir:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    No entanto, a habilitação de `trace.axd` em um aplicativo de produção não é recomendada por motivos de segurança. Nas seções a seguir, você verá uma maneira mais fácil de ler logs de rastreamento em um aplicativo do serviço de aplicativo.

### <a name="view-the-tracing-output-in-azure"></a>Exibir a saída de rastreamento no Azure
1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto Web e clique em **publicar**.
2. Na caixa de diálogo **publicar na Web** , clique em **publicar**.

    Depois que o Visual Studio publica sua atualização, ele abre uma janela do navegador para seu home page (supondo que você não limpou a **URL de destino** na guia **conexão** ).
3. Em **Gerenciador de servidores**, clique com o botão direito do mouse em seu aplicativo e selecione **Exibir logs de streaming**.

    ![Exibir logs de streaming no menu de contexto](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    A janela **saída** mostra que você está conectado ao serviço de streaming de log e adiciona uma linha de notificação a cada minuto que passa sem um log para exibir.

    ![Exibir logs de streaming no menu de contexto](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. Na janela do navegador que mostra o home page do seu aplicativo, clique em **contato**.

    Em alguns segundos, a saída do rastreamento de nível de erro adicionado ao método `Contact` aparece na janela **saída** .

    ![Rastreamento de erro na janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    O Visual Studio só mostra rastreamentos de nível de erro porque essa é a configuração padrão quando você habilita o serviço de monitoramento de log. Quando você cria um novo aplicativo do serviço de aplicativo, todo o log é desabilitado por padrão, como vimos quando você abriu a página de configurações anteriormente:

    ![Log de aplicativo desativado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    No entanto, quando você selecionou **Exibir logs de streaming**, o Visual Studio alterou automaticamente o **log do aplicativo (sistema de arquivos)** para **erro**, o que significa que os logs de nível de erro são relatados. Para ver todos os seus logs de rastreamento, você pode alterar essa configuração para **detalhado**. Quando você seleciona um nível de severidade inferior a erro, todos os logs de níveis de severidade mais altos também são relatados. Assim, ao selecionar detalhado, você também verá os logs de informações, avisos e erros.  

5. No **Gerenciador de servidores**, clique com o botão direito do mouse no aplicativo e clique em **exibir configurações** como fazia anteriormente.
6. Altere o **log do aplicativo (sistema de arquivos)** para **detalhado**e clique em **salvar**.

    ![Definindo o nível de rastreamento como detalhado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. Na janela do navegador que agora está mostrando sua página de **contato** , clique em **página inicial**, clique em **sobre**e, em seguida, clique em **contato**.

    Em alguns segundos, a janela **saída** mostra toda a saída de rastreamento.

    ![Saída de rastreamento detalhada](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    Nesta seção, você habilitou e desabilitou o log usando as configurações do aplicativo. Você também pode habilitar e desabilitar os ouvintes de rastreamento modificando o arquivo Web. config. No entanto, modificar o arquivo Web. config faz com que o domínio do aplicativo seja reciclado, ao passo que habilitar o log por meio da configuração do aplicativo não faz isso. Se o problema levar muito tempo para ser reproduzido ou for intermitente, a reciclagem do domínio de aplicativo poderá "corrigir" e forçá-lo a aguardar até que ele ocorra novamente. Habilitar o diagnóstico no Azure permite que você inicie a captura de informações de erro imediatamente sem reciclar o domínio do aplicativo.

### <a name="output-window-features"></a>Recursos da janela de saída
A guia **logs de Microsoft Azure** da janela **saída** tem vários botões e uma caixa de texto:

![Botões da guia logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Eles executam as seguintes funções:

* Desmarque a janela **saída** .
* Habilitar ou desabilitar quebra automática de texto.
* Iniciar ou parar logs de monitoramento.
* Especifique quais logs monitorar.
* Baixar logs.
* Filtrar logs com base em uma cadeia de caracteres de pesquisa ou em uma expressão regular.
* Feche a janela **saída** .

Se você inserir uma cadeia de caracteres de pesquisa ou uma expressão regular, o Visual Studio filtrará as informações de log no cliente. Isso significa que você pode inserir os critérios depois que os logs são exibidos na janela de **saída** e você pode alterar os critérios de filtragem sem precisar gerar os logs novamente.

## <a name="webserverlogs"></a>Exibir logs do servidor Web
Os logs do servidor Web registram todas as atividades HTTP para o aplicativo. Para vê-los na janela de **saída** , você deve habilitá-los para o aplicativo e informar ao Visual Studio que deseja monitorá-los.

1. Na guia **configuração do aplicativo Web do Azure** que você abriu em **Gerenciador de servidores**, altere o log do servidor Web para **ativado**e clique em **salvar**.

    ![Habilitar log do servidor Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. Na janela **saída** , clique no botão **especificar os logs de Microsoft Azure a serem monitorados** .

    ![Especificar quais logs do Azure monitorar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. Na caixa de diálogo **Opções de log de Microsoft Azure** , selecione **logs do servidor Web**e clique em **OK**.

    ![Monitorar logs do servidor Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. Na janela do navegador que mostra o aplicativo, clique em **início**, clique em **sobre**e, em seguida, clique em **contato**.

    Os logs de aplicativo geralmente aparecem primeiro, seguidos pelos logs do servidor Web. Talvez seja necessário aguardar um tempo para que os logs sejam exibidos.

    ![Logs do servidor Web na janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Por padrão, quando você habilita os logs do servidor Web pela primeira vez usando o Visual Studio, o Azure grava os logs no sistema de arquivos. Como alternativa, você pode usar o portal do Azure para especificar que os logs do servidor Web devem ser gravados em um contêiner de BLOB em uma conta de armazenamento.

Se você usar o portal para habilitar o log de servidor Web para uma conta de armazenamento do Azure e, em seguida, desabilitar o log no Visual Studio, ao reabilitar o log no Visual Studio, suas configurações de conta de armazenamento serão restauradas.

## <a name="detailederrorlogs"></a>Exibir logs de mensagem de erro detalhados
Logs de erros detalhados fornecem algumas informações adicionais sobre solicitações HTTP que resultam em códigos de resposta de erro (400 ou acima). Para vê-los na janela de **saída** , você precisa habilitá-los para o aplicativo e informar ao Visual Studio que deseja monitorá-los.

1. Na guia **configuração do aplicativo Web do Azure** que você abriu no **Gerenciador de servidores**, altere **as mensagens de erro detalhadas** para **ativado**e clique em **salvar**.

    ![Habilitar mensagens de erro detalhadas](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Na janela **saída** , clique no botão **especificar os logs de Microsoft Azure a serem monitorados** .

3. Na caixa de diálogo **Opções de log de Microsoft Azure** , clique em **todos os logs**e, em seguida, clique em **OK**.

    ![Monitorar todos os logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Na barra de endereços da janela do navegador, adicione um caractere extra à URL para causar um erro 404 (por exemplo, `http://localhost:53370/Home/Contactx`) e pressione Enter.

    Após vários segundos, o log de erros detalhado aparece na janela **saída** do Visual Studio.

    ![Log de erros detalhado-janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Controle + clique no link para ver a saída de log formatada em um navegador:

    ![Log de erros detalhado-janela do navegador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Baixar logs do sistema de arquivos
Todos os logs que você pode monitorar na janela de **saída** também podem ser baixados como um arquivo *. zip* .

1. Na janela **saída** , clique em **baixar logs de streaming**.

    ![Botões da guia logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    O explorador de arquivos é aberto na pasta *downloads* com o arquivo baixado selecionado.

    ![Arquivo baixado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Extraia o arquivo *. zip* e você verá a seguinte estrutura de pastas:

    ![Arquivo baixado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Os logs de rastreamento de aplicativo estão em arquivos *. txt* na pasta *LogFiles\Application* .
   * Os logs do servidor Web estão em arquivos *. log* na pasta *LogFiles\http\RawLogs* . Você pode usar uma ferramenta como o [Log Parser](https://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) para exibir e manipular esses arquivos.
   * Os logs de mensagem de erro detalhados estão em arquivos *. html* na pasta *LogFiles\DetailedErrors* .

     (A pasta *implantações* é para arquivos criados pela publicação de controle do código-fonte; ele não tem nada relacionado à publicação do Visual Studio. A pasta *git* é para rastreamentos relacionados à publicação de controle do código-fonte e ao serviço de streaming do arquivo de log.)  

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
## <a name="failedrequestlogs"></a>Exibir logs de rastreamento de solicitação com falha
Os logs de rastreamento de solicitação com falha são úteis quando você precisa entender os detalhes de como o IIS está tratando uma solicitação HTTP, em cenários como problemas de regravação de URL ou de autenticação.

Os aplicativos do serviço de aplicativo usam a mesma funcionalidade de rastreamento de solicitação com falha que foi disponibilizada com o IIS 7,0 e posterior. No entanto, você não tem acesso às configurações do IIS que configuram quais erros são registrados. Quando você habilita o rastreamento de solicitação com falha, todos os erros são capturados.

Você pode habilitar o rastreamento de solicitação com falha usando o Visual Studio, mas não pode exibi-los no Visual Studio. Esses logs são arquivos XML. O serviço de log de streaming monitora apenas os arquivos que são considerados legíveis em modo de texto sem formatação: *. txt*, *. html*e arquivos *. log* .

Você pode exibir os logs de rastreamento de solicitação com falha em um navegador diretamente por FTP ou localmente depois de usar uma ferramenta de FTP para baixá-los em seu computador local. Nesta seção, você os exibirá diretamente em um navegador.

1. Na guia **configuração** da janela do **aplicativo Web do Azure** que você abriu no **Gerenciador de servidores**, altere **rastreamento de solicitação com falha** para **ativado**e clique em **salvar**.

    ![Habilitar rastreamento de solicitação com falha](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Na barra de endereços da janela do navegador que mostra o aplicativo, adicione um caractere extra à URL e clique em Enter para causar um erro 404.

    Isso faz com que um log de rastreamento de solicitação com falha seja criado e as etapas a seguir mostram como exibir ou baixar o log.

3. No Visual Studio, na guia **configuração** da janela **aplicativo Web do Azure** , clique em **abrir no portal de gerenciamento**.

4. Na página [](https://portal.azure.com) **configurações** de portal do Azure para seu aplicativo, clique em **credenciais de implantação**e insira um novo nome de usuário e senha.

    ![Novo nome de usuário e senha de FTP](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Ao fazer logon, você precisa usar o nome de usuário completo com o nome do aplicativo prefixado para ele. Por exemplo, se você digitar "MyID" como um nome de usuário e o site for "myExample", faça logon como "myexample\myid".
    >

5. Em uma nova janela do navegador, vá para a URL mostrada em nome do host **do FTP** ou **nome do host FTPS** na página **visão geral** do seu aplicativo.

6. Entre usando as credenciais de FTP que você criou anteriormente (incluindo o prefixo do nome do aplicativo para o nome de usuário).

    O navegador mostra a pasta raiz do aplicativo.

7. Abra a pasta *LogFiles* .

    ![Abrir pasta LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Abra a pasta chamada W3SVC mais um valor numérico.

    ![Abrir pasta W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    A pasta contém arquivos XML para todos os erros que foram registrados depois que você habilitou o rastreamento de solicitação com falha e um arquivo XSL que um navegador pode usar para formatar o XML.

    ![Pasta W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Clique no arquivo XML para a solicitação com falha para a qual você deseja ver informações de rastreamento.

    A ilustração a seguir mostra parte das informações de rastreamento de um erro de exemplo.

    ![Falha no rastreamento de solicitação no navegador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>Passos Seguintes
Você viu como o Visual Studio facilita a exibição de logs criados por um aplicativo do serviço de aplicativo. As seções a seguir fornecem links para mais recursos em tópicos relacionados:

* Solução de problemas do serviço de aplicativo
* Depuração no Visual Studio
* Depuração remota no Azure
* Rastreamento em aplicativos ASP.NET
* Analisando logs do servidor Web
* Analisando logs de rastreamento de solicitação com falha
* Depuração de serviços de nuvem

### <a name="app-service-troubleshooting"></a>Solução de problemas do serviço de aplicativo
Para obter mais informações sobre como solucionar problemas de aplicativos no Azure App Service, consulte os seguintes recursos:

* [Como monitorar aplicativos](web-sites-monitor.md)
* [Investigando vazamentos de memória no serviço Azure app com Visual Studio 2013](https://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Postagem de blog do Microsoft ALM sobre recursos do Visual Studio para analisar problemas de memória gerenciada.
* [Azure app ferramentas online do serviço que você deve conhecer](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Postagem de blog de Amit Apple.

Para obter ajuda com uma pergunta de solução de problemas específica, inicie um thread em um dos seguintes fóruns:

* [O fórum do Azure no site do ASP.net](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [O fórum do Azure no MSDN](https://social.msdn.microsoft.com/Forums/windowsazure/).
* [Stackoverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Depuração no Visual Studio
Para obter mais informações sobre como usar o modo de depuração no Visual Studio, consulte [Depurando no Visual Studio](/visualstudio/debugger/debugging-in-visual-studio) e [dicas de depuração com o Visual Studio 2010](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Depuração remota no Azure
Para obter mais informações sobre a depuração remota para aplicativos do serviço de aplicativo e trabalhos Web, consulte os seguintes recursos:

* [Introdução ao serviço de Azure app de depuração remota](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introdução à depuração remota Azure App serviço parte 2-dentro da depuração remota](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introdução à depuração remota no serviço de Azure App parte 3-ambiente de várias instâncias e GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Depuração de trabalhos Web (vídeo)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Se seu aplicativo usa uma API Web do Azure ou um back-end de serviços móveis e você precisa depurá-lo, consulte [Depurando o back-end do .net no Visual Studio](https://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Rastreamento em aplicativos ASP.NET
Não há introduções completas e atualizadas sobre o rastreamento do ASP.NET disponíveis na Internet. O melhor que você pode fazer é começar com os materiais introdutórios antigos escritos para Web Forms porque o MVC ainda não existia e complementar isso com postagens de blog mais recentes que se concentram em problemas específicos. Alguns bons locais para iniciar são os seguintes recursos:

* [Monitoramento e telemetria (criando aplicativos de nuvem do mundo real com o Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  Capítulo de livro eletrônico com recomendações para rastreamento em aplicativos de nuvem do Azure.
* [Rastreamento de ASP.NET](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Antiga, mas ainda um bom recurso para uma introdução básica ao assunto.
* [Ouvintes de rastreamento](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Informações sobre ouvintes de rastreamento, mas não menciona o [WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener).
* [Walkthrough: integrando o rastreamento ASP.NET com o rastreamento System. Diagnostics](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Este artigo também é antigo, mas inclui algumas informações adicionais que o artigo introdutório não cobre.
* [Rastreamento em exibições do Razor do ASP.NET MVC](https://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Além do rastreamento em exibições do Razor, a postagem também explica como criar um filtro de erro para registrar em log todas as exceções sem tratamento em um aplicativo MVC. Para obter informações sobre como registrar em log todas as exceções sem tratamento em um aplicativo Web Forms, consulte o exemplo global. asax em [exemplo completo para manipuladores de erro](/previous-versions/bb397417(v=vs.140)) no msdn. No MVC ou Web Forms, se você quiser registrar determinadas exceções, mas deixar que o tratamento de estrutura padrão tenha efeito para elas, você poderá capturar e relançar como no exemplo a seguir:

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

* [Log de rastreamento de diagnóstico de streaming da linha de comando do Azure (além da visão!)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Como usar a linha de comando para fazer o que este tutorial mostra como fazer no Visual Studio. A [visão](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) é uma ferramenta para depurar aplicativos ASP.net.
* [Usando o log e o diagnóstico de aplicativos Web-com David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) e [logs de streaming de aplicativos Web-com David Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Vídeos de Scott Hanselman e David Ebbo.

Para o log de erros, uma alternativa para escrever seu próprio código de rastreamento é usar uma estrutura de log de software livre, como o [ELMAH](https://nuget.org/packages/elmah/). Para obter mais informações, consulte [Postagens de blog de Scott Hanselman sobre o ELMAH](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Além disso, você não precisa usar o rastreamento ASP.NET ou `System.Diagnostics` para obter logs de streaming do Azure. O serviço de log de streaming do aplicativo do serviço de aplicativo transmite qualquer arquivo *. txt*, *. html*ou *. log* encontrado na pasta *LogFiles* . Portanto, você pode criar seu próprio sistema de registro em log que grava no sistema de arquivos do aplicativo, e o arquivo é automaticamente transmitido e baixado. Tudo o que você precisa fazer é escrever o código do aplicativo que cria arquivos na pasta *d:\home\logfiles* .

### <a name="analyzing-web-server-logs"></a>Analisando logs do servidor Web
Para obter mais informações sobre a análise de logs do servidor Web, consulte os seguintes recursos:

* [LogParser](https://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Uma ferramenta para exibir dados em logs do servidor Web (arquivos *. log* ).
* [Solucionando problemas de desempenho do IIS ou erros de aplicativo usando o LogParser](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Uma introdução à ferramenta do Log Parser que você pode usar para analisar logs do servidor Web.
* [Postagens de blog de Robert McMurray sobre como usar o LogParser](https://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [O código de status HTTP no IIS 7,0, IIS 7,5 e IIS 8,0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analisando logs de rastreamento de solicitação com falha
O site do Microsoft TechNet inclui uma seção [usando rastreamento de solicitação com falha](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) , que pode ser útil para entender como usar esses logs. No entanto, esta documentação se concentra principalmente na configuração do rastreamento de solicitação com falha no IIS, que você não pode fazer no serviço Azure App.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
