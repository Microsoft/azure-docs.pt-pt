---
title: Notas de lançamento para Microsoft.ApplicationInsights.SnapshotCollector NuGet pacote - Insights de aplicações
description: Notas de lançamento para o pacote Microsoft.ApplicationInsights.SnapshotCollector NuGet utilizado pelo Debugger Snapshot Debugger de Insights de Aplicação.
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: 4a787c6e2a9b59874f965a2bbcebea9ce02d8082
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97093265"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Notas de lançamento para Microsoft.ApplicationInsights.SnapshotCollector

Este artigo contém as notas de lançamento para o pacote Microsoft.ApplicationInsights.SnapshotCollector NuGet para aplicações .NET, que é utilizado pelo Debugger Snapshot Desetor de Insights de Aplicação.

[Saiba](./snapshot-debugger.md) mais sobre o Application Insights Snapshot Debugger para aplicações .NET.

Para relatórios de bugs e feedback, abra um problema no GitHub em https://github.com/microsoft/ApplicationInsights-SnapshotCollector

## <a name="release-notes"></a>Notas de versão

## <a name="1375"></a>[1.3.7.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.5)
Um lançamento de ponto para retroportar uma correção a partir de 1.4.0-pre.
### <a name="bug-fixes"></a>Correções de erros
- Corrigir [ObjectDisposedException on shutdown](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2097).

