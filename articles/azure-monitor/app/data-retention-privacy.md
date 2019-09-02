---
title: Retenção e armazenamento de dados no Aplicativo Azure insights | Microsoft Docs
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
ms.date: 08/22/2019
ms.author: mbullwin
ms.openlocfilehash: df441a55ef4a9a40fe4defcabca5f667eeddbf29
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207290"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Recolha de dados, retenção e armazenamento no Application Insights

Quando você instala o SDK do insights [aplicativo Azure][start] em seu aplicativo, ele envia a telemetria sobre seu aplicativo para a nuvem. Naturalmente, os desenvolvedores responsáveis desejam saber exatamente quais dados são enviados, o que acontece com os dados e como eles podem manter o controle dele. Em particular, os dados confidenciais poderiam ser enviados, onde são armazenados e qual é a segurança? 

Primeiro, a resposta curta:

* Os módulos de telemetria padrão que executam "prontos" são improvável de enviar dados confidenciais ao serviço. A telemetria se preocupa com métricas de carga, desempenho e uso, relatórios de exceção e outros dados de diagnóstico. Os dados principais do usuário visíveis nos relatórios de diagnóstico são URLs; Mas seu aplicativo não deve, em nenhum caso, colocar dados confidenciais em texto sem formatação em uma URL.
* Você pode escrever um código que envie telemetria personalizada adicional para ajudá-lo com o diagnóstico e o uso do monitoramento. (Essa extensibilidade é um grande recurso do Application Insights.) Seria possível, por engano, escrever esse código para que ele inclua dados confidenciais e pessoais. Se seu aplicativo funciona com esses dados, você deve aplicar um processo de revisão completo a todo o código que você escreve.
* Ao desenvolver e testar seu aplicativo, é fácil inspecionar o que está sendo enviado pelo SDK. Os dados são exibidos nas janelas de saída de depuração do IDE e do navegador. 
* Os dados são mantidos em servidores [Microsoft Azure](https://azure.com) nos EUA ou na Europa. (Mas seu aplicativo pode ser executado em qualquer lugar). O Azure tem [processos fortes de segurança e atende a uma ampla gama de padrões de conformidade](https://azure.microsoft.com/support/trust-center/). Somente você e sua equipe designada têm acesso aos seus dados. A equipe da Microsoft pode ter acesso restrito a ela somente sob circunstâncias limitadas específicas com seu conhecimento. Ele é criptografado em trânsito e em repouso.

O restante deste artigo elabora mais detalhes sobre essas respostas. Ele foi projetado para ser independente, para que você possa mostrá-lo para colegas que não fazem parte de sua equipe imediata.

## <a name="what-is-application-insights"></a>O que é o Application Insights?
O [aplicativo Azure][start] insights é um serviço fornecido pela Microsoft que ajuda a melhorar o desempenho e a usabilidade do seu aplicativo em tempo real. Ele monitora o aplicativo todo o tempo em que está sendo executado, tanto durante o teste quanto quando você o publicou ou implantou. Application Insights cria gráficos e tabelas que mostram, por exemplo, quais horas do dia você obtém a maioria dos usuários, a resposta do aplicativo e o quão bem ele é atendido por quaisquer serviços externos dos quais depende. Se houver falhas, falhas ou problemas de desempenho, você poderá pesquisar os dados de telemetria em detalhes para diagnosticar a causa. E o serviço enviará emails se houver alterações na disponibilidade e no desempenho do seu aplicativo.

Para obter essa funcionalidade, você instala um SDK Application Insights em seu aplicativo, que se torna parte de seu código. Quando seu aplicativo está em execução, o SDK monitora sua operação e envia a telemetria para o serviço de Application Insights. Esse é um serviço de nuvem hospedado pelo [Microsoft Azure](https://azure.com). (Mas Application Insights funciona para qualquer aplicativo, não apenas aqueles que estão hospedados no Azure.)

O serviço de Application Insights armazena e analisa a telemetria. Para ver a análise ou pesquisar por meio da telemetria armazenada, você entra em sua conta do Azure e abre o recurso de Application Insights para seu aplicativo. Você também pode compartilhar o acesso aos dados com outros membros da sua equipe ou com os assinantes do Azure especificados.

Você pode ter dados exportados do serviço de Application Insights, por exemplo, para um banco de dados ou para ferramentas externas. Você fornece a cada ferramenta uma chave especial que obtém do serviço. A chave pode ser revogada, se necessário. 

Os SDKs do Application Insights estão disponíveis para uma variedade de tipos de aplicativos: serviços Web hospedados em seus próprios servidores Java EE ou ASP.NET ou no Azure; clientes Web – ou seja, o código em execução em uma página da Web; aplicativos e serviços de desktop; aplicativos de dispositivo, como Windows Phone, iOS e Android. Todos eles enviam telemetria para o mesmo serviço.

## <a name="what-data-does-it-collect"></a>Quais dados são coletados?
### <a name="how-is-the-data-is-collected"></a>Como os dados são coletados?
Há três fontes de dados:

* O SDK, que você integra com seu aplicativo [no desenvolvimento](../../azure-monitor/app/asp-net.md) ou em [tempo de execução](../../azure-monitor/app/monitor-performance-live-website-now.md). Há diferentes SDKs para diferentes tipos de aplicativo. Há também um [SDK para páginas da Web](../../azure-monitor/app/javascript.md), que é carregado no navegador do usuário final junto com a página.
  
  * Cada SDK tem vários [módulos](../../azure-monitor/app/configuration-with-applicationinsights-config.md), que usam técnicas diferentes para coletar diferentes tipos de telemetria.
  * Se você instalar o SDK no desenvolvimento, poderá usar sua API para enviar sua própria telemetria, além dos módulos padrão. Essa telemetria personalizada pode incluir todos os dados que você deseja enviar.
* Em alguns servidores Web, também há agentes que são executados juntamente com o aplicativo e enviam telemetria sobre CPU, memória e ocupação de rede. Por exemplo, as VMs do Azure, hosts do Docker e [servidores Java EE](../../azure-monitor/app/java-agent.md) podem ter esses agentes.
* Os [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) são processos executados pela Microsoft que enviam solicitações para seu aplicativo Web em intervalos regulares. Os resultados são enviados para o serviço de Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Que tipos de dados são coletados?
As principais categorias são:

* [Telemetria do servidor Web](../../azure-monitor/app/asp-net.md) -solicitações HTTP.  URI, tempo levado para processar a solicitação, o código de resposta, o endereço IP do cliente. ID da sessão.
* [Páginas da Web](../../azure-monitor/app/javascript.md) – contagens de página, usuário e sessão. Tempos de carregamento de página. Exceções. Chamadas AJAX.
* Contadores de desempenho-memória, CPU, e/s, ocupação de rede.
* Contexto de cliente e servidor-sistema operacional, localidade, tipo de dispositivo, navegador, resolução de tela.
* [Exceções](../../azure-monitor/app/asp-net-exceptions.md) e falhas- **despejos de pilha**, ID de compilação, tipo de CPU. 
* [Dependências](../../azure-monitor/app/asp-net-dependencies.md) – chamadas para serviços externos, como REST, SQL, Ajax. URI ou cadeia de conexão, duração, êxito, comando.
* [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) -duração de teste e etapas, respostas.
* [Logs de rastreamento](../../azure-monitor/app/asp-net-trace-logs.md) e telemetria [](../../azure-monitor/app/api-custom-events-metrics.md) - personalizada**qualquer código que você codifique em seus logs ou telemetria**.

[Mais detalhes](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Como posso verificar o que está sendo coletado?
Se você estiver desenvolvendo o aplicativo usando o Visual Studio, execute o aplicativo no modo de depuração (F5). A telemetria aparece na janela saída. A partir daí, você pode copiá-lo e formatá-lo como JSON para uma inspeção fácil. 

![](./media/data-retention-privacy/06-vs.png)

Também há uma exibição mais legível na janela de diagnóstico.

Para páginas da Web, abra a janela de depuração do navegador.

![Pressione F12 e abra a guia rede.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Posso escrever código para filtrar a telemetria antes que ela seja enviada?
Isso seria possível escrevendo um [plug-in de processador](../../azure-monitor/app/api-filtering-sampling.md)de telemetria.

## <a name="how-long-is-the-data-kept"></a>Por quanto tempo os dados são mantidos?
Pontos de dados brutos (ou seja, itens que você pode consultar na análise e inspecionar na pesquisa) são mantidos por até 730 dias. Você pode [selecionar uma duração de retenção](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period) de 30, 60, 90, 120, 180, 270, 365, 550 ou 730 dias. Se você precisar manter os dados com mais de 730 dias, poderá usar a [exportação contínua](../../azure-monitor/app/export-telemetry.md) para copiá-los para uma conta de armazenamento durante a ingestão de dados. 

Os dados mantidos por mais de 90 dias incorrerão em encargos adicionais. Saiba mais sobre preços de Application Insights na [página de preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

Os dados agregados (ou seja, contagens, médias e outros dados estatísticos que você vê no Gerenciador de métricas) são mantidos em um detalhamento de 1 minuto por 90 dias.

Os instantâneos de [depuração](../../azure-monitor/app/snapshot-debugger.md) são armazenados por quinze dias. Esta política de retenção está definida numa base por aplicação. Se precisar de aumentar este valor, pode pedir um aumento ao abrir um incidente de suporte no portal do Azure.

## <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?
Os dados ficam visíveis para você e, se você tiver uma conta da organização, seus membros da equipe. 

Ele pode ser exportado por você e pelos membros da equipe e pode ser copiado para outros locais e transmitido a outras pessoas.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>O que a Microsoft faz com as informações que meu aplicativo envia para Application Insights?
A Microsoft usa os dados somente para fornecer o serviço a você.

## <a name="where-is-the-data-held"></a>Onde os dados são mantidos?
* Você pode selecionar o local ao criar um novo recurso de Application Insights. Saiba mais sobre a disponibilidade de Application Insights por região [aqui](https://azure.microsoft.com/global-infrastructure/services/?products=all).

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Isso significa que meu aplicativo deve ser hospedado nos EUA, Europa ou sudeste asiático?
* Não. Seu aplicativo pode ser executado em qualquer lugar, seja em seus próprios hosts locais ou na nuvem.

## <a name="how-secure-is-my-data"></a>Qual a segurança dos meus dados?
Application Insights é um serviço do Azure. As políticas de segurança são descritas na [White Paper de segurança, privacidade e conformidade do Azure](https://go.microsoft.com/fwlink/?linkid=392408).

Os dados são armazenados em servidores Microsoft Azure. Para contas no portal do Azure, as restrições de conta são descritas no [documento segurança, privacidade e conformidade do Azure](https://go.microsoft.com/fwlink/?linkid=392408).

O acesso aos seus dados pela equipe da Microsoft é restrito. Acessamos seus dados somente com sua permissão e, se necessário, para dar suporte ao uso de Application Insights. 

Os dados em agregação em todos os aplicativos de nossos clientes (como taxas de dados e tamanho médio de rastreamentos) são usados para melhorar o Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Outra pessoa poderia interferir na telemetria com meus dados de Application Insights?
Eles podem enviar telemetria adicional à sua conta usando a chave de instrumentação, que pode ser encontrada no código de suas páginas da Web. Com dados adicionais suficientes, suas métricas não representariam corretamente o desempenho e o uso do seu aplicativo.

Se você compartilhar código com outros projetos, lembre-se de remover sua chave de instrumentação.

## <a name="is-the-data-encrypted"></a>Os dados são criptografados?
Todos os dados são criptografados em repouso e à medida que se movem entre data centers.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Os dados são criptografados em trânsito de meu aplicativo para servidores Application Insights?
Sim, usamos HTTPS para enviar dados ao portal de praticamente todos os SDKs, incluindo servidores Web, dispositivos e páginas da Web HTTPS. A única exceção são os dados enviados de páginas da Web HTTP simples.

## <a name="does-the-sdk-create-temporary-local-storage"></a>O SDK cria um armazenamento local temporário?

Sim, determinados canais de telemetria manterão os dados localmente se um ponto de extremidade não puder ser alcançado. Leia abaixo para ver quais estruturas e canais de telemetria são afetados.

Os canais de telemetria que utilizam o armazenamento local criam arquivos temporários nos diretórios TEMP ou APPDATA que são restritos à conta específica que executa o aplicativo. Isso pode acontecer quando um ponto de extremidade estava temporariamente indisponível ou quando você atinge o limite de limitação. Depois que esse problema for resolvido, o canal de telemetria continuará enviando todos os dados novos e persistidos.

Os dados persistentes não são criptografados localmente. Se essa for uma preocupação, revise os dados e restrinja a coleta de dados privados. (Consulte [como exportar e excluir dados privados](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data) para obter mais informações.)

Se um cliente precisar configurar esse diretório com requisitos de segurança específicos, ele poderá ser configurado por estrutura. Certifique-se de que o processo que está executando seu aplicativo tenha acesso de gravação a esse diretório, mas também Verifique se esse diretório está protegido para evitar que a telemetria seja lida por usuários indesejados.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp`é usado para manter dados. Esse local não pode ser configurado no diretório de configuração e as permissões para acessar essa pasta são restritas ao usuário específico com as credenciais necessárias. (Consulte a [implementação](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72) aqui.)

###  <a name="net"></a>.Net

Por padrão `ServerTelemetryChannel` , o usa a pasta `%localAppData%\Microsoft\ApplicationInsights` de dados do aplicativo local do usuário `%TMP%`atual ou a pasta temporária. (Consulte a [implementação](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) aqui.)


Por meio do arquivo de configuração:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Por meio de código:

- Remover ServerTelemetryChannel do arquivo de configuração
- Adicione este trecho à sua configuração:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Por padrão `ServerTelemetryChannel` , o usa a pasta `%localAppData%\Microsoft\ApplicationInsights` de dados do aplicativo local do usuário `%TMP%`atual ou a pasta temporária. (Consulte a [implementação](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) aqui.) Em um ambiente Linux, o armazenamento local será desabilitado, a menos que uma pasta de armazenamento seja especificada.

O trecho de código a seguir mostra como `ServerTelemetryChannel.StorageFolder` definir `ConfigureServices()` no método da sua `Startup.cs` classe:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Consulte [configuração personalizada do AspNetCore](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) para obter mais informações. )

### <a name="nodejs"></a>Node.js

Por padrão `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` , é usado para manter dados. As permissões para acessar essa pasta são restritas ao usuário atual e aos administradores. (Consulte a [implementação](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) aqui.)

O prefixo `appInsights-node` da pasta pode ser substituído alterando o valor de tempo de execução `Sender.TEMPDIR_PREFIX` da variável estática encontrada em [Sender. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).



## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Como fazer enviar dados para Application Insights usando o TLS 1,2?

Para garantir a segurança dos dados em trânsito para os pontos de extremidade de Application Insights, recomendamos que os clientes configurem seus aplicativos para usarem pelo menos o protocolo TLS (segurança de camada de transporte) 1,2. As versões mais antigas do TLS/Secure Sockets Layer (SSL) foram encontradas vulneráveis e enquanto trabalham ainda atualmente para permitir a compatibilidade com versões anteriores, estão **não recomendada**, e o setor que está a mudar rapidamente para abandonar o suporte para esses protocolos mais antigos. 

O [PCI Security Standards Council](https://www.pcisecuritystandards.org/) definiu um [dentro do prazo de 30 de Junho de 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) para desativar as versões mais antigas do TLS/SSL e a atualização para proteger mais os protocolos. Quando o Azure descartar o suporte herdado, se seu aplicativo/clientes não puderem se comunicar por pelo menos o TLS 1,2, você não poderá enviar dados para Application Insights. A abordagem que você tomará para testar e validar o suporte a TLS do aplicativo varia dependendo do sistema operacional/plataforma, bem como da linguagem/estrutura usada pelo aplicativo.

Não recomendamos definir explicitamente seu aplicativo para usar apenas o TLS 1,2, a menos que seja absolutamente necessário, pois isso pode interromper os recursos de segurança no nível da plataforma que permitem detectar e aproveitar automaticamente os protocolos mais seguros à medida que se tornam disponível como o TLS 1,3. É recomendável executar uma auditoria completa do código do aplicativo para verificar o codificação de versões de TLS/SSL específicas.

### <a name="platformlanguage-specific-guidance"></a>Diretrizes específicas de plataforma/linguagem

|Plataforma/linguagem | Suporte | Mais Informações |
| --- | --- | --- |
| Serviços Aplicacionais do Azure  | Com suporte, a configuração pode ser necessária. | O suporte foi anunciado em abril de 2018. Leia o comunicado para obter [detalhes de configuração](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Aplicativos de funções do Azure | Com suporte, a configuração pode ser necessária. | O suporte foi anunciado em abril de 2018. Leia o comunicado para obter [detalhes de configuração](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Com suporte, a configuração varia de acordo com a versão. | Para obter informações de configuração detalhadas para o .NET 4,7 e versões anteriores, consulte [estas instruções](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitor de Estado | Com suporte, configuração necessária | Status monitor conta com a[configuração do .net](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) de [configuração](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + do so para dar suporte a TLS 1,2.
|Node.js |  Com suporte, em v 10.5.0, a configuração pode ser necessária. | Use a [documentação oficial do TLS/SSL do node. js](https://nodejs.org/api/tls.html) para qualquer configuração específica do aplicativo. |
|Java | Com suporte, o suporte do JDK para TLS 1,2 foi adicionado no [JDK 6 atualização 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) e [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | O JDK 8 usa o [TLS 1,2 por padrão](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Distribuições do Linux tendem a depender [OpenSSL](https://www.openssl.org) para suporte de TLS 1.2.  | Verifique os [registo de alterações de OpenSSL](https://www.openssl.org/news/changelog.html) para confirmar a sua versão do OpenSSL que é suportado.|
| Windows 8.0 10 | Suportado e ativado por predefinição. | Para confirmar que ainda está a utilizar o [predefinições](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2016 de 2012 | Suportado e ativado por predefinição. | Para confirmar que ainda está a utilizar o [predefinições](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 e Windows Server 2008 R2 SP1 | Suportado, mas não ativado por predefinição. | Consulte a [definições de registo de Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) página para obter detalhes sobre como ativar.  |
| Windows Server 2008 SP2 | Suporte para TLS 1.2 requer uma atualização. | Ver [atualização para adicionar suporte para TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) no Windows Server 2008 SP2. |
|Windows Vista | Não suportado. | N/A

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Verifique qual versão do OpenSSL sua distribuição do Linux está em execução

Para verificar qual versão do OpenSSL você instalou, abra o terminal e execute:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Executar uma transação Test TLS 1,2 no Linux

Para executar um teste preliminar básico para ver se o seu sistema Linux pode se comunicar por meio do TLS 1,2. Abra o terminal e execute:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Dados pessoais armazenados no Application Insights

Nosso [artigo Application insights dados pessoais](../../azure-monitor/platform/personal-data-mgmt.md) aborda esse problema em detalhes.

#### <a name="can-my-users-turn-off-application-insights"></a>Meus usuários podem desligar Application Insights?
Não diretamente. Não fornecemos um comutador que os usuários podem operar para desativar Application Insights.

No entanto, você pode implementar um recurso desse tipo em seu aplicativo. Todos os SDKs incluem uma configuração de API que desativa a coleta de telemetria. 

## <a name="data-sent-by-application-insights"></a>Dados enviados por Application Insights
Os SDKs variam entre as plataformas, e há vários componentes que você pode instalar. (Consulte [Application insights-visão geral][start].) Cada componente envia dados diferentes.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Classes de dados enviadas em cenários diferentes

| Sua ação | Classes de dados coletadas (consulte a próxima tabela) |
| --- | --- |
| [Adicionar Application Insights SDK a um projeto Web .NET][greenbrown] |ServerContext<br/>Inferido<br/>Contadores de desempenho<br/>Pedidos<br/>**Exceções**<br/>Sessão<br/>Utilizadores |
| [Instalar o Status Monitor no IIS][redfield] |Dependências<br/>ServerContext<br/>Inferido<br/>Contadores de desempenho |
| [Adicionar Application Insights SDK a um aplicativo Web Java][java] |ServerContext<br/>Inferido<br/>Pedir<br/>Sessão<br/>Utilizadores |
| [Adicionar SDK do JavaScript à página da Web][client] |ClientContext <br/>Inferido<br/>Página<br/>ClientPerf<br/>Ajax |
| [Definir propriedades padrão][apiproperties] |**Propriedades** em todos os eventos padrão e personalizados |
| [Chamar TrackMetric][api] |Valores numéricos<br/>**Properties** |
| [Controle de chamada *][api] |Nome do evento<br/>**Properties** |
| [Chamar Trackexception][api] |**Exceções**<br/>Despejo de pilha<br/>**Properties** |
| O SDK não pode coletar dados. Por exemplo: <br/> -Não é possível acessar contadores de desempenho<br/> -exceção no inicializador de telemetria |Diagnóstico do SDK |

Para [SDKs para outras plataformas][platforms], consulte seus documentos.

#### <a name="the-classes-of-collected-data"></a>As classes de dados coletados

| Classe de dados coletados | Inclui (não é uma lista completa) |
| --- | --- |
| **Properties** |**Qualquer dado-determinado pelo seu código** |
| DeviceContext |ID, IP, localidade, modelo de dispositivo, rede, tipo de rede, nome do OEM, resolução da tela, instância de função, nome da função, tipo de dispositivo |
| ClientContext |Sistema operacional, localidade, idioma, rede, resolução de janela |
| Sessão |ID da sessão |
| ServerContext |Nome do computador, localidade, sistema operacional, dispositivo, sessão de usuário, contexto de usuário, operação |
| Inferido |localização geográfica de endereço IP, carimbo de data/hora, sistema operacional, navegador |
| Métricas |Nome e valor da métrica |
| Events |Nome e valor do evento |
| PageViews |URL e nome da página ou nome da tela |
| Desempenho do cliente |URL/nome da página, tempo de carregamento do navegador |
| Ajax |Chamadas HTTP de página da Web para o servidor |
| Pedidos |URL, duração, código de resposta |
| Dependências |Tipo (SQL, HTTP,...), Cadeia de conexão ou URI, sincronização/Async, duração, êxito, instrução SQL (com Status Monitor) |
| **Exceções** |Tipo, **mensagem**, pilhas de chamadas, arquivo de origem e número da linha, ID do thread |
| Falhas |ID do processo, ID do processo pai, ID do thread de falha; patch do aplicativo, ID, Build;  tipo de exceção, endereço, motivo; símbolos e registros ofuscados, endereços de início e término binários, nome e caminho binários, tipo de CPU |
| Rastreio |Nível de **mensagem** e severidade |
| Contadores de desempenho |Tempo do processador, memória disponível, taxa de solicitação, taxa de exceção, bytes particulares do processo, taxa de e/s, duração da solicitação, comprimento da fila de solicitações |
| Disponibilidade |Código de resposta de teste na Web, duração de cada etapa de teste, nome do teste, carimbo de data/hora, êxito, tempo de resposta, local do teste |
| Diagnóstico do SDK |Mensagem de rastreamento ou exceção |

Você pode [desativar alguns dos dados editando ApplicationInsights. config][config]

> [!NOTE]
> O IP do cliente é usado para inferir a localização geográfica, mas os dados IP padrão não são mais armazenados e todos os zeros são gravados no campo associado. Para entender mais sobre a manipulação de dados pessoais, recomendamos este [artigo](../../azure-monitor/platform/personal-data-mgmt.md#application-data). Se você precisar armazenar dados de endereço IP, nosso [artigo de coleção de endereços IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection) o guiará pelas suas opções.

## <a name="credits"></a>Ficha técnica
Este produto inclui dados de GeoLite2 criados pelo MaxMind, disponíveis [https://www.maxmind.com](https://www.maxmind.com)no.



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
