---
title: Resolução de problemas de extensão de diagnóstico do Azure
description: Resolva problemas ao utilizar o diagnóstico do Azure em máquinas virtuais do Azure, Service Fabric ou serviços Cloud.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: robb
ms.openlocfilehash: 81c93900acf2d75eeb8e4fdc8da7d563f3a59595
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60395055"
---
# <a name="azure-diagnostics-troubleshooting"></a>Resolução de problemas de diagnóstico do Azure
Este artigo descreve as informações de resolução de problemas que são relevantes para a utilização de diagnóstico do Azure. Para obter mais informações sobre o diagnóstico do Azure, consulte [descrição geral de diagnóstico do Azure](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Componentes lógicos
**Iniciador de plug-in de diagnóstico (DiagnosticsPluginLauncher.exe)**: Inicia a extensão de diagnóstico do Azure. Serve como a entrada do ponto de processo.

**Plug-in de diagnóstico (DiagnosticsPlugin.exe)**: Configura, inicia e gere a vida útil do agente de monitorização. É o processo principal que é iniciado quando o iniciador.

**Agente de monitorização (MonAgent\*.exe processos)**: Monitoriza, recolhe e transfere os dados de diagnóstico.  

## <a name="logartifact-paths"></a>Caminhos de registo/artefacto
Seguem-se os caminhos para alguns registos de importantes e artefactos. Fazemos referência a essas informações em todo o resto do documento.

### <a name="azure-cloud-services"></a>Cloud Services do Azure
| Artefacto | Caminho |
| --- | --- |
| **Ficheiro de configuração de diagnósticos do Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Ficheiros de registo** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Armazenamento local para dados de diagnóstico** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **Ficheiro de configuração do agente de monitorização** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Pacote de extensão de diagnóstico do Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Caminho de utilitário de coleção de registo** | %SystemDrive%\Packages\GuestAgent\ |
| **Ficheiro de registo de MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Máquinas virtuais
| Artefacto | Caminho |
| --- | --- |
| **Ficheiro de configuração de diagnósticos do Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Ficheiros de registo** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Armazenamento local para dados de diagnóstico** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Ficheiro de configuração do agente de monitorização** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Ficheiro de estado** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Pacote de extensão de diagnóstico do Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Caminho de utilitário de coleção de registo** | C:\WindowsAzure\Packages |
| **Ficheiro de registo de MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Dados de métrica não aparecem no portal do Azure
Diagnóstico do Azure fornece dados métricos que podem ser apresentados no portal do Azure. Se tiver problemas a ver os dados no portal, verifique o WADMetrics\* tabela na conta de armazenamento do diagnóstico do Azure para ver se os registros de métrica correspondentes estão lá.

Aqui, o **PartitionKey** da tabela é o conjunto de dimensionamento de máquina virtual, máquina virtual ou ID de recurso. **RowKey** é o nome da métrica (também conhecido como o nome de contador de desempenho).

Se o ID de recurso está incorreto, verifique **diagnóstico** **configuração** > **métricas** > **ResourceId**para ver se o ID de recurso está definido corretamente.

Se não houver dados para a métrica específica, verifique **configuração de diagnósticos** > **PerformanceCounter** para ver se a métrica (contador de desempenho) está incluída. Podemos habilitar os seguintes contadores por padrão:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- Aplicativos de \ASP.NET (__Total__) \Requests/Sec
- Aplicativos de \ASP.NET (__Total__) \Errors/seg
- \ASP.NET\Requests colocados em fila
- \ASP.NET\Requests rejeitado
- \Processor(w3wp)\% de tempo do processador
- Bytes de \Private \Process (w3wp)
- \Process(WaIISHost)\% Processor Time
- Bytes de \Private \Process (WaIISHost)
- \Process(WaWorkerHost)\% Processor Time
- \Process(WaWorkerHost)\Private Bytes
- \Memory\Page stránkování/s
- \.Memória de NET CLR (_Global_)\% Time no GC
- Escrita de \Disk \LogicalDisk (c) Bytes/seg
- \Disk \LogicalDisk (c) Bytes lidos/seg
- Escrita de \Disk \LogicalDisk (d) Bytes/seg
- \Disk \LogicalDisk (d) Bytes lidos/seg

Se a configuração está definida corretamente, mas não pode ver os dados de métrica, utilize as seguintes diretrizes para ajudar a resolver.


## <a name="azure-diagnostics-is-not-starting"></a>Diagnóstico do Azure não está a iniciar
Para obter informações sobre por que o diagnóstico do Azure falhou ao iniciar, consulte a **DiagnosticsPluginLauncher.log** e **DiagnosticsPlugin.log** ficheiros na localização de ficheiros de registo que foi fornecida anteriormente.

Se estes registos indicarem `Monitoring Agent not reporting success after launch`, significa que ocorreu uma falha de inicialização MonAgentHost.exe. Consulte os registos na localização que é indicado para `MonAgentHost log file` na secção anterior.

A última linha dos ficheiros de registo contém o código de saída.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Se encontrar um **negativo** código de saída, consulte a [tabela de códigos de saída](#azure-diagnostics-plugin-exit-codes) no [faz referência a seção](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Dados de diagnóstico não estão conectados ao armazenamento do Azure
Determine se nenhum dos dados é apresentada ou alguns dos dados é apresentada.

### <a name="diagnostics-infrastructure-logs"></a>Registos de infraestruturas de diagnósticos
Registos do diagnóstico todos os erros nos logs de infraestrutura de diagnósticos. Certifique-se de que ativou a [registos de captura da infraestrutura de diagnósticos na sua configuração](#how-to-check-diagnostics-extension-configuration). Em seguida, pode procurar rapidamente para quaisquer erros relevantes que são apresentados no `DiagnosticInfrastructureLogsTable` tabela na sua conta de armazenamento configurado.

### <a name="no-data-is-appearing"></a>Não existem dados é apresentada
A razão mais comum que os dados de eventos não aparecem em todos os é que as informações de conta de armazenamento são definidas incorretamente.

Solução: Corrija a configuração de diagnósticos e reinstalar o diagnóstico.

Se a conta de armazenamento está configurado corretamente acesso remoto na máquina e certifique-se de que *DiagnosticsPlugin.exe* e *MonAgentCore.exe* estão em execução. Se não estiver em execução, siga os passos em [diagnóstico do Azure não está a iniciar](#azure-diagnostics-is-not-starting).

Se estiver a executar os processos, aceda a [são dados introdução capturados localmente?](#is-data-getting-captured-locally) e siga as instruções incluídas.

Se isso não resolver o problema, tente:

1. Desinstalar o agente
2. Remover o diretório C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics
3. Instalar o agente novamente


### <a name="part-of-the-data-is-missing"></a>Parte dos dados está em falta
Se estiver a obter alguns dados, mas não todas, significa que o pipeline de coleção/transferência de dados está definido corretamente. Siga as subsecções aqui para limitar o problema.

#### <a name="is-the-collection-configured"></a>A coleção é configurada?
A configuração de diagnósticos contém instruções para um determinado tipo de dados a serem recolhidos. [Reveja a configuração](#how-to-check-diagnostics-extension-configuration) para verificar que apenas estiver procurando por dados que configurou para a coleção.

#### <a name="is-the-host-generating-data"></a>O anfitrião está a gerar dados?
- **Contadores de desempenho**: Abra o perfmon e verifique o contador.

- **Registos de rastreio**:  Remoto de acesso na VM e adicione um TextWriterTraceListener ao ficheiro de configuração da aplicação.  Consulte https://msdn.microsoft.com/library/sk36c28t.aspx para configurar o serviço de escuta de texto.  Certifique-se de que o `<trace>` elemento tem `<trace autoflush="true">`.<br />
Se não vir a ser gerados de registos de rastreio, consulte mais sobre os registos de rastreio em falta.

- **Rastreios ETW**: Acesso remoto para a VM e instalar PerfView.  No PerfView, execute **arquivo** > **comando de utilizador** > **escutar etwprovder1** > **etwprovider2**, e assim por diante. O **escutar** comando diferencia maiúsculas de minúsculas e não pode haver espaços entre a lista separada por vírgulas dos fornecedores ETW. Se não for possível executar o comando, pode selecionar o **Log** botão na parte inferior direita da ferramenta Perfview para ver o que tentou executar e que o resultado era.  Partindo do princípio de que a entrada está correta, uma nova janela será exibida. Em alguns segundos, começa a ver rastreios ETW.

- **Registos de eventos**: Acesso remoto na VM. Abra `Event Viewer`e, em seguida, certifique-se de que existem os eventos.

#### <a name="is-data-getting-captured-locally"></a>Dados é obter capturados localmente?
Em seguida, certifique-se de que os dados são introdução capturados localmente.
Os dados são armazenados localmente no `*.tsf` ficheiros no arquivo local para dados de diagnóstico. Diferentes tipos de registos coletados em diferentes `.tsf` ficheiros. Os nomes são semelhantes aos nomes de tabela no armazenamento do Azure.

Por exemplo, `Performance Counters` obter recolhidos no `PerformanceCountersTable.tsf`. Registos de eventos coletados no `WindowsEventLogsTable.tsf`. Utilize as instruções no [extração de registo de Local](#local-log-extraction) secção para abrir os ficheiros de recolha local e certifique-se de que vê-los, obtendo recolhidos no disco.

Se não vir registos introdução recolhidos localmente e já tiver verificado que o anfitrião está a gerar dados, provavelmente terá um problema de configuração. Reveja a configuração com cuidado.

Reveja também a configuração que foi gerada para MonitoringAgent MaConfig.xml. Certifique-se de que existe uma secção que descreve a origem de registo relevantes. Em seguida, certifique-se de que não é perdeu na tradução entre a configuração de diagnósticos e a configuração do agente de monitorização.

#### <a name="is-data-getting-transferred"></a>Estão a ser transferidos dados?
Se tiver verificado que os dados são introdução capturados localmente, mas ainda não o vir na sua conta de armazenamento, siga os passos seguintes:

- Certifique-se de que forneceu uma conta de armazenamento correto e que ainda não tiver transferido chaves de conta de armazenamento fornecida. Serviços Cloud do Azure, por vezes, podemos ver que as pessoas não os atualizar `useDevelopmentStorage=true`.

- Certifique-se de que a conta de armazenamento fornecida está correta. Certifique-se de que não tem restrições de rede que impedem os componentes de chegar aos pontos finais de armazenamento público. Uma forma de fazer isso é para acesso remoto no computador e, em seguida, tente escrever algo para a mesma conta de armazenamento por conta própria.

- Por fim, pode ver que falhas estão a ser reportadas pelo agente de monitorização. O agente de monitorização escreve os respetivos registos na `maeventtable.tsf`, que está localizado no arquivo local para dados de diagnóstico. Siga as instruções no [extração de registo de Local](#local-log-extraction) secção para abrir este ficheiro. Em seguida, tente determinar se existem `errors` que indicar falhas de leitura para escrita no armazenamento de ficheiros locais.

### <a name="capturing-and-archiving-logs"></a>Capturar e arquivamento de logs
Se estiver pensando em como contatar o suporte, é a primeira coisa de que eles podem pedir-lhe recolher registos a partir do seu computador. Pode poupar tempo ao fazer o que mesmo. Execute o `CollectGuestLogs.exe` utilitário no caminho de utilitário de coleção de registo. Gera um. zip registos do ficheiro com o todos relevantes para o Azure na mesma pasta.

## <a name="diagnostics-data-tables-not-found"></a>Não foram encontradas tabelas de dados de diagnóstico
As tabelas no armazenamento do Azure que realizar Eventos ETW são nomeadas com o código a seguir:

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Segue-se um exemplo:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Esse código gera quatro tabelas:

| Evento | Nome da tabela |
| --- | --- |
| provider=”prov1” &lt;Event id=”1” /&gt; |WADEvent + MD5("prov1") + "1" |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| provider=”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt; |WADdest2 |

## <a name="references"></a>Referências

### <a name="how-to-check-diagnostics-extension-configuration"></a>Como verificar a configuração da extensão de diagnóstico
A maneira mais fácil para verificar a configuração de extensão é ir para [Explorador de recursos do Azure](http://resources.azure.com), e, em seguida, vá para a máquina virtual ou na cloud de serviço onde a extensão de diagnóstico do Azure (IaaSDiagnostics / PaaDiagnostics) é.

Em alternativa, o ambiente de trabalho remoto para a máquina e ver o ficheiro de configuração de diagnósticos do Azure que é descrito na secção de caminho de artefactos de registo.

Em ambos os casos, procure **Microsoft.Azure.Diagnostics**e, em seguida, para o **xmlCfg** ou **WadCfg** campo.

Caso esteja pesquisando numa máquina virtual e o **WadCfg** campo está presente, significa que a configuração está no formato JSON. Se o **xmlCfg** campo está presente, significa que a configuração está em XML e é codificada em base64. Precisa [decodificá-la](https://www.bing.com/search?q=base64+decoder) para ver o XML carregados por diagnósticos.

Para a função de serviço em nuvem, se escolher a configuração do disco, os dados são codificados em base64, por isso terá [decodificá-la](https://www.bing.com/search?q=base64+decoder) para ver o XML carregados por diagnósticos.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Códigos de saída de plug-in de diagnóstico do Azure
O plug-in devolve os códigos de saída seguinte:

| Código de saída | Descrição |
| --- | --- |
| 0 |Êxito. |
| -1 |Erro genérico. |
| -2 |Não é possível carregar o ficheiro de rcf.<p>Este erro interno só deve acontecer se o iniciador de plug-in do agente convidado é invocado manualmente incorretamente na VM. |
| -3 |Não é possível carregar o ficheiro de configuração de diagnósticos.<p><p>Solução: Causado por um ficheiro de configuração não passar a validação do esquema. A solução é fornecer um ficheiro de configuração que está em conformidade com o esquema. |
| -4 |Outra instância do agente de monitorização, diagnóstico já está a utilizar o diretório recurso local.<p><p>Solução: Especifique um valor diferente para **LocalResourceDirectory**. |
| -6 |Tentativa de iniciador de plug-in do agente do convidado iniciar o diagnóstico com uma linha de comando inválida.<p><p>Este erro interno só deve acontecer se o iniciador de plug-in do agente convidado é invocado manualmente incorretamente na VM. |
| -10 |O plug-in de diagnóstico. exe saiu com uma exceção não processada. |
| -11 |O agente convidado não foi possível criar o processo responsável por iniciar e monitorizar o agente de monitorização.<p><p>Solução: Certifique-se de que os recursos de sistema suficientes estão disponíveis para iniciar processos novos.<p> |
| -101 |Argumentos inválidos ao chamar o plug-in de diagnóstico.<p><p>Este erro interno só deve acontecer se o iniciador de plug-in do agente convidado é invocado manualmente incorretamente na VM. |
| -102 |O processo de plug-in é não é possível inicializar em si.<p><p>Solução: Certifique-se de que os recursos de sistema suficientes estão disponíveis para iniciar processos novos. |
| -103 |O processo de plug-in é não é possível inicializar em si. Especificamente, não consegue criar o objeto logger.<p><p>Solução: Certifique-se de que os recursos de sistema suficientes estão disponíveis para iniciar processos novos. |
| -104 |Não é possível carregar o ficheiro de rcf fornecido pelo agente de convidado.<p><p>Este erro interno só deve acontecer se o iniciador de plug-in do agente convidado é invocado manualmente incorretamente na VM. |
| -105 |O plug-in de diagnóstico não é possível abrir o ficheiro de configuração de diagnósticos.<p><p>Este erro interno só deve acontecer se o plug-in de diagnóstico é invocado manualmente incorretamente na VM. |
| -106 |Não é possível ler o ficheiro de configuração de diagnósticos.<p><p>Causado por um ficheiro de configuração não passar a validação do esquema. <br><br>Solução: Fornece um ficheiro de configuração que está em conformidade com o esquema. Para obter mais informações, consulte [como verificar a configuração da extensão de diagnóstico](#how-to-check-diagnostics-extension-configuration). |
| -107 |A passagem de diretório de recursos para o agente de monitorização é inválida.<p><p>Este erro interno só deve acontecer se o agente de monitorização é invocado manualmente incorretamente na VM.</p> |
| -108 |Não é possível converter o ficheiro de configuração de diagnósticos no arquivo de configuração monitorização do agente.<p><p>Este erro interno apenas deve acontecer se o plug-in de diagnóstico manualmente é invocado com um arquivo de configuração inválido. |
| -110 |Erro de configuração gerais de diagnóstico.<p><p>Este erro interno apenas deve acontecer se o plug-in de diagnóstico manualmente é invocado com um arquivo de configuração inválido. |
| -111 |Não é possível iniciar o agente de monitorização.<p><p>Solução: Certifique-se de que os recursos de sistema suficientes estão disponíveis. |
| -112 |Erro geral |

### <a name="local-log-extraction"></a>Extração de registo de local
O agente de monitorização recolhe registos e artefatos como `.tsf` ficheiros. O `.tsf` ficheiro não é legível, mas pode convertê-la num `.csv` da seguinte forma:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Um novo ficheiro chamado `<relevantLogFile>.csv` é criado no mesmo caminho que correspondente `.tsf` ficheiro.

>[!NOTE]
> Apenas terá de executar este utilitário relativamente ao ficheiro .tsf principal (por exemplo, PerformanceCountersTable.tsf). Os ficheiros que acompanha este artigo (por exemplo, PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002.tsf e assim por diante) são processados automaticamente.

### <a name="more-about-missing-trace-logs"></a>Mais informações sobre a falta de registos de rastreio

>[!NOTE]
> As seguintes informações aplicam-se principalmente para serviços Cloud do Azure, a menos que tiver configurado o DiagnosticsMonitorTraceListener num aplicativo que está em execução na sua VM de IaaS.

- Certifique-se de que o **DiagnosticMonitorTraceListener** está configurado no Web. config ou App. config.  Este é configurado por padrão nos projetos de serviço de nuvem. No entanto, alguns clientes comentem-out, que faz com que as declarações de rastreio para não ser recolhidos pelo diagnóstico.

- Se os registos não são introdução escritos do **OnStart** ou **executar** método, certifique-se de que o **DiagnosticMonitorTraceListener** está no App. config do.  Por predefinição é o Web. config, mas que só se aplica ao código em execução dentro de w3wp.exe. Por isso, precisa-lo no App. config para capturar rastreios que estão em execução no WaIISHost.exe.

- Certifique-se de que está a utilizar **Diagnostics.Trace.TraceXXX** em vez de **Diagnostics.Debug.WriteXXX.** As declarações de depuração são removidas de uma compilação de versão.

- Certifique-se o código compilado, na verdade, tem de **Diagnostics.Trace linhas** (utilizar o Reflector, ildasm ou ILSpy para verificar). **Diagnostics.Trace** comandos são removidos do binário compilado, a menos que utilize o símbolo de compilação condicional de rastreio. Este é um problema comum que ocorre quando estiver usando o msbuild para criar um projeto.   

## <a name="known-issues-and-mitigations"></a>Problemas conhecidos e atenuações
Aqui está uma lista dos problemas conhecidos com atenuações conhecidos:

**1. dependência de .NET 4.5**

Extensão de diagnóstico do Windows Azure tem uma dependência de tempo de execução no framework .NET 4.5 ou posterior. No momento da escrita, todos os computadores aprovisionados para serviços Cloud do Azure, bem como todas as imagens oficiais do que se baseiam em máquinas virtuais do Azure, ter o .NET 4.5 ou posterior instalado.

É possível encontro uma situação em que tentar executar a extensão de diagnóstico do Windows Azure numa máquina que não tem o .NET 4.5 ou posterior. Isto acontece quando criar o seu computador a partir de uma imagem antiga ou um instantâneo ou quando traga seu próprio disco personalizada.

Isso geralmente se manifesta como um código de saída **255** durante a execução **DiagnosticsPluginLauncher.exe.** Ocorrerá falha devido à seguinte exceção não processada:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Atenuação:** Instale o .NET 4.5 ou posterior no seu computador.

**2. Dados de contadores de desempenho estão disponíveis no armazenamento, mas não está a mostrar no portal**

A experiência do portal nas máquinas virtuais mostra alguns contadores de desempenho por predefinição. Se não vir os contadores de desempenho, e sabe que os dados é gerados uma vez que está disponível no armazenamento, verifique o seguinte:

- Se os dados no armazenamento têm nomes de contadores em inglês. Se os nomes de contador não estão em inglês, o gráfico de métricas de portal não capaz de reconhecê-lo. **Atenuação**: Altere idioma da máquina para inglês para as contas do sistema. Para tal, selecione **painel de controlo** > **região** > **administração** > **as definições de cópia**. Em seguida, desmarque **contas de sistema e de tela de boas-vindas** para que o idioma personalizado não é aplicado para a conta do sistema.

- Se estiver a utilizar carateres universais (\*) em seus nomes de contador de desempenho, o portal não conseguir correlacionar o contador recolhido e configurado, quando os contadores de desempenho são enviados para o sink de armazenamento do Azure. **Atenuação**: Para se certificar-se de que pode utilizar carateres universais e fazer com o portal, expanda a (\*), encaminhar os contadores de desempenho para o [sink "Do Azure Monitor"](diagnostics-extension-schema.md#diagnostics-extension-111).

