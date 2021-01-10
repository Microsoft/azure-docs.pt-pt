---
title: Node.js melhores práticas e resolução de problemas
description: Aprenda as melhores práticas e etapas de resolução de problemas para aplicações Node.js em execução no Azure App Service.
author: msangapu-msft
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 9763835142e66bbbce51cd5c863dff87f261c270
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060165"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Boas práticas e guia de resolução de problemas para aplicações de nó no Azure App Service Windows

Neste artigo, aprende-se as melhores práticas e etapas de resolução de problemas para [aplicações windows Node.js](quickstart-nodejs.md?pivots=platform-windows) em execução no Azure App Service (com [iisnode).](https://github.com/azure/iisnode)

> [!WARNING]
> Tenha cuidado ao utilizar os passos de resolução de problemas no seu local de produção. A recomendação é resolver problemas com a sua aplicação numa configuração não-produção, por exemplo, a sua ranhura de paragem e quando o problema for corrigido, troque a sua ranhura de paragem com a sua ranhura de produção.
>

## <a name="iisnode-configuration"></a>Configuração IISNODE

Este [ficheiro de esquema](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) mostra todas as definições que pode configurar para iisnode. Algumas das definições que são úteis para a sua aplicação:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Esta definição controla o número de processos de nó que são lançados por aplicação IIS. O valor predefinido é 1. Pode lançar tantas node.execomo a sua contagem VM vCPU alterando o valor para 0. O valor recomendado é 0 para a maioria das aplicações para que possa utilizar todos os vCPUs na sua máquina. Node.exe é de rosca única, pelo que um node.exe consome um máximo de 1 vCPU. Para obter o máximo desempenho da sua aplicação de nó, pretende utilizar todos os vCPUs.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Esta definição controla o caminho para o node.exe. Pode definir este valor para apontar para a sua versão node.exe.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Esta definição controla o número máximo de pedidos simultâneos enviados por iisnode a cada node.exe. No Serviço de Aplicações Azure, o valor padrão é Infinito. Pode configurar o valor dependendo de quantos pedidos a sua candidatura recebe e da rapidez com que a sua candidatura processa cada pedido.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Esta definição controla o número máximo de vezes que iisnode retréis fazendo a ligação no tubo nomeado para enviar os pedidos para node.exe. Esta definição em combinação com o nomePipeConnectionRetryDelay determina o tempo limite total de cada pedido dentro do iisnode. O valor predefinido é de 200 no Azure App Service. Tempo limite total em segundos = (maxNamedPipeConnectionRetry \* nomeadoPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>nomeadoPipeConnectionRetryDelay

Esta definição controla a quantidade de tempo (em ms) iisnode espera entre cada nova tentou enviar o pedido para node.exe sobre o tubo nomeado. O valor predefinido é de 250 ms.
Tempo limite total em segundos = (maxNamedPipeConnectionRetry \* nomeadoPipeConnectionRetryDelay) / 1000

Por predefinição, o tempo limite total em iisnode no Azure App Service é de 200 \* 250 ms = 50 segundos.

### <a name="logdirectory"></a>logDirect

Esta definição controla o diretório onde o iisnode regista stdout/stderr. O valor padrão é iisnode, que é relativo ao diretório principal de scripts (diretório onde está presente o principal server.js)

### <a name="debuggerextensiondll"></a>depuraçãoExtensionDll

Esta definição controla a versão do iisnode do inspetor-nó que utiliza ao depurar a sua aplicação de nó. Atualmente, iisnode-inspector-0.7.3.dll e iisnode-inspector.dll são os únicos dois valores válidos para esta configuração. O valor predefinido é iisnode-inspector-0.7.3.dll. A versão iisnode-inspector-0.7.3.dll utiliza o sub-inspector-0.7.3 e utiliza tomadas web. Ative as tomadas web no seu webapp Azure para utilizar esta versão. Veja <https://ranjithblogs.azurewebsites.net/?p=98> mais detalhes sobre como configurar o iisnode para usar o novo inspetor de nó.

### <a name="flushresponse"></a>flushResponse

O comportamento padrão do IIS é que tampona dados de resposta até 4 MB antes do flushing, ou até o final da resposta, o que vier primeiro. O iisnode oferece uma configuração para anular este comportamento: para descarregar um fragmento do corpo da entidade de resposta assim que o iisnode o receber de node.exe, é necessário definir o iisnode/@flushResponse atributo em web.config para "verdadeiro":

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Ativar a descarga de cada fragmento do corpo da entidade de resposta adiciona a sobrecarga de desempenho que reduz a produção do sistema em ~5% (a partir de v0.1.13). O melhor para estender esta definição apenas para pontos finais que requerem streaming de resposta (por exemplo, usando o `<location>` elemento no web.config)

Além disso, para aplicações de streaming, também deve definir respostaSLimit do seu manipulador de iisnode para 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>assistidoFiles

Uma lista separada de semi-cólon de ficheiros que são vigiados por alterações. Qualquer alteração a um ficheiro faz com que a aplicação recicle. Cada entrada é constituída por um nome de diretório opcional, bem como um nome de ficheiro requerido, que se encontra em relação ao diretório onde se encontra o principal ponto de entrada da aplicação. Os cartões selvagens são permitidos apenas na parte do nome do ficheiro. O valor predefinido é `*.js;iisnode.yml`

### <a name="recyclesignalenabled"></a>reciclagemSignalEnabled

O valor predefinido é false. Se estiver ativado, a aplicação do nó pode ligar-se a um tubo denominado (tubo de controlo iisnode da variável \_ \_ ambiente) e enviar uma mensagem de "reciclagem". Isto faz com que o w3wp recicle graciosamente.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

O valor predefinido é 0, o que significa que esta função está desativada. Quando definido para algum valor superior a 0, o iisnode irá apagar todos os seus processos infantis cada 'idlePagePageTimePeriod' em milissegundos. Consulte a [documentação](/windows/desktop/api/psapi/nf-psapi-emptyworkingset) para entender o que significa página para fora. Esta definição é útil para aplicações que consomem uma grande quantidade de memória e querem página de memória para disco ocasionalmente para libertar RAM.

> [!WARNING]
> Tenha cuidado ao ativar as seguintes definições de configuração nas aplicações de produção. A recomendação é não permitir que se baseiem em aplicações de produção ao vivo.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

O valor predefinido é false. Se definido como verdadeiro, o iisnode adiciona um cabeçalho de resposta HTTP `iisnode-debug` a cada resposta HTTP que envia o valor do `iisnode-debug` cabeçalho é um URL. Peças individuais de informação de diagnóstico podem ser obtidas olhando para o fragmento de URL, no entanto, uma visualização está disponível abrindo o URL em um navegador.

### <a name="loggingenabled"></a>loggingEnabled

Esta definição controla o registo de sesta e stderr por iisnode. O Iisnode captura o stdout/stderr dos processos de nó que lança e escreve para o diretório especificado na definição 'logDirectory'. Uma vez ativado, a sua aplicação escreve registos para o sistema de ficheiros e, dependendo da quantidade de registos realizados pela aplicação, pode haver implicações no desempenho.

### <a name="deverrorsenabled"></a>devErrorsEnabled

O valor predefinido é false. Quando definido como verdadeiro, o iisnode exibe o código de estado HTTP e o código de erro Win32 no seu navegador. O código win32 é útil para depurar certos tipos de problemas.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>depurarenabled (não permitir no local de produção ao vivo)

Esta definição controla a função de depurar. Iisnode está integrado com o inspetor de nó. Ao ativar esta definição, pode ativar a depuração da sua aplicação de nó. Ao permitir esta configuração, o iisnode cria ficheiros de inspetor-nó no diretório 'debuggerVirtualDir' no primeiro pedido de depuração para o seu pedido de nó. Pode carregar o inspetor de nó enviando um pedido para `http://yoursite/server.js/debug` . Pode controlar o segmento DEBUG URL com a definição 'debuggerPathSegment'. Por padrão, debuggerPathSegment='debug'. Pode definir `debuggerPathSegment` um GUID, por exemplo, para que seja mais difícil ser descoberto por outros.

Leia [as aplicações de node.js de Debug no Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) para obter mais detalhes sobre a depuragem.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Cenários e recomendações/resolução de problemas

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>O meu pedido de nó está a fazer chamadas de saída excessivas.

Muitas aplicações gostariam de fazer ligações de saída como parte do seu funcionamento regular. Por exemplo, quando um pedido chega, a sua app de nó gostaria de contactar uma API REST em outro lugar e obter algumas informações para processar o pedido. Você gostaria de usar um agente keep alive ao fazer chamadas http ou https. Pode usar o módulo de agentekeepalive como agente de manutenção viva ao fazer estas chamadas de saída.

O módulo de agentekeepalive garante que as tomadas são reutilizadas no seu VM webapp Azure. A criação de uma nova tomada em cada pedido de saída adiciona sobrecarga à sua aplicação. Ter a sua aplicação a reutilizar tomadas para pedidos de saída garante que a sua aplicação não exceda os maxSockets que são atribuídos por VM. A recomendação sobre o Azure App Service é definir o valor de maxSockets do agenteKeepAlive para um total de (4 instâncias de node.exe \* 32 maxSockets/instância) 128 tomadas por VM.

Configuração [do agente de exemploKeepALive:](https://www.npmjs.com/package/agentkeepalive)

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 32,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> Este exemplo pressupõe que tem 4 node.exe a funcionar no seu VM. Se tiver um número diferente de node.exe em execução no VM, deve modificar a definição de maxSockets em conformidade.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>O meu pedido de nó está a consumir demasiado CPU

Poderá receber uma recomendação do Azure App Service no seu portal sobre o elevado consumo de CPU. Também pode configurar monitores para observar [determinadas métricas](web-sites-monitor.md). Ao verificar a utilização do CPU no painel de instrumentos do [portal Azure,](../azure-monitor/platform/metrics-charts.md)verifique os valores MAX para CPU para que não perca os valores de pico.
Se acredita que a sua aplicação está a consumir demasiado CPU e não consegue explicar porquê, pode perfilar a sua aplicação de nó para descobrir.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>Perfilar a sua aplicação de nó no Azure App Service com V8-Profiler

Por exemplo, digamos que tem uma aplicação hello world que quer perfilar da seguinte forma:

```nodejs
const http = require('http');
function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    WriteConsoleLog();
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Vá ao site da Consola Debug `https://yoursite.scm.azurewebsites.net/DebugConsole`

Vá ao seu site/diretório wwwroot. Vê-se uma solicitação de comando como mostrado no seguinte exemplo:

![Screenshot que mostra o seu site/diretório de wwwroot e solicitação de comando.](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Execute o comando `npm install v8-profiler`.

Este comando instala o perfil v8 no \_ diretório de módulos de nó e todas as suas dependências.
Agora, edite a sua server.js para perfilar a sua aplicação.

```nodejs
const http = require('http');
const profiler = require('v8-profiler');
const fs = require('fs');

function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    profiler.startProfiling('HandleRequest');
    WriteConsoleLog();
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

O código anterior perfis a função WriteConsoleLog e, em seguida, escreve a saída de perfil para o ficheiro 'profile.cpuprofile' no seu site wwwroot. Envie um pedido para o seu pedido. Vê um ficheiro 'profile.cpuprofile' criado no seu site wwwroot.

![Screenshot que mostra o ficheiro profile.cpuprofile.](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Descarregue este ficheiro e abra-o com ferramentas Chrome F12. Pressione F12 no Chrome e, em seguida, escolha o **separador Perfis.** Escolha o botão **Carregar.** Selecione o seu ficheiro profile.cpuprofile que descarregou. Clique no perfil que acabou de carregar.

![Screenshot que mostra o ficheiro profile.cpuprofile que carregou.](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Pode ver que 95% do tempo foi consumido pela função WriteConsoleLog. A saída também mostra os números de linha exatos e os ficheiros de origem que causaram o problema.

### <a name="my-node-application-is-consuming-too-much-memory"></a>A minha aplicação de nó está a consumir demasiada memória.

Se a sua aplicação estiver a consumir demasiada memória, vê um aviso do Azure App Service no seu portal sobre o consumo elevado de memória. Pode configurar monitores para observar [determinadas métricas.](web-sites-monitor.md) Ao verificar a utilização da memória no painel de instrumentos do [portal Azure,](../azure-monitor/platform/metrics-charts.md)certifique-se de verificar os valores MAX para não perder os valores de pico.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Deteção de fugas e heap diff para node.js

Pode usar [o node-memwatch para ajudá-lo](https://github.com/lloyd/node-memwatch) a identificar fugas de memória.
Pode instalar `memwatch` tal como o perfil v8 e editar o seu código para capturar e difundir pilhas para identificar as fugas de memória na sua aplicação.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Os meus node.exe estão a ser mortos aleatoriamente.

Há algumas razões pelas quais node.exe é encerrado aleatoriamente:

1. A sua aplicação está a lançar exceções sem correntes – Ver d: \\ \\ Aplicação de Registos \\ \\ domésticoslogging-errors.txt ficheiro para os detalhes sobre a exceção lançada. Este ficheiro tem o traço da pilha para ajudar a depurar e corrigir a sua aplicação.
2. A sua aplicação está a consumir demasiada memória, o que está a afetar outros processos desde o início. Se a memória total de VM estiver perto de 100%, a sua node.exe pode ser morta pelo gestor de processos. O gestor de processos mata alguns processos para permitir que outros processos mentem para fazer algum trabalho. Para corrigir este problema, perfile a sua aplicação para fugas de memória. Se a sua aplicação necessitar de grandes quantidades de memória, dimensione até um VM maior (o que aumenta a RAM disponível para o VM).

### <a name="my-node-application-does-not-start"></a>A minha aplicação de nó não começa

Se a sua aplicação estiver a devolver 500 erros quando começar, pode haver algumas razões:

1. Node.exe não está presente no local certo. Verifique a definição de nodeProcessCommandLine.
2. O ficheiro principal do script não está presente na localização correta. Verifique web.config e certifique-se de que o nome do ficheiro de script principal na secção de manipuladores corresponde ao ficheiro de script principal.
3. Web.config configuração não está correta – verifique os nomes/valores das definições.
4. Arranque a Frio – A sua aplicação está a demorar demasiado tempo a começar. Se a sua aplicação demorar mais tempo do que (maxNamedPipeConnectionRetry \* nomeadoPipeConnectionRetryDelay) / 1000 segundos, o iisnode devolve um erro de 500. Aumente os valores destas definições para corresponder à hora de início da sua aplicação para evitar que o iisnode se anteente e devolva o erro de 500.

### <a name="my-node-application-crashed"></a>A minha aplicação de nó despenhou-se.

A sua aplicação está a lançar exceções sem correntes – Verifique `d:\\home\\LogFiles\\Application\\logging-errors.txt` o ficheiro para obter os detalhes sobre a exceção lançada. Este ficheiro tem o traço da pilha para ajudar a diagnosticar e corrigir a sua aplicação.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>A minha aplicação de nó leva muito tempo para começar (Arranque a Frio)

A causa comum para os longos tempos de início da aplicação é um elevado número de ficheiros nos módulos de \_ nó. A aplicação tenta carregar a maioria destes ficheiros quando começa. Por padrão, uma vez que os seus ficheiros são armazenados na partilha de rede no Azure App Service, o carregamento de muitos ficheiros pode demorar algum tempo.
Algumas soluções para tornar este processo mais rápido são:

1. Tente carregar preguiçosos os seus módulos de nó \_ e não carregue todos os módulos no início da aplicação. Para os módulos de carga preguiçosos, a chamada para exigir ('módulo') deve ser feita quando realmente precisa do módulo dentro da função antes da primeira execução do código do módulo.
2. O Azure App Service oferece uma funcionalidade chamada cache local. Esta funcionalidade copia o seu conteúdo da partilha de rede para o disco local no VM. Uma vez que os ficheiros são locais, o tempo de carga dos módulos de nó \_ é muito mais rápido.

## <a name="iisnode-http-status-and-substatus"></a>IISNODE http status e substatus

O `cnodeconstants` [ficheiro de origem](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) lista todas as combinações possíveis de estado/substatus que o iisnode pode ser retornado devido a um erro.

Ativar o FREB para a sua aplicação para ver o código de erro win32 (certifique-se de que ativa o FREB apenas em locais de não produção por razões de desempenho).

| Estado http | Http Substatus | Possível razão? |
| --- | --- | --- |
| 500 |1000 |Houve algum problema em enviar o pedido para o IISNODE – Verificar se node.exe foi iniciado. Node.exe podia ter caído quando começava. Verifique se a configuração do web.config se pode verificar se há erros. |
| 500 |1001 |- Win32Error 0x2 - A app não responde ao URL. Verifique as regras de reescrita de URL ou verifique se a sua aplicação expressa tem as rotas corretas definidas. - Win32Error 0x6d – o tubo com nome está ocupado – Node.exe não está a aceitar pedidos porque o tubo está ocupado. Verifique o uso elevado do cpu. - Outros erros – verifique se node.exe se despenhou. |
| 500 |1002 |Node.exe despenhou-se – verifique d: \\logging-errors.txt de \\ registos \\ domésticos para rastrear a pilha. |
| 500 |1003 |Configuração do tubo Problema – A configuração do tubo com nome está incorreta. |
| 500 |1004-1018 |Houve algum erro ao enviar o pedido ou processar a resposta de/para node.exe. Verifique se node.exe se despenhou. check d: \\ \\ Home LogFiles \\logging-errors.txt para rastrear a pilha. |
| 503 |1000 |Não há memória suficiente para alocar mais ligações de tubos. Verifique porque é que a sua aplicação está a consumir tanta memória. Verifique o valor de definição maxConcurrentRequestsPerProcess. Se não for infinito e tiver muitos pedidos, aumente este valor para evitar este erro. |
| 503 |1001 |O pedido não pôde ser enviado para node.exe porque a aplicação está a ser reciclada. Depois de o pedido ter sido reciclado, os pedidos devem ser servidos normalmente. |
| 503 |1002 |Verifique o código de erro win32 por razão real – O pedido não pôde ser enviado para um node.exe. |
| 503 |1003 |Tubo com nome é muito ocupado – Verifique se node.exe está a consumir CPU excessivo |

NODE.exe tem uma definição chamada `NODE_PENDING_PIPE_INSTANCES` . No Azure App Service, este valor está definido para 5000. O que significa que node.exe pode aceitar 5000 pedidos de cada vez no tubo nomeado. Este valor deve ser bom o suficiente para a maioria das aplicações de nó que estão a decorrer no Azure App Service. Você não deve ver 503.1003 no Azure App Service devido ao alto valor para o `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Mais recursos

Siga estes links para saber mais sobre node.js aplicações no Azure App Service.

* [Introdução às aplicações Web Node.js no Serviço de Aplicações do Azure](quickstart-nodejs.md)
* [Como depurar uma aplicação Web Node.js no App Service do Azure](/archive/blogs/azureossds/debugging-node-js-apps-on-azure-app-services)
* [Utilizar Módulos do Node.js com aplicações do Azure](../nodejs-use-node-modules-azure-apps.md)
* [Web Apps do App Service do Azure: Node.js](/archive/blogs/silverlining/windows-azure-websites-node-js)
* [Centro de Programadores do Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Explorar a Consola de Depuração do Kudu Super Secreta](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
