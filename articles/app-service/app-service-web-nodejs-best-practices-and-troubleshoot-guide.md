---
title: Node.js boas práticas e resolução de problemas
description: Aprenda as melhores práticas e passos de resolução de problemas para aplicações nonóios em execução no Serviço de Aplicações Azure.
author: msangapu-msft
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 682884d11b298a97e27056af3c10802dfd410e4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75430570"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Boas práticas e guia de resolução de problemas para aplicações de nó no Azure App Service Windows

Neste artigo, aprende as melhores práticas e passos de resolução de problemas para [aplicações](app-service-web-get-started-nodejs.md) de nó em execução no Azure App Service (com [iisnode).](https://github.com/azure/iisnode)

> [!WARNING]
> Tenha cuidado ao utilizar passos de resolução de problemas no seu local de produção. A recomendação é resolver problemas na sua aplicação numa configuração de não produção, por exemplo, a sua ranhura de preparação e quando o problema for corrigido, troque a sua ranhura de preparação com a sua ranhura de produção.
>

## <a name="iisnode-configuration"></a>Configuração IISNODE

Este [ficheiro de esquema](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) sintetiza todas as definições que pode configurar para iisnode. Algumas das definições que são úteis para a sua aplicação:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Esta definição controla o número de processos de nó que são lançados por aplicação IIS. O valor predefinido é 1. Pode lançar tantos nó.ex como a contagem vCPU VM, alterando o valor para 0. O valor recomendado é 0 para a maioria das aplicações para que possa utilizar todos os vCPUs na sua máquina. O nó.exe é de roscar-se para que um nó.exe consuma um máximo de 1 vCPU. Para obter o máximo desempenho da sua aplicação de nó, você deseja usar todos os vCPUs.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Esta definição controla o caminho para o nó.exe. Pode definir este valor para apontar para a sua versão nó.exe.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Esta definição controla o número máximo de pedidos simultâneos enviados por ísóno a cada nó.exe. No Serviço de Aplicações Azure, o valor padrão é Infinito. Pode configurar o valor dependendo do número de pedidos que a sua aplicação recebe e da rapidez com que a sua aplicação processa cada pedido.

### <a name="maxnamedpipeconnectionretry"></a>maxNamePipeConnectionRetry

Esta definição controla o número máximo de vezes que o iisnode se retenta fazendo a ligação no tubo nomeado para enviar os pedidos para o nó.exe. Esta definição em combinação com o denominadoPipeConnectionRetryDelay determina o tempo total de cada pedido dentro do iisnode. O valor padrão é de 200 no Serviço de Aplicações Azure. Tempo total em segundos = (maxNamedPipeConnectionRetry \* chamadoPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>nomeadoPipeConnectionRetryDelay

Esta definição controla a quantidade de tempo (em ms) que o iisnode aguarda entre cada nova tentativa para enviar o pedido para nó.exe sobre o tubo nomeado. O valor padrão é de 250 ms.
Tempo total em segundos = (maxNamedPipeConnectionRetry \* chamadoPipeConnectionRetryDelay) / 1000

Por padrão, o tempo total de iisnode no Serviço \* de Aplicações Azure é de 200 250 ms = 50 segundos.

### <a name="logdirectory"></a>logDirectory

Esta definição controla o diretório onde os logs de iisnode stdout/stderr. O valor padrão é o iisnode, que é relativo ao diretório principal do script (diretório onde o servidor principal.js está presente)

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Esta definição controla a versão do iisnódodo do inspetor de nós que utiliza ao depurar a aplicação do nó. Atualmente, o iisnode-inspector-0.7.3.dll e o iisnode-inspector.dll são os dois únicos valores válidos para esta definição. O valor por defeito é iisnode-inspector-0.7.3.dll. A versão iisnode-inspector-0.7.3.dll utiliza o nó-inspector-0.7.3 e utiliza tomadas web. Ative as tomadas web no seu webapp Azure para utilizar esta versão. Consulte <https://ranjithblogs.azurewebsites.net/?p=98> mais detalhes sobre como configurar o iisnode para utilizar o novo inspetor de nó.

### <a name="flushresponse"></a>flushResponse

O comportamento padrão do IIS é que tampona dados de resposta até 4 MB antes de flushing, ou até o final da resposta, o que vier primeiro. O iisnode oferece uma configuração de configuração para sobrepor este comportamento: para descarregar um fragmento do corpo da entidade iisnode/@flushResponse de resposta assim que o iisnode o receber do nó.exe, é necessário definir o atributo em web.config para 'verdadeiro':

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Ativar a descarga de cada fragmento do corpo da entidade de resposta adiciona a sobrecarga de desempenho que reduz a entrada do sistema em ~5% (a partir de v0.1.13). O melhor para analisar esta definição apenas para pontos `<location>` finais que requerem streaming de resposta (por exemplo, utilizando o elemento na web.config)

Além disso, para aplicações de streaming, também deve definir a respostaBufferLimit do seu manipulador de iisnode para 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchfiles

Uma lista separada do ponto-semi-cólon de ficheiros que são vigiadas para alterações. Qualquer alteração a um ficheiro faz com que a aplicação recicle. Cada entrada consiste num nome de diretório opcional, bem como num nome de ficheiro exigido, relativo ao diretório onde se encontra o principal ponto de entrada da aplicação. Cartões selvagens são permitidos apenas na porção de nome de ficheiro. O valor predefinido é `*.js;iisnode.yml`

### <a name="recyclesignalenabled"></a>reciclarSignalEnabled

O valor predefinido é false. Se ativado, a aplicação do nó pode ligar-se\_a\_um tubo denominado (variável ambiental IISNODE CONTROL PIPE) e enviar uma mensagem de "reciclagem". Isto faz com que o W3WP recicle graciosamente.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

O valor predefinido é 0, o que significa que esta funcionalidade está desativada. Quando definido para um valor superior a 0, o iisnode irá páginar todos os seus processos infantis em milissegundos. Consulte a [documentação](/windows/desktop/api/psapi/nf-psapi-emptyworkingset) para entender o que significa a página. Esta definição é útil para aplicações que consomem uma elevada quantidade de memória e querem pager a memória para o disco ocasionalmente para libertar ram.

> [!WARNING]
> Tenha cuidado ao ativar as seguintes definições de configuração nas aplicações de produção. A recomendação é não permitir que se instem aplicações de produção ao vivo.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

O valor predefinido é false. Se for definido como verdadeiro, o iisnode adiciona um cabeçalho `iisnode-debug` de resposta HTTP a cada resposta HTTP que envia o valor do `iisnode-debug` cabeçalho é um URL. Peças individuais de informação de diagnóstico podem ser obtidas olhando para o fragmento de URL, no entanto, uma visualização está disponível abrindo o URL num navegador.

### <a name="loggingenabled"></a>exploração madeireiraHabilitada

Esta definição controla o registo de stdout e stderr por ísóno. Iisnode captura stdout/stderr dos processos de nó que lança e escreve para o diretório especificado na definição 'logDirectory'. Uma vez ativada, a sua aplicação escreve registos para o sistema de ficheiros e, dependendo da quantidade de registo feito pela aplicação, pode haver implicações de desempenho.

### <a name="deverrorsenabled"></a>devErrorsEnabled

O valor predefinido é false. Quando definido como verdadeiro, o iisnode apresenta o código de estado HTTP e o código de erro Win32 no seu navegador. O código win32 é útil para depurar certos tipos de problemas.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>depuraçãoEnabled (não permitir no local de produção ao vivo)

Esta definição controla a função de depuração. Iisnode está integrado com o inspetor do nó. Ao ativar esta definição, permite a depuração da aplicação do nó. Ao permitir esta definição, o iisnode cria ficheiros de inspetor de nós no diretório 'debuggerVirtualDir' no primeiro pedido de depuração para a sua aplicação de nó. Você pode carregar o inspetor de nó, enviando um pedido para `http://yoursite/server.js/debug`. Pode controlar o segmento DEURL de depuração com a definição 'debuggerPathSegment'. Por predefinição, debuggerPathSegment='debug'. Pode definir `debuggerPathSegment` um GUID, por exemplo, para que seja mais difícil ser descoberto por outros.

Leia [as aplicações Debug node.js no Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) para obter mais detalhes sobre a depuração.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Cenários e recomendações/resolução de problemas

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>O meu pedido de nó está a fazer chamadas excessivas de saída.

Muitas aplicações gostariam de fazer ligações de saída como parte do seu funcionamento regular. Por exemplo, quando um pedido chega, a sua app de nó quereria contactar uma API REST em outro lugar e obter algumas informações para processar o pedido. Você gostaria de usar um agente para manter vivo ao fazer chamadas http ou https. Podes usar o módulo de agentekeepalive como agente para manter vivo ao fazer estas chamadas de saída.

O módulo agentkeepalive garante que as tomadas são reutilizadas no seu VM webapp Azure. A criação de uma nova tomada em cada pedido de saída adiciona sobrecarga à sua aplicação. Ter as tomadas de reutilização da sua aplicação para pedidos de saída garante que a sua aplicação não excede as maxSockets que são atribuídas por VM. A recomendação sobre o Serviço de Aplicações Azure é definir o valor maxSockets do \* agenteKeepAlive para um total de (4 instâncias de nó.exe 40 maxSockets/instância) 160 tomadas por VM.

Configuração do [agente de exemploKeepALive:](https://www.npmjs.com/package/agentkeepalive)

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> Este exemplo pressupõe que tem 4 nós.exe em execução no seu VM. Se tiver um número diferente de nó.exe em execução no VM, deve modificar a definição maxSockets em conformidade.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>A minha aplicação de nó está a consumir demasiado CPU

Poderá receber uma recomendação do Azure App Service no seu portal sobre o elevado consumo de CPU. Também pode configurar monitores para vigiar certas [métricas](web-sites-monitor.md). Ao verificar a utilização do CPU no [Painel do Portal Azure,](../azure-monitor/app/web-monitor-performance.md)verifique os valores MAX para CPU para que não perca os valores máximos.
Se acredita que a sua aplicação está a consumir demasiado CPU e não consegue explicar porquê, pode perfilar a sua aplicação de nó para descobrir.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>Perfilde a sua aplicação de nó no Serviço de Aplicações Azure com Perfil V8

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

Ir ao site da Consola Debug`https://yoursite.scm.azurewebsites.net/DebugConsole`

Entre no seu diretório site/wwwroot. Vê um pedido de comando como mostrado no seguinte exemplo:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Execute o comando `npm install v8-profiler`.

Este comando instala o perfil v8\_sob o diretório de módulos de nó e todas as suas dependências.
Agora, edite o seu servidor.js para perfilar a sua aplicação.

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

Os perfis de código anteriores da função WriteConsoleLog e, em seguida, escreve a saída de perfil para o ficheiro 'profile.cpuprofile' no seu site wwwroot. Envie um pedido para o seu pedido. Você vê um ficheiro 'profile.cpuprofile' criado sob o seu site wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Descarregue este ficheiro e abra-o com as Ferramentas Chrome F12. Prima F12 no Chrome e, em seguida, escolha o separador **Perfis.** Escolha o botão **carregar.** Selecione o ficheiro profile.cpuprofile do seu perfil que descarregou. Clique no perfil que acabou de carregar.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Pode ver que 95% do tempo foi consumido pela função WriteConsoleLog. A saída também mostra os números de linha exatos e os ficheiros de origem que causaram o problema.

### <a name="my-node-application-is-consuming-too-much-memory"></a>A minha aplicação de nó está a consumir demasiada memória.

Se a sua aplicação estiver a consumir demasiada memória, vê um aviso do Serviço de Aplicações Azure no seu portal sobre o elevado consumo de memória. Pode configurar monitores para vigiar certas [métricas.](web-sites-monitor.md) Ao verificar o uso da memória no [Painel do Portal Azure,](../azure-monitor/app/web-monitor-performance.md)certifique-se de verificar se há memória dos valores MAX para que não perca os valores de pico.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Deteção de fugas e Heap Diff para nó.js

Pode usar o memwatch do nó para [ajudá-lo](https://github.com/lloyd/node-memwatch) a identificar fugas de memória.
Pode instalar-se `memwatch` tal como o perfil v8 e editar o seu código para capturar e difundir pilhas para identificar as fugas de memória na sua aplicação.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>O meu nó.exe's está a ser morto aleatoriamente.

Há algumas razões pelas quais o nó.exe é desligado aleatoriamente:

1. A sua aplicação está a lançar\\\\exceções\\\\não apanhadas – Verifique d: registo de registos em casa Ficheiro de registo de erros.txt para obter os detalhes sobre a exceção lançada. Este ficheiro tem o traço da pilha para ajudar a depurar e corrigir a sua aplicação.
2. A sua aplicação está a consumir demasiada memória, o que está a afetar outros processos desde o início. Se a memória VM total estiver perto de 100%, o seu nó.exe pode ser morto pelo gestor do processo. O gestor de processos mata alguns processos para permitir que outros processos possam fazer algum trabalho. Para corrigir este problema, desmepresente o seu pedido de fugas de memória. Se a sua aplicação necessitar de grandes quantidades de memória, aumente até um VM maior (o que aumenta a RAM disponível para o VM).

### <a name="my-node-application-does-not-start"></a>A minha aplicação de nó não começa.

Se a sua aplicação estiver a devolver 500 Erros quando começar, pode haver algumas razões:

1. O nó.exe não está presente no local certo. Verifique a definição de nodeProcessCommandLine.
2. O ficheiro principal do script não está presente no local correto. Verifique web.config e certifique-se de que o nome do ficheiro principal do script na secção de manipuladores corresponde ao ficheiro principal do script.
3. A configuração Web.config não está correta – verifique os nomes/valores das definições.
4. Arranque a frio – A sua aplicação está a demorar muito tempo a ser iniciada. Se a sua aplicação demorar mais \* do que (maxNamedPipeConnectionRetrynome chamadoPipeConnectionRetryDelay) / 1000 segundos, o iisnode devolve um erro de 500. Aumente os valores destas definições para corresponder à hora de início da sua aplicação para evitar que o iisnode saia e desatifique o erro de 500.

### <a name="my-node-application-crashed"></a>A minha aplicação de nó despenhou-se.

A sua aplicação está a `d:\\home\\LogFiles\\Application\\logging-errors.txt` lançar exceções não apanhadas – Verifique o ficheiro para obter os detalhes da exceção lançada. Este ficheiro tem o traço da pilha para ajudar a diagnosticar e corrigir a sua aplicação.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>A minha aplicação de nó leva muito tempo para começar (Início frio)

A causa comum para longos tempos de início\_de aplicação é um elevado número de ficheiros nos módulos do nó. A aplicação tenta carregar a maioria destes ficheiros quando começa. Por predefinição, uma vez que os seus ficheiros estão armazenados na partilha da rede no Serviço de Aplicações Azure, carregar muitos ficheiros pode demorar algum tempo.
Algumas soluções para tornar este processo mais rápido são:

1. Certifique-se de que tem uma estrutura de dependência plana e nenhuma dependência duplicada utilizando o npm3 para instalar os seus módulos.
2. Tente carregar os\_módulos de nó preguiçosos e não carregue todos os módulos no início da aplicação. Para os módulos de carga preguiçosos, a chamada a exigir ('módulo') deve ser feita quando realmente precisa do módulo dentro da função antes da primeira execução do código do módulo.
3. O Azure App Service oferece uma funcionalidade chamada cache local. Esta funcionalidade copia o seu conteúdo desde a partilha de rede até ao disco local no VM. Como os ficheiros são locais,\_o tempo de carga dos módulos de nó é muito mais rápido.

## <a name="iisnode-http-status-and-substatus"></a>Estatuto e subestatuto do IISNODE

O `cnodeconstants` [ficheiro fonte](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) lista todas as possíveis combinações de estado/subestatuto que o iisnode pode devolver devido a um erro.

Ative o FREB para a sua aplicação ver o código de erro win32 (certifique-se de que ativa a FREB apenas em sites não produtivos por razões de desempenho).

| Estado http | Subestatuto http | Possível razão? |
| --- | --- | --- |
| 500 |1000 |Houve algum problema a enviar o pedido para o IISNODE – Verificar se o nó.exe foi iniciado. O nó.exe pode ter caído quando começou. Verifique se a configuração web.config está a verificar se há erros. |
| 500 |1001 |- Win32Error 0x2 - A app não responde ao URL. Verifique as regras de reescrita do URL ou verifique se a sua aplicação expressa tem as rotas corretas definidas. - Win32Error 0x6d – o tubo nomeado está ocupado – O Node.exe não aceita pedidos porque o tubo está ocupado. Verifique o alto uso do CPU. - Outros erros - verifique se o nó.exe se despenhou. |
| 500 |1002 |Node.exe crashed –\\check\\d: home LogFiles\\logging-errors.txt for stack trace. |
| 500 |1003 |Problema de configuração do tubo – A configuração do tubo nomeado está incorreta. |
| 500 |1004-1018 |Houve algum erro ao enviar o pedido ou processar a resposta para/a partir do nó.exe. Verifique se o nó.exe se despenhou. verificar\\d:\\home\\LogFiles log-errors.txt para rastrear o rastreio da pilha. |
| 503 |1000 |Não há memória suficiente para alocar ligações de tubos mais nomeadas. Verifique por que a sua aplicação está a consumir tanta memória. Verifique o valor de definição maxConcurrentRequestSPerProcess. Se não é infinito e tem muitos pedidos, aumente este valor para evitar este erro. |
| 503 |1001 |O pedido não pôde ser enviado para nó.exe porque o pedido é reciclagem. Depois de o pedido ter sido reciclado, os pedidos devem ser servidos normalmente. |
| 503 |1002 |Verifique o código de erro win32 por razão real – O pedido não pôde ser enviado para um nó.exe. |
| 503 |1003 |O tubo nomeado é muito ocupado – Verifique se o nó.exe está a consumir CPU excessivo |

Node.exe tem uma `NODE_PENDING_PIPE_INSTANCES`definição chamada . No Serviço de Aplicações Azure, este valor está definido para 5000. O que significa que o nó.exe pode aceitar 5000 pedidos de cada vez no cano nomeado. Este valor deve ser bom o suficiente para a maioria das aplicações de nó que estão em execução no Serviço de Aplicações Azure. Você não deve ver 503.1003 no Azure App Service devido ao alto valor para o`NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Mais recursos

Siga estes links para saber mais sobre aplicações no nó.js no Serviço de Aplicações Azure.

* [Introdução às aplicações Web Node.js no Serviço de Aplicações do Azure](app-service-web-get-started-nodejs.md)
* [Como depurar uma aplicação Web Node.js no App Service do Azure](https://blogs.msdn.microsoft.com/azureossds/2018/08/03/debugging-node-js-apps-on-azure-app-services/)
* [Utilizar Módulos do Node.js com aplicações do Azure](../nodejs-use-node-modules-azure-apps.md)
* [Web Apps do App Service do Azure: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centro de Programadores do Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Explorar a Consola de Depuração do Kudu Super Secreta](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