## <a name="1374"></a>[1.3.7.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.4)
Um lançamento de ponto para resolver um problema descoberto no teste do cenário de anexação codeless do Azure App Service.
### <a name="changes"></a>Alterações
- O objetivo netcoreapp3.0 depende agora do Microsoft.ApplicationInsights.AspNetCore >= 2.1.1 (anteriormente >= 2.1.2).

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
Um lançamento de ponto para abordar um par de problemas de alto impacto.
### <a name="bug-fixes"></a>Correções de erros
- Descoberta fixa de PDB na pasta wwwroot/bin, que foi quebrada quando mudamos o algoritmo de pesquisa de símbolos em 1.3.6.
- Extrato ruidoso fixoWasCalledMultipleTimesExcepção na telemetria.

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>Alterações
- O objetivo netcoreapp2.0 do SnapshotCollector depende do Microsoft.ApplicationInsights.AspNetCore >= 2.1.1 (novamente). Isto reverte o comportamento como era antes de 1.3.5. Tentámos atualizá-lo em 1.3.6, mas quebrou alguns cenários do Azure App Service.
### <a name="new-features"></a>Novas funcionalidades
- O Snapshot Collector lê e analisa o ConnectionString da variável ambiente APPLICATIONINSIGHTS_CONNECTION_STRING ou da TelemetriaConfiguration. Em primeiro lugar, este é utilizado para definir o ponto final para a ligação ao serviço Snapshot. Para obter mais informações, consulte a [documentação das cordas de ligação.](./sdk-connection-string.md)
### <a name="bug-fixes"></a>Correções de erros
- Comutado a utilizar httpClient para todos os alvos exceto net45 porque webRequest estava falhando em alguns ambientes devido a um SecurityProtocol incompatível (requer TLS 1.2).

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>Alterações
- O SnapshotCollector depende agora do Microsoft.ApplicationInsights >= 2.5.1 para todos os quadros-alvo. Isto pode ser uma mudança de rutura se a sua aplicação depender de uma versão mais antiga do Microsoft.ApplicationInsights SDK.
- Remova o suporte para TLS 1.0 e 1.1 no Uploader Snapshot.
- O período de análises de PDB passa a estar em incumprimento de 24 horas em vez de 15 minutos. Configurado via PdbRescanInterval na SnapshotCollectorConfiguration.
- A varredura de PDB procura apenas pastas de nível superior, em vez de recursiva. Isto pode ser uma mudança de rutura se os seus símbolos estiverem em sub-dobras da pasta binária.
### <a name="new-features"></a>Novas funcionalidades
- Faça a rotação do registo no SnapshotUploader para evitar o preenchimento da pasta de registos com ficheiros antigos.
- Suporte de desoptimização (via ReJIT no anexo) para aplicações .NET Core 3.0.
- Adicione símbolos ao pacote NuGet.
- Desaponte metadados adicionais ao carregar minidumps.
- Adicionei uma propriedade inicializada ao SnapshotCollectorTelemetryProcessor. É um CanceladoToken, que será cancelado quando o Snapshot Collector estiver completamente inicializado e ligado ao ponto final de serviço.
- Os instantâneos podem agora ser capturados para exceções em métodos gerados dinamicamente. Por exemplo, a expressão compilada das árvores geradas pelas consultas do Quadro de Entidades.
### <a name="bug-fixes"></a>Correções de erros
- AmbíguoMesexcepção a carregar o Snapshot Collector devido ao Status Monitor.
- O método de extensão GetSnapshotCollector procura agora todos os TelemetriaSinks.
- Não inicie o Uploader Snapshot em plataformas não apoiadas.
- Lidar com a invalidaçãoExcepção ao desoptimizar métodos dinâmicos (por exemplo, Quadro de Entidade)

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- Adicione suporte para nuvens soberanas (versões mais antigas não funcionarão em nuvens soberanas)
- A adição de um coletor instantâneo facilitada com a utilização do AddSnapshotCollector(). Pode encontrar mais informações [aqui](./snapshot-debugger-appservice.md).
- Utilize a definição FISMA MD5 para verificar os blocos de bolhas. Isto evita o algoritmo cripto padrão .NET MD5, que não está disponível quando o SO está definido para o modo compatível com FIPS.
- Ignore os quadros de quadros .NET ao desoptimizar as chamadas de função. Este comportamento pode ser controlado pela configuração de DeoptimizeIgnoredModules.
- Adicione `DeoptimizeMethodCount` a definição de configuração que permite a desopimação de mais de uma chamada de função. Mais informações aqui

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- Permitir chaves estruturadas de instrumentação.
- Aumentar a robustez do SnapshotUploader - continue a iniciar o arranque mesmo que os registos de uploader antigos não possam ser movidos.
- Reativada a comunicação de telemetria adicional quando SnapshotUploader.exe saídas imediatamente (foi desativada em 1.3.3).
- Simplificar a telemetria interna.
- _Característica experimental_: Planos de recolha de Snappoint: Adicione "snapshotOnFirstOccurence". Mais informações disponíveis [aqui.](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- Bug fixo que estava a causar SnapshotUploader.exe para parar de responder e não carregar instantâneos para aplicações .NET Core.

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _Característica experimental_: Planos de recolha de Snappoint. Mais informações disponíveis [aqui.](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)
- SnapshotUploader.exe sairá quando o tempo de funcionamento descarregar o AppDomain a partir do qual o SnapshotCollector está carregado, em vez de esperar que o processo saia. Isto melhora a fiabilidade do colecionador quando hospedado no IIS.
- Adicione a configuração para permitir que várias instâncias snapshotCollector que estão a usar a mesma Chave de Instrumentação partilhem o mesmo processo SnapshotUploader: ShareUploaderProcess (predefinições a `true` ).
- Reporte telemetria adicional quando SnapshotUploader.exe sair imediatamente.
- Reduziu o número de ficheiros de suporte SnapshotUploader.exe precisa de escrever para o disco.

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- Remova o suporte para recolher instantâneos com a API rtlCloneUser Process e apoie apenas a API de Fotos PssCaptureSnapshots.
- Aumente o limite de predefinição de quantos instantâneos podem ser capturados em 10 minutos de 1 a 3.
- Permitir SnapshotUploader.exe negociar TLS 1.1 e 1.2
- Reporte telemetria adicional quando o SnapshotUploader regista um aviso ou um erro
- Pare de tirar fotos quando o serviço de backend reportar que a quota diária foi atingida (50 instantâneos por dia)
- Adicione o check-in extra SnapshotUploader.exe para não permitir que duas instâncias corram ao mesmo tempo.

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>Alterações
- Para aplicações que direcionam o Quadro .NET, o Snapshot Collector depende agora da versão 2.3.0 ou superior da Microsoft.ApplicationInsights.
Costumava ser 2.2.0 ou superior.
Acreditamos que este não será um problema para a maioria das aplicações, mas avise-nos se esta mudança o impede de usar o mais recente Snapshot Collector.
- Utilize atrasos exponenciais de back-off no Uploader Snapshot quando voltar a tentar os uploads falhados.
- Utilize serverTelemetryChannel (se disponível) para um relato mais fiável da telemetria.
- Utilize o 'SdkInternalOperationsMonitor' na ligação inicial ao serviço Snapshot Debugger para que seja ignorado pelo rastreio de dependência.
- Melhore a telemetria em torno da ligação inicial ao serviço Snapshot Debugger.
- Reportar telemetria adicional para:
  - Versão Azure App Service.
  - Casos de computação azure.
  - Os contentores.
  - App Azure Function.
### <a name="bug-fixes"></a>Correções de erros
- Quando o intervalo de reset do contador de problemas estiver definido para 24 dias, interprete-o como 24 horas.
- Corrigiu um bug onde o Uploader Snapshot deixaria de processar novas imagens se houvesse uma exceção enquanto eliminava uma fotografia.

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- Fixe a assinatura de nome forte com binários Snapshot Uploader.

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>Alterações
- Os ficheiros necessários para o SnapshotUploader(64)).exe estão agora incorporados como recursos no DLL principal. Isto significa que a pasta SnapshotCollectorFiles já não é criada, simplificando a construção e implementação e reduzindo a desordem no Solution Explorer. Tome cuidado ao atualizar para rever as alterações no seu `.csproj` ficheiro. O `Microsoft.ApplicationInsights.SnapshotCollector.targets` ficheiro já não é necessário.
- A telemetria é registada no seu recurso Application Insights mesmo que aTelemetry ProvideAnonymous esteja definida como falsa. Isto é para que possamos implementar uma funcionalidade de verificação de saúde no portal Azure. A ProvideAnonymousTelemetry afeta apenas a telemetria enviada à Microsoft para suporte e melhoria do produto.
- Quando o TempFolder ou o ShadowCopyFolder forem redirecionados para variáveis ambientais, mantenha o coletor inativo até que essas variáveis ambientais estejam definidas.
- Para aplicações que se ligam à Internet através de um servidor proxy, o Snapshot Collector irá agora automaticamente desdetectar quaisquer definições de procuração e passá-las para SnapshotUploader.exe.
- Diminua a prioridade do processo SnapshotUplaoder (sempre que possível). Esta prioridade pode ser ultrapassada através da opção IsLowPrioirtySnapshotUploader.
- Adicione um método de extensão GetSnapshotCollector na TelemetriaConfiguration para cenários onde pretende configurar o Colecionador instantâneo programáticamente.
- Desaponte a versão SDK do Application Insights (em vez da versão de aplicação) na telemetria voltada para o cliente.
- Envie o primeiro evento cardíaco depois de dois minutos.
### <a name="bug-fixes"></a>Correções de erros
- Fixar NullReferenceExcepção quando as exceções tiverem dicionários de dados nulos ou imutáveis.
- No carregador, recandidú-lo correspondendo algumas vezes se tivermos uma violação de partilha.
- Corrija a telemetria duplicada quando mais de um fio chama para o pipeline de telemetria no arranque.

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>Alterações
- Os ficheiros de comentários XML Doc estão agora incluídos no pacote NuGet.
- Adicione um método de extensão ExcluiFromSnapshotting `System.Exception` para cenários em que sabe que tem uma exceção ruidosa e quer evitar criar instantâneos para ele.
- Adicionei uma propriedade de configuração IsEnabledWhenProfiling, predefinidos a verdadeiro. Esta é uma mudança em versões anteriores em que a criação de instantâneos foi temporariamente desativada se o Application Insights Profiler estava a realizar uma coleção detalhada. O comportamento antigo pode ser recuperado definindo esta propriedade em falso.
### <a name="bug-fixes"></a>Correções de erros
- Assine SnapshotUploader64.exe corretamente.
- Proteja contra a dupla inicialização do processador de telemetria.
- Evite o duplo registo de telemetria em aplicações com vários oleodutos.
- Corrija um erro com o tempo de validade de um plano de recolha, o que poderia evitar instantâneos após 24 horas.

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
A maior mudança nesta versão (daí a mudança para um novo número de versão menor) é uma reescrita do pipeline de criação e manuseamento de instantâneos. Em versões anteriores, esta funcionalidade foi implementada em código nativo (ProductionBreakpoints *.dll e SnapshotHolder*.exe). A nova implementação é tudo gerido código com P/Invokes. Para esta primeira versão usando o novo oleoduto, não nos desviamos muito do comportamento original. A nova implementação permite uma melhor comunicação de erros e prepara-nos para melhorias futuras.

