---
title: Práticas recomendadas e solução de problemas do node. js
description: Conheça as práticas recomendadas e as etapas de solução de problemas para aplicativos node. js em execução no serviço Azure App.
author: ranjithr
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: bwren
ms.custom: seodec18
ms.openlocfilehash: 75195bd7ad228bb66dfd21d2c65997cc8c02680e
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672051"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Guia de práticas recomendadas e solução de problemas para aplicativos de nó em janelas de serviço Azure App

Neste artigo, você aprenderá as práticas recomendadas e as etapas de solução de problemas para [aplicativos de nó](app-service-web-get-started-nodejs.md) em execução no serviço de Azure app (com [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Tenha cuidado ao usar as etapas de solução de problemas em seu site de produção. A recomendação é solucionar problemas de seu aplicativo em uma configuração de não produção, por exemplo, seu slot de preparo e quando o problema for corrigido, trocar o slot de preparo pelo slot de produção.
>

## <a name="iisnode-configuration"></a>Configuração do IISNODE

Esse [arquivo de esquema](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) mostra todas as configurações que você pode definir para iisnode. Algumas das configurações que são úteis para seu aplicativo:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Essa configuração controla o número de processos de nó que são iniciados por aplicativo do IIS. O valor padrão é 1. Você pode iniciar quantos node. EXEs forem sua contagem de vCPU de VM alterando o valor para 0. O valor recomendado é 0 para a maioria dos aplicativos, para que você possa usar todos os vCPUs em seu computador. O Node. exe é de thread único, portanto, um node. exe consome um máximo de 1 vCPU. Para obter o desempenho máximo do seu aplicativo de nó, você deseja usar todos os vCPUs.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Essa configuração controla o caminho para o Node. exe. Você pode definir esse valor para apontar para a versão do node. exe.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Essa configuração controla o número máximo de solicitações simultâneas enviadas pelo iisnode para cada node. exe. No serviço Azure App, o valor padrão é infinito. Você pode configurar o valor dependendo de quantas solicitações seu aplicativo recebe e a rapidez com que seu aplicativo processa cada solicitação.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Essa configuração controla o número máximo de vezes que o iisnode tenta fazer a conexão no pipe nomeado para enviar as solicitações para o Node. exe. Essa configuração em combinação com namedPipeConnectionRetryDelay determina o tempo limite total de cada solicitação dentro de iisnode. O valor padrão é 200 no serviço Azure App. Tempo limite total em segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Essa configuração controla a quantidade de tempo (em MS) iisnode aguarda entre cada repetição para enviar a solicitação para o Node. exe no pipe nomeado. O valor padrão é 250 MS.
Tempo limite total em segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000

Por padrão, o tempo limite total em iisnode no serviço Azure App é 200 \* 250 MS = 50 segundos.

### <a name="logdirectory"></a>logDirectory

Essa configuração controla o diretório em que iisnode registra stdout/stderr. O valor padrão é iisnode, que é relativo ao diretório do script principal (diretório em que o Server. js principal está presente)

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Essa configuração controla qual versão do node-Inspector iisnode usa ao depurar o aplicativo de nó. Atualmente, iisnode-0.7.3 0.7.3. dll e iisnode. dll são os únicos dois valores válidos para essa configuração. O valor padrão é iisnode-0.7.3 0.7.3. dll. A versão iisnode-0.7.3 0.7.3. dll usa node-Inspector-0.7.3 e usa o Web Sockets. Habilite o Web Sockets em seu webapp do Azure para usar esta versão. Consulte <https://ranjithblogs.azurewebsites.net/?p=98> para obter mais detalhes sobre como configurar o iisnode para usar o novo node-Inspector.

### <a name="flushresponse"></a>flushResponse

O comportamento padrão do IIS é que ele armazena em buffer os dados de resposta de até 4 MB antes da liberação, ou até o final da resposta, o que vier primeiro. o iisnode oferece uma definição de configuração para substituir esse comportamento: para liberar um fragmento do corpo da entidade de resposta assim que o iisnode o receber do node. exe, você precisará definir o atributo iisnode/@flushResponse em Web. config como ' true ':

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Habilitar a liberação de cada fragmento do corpo da entidade de resposta adiciona sobrecarga de desempenho que reduz a taxa de transferência do sistema em aproximadamente 5% (a partir de v 0.1.13). O melhor para definir o escopo dessa configuração somente para pontos de extremidade que exigem streaming de resposta (por exemplo, usando o elemento `<location>` no Web. config)

Além disso, para aplicativos de streaming, você também deve definir responseBufferLimit do manipulador de iisnode como 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Uma lista de arquivos separados por ponto e vírgula que são observados para alterações. Qualquer alteração em um arquivo faz com que o aplicativo seja reciclado. Cada entrada consiste em um nome de diretório opcional, bem como um nome de arquivo necessário, que são relativos ao diretório em que o ponto de entrada do aplicativo principal está localizado. Curingas são permitidos somente na parte do nome do arquivo. O valor padrão é `*.js;iisnode.yml`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

O valor padrão é false. Se habilitada, o aplicativo de nó pode se conectar a um pipe nomeado (variável de ambiente IISNODE\_controle de\_PIPE) e enviar uma mensagem de "reciclagem". Isso faz com que w3wp seja reciclado normalmente.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

O valor padrão é 0, o que significa que esse recurso está desabilitado. Quando definido como um valor maior que 0, iisnode fará a despágina de todos os seus processos filho a cada ' idlePageOutTimePeriod ' em milissegundos. Consulte a [documentação](/windows/desktop/api/psapi/nf-psapi-emptyworkingset) para entender o que o Page out significa. Essa configuração é útil para aplicativos que consomem uma grande quantidade de memória e desejam paginar a memória para o disco ocasionalmente para liberar RAM.

> [!WARNING]
> Tenha cuidado ao habilitar as seguintes definições de configuração em aplicativos de produção. A recomendação é não habilitá-los em aplicativos de produção ao vivo.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

O valor padrão é false. Se definido como true, iisnode adicionará um cabeçalho de resposta HTTP `iisnode-debug` a cada resposta HTTP, ele enviará o valor do cabeçalho `iisnode-debug` será uma URL. Partes individuais de informações de diagnóstico podem ser obtidas examinando o fragmento de URL, no entanto, uma visualização está disponível abrindo a URL em um navegador.

### <a name="loggingenabled"></a>loggingEnabled

Essa configuração controla o registro em log de stdout e stderr por iisnode. Iisnode captura stdout/stderr de processos de nó que ele inicia e grava no diretório especificado na configuração ' logDirectory '. Depois que isso estiver habilitado, seu aplicativo gravará logs no sistema de arquivos e, dependendo da quantidade de log feita pelo aplicativo, poderá haver implicações de desempenho.

### <a name="deverrorsenabled"></a>devErrorsEnabled

O valor padrão é false. Quando definido como true, iisnode exibe o código de status HTTP e o código de erro do Win32 em seu navegador. O código Win32 é útil na depuração de determinados tipos de problemas.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (não habilitar no site de produção ao vivo)

Essa configuração controla o recurso de depuração. O Iisnode é integrado ao node-Inspector. Ao habilitar essa configuração, você habilita a depuração do seu aplicativo de nó. Ao habilitar essa configuração, o iisnode cria arquivos de Inspetor de nó no diretório ' debuggerVirtualDir ' na primeira solicitação de depuração para o aplicativo de nó. Você pode carregar o node-Inspector enviando uma solicitação para `http://yoursite/server.js/debug`. Você pode controlar o segmento de URL de depuração com a configuração ' debuggerPathSegment '. Por padrão, debuggerPathSegment = ' Debug '. Você pode definir `debuggerPathSegment` para um GUID, por exemplo, para que seja mais difícil de ser descoberto por outras pessoas.

Leia [depurar aplicativos node. js no Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) para obter mais detalhes sobre a depuração.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Cenários e recomendações/solução de problemas

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Meu aplicativo de nó está fazendo chamadas de saída excessivas

Muitos aplicativos desejariam fazer conexões de saída como parte de sua operação regular. Por exemplo, quando uma solicitação chega, seu aplicativo de nó deseja entrar em contato com uma API REST em outro lugar e obter algumas informações para processar a solicitação. Você desejaria usar um agente Keep Alive ao fazer chamadas http ou HTTPS. Você pode usar o módulo agentkeepalive como seu agente Keep Alive ao fazer essas chamadas de saída.

O módulo agentkeepalive garante que os soquetes sejam reutilizados em sua VM webapp do Azure. A criação de um novo soquete em cada solicitação de saída adiciona sobrecarga ao seu aplicativo. Fazer com que seu aplicativo reutilize soquetes para solicitações de saída garante que seu aplicativo não exceda os maxSockets alocados por VM. A recomendação no serviço de Azure App é definir o valor de maxSockets de agentKeepAlive para um total de (4 instâncias de Node. exe \* 40 maxSockets/instância) 160 soquetes por VM.

Exemplo de configuração de [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) :

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> Este exemplo pressupõe que você tenha quatro node. exe em execução em sua VM. Se você tiver um número diferente de Node. exe em execução na VM, será necessário modificar a configuração maxSockets de acordo.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>Meu aplicativo de nó está consumindo muita CPU

Você pode receber uma recomendação do serviço Azure App em seu portal sobre o alto consumo de CPU. Você também pode configurar monitores para observar determinadas [métricas](web-sites-monitor.md). Ao verificar o uso da CPU no [painel do portal do Azure](../azure-monitor/app/web-monitor-performance.md), verifique os valores máximos da CPU para que você não perca os valores de pico.
Se você acredita que seu aplicativo está consumindo muita CPU e não pode explicar por quê, você pode criar o perfil de seu aplicativo de nó para descobrir.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>Criando perfil do aplicativo de nó no serviço de Azure App com o V8-Profiler

Por exemplo, digamos que você tenha um aplicativo Olá, mundo do qual deseja criar o perfil da seguinte maneira:

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

Vá para o site do console de depuração `https://yoursite.scm.azurewebsites.net/DebugConsole`

Vá para o diretório site/wwwroot. Você verá um prompt de comando, conforme mostrado no exemplo a seguir:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Execute o comando `npm install v8-profiler`.

Esse comando instala o V8-Profiler em nó\_diretório de módulos e todas as suas dependências.
Agora, edite o Server. js para criar o perfil do seu aplicativo.

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

O código anterior cria o perfil da função WriteConsoleLog e, em seguida, grava a saída do perfil no arquivo ' Profile. cpuprofile ' em seu site wwwroot. Envie uma solicitação para seu aplicativo. Você verá um arquivo ' Profile. cpuprofile ' criado no seu site wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Baixe esse arquivo e abra-o com as ferramentas do Chrome F12. Pressione F12 no Chrome e escolha a guia **perfis** . escolha o botão **carregar** . Selecione o arquivo Profile. cpuprofile que você baixou. Clique no perfil que você acabou de carregar.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Você pode ver que 95% do tempo foi consumido pela função WriteConsoleLog. A saída também mostra os números de linha e os arquivos de origem exatos que causaram o problema.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Meu aplicativo de nó está consumindo muita memória

Se seu aplicativo estiver consumindo muita memória, você verá um aviso de Azure App serviço em seu portal sobre o alto consumo de memória. Você pode configurar monitores para observar determinadas [métricas](web-sites-monitor.md). Ao verificar o uso de memória no [painel do portal do Azure](../azure-monitor/app/web-monitor-performance.md), verifique os valores máximos de memória para que você não perca os valores de pico.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Detecção de vazamentos e comparação de heap para node. js

Você pode usar [node-memwatch](https://github.com/lloyd/node-memwatch) para ajudá-lo a identificar vazamentos de memória.
Você pode instalar `memwatch` assim como o V8-Profiler e editar seu código para capturar e comparar heaps para identificar os vazamentos de memória em seu aplicativo.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Meus node. exe estão sendo eliminados aleatoriamente

Há alguns motivos pelos quais o Node. exe é desligado aleatoriamente:

1. Seu aplicativo está lançando exceções não capturadas – Verifique d:\\Home\\arquivos de log\\aplicativo\\arquivo logging-Errors. txt para obter os detalhes sobre a exceção lançada. Esse arquivo tem o rastreamento de pilha para ajudar a depurar e corrigir seu aplicativo.
2. Seu aplicativo está consumindo muita memória, o que está afetando outros processos de começar. Se a memória total da VM estiver próxima de 100%, o Node. exe poderá ser eliminado pelo Gerenciador de processos. O Process Manager elimina alguns processos para permitir que outros processos tenham uma chance de realizar algum trabalho. Para corrigir esse problema, perfilie seu aplicativo para vazamentos de memória. Se seu aplicativo exigir grandes quantidades de memória, escale verticalmente para uma VM maior (o que aumenta a RAM disponível para a VM).

### <a name="my-node-application-does-not-start"></a>O aplicativo do meu nó não é iniciado

Se seu aplicativo estiver retornando erros 500 quando for iniciado, pode haver alguns motivos:

1. O Node. exe não está presente no local correto. Verifique a configuração nodeProcessCommandLine.
2. O arquivo de script principal não está presente no local correto. Verifique o Web. config e certifique-se de que o nome do arquivo de script principal na seção de manipuladores corresponda ao arquivo de script principal.
3. A configuração de Web. config não está correta – Verifique os nomes/valores das configurações.
4. Início frio – o seu aplicativo está demorando muito para começar. Se seu aplicativo demorar mais do que (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000 segundos, iisnode retornará um erro 500. Aumente os valores dessas configurações para corresponder à hora de início do seu aplicativo para evitar que o iisnode atinja o tempo limite e retornando o erro 500.

### <a name="my-node-application-crashed"></a>Meu aplicativo de nó falhou

Seu aplicativo está lançando exceções não capturadas – Verifique `d:\\home\\LogFiles\\Application\\logging-errors.txt` arquivo para obter os detalhes sobre a exceção lançada. Esse arquivo tem o rastreamento de pilha para ajudar a diagnosticar e corrigir seu aplicativo.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>O aplicativo de nó leva muito tempo para começar (inicialização a frio)

A causa comum para tempos de início de aplicativo longos é um grande número de arquivos no nó\_módulos. O aplicativo tenta carregar a maioria desses arquivos ao iniciar. Por padrão, como os arquivos são armazenados no compartilhamento de rede no serviço Azure App, o carregamento de muitos arquivos pode levar tempo.
Algumas soluções para tornar esse processo mais rápido são:

1. Verifique se você tem uma estrutura de dependência simples e nenhuma dependência duplicada usando npm3 para instalar seus módulos.
2. Tente carregar lentamente os módulos de\_de nó e não carregue todos os módulos no início do aplicativo. Para módulos de carga lenta, a chamada para exigir (' module ') deve ser feita quando você realmente precisa do módulo dentro da função antes da primeira execução do código do módulo.
3. Azure App serviço oferece um recurso chamado cache local. Esse recurso copia o conteúdo do compartilhamento de rede para o disco local na VM. Como os arquivos são locais, o tempo de carregamento dos módulos de\_de nó é muito mais rápido.

## <a name="iisnode-http-status-and-substatus"></a>Status e substatus HTTP do IISNODE

O [arquivo de origem](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) `cnodeconstants` lista todas as combinações de status/substatus possíveis que o iisnode pode retornar devido a um erro.

Habilite o FREB para que seu aplicativo Veja o código de erro do Win32 (certifique-se de habilitar o FREB somente em sites que não sejam de produção por motivos de desempenho).

| Status do http | Substatus http | Possível motivo? |
| --- | --- | --- |
| 500 |1000 |Houve algum problema ao despachar a solicitação para IISNODE – Verifique se o Node. exe foi iniciado. O Node. exe pode ter falhado ao iniciar. Verifique se há erros na configuração de Web. config. |
| 500 |1001 |-Win32Error 0x2-o aplicativo não está respondendo à URL. Verifique as regras de reescrita de URL ou verifique se o aplicativo expresso tem as rotas corretas definidas. -Win32Error 0x6d – o pipe nomeado está ocupado – node. exe não está aceitando solicitações porque o pipe está ocupado. Verifique o alto uso da CPU. -Outros erros – Verifique se o Node. exe falhou. |
| 500 |1002 |Node. exe falhou – Verifique d:\\Home\\LogFiles\\Logging-Errors. txt para rastreamento de pilha. |
| 500 |1003 |Problema de configuração de pipe – a configuração de pipe nomeado está incorreta. |
| 500 |1004-1018 |Ocorreu um erro ao enviar a solicitação ou processar a resposta para/do node. exe. Verifique se o Node. exe falhou. Marque d:\\Home\\arquivos de log\\Logging-Errors. txt para rastreamento de pilha. |
| 503 |1000 |Não há memória suficiente para alocar mais conexões de pipe nomeado. Verifique por que seu aplicativo está consumindo tanta memória. Verifique o valor da configuração maxConcurrentRequestsPerProcess. Se não for infinito e você tiver muitas solicitações, aumente esse valor para evitar esse erro. |
| 503 |1001 |A solicitação não pôde ser expedida para o Node. exe porque o aplicativo está sendo reciclado. Depois que o aplicativo for reciclado, as solicitações deverão ser atendidas normalmente. |
| 503 |1002 |Verifique o código de erro Win32 para obter o motivo real – a solicitação não pôde ser expedida para um node. exe. |
| 503 |1003 |O pipe nomeado está muito ocupado – Verifique se o Node. exe está consumindo CPU excessiva |

O NODE. exe tem uma configuração chamada `NODE_PENDING_PIPE_INSTANCES`. No serviço Azure App, esse valor é definido como 5000. O que significa que o Node. exe pode aceitar solicitações de 5000 por vez no pipe nomeado. Esse valor deve ser bom o suficiente para a maioria dos aplicativos de nó em execução no serviço Azure App. Você não deve ver 503,1003 no serviço Azure App devido ao valor alto para o `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Mais recursos

Siga estes links para saber mais sobre os aplicativos node. js no serviço Azure App.

* [Introdução às aplicações Web Node.js no Serviço de Aplicações do Azure](app-service-web-get-started-nodejs.md)
* [Como depurar uma aplicação Web Node.js no Serviço de Aplicações do Azure](https://blogs.msdn.microsoft.com/azureossds/2018/08/03/debugging-node-js-apps-on-azure-app-services/)
* [Utilizar Módulos do Node.js com aplicações do Azure](../nodejs-use-node-modules-azure-apps.md)
* [Aplicações Web do Serviço de Aplicações do Azure: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centro de Programadores do Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Explorar a Consola de Depuração do Kudu Super Secreta](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
