---
title: Retenção de dados e armazenamento no Azure Application Insights | Documentos da Microsoft
description: Declaração de política de retenção e privacidade
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: mbullwin
ms.openlocfilehash: 3c74d3a6c5b66053fb968ad52f72eca181799a3c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003592"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Recolha de dados, retenção e armazenamento no Application Insights

Quando instala [do Azure Application Insights] [ start] SDK na sua aplicação, que envia telemetria sobre a sua aplicação para a Cloud. Naturalmente, os desenvolvedores responsáveis querem saber exatamente quais dados são enviados, o que acontece com os dados e como eles podem manter o controle do mesmo. Em particular, foi possível enviar os dados confidenciais, onde é que está armazenado e quão segura é? 

Primeiro, a resposta curta:

* Os módulos de telemetria standard que executam "Out of box" são pouco provável que enviar dados confidenciais para o serviço. Diz respeito a telemetria de carga, as métricas de desempenho e utilização, relatórios de exceções e outros dados de diagnóstico. Os dados de utilizador principal visíveis nos relatórios de diagnóstico são URLs; mas a sua aplicação em qualquer caso não deve colocar dados confidenciais em texto sem formatação numa URL.
* Pode escrever código que envia telemetria personalizada adicional para ajudá-lo com o diagnóstico e utilização de monitorização. (Essa extensibilidade é um ótimo recurso do Application Insights.) Seria possível, por engano, escrever esse código para que ele inclua pessoais e outros dados confidenciais. Se a sua aplicação funciona com esses dados, deve aplicar processos uma revisão completa para todo o código que escreve.
* Ao desenvolver e testar a sua aplicação, é fácil inspecionar o que está a ser enviado pelo SDK. Os dados aparecem nas janelas de saída de depuração do IDE e navegador. 
* Os dados são mantidos numa [Microsoft Azure](https://azure.com) servidores nos EUA ou na Europa. (Mas a aplicação pode ser executado em qualquer lugar.) O Azure tem [segurança forte processa e cumpre um vasto leque de normas de conformidade](https://azure.microsoft.com/support/trust-center/). Apenas o e sua equipe designado tem acesso aos seus dados. À equipe da Microsoft pode ter restringido a acesso a ele apenas em circunstâncias limitadas específicas com seu conhecimento. São encriptado em trânsito, embora não estivesse nos servidores.

O restante deste artigo aborda-se mais totalmente a essas respostas. Foi concebido para ser independentes, para que ele pode mostrar aos colegas que não fazem parte da sua equipa de imediato.

## <a name="what-is-application-insights"></a>O que é o Application Insights?
[O Azure Application Insights] [ start] é um serviço fornecido pela Microsoft que o ajuda a melhorar o desempenho e a usabilidade da sua aplicação em direto. Monitoriza a aplicação sempre que ele é executado, durante o teste e depois de ter publicado ou implementado. O Application Insights cria gráficos e tabelas que mostram-lhe, por exemplo, que alturas do dia, obtém a maioria dos usuários, como capacidade de resposta a aplicação está e bem servida por todos os serviços externos que depende. Se existirem panes, falhas ou problemas de desempenho, pode pesquisar através dos dados de telemetria em detalhes para diagnosticar a causa. E o serviço irá enviar-lhe e-mails se há alguma alteração na disponibilidade e desempenho da sua aplicação.

Para obter essa funcionalidade, instale o SDK do Application Insights na sua aplicação, o que torna-se parte de seu código. Quando a aplicação é executada, o SDK monitoriza a sua operação e envia a telemetria para o serviço Application Insights. Este é um serviço em nuvem alojado pela [Microsoft Azure](https://azure.com). (Mas o Application Insights funciona para quaisquer aplicações, não apenas aqueles que estão alojados no Azure.)

O serviço Application Insights armazena e analisa a telemetria. Para ver a análise ou pesquisar a telemetria armazenada, inicie sessão na sua conta do Azure e abrir o recurso do Application Insights para a sua aplicação. Também pode partilhar o acesso aos dados com outros membros da sua equipa ou com os subscritores do Azure especificados.

Pode ter dados exportados do serviço do Application Insights, por exemplo, para uma base de dados ou ferramentas externas. Cada ferramenta é fornecer uma chave especial que obtém do serviço. A chave pode ser revogada se necessário. 

SDKs do Application Insights estão disponíveis para uma variedade de tipos de aplicações: web services hospedados em seus próprios servidores de Java EE ou ASP.NET, ou no Azure; Web clientes – ou seja, o código em execução numa página da web; aplicações de ambiente de trabalho e serviços; aplicações de dispositivos, como o Windows Phone, iOS e Android. Todos eles enviam telemetria para o mesmo serviço.

## <a name="what-data-does-it-collect"></a>Os dados que-recolhe?
### <a name="how-is-the-data-is-collected"></a>É como os dados é recolhidos?
Existem três origens de dados:

* O SDK, que é integrar com a sua aplicação seja [no desenvolvimento](../../azure-monitor/app/asp-net.md) ou [em tempo de execução](../../azure-monitor/app/monitor-performance-live-website-now.md). Existem SDKs diferentes para diferentes tipos de aplicativos. Há também uma [SDK para páginas da web](../../azure-monitor/app/javascript.md), que é carregada no navegador do usuário final, juntamente com a página.
  
  * Cada SDK tem diversas [módulos](../../azure-monitor/app/configuration-with-applicationinsights-config.md), que usam diferentes técnicas para recolher diferentes tipos de telemetria.
  * Se instalar o SDK no desenvolvimento, pode utilizar a sua API para enviar sua própria telemetria, além dos módulos padrão. Este telemetria personalizada pode incluir quaisquer dados que deseja enviar.
* Em alguns servidores web, também existem agentes que são executados em conjunto com a aplicação e enviam telemetria sobre CPU, memória e ocupação da rede. Por exemplo, VMs do Azure, anfitriões do Docker, e [servidores de Java EE](../../azure-monitor/app/java-agent.md) pode ter esses agentes.
* [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) processos são executados pela Microsoft que enviam pedidos à sua aplicação web em intervalos regulares. Os resultados são enviados para o serviço do Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Quais são os tipos de dados são recolhidos?
As categorias principais são:

* [Telemetria do servidor da Web](../../azure-monitor/app/asp-net.md) -pedidos de HTTP.  URI, tempo decorrido para processar o pedido, o código de resposta, o endereço IP do cliente. Id de sessão.
* [Páginas da Web](../../azure-monitor/app/javascript.md) -contagens de página, de utilizadores e de sessão. Tempos de carregamento de página. Exceções. Chamadas AJAX.
* Desempenho contadores - memória, CPU, e/s, ocupação da rede.
* Cliente e servidor contexto - SO, região, tipo de dispositivo, browser, resolução do ecrã.
* [Exceções](../../azure-monitor/app/asp-net-exceptions.md) e as falhas - **despejos de pilha**, criar id, tipo de CPU. 
* [Dependências](../../azure-monitor/app/asp-net-dependencies.md) -chamadas para serviços externos, como o REST, SQL, AJAX. Cadeia de ligação ou URI, duração, sucesso, comando.
* [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) -duração do teste e etapas, as respostas.
* [Registos de rastreio](../../azure-monitor/app/asp-net-trace-logs.md) e [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md) - **nada código no seu registos ou telemetria**.

[Mais detalhes](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Como posso verificar o que está a ser recolhido?
Se estiver a desenvolver a aplicação com o Visual Studio, execute a aplicação no modo de depuração (F5). A telemetria é apresentada na janela de saída. A partir daí, pode copiá-lo e formatá-lo como JSON para inspeção fácil. 

![](./media/data-retention-privacy/06-vs.png)

Também existe uma vista mais legível na janela de diagnóstico.

Para páginas da web, abra a janela de depuração do seu browser.

![Premir a tecla F12 e abra o separador rede.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Pode escrever código para filtrar a telemetria antes do envio?
Isso seria possível com a criação de um [Plug-in de processador de telemetria](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Quanto tempo os dados encontram-se?
Pontos de dados não processados (ou seja, os itens que pode consultar no Analytics e inspecionar na pesquisa) são mantidos durante 90 dias. Se precisar de manter os dados de mais do que isso, pode usar [exportação contínua](../../azure-monitor/app/export-telemetry.md) copiá-lo para uma conta de armazenamento.

Dados agregados (ou seja, contagens, médias e outros dados de estatísticos que vê no Explorador de métricas) são mantidos de acordo com um intervalo de agregação de 1 minuto durante 90 dias.

[Instantâneos de depuração](../../azure-monitor/app/snapshot-debugger.md) são armazenados durante sete dias. Esta política de retenção está definida numa base por aplicação. Se precisar de aumentar este valor, pode pedir um aumento ao abrir um incidente de suporte no portal do Azure.

## <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?
Os dados são visíveis para si e, se tiver uma conta de organização, os membros da equipe. 

Pode ser exportado por e os membros da Equipe e pode ser copiado para outras localizações e passado para outras pessoas.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>O que faz a Microsoft com as informações a minha aplicação envia para o Application Insights?
A Microsoft utiliza os dados apenas para fornecer o serviço para.

## <a name="where-is-the-data-held"></a>Onde são mantidos os dados?
* Nos EUA, Europa ou Sudeste asiático. Pode selecionar a localização quando cria um novo recurso do Application Insights. 

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Significa que meu aplicativo tem a serem alojadas na EUA, Europa ou Sudeste asiático?
* Não. Seu aplicativo pode executar em qualquer lugar e em seu próprio anfitriões no local ou na cloud.

## <a name="how-secure-is-my-data"></a>Quão seguros são meus dados?
O Application Insights é um serviço do Azure. As políticas de segurança descritas a [white paper de segurança do Azure, privacidade e conformidade](https://go.microsoft.com/fwlink/?linkid=392408).

Os dados são armazenados em servidores do Microsoft Azure. Para as contas no Portal do Azure, as restrições de conta estão descritas no [documento de segurança do Azure, privacidade e conformidade](https://go.microsoft.com/fwlink/?linkid=392408).

O acesso aos seus dados pela equipa da Microsoft é restrito. Podemos aceder aos seus dados apenas com a sua autorização e se é necessário suportar a utilização do Application Insights. 

Dados em forma agregada em todas as aplicações de todos os nossos clientes (por exemplo, as taxas de dados e tamanho médio de rastreios) são utilizados para melhorar o Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Telemetria de outra pessoa poderia interferir com os meus dados do Application Insights?
Eles poderiam enviar telemetria adicional à sua conta utilizando a chave de instrumentação, que pode ser encontrada no código de suas páginas da web. Com dados suficientes adicionais, as métricas não corretamente representaria desempenho e a utilização da sua aplicação.

Se compartilhar código com outros projetos, não se esqueça de remover a sua chave de instrumentação.

## <a name="is-the-data-encrypted"></a>Os dados são encriptados?
Todos os dados são encriptados em descanso e como ele se move entre dados centros.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Os dados são encriptados em trânsito do meu aplicativo para servidores do Application Insights?
Sim, vamos utilizar https para enviar dados para o portal de quase todos os SDKs, incluindo servidores web, dispositivos e páginas da web HTTPS. A única exceção é os dados enviados a partir de páginas da web HTTP simples.

## <a name="does-the-sdk-create-temporary-local-storage"></a>O SDK cria um armazenamento temporário local?

Sim, determinados canais de telemetria irá manter os dados localmente se não for possível aceder um ponto de extremidade. Consulte abaixo para ver quais estruturas e canais de telemetria são afetados.


Canais de telemetria que utilizam o armazenamento local criar arquivos temporários nos diretórios TEMP ou APPDATA que são restritos para a conta específica a execução da sua aplicação. Isto pode acontecer quando um ponto final foi temporariamente indisponível ou a atingir o limite de limitação. Assim que este problema for resolvido, o canal de telemetria retomará o envio de todos os dados novos e persistentes.


Estes dados persistentes são **não encriptados** e recomenda-se vivamente reestruturar sua política de recolha de dados para desativar a recolha de dados privadas. (Consulte [como exportar e eliminar dados privados](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data) para obter mais informações.)


Se um cliente precisa de configurar este diretório com os requisitos de segurança específicos podem ser configurada por framework. Certifique-se de que o processo de execução da sua aplicação tem acesso de escrita para este diretório, mas também fazer-se de que este diretório é protegido para evitar a telemetria que está a ser lida por utilizadores não desejados.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` é utilizado para manter dados. Esta localização não é configurável do diretório de configuração e as permissões para aceder esta pasta estão limitadas ao utilizador específico com as credenciais necessárias. (Consulte [implementação](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72) aqui.)

###  <a name="net"></a>.Net

Por predefinição `ServerTelemetryChannel` utiliza a pasta de dados de uma aplicação local do utilizador atual `%localAppData%\Microsoft\ApplicationInsights` ou a pasta temporária `%TMP%`. (Consulte [implementação](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) aqui.)


Através do ficheiro de configuração:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Por meio do código:

- Remover ServerTelemetryChannel do ficheiro de configuração
- Adicione este trecho de código à sua configuração:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Por predefinição `ServerTelemetryChannel` utiliza a pasta de dados de uma aplicação local do utilizador atual `%localAppData%\Microsoft\ApplicationInsights` ou a pasta temporária `%TMP%`. (Consulte [implementação](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) aqui.) Num ambiente do Linux, armazenamento local será desativado, a menos que uma pasta de armazenamento é especificada.

O fragmento de código seguinte mostra como definir `ServerTelemetryChannel.StorageFolder` no `ConfigureServices()`  método de sua `Startup.cs` classe:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Consulte [configuração personalizada de AspNetCore](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) para obter mais informações. )

### <a name="nodejs"></a>Node.js

Por predefinição `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` é utilizado para manter dados. Permissões para aceder esta pasta estão limitadas ao utilizador atual e os administradores. (Consulte [implementação](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) aqui.)

O prefixo da pasta `appInsights-node` pode ser substituído ao alterar o valor de tempo de execução da variável estática `Sender.TEMPDIR_PREFIX` encontrados na [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).



## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Como posso enviar dados para o Application Insights com o TLS 1.2?

Para garantir a segurança dos dados em trânsito para os pontos de extremidade do Application Insights, recomendamos os clientes para configurar a sua aplicação para utilizar, pelo menos, Transport Layer Security (TLS) 1.2. As versões mais antigas do TLS/Secure Sockets Layer (SSL) foram encontradas vulneráveis e enquanto trabalham ainda atualmente para permitir a compatibilidade com versões anteriores, estão **não recomendada**, e o setor que está a mudar rapidamente para abandonar o suporte para esses protocolos mais antigos. 

O [PCI Security Standards Council](https://www.pcisecuritystandards.org/) definiu um [dentro do prazo de 30 de Junho de 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) para desativar as versões mais antigas do TLS/SSL e a atualização para proteger mais os protocolos. Assim que o Azure ignora o suporte legacy, se seu aplicativo/clientes não conseguirem comunicar através de, pelo menos, TLS 1.2 não seria capaz de enviar dados para o Application Insights. A abordagem de testar e validar o suporte TLS do seu aplicativo irá variar consoante a sistema operacional/plataforma, bem como o idioma/framework que utiliza o seu aplicativo.

Não é recomendável definir o seu aplicativo para utilizar apenas o TLS 1.2, a menos que explicitamente absolutamente necessário, como isso pode danificar o recursos de segurança de nível de plataforma que permitem-lhe detetar e tirar partido das mais recentes protocolos mais seguros à medida que ficam automaticamente disponível como TLS 1.3. Recomendamos a realização de uma auditoria completa de código de seu aplicativo para verificar a existência de codificar de versões específicas do TLS/SSL.

### <a name="platformlanguage-specific-guidance"></a>Orientações específicas de plataforma/linguagem

|Plataforma/linguagem | Suporte | Mais Informações |
| --- | --- | --- |
| Serviços de Aplicações do Azure  | Suportado, pode ser necessária configuração. | Suporte foi anunciado em Abril de 2018. Leia o anúncio para [detalhes de configuração](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Aplicações de funções do Azure | Suportado, pode ser necessária configuração. | Suporte foi anunciado em Abril de 2018. Leia o anúncio para [detalhes de configuração](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Configuração suportada, varia por versão. | Para informações detalhadas de configuração para o .NET 4.7 e versões anteriores, consulte a [estas instruções](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitor de Estado | Configuração suportada, necessária | Monitor de estado depende [configuração do SO](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [configuração do .NET](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) ao suporte de TLS 1.2.
|Node.js |  Suportado em v10.5.0, pode ser necessária configuração. | Utilize o [documentação oficial do node. js TLS/SSL](https://nodejs.org/api/tls.html) para qualquer configuração específica do aplicativo. |
|Java | Suportado, foi adicionado suporte JDK para TLS 1.2 no [JDK 6 atualização 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) e [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 usa [TLS 1.2 por predefinição](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Distribuições do Linux tendem a depender [OpenSSL](https://www.openssl.org) para suporte de TLS 1.2.  | Verifique os [registo de alterações de OpenSSL](https://www.openssl.org/news/changelog.html) para confirmar a sua versão do OpenSSL que é suportado.|
| Windows 8.0 10 | Suportado e ativado por predefinição. | Para confirmar que ainda está a utilizar o [predefinições](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2016 de 2012 | Suportado e ativado por predefinição. | Para confirmar que ainda está a utilizar o [predefinições](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 e Windows Server 2008 R2 SP1 | Suportado, mas não ativado por predefinição. | Consulte a [definições de registo de Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) página para obter detalhes sobre como ativar.  |
| Windows Server 2008 SP2 | Suporte para TLS 1.2 requer uma atualização. | Ver [atualização para adicionar suporte para TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) no Windows Server 2008 SP2. |
|Windows Vista | Não suportado. | N/A

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Verifique qual versão do OpenSSL que sua distribuição de Linux está a funcionar

Para verificar que versão do OpenSSL que instalou, abra o terminal e execute:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Executar um teste de transação de TLS 1.2 no Linux

Para executar um teste básico preliminar para ver se o seu sistema Linux possam comunicar através de TLS 1.2. Abra o terminal e execute:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Dados pessoais armazenados no Application Insights

Nosso [artigo de dados pessoais do Application Insights](../../azure-monitor/platform/personal-data-mgmt.md) aborda este problema aprofundado.

#### <a name="can-my-users-turn-off-application-insights"></a>Podem os meus utilizadores desativar Application Insights?
Não diretamente. Não fornecemos um comutador que os utilizadores podem operar para desativar o Application Insights.

No entanto, pode implementar desse recurso em seu aplicativo. Todos os SDKs incluem uma definição de API que desativa a coleção de telemetria. 

## <a name="data-sent-by-application-insights"></a>Dados enviados pelo Application Insights
Os SDKs variam entre plataformas e existem vários componentes que podem instalar. (Consulte a [Application Insights - descrição geral][start].) Cada componente envia dados diferentes.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Classes de dados enviados em cenários diferentes

| A ação | Classes de dados recolhidos (consulte a tabela seguinte) |
| --- | --- |
| [Adicionar o Application Insights SDK para um projeto de web do .NET][greenbrown] |ServerContext<br/>Inferido<br/>Contadores de desempenho<br/>Pedidos<br/>**Exceções**<br/>Sessão<br/>utilizadores |
| [Instale o Monitor de estado no IIS][redfield] |Dependências<br/>ServerContext<br/>Inferido<br/>Contadores de desempenho |
| [Adicionar o Application Insights SDK para uma aplicação web Java][java] |ServerContext<br/>Inferido<br/>Pedir<br/>Sessão<br/>utilizadores |
| [Adicionar o SDK de JavaScript para a página da web][client] |ClientContext <br/>Inferido<br/>Página<br/>ClientPerf<br/>Ajax |
| [Definir propriedades predefinidas][apiproperties] |**Propriedades** em todos os eventos padrão e personalizados |
| [Chamar TrackMetric][api] |Valores numéricos<br/>**Propriedades** |
| [Controle de chamada *][api] |Nome do evento<br/>**Propriedades** |
| [Chamar TrackException][api] |**Exceções**<br/>Despejo da pilha<br/>**Propriedades** |
| SDK não é possível recolher dados. Por exemplo: <br/> -Não é possível aceder aos contadores de desempenho<br/> -exceção na inicializador de telemetria |Diagnóstico SDK |

Para [SDKs para outras plataformas][platforms], veja os seus documentos.

#### <a name="the-classes-of-collected-data"></a>As classes de dados recolhidos

| Classe de dados recolhidos | Inclui (não uma lista exaustiva) |
| --- | --- |
| **Propriedades** |**Todos os dados - determinados pelo seu código** |
| DeviceContext |ID, IP, região, modelo do dispositivo, rede, o tipo de rede, nome OEM, resolução de tela, instância de função, o nome de função, o tipo de dispositivo |
| ClientContext |Sistema operativo, idioma, Localidade, resolução de janela de rede |
| Sessão |Id de sessão |
| ServerContext |Nome da máquina, a Localidade do sistema operacional, dispositivo, sessão de utilizador, contexto de usuário, operação |
| Inferido |localização geográfica do endereço de IP, timestamp, sistema operativo, browser |
| Métricas |Nome da métrica e valor |
| Eventos |Nome do evento e o valor |
| PageViews |Nome de URL e página ou o nome do ecrã |
| Desempenho do cliente |Nome da URL/página, o tempo de carregamento do browser |
| Ajax |Chamadas HTTP da página da web para servidor |
| Pedidos |URL, duração, o código de resposta |
| Dependências |Tipo (SQL, HTTP,...), a cadeia de ligação ou URI, sincronização/async, duração, sucesso, instrução de SQL (com o Monitor de estado) |
| **Exceções** |Tipo, **mensagem**, pilhas de chamadas, id de thread number, ficheiros e de linha de origem |
| Falhas de sistema |Id do processo, a identificação do processo pai, a id do thread de falhas; patch do aplicativo, a id, a compilação;  tipo de exceção, endereço, motivo; símbolos oculto e registros, binários endereços inicial e final, nome binário e caminho, tipo de cpu |
| Rastreio |**Mensagem** e nível de gravidade |
| Contadores de desempenho |Tempo do processador, memória disponível, taxa de pedidos, taxa de exceções, bytes privados do processo, taxa de e/s, duração do pedido, comprimento da fila de pedidos |
| Disponibilidade |Código de resposta de teste Web, a duração de cada etapa do teste, nome do teste, timestamp, sucesso, tempo de resposta, localização de teste |
| Diagnóstico SDK |Mensagem de rastreio ou exceção |

Pode [optar por desativar alguns dos dados ao editar applicationinsights. config][config]

> [!NOTE]
> Cliente IP é utilizado para inferir a localização geográfica, mas por padrão os dados IP já não são armazenados e todos os zeros são escritos para o campo associado. Para saber mais sobre o processamento de dados pessoais, recomendamos isso [artigo](../../azure-monitor/platform/personal-data-mgmt.md#application-data). Se tiver de armazenar o endereço IP pode fazê-lo com uma [inicializador de telemetria](./../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="credits"></a>Ficha técnica
Este produto inclui dados de GeoLite2 criados pelo MaxMind, disponível a partir [ https://www.maxmind.com ](https://www.maxmind.com).



<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiproperties]: ../../azure-monitor/app/api-custom-events-metrics.md#properties
[client]: ../../azure-monitor/app/javascript.md
[config]: ../../azure-monitor/app/configuration-with-applicationinsights-config.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[java]: ../../azure-monitor/app/java-get-started.md
[platforms]: ../../azure-monitor/app/platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md

