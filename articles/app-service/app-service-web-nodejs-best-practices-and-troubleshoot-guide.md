---
title: Melhores práticas e resolução de problemas do node. js - serviço de aplicações do Azure
description: Aprenda as práticas recomendadas e passos de resolução de problemas para aplicações de nó no serviço de aplicações do Azure.
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: ''
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: ranjithr
ms.custom: seodec18
ms.openlocfilehash: 6a22600f7d6653da64256d7ec63ba26f272b2184
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888275"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Melhores práticas e guia de resolução de problemas para aplicações de nó no Windows de serviço de aplicações do Azure

Neste artigo, ficará a saber as práticas recomendadas e passos de resolução [aplicações de nó](app-service-web-get-started-nodejs.md) em execução no App Service do Azure (com [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Tenha cuidado ao utilizar os passos de resolução de problemas no seu site de produção. Recomenda-se resolver problemas de seu aplicativo numa configuração de não produção por exemplo o bloco de teste e quando o problema estiver resolvido, trocar o bloco de teste com o bloco de produção.
>

## <a name="iisnode-configuration"></a>Configuração do IISNODE

Isso [arquivo do esquema](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) mostra todas as definições que pode configurar para o iisnode. Algumas das definições que são úteis para a sua aplicação:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Esta definição controla o número de processos de nó que são iniciadas de cada aplicativo IIS. O valor predefinido é 1. Pode iniciar node.exes tantos como sua contagem de vCPU VM ao alterar o valor como 0. O valor recomendado é 0 para a maioria dos aplicativos, pelo que pode utilizar todas as vCPUs no seu computador. NODE.exe é um único thread para que um node.exe consome um máximo de 1 vCPU. Para obter o máximo desempenho do seu aplicativo de nó, que pretende utilizar todas as vCPUs.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Esta definição controla o caminho para o node.exe. Pode definir este valor para apontar para a sua versão de node.exe.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Esta definição controla o número máximo de pedidos simultâneos enviada por iisnode para cada node.exe. No serviço de aplicações do Azure, o valor predefinido é infinito. Pode configurar o valor dependendo de quantas solicitações que seu aplicativo receba e rapidez o seu aplicativo processa cada pedido.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Esta definição controla o número máximo de vezes que fazer a conexão no pipe nomeado para enviar os pedidos para node.exe de repetições de iisnode. Esta definição em combinação com namedPipeConnectionRetryDelay determina o tempo limite total de cada pedido dentro do iisnode. O valor predefinido é 200 no App Service do Azure. Total de tempo limite em segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Esta definição controla a quantidade de tempo (em ms) iisnode esperas entre cada tentativa para enviar o pedido para node.exe através do pipe nomeado. O valor predefinido é 250 ms.
Total de tempo limite em segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

Por predefinição, o total de tempo limite no iisnode no App Service do Azure é 200 \* 250 ms = 50 segundos.

### <a name="logdirectory"></a>logDirectory

Esta definição controla o diretório onde o iisnode registos de stdout/stderr. O valor predefinido é iisnode, que é relativos ao diretório principal do script (o diretório onde está presente Server. js principal)

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Esta definição controla qual versão do nó-inspector iisnode utiliza ao depurar a aplicação de nó. Atualmente, o iisnode-inspector-0.7.3.dll e iisnode inspector.dll são os dois únicos valores válidos para esta definição. O valor predefinido é iisnode-inspector-0.7.3.dll. A versão do iisnode-inspector-0.7.3.dll utiliza o nó-inspector-0.7.3 e utiliza web sockets. Ative sockets web na sua aplicação Web do Azure para utilizar esta versão. Consulte <https://ranjithblogs.azurewebsites.net/?p=98> para obter mais detalhes sobre como configurar o iisnode para utilizar o inspetor de nó de novo.

### <a name="flushresponse"></a>flushResponse

O comportamento padrão do IIS é que ele coloca na memória intermédia os dados de resposta até 4 MB, antes de liberá-lo, ou até ao fim da resposta, o que acontecer primeiro. o iisnode oferece uma definição de configuração para substituir esse comportamento: para liberar um fragmento do corpo da entidade de resposta, assim que o iisnode recebe-a de node.exe, tem de definir o iisnode/@flushResponse atributo na Web. config, como 'true':

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Ativar a liberá-lo de cada fragmento da resposta do corpo de entidade adiciona a sobrecarga de desempenho que reduz a taxa de transferência do sistema % ~ 5 (a partir de v0.1.13). A melhor para definir o âmbito esta definição apenas para pontos finais que necessitam de resposta de transmissão em fluxo (por exemplo, utilizando o `<location>` elemento no Web. config)

Além disso, para transmissão em fluxo de aplicativos, também tem de definir responseBufferLimit do seu manipulador iisnode como 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Lista de ficheiros que são assistiu para alterações separados por ponto e vírgula. Qualquer alteração num arquivo faz com que o aplicativo reciclar. Cada entrada é composta por um nome opcional do, bem como um nome de ficheiro requerido, que são relativos ao diretório onde está localizado o ponto de entrada do aplicativo principal. A parte do nome de ficheiro só são permitidos carateres universais. O valor predefinido é `*.js;iisnode.yml`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

O valor predefinido é false. Se estiver ativada, a aplicação de nó pode ligar a um pipe nomeado (a variável de ambiente IISNODE\_CONTROLE\_PIPE) e enviar uma mensagem de "Reciclagem". Isso faz com que o w3wp reciclar corretamente.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

O valor predefinido é 0, o que significa que esta funcionalidade está desativada. Quando definido como um valor maior que 0, iisnode será page out de todos os processos filho cada "idlePageOutTimePeriod' em milissegundos. Ver [documentação](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx) para compreender o que page out de meio. Esta definição é útil para aplicativos que consomem uma elevada quantidade de memória e quer page out de memória no disco ocasionalmente para libertar de RAM.

> [!WARNING]
> Tenha cuidado ao ativar as seguintes definições de configuração em aplicações de produção. A recomendação é não ativá-los em aplicações de produção em direto.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

O valor predefinido é false. Se definido como true, iisnode adiciona um cabeçalho de resposta HTTP `iisnode-debug` para cada resposta HTTP que envia o `iisnode-debug` valor do cabeçalho é um URL. As partes individuais de informações de diagnóstico podem ser obtidas ao observar o fragmento da URL, no entanto, uma visualização está disponível ao abrir o URL num browser.

### <a name="loggingenabled"></a>loggingEnabled

Esta definição controla o registo de stdout e stderr por iisnode. O Iisnode captura stdout/stderr de processos de nó, ele inicia e grava no diretório especificado na definição de 'logDirectory'. Quando esta opção estiver ativada, a aplicação escreve os registos para o sistema de ficheiros e dependendo da quantidade de Registro em log feito pela aplicação, pode haver implicações de desempenho.

### <a name="deverrorsenabled"></a>devErrorsEnabled

O valor predefinido é false. Quando definido como true, iisnode apresenta o código de estado HTTP e o código de erro do Win32 no seu navegador. O código do win32 é útil na depuração de determinados tipos de problemas.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (não ativar no site de produção em direto)

Esta definição controla a funcionalidade de depuração. O Iisnode está integrado com o node-inspector. Ao ativar esta definição, ativar a depuração da sua aplicação de nó. Após a ativação desta definição, o iisnode cria arquivos de um inspetor do nó no diretório de "debuggerVirtualDir" na primeira solicitação de depuração para a aplicação de nó. É possível carregar o inspetor de nó ao enviar um pedido para http://yoursite/server.js/debug. Pode controlar o segmento de URL de depuração com a definição de 'debuggerPathSegment'. Por predefinição, debuggerPathSegment = "debug". Pode definir `debuggerPathSegment` para um GUID, por exemplo, por isso que ele é mais difícil de ser detetados por outros utilizadores.

Leia [depurar aplicações node. js no Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) para obter mais detalhes sobre a depuração.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Cenários e recomendações/resolução de problemas

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Meu aplicativo de nó está fazendo chamadas excessivas de saída

Muitos aplicativos iria querer fazer conexões de saída como parte da respetiva operação normal. Por exemplo, quando uma solicitação chega, a aplicação de nó gostaria entre em contato com uma API REST em outro lugar e obtenha algumas informações para processar o pedido. Gostaria de utilizar um agente ativo de manter, ao efetuar chamadas http ou https. Poderia usar o módulo de agentkeepalive seu agente alive keep quando fazer essas chamadas de saída.

O módulo de agentkeepalive garante que os soquetes são reutilizados na sua aplicação de Web do Azure VM. Criar um novo socket em cada solicitação de saída adiciona sobrecarga ao seu aplicativo. O seu aplicativo reutilizar sockets para pedidos de saída garante que seu aplicativo não excede o maxSockets alocadas por VM. A recomendação no App Service do Azure é definir o valor de maxSockets agentKeepAlive para um total de (4 instâncias de node.exe \* 40 maxSockets/instância) 160 sockets por VM.

Exemplo [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) configuração:

```nodejs
var keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> Este exemplo assume que tem 4 node.exe em execução na sua VM. Se tiver um número diferente de node.exe em execução na VM, tem de modificar o maxSockets definição em conformidade.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>Meu aplicativo de nó está a consumir muita CPU

Poderá receber uma recomendação do serviço de aplicações do Azure no seu portal sobre o consumo de cpu elevada. Também pode definir monitores para ver a determinados [métricas](web-sites-monitor.md). Ao verificar a utilização da CPU no [Dashboard do Portal do Azure](../azure-monitor/app/web-monitor-performance.md), verifique os valores de máx. de CPU para não perder os valores de pico.
Se considerar sua aplicação está a consumir muita CPU e não pode explicar por que, pode criar o perfil a aplicação de nó para descobrir.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>A aplicação de nó no serviço de aplicações do Azure com o Profiler V8 de criação de perfis

Por exemplo, digamos que tenha uma aplicação hello world que quer criar perfis da seguinte forma:

```nodejs
var http = require('http');
function WriteConsoleLog() {
    for(var i=0;i<99999;++i) {
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

Vá para o site da consola de depuração https://yoursite.scm.azurewebsites.net/DebugConsole

Entrar no seu diretório do site/wwwroot. Verá um prompt de comando, conforme mostrado no exemplo a seguir:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Execute o comando `npm install v8-profiler`.

Este comando instala o profiler v8 no nó\_diretório modules e todas as respetivas dependências.
Agora, edite sua Server. js para criar um perfil de seu aplicativo.

```nodejs
var http = require('http');
var profiler = require('v8-profiler');
var fs = require('fs');

function WriteConsoleLog() {
    for(var i=0;i<99999;++i) {
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

Os perfis de código anterior a WriteConsoleLog de função e, em seguida, grava a saída de perfil para o ficheiro de "profile.cpuprofile" em seu wwwroot de site. Envie um pedido para seu aplicativo. Vê um ficheiro de 'profile.cpuprofile' criado em seu wwwroot de site.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Transferir este ficheiro e abri-lo com ferramentas de F12 do Chrome. Premir a tecla F12 no Chrome, em seguida, escolha o **perfis** separador. Escolha o **carga** botão. Selecione o seu ficheiro de profile.cpuprofile que transferiu. Clique no perfil de que acabou de carregar.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Pode ver que 95% do tempo foi consumido pela função WriteConsoleLog. O resultado também mostra os números de linha exata e os ficheiros de origem que causou o problema.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Meu aplicativo de nó está a consumir demasiada memória

Se a sua aplicação está a consumir demasiada memória, verá um aviso do serviço de aplicações do Azure no seu portal sobre o consumo de memória elevada. Pode configurar monitores para ver a determinados [métricas](web-sites-monitor.md). Ao verificar a utilização da memória no [Dashboard do Portal do Azure](../azure-monitor/app/web-monitor-performance.md), certifique-se de que verifique os valores de máx. de memória para não perder os valores de pico.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Deteção de vazamento e Diff de Heap para node. js

Poderia usar [nó memwatch](https://github.com/lloyd/node-memwatch) vazamentos de para o ajudar a identificar a memória.
Pode instalar `memwatch` assim como criador de perfil v8 e editar o código para capturar e diff heaps para identificar vazamentos de memória em seu aplicativo.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Meu node.exe estão obtendo terminadas aleatoriamente

Há alguns motivos por que motivo node.exe é encerrado aleatoriamente:

1. A aplicação que está a gerar exceções não identificadas – d: de verificação\\doméstica\\LogFiles\\aplicação\\arquivo de log-errors para obter os detalhes na exceção gerada. Este ficheiro tem o rastreio de pilha para ajudar a depurar e corrigir a sua aplicação.
2. A aplicação está a consumir demasiada memória, o que está a afetar outros processos desde o início. Se a memória total da VM está próximo de 100%, seu node.exe poderia ser terminado pelo Gerenciador de processos. Gestor de processos interrompe alguns processos para permitir que outros processos obter uma oportunidade de se fazer algum trabalho. Para corrigir este problema, analisar seu aplicativo vazamentos de memória. Se a sua aplicação necessitar de grandes quantidades de memória, aumente verticalmente para uma VM maior (o que aumenta a quantidade de RAM disponível para a VM).

### <a name="my-node-application-does-not-start"></a>Meu aplicativo de nó não inicia

Se seu aplicativo estiver retornando 500 erros durante o arranque, pode haver alguns dos motivos:

1. NODE.exe não está presente no local correto. Verifique a definição de nodeProcessCommandLine.
2. Ficheiro de script principal não está presente no local correto. Verifique a Web. config e certifique-se de que o nome do ficheiro de script principal na secção de processadores coincide com o ficheiro de script principal.
3. Configuração de Web. config não está correta – verificar os nomes/valores de definições.
4. Arranque a frio – a aplicação está a demorar demasiado tempo a iniciar. Se seu aplicativo exige mais (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 segundos, o iisnode devolve um erro de 500. Aumente os valores dessas configurações de acordo com a hora de início de aplicação para impedir que o iisnode de exceder o tempo limite e devolver o erro 500.

### <a name="my-node-application-crashed"></a>Meu aplicativo de nó falhado

A aplicação que está a gerar exceções não identificadas – verificação `d:\\home\\LogFiles\\Application\\logging-errors.txt` ficheiro para obter os detalhes na exceção gerada. Este ficheiro tem o rastreio de pilha para ajudar a diagnosticar e corrigir a sua aplicação.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Meu aplicativo de nó leva muito tempo para iniciar (começar a frio)

A causa comum para horas de início de aplicação muito tempo é um grande número de ficheiros no nó\_módulos. A aplicação tenta carregar a maioria desses arquivos ao iniciar. Por predefinição, uma vez que os ficheiros são armazenados na partilha de rede no serviço de aplicações do Azure, a carregar vários ficheiros pode demorar tempo.
Algumas soluções para tornar esse processo mais rápido são:

1. Certifique-se de que tem uma estrutura de dependência simples e sem dependências duplicadas ao utilizar npm3 para instalar os módulos.
2. Tente lento para carregar o seu nó\_módulos e não carregar todos os módulos no início do aplicativo. Aos módulos de carregamento lento, a chamada para require('module') deve ser feita quando precisa, na verdade, o módulo na função antes da primeira execução de código do módulo.
3. Serviço de aplicações do Azure oferece um recurso chamado de local cache. Esta funcionalidade copia o conteúdo de partilha de rede para o disco local na VM. Uma vez que os ficheiros são locais, o tempo de carregamento do nó\_módulos é muito mais rápido.

## <a name="iisnode-http-status-and-substatus"></a>Estado de http do IISNODE e subestado

O `cnodeconstants` [ficheiro de origem](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) listas todos o iisnode de combinações possíveis de estado/subestado podem retornar devido a um erro.

Ativar FREB para a sua aplicação ver o código de erro do win32 (Certifique-se de ativar FREB apenas em sites de não produção por motivos de desempenho).

| Estado de Http | Subestado de HTTP | Razão possível? |
| --- | --- | --- |
| 500 |1000 |Ocorreu algum problema de expedir a solicitação para o IISNODE – Verifique se node.exe foi iniciada. NODE.exe poderia ter falhou ao iniciar. Verifique a configuração de Web. config para erros. |
| 500 |1001 |-Win32Error 0x2 - aplicação não está a responder ao URL. Verifique a regras de reescrita de URL ou verifique se a aplicação express tem as corretas rotas definidas. -Win32Error 0x6d – pipe nomeado está ocupado – Node.exe não está a aceitar pedidos porque o pipe está ocupado. Verifique a utilização elevada da cpu. -Outros erros – Verifique se node.exe falhou. |
| 500 |1002 |NODE.exe falhou – d: de verificação\\doméstica\\LogFiles\\errors de registo de rastreio de pilha. |
| 500 |1003 |Configuração de pipe problema – a configuração de pipe nomeado é incorreta. |
| 500 |1004-1018 |Ocorreu um erro ao enviar o pedido ou processar a resposta de/para node.exe. Verifique se node.exe falhou. verificar d:\\doméstica\\LogFiles\\errors de registo de rastreio de pilha. |
| 503 |1000 |Não existe memória suficiente para alocar mais ligações de pipe nomeado. Verificação por que a aplicação está a consumir memória tanta. Verifique o valor de definição de maxConcurrentRequestsPerProcess. Se não é infinito e tiver muitos pedidos, aumente este valor para evitar este erro. |
| 503 |1001 |Pedido não foi possível emitir node.exe porque está a reciclar o aplicativo. Depois do aplicativo tem reciclado, os pedidos devem ser fornecidos normalmente. |
| 503 |1002 |Não foi possível emitir o código de erro do win32 de verificação para o real motivo – pedido para um node.exe. |
| 503 |1003 |Pipe nomeado está demasiado ocupado – Certifique-se de que se node.exe está a consumir CPU excessiva |

NODE.exe tem uma configuração chamada `NODE_PENDING_PIPE_INSTANCES`. No serviço de aplicações do Azure, este valor é definido como 5000. O que significa que node.exe pode aceitar 5000 pedidos num momento no pipe nomeado. Este valor deve ser bom o suficiente para a maioria dos aplicativos de nó em execução no App Service do Azure. Não deverá ver 503.1003 no App Service do Azure devido ao valor alto para o `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Mais recursos

Siga estas ligações para saber mais sobre aplicações node. js no App Service do Azure.

* [Introdução às aplicações Web Node.js no Serviço de Aplicações do Azure](app-service-web-get-started-nodejs.md)
* [Como depurar uma aplicação Web Node.js no Serviço de Aplicações do Azure](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Utilizar Módulos do Node.js com aplicações do Azure](../nodejs-use-node-modules-azure-apps.md)
* [Aplicações de Web do serviço de aplicações do Azure: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centro de Programadores do Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Explorar a Consola de Depuração do Kudu Super Secreta](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
