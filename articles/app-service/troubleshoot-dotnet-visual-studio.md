---
title: Resolver problemas com o Visual Studio
description: Saiba como resolver problemas com uma aplicação do Serviço de Aplicações utilizando ferramentas de depuração, rastreio e registo remoto que são incorporadas no Visual Studio 2013.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 65eb4e8fefb74219c927fe507baceca7c55462be
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095717"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Resolução de problemas de uma aplicação no Azure App Service usando o Visual Studio
## <a name="overview"></a>Descrição Geral
Este tutorial mostra como usar ferramentas do Visual Studio para ajudar a depurar uma aplicação no Serviço de [Aplicações](./overview.md), executando em [modo de depurar](/visualstudio/debugger/) remotamente ou visualizando registos de aplicações e registos de servidores web.

Irá aprender:

* Quais as funções de gestão de aplicações disponíveis no Visual Studio.
* Como utilizar a visão remota do Visual Studio para fazer mudanças rápidas numa aplicação remota.
* Como executar o modo de depuração remotamente enquanto um projeto está em execução em Azure, tanto para uma aplicação como para um WebJob.
* Como criar registos de rastreios de aplicações e vê-los enquanto a aplicação os está a criar.
* Como visualizar registos de servidores web, incluindo mensagens de erro detalhadas e rastreio de pedidos falhados.
* Como enviar registos de diagnóstico para uma conta de Armazenamento Azure e vê-los lá.

Se tiver o Visual Studio Ultimate, também pode utilizar [o IntelliTrace](/visualstudio/debugger/intellitrace) para depurar. A IntelliTrace não está abrangida por este tutorial.

## <a name="prerequisites"></a><a name="prerequisites"></a>Pré-requisitos
Este tutorial funciona com o ambiente de desenvolvimento, projeto web e app app de Serviço de Aplicações que criou na [Create a ASP.NET app no Azure App Service](quickstart-dotnet-framework.md). Para as secções WebJobs, você precisará da aplicação que cria em [Get Started com o Azure WebJobs SDK][GetStartedWJ].

As amostras de código mostradas neste tutorial são para uma aplicação web C# MVC, mas os procedimentos de resolução de problemas são os mesmos para aplicações Visuais Basic e Web Forms.

O tutorial pressupõe que está a usar o Visual Studio 2019. 

Os registos de streaming apenas funcionam para aplicações que visam o Quadro .NET 4 ou posterior.