### <a name="other-changes-in-this-version"></a>Outras alterações nesta versão
- MinidumpUploader.exe foi renomeado para SnapshotUploader.exe (ou SnapshotUploader64.exe).
- Telemetria de tempo adicionado a Pedidos de DeOptimize/ReOptimize.
- Compressão gzip adicionada para uploads de minidump.
- Corrigiu um problema em que os PDBs estavam bloqueados impedindo a atualização do local.
- Faça o registo do nome da pasta original (SnapshotCollectorFiles) ao copiar sombra.
- Ajuste os limites de memória para processos de 64 bits para evitar que o local reinicie devido à OOM.
- Corrija um problema onde as fotos ainda eram recolhidas mesmo depois de desativadas.
- Registar eventos de batimentos cardíacos para o recurso de IA do cliente.
- Melhore a velocidade do instantâneo removendo "Source" do ID do problema.

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>Alterações
Telemetria de utilização aumentada
- Detetar e reportar a versão .NET e o SO
- Detetar e reportar ambientes Azure adicionais (Cloud Service, Service Fabric)
- Registar e reportar métricas de exceção (número de 1ª chance exceções e número de chamadas TrackException) na telemetria heartbeat.
### <a name="bug-fixes"></a>Correções de erros
- Manuseamento correto da SqlException onde a exceção interior (Win32Exception) não é lançada.
- Aparar espaços em pastas de símbolos, o que causou uma análise incorreta dos argumentos da linha de comando para o MinidumpUploader.
- Evite uma repetição infinita de ligações falhadas ao ponto final do agente Snapshot Debugger.

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>Alterações
- Acrescentou proteção de memória do anfitrião. Esta funcionalidade reduz o impacto na memória da máquina hospedeira.
- Melhore a experiência de visualização instantânea do portal Azure.