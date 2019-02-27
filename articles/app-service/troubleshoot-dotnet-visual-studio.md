---
title: Resolver problemas de uma aplicação com o Visual Studio - serviço de aplicações do Azure
description: Saiba como resolver problemas de uma aplicação de serviço de aplicações ao utilizar a depuração remota, rastreamento e ferramentas de registo que são incorporadas no Visual Studio 2013.
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
editor: ''
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5e749f68aba48ac258363a0a03e3474e1e28b064
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876777"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Resolver problemas de uma aplicação no serviço de aplicações do Azure com o Visual Studio
## <a name="overview"></a>Descrição geral
Este tutorial mostra como utilizar ferramentas do Visual Studio para ajudar a depurar uma aplicação no [serviço de aplicações](https://go.microsoft.com/fwlink/?LinkId=529714), executando [modo de depuração](https://docs.microsoft.com/visualstudio/debugger/) remotamente ou ao visualizar registos de aplicações e registos do servidor web.

Irá aprender:

* As funções de gestão de aplicações estão disponíveis no Visual Studio.
* Como utilizar o modo de exibição remoto do Visual Studio para fazer alterações rápidas num aplicativo remoto.
* Como executar o modo de depuração remotamente, enquanto um projeto está em execução no Azure, para uma aplicação e um WebJob.
* Como criar registos de rastreio de aplicação e visualizá-los enquanto a aplicação é criá-las.
* Como ver os registos do servidor web, incluindo mensagens de erro detalhadas e de pedidos falhados.
* Como enviar registos de diagnóstico ao armazenamento do Azure da conta e visualizá-los lá.

Se tiver o Visual Studio Ultimate, também pode usar [IntelliTrace](https://msdn.microsoft.com/library/vstudio/dd264915.aspx) para depuração. IntelliTrace não é abrangido neste tutorial.

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial funciona com o ambiente de desenvolvimento, o projeto web e a aplicação de serviço de aplicações que configurou no [criar uma aplicação ASP.NET no App Service do Azure](app-service-web-get-started-dotnet-framework.md). Para as secções de WebJobs, terá da aplicação que criar no [começar com o SDK de WebJobs do Azure][GetStartedWJ].

Os exemplos de código mostrados neste tutorial são de um aplicativo da web de MVC do c#, mas os procedimentos de resolução de problemas são os mesmos para aplicativos Visual Basic e Web Forms.

O tutorial parte do princípio de que está a utilizar o Visual Studio 2017. 

Os registos de transmissão em fluxo funcionalidade só funciona para aplicativos que se destinam a .NET Framework 4 ou posterior.

## <a name="sitemanagement"></a>Configuração de aplicações e gestão
O Visual Studio fornece acesso a um subconjunto das funções de gestão de aplicações e definições de configuração disponíveis na [portal do Azure](https://go.microsoft.com/fwlink/?LinkId=529715). Nesta secção, irá ver o que está disponível através da utilização **Explorador de servidores**. Para ver as mais recentes funcionalidades de integração do Azure, experimente **Cloud Explorer** também. É possível abrir duas janelas do **vista** menu.

1. Se não tiver iniciado sessão para o Azure no Visual Studio, clique com botão direito **Azure** e selecione Connect to **subscrição do Microsoft Azure** na **Server Explorer**.

    Uma alternativa é instalar um certificado de gestão que permite o acesso à sua conta. Se optar por instalar um certificado, clique com botão direito a **Azure** nó **Explorador de servidores**e, em seguida, selecione **gerir e subscrições de filtro** no menu de contexto. Na **gerir subscrições do Microsoft Azure** caixa de diálogo, clique nas **certificados** separador e, em seguida, clique em **importar**. Siga as instruções para transferir e, em seguida, importar um ficheiro de subscrição (também chamado de um *. publishsettings* ficheiro) para a sua conta do Azure.

   > [!NOTE]
   > Se transferir um ficheiro de subscrição, guarde-o para uma pasta fora dos diretórios do código de origem (por exemplo, na pasta transferências) e, em seguida, eliminá-lo Depois de concluída a importação. Um utilizador mal intencionado obtiver acesso ao ficheiro de subscrição pode editar, criar e eliminar os seus serviços do Azure.
   >
   >

    Para obter mais informações sobre como ligar aos recursos do Azure a partir do Visual Studio, consulte [gerir contas, subscrições e funções administrativas](https://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. Na **Explorador de servidores**, expanda **Azure** e expanda **serviço de aplicações**.
3. Expanda o grupo de recursos que inclui a aplicação que criou no [criar uma aplicação ASP.NET no App Service do Azure](app-service-web-get-started-dotnet-framework.md)e, em seguida, clique no nó da aplicação e clique em **ver definições**.

    ![Definições de vista do Explorador de servidores](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    O **aplicação Web do Azure** guia é exibida e pode ver, as tarefas de gestão e configuração de aplicação que estão disponíveis no Visual Studio.

    ![Janela de aplicação Web do Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    Neste tutorial, vai utilizar o registo e as listas pendentes de rastreamento. Também vai utilizar a depuração remota, mas usará um método diferente para ativá-la.

    Para obter informações sobre as caixas de definições de aplicações e as cadeias de ligação nesta janela, consulte [App Service do Azure: Como cadeias de caracteres do aplicativo e a ligação de seqüências de caracteres de trabalho](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Se quiser executar uma tarefa de gestão de aplicações que não pode ser feita nessa janela, clique em **abra no Portal de gestão** para abrir uma janela do browser para o portal do Azure.

## <a name="remoteview"></a>Aceder aos ficheiros de aplicação no Explorador de servidores
Normalmente, implementa um projeto da web com o `customErrors` sinalizador no arquivo Web. config definido como `On` ou `RemoteOnly`, que significa que não obtém uma mensagem de erro útil quando algo dá errado. Para muitos erros, tudo o que obtenha é uma página como um dos seguintes:

**Erro de servidor na aplicação '/':**

![Página de erro inúteis](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Ocorreu um erro:**

![Página de erro inúteis](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**O Web site não é possível apresentar a página**

![Página de erro inúteis](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Com frequência a forma mais fácil de encontrar a causa do erro é permitir mensagens de erro detalhada, que o primeiro de capturas de ecrã anteriores explica como fazer. Isso exija uma alteração no arquivo Web. config implantado. Pode editar a *Web. config* de ficheiros no projeto e voltar a implementar o projeto ou criar um [transformação Web. config](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) e implementar uma compilação de depuração, mas há uma maneira mais rápida: no **Explorador de soluções** , pode ver e editar ficheiros na aplicação remota usando diretamente o *vista remota* funcionalidade.

1. Na **Explorador de servidores**, expanda **Azure**, expanda **serviço de aplicações**, expanda o grupo de recursos localizado na sua aplicação e, em seguida, expanda o nó para a sua aplicação.

    Pode ver nós que lhe darão acesso aos ficheiros de conteúdo e arquivos de log do aplicativo.
2. Expanda a **arquivos** nó e clique duas vezes o *Web. config* ficheiro.

    ![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio abre o ficheiro Web. config da aplicação remota e mostra [remoto] junto ao nome do ficheiro na barra de título.
3. Adicione a seguinte linha para o `system.web` elemento:

    `<customErrors mode="Off"></customErrors>`

    ![Edit Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Atualize o browser que está a mostrar a mensagem de erro inúteis e, agora obtém uma mensagem de erro detalhadas, como o exemplo seguinte:

    ![Mensagem de erro detalhada](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (O erro mostrado foi criado, adicionando a linha mostrada em vermelho para *Views\Home\Index.cshtml*.)

Editar o ficheiro Web. config é apenas um exemplo de cenários em que a capacidade de ler e editar ficheiros no seu serviço de aplicações que a solução de problemas.

## <a name="remotedebug"></a>Aplicações de depuração remotas
Se a mensagem de erro detalhada não fornece informações suficientes e não é possível recriar o erro localmente, outra forma de resolução de problemas é executado no modo de depuração remotamente. Pode definir pontos de interrupção, manipular diretamente a memória, percorrer o código e até mesmo alterar o caminho do código.

Depuração remota não funcionam nas edições Express do Visual Studio.

Esta secção mostra como depurar remotamente com o projeto que criar no [criar uma aplicação ASP.NET no App Service do Azure](app-service-web-get-started-dotnet-framework.md).

1. Abra o projeto da web que criou no [criar uma aplicação ASP.NET no App Service do Azure](app-service-web-get-started-dotnet-framework.md).

2. Open *Controllers\HomeController.cs*.

3. Eliminar o `About()` método e insira o seguinte código no seu lugar.

``` c#
public ActionResult About()
{
    string currentTime = DateTime.Now.ToLongTimeString();
    ViewBag.Message = "The current time is " + currentTime;
    return View();
}
```

4. [Defina um ponto de interrupção](https://docs.microsoft.com/visualstudio/debugger/) sobre o `ViewBag.Message` linha.

5. Na **Explorador de soluções**, clique com o botão direito no projeto e clique em **Publish**.

6. Na **perfil** na lista pendente, selecione o mesmo perfil que utilizou na [criar uma aplicação ASP.NET no App Service do Azure](app-service-web-get-started-dotnet-framework.md). Em seguida, clique em definições.

7. Na **Publish** caixa de diálogo, clique no **definições** separador e, em seguida, altere **configuração** para **depurar**e, em seguida, clique em  **Guardar**.

    ![Publicar no modo de depuração](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

8. Clique em **Publicar**. Após a implementação estiver concluída e o browser abre-se para o URL do Azure da sua aplicação, feche o browser.

9. Na **Explorador de servidores**, clique com o botão direito a aplicação e, em seguida, clique em **anexar o depurador**.

    ![Anexar o depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    O browser abre automaticamente a sua home page em execução no Azure. Poderá ter de aguardar 20 segundos mais ou menos, enquanto o Azure configura o servidor para depuração. Este atraso ocorre apenas na primeira vez que executar no modo de depuração num aplicativo num período de 48 horas. Quando começa a depuração novamente no mesmo período, não existe um atraso.

    > [!NOTE] 
    > Se tiver problemas para iniciar o depurador, tentar fazê-lo através de **Cloud Explorer** em vez de **Explorador de servidores**.
    >

10. Clique em **sobre** no menu.

     Visual Studio termina no ponto de interrupção, e o código está em execução no Azure, não no seu computador local.

11. Paire o rato sobre o `currentTime` variável para ver o valor de tempo.

     ![Variável de vista no modo de depuração em execução no Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

     O tempo que vê é a hora do servidor do Azure, que pode ser um fuso horário diferente do que o seu computador local.

12. Introduza um novo valor para o `currentTime` variável, tais como "Agora em execução no Azure".

13. Prima F5, continue a executar.

     A página sobre em execução no Azure mostra o novo valor que introduziu na variável currentTime.

     ![Página sobre com o novo valor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> Depurar remotamente WebJobs
Esta secção mostra como depurar remotamente com o projeto e a aplicação que criar no [começar com o Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

As funcionalidades apresentadas nesta secção estão disponíveis apenas no Visual Studio 2013 com o Update 4 ou posterior.

Depuração remota só funciona com o WebJobs contínuos. WebJobs agendadas e sob demanda não suportar a depuração.

1. Abra o projeto da web que criou no [começar com o SDK de WebJobs do Azure][GetStartedWJ].

2. No projeto ContosoAdsWebJob, abra *Functions.cs*.

3. [Defina um ponto de interrupção](https://docs.microsoft.com/visualstudio/debugger/) na primeira instrução no `GnerateThumbnail` método.

    ![Ponto de interrupção do conjunto](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. Na **Explorador de soluções**, clique com o botão direito no projeto da web (e não o projeto do trabalho Web) e clique em **Publish**.

5. Na **perfil** na lista pendente, selecione o mesmo perfil que utilizou na [começar com o Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Clique nas **definições** separador e altere **configuração** para **depurar**e, em seguida, clique em **publicar**.

    O Visual Studio implementa o web e os projetos de WebJob e o browser abre-se para o URL do Azure da sua aplicação.

7. Na **Explorador de servidores**, expanda **Azure > serviço de aplicações > seu grupo de recursos > a aplicação > WebJobs > contínua**e, em seguida, clique com botão direito **ContosoAdsWebJob**.

8. Clique em **anexar o depurador**.

    ![Anexar o depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    O browser abre automaticamente a sua home page em execução no Azure. Poderá ter de aguardar 20 segundos mais ou menos, enquanto o Azure configura o servidor para depuração. Este atraso ocorre apenas na primeira vez que executar no modo de depuração num aplicativo num período de 48 horas. Quando começa a depuração novamente no mesmo período, não existe um atraso.

9. O navegador da web que é aberto para a home page de anúncios da Contoso, crie um novo anúncio.

    Criar um anúncio faz com que uma mensagem de fila ser criado, o que é captada pelo WebJob e processada. Quando o WebJobs SDK chama a função para processar a mensagem de fila, as visitas de código do ponto de interrupção.

10. Quando o depurador divide em seu ponto de interrupção, pode examinar e alterar valores de variáveis, enquanto o programa está em execução na cloud. Na ilustração seguinte, o depurador mostra o conteúdo do objeto blobInfo que foi passado para o `GenerateThumbnail` método.

     ![objeto blobInfo no depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Prima F5, continue a executar.

     O `GenerateThumbnail` método acabar de criar a miniatura.

12. No browser, atualize a página de índice e ver a miniatura.

13. No Visual Studio, prima SHIFT + F5 para parar a depuração.

14. Na **Explorador de servidores**, clique com o botão direito no nó ContosoAdsWebJob e clique em **vista de Dashboard**.

15. Inicie sessão com as credenciais do Azure e, em seguida, clique no nome de WebJob para ir para a página para o WebJob.

     ![Clique em ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     O Dashboard mostra que o `GenerateThumbnail` função executada recentemente.

     (Na próxima vez que clicar **ver o Dashboard**, não precisa de iniciar sessão e o browser vai diretamente para a página para o WebJob.)

16. Clique no nome de função para ver detalhes sobre a execução de função.

     ![Detalhes de função](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Se a função [escreveu logs](https://github.com/Azure/azure-webjobs-sdk/wiki), pode clicar **ToggleOutput** para vê-los.

## <a name="notes-about-remote-debugging"></a>Notas sobre a depuração remota

* Não é recomendada em execução no modo de depuração na produção. Se a sua aplicação de produção não é aumentar horizontalmente para várias instâncias de servidor, depuração impede que o servidor web de responder a pedidos de outros. Se tiver várias instâncias de servidor web, ao anexar o depurador, obtém uma instância aleatória e não há como garantir que as solicitações do navegador subsequentes Ir para a mesma instância. Além disso, normalmente não implementa uma compilação de depuração para produção e Otimizações do compilador para compilações de versão poderiam tornar impossível Mostrar o que acontece linha por linha no seu código-fonte. Para resolução de problemas de produção, seus melhores recursos são a aplicação web e de rastreio de registos do servidor.
* Evitar longas paradas em pontos de interrupção quando remoto depuração. O Azure trata um processo que está parado durante mais de alguns minutos como um processo sem resposta e encerra-lo.
* Enquanto estiver a depurar, o servidor está a enviar dados para o Visual Studio, que podem afetar os custos de largura de banda. Para obter informações sobre as taxas de largura de banda, consulte [preços do Azure](https://azure.microsoft.com/pricing/calculator/).
* Certifique-se de que o `debug` atributo do `compilation` elemento no *Web. config* arquivo está definido como true. Ele é definido como true por padrão quando publica uma configuração de compilação de depuração.

``` xml
<system.web>
  <compilation debug="true" targetFramework="4.5" />
  <httpRuntime targetFramework="4.5" />
</system.web>
```
* Se achar que o depurador não avance para o código que deseja depurar, poderá ter de alterar a definição de Just My Code.  Para obter mais informações, consulte [Especifique se pretende depurar o código de utilizador apenas usando o Just My Code no Visual Studio](https://docs.microsoft.com/visualstudio/debugger/just-my-code).
* Um temporizador é iniciada no servidor quando ativar a funcionalidade de depuração remota e, após 48 horas a funcionalidade é desativada automaticamente. Este limite de 48 horas é feito por razões de segurança e desempenho. Pode facilmente voltar a ativá a funcionalidade de quantas vezes desejar. É recomendável deixá-lo desativada quando não estiver depurando ativamente.
* Pode anexar manualmente o depurador a qualquer processo, não apenas o processo de aplicação (w3wp.exe). Para obter mais informações sobre como utilizar o modo de depuração no Visual Studio, consulte [depuração no Visual Studio](https://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Descrição geral de registos de diagnóstico
Um aplicativo ASP.NET que é executado num aplicativo de serviço de aplicações, pode criar os seguintes tipos de registos:

* **Registos de rastreio de aplicação**<br/>
  O aplicativo cria estes registos ao chamar métodos para o [Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx) classe.
* **Registos do servidor Web**<br/>
  O servidor web cria uma entrada de registo para cada solicitação HTTP para a aplicação.
* **Registos de mensagem de erro detalhadas**<br/>
  O servidor web cria uma página HTML com algumas informações adicionais para pedidos HTTP falhados (pedidos que resultam num código de estado 400 ou superior).
* **Registos de rastreio de pedidos falhados**<br/>
  O servidor web cria um arquivo XML com informações de rastreamento detalhadas para pedidos HTTP falhados. O servidor web também fornece um arquivo XSL para formatar o XML num navegador.

Registo afeta o desempenho de aplicação, por isso, Azure dá-lhe a capacidade de habilitar ou desabilitar cada tipo de registo, conforme necessário. Para os registos de aplicações, pode especificar que apenas os registos acima de um determinado nível de gravidade devem ser escritos. Quando cria uma nova aplicação, por predefinição todos os registos está desativada.

Os registos são escritos em ficheiros num *LogFiles* pasta no sistema de ficheiros da sua aplicação e estão acessível através de FTP. Registos do servidor Web e registos de aplicações também podem ser escritos para uma conta de armazenamento do Azure. Pode manter um volume maior de registos numa conta de armazenamento que é possível no sistema de arquivos. Está limitado a um máximo de 100 megabytes de registos ao utilizar o sistema de ficheiros. (Os registos de sistema de ficheiros são apenas para retenção de curta duração. Azure elimina os ficheiros de registo antigos para disponibilizar espaço para novos após ter sido atingido o limite.)  

## <a name="apptracelogs"></a>Criar e ver os registos de rastreio de aplicação
Nesta secção, efetue as seguintes tarefas:

* Adicionar declarações de rastreio para o projeto da web que criou no [introdução ao Azure e ASP.NET](app-service-web-get-started-dotnet-framework.md).
* Ver os registos ao executar localmente o projeto.
* Ver os registos à medida que são gerados pela aplicação em execução no Azure.

Para obter informações sobre como criar aplicação registos no WebJobs, consulte [como trabalhar com o armazenamento de filas do Azure com o SDK de WebJobs - como escrever registos](https://github.com/Azure/azure-webjobs-sdk/wiki). As instruções seguintes para ver os registos e controlar como estão armazenados no Azure também se aplicam a registos de aplicações criados por WebJobs.

### <a name="add-tracing-statements-to-the-application"></a>Adicionar declarações de rastreio à aplicação
1. Open *Controllers\HomeController.cs*e substitua o `Index`, `About`, e `Contact` métodos com o código a seguir para adicionar `Trace` instruções e um `using` instrução para `System.Diagnostics`:

```c#
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

2. Adicionar um `using System.Diagnostics;` instrução na parte superior do ficheiro.

### <a name="view-the-tracing-output-locally"></a>Ver a saída de rastreamento localmente
1. Prima F5 para executar a aplicação no modo de depuração.

    O serviço de escuta de rastreio predefinido grava todos os resultados de rastreio para o **saída** janela, juntamente com outra saída de depuração. A ilustração seguinte mostra a saída de declarações de rastreio que adicionou ao `Index` método.

    ![Rastreio na janela de depuração](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Os passos seguintes mostram como ver resultados de rastreio numa página da web, sem compilar no modo de depuração.
2. Abra o ficheiro da aplicação Web. config (aquele localizado na pasta de projeto) e adicione um `<system.diagnostics>` elemento no final do arquivo apenas antes do fechar `</configuration>` elemento:

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

O `WebPageTraceListener` saída de rastreio permite ver ao navegar para `/trace.axd`.
3. Adicionar uma <a href="https://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">elemento de rastreio</a> sob `<system.web>` no arquivo Web. config, como o exemplo seguinte:

``` xml
<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
```       

4. Prima CTRL+F5 para executar a aplicação.
5. Na barra de endereços da janela do navegador, adicione *axd* ao URL e, em seguida, prima Enter (o URL é semelhante ao http://localhost:53370/trace.axd).
6. Sobre o **rastreio de aplicação** página, clique em **ver detalhes** na primeira linha (não a linha BrowserLink).

    ![axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    O **detalhes do pedido** é apresentada a página e, no **informações de rastreio** secção verá a saída de declarações de rastreio que adicionou ao `Index` método.

    ![axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Por predefinição, `trace.axd` só está disponível localmente. Se quiser torná-la disponível a partir de uma aplicação remota, poderia adicionar `localOnly="false"` para o `trace` elemento no *Web. config* ficheiro, conforme mostrado no exemplo a seguir:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    No entanto, permitindo `trace.axd` numa produção a aplicação não é recomendada por motivos de segurança. Nas seções a seguir, verá uma forma mais fácil de ler logs de rastreamento num aplicativo de serviço de aplicações.

### <a name="view-the-tracing-output-in-azure"></a>Ver a saída de rastreamento no Azure
1. Na **Explorador de soluções**, clique com o botão direito no projeto web e clique em **Publish**.
2. Na **publicar Web** caixa de diálogo, clique em **Publish**.

    Depois do Visual Studio publica a atualização, ela abre uma janela do browser para a sua home page (partindo do princípio de que não desmarque **URL de destino** sobre o **ligação** separador).
3. Na **Explorador de servidores**, a sua aplicação com o botão direito e selecione **ver registos de transmissão em fluxo**.

    ![Ver registos de transmissão em fluxo no menu de contexto](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    O **saída** janela mostra que estão ligados ao serviço de transmissão de registos e adiciona uma linha de notificação a cada minuto que vai, sem um log para exibir.

    ![Ver registos de transmissão em fluxo no menu de contexto](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. Na janela do browser que mostra a sua home page da aplicação, clique em **contacto**.

    Dentro de alguns segundos, a saída do nível de erro de rastreio que adicionou para o `Contact` método aparece no **saída** janela.

    ![Rastreio de erro na janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio é mostrar apenas os rastreios de nível de erro pois é a predefinição quando ativar o registo de serviço de monitorização. Quando cria uma nova aplicação de serviço de aplicações, todos os registos está desativada por predefinição, como viu ao abrir a página de definições anteriormente:

    ![Terminar sessão de aplicação](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    No entanto, quando tiver selecionado **ver registos de transmissão em fluxo**, Visual Studio automaticamente alterado **aplicação Logging(File System)** para **erro**, que significa que os registos de nível de erro relatadas. Para ver todos os registos de rastreio, pode alterar esta definição para **verboso**. Quando seleciona um nível de gravidade inferior de erro, também são comunicados todos os registos para níveis de gravidade mais elevados. Então, quando seleciona verboso, também verá informação, aviso e registos de erros.  

5. Na **Explorador de servidores**, clique com o botão direito a aplicação e, em seguida, clique em **ver definições** como fez anteriormente.
6. Alteração **registo de aplicação (sistema de ficheiros)** ao **verboso**e, em seguida, clique em **guardar**.

    ![Definir o nível de rastreio para verboso](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. Na janela do browser que está agora a mostrar sua **contacto** página, clique em **home page**, em seguida, clique em **sobre**e, em seguida, clique em **contacto**.

    Dentro de alguns segundos, o **saída** janela mostra toda sua saída de rastreamento.

    ![Saída de rastreamento detalhado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    Nesta secção, habilitado e desabilitado o registro, utilizando as definições da aplicação. Também pode ativar e desativar os ouvintes de rastreamento, modificando o arquivo Web. config. No entanto, modificar o arquivo Web. config faz com que o domínio de aplicativo reciclar, enquanto a ativar o registo através da configuração de aplicação não faz isso. Se o problema demora muito tempo para reproduzir, ou é intermitente, reciclagem o domínio de aplicativo pode "corrigir" e forçá-lo aguardar até que ele voltar a ocorrer. Ativar os diagnósticos no Azure permite-lhe iniciar imediatamente a capturar informações de erro sem reciclar o domínio de aplicativo.

### <a name="output-window-features"></a>Funcionalidades de janela de saída
O **Microsoft Azure Logs** separador da **saída** janela tem vários botões e uma caixa de texto:

![Registos do separador botões](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Estes efetuar as seguintes funções:

* Limpar o **saída** janela.
* Ativar ou desativar a moldagem do texto.
* Iniciar ou parar a monitorização de registos.
* Especifique que regista a monitorizar.
* Transferir registos.
* Filtre registos com base numa cadeia de caracteres de pesquisa ou uma expressão regular.
* Fechar o **saída** janela.

Se introduzir uma cadeia de pesquisa ou uma expressão regular, o Visual Studio filtra as informações de registo no cliente. Isso significa que pode inserir os critérios depois dos registos são apresentados no **saída** janela e pode alterar os critérios de filtragem sem ter de voltar a gerar os registos.

## <a name="webserverlogs"></a>Ver registos de servidor web
Registos do servidor Web registram todas as atividades HTTP para a aplicação. Para vê-los no **saída** janela, tem de ativá-las para a aplicação e indique ao Visual Studio que deseja monitorizá-las.

1. Na **configuração da aplicação Web do Azure** separador abertos a partir de **Explorador de servidores**, alterar o registo de servidor Web para **no**e, em seguida, clique em **guardar**.

    ![Ativar o registo de servidor web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. Na **saída** janela, clique nas **especifique que registra em log Microsoft Azure para monitorizar** botão.

    ![Especifique que o Azure regista para monitorizar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. Na **opções de registo do Microsoft Azure** caixa de diálogo, selecione **os registos do servidor da Web**e, em seguida, clique em **OK**.

    ![Monitorizar os registos do servidor web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. Na janela do browser que mostra a aplicação, clique em **home page**, em seguida, clique em **sobre**e, em seguida, clique em **contacto**.

    Os registos da aplicação geralmente aparecem primeiros, seguidos de registos de servidor web. Poderá ter de aguardar algum tempo para que os registos a aparecer.

    ![Registos do servidor de Web na janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Por predefinição, quando ativa primeiro a registos do servidor web com o Visual Studio, o Azure escreve os registos para o sistema de ficheiros. Como alternativa, pode utilizar o portal do Azure para especificar que o servidor web registos devem ser escritos para um contentor de BLOBs numa conta de armazenamento.

Se utilizar o portal para ativar o servidor web de registo para uma conta de armazenamento do Azure e, em seguida, desative o registo no Visual Studio, quando voltar a ativar o registo no Visual Studio definições da conta de armazenamento serão restauradas.

## <a name="detailederrorlogs"></a>Ver registos de mensagem de erro detalhadas
Registos de erros detalhados fornecem informações adicionais sobre os pedidos HTTP que resultam em códigos de resposta de erro (400 ou acima). Para vê-los no **saída** janela, terá de ativá-las para a aplicação e indique ao Visual Studio que deseja monitorizá-las.

1. Na **configuração da aplicação Web do Azure** separador abertos a partir de **Explorador de servidores**, alterar **mensagens de erro detalhadas** para **no**e, em seguida, Clique em **guardar**.

    ![Ativar mensagens de erro detalhadas](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Na **saída** janela, clique nas **especifique que registra em log Microsoft Azure para monitorizar** botão.

3. Na **opções de registo do Microsoft Azure** caixa de diálogo, clique em **todos os registos**e, em seguida, clique em **OK**.

    ![Monitorizar todos os registos](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Na barra de endereços da janela do navegador, adicionar um caráter extra ao URL para fazer com que um erro 404 (por exemplo, `http://localhost:53370/Home/Contactx`), e prima Enter.

    Após alguns segundos, o registo de erro detalhada é apresentada no Visual Studio **saída** janela.

    ![Registo de erro detalhada - janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Controlar + clique na ligação para ver a saída de registo formatada num navegador:

    ![Registo de erro detalhada - janela do browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Transferir os registos de sistema de ficheiros
Quaisquer registos de monitorização no **saída** janela também pode ser transferida como um *. zip* ficheiro.

1. Na **saída** janela, clique em **transferir registos de transmissão em fluxo**.

    ![Registos do separador botões](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Explorador de ficheiros é aberto em sua *Downloads* pasta com o ficheiro transferido selecionado.

    ![Ficheiro transferido](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Extrair os *. zip* ficheiros e verá a seguinte estrutura de pastas:

    ![Ficheiro transferido](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Registos de rastreio de aplicação estão na *. txt* arquivos no *LogFiles\Application* pasta.
   * Registos de servidor Web se encontram no *. log* arquivos no *LogFiles\http\RawLogs* pasta. Pode usar uma ferramenta como [Log Parser](https://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) para exibir e manipular esses arquivos.
   * Registos de mensagem de erro detalhadas estão na *. HTML* arquivos no *LogFiles\DetailedErrors* pasta.

    (A *implementações* pasta destina-se os arquivos criados pelo controlo de origem de publicação; ele não tem qualquer coisa relacionada à publicação do Visual Studio. Quanto *Git* pasta é para que os rastreios relacionadas com controlo de origem a publicação e o registo de serviço de transmissão em fluxo de ficheiros.)  

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
Registos de rastreio de pedido falhado são úteis quando precisa entender os detalhes de como o IIS é processar um pedido HTTP, em cenários tais como problemas de autenticação ou de regravação de URL.

Aplicações de serviço de aplicações utilizam a mesma funcionalidade de rastreio de pedido falhado que está disponíveis com o IIS 7.0 e posterior. Não tem acesso para as definições de IIS que configurar quais erros fazer logon, no entanto. Quando ativar o rastreio de pedidos falhados, todos os erros são capturados.

Pode ativar o rastreio de pedidos falhados com o Visual Studio, mas que não é possível vê-las no Visual Studio. Estes registos são arquivos XML. O serviço de registo de transmissão em fluxo monitoriza apenas os ficheiros que são considerados legíveis no modo de texto sem formatação: *. txt*, *. HTML*, e *. log* ficheiros.

Pode ver os registos de rastreio de pedido falhado num navegador diretamente através de FTP ou localmente depois de utilizar uma ferramenta FTP para baixá-los para o computador local. Nesta secção, que irá vê-las num navegador diretamente.

1. Na **Configuration** separador da **aplicação Web do Azure** janela abertos a partir de **Explorador de servidores**, alterar **Failed Request Tracing** para **Nos**e, em seguida, clique em **guardar**.

    ![Ativar o rastreio de pedidos falhados](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Na barra de endereços da janela do browser que mostra a aplicação, adicionar um caráter extra ao URL e clique em Enter para fazer com que um erro 404.

    Isso faz com que um registo de rastreio de pedido falhado a ser criado e os passos seguintes mostram como ver ou transferir o registo.

3. No Visual Studio, no **Configuration** separador da **aplicação Web do Azure** janela, clique em **abrir no Portal de gestão**.

4. Na [portal do Azure](https://portal.azure.com) **definições** para a sua aplicação, clique em **credenciais de implementação**e, em seguida, introduza um novo nome de utilizador e palavra-passe.

    ![Novo nome de utilizador FTP e a palavra-passe](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Quando iniciar sessão, terá de utilizar o nome de utilizador completo com o nome da aplicação como prefixo a ele. Por exemplo, se inserir "myid" como um nome de utilizador e o site é "omeuexemplo", que inicia sessão como "myexample\myid".
    >

5. Na nova janela do browser, aceda ao URL que é apresentado em **nome do anfitrião FTP** ou **nome do anfitrião FTPS** no **descrição geral** página para a sua aplicação.

6. Inicie sessão com as credenciais FTP que criou anteriormente (incluindo o prefixo de nome de aplicação para o nome de utilizador).

    O navegador mostra a pasta raiz da aplicação.

7. Abra o *LogFiles* pasta.

    ![Abrir pasta LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Abra a pasta com o nome W3SVC e ainda um valor numérico.

    ![Abrir pasta de W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    A pasta contém arquivos XML para quaisquer erros que foram registados depois de ativado o rastreio de pedidos falhados e um arquivo XSL que um navegador pode utilizar para formatar o XML.

    ![Pasta de W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Clique no ficheiro XML para o pedido falhado que pretende ver informações de rastreamento para.

    A ilustração seguinte mostra a parte das informações de rastreamento para um erro de exemplo.

    ![Pedidos falhados no browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>Passos Seguintes
Viu como Visual Studio torna mais fácil ver os registos criados por uma aplicação de serviço de aplicações. As secções seguintes fornecem ligações para mais recursos em Tópicos relacionados:

* Resolução de problemas de serviço de aplicações
* Depuração no Visual Studio
* Depuração no Azure remota
* Rastreio em aplicativos ASP.NET
* Analisar registos do servidor web
* Analisar registos de rastreio de pedido falhado
* Depuração de serviços Cloud

### <a name="app-service-troubleshooting"></a>Resolução de problemas de serviço de aplicações
Para obter mais informações sobre resolução de problemas de aplicações no serviço de aplicações do Azure, consulte os seguintes recursos:

* [Como monitorizar aplicações](web-sites-monitor.md)
* [Investigar vazamentos de memória no serviço de aplicações do Azure com o Visual Studio 2013](https://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Mensagem de blogue do Microsoft ALM sobre as funcionalidades do Visual Studio para analisar problemas de memória gerenciada.
* [Ferramentas online do Azure do serviço de aplicações, deve saber sobre](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Mensagem de blogue pela Amit Apple.

Para obter ajuda com uma pergunta específica de resolução de problemas, inicie um thread em um dos Fóruns do seguintes:

* [O Fórum do Azure no site do ASP.NET](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [O Fórum do Azure no MSDN](https://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Depuração no Visual Studio
Para obter mais informações sobre como utilizar o modo de depuração no Visual Studio, consulte [depuração no Visual Studio](https://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) e [dicas de depuração com o Visual Studio 2010](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Depuração no Azure remota
Para obter mais informações sobre depuração remota para aplicações de serviço de aplicações e WebJobs, consulte os seguintes recursos:

* [Introdução para o serviço de aplicação de Azure de depuração remoto](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introdução à parte de remoto depuração App Service do Azure 2 - dentro de depuração remota](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introdução à depuração remota por parte do serviço de aplicações do Azure 3 - ambiente de várias instâncias e o GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs depuração (vídeo)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Se a sua aplicação utiliza um back-end de API Web do Azure ou serviços móveis e terá de efetuar a depuração, consulte [depuração back-end .NET no Visual Studio](https://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Rastreio em aplicativos ASP.NET
Não há nenhum introduções completas e atualizadas para rastreamento do ASP.NET disponíveis na Internet. O melhor que pode fazer é começar com o antigos materiais introdutórios escritos para o Web Forms porque MVC não ainda existe e complementar que com o blog da mais recente publica que se concentram no problemas específicos. Alguns bons lugares para iniciar são os seguintes recursos:

* [Monitorização e telemetria (criação de aplicações na Cloud reais com o Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  Capítulo do livro eletrônico com recomendações para rastreamento em aplicações na cloud do Azure.
* [Rastreamento do ASP.NET](https://msdn.microsoft.com/library/ms972204.aspx)<br/>
  Velha, mas ainda um bom recurso para uma introdução básica para o assunto.
* [Ouvintes de rastreamento](https://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  Informações sobre ouvintes de rastreamento, mas não mencionar a [WebPageTraceListener](https://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Passo a passo: Integração de rastreamento do ASP.NET com o rastreamento de System. Diagnostics](https://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  Este artigo também é antigo, mas inclui algumas informações adicionais que não abrange o artigo introdutório.
* [Rastreio em modos de exibição do ASP.NET MVC Razor](https://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Além de rastreamento em modos de exibição do Razor, a mensagem também explica como criar um filtro de erro para iniciar a sessão de todas as exceções sem tratamento num aplicativo MVC. Para obter informações sobre como registrar exceções, tudo sem tratamento num aplicativo Web Forms, consulte o exemplo de global. asax na [um exemplo completo para manipuladores de erro](https://msdn.microsoft.com/library/bb397417.aspx) no MSDN. No MVC ou Web Forms, se quiser registrar determinadas exceções, mas permitir que a estrutura padrão manipulação vigor para os mesmos, pode ser capturado e gerar novamente como no exemplo seguinte:

``` c#
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

* [Registo a partir da linha de comandos do Azure (mais rápida visualização!) de rastreio de diagnóstico de transmissão em fluxo](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Como utilizar a linha de comandos para fazer o que este tutorial mostra como fazer no Visual Studio. [Antevisão](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) é uma ferramenta para depuração de aplicativos do ASP.NET.
* [Utilizar aplicações Web, registo e diagnóstico - com David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) e [transmissão em fluxo registos de aplicações Web – com David Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Vídeos por Scott Hanselman e David Ebbo.

Para o registo de erro, uma alternativa para escrever seu próprio código de rastreamento é usar uma arquitetura de registo de código-fonte aberto, como [ELMAH](https://nuget.org/packages/elmah/). Para obter mais informações, consulte [mensagens de blogue de Scott Hanselman sobre o ELMAH](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Além disso, não precisa usar o ASP.NET ou `System.Diagnostics` registos de rastreio para obter a transmissão em fluxo do Azure. A aplicação de serviço de aplicações, serviço de registo de transmissão em fluxo transmite em fluxo qualquer *. txt*, *. HTML*, ou *. log* ficheiros encontrados no *LogFiles* pasta. Portanto, poderia criar seu próprio sistema de registo que escreve para o sistema de ficheiros da aplicação e seu arquivo é transmitido em fluxo e transferido automaticamente. Tudo o que precisa fazer é o código de aplicação de escrita que cria arquivos no *d:\home\logfiles* pasta.

### <a name="analyzing-web-server-logs"></a>Analisar registos do servidor web
Para obter mais informações sobre como analisar os registos do servidor web, consulte os seguintes recursos:

* [LogParser](https://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Uma ferramenta para ver os dados nos registos de servidor web (*. log* ficheiros).
* [Resolução de problemas de desempenho do IIS ou erros de aplicações com LogParser](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Uma introdução à ferramenta Analisador de registos que pode usar para analisar registos do servidor web.
* [Mensagens de blogue por Robert McMurray sobre a utilização do LogParser](https://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [O código de estado HTTP no IIS 7.0, IIS 7.5 e IIS 8.0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analisar registos de rastreio de pedido falhado
Site do Microsoft TechNet inclui um [usando Failed Request Tracing](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) seção, que pode ser útil para compreender como utilizar estes registos. No entanto, esta documentação concentra-se principalmente sobre como configurar o rastreio de pedidos falhados no IIS, que não pode ser feito no serviço de aplicações do Azure.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
