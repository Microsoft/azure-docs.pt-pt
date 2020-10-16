---
title: Falha de carga SDK de resolução de problemas para aplicações web JavaScript - Azure Application Insights
description: Como resolver falhas de carga SDK para aplicações web JavaScript
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: 30c7caef4143b1a7cdba959971ff7689f986cb9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333261"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>Falha de carga SDK na resolução de problemas para aplicações web JavaScript

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

A exceção de falha de carga SDK é criada e reportada pelo snippet JavaScript (v3 ou mais tarde) quando deteta que o script SDK não conseguiu descarregar ou inicializar. Simplisticamente, o cliente dos seus utilizadores finais (browser) não foi capaz de descarregar o App Insights SDK, ou inicializar a partir da página de hospedagem identificada e, portanto, não será reportada nenhuma telemetria ou eventos.

![Visão geral da falha do navegador do portal Azure](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> Esta exceção é suportada em todos os principais navegadores que suportam a apreensão() API ou XMLHttpRequest. Isto exclui o IE 8 e abaixo, pelo que não obterá este tipo de exceção reportado a partir desses navegadores (a menos que o seu ambiente inclua um polifill de busca).

![detalhe de exceção do navegador](./media/javascript-sdk-load-failure/exception.png)

Os detalhes da pilha incluem as informações básicas com os URLs a serem usados pelo utilizador final.

| Nome                      | Descrição                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;Ponto &nbsp; final do CDN&gt; | O URL que foi usado (e falhou) para descarregar o SDK.                                                      |
| &lt;Link de ajuda &nbsp;&gt;    | Um URL que se liga à documentação de resolução de problemas (esta página).                                              |
| &lt;&nbsp;URL anfitrião&gt;     | O URL completo da página que o utilizador final estava a usar.                                                    |
| &lt;URL de &nbsp; ponto final&gt; | O URL que foi usado para reportar a exceção, este valor pode ser útil para identificar se a página de hospedagem foi acedida a partir da internet pública ou de uma nuvem privada.

As razões mais comuns para esta exceção ocorrer:

- Falha intermitente na conectividade da rede.
- Falha do CDN de Insights de Aplicação.
- A SDK não iniciaisizou depois de carregar o script.
- Insights de Aplicação JavaScript CDN foi bloqueado.

[A falha intermitente na conectividade](#intermittent-network-connectivity-failure) da rede é a razão mais comum para esta exceção, especialmente em cenários de roaming móvel onde os utilizadores perdem a conectividade da rede intermitentemente.

As seguintes secções descreverão como resolver problemas com cada potencial causa deste erro.

> [!NOTE]
> Vários dos passos de resolução de problemas assumem que a sua aplicação tem controlo direto do script/tag Snippet &lt; e da sua &gt; configuração que são devolvidas como parte da página HTML do anfitrião. Se não o fizer, os passos identificados não se aplicarão ao seu cenário.

## <a name="intermittent-network-connectivity-failure"></a>Falha intermitente na conectividade da rede

**Se o utilizador estiver a sofrer falhas intermitentes de conectividade da rede, então existem menos soluções possíveis e, em geral, resolver-se-ão durante um curto período de tempo.** Por exemplo, se o utilizador recarregar o seu site (atualiza a página) os ficheiros serão (eventualmente) descarregados e em cache localmente até que uma versão atualizada seja lançada.

> [!NOTE]
> Se esta exceção for persistente e ocorrer em muitos dos seus utilizadores (diagnosticada por um nível rápido e sustentado desta exceção sendo reportada) juntamente com uma redução da telemetria normal do cliente, então os problemas de conectividade intermitente da rede não são _a_ verdadeira causa do problema e deve continuar a diagnosticar com os outros problemas possíveis conhecidos.

Com esta situação de hospedagem do SDK no seu próprio CDN é improvável que forneça ou reduza as ocorrências desta exceção, uma vez que o seu próprio CDN será afetado pelo mesmo problema.

O mesmo acontece quando se utiliza a solução de pacotes SDK através de pacotes NPM. No entanto, do ponto de vista dos utilizadores finais, quando isto ocorre, toda a sua aplicação não consegue carregar/inicializar (em vez de _apenas_ a telemetria SDK - que não vêem visualmente), pelo que provavelmente irão refrescar o seu site até que esteja completamente carregado.

Também pode tentar utilizar [pacotes NPM](#use-npm-packages-to-embed-the-application-insight-sdk) para incorporar o SDK Application Insights.

Para minimizar a falha intermitente de conectividade da rede, implementámos Cache-Control cabeçalhos em todos os ficheiros CDN para que, uma vez que o navegador do utilizador final tenha descarregado a versão atual do SDK, não precisará de descarregar novamente e o navegador reutilizará a cópia previamente obtida (ver [como funciona o caching).](../../cdn/cdn-how-caching-works.md) Se a verificação de caching falhar ou tiver havido uma nova versão, então o navegador do utilizador final terá de descarregar a versão atualizada. Assim, pode ver um nível de fundo de _"ruído"_ no cenário de falha de verificação ou um pico temporário quando ocorre uma nova versão e é geralmente disponibilizado (implantado na CDN).
 
## <a name="application-insights-cdn-outage"></a>Falha do CDN de Insights de Aplicação

Pode confirmar se existe uma falha de CDN do Application Insights ao tentar aceder ao ponto final do CDN diretamente do navegador (por exemplo, https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js) a partir de um local diferente do dos seus utilizadores finais, provavelmente da sua própria máquina de desenvolvimento (assumindo que a sua organização não bloqueou este domínio).

Se confirmar que há uma paragem, pode [criar um novo bilhete de suporte](https://azure.microsoft.com/support/create-ticket/) ou tentar alterar o URL utilizado para descarregar o SDK.

### <a name="change-the-cdn-endpoint"></a>Alterar o ponto final do CDN
  
À medida que o snippet e a sua configuração são devolvidos pela sua aplicação como parte de cada página gerada, pode alterar a configuração do snippet `src` para usar um URL diferente para o SDK. Ao utilizar esta abordagem, pode contornar a questão bloqueada do CDN, uma vez que o novo URL não deve ser bloqueado.

Insights de aplicação atuais JavaScript SDK CDN pontos finais
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> O `https://js.monitor.azure.com/` ponto final é um pseudónimo que nos permite alternar entre fornecedores de CDN dentro de aproximadamente 5 minutos, sem a necessidade de alterar qualquer config. Isto permite-nos corrigir problemas relacionados com a CDN detetados mais rapidamente se um fornecedor de CDN estiver a ter problemas regionais ou globais sem exigir que todos ajustem as suas definições.

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>SDK não iniciaisizou depois de carregar o script

Quando o SDK não inicia, o &lt; script / foi descarregado com sucesso a partir do &gt; CDN, mas falha durante a inicialização. Isto pode ser devido a dependências desaparecidas ou inválidas ou alguma forma de exceção JavaScript.

A primeira coisa a verificar é se o SDK foi descarregado com sucesso, se o script não foi descarregado então este cenário __não__ é a causa da exceção de falha de carga SDK.

Verificação rápida: Utilizando um navegador que suporta ferramentas de desenvolvimento (F12), valide no separador de rede que o script definido na configuração do ```src``` snippet foi descarregado com um código de resposta de 200 (sucesso) ou um 304 (não alterado). Também pode usar uma ferramenta como violinista para rever o tráfego da rede.

As secções abaixo incluem diferentes opções de reporte, recomendará a criação de um bilhete de apoio ou a colocação de um problema no GitHub.

 Regras básicas de informação:

- Se o problema estiver apenas a afetar um pequeno número de utilizadores e um ouso ou subconjunto específico ou subconjunto de versão do navegador (verifique os detalhes da exceção reportada), então é provável que seja um problema de utilizador final ou ambiente, o que provavelmente exigirá que a sua aplicação forneça `polyfill` implementações adicionais. Para estes, [arquiva um problema no GitHub.](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- Se este problema está a afetar toda a sua aplicação e todos os seus utilizadores estão afetados, então é provavelmente um problema relacionado com a libertação e, portanto, você deve [criar um novo bilhete de suporte](https://azure.microsoft.com/support/create-ticket/).

### <a name="javascript-exceptions"></a>Exceções JavaScript

Primeiro permite verificar se existem exceções javaScript, utilizando um navegador que suporta ferramentas de desenvolvimento (F12) carregar a página e rever se ocorrerem exceções.

Se houver exceções a serem reportadas no script SDK (por exemplo ai.2.min.js), então isto pode indicar que a configuração transmitida para o SDK contém configuração inesperada ou em falta ou uma versão defeituosa foi implantada no CDN.

Para verificar se há uma configuração defeituosa, altere a configuração passada para o corte (se não já for) de modo a que inclua apenas a sua chave de instrumentação como um valor de cadeia.

> src: " https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js ", "<br />
> cfg:{<br />
> instrumentaçãoKey: "INSTRUMENTATION_KEY"<br />
> }});<br />

Se ao utilizar esta configuração mínima ainda estiver a ver uma exceção JavaScript no script SDK, [crie um novo bilhete de suporte,](https://azure.microsoft.com/support/create-ticket/) pois isso exigirá que a construção defeituosa seja revirada, uma vez que é provavelmente um problema com uma versão recentemente implementada.

Se a exceção desaparecer, então o problema é provavelmente causado por um tipo de incompatibilidade ou valor inesperado. Comece a adicionar as suas opções de configuração um a um e teste até que a exceção volte a ocorrer. Em seguida, verifique a documentação do item que está a causar o problema. Se a documentação não for clara ou precisar de assistência, [apresente um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).

Se a sua configuração foi previamente implementada e a funcionar, mas acabou de começar a reportar esta exceção, então pode ser um problema com uma versão recém-implantada, verifique se está a afetar apenas um pequeno conjunto de utilizadores/navegador e ou [arquivar um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues) ou criar um novo bilhete de [suporte.](https://azure.microsoft.com/support/create-ticket/)

### <a name="enable-console-debugging"></a>Ativar a depuragem da consola

Assumindo que não existem exceções a serem lançadas, o próximo passo é permitir a depuração da consola adicionando a `loggingLevelConsole` definição à configuração, isto irá enviar todos os erros de inicialização e avisos para a consola dos navegadores (normalmente disponível através das ferramentas de desenvolvimento (F12)). Quaisquer erros reportados devem ser autoexplicativos e se precisar de assistência [adicional, apresente um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).

> cfg:{<br />
> instrumentaçãoKey: "INSTRUMENTATION_KEY",<br />
> loggingLevelConsole: 2<br />
> }});<br />

> [!NOTE]
> Durante a inicialização, o SDK realiza alguns controlos básicos para as dependências importantes conhecidas. Se estas não forem fornecidas pelo tempo de funcionação atual, reportará as falhas como mensagens de aviso para a consola, mas apenas se a for superior a `loggingLevelConsole` zero.

Se ainda não tiver sido inicializada, tente ativar a definição de ```enableDebug``` configuração. Isto fará com que todos os erros internos sejam lançados como uma exceção (o que fará com que a telemetria se perca). Como esta é uma definição de desenvolvedor apenas, provavelmente ficará ruidosa com exceções sendo lançadas como parte de algumas verificações internas, por isso terá de rever cada exceção para determinar que problema está a causar a falha do SDK. Utilize a versão não minificada do script (note a extensão abaixo é ".js" e não ".min.js") caso contrário as exceções serão ilegíveis.

> [!WARNING]
> Trata-se de uma definição apenas de desenvolvedor e nunca deve ser ativada num ambiente de produção completo, uma vez que perderá a telemetria.

> src: " https://az416426.vo.msecnd.net/scripts/b/ai.2.js ", "<br />
> cfg:{<br />
> instrumentaçãoKey: "INSTRUMENTATION_KEY",<br />
> enableDebug: verdadeiro<br />
> }});<br />

Se isto ainda não fornecer qualquer informação, deverá [arquivar um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues) com os detalhes e um site de exemplo se tiver um. Inclua a versão do navegador, o sistema operativo e os detalhes do quadro JS para ajudar a identificar o problema.

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>A aplicação Insights JavaScript CDN foi bloqueada

O bloqueio do CDN é possível quando um ponto final javaScript SDK CDN foi reportado e/ou identificado como inseguro. Quando isto ocorrer, fará com que o ponto final seja publicamente listado e os consumidores destas listas começarão a bloquear todos os acessos.

Para resolver, requer que o proprietário do ponto final da CDN trabalhe com a entidade de listagem de blocos que marcou o ponto final como inseguro para que possa ser removido da lista relevante.

Verifique se o ponto final da CDN foi identificado como inseguro.
- [Relatório de Transparência do Google](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

Dependendo da frequência que a aplicação, firewall ou ambiente atualizam as suas cópias locais destas listas, pode demorar uma quantidade considerável de tempo e/ou exigir uma intervenção manual por parte dos utilizadores finais ou departamentos de TI corporativos para forçar uma atualização ou permitir explicitamente que os pontos finais do CDN resolvam o problema.

Se o ponto final da CDN for identificado como inseguro, [crie um bilhete de apoio](https://azure.microsoft.com/support/create-ticket/) para garantir que o problema seja resolvido o mais rapidamente possível.

Para *potencialmente* contornar este problema mais rapidamente, pode [alterar o ponto final SDK CDN](#change-the-cdn-endpoint).

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>Application Insights JavaScript CDN está bloqueado (por utilizador final - bloqueado pelo navegador; bloqueador instalado; firewall pessoal)

Verifique se os seus utilizadores finais têm:

- Instalou um plug-in de navegador (normalmente algum tipo de bloqueador de anúncios, malware ou pop-up).
- Bloqueado (ou não permitido) os pontos finais do Application Insights CDN no seu navegador ou procuração.
- Configurar uma regra de firewall que está a fazer com que o domínio CDN para o SDK seja bloqueado (ou a entrada de DNS não seja resolvida).

Se eles configuraram alguma destas opções, você precisará trabalhar com elas (ou fornecer documentação) para permitir os pontos finais cdN.

É possível que o plug-in instalado esteja a utilizar a lista de bloqueios públicos. Se não for esse o caso, então é mais provável que seja outra solução configurada manualmente ou está a usar uma lista de blocos de domínio privado.

#### <a name="add-exceptions-for-cdn-endpoints"></a>Adicionar exceções para os pontos finais do CDN

Trabalhe com os seus utilizadores finais ou forneça documentação informando-os de que devem permitir que scripts dos pontos finais do Application Insights CDN sejam descarregados, incluindo-os na lista de exceções de regras plug-in ou firewall do seu navegador (variará em função do ambiente do utilizador final).

Aqui está um exemplo de como configurar o [Chrome para permitir ou bloquear o acesso aos websites.](https://support.google.com/chrome/a/answer/7532419?hl=en)

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Application Insights CDN está bloqueado (por firewall corporativa)

Se os seus utilizadores finais estão numa rede corporativa, então é provavelmente a sua solução de firewall e que o seu departamento de TI implementou alguma forma de sistema de filtragem de internet. Neste caso, terá de trabalhar com eles para permitir as regras necessárias para os seus utilizadores finais.

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>Adicionar exceções para os pontos finais da CDN para as empresas

  Isto é semelhante a [adicionar exceções para os utilizadores finais](#add-exceptions-for-cdn-endpoints), mas você precisará trabalhar com o departamento de TI da empresa para que eles configurem os pontos finais do Application Insights CDN para serem descarregados incluindo (ou removendo) em qualquer serviço de listagem de blocos de domínio ou de listagem de permiti-anúncio.

  > [!WARNING]
  > Se o utilizador corporativo estiver a utilizar uma [nuvem privada](https://azure.microsoft.com/overview/what-is-a-private-cloud/) e não conseguir permitir qualquer forma de exceção para fornecer aos seus utilizadores internos acesso público aos pontos finais do CDN, então terá de utilizar os [pacotes NPM do Application Insights](https://www.npmjs.com/package/applicationinsights) ou hospedar o SDK Application Insights no seu próprio CDN.  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>Resolução adicional de problemas para CDN bloqueado

> [!NOTE]
> Se os seus utilizadores estiverem a utilizar uma [nuvem privada](https://azure.microsoft.com/overview/what-is-a-private-cloud/) e não tiverem acesso à internet pública, terá de hospedar o SDK no seu próprio CDN ou utilizar pacotes NPM.

#### <a name="host-the-sdk-on-your-own-cdn"></a>Apresente o SDK no seu próprio CDN

 Em vez de os seus utilizadores finais descarregarem o SDK Application Insights a partir do CDN público, poderá hospedar o SDK Application Insights a partir do seu próprio ponto final CDN. Recomenda-se que utilize uma versão específica (ai.2.#.#.min.js) para que seja mais fácil identificar a versão que está a utilizar. Também atualize-o regularmente para a versão atual (ai.2.min.js) para que possa aproveitar quaisquer correções de bugs e novas funcionalidades que fiquem disponíveis.

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>Utilize pacotes NPM para incorporar o SDK Application Insight

Em vez de utilizar os pontos finais do Snippet e do CDN públicos, pode utilizar os [pacotes NPM](https://www.npmjs.com/package/applicationinsights) para incluir o SDK como parte dos seus próprios ficheiros JavaScript. O SDK tornar-se-á apenas mais um pacote dentro dos seus próprios scripts.

> [!NOTE]
> Recomenda-se que ao utilizar pacotes NPM também deve utilizar alguma forma de [bundler JavaScript](https://www.bing.com/search?q=javascript+bundler) para ajudar na divisão de códigos e na minificação.

Tal como acontece com o snippet, também é possível que os seus próprios scripts (com ou sem utilização dos pacotes SDK NPM) possam ser afetados pelos mesmos problemas de bloqueio listados aqui, pelo que, dependendo da sua aplicação, dos seus utilizadores e da sua estrutura, poderá considerar a implementação de algo semelhante à lógica no corte para detetar e reportar estes problemas.


## <a name="next-steps"></a>Passos seguintes 
- [Obtenha ajuda adicional ao arquivar um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [Monitorize o uso da página web](javascript.md)
