---
title: Retenção e armazenamento de dados em Insights de Aplicação Azure [ Microsoft Docs
description: Declaração de política de retenção e privacidade
ms.topic: conceptual
ms.date: 09/29/2019
ms.openlocfilehash: 30878eecf795c85713b9f09b8325b326416022b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79276000"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Recolha, retenção e armazenamento de dados em Insights de Aplicação

Quando instala o [Azure Application Insights][start] SDK na sua aplicação, envia telemetria sobre a sua aplicação para a Cloud. Naturalmente, os desenvolvedores responsáveis querem saber exatamente quais os dados enviados, o que acontece aos dados, e como podem manter o controlo dos mesmos. Em particular, poderiam ser enviados dados sensíveis, onde é armazenado e quão seguro é? 

Primeiro, a resposta curta:

* É pouco provável que os módulos de telemetria padrão que funcionam "fora da caixa" enviem dados sensíveis ao serviço. A telemetria diz respeito às métricas de carga, desempenho e utilização, relatórios de exceção e outros dados de diagnóstico. Os principais dados do utilizador visíveis nos relatórios de diagnóstico são URLs; mas a sua aplicação não deve, em caso algum, colocar dados sensíveis em texto simples num URL.
* Pode escrever código que envia telemetria personalizada adicional para ajudá-lo com diagnósticos e monitorização do uso. (Esta extensibility é uma grande característica de Application Insights.) Seria possível, por engano, escrever este código de modo a incluir dados pessoais e outros sensíveis. Se a sua aplicação funcionar com esses dados, deverá aplicar um processo de revisão exaustivo a todo o código que escrever.
* Ao desenvolver e testar a sua aplicação, é fácil inspecionar o que está a ser enviado pelo SDK. Os dados aparecem nas janelas de saída dedepuração do IDE e do navegador. 
* Os dados são guardados em servidores [do Microsoft Azure](https://azure.com) nos EUA ou na Europa. (Mas a sua aplicação pode correr em qualquer lugar.) O Azure tem processos de [segurança fortes e cumpre um vasto leque de normas de conformidade.](https://azure.microsoft.com/support/trust-center/) Só você e a sua equipa designada têm acesso aos seus dados. Os colaboradores da Microsoft só podem ter acesso restrito a ele em circunstâncias limitadas específicas com o seu conhecimento. Está encriptado em trânsito e em repouso.
*   Reveja os dados recolhidos, uma vez que estes podem incluir dados que são permitidos em algumas circunstâncias, mas não em outras.  Um bom exemplo disto é o Nome do Dispositivo. O nome do dispositivo de um servidor não tem impacto na privacidade e é útil, mas um nome de dispositivo de um telefone ou portátil pode ter um impacto de privacidade e ser menos útil. Um SDK desenvolvido principalmente para servidores-alvo, recolheria o nome do dispositivo por padrão, e isso pode ter de ser substituído tanto em eventos normais como em exceções.

O resto deste artigo elabora mais plenamente estas respostas. Foi concebido para ser autossuficiente, para que possa mostrá-lo a colegas que não fazem parte da sua equipa imediata.

## <a name="what-is-application-insights"></a>O que é o Application Insights?
[O Azure Application Insights][start] é um serviço fornecido pela Microsoft que o ajuda a melhorar o desempenho e a usabilidade da sua aplicação ao vivo. Monitoriza a sua aplicação durante todo o tempo em que está a funcionar, tanto durante os testes como depois de a publicar ou implementar. Application Insights cria gráficos e tabelas que mostram, por exemplo, em que horas do dia obtém a maioria dos utilizadores, quão responsiva é a app, e quão bem é servida por quaisquer serviços externos de que depende. Se houver falhas, falhas ou problemas de desempenho, pode pesquisar os dados da telemetria em detalhe para diagnosticar a causa. E o serviço irá enviar-lhe e-mails se houver alguma alteração na disponibilidade e desempenho da sua aplicação.

Para obter esta funcionalidade, instala um SDK de Insights de Aplicação na sua aplicação, que passa a fazer parte do seu código. Quando a sua aplicação está em execução, o SDK monitoriza o seu funcionamento e envia telemetria para o serviço Application Insights. Este é um serviço na nuvem hospedado pelo [Microsoft Azure.](https://azure.com) (Mas a Application Insights funciona para quaisquer aplicações, e não apenas aplicações que estejam alojadas no Azure.)

O serviço Application Insights armazena e analisa a telemetria. Para ver a análise ou pesquisar através da telemetria armazenada, insere-se na sua conta Azure e abra o recurso Application Insights para a sua aplicação. Também pode partilhar o acesso aos dados com outros membros da sua equipa, ou com assinantes Azure especificados.

Pode ter dados exportados do serviço Application Insights, por exemplo, para uma base de dados ou para ferramentas externas. Fornece a cada ferramenta uma chave especial que obtém do serviço. A chave pode ser revogada se necessário. 

Os SDKs de Insights de Aplicação estão disponíveis para uma série de tipos de aplicações: serviços web hospedados nos seus próprios servidores Java EE ou ASP.NET, ou em Azure; web clientes - isto é, o código que está a ser em execução numa página web; aplicativos e serviços de desktop; aplicações de dispositivos como Windows Phone, iOS e Android. Todos enviam telemetria para o mesmo serviço.

## <a name="what-data-does-it-collect"></a>Que dados recolhe?
Existem três fontes de dados:

* O SDK, que integra com a sua app em desenvolvimento ou [em tempo de execução.](../../azure-monitor/app/monitor-performance-live-website-now.md) [in development](../../azure-monitor/app/asp-net.md) Existem diferentes SDKs para diferentes tipos de aplicações. Há também um [SDK para páginas web](../../azure-monitor/app/javascript.md), que se carrega no navegador do utilizador final juntamente com a página.
  
  * Cada SDK tem uma série de [módulos,](../../azure-monitor/app/configuration-with-applicationinsights-config.md)que utilizam diferentes técnicas para recolher diferentes tipos de telemetria.
  * Se instalar o SDK em desenvolvimento, pode utilizar a sua API para enviar a sua própria telemetria, além dos módulos padrão. Esta telemetria personalizada pode incluir todos os dados que pretenda enviar.
* Em alguns servidores web, há também agentes que correm ao lado da app e enviam telemetria sobre CPU, memória e ocupação de rede. Por exemplo, os VMs Azure, os anfitriões do Docker e os [servidores Java EE](../../azure-monitor/app/java-agent.md) podem ter esses agentes.
* [Os testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) são processos executados pela Microsoft que enviam pedidos para a sua aplicação web em intervalos regulares. Os resultados são enviados para o serviço Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Que tipo de dados são recolhidos?
As principais categorias são:

* [Telemetria](../../azure-monitor/app/asp-net.md) do servidor web - PEDIDOS HTTP.  Uri, tempo para processar o pedido, código de resposta, endereço IP do cliente. `Session id`.
* [Páginas web](../../azure-monitor/app/javascript.md) - Página, utilizador e contagem de sessões. Tempos de carregamento de página. Exceções. O Ajax chama.
* Contadores de desempenho - Memória, CPU, IO, Ocupação da Rede.
* Contexto do cliente e servidor - OS, local, tipo de dispositivo, navegador, resolução de ecrã.
* [Exceções](../../azure-monitor/app/asp-net-exceptions.md) e acidentes - `build id` **depósitos de pilhas,** tipo CPU. 
* [Dependências](../../azure-monitor/app/asp-net-dependencies.md) - chamadas para serviços externos como REST, SQL, AJAX. URI ou corda de ligação, duração, sucesso, comando.
* [Testes](../../azure-monitor/app/monitor-web-app-availability.md) de disponibilidade - duração do teste e etapas, respostas.
* [Trace logs](../../azure-monitor/app/asp-net-trace-logs.md) e [telemetria](../../azure-monitor/app/api-custom-events-metrics.md) - personalizada**qualquer coisa que codificar nos seus registos ou telemetria**.

[Mais detalhes.](#data-sent-by-application-insights)

## <a name="how-can-i-verify-whats-being-collected"></a>Como posso verificar o que está a ser recolhido?
Se estiver a desenvolver a aplicação utilizando o Visual Studio, execute a aplicação em modo dedepuração (F5). A telemetria aparece na janela de saída. A partir daí, pode copiá-lo e fortá-lo como JSON para uma inspeção fácil. 

![](./media/data-retention-privacy/06-vs.png)

Há também uma vista mais legível na janela de Diagnósticos.

Para páginas web, abra a janela de depuração do seu navegador.

![Prima F12 e abra o separador Rede.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Posso escrever código para filtrar a telemetria antes de ser enviada?
Isto seria possível escrevendo um plugin de processador de [telemetria.](../../azure-monitor/app/api-filtering-sampling.md)

## <a name="how-long-is-the-data-kept"></a>Quanto tempo os dados são mantidos?
Os pontos de dados brutos (isto é, itens que pode consultar em Analytics e inspecionar em Pesquisa) são mantidos até 730 dias. Pode [selecionar uma duração](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period) de retenção de 30, 60, 90, 120, 180, 270, 365, 550 ou 730 dias. Se precisar de manter os dados por mais de 730 dias, pode utilizar a [Exportação Contínua](../../azure-monitor/app/export-telemetry.md) para copiá-la para uma conta de armazenamento durante a ingestão de dados. 

Os dados mantidos por mais de 90 dias incorrerão em encargos adicionais. Saiba mais sobre os preços dos Insights de Aplicação na página de preços do [Monitor Do Azure](https://azure.microsoft.com/pricing/details/monitor/).

Os dados agregados (isto é, conta, médias e outros dados estatísticos que se vê no Metric Explorer) são retidos a um grão de 1 minuto durante 90 dias.

[As fotos de depuração](../../azure-monitor/app/snapshot-debugger.md) são armazenadas durante 15 dias. Esta política de retenção é definida numa base por aplicação. Se precisar de aumentar este valor, pode solicitar um aumento abrindo um caso de suporte no portal Azure.

## <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?
Os dados são visíveis para si e, se tiver uma conta de organização, os membros da sua equipa. 

Pode ser exportado por si e pelos membros da sua equipa e pode ser copiado para outros locais e passado para outras pessoas.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>O que faz a Microsoft com a informação que a minha aplicação envia para o Application Insights?
A Microsoft utiliza os dados apenas para lhe prestar o serviço.

## <a name="where-is-the-data-held"></a>Onde estão os dados?
* Pode selecionar a localização quando criar um novo recurso Application Insights. Saiba mais sobre a disponibilidade de Informações de Aplicação por região [aqui](https://azure.microsoft.com/global-infrastructure/services/?products=all).

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Isso significa que a minha aplicação tem de ser hospedada nos EUA, na Europa ou no Sudeste Asiático?
* Não. A sua aplicação pode ser executada em qualquer lugar, seja nos seus próprios anfitriões no local ou na nuvem.

## <a name="how-secure-is-my-data"></a>Quão seguros são os meus dados?
Application Insights é um Serviço Azure. As políticas de segurança são descritas no Livro Branco de [Segurança, Privacidade e Conformidade do Azure.](https://go.microsoft.com/fwlink/?linkid=392408)

Os dados são armazenados nos servidores do Microsoft Azure. Para contas no portal Azure, as restrições de conta são descritas no [documento de Segurança, Privacidade e Conformidade do Azure.](https://go.microsoft.com/fwlink/?linkid=392408)

O acesso aos seus dados pelo pessoal da Microsoft é restrito. Acedemos aos seus dados apenas com a sua permissão e se for necessário apoiar a sua utilização de Insights de Aplicação. 

Os dados agregados em todas as aplicações dos nossos clientes (como taxas de dados e tamanho médio de vestígios) são utilizados para melhorar os Insights de Aplicação.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>A telemetria de outra pessoa pode interferir com os meus dados de Informação de Aplicação?
Podem enviar telemetria adicional para a sua conta utilizando a chave de instrumentação, que pode ser encontrada no código das suas páginas web. Com dados adicionais suficientes, as suas métricas não representam corretamente o desempenho e o uso da sua aplicação.

Se partilhar código com outros projetos, lembre-se de remover a sua chave de instrumentação.

## <a name="is-the-data-encrypted"></a>Os dados estão encriptados?
Todos os dados são encriptados em repouso e à medida que se movem entre centros de dados.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Os dados estão encriptados em trânsito desde a minha aplicação até servidores De Insights de Aplicação?
Sim, usamos https para enviar dados para o portal de quase todos os SDKs, incluindo servidores web, dispositivos e páginas web HTTPS. 

## <a name="does-the-sdk-create-temporary-local-storage"></a>O SDK cria armazenamento local temporário?

Sim, certos canais de telemetria persistirão os dados localmente se não for possível alcançar um ponto final. Por favor, reveja abaixo para ver quais os quadros e canais de telemetria afetados.

Os canais de telemetria que utilizam o armazenamento local criam ficheiros temporários nos diretórios TEMP ou APPDATA, que estão restritos à conta específica que executa a sua aplicação. Isto pode acontecer quando um ponto final estava temporariamente indisponível ou atingiste o limite de estrangulamento. Uma vez resolvido este problema, o canal de telemetria retomará o envio de todos os dados novos e persistidos.

Estes dados persistidos não são encriptados localmente. Se for uma preocupação, reveja os dados e restrinja a recolha de dados privados. (Para mais informações, consulte [Como exportar e eliminar dados privados](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data).)

Se um cliente precisar de configurar este diretório com requisitos de segurança específicos, pode ser configurado por enquadramento. Certifique-se de que o processo de execução da sua aplicação tem acesso a este diretório, mas também certifique-se de que este diretório está protegido para evitar que a telemetria seja lida por utilizadores não intencionais.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp`é utilizado para dados persistentes. Esta localização não é configurável a partir do diretório de config e as permissões para aceder a esta pasta são restritas ao utilizador específico com credenciais necessárias. (Para mais informações, consulte [a implementação](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72).)

###  <a name="net"></a>.Net

Por `ServerTelemetryChannel` predefinição, utiliza a pasta `%localAppData%\Microsoft\ApplicationInsights` de dados `%TMP%`de aplicação local do utilizador atual ou a pasta temporária . (Ver [implementação](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) aqui.)


Através de ficheiro de configuração:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Via código:

- Remover ServerTelemettryChannel do ficheiro de configuração
- Adicione este corte à sua configuração:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Por `ServerTelemetryChannel` predefinição, utiliza a pasta `%localAppData%\Microsoft\ApplicationInsights` de dados `%TMP%`de aplicação local do utilizador atual ou a pasta temporária . (Ver [implementação](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) aqui.) Num ambiente Linux, o armazenamento local será desativado a menos que seja especificada uma pasta de armazenamento.

O seguinte código de corte `ServerTelemetryChannel.StorageFolder` mostra `ConfigureServices()` como definir `Startup.cs` no método da sua classe:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Para mais informações, consulte [a Configuração Personalizada AspNetCore](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).)

### <a name="nodejs"></a>Node.js

Por `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` predefinição é utilizado para dados persistentes. As permissões para aceder a esta pasta estão restritas ao utilizador e administradores atuais. (Ver [implementação](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) aqui.)

O prefixo `appInsights-node` da pasta pode ser ultrapassado alterando o `Sender.TEMPDIR_PREFIX` valor de tempo de execução da variável estática encontrada em [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).

### <a name="javascript-browser"></a>JavaScript (navegador)

[HTML5 Armazenamento](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) de sessão é usado para persistir dados. São utilizados dois `AI_buffer` tampão separados: e `AI_sent_buffer`. A telemetria que é lotada e `AI_buffer`à espera de ser enviada é armazenada em . A telemetria que acabou `AI_sent_buffer` de ser enviada é colocada até que o servidor de ingestão responda que foi recebida com sucesso. Quando a telemetria é recebida com sucesso, é removida de todos os amortecedores. Em falhas transitórias (por exemplo, um utilizador perde a `AI_buffer` conectividade da rede), a telemetria permanece até ser recebida com sucesso ou o servidor de ingestão responde que a telemetria é inválida (mau esquema ou demasiado velho, por exemplo).

Os tampão de telemetria [`enableSessionStorageBuffer`](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/JavaScript/JavaScriptSDK.Interfaces/IConfig.ts#L31) podem `false`ser desativados regulando para . Quando o armazenamento da sessão é desligado, uma matriz local é usada como armazenamento persistente. Uma vez que o JavaScript SDK funciona num dispositivo cliente, o utilizador tem acesso a este local de armazenamento através das ferramentas de desenvolvimento do seu navegador.

### <a name="opencensus-python"></a>OpenCensus Python

Por padrão, o OpenCensus Python SDK utiliza a atual pasta `%username%/.opencensus/.azure/`de utilizador . As permissões para aceder a esta pasta estão restritas ao utilizador e administradores atuais. (Ver [implementação](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py) aqui.) A pasta com os seus dados persistidos será nomeada em homenagem ao ficheiro Python que gerou a telemetria.

Pode alterar a localização do seu ficheiro `storage_path` de armazenamento, passando o parâmetro no construtor do exportador que está a utilizar.

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Como envio dados para Informações de Aplicação usando TLS 1.2?

Para garantir a segurança dos dados em trânsito para os pontos finais da Aplicação Insights, encorajamos fortemente os clientes a configurar a sua aplicação para utilizar pelo menos a Transport Layer Security (TLS) 1.2. As versões mais antigas da Camada de Tomadas Seguras (SSL) foram consideradas vulneráveis e, embora ainda trabalhem para permitir a retrocompatibilidade, não são **recomendadas**, e a indústria está rapidamente a mover-se para abandonar o suporte a estes protocolos mais antigos. 

O Conselho de Normas de Segurança do [PCI](https://www.pcisecuritystandards.org/) fixou um [prazo de 30 de junho de 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) para desativar versões mais antigas de TLS/SSL e atualizar para protocolos mais seguros. Uma vez que o Azure deixe cair o suporte legado, se a sua aplicação/clientes não puder comunicar pelo menos TLS 1.2 não poderá enviar dados para A Aplicação Insights. A abordagem que tomar para testar e validar o suporte TLS da sua aplicação variará consoante o sistema operativo/plataforma, bem como o idioma/enquadramento que a sua aplicação utiliza.

Não recomendamos que a definição explícita da sua aplicação utilize apenas TLS 1.2, a menos que seja necessário, uma vez que isso pode quebrar as funcionalidades de segurança de nível de plataforma que lhe permitem detetar e tirar partido de protocolos mais recentes e seguros à medida que se tornam disponíveis, como TLS 1.3. Recomendamos a realização de uma auditoria exaustiva do código da sua aplicação para verificar se há uma codificação rigorosa de versões específicas de TLS/SSL.

### <a name="platformlanguage-specific-guidance"></a>Orientação específica da plataforma/linguagem

|Plataforma/Idioma | Suporte | Mais Informações |
| --- | --- | --- |
| Serviços de Aplicações do Azure  | Suportada, pode ser necessária uma configuração. | O apoio foi anunciado em abril de 2018. Leia o anúncio para obter detalhes de [configuração](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Aplicações de Funções do Azure | Suportada, pode ser necessária uma configuração. | O apoio foi anunciado em abril de 2018. Leia o anúncio para obter detalhes de [configuração](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Suportada, a configuração varia consocada por versão. | Para obter informações detalhadas sobre a configuração para .NET 4.7 e versões anteriores, consulte [estas instruções](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitor de Estado | Suportado, configuração necessária | O Monitor de Estado baseia-se na [configuração](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + dos OS[.NET](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) para suportar o TLS 1.2.
|Node.js |  Suportada, em v10.5.0, pode ser necessária configuração. | Utilize a documentação oficial do [Nó.js TLS/SSL](https://nodejs.org/api/tls.html) para qualquer configuração específica da aplicação. |
|Java | Suportado, o suporte JDK para TLS 1.2 foi adicionado na [atualização JDK 6 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) e [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 utiliza [TLS 1.2 por defeito](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | As distribuições linux tendem a depender do [OpenSSL](https://www.openssl.org) para suporte TLS 1.2.  | Verifique o [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) para confirmar que a sua versão do OpenSSL está suportada.|
| Windows 8.0 - 10 | Suportado e ativado por defeito. | Para confirmar que ainda está a utilizar as [definições predefinidas](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 - 2016 | Suportado e ativado por defeito. | Para confirmar que ainda está a utilizar as [definições predefinidas](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 e Windows Server 2008 R2 SP1 | Suportado, mas não ativado por padrão. | Consulte a página de definições de registo de Segurança da Camada de [Transporte (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) para obter mais detalhes sobre como ativar.  |
| Windows Server 2008 SP2 | O suporte para TLS 1.2 requer uma atualização. | Consulte [a Atualização para adicionar suporte para TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) no Windows Server 2008 SP2. |
|Windows Vista | Não suportado. | N/D

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Verifique qual a versão do OpenSSL que a sua distribuição linux está a correr

Para verificar que versão do OpenSSL instalou, abra o terminal e corra:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Faça uma transação tLS 1.2 de teste no Linux

Para eexecutar um teste preliminar para ver se o seu sistema Linux pode comunicar através de TLS 1.2., abra o terminal e corra:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Dados pessoais armazenados em Insights de Aplicação

O nosso artigo de [dados pessoais da Application Insights](../../azure-monitor/platform/personal-data-mgmt.md) discute esta questão em profundidade.

#### <a name="can-my-users-turn-off-application-insights"></a>Os meus utilizadores podem desligar as Informações de Aplicação?
Não diretamente. Não fornecemos um interruptor que os seus utilizadores possam operar para desligar os Insights da Aplicação.

No entanto, pode implementar tal funcionalidade na sua aplicação. Todos os SDKs incluem um cenário de API que desliga a coleção de telemetria. 

## <a name="data-sent-by-application-insights"></a>Dados enviados por Informações de Aplicação
Os SDKs variam entre plataformas, existindo vários componentes que pode instalar. (Consulte os Insights de [Aplicação - visão geral][start].) Cada componente envia dados diferentes.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Classes de dados enviadas em diferentes cenários

| A sua ação | Aulas de dados recolhidas (ver tabela seguinte) |
| --- | --- |
| [Adicione Insights de Aplicação SDK a um projeto web .NET][greenbrown] |Contexto do servidor<br/>Inferido<br/>Contadores de desempenho<br/>Pedidos<br/>**Exceções**<br/>Sessão<br/>utilizadores |
| [Instalar monitor de estado no IIS][redfield] |Dependências<br/>Contexto do servidor<br/>Inferido<br/>Contadores de desempenho |
| [Adicione Insights de aplicação SDK a uma aplicação web java][java] |Contexto do servidor<br/>Inferido<br/>Pedir<br/>Sessão<br/>utilizadores |
| [Adicione JavaScript SDK à página web][client] |Contexto de Clientes <br/>Inferido<br/>Página<br/>ClientPerf<br/>Ajax |
| [Definir propriedades por defeito][apiproperties] |**Propriedades** em todos os eventos standard e personalizados |
| [Chamada TrackMetric][api] |Valores numéricos<br/>**Propriedades** |
| [Chamada Track*][api] |Nome do evento<br/>**Propriedades** |
| [Call TrackException][api] |**Exceções**<br/>Depósito de pilha<br/>**Propriedades** |
| A SDK não pode recolher dados. Por exemplo: <br/> - não pode aceder a contadores perf<br/> - exceção no inicializador de telemetria |Diagnósticos SDK |

Para [SDKs para outras plataformas,][platforms]consulte os seus documentos.

#### <a name="the-classes-of-collected-data"></a>As classes de dados recolhidos

| Classe de dados recolhida | Inclui (não uma lista exaustiva) |
| --- | --- |
| **Propriedades** |**Quaisquer dados - determinados pelo seu código** |
| Contexto de dispositivos |`Id`IP, Locale, Modelo de Dispositivo, rede, tipo de rede, nome OEM, resolução de ecrã, Role Instance, Role Name, Device Type |
| Contexto de Clientes |S, local, linguagem, rede, resolução de janelas |
| Sessão |`session id` |
| Contexto do servidor |Nome da máquina, local, SO, dispositivo, sessão do utilizador, contexto do utilizador, operação |
| Inferido |localização geo a partir de endereço IP, carimbo de tempo, OS, navegador |
| Métricas |Nome e valor métricos |
| Eventos |Nome e valor do evento |
| PageViews |URL e nome de página ou nome de tela |
| Cliente perf |Nome URL/página, tempo de carga do navegador |
| Ajax |HTTP chamadas de página web para servidor |
| Pedidos |URL, duração, código de resposta |
| Dependências |Tipo (SQL, HTTP, ...), cadeia de ligação ou URI, sync/async, duração, sucesso, declaração SQL (com Monitor de Estado) |
| **Exceções** |Tipo, **mensagem,** pilhas de chamadas, ficheiro fonte, número de linha,`thread id` |
| Acidentes |`Process id`, `parent process id`, `crash thread id`; patch de `id`aplicação, construir;  tipo de exceção, endereço, razão; símbolos e registos obfuscados, endereços binários de início e fim, nome binário e caminho, tipo cpu |
| Rastreio |**Mensagem** e nível de gravidade |
| Contadores de desempenho |Tempo de processador, memória disponível, taxa de pedido, taxa de exceção, processo de bytes privados, taxa IO, duração do pedido, tempo de fila de pedidos |
| Disponibilidade |Código de resposta do teste web, duração de cada etapa de teste, nome do teste, carimbo de tempo, sucesso, tempo de resposta, local de teste |
| Diagnósticos SDK |Mensagem de rastreio ou exceção |

Pode [desligar alguns dos dados editando ApplicationInsights.config][config]

> [!NOTE]
> O IP do cliente é usado para inferir a localização geográfica, mas por padrão os dados IP já não são armazenados e todos os zeros são escritos para o campo associado. Para saber mais sobre o tratamento de dados pessoais recomendamos este [artigo.](../../azure-monitor/platform/personal-data-mgmt.md#application-data) Se precisar de armazenar dados de endereçoIP, o nosso artigo de recolha de [endereços IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection) irá acompanhá-lo através das suas opções.

## <a name="credits"></a>Créditos
Este produto inclui dados GeoLite2 criados [https://www.maxmind.com](https://www.maxmind.com)pela MaxMind, disponíveis a partir de .



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