## <a name="app-configuration-and-management"></a><a name="sitemanagement"></a>Configuração e gestão de aplicativos
O Visual Studio fornece acesso a um subconjunto das funções de gestão de aplicações e configurações de configuração disponíveis no [portal Azure](https://go.microsoft.com/fwlink/?LinkId=529715). Nesta secção, verá o que está disponível usando **o Server Explorer**. Para ver as mais recentes funcionalidades de integração do Azure, experimente também **o Cloud Explorer.** Pode abrir ambas as janelas do menu **Ver.**

1. Se ainda não tiver assinado no Azure em Visual Studio, clique com o botão direito **Azure** e selecione Connect to **Microsoft Azure Subscription** in **Server Explorer**.

    Uma alternativa é instalar um certificado de gestão que permita o acesso à sua conta. Se optar por instalar um certificado, clique no nó **Azure** no **Server Explorer** e, em seguida, selecione **'Gerir e Filtrar Assinaturas'** no menu de contexto. Na caixa de diálogo **de assinaturas Do Microsoft Azure,** clique no separador **Certificados** e, em seguida, clique em **Importar**. Siga as instruções para descarregar e, em seguida, importe um ficheiro de subscrição (também chamado de ficheiro *.publishsettings)* para a sua conta Azure.

   > [!NOTE]
   > Se descarregar um ficheiro de subscrição, guarde-o para uma pasta fora dos seus diretórios de código fonte (por exemplo, na pasta Downloads) e, em seguida, elimine-o uma vez que a importação esteja concluída. Um utilizador malicioso que obtenha acesso ao ficheiro de subscrição pode editar, criar e eliminar os seus serviços Azure.
   >
   >

    Para obter mais informações sobre a ligação aos recursos da Azure a partir do Visual Studio, consulte [as funções De Assign Azure utilizando o portal Azure](../role-based-access-control/role-assignments-portal.md).
2. No **Server Explorer,** expanda **o Azure** e expanda o **Serviço de Aplicações**.
3. Expandir o grupo de recursos que inclui a aplicação que criou na [Criar uma aplicação ASP.NET no Azure App Service](quickstart-dotnet-framework.md), e depois clicar com o direito no nó da aplicação e clicar em **Definições de Visualização.**

    ![Ver Definições no Explorador de Servidor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    O separador **Azure Web App** aparece e pode ver lá as tarefas de gestão e configuração de aplicações que estão disponíveis no Visual Studio.

    ![Janela da App Azure Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    Neste tutorial, você usará o registo e rastreando drop-downs. Também utilizará depuragem remota, mas utilizará um método diferente para o ativar.

    Para obter informações sobre as configurações de aplicações e as caixas de cordas de conexão nesta janela, consulte [o Serviço de Aplicações Azure: Como funcionam as cordas de aplicação e as cordas de ligação](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Se pretender executar uma tarefa de gestão de aplicações que não pode ser feita nesta janela, clique **em Abrir no Portal de Gestão** para abrir uma janela do navegador para o portal Azure.

## <a name="access-app-files-in-server-explorer"></a><a name="remoteview"></a>Aceder a ficheiros de aplicativos no Server Explorer
Normalmente, implementa-se um projeto web com a `customErrors` bandeira na Web.config de ficheiros definidos para ou , o que significa que não recebe uma `On` `RemoteOnly` mensagem de erro útil quando algo corre mal. Para muitos erros, tudo o que obtém é uma página como uma das seguintes:

**Erro do servidor na aplicação '/' :**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png" alt-text="Screenshot mostrando um erro do servidor em erro de aplicação '/' num navegador web.":::

**Ocorreu um erro:**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png" alt-text="Screenshot mostrando um exemplo de um erro genérico ocorrido num navegador web.":::

**O site não pode exibir a página**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png" alt-text="Screenshot mostrando um O site não pode exibir o erro da página num navegador web.":::

Frequentemente, a forma mais fácil de encontrar a causa do erro é ativar mensagens de erro detalhadas, o que a primeira das imagens anteriores explica como fazer. Isso requer uma alteração no ficheiro Web.config implantado. Pode editar o ficheiro *Web.config* no projeto e recolocar o projeto, ou criar uma [Web.config transformar](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) e implementar uma construção de depuração, mas há uma forma mais rápida: no **Solution Explorer,** pode visualizar e editar diretamente ficheiros na aplicação remota utilizando a funcionalidade *de visualização remota.*

1. No **Server Explorer**, expanda o **Azure,** expanda o **Serviço de Aplicações,** expanda o grupo de recursos em que a sua aplicação está localizada e, em seguida, expanda o nó para a sua aplicação.

    Você vê nós que lhe dão acesso aos ficheiros de conteúdo da aplicação e ficheiros de registo.
2. Expanda o nó **de Ficheiros** e clique duas vezes no ficheiro *Web.config.*

    ![Abrir Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    O Visual Studio abre o ficheiro Web.config a partir da aplicação remota e mostra [Remote] ao lado do nome do ficheiro na barra de títulos.
3. Adicione a seguinte linha ao `system.web` elemento:

    `<customErrors mode="Off"></customErrors>`

    ![Editar Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Refresque o navegador que está a mostrar a mensagem de erro inútil e agora obtém uma mensagem de erro detalhada, tal como o seguinte exemplo:

    ![Mensagem de erro detalhada](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (O erro mostrado foi criado adicionando a linha mostrada a vermelho a *Views\Home\Index.cshtml*.)

Editar o ficheiro Web.config é apenas um exemplo de cenários em que a capacidade de ler e editar ficheiros na sua aplicação de Serviço de Aplicações facilita a resolução de problemas.

## <a name="remote-debugging-apps"></a><a name="remotedebug"></a>Aplicativos de depuragem remoto
Se a mensagem de erro detalhada não fornecer informações suficientes e não puder recriar o erro localmente, outra forma de resolver problemas é funcionar remotamente no modo de depuração. Pode definir pontos de rutura, manipular a memória diretamente, passar pelo código e até alterar a via do código.

A depuragem remota não funciona nas edições express do Visual Studio.

Esta secção mostra como depurar remotamente usando o projeto que cria na [Create a ASP.NET app no Azure App Service](quickstart-dotnet-framework.md).

1. Abra o projeto web que criou na [Create a ASP.NET app no Azure App Service](quickstart-dotnet-framework.md).

1. *Controladores abertos\HomeController.cs*.

1. Elimine o `About()` método e insira o seguinte código no seu lugar.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. [Coloque um ponto de rutura](/visualstudio/debugger/) na `ViewBag.Message` linha.

1. No **Solution Explorer,** clique com o botão direito no projeto e clique em **Publicar**.

1. Na  lista de drop-down profile, selecione o mesmo perfil que utilizou na [Criar uma aplicação ASP.NET no Azure App Service](quickstart-dotnet-framework.md). Em seguida, clique em Definições.

1. No diálogo **publicar,** clique no separador **Definições** e, em seguida, **altere a Configuração** para **Debug** e, em seguida, clique em **Guardar**.

    ![Publicar em modo de depurar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Clique em **Publish** (Publicar). Após a implementação terminar e o seu navegador abre para o URL Azure da sua aplicação, feche o navegador.

1. No **Server Explorer,** clique com o botão direito na sua aplicação e, em seguida, clique **em Anexar Debugger**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png" alt-text="Screenshot da janela Do Explorador do Servidor mostrando uma aplicação selecionada e, em seguida, clicando em Anexar Debugger.":::

    O navegador abre automaticamente para a sua página inicial em execução em Azure. Pode ter de esperar 20 segundos ou mais enquanto o Azure configura o servidor para depuração. Este atraso só acontece na primeira vez que corre em modo depuramento numa aplicação num período de 48 horas. Quando se começa a depurar de novo no mesmo período, não há um atraso.

    > [!NOTE] 
    > Se tiver algum problema em iniciar o depurante, tente fazê-lo usando **o Cloud Explorer** em vez do Server **Explorer**.
    >

1. Clique  no menu.

    O Visual Studio para no ponto de rutura, e o código está a funcionar em Azure, não no computador local.

1. Pairar sobre a `currentTime` variável para ver o valor do tempo.

    ![Ver variável no modo de depuração em execução em Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    A hora que vê é a hora do servidor Azure, que pode estar num fuso horário diferente do computador local.

1. Introduza um novo valor para a `currentTime` variável, como "Agora a correr em Azure".

1. Pressione F5 para continuar a correr.

     A página Sobre a execução em Azure exibe o novo valor que introduziu na variável Tempo atual.

     ![Sobre página com novo valor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remote-debugging-webjobs"></a><a name="remotedebugwj"></a> WebJobs de depuragem remota
Esta secção mostra como depurar remotamente usando o projeto e app que cria em [Get Started com o Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

As funcionalidades mostradas nesta secção só estão disponíveis no Visual Studio 2013 com a Atualização 4 ou posterior.

A depuragem remota funciona apenas com WebJobs contínuos. WebJobs agendados e a pedido não suportam depurações.

1. Abra o projeto web que criou em [Get Started com o Azure WebJobs SDK][GetStartedWJ].

2. No projeto ContosoAdsWebJob, open *Functions.cs*.

3. [Desaponte um ponto de rutura](/visualstudio/debugger/) na primeira declaração do `GnerateThumbnail` método.

    ![Definir ponto de rutura](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. No **Solution Explorer,** clique com o botão direito no projeto web (não no projeto WebJob) e clique em **Publicar**.

5. Na  lista de drop-down profile, selecione o mesmo perfil que usou em [Get Started com o Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Clique no separador **Definições** e altere **a Configuração** para **Debug** e, em seguida, clique em **Publicar**.

    O Visual Studio implementa os projetos web e WebJob, e o seu navegador abre-se para o URL Azure da sua aplicação.

7. No **Server Explorer**, expanda o **Azure > App Service > o seu grupo de recursos > a sua aplicação > WebJobs > Continuous**, e depois clique com o botão direito **ContosoAdsWebJob**.

8. Clique **em Anexar Debugger**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png" alt-text="Screenshot do Server Explorer mostrando ContosoAdsWebJob selecionado no menu suspenso e anexar Debugger selecionado.":::

    O navegador abre automaticamente para a sua página inicial em execução em Azure. Pode ter de esperar 20 segundos ou mais enquanto o Azure configura o servidor para depuração. Este atraso só acontece na primeira vez que corre em modo depuramento numa aplicação num período de 48 horas. Quando se começa a depurar de novo no mesmo período, não há um atraso.

9. No navegador web que é aberto para a página inicial dos Contoso Ads, crie um novo anúncio.

    A criação de um anúncio faz com que seja criada uma mensagem de fila, que é captada pelo WebJob e processada. Quando o WebJobs SDK chama a função para processar a mensagem de fila, o código atinge o seu ponto de rutura.

10. Quando o depurador quebra no seu ponto de rutura, pode examinar e alterar valores variáveis enquanto o programa está a executar a nuvem. Na seguinte ilustração, o depurador mostra o conteúdo do objeto blobInfo que foi passado para o `GenerateThumbnail` método.

     ![blobInfo objeto em debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Pressione F5 para continuar a correr.

     O `GenerateThumbnail` método termina criando a miniatura.

12. No navegador, refresque a página 'Índice' e veja a miniatura.

13. No Visual Studio, prima SHIFT+F5 para parar de depurar.

14. No **Server Explorer,** clique à direita no nó ContosoAdsWebJob e clique **em Ver Painel de Instrumentos**.

15. Inscreva-se com as suas credenciais Azure e, em seguida, clique no nome WebJob para ir à página para o seu WebJob.

     ![Clique em ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     O Painel de Instrumentos mostra que a `GenerateThumbnail` função executada recentemente.

     (Da próxima vez que clicar em **Ver Painel,** não tem de iniciar sposição e o navegador vai diretamente para a página do seu WebJob.)

16. Clique no nome da função para ver detalhes sobre a execução da função.

     ![Detalhes da função](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Se a sua função [escreveu registos,](https://github.com/Azure/azure-webjobs-sdk/wiki)pode clicar em **ToggleOutput** para os ver.

## <a name="notes-about-remote-debugging"></a>Notes about remote debugging (Notas sobre a depuração remota)

* Não é aconselhável correr em modo de depuração na produção. Se a sua aplicação de produção não for dimensionada para várias instâncias de servidor, a depuragem impede que o servidor web responda a outros pedidos. Se tiver várias instâncias do servidor web, quando se anexa ao depurar, obtém-se uma instância aleatória e não tem como garantir que os pedidos subsequentes do navegador vão para a mesma instância. Além disso, normalmente não se implanta uma construção de depuração para a produção, e as otimizações de compilador para construções de lançamento podem tornar impossível mostrar o que está a acontecer linha a linha no seu código fonte. Para resolver problemas de produção, o seu melhor recurso é o rastreio de aplicações e registos de servidores web.
* Evite paragens longas em pontos de rutura quando estiver a depurar remotamente. Azure trata um processo que é parado por mais de alguns minutos como um processo sem resposta, e desliga-o.
* Enquanto está a depurar, o servidor está a enviar dados para o Visual Studio, o que pode afetar as taxas de largura de banda. Para obter informações sobre taxas de largura de banda, consulte [a Azure Pricing](https://azure.microsoft.com/pricing/calculator/).
* Certifique-se de que `debug` o atributo do elemento no ficheiro `compilation` *Web.config* está definido como verdadeiro. É definido como verdadeiro por padrão quando publica uma configuração de construção de depuração.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Se descobrir que o depurador não entra no código que pretende depurar, poderá ter de alterar a definição Just My Code.  Para obter mais informações, consulte [Especificar se deve depurar apenas o código do utilizador utilizando o Just My Code in Visual Studio](/visualstudio/debugger/just-my-code).
* Um temporizador começa no servidor quando ativa a função de depuragem remota e, após 48 horas, a funcionalidade é automaticamente desligada. Este limite de 48 horas é feito por razões de segurança e desempenho. Pode facilmente voltar a ligar o recurso quantas vezes quiser. Recomendamos deixá-lo desativado quando não estiver a depurar ativamente.
* Pode anexar manualmente o depurar a qualquer processo, não só o processo de aplicação (w3wp.exe). Para obter mais informações sobre como usar o modo de depuragem no Estúdio Visual, consulte [Debugging no Visual Studio](/visualstudio/debugger/debugging-in-visual-studio).

## <a name="diagnostic-logs-overview"></a><a name="logsoverview"></a>Visão geral dos registos de diagnóstico
Uma aplicação ASP.NET que funciona numa aplicação do Serviço de Aplicações pode criar os seguintes tipos de registos:

* **Registos de rastreio de aplicações**<br/>
  A aplicação cria estes registos através da chamada de métodos da classe [System.Diagnostics.Trace.](/dotnet/api/system.diagnostics.trace)
* **Registos de servidores web**<br/>
  O servidor web cria uma entrada de registo para cada pedido HTTP para a aplicação.
* **Registos de mensagens de erro detalhadas**<br/>
  O servidor web cria uma página HTML com algumas informações adicionais para pedidos HTTP falhados (pedidos que resultam em código de estado 400 ou superior).
* **Registos de rastreio de pedidos falhados**<br/>
  O servidor web cria um ficheiro XML com informações detalhadas de rastreio para pedidos HTTP falhados. O servidor web também fornece um ficheiro XSL para formatar o XML num browser.

O registo de registos afeta o desempenho da aplicação, pelo que o Azure dá-lhe a capacidade de ativar ou desativar cada tipo de registo conforme necessário. Para registos de aplicações, pode especificar que apenas os registos acima de um determinado nível de gravidade devem ser escritos. Quando cria uma nova aplicação, por padrão, todas as explorações madeireiras são desativadas.

Os registos são escritos para ficheiros numa pasta *LogFiles* no sistema de ficheiros da sua aplicação e estão acessíveis via FTP. Os registos de servidores web e os registos de aplicações também podem ser escritos numa conta de Armazenamento Azure. Pode reter um maior volume de registos numa conta de armazenamento do que é possível no sistema de ficheiros. Está limitado a um máximo de 100 megabytes de registos quando utiliza o sistema de ficheiros. (Os registos do sistema de ficheiros são apenas para retenção a curto prazo. O Azure elimina ficheiros de registo antigos para dar espaço aos novos após o limite ser atingido.)  

## <a name="create-and-view-application-trace-logs"></a><a name="apptracelogs"></a>Criar e visualizar registos de vestígios de aplicações
Nesta secção, faz as seguintes tarefas:

* Adicione declarações de rastreio ao projeto web que criou em [Get start with Azure and ASP.NET](quickstart-dotnet-framework.md).
* Veja os registos quando executar o projeto localmente.
* Ver os registos tal como são gerados pela aplicação em execução em Azure.

Para obter informações sobre como criar registos de aplicações em WebJobs, consulte [Como trabalhar com o armazenamento da fila Azure utilizando o WebJobs SDK - Como escrever registos](https://github.com/Azure/azure-webjobs-sdk/wiki). As seguintes instruções para visualizar registos e controlar a forma como são armazenados no Azure aplicam-se também aos registos de aplicações criados pela WebJobs.

### <a name="add-tracing-statements-to-the-application"></a>Adicionar declarações de rastreio à aplicação
1. *Controladores abertos\HomeController.cs*, e substituir o `Index` , e `About` `Contact` métodos pelo seguinte código, a fim de adicionar `Trace` declarações e uma declaração `using` `System.Diagnostics` para:

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

1. Adicione uma `using System.Diagnostics;` declaração ao topo do ficheiro.

### <a name="view-the-tracing-output-locally"></a>Ver a saída de rastreio local
1. Prima F5 para executar a aplicação no modo depuragem.

    O ouvinte de traços predefinidos escreve toda a saída de vestígios para a janela **de saída,** juntamente com outra saída de Debug. A seguinte ilustração mostra a saída das declarações de traços que adicionou ao `Index` método.

    ![Rastreio na janela de Debug](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Os passos a seguir mostram como visualizar a saída de vestígios numa página web, sem compilar em modo depuração.
1. Abra a aplicação Web.config ficheiro (o localizado na pasta do projeto) e adicione um `<system.diagnostics>` elemento na extremidade do ficheiro pouco antes do elemento de `</configuration>` fecho:

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

`WebPageTraceListener`Permite-lhe visualizar a saída de vestígios navegando para `/trace.axd` .
1. Adicione um <a href="/previous-versions/dotnet/netframework-4.0/6915t83k(v=vs.100)">elemento de rastreio</a> no ficheiro `<system.web>` Web.config, tal como o seguinte exemplo:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Prima CTRL+F5 para executar a aplicação.
1. Na barra de endereço da janela do navegador, adicione *trace.axd* ao URL e, em seguida, pressione Enter (o URL é semelhante a `http://localhost:53370/trace.axd` ).
1. Na página **'Rastreio de Aplicações',** clique em **Ver Detalhes** na primeira linha (não na linha BrowserLink).

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png" alt-text="Screenshot da página 'Trace' de aplicações num navegador web que mostra detalhes de visualização selecionados na primeira linha.":::

    A página **'Detalhes do Pedido'** aparece e na secção **De Informação de Rastreio** vê a saída a partir das declarações de traços que adicionou ao `Index` método.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png" alt-text="Screenshot da página 'Detalhes do Pedido' num navegador web mostrando uma mensagem realçada na secção De Informação de Rastreio.":::

    Por padrão, `trace.axd` só está disponível localmente. Se quiser disponibilizá-lo a partir de uma aplicação remota, pode adicionar `localOnly="false"` ao elemento do ficheiro `trace` *Web.config,* como mostra o seguinte exemplo:

    ```xml
    <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />
    ```

    No entanto, a ativação `trace.axd` numa aplicação de produção não é recomendada por razões de segurança. Nas secções seguintes, verá uma forma mais fácil de ler registos de rastreio numa aplicação do Serviço de Aplicações.

### <a name="view-the-tracing-output-in-azure"></a>Ver a saída de rastreio em Azure
1. No **Solution Explorer,** clique com o botão direito no projeto web e clique em **Publicar**.
2. Na caixa de diálogo **da Web,** clique em **Publicar**.

    Depois de o Visual Studio publicar a sua atualização, abre uma janela do navegador para a sua página inicial (assumindo que não limpou o **URL de destino** no separador **'Ligação').**
3. No **Server Explorer,** clique com o botão direito da sua aplicação e selecione **Ver Registos de Streaming**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png" alt-text="Screenshot do Server Explorer depois de clicar à direita na sua aplicação, com os Registos de Streaming de Visualização selecionados numa nova janela.":::

    A janela **saída** mostra que está ligado ao serviço de streaming de registos e adiciona uma linha de notificação a cada minuto que passa sem um registo para visualizar.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png" alt-text="Screenshot da janela saída mostrando um exemplo de uma ligação a um serviço de streaming de registo com linhas de notificação.":::

4. Na janela do navegador que mostra a página inicial da sua aplicação, clique em **Contacto**.

    Em poucos segundos, a saída do nível de erro que adicionou ao `Contact` método aparece na janela **saída.**

    ![Traço de erro na janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    O Visual Studio só mostra vestígios de nível de erro, porque é a definição predefinição quando ativa o serviço de monitorização de registos. Quando cria uma nova aplicação de Serviço de Aplicações, todo o registo é desativado por padrão, como viu quando abriu a página de definições mais cedo:

    ![Início de Sessão de Aplicação](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    No entanto, quando selecionou **Ver Registos de Streaming**, o Estúdio Visual alterou automaticamente o Registo de **Aplicações (Sistema de Ficheiros)** para **Erro**, o que significa que os registos de nível de erro são reportados. Para ver todos os seus registos de rastreio, pode alterar esta definição para **Verbose**. Quando seleciona um nível de gravidade inferior ao erro, todos os registos para níveis de severidade mais elevados também são reportados. Assim, quando seleciona verbose, também vê informações, avisos e registos de erros.  

5. No **Server Explorer,** clique à direita na aplicação e, em seguida, clique em **Ver Definições** como fez anteriormente.
6. Alterar **o registo de aplicações (Sistema de Ficheiros)** para **Verbose** e, em seguida, clicar em **Guardar**.

    ![Definição do nível de traço para Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. Na janela do navegador que está agora a mostrar a sua página **de Contacto,** clique em **'Casa'** e, em seguida, clique em **Cerca** de ' **Contacto'** e, em seguida, clique em Contacto .

    Em poucos segundos, a janela **de saída** mostra toda a sua saída de rastreio.

    ![Saída de vestígios verboso](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    Nesta secção, ativou e desativou o registo através da utilização das definições de aplicações. Também pode ativar e desativar os ouvintes, modificando o ficheiro Web.config. No entanto, modificar o ficheiro Web.config faz com que o domínio da aplicação recicle, ao mesmo tempo que permitir o registo através da configuração da aplicação não o faz. Se o problema demorar muito tempo a reproduzir, ou for intermitente, a reciclagem do domínio da aplicação pode "corrigi-lo" e forçá-lo a esperar até que volte a acontecer. Ativar diagnósticos em Azure permite-lhe começar a capturar informações de erro imediatamente sem reciclar o domínio da aplicação.

### <a name="output-window-features"></a>Características da janela de saída
O **separador Microsoft Azure Logs** da Janela de **Saída** tem vários botões e uma caixa de texto:

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png" alt-text="Screenshot mostrando os botões e a caixa de texto do separador Microsoft Azure Logs na janela saída.":::

Estas desempenham as seguintes funções:

* Limpe a janela **de saída.**
* Ativar ou desativar o embrulho de palavras.
* Iniciar ou parar de monitorizar os registos.
* Especificar quais os registos a monitorizar.
* Baixar registos.
* Registos de filtros baseados numa cadeia de pesquisa ou numa expressão regular.
* Feche a janela **de saída.**

Se introduzir uma cadeia de pesquisa ou expressão regular, o Visual Studio filtra a informação de registo de informação no cliente. Isto significa que pode introduzir os critérios depois de os registos serem apresentados na janela **saída** e pode alterar critérios de filtragem sem ter de regenerar os registos.

## <a name="view-web-server-logs"></a><a name="webserverlogs"></a>Ver registos de servidores web
Os registos de servidores web registam toda a atividade HTTP para a aplicação. Para vê-los na janela **saída,** deve ative-los para a aplicação e dizer ao Visual Studio que pretende monitorá-los.

1. No separador **Azure Web App Configuration Que** abriu a partir do Server **Explorer**, altere o Web Server Logging para **On** e, em seguida, clique em **Guardar**.

    ![Ativar a sessão de registo do servidor web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. Na janela **de saída,** clique na **especificação que o Microsoft Azure regista para monitorizar o** botão.

    ![Especificar quais registos Azure para monitorizar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. Na caixa de diálogo **de opções de registo do Microsoft Azure,** selecione **os registos do servidor Web** e, em seguida, clique em **OK**.

    ![Monitorize registos de servidores web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. Na janela do navegador que mostra a aplicação, clique em **Casa,** clique em **Cerca** de e, em seguida, clique em **Contacto**.

    Os registos de aplicações geralmente aparecem primeiro, seguidos pelos registos do servidor web. Talvez tenha que esperar um pouco para que os registos apareçam.

    ![Registos de servidor web na janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Por predefinição, quando ativa os registos do servidor web utilizando o Visual Studio, o Azure escreve os registos para o sistema de ficheiros. Como alternativa, pode utilizar o portal Azure para especificar que os registos de servidores web devem ser escritos num recipiente de bolhas numa conta de armazenamento.

Se utilizar o portal para ativar o registo do servidor web numa conta de armazenamento Azure e, em seguida, desativar o registo no Visual Studio, quando voltar a ativar o registo no Estúdio Visual, as definições de conta de armazenamento são restauradas.

## <a name="view-detailed-error-message-logs"></a><a name="detailederrorlogs"></a>Ver registos de mensagens de erro detalhadas
Os registos de erros detalhados fornecem algumas informações adicionais sobre pedidos HTTP que resultam em códigos de resposta a erros (400 ou mais). Para vê-los na janela **saída,** tem de os ativar para a aplicação e dizer ao Visual Studio que pretende monitorá-los.

1. No separador **Azure Web App Configuration Que** abriu a partir do Explorador do **Servidor,** **altere mensagens de erro detalhadas** para On e, **em** seguida, clique em **Guardar**.

    ![Ativar mensagens de erro detalhadas](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Na janela **de saída,** clique na **especificação que o Microsoft Azure regista para monitorizar o** botão.

3. Na caixa de diálogo **de opções de registo do Microsoft Azure,** clique em **Todos os registos** e, em seguida, clique em **OK**.

    ![Monitorize todos os registos](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Na barra de endereços da janela do navegador, adicione um carácter extra ao URL para causar um erro de 404 (por exemplo, `http://localhost:53370/Home/Contactx` ) e prima Enter.

    Após vários segundos, o registo de erro detalhado aparece na janela **de saída** do Estúdio Visual.

    ![Registo de erros detalhado - Janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Control+clique no link para ver a saída de registo formatada num browser:

    ![Registo de erro detalhado - janela do navegador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="download-file-system-logs"></a><a name="downloadlogs"></a>Baixar registos do sistema de ficheiros
Quaisquer registos que possa monitorizar na janela **Saída** também podem ser descarregados como um ficheiro *.zip.*

1. Na janela **saída,** clique em **Baixar Registos de Streaming**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png" alt-text="Screenshot da janela de saída que mostra o botão de Registos de Streaming de Descarregamento realçado.":::

    O File Explorer abre para a pasta *Downloads* com o ficheiro descarregado selecionado.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png" alt-text="Screenshot da pasta Downloads no File Explorer com um ficheiro descarregado selecionado.":::

2. Extraia o ficheiro *.zip* e vê a seguinte estrutura de pasta:

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png" alt-text="Screenshot da estrutura da pasta de ficheiros .zip depois de o ficheiro ter sido extraído.":::

   * Os registos de rastreio de aplicações estão em *.txt* ficheiros na pasta *LogFiles\Application.*
   * Os registos de servidores web estão em *.log* ficheiros na pasta *LogFiles\http\RawLogs.* Pode utilizar uma ferramenta como [Log Parser](https://www.iis.net/downloads/community/2010/04/log-parser-22) para visualizar e manipular estes ficheiros.
   * Os registos de mensagens de erro detalhados estão *.html* ficheiros na pasta *LogFiles\DetailedErrors.*

     (A pasta *de implementações* destina-se a ficheiros criados pela publicação de controlo de fontes; não tem nada relacionado com a publicação do Visual Studio. A pasta *Git destina-se* a vestígios relacionados com a publicação de controlo de origem e o serviço de streaming de ficheiros de registo.)  

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
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-account-create.md).
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
## <a name="view-failed-request-tracing-logs"></a><a name="failedrequestlogs"></a>Ver registos de rastreio de pedidos falhados
Os registos de rastreio de pedidos falhados são úteis quando precisa de compreender os detalhes de como o IIS está a lidar com um pedido HTTP, em cenários como a reescrita de URL ou problemas de autenticação.

As aplicações do Serviço de Aplicações utilizam a mesma funcionalidade de rastreio de pedidos falhados que tem sido disponibilizada com o IIS 7.0 e mais tarde. No entanto, não tem acesso às definições do IIS que configuram quais os erros que são registados. Quando ativa o rastreio de pedidos falhados, todos os erros são capturados.

Pode ativar o rastreio de pedidos falhados utilizando o Visual Studio, mas não pode vê-los no Visual Studio. Estes registos são ficheiros XML. O serviço de registo de streaming apenas monitoriza ficheiros que são considerados legíveis em modo de texto simples:  *.txt*, *.html* e *.log* ficheiros.

Pode visualizar registos de rastreio de pedidos falhados num navegador diretamente via FTP ou localmente depois de utilizar uma ferramenta FTP para os transferir para o seu computador local. Nesta secção, você irá vê-los diretamente em um navegador.

1. No separador **Configuração** da janela **Azure Web App** que abriu a partir do Explorador do **Servidor,** **altere o Rastreio** de Pedido Falhado para On e, **em** seguida, clique em **Guardar**.

    ![Permitir rastreio de pedidos falhados](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Na barra de endereços da janela do navegador que mostra a aplicação, adicione um carácter extra ao URL e clique em Enter para causar um erro de 404.

    Isto faz com que seja criado um registo de rastreio de pedidos falhado, e os passos seguintes mostram como visualizar ou descarregar o registo.

3. No Estúdio Visual, no separador **Configuração** da janela **Azure Web App,** clique **em Abrir no Portal de Gestão**.

4. Na página **de Definições** do [portal Azure](https://portal.azure.com) para a sua aplicação, clique em **Credenciais de Implementação** e, em seguida, introduza um novo nome de utilizador e palavra-passe.

    ![Novo nome de utilizador FTP e senha](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Ao iniciar sessão, tem de utilizar o nome de utilizador completo com o nome da aplicação pré-fixado. Por exemplo, se introduzir "myid" como nome de utilizador e o site for "myexample", faça login como "myexample\myid".
    >

5. Numa nova janela do navegador, aceda ao URL que é mostrado no **nome anfitrião FTP** ou **no nome de anfitrião FTPS** na página **geral** da sua aplicação.

6. Inscreva-se utilizando as credenciais FTP que criou anteriormente (incluindo o prefixo do nome da aplicação para o nome de utilizador).

    O navegador mostra a pasta raiz da aplicação.

7. Abra a pasta *'Ficheiros de Registo'.*

    ![Abrir pasta 'Ficheiros de Registo'](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Abra a pasta que se chama W3SVC mais um valor numérico.

    ![Abrir pasta W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    A pasta contém ficheiros XML para quaisquer erros que tenham sido registados após o rastreio de pedido falhado, e um ficheiro XSL que um navegador pode usar para formatar o XML.

    ![Pasta W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Clique no ficheiro XML para o pedido falhado para o qual deseja ver informações de rastreio.

    A seguinte ilustração mostra parte das informações de rastreio para um erro de amostra.

    ![Rastreio de pedido falhado no navegador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="next-steps"></a><a name="nextsteps"></a>Próximos Passos
Já viu como o Visual Studio facilita a visualização de registos criados por uma aplicação de Serviço de Aplicações. As seguintes secções fornecem ligações a mais recursos sobre temas relacionados:

* Resolução de problemas do Serviço de Aplicações
* Debugging no Estúdio Visual
* Depuração remota em Azure
* Rastreio em aplicações ASP.NET
* Analisar registos de servidores web
* Analisar registos de rastreio de pedidos falhados
* Serviços de Depuring Cloud

### <a name="app-service-troubleshooting"></a>Resolução de problemas do Serviço de Aplicações
Para obter mais informações sobre aplicações de resolução de problemas no Azure App Service, consulte os seguintes recursos:

* [Como monitorizar apps](web-sites-monitor.md)
* [Investigando leaks de memória em Azure App Service com Visual Studio 2013](https://devblogs.microsoft.com/devops/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013/). Post de blog da Microsoft ALM sobre funcionalidades do Visual Studio para analisar problemas de memória geridos.
* [Ferramentas on-line Azure App Service que deve conhecer.](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/) Blog post by Amit Apple.

Para obter ajuda com uma questão específica de resolução de problemas, inicie um fio num dos seguintes fóruns:

* [O fórum Azure no site ASP.NET.](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET)
* [O fórum Azure no Microsoft Q&A](/answers/topics/azure-webapps.html).
* [StackOverflow.com.](https://www.stackoverflow.com)

### <a name="debugging-in-visual-studio"></a>Debugging no Estúdio Visual
Para obter mais informações sobre como usar o modo de depuragem no Visual Studio, consulte [Debugging em Visual Studio](/visualstudio/debugger/debugging-in-visual-studio) e [Debugging Tips com Visual Studio 2010](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Depuração remota em Azure
Para obter mais informações sobre depurações remotas para aplicações do Serviço de Aplicações e WebJobs, consulte os seguintes recursos:

* [Introdução ao Serviço de Aplicações Azure de depuração remota](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introdução ao Serviço de Aplicações Azure desormamento remoto parte 2 - Depuração remota](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introdução ao Debugging Remoto na parte 3 do Serviço de Aplicações Azure - Ambiente multi-instância e GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs Debugging (vídeo)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Se a sua aplicação utilizar um API web AZure ou serviços móveis de volta e precisar de depurar isso, consulte [Debugging .NET Backend no Visual Studio](/archive/blogs/azuremobile/debugging-net-backend-in-visual-studio).

### <a name="tracing-in-aspnet-applications"></a>Rastreio em aplicações ASP.NET
Não existem apresentações minuciosas e atualizadas para ASP.NET rastreio disponível na Internet. O melhor que pode fazer é começar com materiais introdutórios antigos escritos para Web Forms porque MVC ainda não existia, e complementá-lo com publicações de blog mais recentes que se focam em questões específicas. Alguns bons lugares para começar são os seguintes recursos:

* [Monitorização e Telemetria (Building Real-World Cloud Apps com Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  Capítulo do e-book com recomendações para rastreio em aplicações em nuvem Azure.
* [Rastreio ASP.NET](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Velho, mas ainda um bom recurso para uma introdução básica ao assunto.
* [Trace Ouvintes](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Informações sobre os ouvintes de rastreios, mas não mencionam o [WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener).
* [Walkthrough: Integração ASP.NET rastreio com rastreio de sistema.Rastreio de diagnóstico](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Este artigo também é antigo, mas inclui algumas informações adicionais que o artigo introdutório não cobre.
* [Rastreio em ASP.NET MVC Vistas de Barbear](https://devblogs.microsoft.com/aspnet/tracing-in-asp-net-mvc-razor-views/)<br/>
  Além de rastrear as vistas da Razor, o post também explica como criar um filtro de erro para registar todas as exceções não manipuladas numa aplicação MVC. Para obter informações sobre como registar todas as exceções não manipuladas numa aplicação De Formulários Web, consulte o exemplo Global.asax em ["Exemplo Completo para Manipuladores de Erros"](/previous-versions/bb397417(v=vs.140)) na MSDN. Em MVC ou Web Forms, se pretender registar certas exceções, mas deixar que o manuseamento da estrutura padrão produza efeitos para eles, pode capturar e relançar como no exemplo seguinte:

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

* [Rastreio de diagnóstico de streaming da linha de comando Azure (mais vislumbre!)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Como usar a linha de comando para fazer o que este tutorial mostra como fazer no Estúdio Visual. [O Glimpse](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) é uma ferramenta para depurar aplicações ASP.NET.
* [Utilização de Web Apps Logging and Diagnostics - com David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) e [streaming de registos de aplicações web - com David Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Vídeos de Scott Hanselman e David Ebbo.

Para a exploração de erros, uma alternativa para escrever o seu próprio código de rastreio é utilizar uma estrutura de registo de código aberto, como [o ELMAH](https://nuget.org/packages/elmah/). Para mais informações, consulte [as publicações de Scott Hanselman no blog sobre a ELMAH.](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx)

Além disso, não precisa de usar ASP.NET ou `System.Diagnostics` rastreio para obter registos de streaming do Azure. O serviço de registo de streaming de aplicações do Serviço de Aplicações transmite qualquer *.txt*, *.html* ou *.log* ficheiro que encontre na pasta *LogFiles.* Portanto, pode criar o seu próprio sistema de registo que escreve para o sistema de ficheiros da aplicação, e o seu ficheiro é automaticamente transmitido e descarregado. Tudo o que tem de fazer é escrever código de aplicação que cria ficheiros na pasta *d:\home\logfiles.*

### <a name="analyzing-web-server-logs"></a>Analisar registos de servidores web
Para obter mais informações sobre a análise de registos de servidores web, consulte os seguintes recursos:

* [LogParser](https://www.iis.net/downloads/community/2010/04/log-parser-22)<br/>
  Uma ferramenta para visualizar dados em registos de servidores web *(.log* ficheiros).
* [Problemas de resolução de problemas Problemas IIS Problemas de Desempenho ou Erros de Aplicação usando LogParser](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Uma introdução à ferramenta Log Parser que pode utilizar para analisar registos de servidores web.
* [Publicações de blog de Robert McMurray sobre a utilização do LogParser](/archive/blogs/robert_mcmurray/using-logparser-with-ftp-7-x-sessions)<br/>
* [O código de estado HTTP no IIS 7.0, IIS 7.5 e IIS 8.0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analisar registos de rastreio de pedidos falhados
O website da Microsoft TechNet inclui uma secção [de rastreio de pedidos falhados,](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) que pode ser útil para entender como usar estes registos. No entanto, esta documentação centra-se principalmente na configuração do rastreio de pedidos falhados no IIS, o que não é possível fazer no Azure App Service.

[GetStarted]: quickstart-dotnetcore.md?pivots=platform-windows
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki