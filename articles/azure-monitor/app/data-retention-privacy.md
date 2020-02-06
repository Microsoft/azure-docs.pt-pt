---
title: Retenção e armazenamento de dados no Aplicativo Azure insights | Microsoft Docs
description: Declaração de política de retenção e privacidade
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/29/2019
ms.openlocfilehash: ba8a76cd4d3804bcb062ae0554e3fe7002804ed2
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031685"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Coleta de dados, retenção e armazenamento no Application Insights

Quando instala o [Azure Application Insights][start] SDK na sua aplicação, envia telemetria sobre a sua aplicação para a Cloud. Naturalmente, os desenvolvedores responsáveis desejam saber exatamente quais dados são enviados, o que acontece com os dados e como eles podem manter o controle dele. Em particular, os dados confidenciais poderiam ser enviados, onde são armazenados e qual é a segurança? 

Primeiro, a resposta curta:

* Os módulos de telemetria padrão que executam "prontos" são improvável de enviar dados confidenciais ao serviço. A telemetria se preocupa com métricas de carga, desempenho e uso, relatórios de exceção e outros dados de diagnóstico. Os dados principais do usuário visíveis nos relatórios de diagnóstico são URLs; Mas seu aplicativo não deve, em nenhum caso, colocar dados confidenciais em texto sem formatação em uma URL.
* Você pode escrever um código que envie telemetria personalizada adicional para ajudá-lo com o diagnóstico e o uso do monitoramento. (Essa extensibilidade é um grande recurso do Application Insights.) Seria possível, por engano, escrever esse código para que ele inclua dados confidenciais e pessoais. Se seu aplicativo funciona com esses dados, você deve aplicar um processo de revisão completo a todo o código que você escreve.
* Ao desenvolver e testar seu aplicativo, é fácil inspecionar o que está sendo enviado pelo SDK. Os dados são exibidos nas janelas de saída de depuração do IDE e do navegador. 
* Os dados são guardados em servidores [do Microsoft Azure](https://azure.com) nos EUA ou na Europa. (Mas a sua aplicação pode correr em qualquer lugar.) O Azure tem processos de [segurança fortes e cumpre um vasto leque de normas de conformidade.](https://azure.microsoft.com/support/trust-center/) Somente você e sua equipe designada têm acesso aos seus dados. A equipe da Microsoft pode ter acesso restrito a ela somente sob circunstâncias limitadas específicas com seu conhecimento. Ele é criptografado em trânsito e em repouso.
*   Examine os dados coletados, pois isso pode incluir dados permitidos em algumas circunstâncias, mas não em outros.  Um bom exemplo disso é o nome do dispositivo. O nome do dispositivo de um servidor não tem impacto de privacidade e é útil, mas um nome de dispositivo de um telefone ou laptop pode ter um impacto de privacidade e ser menos útil. Um SDK desenvolvido principalmente para servidores de destino, coletaria o nome do dispositivo por padrão, e isso pode precisar ser substituído em eventos normais e exceções.

O restante deste artigo elabora mais detalhes sobre essas respostas. Ele foi projetado para ser independente, para que você possa mostrá-lo para colegas que não fazem parte de sua equipe imediata.

## <a name="what-is-application-insights"></a>O que é o Application Insights?
[O Azure Application Insights][start] é um serviço fornecido pela Microsoft que o ajuda a melhorar o desempenho e a usabilidade da sua aplicação ao vivo. Ele monitora o aplicativo todo o tempo em que está sendo executado, tanto durante o teste quanto quando você o publicou ou implantou. Application Insights cria gráficos e tabelas que mostram, por exemplo, quais horas do dia você obtém a maioria dos usuários, a resposta do aplicativo e o quão bem ele é atendido por quaisquer serviços externos dos quais depende. Se houver falhas, falhas ou problemas de desempenho, você poderá pesquisar os dados de telemetria em detalhes para diagnosticar a causa. E o serviço enviará emails se houver alterações na disponibilidade e no desempenho do seu aplicativo.

Para obter essa funcionalidade, você instala um SDK Application Insights em seu aplicativo, que se torna parte de seu código. Quando seu aplicativo está em execução, o SDK monitora sua operação e envia a telemetria para o serviço de Application Insights. Este é um serviço na nuvem hospedado pelo [Microsoft Azure.](https://azure.com) (Mas Application Insights funciona para qualquer aplicativo, não apenas aplicativos hospedados no Azure.)

O serviço de Application Insights armazena e analisa a telemetria. Para ver a análise ou pesquisar por meio da telemetria armazenada, você entra em sua conta do Azure e abre o recurso de Application Insights para seu aplicativo. Você também pode compartilhar o acesso aos dados com outros membros da sua equipe ou com os assinantes do Azure especificados.

Você pode ter dados exportados do serviço de Application Insights, por exemplo, para um banco de dados ou para ferramentas externas. Você fornece a cada ferramenta uma chave especial que obtém do serviço. A chave pode ser revogada, se necessário. 

Os SDKs do Application Insights estão disponíveis para uma variedade de tipos de aplicativos: serviços Web hospedados em seus próprios servidores Java EE ou ASP.NET ou no Azure; clientes Web – ou seja, o código em execução em uma página da Web; aplicativos e serviços de desktop; aplicativos de dispositivo, como Windows Phone, iOS e Android. Todos eles enviam telemetria para o mesmo serviço.

## <a name="what-data-does-it-collect"></a>Quais dados são coletados?
Há três fontes de dados:

* O SDK, que integra com a sua app em desenvolvimento ou [em tempo de execução.](../../azure-monitor/app/monitor-performance-live-website-now.md) [](../../azure-monitor/app/asp-net.md) Há diferentes SDKs para diferentes tipos de aplicativo. Há também um [SDK para páginas web](../../azure-monitor/app/javascript.md), que se carrega no navegador do utilizador final juntamente com a página.
  
  * Cada SDK tem uma série de [módulos,](../../azure-monitor/app/configuration-with-applicationinsights-config.md)que utilizam diferentes técnicas para recolher diferentes tipos de telemetria.
  * Se você instalar o SDK no desenvolvimento, poderá usar sua API para enviar sua própria telemetria, além dos módulos padrão. Essa telemetria personalizada pode incluir todos os dados que você deseja enviar.
* Em alguns servidores Web, também há agentes que são executados juntamente com o aplicativo e enviam telemetria sobre CPU, memória e ocupação de rede. Por exemplo, os VMs Azure, os anfitriões do Docker e os [servidores Java EE](../../azure-monitor/app/java-agent.md) podem ter esses agentes.
* [Os testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) são processos executados pela Microsoft que enviam pedidos para a sua aplicação web em intervalos regulares. Os resultados são enviados para o serviço de Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Que tipos de dados são coletados?
As principais categorias são:

* [Telemetria](../../azure-monitor/app/asp-net.md) do servidor web - PEDIDOS HTTP.  URI, tempo levado para processar a solicitação, o código de resposta, o endereço IP do cliente. `Session id`.
* [Páginas web](../../azure-monitor/app/javascript.md) - Página, utilizador e contagem de sessões. Tempos de carregamento de página. Exceções. Chamadas AJAX.
* Contadores de desempenho-memória, CPU, e/s, ocupação de rede.
* Contexto de cliente e servidor-sistema operacional, localidade, tipo de dispositivo, navegador, resolução de tela.
* [Exceções](../../azure-monitor/app/asp-net-exceptions.md) e acidentes - **depósitos de pilhas,** `build id`, tipo CPU. 
* [Dependências](../../azure-monitor/app/asp-net-dependencies.md) - chamadas para serviços externos como REST, SQL, AJAX. URI ou cadeia de conexão, duração, êxito, comando.
* [Testes](../../azure-monitor/app/monitor-web-app-availability.md) de disponibilidade - duração do teste e etapas, respostas.
* [Trace logs](../../azure-monitor/app/asp-net-trace-logs.md) e [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md) - **qualquer coisa que codifique nos seus registos ou telemetria**.

[Mais detalhes.](#data-sent-by-application-insights)

## <a name="how-can-i-verify-whats-being-collected"></a>Como posso verificar o que está sendo coletado?
Se você estiver desenvolvendo o aplicativo usando o Visual Studio, execute o aplicativo no modo de depuração (F5). A telemetria aparece na janela saída. A partir daí, você pode copiá-lo e formatá-lo como JSON para uma inspeção fácil. 

![](./media/data-retention-privacy/06-vs.png)

Também há uma exibição mais legível na janela de diagnóstico.

Para páginas da Web, abra a janela de depuração do navegador.

![Pressione F12 e abra a guia rede.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Posso escrever código para filtrar a telemetria antes que ela seja enviada?
Isto seria possível escrevendo um plugin de processador de [telemetria.](../../azure-monitor/app/api-filtering-sampling.md)

## <a name="how-long-is-the-data-kept"></a>Por quanto tempo os dados são mantidos?
Pontos de dados brutos (ou seja, itens que você pode consultar na análise e inspecionar na pesquisa) são mantidos por até 730 dias. Pode [selecionar uma duração](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period) de retenção de 30, 60, 90, 120, 180, 270, 365, 550 ou 730 dias. Se precisar de manter os dados por mais de 730 dias, pode utilizar a [Exportação Contínua](../../azure-monitor/app/export-telemetry.md) para copiá-la para uma conta de armazenamento durante a ingestão de dados. 

Os dados mantidos por mais de 90 dias incorrerão em encargos adicionais. Saiba mais sobre os preços dos Insights de Aplicação na página de preços do [Monitor Do Azure](https://azure.microsoft.com/pricing/details/monitor/).

Os dados agregados (ou seja, contagens, médias e outros dados estatísticos que você vê no Gerenciador de métricas) são mantidos em um detalhamento de 1 minuto por 90 dias.

[As fotos de depuração](../../azure-monitor/app/snapshot-debugger.md) são armazenadas durante 15 dias. Esta política de retenção está definida numa base por aplicação. Se precisar de aumentar este valor, pode pedir um aumento ao abrir um incidente de suporte no portal do Azure.

## <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?
Os dados ficam visíveis para você e, se você tiver uma conta da organização, seus membros da equipe. 

Ele pode ser exportado por você e pelos membros da equipe e pode ser copiado para outros locais e transmitido a outras pessoas.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>O que a Microsoft faz com as informações que meu aplicativo envia para Application Insights?
A Microsoft usa os dados somente para fornecer o serviço a você.

## <a name="where-is-the-data-held"></a>Onde os dados são mantidos?
* Você pode selecionar o local ao criar um novo recurso de Application Insights. Saiba mais sobre a disponibilidade de Informações de Aplicação por região [aqui](https://azure.microsoft.com/global-infrastructure/services/?products=all).

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Isso significa que meu aplicativo deve ser hospedado nos EUA, na Europa ou no sudeste asiático?
* Não. Seu aplicativo pode ser executado em qualquer lugar, seja em seus próprios hosts locais ou na nuvem.

## <a name="how-secure-is-my-data"></a>Qual a segurança dos meus dados?
Application Insights é um serviço do Azure. As políticas de segurança são descritas no Livro Branco de [Segurança, Privacidade e Conformidade do Azure.](https://go.microsoft.com/fwlink/?linkid=392408)

Os dados são armazenados em servidores Microsoft Azure. Para contas no portal Azure, as restrições de conta são descritas no [documento de Segurança, Privacidade e Conformidade do Azure.](https://go.microsoft.com/fwlink/?linkid=392408)

O acesso aos seus dados pela equipe da Microsoft é restrito. Acessamos seus dados somente com sua permissão e, se necessário, para dar suporte ao uso de Application Insights. 

Os dados em agregação em todos os aplicativos de nossos clientes (como taxas de dados e tamanho médio de rastreamentos) são usados para melhorar o Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Outra pessoa poderia interferir na telemetria com meus dados de Application Insights?
Eles podem enviar telemetria adicional à sua conta usando a chave de instrumentação, que pode ser encontrada no código de suas páginas da Web. Com dados adicionais suficientes, suas métricas não representariam corretamente o desempenho e o uso do seu aplicativo.

Se você compartilhar código com outros projetos, lembre-se de remover sua chave de instrumentação.

## <a name="is-the-data-encrypted"></a>Os dados são criptografados?
Todos os dados são criptografados em repouso e à medida que se movem entre data centers.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Os dados são criptografados em trânsito de meu aplicativo para servidores Application Insights?
Sim, usamos HTTPS para enviar dados ao portal de praticamente todos os SDKs, incluindo servidores Web, dispositivos e páginas da Web HTTPS. 

## <a name="does-the-sdk-create-temporary-local-storage"></a>O SDK cria um armazenamento local temporário?

Sim, determinados canais de telemetria manterão os dados localmente se um ponto de extremidade não puder ser alcançado. Leia abaixo para ver quais estruturas e canais de telemetria são afetados.

Os canais de telemetria que utilizam o armazenamento local criam arquivos temporários nos diretórios TEMP ou APPDATA, que são restritos à conta específica que executa o aplicativo. Isso pode acontecer quando um ponto de extremidade estava temporariamente indisponível ou quando você atinge o limite de limitação. Depois que esse problema for resolvido, o canal de telemetria continuará enviando todos os dados novos e persistidos.

Os dados persistentes não são criptografados localmente. Se essa for uma preocupação, revise os dados e restrinja a coleta de dados privados. (Para mais informações, consulte [Como exportar e eliminar dados privados](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data).)

Se um cliente precisar configurar esse diretório com requisitos de segurança específicos, ele poderá ser configurado por estrutura. Certifique-se de que o processo que está executando seu aplicativo tenha acesso de gravação a esse diretório, mas também Verifique se esse diretório está protegido para evitar que a telemetria seja lida por usuários indesejados.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` é utilizado para dados persistentes. Esse local não pode ser configurado no diretório de configuração e as permissões para acessar essa pasta são restritas ao usuário específico com as credenciais necessárias. (Para mais informações, consulte [a implementação](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72).)

###  <a name="net"></a>.Net

Por predefinição, `ServerTelemetryChannel` utiliza a pasta de dados de aplicações local do utilizador atual `%localAppData%\Microsoft\ApplicationInsights` ou pasta temporária `%TMP%`. (Ver [implementação](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) aqui.)


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

Por predefinição, `ServerTelemetryChannel` utiliza a pasta de dados de aplicações local do utilizador atual `%localAppData%\Microsoft\ApplicationInsights` ou pasta temporária `%TMP%`. (Ver [implementação](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) aqui.) Num ambiente Linux, o armazenamento local será desativado a menos que seja especificada uma pasta de armazenamento.

O seguinte código de corte mostra como configurar `ServerTelemetryChannel.StorageFolder` no método `ConfigureServices()` da sua classe `Startup.cs`:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Para mais informações, consulte [a Configuração Personalizada AspNetCore](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).)

### <a name="nodejs"></a>Node.js

Por padrão, `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` é utilizado para dados persistentes. As permissões para acessar essa pasta são restritas ao usuário atual e aos administradores. (Ver [implementação](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) aqui.)

O prefixo da pasta `appInsights-node` pode ser ultrapassado alterando o valor de tempo de execução da variável estática `Sender.TEMPDIR_PREFIX` encontrada em [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).

### <a name="opencensus-python"></a>OpenCensus Python

Por padrão, o OpenCensus Python SDK utiliza a atual pasta de utilizador `%username%/.opencensus/.azure/`. As permissões para acessar essa pasta são restritas ao usuário atual e aos administradores. (Ver [implementação](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py) aqui.) A pasta com os seus dados persistidos será nomeada em homenagem ao ficheiro Python que gerou a telemetria.

Pode alterar a localização do seu ficheiro de armazenamento, passando o parâmetro `storage_path` no construtor do exportador que está a utilizar.

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Como fazer enviar dados para Application Insights usando o TLS 1,2?

Para garantir a segurança dos dados em trânsito para os pontos de extremidade de Application Insights, recomendamos que os clientes configurem seus aplicativos para usarem pelo menos o protocolo TLS (segurança de camada de transporte) 1,2. As versões mais antigas da Camada de Tomadas Seguras (SSL) foram consideradas vulneráveis e, embora ainda trabalhem para permitir a retrocompatibilidade, não são **recomendadas**, e a indústria está rapidamente a mover-se para abandonar o suporte a estes protocolos mais antigos. 

O Conselho de Normas de Segurança do [PCI](https://www.pcisecuritystandards.org/) fixou um [prazo de 30 de junho de 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) para desativar versões mais antigas de TLS/SSL e atualizar para protocolos mais seguros. Quando o Azure descartar o suporte herdado, se seu aplicativo/clientes não puderem se comunicar por pelo menos o TLS 1,2, você não poderá enviar dados para Application Insights. A abordagem que você tomará para testar e validar o suporte a TLS do aplicativo varia dependendo do sistema operacional/plataforma, bem como da linguagem/estrutura usada pelo aplicativo.

Não recomendamos definir explicitamente seu aplicativo para usar apenas o TLS 1,2, a menos que seja necessário, pois isso pode interromper os recursos de segurança no nível da plataforma que permitem detectar e aproveitar automaticamente os protocolos mais seguros à medida que eles se tornam disponíveis, como TLS 1,3. É recomendável executar uma auditoria completa do código do aplicativo para verificar o codificação de versões de TLS/SSL específicas.

### <a name="platformlanguage-specific-guidance"></a>Diretrizes específicas de plataforma/linguagem

|Plataforma/linguagem | Suporte | Mais Informações |
| --- | --- | --- |
| Serviços de Aplicações do Azure  | Com suporte, a configuração pode ser necessária. | O suporte foi anunciado em abril de 2018. Leia o anúncio para obter detalhes de [configuração](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Aplicativos de função Azure | Com suporte, a configuração pode ser necessária. | O suporte foi anunciado em abril de 2018. Leia o anúncio para obter detalhes de [configuração](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Com suporte, a configuração varia de acordo com a versão. | Para obter informações detalhadas sobre a configuração para .NET 4.7 e versões anteriores, consulte [estas instruções](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitor de Estado | Com suporte, configuração necessária | O Monitor de Estado baseia-se na [configuração do OS](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [.NET configuração](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) para suportar TLS 1.2.
|Node.js |  Com suporte, em v 10.5.0, a configuração pode ser necessária. | Utilize a documentação oficial do [Nó.js TLS/SSL](https://nodejs.org/api/tls.html) para qualquer configuração específica da aplicação. |
|Java | Suportado, o suporte JDK para TLS 1.2 foi adicionado na [atualização JDK 6 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) e [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 utiliza [TLS 1.2 por defeito](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | As distribuições linux tendem a depender do [OpenSSL](https://www.openssl.org) para suporte TLS 1.2.  | Verifique o [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) para confirmar que a sua versão do OpenSSL está suportada.|
| Windows 8.0 10 | Suportado e ativado por predefinição. | Para confirmar que ainda está a utilizar as [definições predefinidas](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2016 de 2012 | Suportado e ativado por predefinição. | Para confirmar que ainda está a utilizar as [definições predefinidas](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 e Windows Server 2008 R2 SP1 | Suportado, mas não ativado por predefinição. | Consulte a página de definições de registo de Segurança da Camada de [Transporte (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) para obter mais detalhes sobre como ativar.  |
| Windows Server 2008 SP2 | Suporte para TLS 1.2 requer uma atualização. | Consulte [a Atualização para adicionar suporte para TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) no Windows Server 2008 SP2. |
|Windows Vista | Não suportado. | N/D

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Verifique qual versão do OpenSSL sua distribuição do Linux está em execução

Para verificar qual versão do OpenSSL você instalou, abra o terminal e execute:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Executar uma transação Test TLS 1,2 no Linux

Para executar um teste preliminar para ver se o seu sistema Linux pode se comunicar por meio do TLS 1,2., abra o terminal e execute:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Dados pessoais armazenados no Application Insights

O nosso artigo de [dados pessoais da Application Insights](../../azure-monitor/platform/personal-data-mgmt.md) discute esta questão em profundidade.

#### <a name="can-my-users-turn-off-application-insights"></a>Meus usuários podem desligar Application Insights?
Não diretamente. Não fornecemos um comutador que os usuários podem operar para desativar Application Insights.

No entanto, você pode implementar um recurso desse tipo em seu aplicativo. Todos os SDKs incluem uma configuração de API que desativa a coleta de telemetria. 

## <a name="data-sent-by-application-insights"></a>Dados enviados por Application Insights
Os SDKs variam entre as plataformas, e há vários componentes que você pode instalar. (Consulte os Insights de [Aplicação - visão geral][start].) Cada componente envia dados diferentes.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Classes de dados enviadas em cenários diferentes

| Sua ação | Classes de dados coletadas (consulte a próxima tabela) |
| --- | --- |
| [Adicione Insights de Aplicação SDK a um projeto web .NET][greenbrown] |ServerContext<br/>Inferido<br/>Contadores de desempenho<br/>Pedidos<br/>**Exceções**<br/>Sessão<br/>utilizadores |
| [Instalar monitor de estado no IIS][redfield] |Dependências<br/>ServerContext<br/>Inferido<br/>Contadores de desempenho |
| [Adicione Insights de aplicação SDK a uma aplicação web java][java] |ServerContext<br/>Inferido<br/>Pedir<br/>Sessão<br/>utilizadores |
| [Adicione JavaScript SDK à página web][client] |ClientContext <br/>Inferido<br/>Página<br/>ClientPerf<br/>Ajax |
| [Definir propriedades por defeito][apiproperties] |**Propriedades** em todos os eventos standard e personalizados |
| [Chamada TrackMetric][api] |Valores numéricos<br/>**Propriedades** |
| [Chamada Track*][api] |Nome do evento<br/>**Propriedades** |
| [Call TrackException][api] |**Exceções**<br/>Despejo de pilha<br/>**Propriedades** |
| O SDK não pode coletar dados. Por exemplo: <br/> -Não é possível acessar contadores de desempenho<br/> -exceção no inicializador de telemetria |Diagnóstico do SDK |

Para [SDKs para outras plataformas,][platforms]consulte os seus documentos.

#### <a name="the-classes-of-collected-data"></a>As classes de dados coletados

| Classe de dados coletados | Inclui (não é uma lista completa) |
| --- | --- |
| **Propriedades** |**Quaisquer dados - determinados pelo seu código** |
| DeviceContext |`Id`, IP, Locale, Modelo de Dispositivo, rede, tipo de rede, nome OEM, resolução de ecrã, Role Instance, Role Name, Device Type |
| ClientContext |Sistema operacional, localidade, idioma, rede, resolução de janela |
| Sessão |`session id` |
| ServerContext |Nome do computador, localidade, sistema operacional, dispositivo, sessão de usuário, contexto de usuário, operação |
| Inferido |localização geográfica de endereço IP, carimbo de data/hora, sistema operacional, navegador |
| Métricas |Nome e valor da métrica |
| Eventos |Nome e valor do evento |
| PageViews |URL e nome da página ou nome da tela |
| Desempenho do cliente |URL/nome da página, tempo de carregamento do navegador |
| Ajax |Chamadas HTTP de página da Web para o servidor |
| Pedidos |URL, duração, código de resposta |
| Dependências |Tipo (SQL, HTTP,...), Cadeia de conexão, URI, sincronização/Async, duração, êxito, instrução SQL (com Status Monitor) |
| **Exceções** |Tipo, **mensagem,** pilhas de chamadas, ficheiro fonte, número de linha, `thread id` |
| Falhas |`Process id`, `parent process id`, `crash thread id`; patch de aplicação, `id`, construir;  tipo de exceção, endereço, razão; símbolos e registos obfuscados, endereços binários de início e fim, nome binário e caminho, tipo cpu |
| Rastreio |**Mensagem** e nível de gravidade |
| Contadores de desempenho |Tempo do processador, memória disponível, taxa de solicitação, taxa de exceção, bytes particulares do processo, taxa de e/s, duração da solicitação, comprimento da fila de solicitações |
| Disponibilidade |Código de resposta de teste na Web, duração de cada etapa de teste, nome do teste, carimbo de data/hora, êxito, tempo de resposta, local do teste |
| Diagnóstico do SDK |Mensagem de rastreamento ou exceção |

Pode [desligar alguns dos dados editando ApplicationInsights.config][config]

> [!NOTE]
> O IP do cliente é usado para inferir a localização geográfica, mas os dados IP padrão não são mais armazenados e todos os zeros são gravados no campo associado. Para saber mais sobre o tratamento de dados pessoais recomendamos este [artigo.](../../azure-monitor/platform/personal-data-mgmt.md#application-data) Se precisar de armazenar dados de endereçoIP, o nosso artigo de recolha de [endereços IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection) irá acompanhá-lo através das suas opções.

## <a name="credits"></a>Créditos
Este produto inclui dados GeoLite2 criados pela MaxMind, disponíveis a partir de [https://www.maxmind.com](https://www.maxmind.com).



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
