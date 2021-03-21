---
title: Retenção e armazenamento de dados em Azure Application Insights | Microsoft Docs
description: Declaração de política de retenção e privacidade
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 54d3e53b71b5f63da84e41a752bbbb6fce65c045
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579587"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Recolha, retenção e armazenamento de dados no Application Insights

Quando instala [o Azure Application Insights][start] SDK na sua aplicação, envia telemetria sobre a sua aplicação para a Cloud. Naturalmente, os desenvolvedores responsáveis querem saber exatamente que dados são enviados, o que acontece com os dados, e como podem manter o controlo dos mesmos. Em particular, poderiam ser enviados dados sensíveis, onde está armazenado e quão seguro é? 

Primeiro, a resposta curta:

* É pouco provável que os módulos de telemetria padrão que ficam "fora da caixa" enviem dados sensíveis para o serviço. A telemetria diz respeito a métricas de carga, desempenho e utilização, relatórios de exceção e outros dados de diagnóstico. Os principais dados do utilizador visíveis nos relatórios de diagnóstico são URLs; mas a sua aplicação não deve, em caso algum, colocar dados sensíveis em texto simples num URL.
* Pode escrever código que envia telemetria personalizada adicional para ajudá-lo com diagnósticos e monitorização do uso. (Esta extensibilidade é uma grande característica da Application Insights.) Seria possível, por engano, escrever este código de modo a incluir dados pessoais e outros dados sensíveis. Se a sua aplicação funcionar com esses dados, deverá aplicar um processo de revisão completa a todo o código que escreve.
* Ao desenvolver e testar a sua aplicação, é fácil inspecionar o que está a ser enviado pelo SDK. Os dados aparecem nas janelas de saída depurativa do IDE e do browser.
* Pode selecionar a localização quando criar um novo recurso Application Insights. Saiba mais sobre a disponibilidade de Insights de Aplicação por região [aqui.](https://azure.microsoft.com/global-infrastructure/services/?products=all)
*   Reveja os dados recolhidos, pois estes podem incluir dados que são permitidos em algumas circunstâncias, mas não em outros.  Um bom exemplo disso é o Nome do Dispositivo. O nome do dispositivo de um servidor não tem impacto na privacidade e é útil, mas um nome de dispositivo de um telefone ou portátil pode ter um impacto de privacidade e ser menos útil. Um SDK desenvolvido principalmente para direcionar os servidores, iria recolher o nome do dispositivo por padrão, e isso pode ter de ser substituído tanto em eventos normais como em exceções.

O resto deste artigo elabora mais plenamente estas respostas. Foi concebido para ser autossuficiente, para que possa mostrá-lo a colegas que não fazem parte da sua equipa imediata.

## <a name="what-is-application-insights"></a>O que é o Application Insights?
[O Azure Application Insights][start] é um serviço fornecido pela Microsoft que o ajuda a melhorar o desempenho e a usabilidade da sua aplicação ao vivo. Monitoriza a sua aplicação o tempo todo que está a funcionar, tanto durante os testes como depois de a ter publicado ou implantado. O Application Insights cria gráficos e tabelas que lhe mostram, por exemplo, quais as horas do dia que obtém a maioria dos utilizadores, quão responsiva a aplicação é e quão bem é servida por quaisquer serviços externos de que depende. Se houver falhas, falhas ou problemas de desempenho, pode pesquisar em detalhe os dados da telemetria para diagnosticar a causa. E o serviço enviar-lhe-á e-mails se houver alguma alteração na disponibilidade e desempenho da sua app.

Para obter esta funcionalidade, instale um SDK Application Insights na sua aplicação, que passa a fazer parte do seu código. Quando a sua aplicação está em execução, o SDK monitoriza o seu funcionamento e envia telemetria para o serviço Application Insights. Este é um serviço de nuvem hospedado pela [Microsoft Azure](https://azure.com). (Mas a Application Insights funciona para quaisquer aplicações, e não apenas para aplicações que estão hospedadas no Azure.)

O serviço Application Insights armazena e analisa a telemetria. Para ver a análise ou pesquisar através da telemetria armazenada, faça sedúns na sua conta Azure e abra o recurso Application Insights para a sua aplicação. Também pode partilhar o acesso aos dados com outros membros da sua equipa, ou com os subscritores Azure especificados.

Pode ter dados exportados do serviço Application Insights, por exemplo, para uma base de dados ou para ferramentas externas. Fornece a cada ferramenta uma chave especial que obtém do serviço. A chave pode ser revogada se necessário. 

Os SDKs application Insights estão disponíveis para uma série de tipos de aplicações: serviços web alojados nos seus próprios servidores Java EE ou ASP.NET, ou no Azure; clientes web - isto é, o código em execução em uma página web; aplicativos e serviços de desktop; aplicativos de dispositivos como Windows Phone, iOS e Android. Todos enviam telemetria para o mesmo serviço.

## <a name="what-data-does-it-collect"></a>Que dados recolhe?
Existem três fontes de dados:

* O SDK, que integra com a sua app [em desenvolvimento](./asp-net.md) ou em tempo [de execução.](./monitor-performance-live-website-now.md) Existem diferentes SDKs para diferentes tipos de aplicação. Existe também um [SDK para páginas web](./javascript.md), que carrega no navegador do utilizador final juntamente com a página.
  
  * Cada SDK tem uma série de [módulos,](./configuration-with-applicationinsights-config.md)que usam diferentes técnicas para recolher diferentes tipos de telemetria.
  * Se instalar o SDK em desenvolvimento, pode utilizar a sua API para enviar a sua própria telemetria, para além dos módulos standard. Esta telemetria personalizada pode incluir todos os dados que pretende enviar.
* Em alguns servidores web, existem também agentes que correm ao lado da app e enviam telemetria sobre CPU, memória e ocupação de rede. Por exemplo, VMs Azure, anfitriões Docker e [servidores Java EE](./java-agent.md) podem ter tais agentes.
* [Os testes de disponibilidade](./monitor-web-app-availability.md) são processos executados pela Microsoft que enviam pedidos para a sua aplicação web a intervalos regulares. Os resultados são enviados para o serviço Desemis.

### <a name="what-kinds-of-data-are-collected"></a>Que tipo de dados são recolhidos?
As principais categorias são:

* [Telemetria do servidor web](./asp-net.md) - pedidos HTTP.  Uri, tempo necessário para processar o pedido, código de resposta, endereço IP do cliente. `Session id`.
* [Páginas web](./javascript.md) - Página, utilizador e contagem de sessão. Tempos de carregamento de página. Exceções. Ajax chama.
* Contadores de desempenho - Memória, CPU, IO, Ocupação da Rede.
* Contexto do cliente e do servidor - SISTEMA, local, tipo de dispositivo, navegador, resolução de ecrã.
* [Exceções](./asp-net-exceptions.md) e acidentes - **lixeiras de pilhas,** `build id` tipo CPU. 
* [Dependências](./asp-net-dependencies.md) - chamadas para serviços externos como REST, SQL, AJAX. URI ou fio de ligação, duração, sucesso, comando.
* [Testes de disponibilidade](./monitor-web-app-availability.md) - duração do teste e etapas, respostas.
* [Trace registos](./asp-net-trace-logs.md) e [telemetria personalizada](./api-custom-events-metrics.md)  -  **qualquer coisa que codificar nos seus registos ou telemetria**.

[Mais detalhes.](#data-sent-by-application-insights)

## <a name="how-can-i-verify-whats-being-collected"></a>Como posso verificar o que está a ser recolhido?
Se estiver a desenvolver a aplicação utilizando o Visual Studio, execute a aplicação no modo depuror (F5). A telemetria aparece na janela de saída. A partir daí, pode copiá-lo e enucaê-lo como JSON para uma inspeção fácil. 

![Screenshot que mostra executar a aplicação em modo depurrão em Estúdio Visual.](./media/data-retention-privacy/06-vs.png)

Há também uma vista mais legível na janela de Diagnóstico.

Para páginas web, abra a janela de depuragem do seu navegador.

![Prima F12 e abra o separador 'Rede'.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Posso escrever código para filtrar a telemetria antes de ser enviada?
Isto seria possível escrevendo um [plugin de processador de telemetria](./api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Quanto tempo os dados são mantidos?
Os pontos de dados brutos (isto é, itens que pode consultar em Analytics e inspecionar em Pesquisa) são mantidos por até 730 dias. Pode [selecionar uma duração](./pricing.md#change-the-data-retention-period) de retenção de 30, 60, 90, 120, 180, 270, 365, 550 ou 730 dias. Se precisar de manter os dados por mais de 730 dias, pode utilizar a [Exportação Contínua](./export-telemetry.md) para copiá-lo numa conta de armazenamento durante a ingestão de dados. 

Os dados mantidos por mais de 90 dias incorrerão em taxas adicionais. Saiba mais sobre os preços dos Insights de Aplicação na página de preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Os dados agregados (isto é, contagens, médias e outros dados estatísticos que vê no Metric Explorer) são retidos num grão de 1 minuto durante 90 dias.

[As fotos de depurg](./snapshot-debugger.md) são armazenadas durante 15 dias. Esta política de retenção é definida numa base por aplicação. Se precisar de aumentar este valor, pode solicitar um aumento abrindo um caso de suporte no portal Azure.

## <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?
Os dados são visíveis para si e, se tiver uma conta de organização, os membros da sua equipa. 

Pode ser exportado por si e pelos membros da sua equipa e pode ser copiado para outros locais e transmitido a outras pessoas.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>O que faz a Microsoft com a informação que a minha aplicação envia para o Application Insights?
A Microsoft utiliza os dados apenas para lhe fornecer o serviço.

## <a name="where-is-the-data-held"></a>Onde estão os dados guardados?
* Pode selecionar a localização quando criar um novo recurso Application Insights. Saiba mais sobre a disponibilidade de Insights de Aplicação por região [aqui.](https://azure.microsoft.com/global-infrastructure/services/?products=all)

## <a name="how-secure-is-my-data"></a>Quão seguros são os meus dados?
Application Insights é um Serviço Azure. As políticas de segurança são descritas no [Livro Branco de Segurança, Privacidade e Conformidade do Azure](https://go.microsoft.com/fwlink/?linkid=392408).

Os dados são armazenados nos servidores do Microsoft Azure. Para contas no portal Azure, as restrições à conta são descritas no [documento Azure Security, Privacy e Compliance](https://go.microsoft.com/fwlink/?linkid=392408).

O acesso aos seus dados pelo pessoal da Microsoft é restrito. Acedemos aos seus dados apenas com a sua permissão e se for necessário apoiar a sua utilização de Application Insights. 

Os dados em conjunto em todas as aplicações dos nossos clientes (tais como taxas de dados e tamanho médio de vestígios) são usados para melhorar o Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>A telemetria de outra pessoa pode interferir com os meus dados de Insights de Aplicação?
Podem enviar telemetria adicional para a sua conta utilizando a chave de instrumentação, que pode ser encontrada no código das suas páginas web. Com dados adicionais suficientes, as suas métricas não representariam corretamente o desempenho e utilização da sua aplicação.

Se partilhar código com outros projetos, lembre-se de remover a sua chave de instrumentação.

## <a name="is-the-data-encrypted"></a>Os dados estão encriptados?
Todos os dados são encriptados em repouso e à medida que se movem entre centros de dados.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Os dados são encriptados em trânsito da minha aplicação para servidores Application Insights?
Sim, usamos https para enviar dados para o portal de quase todos os SDKs, incluindo servidores web, dispositivos e páginas web HTTPS. 

## <a name="does-the-sdk-create-temporary-local-storage"></a>O SDK cria armazenamento local temporário?

Sim, certos canais de telemetria persistirão dados localmente se não for possível alcançar um ponto final. Por favor, reveja abaixo para ver quais os quadros e canais de telemetria afetados.

Os canais de telemetria que utilizam o armazenamento local criam ficheiros temporários nos diretórios TEMP ou APPDATA, que se limitam à conta específica que executa a sua aplicação. Isto pode acontecer quando um ponto final estava temporariamente indisponível ou se atingir o limite de estrangulamento. Uma vez resolvido este problema, o canal de telemetria retomará o envio de todos os dados novos e persistidos.

Estes dados persistidos não são encriptados localmente. Se isso for uma preocupação, reveja os dados e restringa a recolha de dados privados. (Para mais informações, consulte [Como exportar e eliminar dados privados](../logs/personal-data-mgmt.md#how-to-export-and-delete-private-data).)

Se um cliente precisar de configurar este diretório com requisitos de segurança específicos, pode ser configurado por estrutura. Certifique-se de que o processo em execução da sua aplicação tem acesso a este diretório, mas também certifique-se de que este diretório está protegido para evitar que a telemetria seja lida por utilizadores não intencionais.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` é usado para dados persistentes. Esta localização não é configurável a partir do diretório config e as permissões de acesso a esta pasta são restritas ao utilizador específico com credenciais necessárias. (Para mais informações, consulte [a implementação](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72).)

###  <a name="net"></a>.NET

Por `ServerTelemetryChannel` predefinição, utiliza a pasta de dados de aplicações locais do utilizador atual `%localAppData%\Microsoft\ApplicationInsights` ou a pasta temporária `%TMP%` . (Ver [implementação](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) aqui.)


Através do ficheiro de configuração:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Via código:

- Remover ServerTelemetryChannel do ficheiro de configuração
- Adicione este corte à sua configuração:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Por `ServerTelemetryChannel` predefinição, utiliza a pasta de dados de aplicações locais do utilizador atual `%localAppData%\Microsoft\ApplicationInsights` ou a pasta temporária `%TMP%` . (Ver [implementação](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) aqui.) 

Num ambiente Linux, o armazenamento local será desativado a menos que seja especificada uma pasta de armazenamento.

> [!NOTE]
> Com o lançamento 2.15.0-beta3 e maior armazenamento local é agora automaticamente criado para Linux, Mac e Windows. Para sistemas não Windows, o SDK criará automaticamente uma pasta de armazenamento local com base na seguinte lógica:
> - `${TMPDIR}` - se `${TMPDIR}` a variável ambiental for definida, esta localização é utilizada.
> - `/var/tmp` - se a localização anterior não existir, `/var/tmp` tentamos.
> - `/tmp` - se ambas as localizações anteriores não existirem, `tmp` tentamos. 
> - Se nenhum desses locais existir armazenamento local não é criado e a configuração manual ainda é necessária. [Para mais detalhes de implementação.](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860)

O seguinte corte de código mostra como definir `ServerTelemetryChannel.StorageFolder` no método da sua `ConfigureServices()` `Startup.cs` classe:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Para mais informações, consulte [a configuração personalizada AspNetCore](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).)

### <a name="nodejs"></a>Node.js

Por padrão `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` é usado para dados persistentes. As permissões de acesso a esta pasta estão restritas ao utilizador e administradores atuais. (Ver [implementação](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) aqui.)

O prefixo da pasta `appInsights-node` pode ser ultrapassado alterando o valor de tempo de execução da variável estática `Sender.TEMPDIR_PREFIX` encontrada em [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).

### <a name="javascript-browser"></a>JavaScript (browser)

[HTML5 O armazenamento de sessão](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) é utilizado para persistir dados. São utilizados dois tampão separados: `AI_buffer` e `AI_sent_buffer` . A telemetria que é emaresada e à espera de ser enviada é armazenada em `AI_buffer` . A telemetria que acabou de ser enviada é colocada `AI_sent_buffer` até que o servidor de ingestão responda que foi recebida com sucesso. Quando a telemetria é recebida com sucesso, é removida de todos os amortecedores. Em falhas transitórias (por exemplo, um utilizador perde a conectividade da rede), a telemetria permanece `AI_buffer` até ser recebida com sucesso ou o servidor de ingestão responde que a telemetria é inválida (esquema mau ou demasiado velho, por exemplo).

Os tampões de telemetria podem ser desativados definindo [`enableSessionStorageBuffer`](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/JavaScript/JavaScriptSDK.Interfaces/IConfig.ts#L31) para `false` . Quando o armazenamento da sessão é desligado, uma matriz local é usada como armazenamento persistente. Como o SDK JavaScript funciona num dispositivo cliente, o utilizador tem acesso a este local de armazenamento através das ferramentas de desenvolvimento do seu navegador.

### <a name="opencensus-python"></a>OpenCensus Python

Por predefinição, o OpenCensus Python SDK utiliza a pasta atual do utilizador `%username%/.opencensus/.azure/` . As permissões de acesso a esta pasta estão restritas ao utilizador e administradores atuais. (Ver [implementação](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py) aqui.) A pasta com os seus dados persistidos será nomeada em homenagem ao ficheiro Python que gerou a telemetria.

Pode alterar a localização do seu ficheiro de armazenamento passando o `storage_path` parâmetro no construtor do exportador que está a utilizar.

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Como envio dados para Insights de Aplicação utilizando o TLS 1.2?

Para garantir a segurança dos dados em trânsito para os pontos finais do Application Insights, encorajamos fortemente os clientes a configurarem a sua aplicação para utilizarem pelo menos a Segurança da Camada de Transporte (TLS) 1.2. Versões mais antigas da Camada de Tomadas TLS/Secure Sockets (SSL) foram consideradas vulneráveis e, embora ainda atualmente trabalhem para permitir retrocompatibilidade, não são **recomendadas**, e a indústria está rapidamente a mover-se para abandonar o suporte a estes protocolos mais antigos. 

O Conselho de Normas de Segurança do [PCI](https://www.pcisecuritystandards.org/) fixou um [prazo de 30 de junho de 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) para desativar versões mais antigas do TLS/SSL e atualizar para protocolos mais seguros. Uma vez que o Azure deixe cair o suporte ao legado, se a sua aplicação/clientes não puderem comunicar pelo menos o TLS 1.2 não poderá enviar dados para a Application Insights. A abordagem que tomar para testar e validar o suporte TLS da sua aplicação variará consoante o sistema operativo/plataforma, bem como o idioma/enquadramento que a sua aplicação utiliza.

Não recomendamos que a sua aplicação apenas utilize o TLS 1.2, a menos que seja necessário, pois pode quebrar funcionalidades de segurança de nível da plataforma que lhe permitam detetar e tirar partido de protocolos mais recentes e mais seguros à medida que ficam disponíveis, como o TLS 1.3. Recomendamos a realização de uma auditoria exaustiva do código da sua aplicação para verificar se existem análises de hardcoding de versões TLS/SSL específicas.

### <a name="platformlanguage-specific-guidance"></a>Orientação específica da plataforma/idioma

|Plataforma/Idioma | Suporte | Mais Informações |
| --- | --- | --- |
| Serviços de Aplicações do Azure  | Suportado, pode ser necessária configuração. | O apoio foi anunciado em abril de 2018. Leia o anúncio para [detalhes da configuração](https://azure.github.io/AppService/2018/04/17/App-Service-and-Functions-hosted-apps-can-now-update-TLS-versions!).  |
| Aplicações de Funções do Azure | Suportado, pode ser necessária configuração. | O apoio foi anunciado em abril de 2018. Leia o anúncio para [detalhes da configuração](https://azure.github.io/AppService/2018/04/17/App-Service-and-Functions-hosted-apps-can-now-update-TLS-versions!). |
|.NET | Suportada, a configuração varia por versão. | Para obter informações detalhadas sobre a configuração para as versões .NET 4.7 e anteriores, consulte [estas instruções](/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitor de Estado | Suportado, configuração necessária | O Status Monitor baseia-se na [configuração de configuração do SISTEMA](/windows-server/security/tls/tls-registry-settings)  +  [.NET](/dotnet/framework/network-programming/tls#support-for-tls-12) para suportar O TLS 1.2.
|Node.js |  Suportada, em v10.5.0, pode ser necessária uma configuração. | Utilize a [documentação oficial Node.js TLS/SSL](https://nodejs.org/api/tls.html) para qualquer configuração específica da aplicação. |
|Java | Suportado, o suporte JDK para TLS 1.2 foi adicionado na [atualização 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) e [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 utiliza [TLS 1.2 por predefinição](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | As distribuições linux tendem a contar com [o suporte OpenSSL](https://www.openssl.org) para suporte TLS 1.2.  | Verifique o [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) para confirmar que a sua versão do OpenSSL está suportada.|
| Windows 8.0 - 10 | Suportado e ativado por defeito. | Para confirmar que ainda está a utilizar as [definições predefinições](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 - 2016 | Suportado e ativado por defeito. | Para confirmar que ainda está a utilizar as [definições predefinidos](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 e Windows Server 2008 R2 SP1 | Suportado, mas não ativado por defeito. | Consulte a página [de definições de registo de segurança da camada de transporte (TLS)](/windows-server/security/tls/tls-registry-settings) para obter mais detalhes sobre como ativar.  |
| Windows Server 2008 SP2 | O suporte para TLS 1.2 requer uma atualização. | Consulte [Update para adicionar suporte para TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) no Windows Server 2008 SP2. |
|Windows Vista | Não suportado. | N/D

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Verifique qual a versão do OpenSSL que a sua distribuição Linux está a decorrer

Para verificar que versão do OpenSSL instalou, abra o terminal e corra:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Executar uma transação TLS 1.2 de teste no Linux

Para realizar um teste preliminar para ver se o seu sistema Linux pode comunicar através do TLS 1.2., abra o terminal e corra:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Dados pessoais armazenados em Insights de Aplicação

O nosso artigo de [dados pessoais da Application Insights](../logs/personal-data-mgmt.md) discute esta questão em profundidade.

#### <a name="can-my-users-turn-off-application-insights"></a>Os meus utilizadores podem desligar o Application Insights?
Não diretamente. Não fornecemos um interruptor que os seus utilizadores possam operar para desativar o Application Insights.

No entanto, pode implementar tal funcionalidade na sua aplicação. Todos os SDKs incluem uma definição de API que desliga a coleção de telemetria. 

## <a name="data-sent-by-application-insights"></a>Dados enviados por Application Insights
Os SDKs variam entre plataformas, e existem vários componentes que pode instalar. (Consulte os [Insights de Aplicação - visão geral][start].) Cada componente envia dados diferentes.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Classes de dados enviados em diferentes cenários

| A sua ação | Aulas de dados recolhidas (ver tabela ao lado) |
| --- | --- |
| [Adicionar Insights de Aplicação SDK a um projeto web .NET][greenbrown] |ServerContext<br/>Inferido<br/>Contadores de desempenho<br/>Pedidos<br/>**Exceções**<br/>Sessão<br/>utilizadores |
| [Instalar o Monitor de Estado no IIS][redfield] |Dependências<br/>ServerContext<br/>Inferido<br/>Contadores de desempenho |
| [Adicionar App Insights SDK a uma aplicação web java][java] |ServerContext<br/>Inferido<br/>Pedir<br/>Sessão<br/>utilizadores |
| [Adicione JavaScript SDK à página web][client] |ClientContext <br/>Inferido<br/>Página<br/>ClientePerf<br/>Ajax |
| [Definir propriedades predefinidos][apiproperties] |**Propriedades** em todos os eventos padrão e personalizados |
| [Chamada TrackMetric][api] |Valores numéricos<br/>**Propriedades** |
| [Faixa de chamada*][api] |Nome do evento<br/>**Propriedades** |
| [Chamada TrackException][api] |**Exceções**<br/>Despejo de pilha<br/>**Propriedades** |
| A SDK não pode recolher dados. Por exemplo: <br/> - não pode aceder a balcões perf<br/> - exceção no inicializador de telemetria |Diagnósticos SDK |

Para [SDKs para outras plataformas,][platforms]consulte os seus documentos.

#### <a name="the-classes-of-collected-data"></a>As classes de dados recolhidos

| Classe de dados recolhidas | Inclui (não uma lista exaustiva) |
| --- | --- |
| **Propriedades** |**Quaisquer dados - determinados pelo seu código** |
| Configuração do Dispositivo |`Id`, IP, Locale, Modelo de Dispositivo, rede, tipo de rede, nome OEM, resolução de ecrã, Instância de Papel, Nome de Papel, Tipo de Dispositivo |
| ClientContext |OS, local, idioma, rede, resolução de janelas |
| Sessão |`session id` |
| ServerContext |Nome da máquina, local, SISTEMA, dispositivo, sessão de utilizador, contexto do utilizador, operação |
| Inferido |localização geo a partir de endereço IP, timetamp, OS, browser |
| Métricas |Nome e valor métricos |
| Eventos |Nome e valor do evento |
| PageViews |URL e nome de página ou nome de ecrã |
| Cliente perf |Nome DE URL/página, tempo de carregamento do navegador |
| Ajax |CHAMADAS HTTP da página web para o servidor |
| Pedidos |URL, duração, código de resposta |
| Dependências |Tipo (SQL, HTTP, ...), cadeia de ligação, ou URI, sincronização/async, duração, sucesso, declaração SQL (com Status Monitor) |
| **Exceções** |Tipo, **mensagem,** pilhas de chamadas, ficheiro de origem, número de linha, `thread id` |
| Acidentes |`Process id`, `parent process id` `crash thread id` ; patch de aplicação, `id` construção;  tipo de exceção, endereço, razão; símbolos e registos obfuscados, endereços binários de início e fim, nome binário e caminho, tipo CPU |
| Rastreio |**Mensagem** e nível de gravidade |
| Contadores de desempenho |Tempo do processador, memória disponível, taxa de pedido, taxa de exceção, processo de bytes privados, taxa de IO, duração do pedido, duração da fila de pedido |
| Disponibilidade |Código de resposta ao teste web, duração de cada etapa de teste, nome de teste, relógio, sucesso, tempo de resposta, localização de teste |
| Diagnósticos SDK |Mensagem de rastreio ou exceção |

Pode [desativar alguns dos dados editando ApplicationInsights.config][config]

> [!NOTE]
> O COMPUTADOR do cliente é utilizado para inferir a localização geográfica, mas por padrão os dados IP já não são armazenados e todos os zeros são escritos para o campo associado. Para saber mais sobre o tratamento de dados pessoais recomendamos este [artigo.](../logs/personal-data-mgmt.md#application-data) Se necessitar de armazenar dados de endereço IP, o nosso [artigo de recolha de endereços IP](./ip-collection.md) irá acompanhar-lhe as suas opções.

## <a name="credits"></a>Créditos
Este produto inclui dados da GeoLite2 criados pela MaxMind, disponíveis a partir de [https://www.maxmind.com](https://www.maxmind.com) .



<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiproperties]: ./api-custom-events-metrics.md#properties
[client]: ./javascript.md
[config]: ./configuration-with-applicationinsights-config.md
[greenbrown]: ./asp-net.md
[java]: ./java-get-started.md
[platforms]: ./platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md

