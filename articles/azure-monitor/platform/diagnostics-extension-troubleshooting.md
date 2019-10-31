---
title: Solucionando problemas de extensão de Diagnóstico do Azure
description: Solucionar problemas ao usar o diagnóstico do Azure em máquinas virtuais do Azure, Service Fabric ou serviços de nuvem.
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 05/08/2019
ms.openlocfilehash: 24a2b8a3c190ed440684ea3aa0ab35ebbf93fca0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161968"
---
# <a name="azure-diagnostics-troubleshooting"></a>Solução de problemas Diagnóstico do Azure
Este artigo descreve as informações de solução de problemas relevantes para o uso de Diagnóstico do Azure. Para obter mais informações sobre o diagnóstico do Azure, consulte [diagnóstico do Azure visão geral](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Componentes lógicos
**Iniciador de plug-in de diagnóstico (DiagnosticsPluginLauncher. exe)** : inicia a extensão de diagnóstico do Azure. Serve como o processo de ponto de entrada.

**Plug-in de diagnóstico (DiagnosticsPlugin. exe)** : configura, inicia e gerencia o tempo de vida do agente de monitoramento. É o processo principal que é iniciado pelo iniciador.

**Agente de monitoramento (MonAgent \*. exe Processes)** : monitora, coleta e transfere os dados de diagnóstico.  

## <a name="logartifact-paths"></a>Caminhos de log/artefato
A seguir estão os caminhos para alguns logs e artefatos importantes. Nós nos referimos a essas informações em todo o restante do documento.

### <a name="azure-cloud-services"></a>Serviços Cloud do Azure
| Artefacto | Caminho |
| --- | --- |
| **Arquivo de configuração Diagnóstico do Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versão > \Config.txt |
| **Arquivos de log** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics \<version > \ |
| **Armazenamento local para dados de diagnóstico** | Pasta c:\resources\directory \<CloudServiceDeploymentID >. \<RoleName >. DiagnosticStore\WAD0107\Tables |
| **Arquivo de configuração do agente de monitoramento** | Pasta c:\resources\directory \<CloudServiceDeploymentID >. \<RoleName >. DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Pacote de extensão Diagnóstico do Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versão > |
| **Caminho do utilitário de coleta de log** | %SystemDrive%\Packages\GuestAgent\ |
| **Arquivo de log do MonAgentHost** | Pasta c:\resources\directory\<CloudServiceDeploymentID >.\<RoleName >. DiagnosticStore\WAD0107\Configuration\MonAgentHost. < seq_num >. log |

### <a name="virtual-machines"></a>Máquinas virtuais
| Artefacto | Caminho |
| --- | --- |
| **Arquivo de configuração Diagnóstico do Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<version > \RuntimeSettings |
| **Arquivos de log** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion > \ |
| **Armazenamento local para dados de diagnóstico** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion > \WAD0107\Tables |
| **Arquivo de configuração do agente de monitoramento** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion > \WAD0107\Configuration\MaConfig.xml |
| **Arquivo de status** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<version > \Status |
| **Pacote de extensão Diagnóstico do Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion >|
| **Caminho do utilitário de coleta de log** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **Arquivo de log do MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion > \WAD0107\Configuration\MonAgentHost. < seq_num >. log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Os dados da métrica não aparecem no portal do Azure
Diagnóstico do Azure fornece dados de métrica que podem ser exibidos no portal do Azure. Se você tiver problemas para ver os dados no portal, verifique a tabela de \* de WADMetrics na Diagnóstico do Azure conta de armazenamento para ver se os registros de métrica correspondentes estão lá.

Aqui, o **PartitionKey** da tabela é a ID de recurso, a máquina virtual ou o conjunto de dimensionamento de máquinas virtuais. **RowKey** é o nome da métrica (também conhecido como o nome do contador de desempenho).

Se a ID do recurso estiver incorreta, verifique a **configuração** de **diagnóstico**  > **métricas**  > **ResourceId** para ver se a ID do recurso está definida corretamente.

Se não houver dados para a métrica específica, verifique **configuração de diagnóstico**  > **PerformanceCounter** para ver se a métrica (contador de desempenho) está incluída. Habilitamos os seguintes contadores por padrão:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ ASP.NET aplicativos (__total__) \ Solicitações/s
- \ ASP.NET aplicativos (__total__) \Errors total/s
- \ASP.NET\Requests na fila
- \ASP.NET\Requests rejeitado
- \Processor (w3wp)\% tempo do processador
- \Process (w3wp) \Bytes privados bytes
- \Process (WaIISHost)\% tempo do processador
- \Process (WaIISHost) \Bytes privados bytes
- \Process (WaWorkerHost)\% tempo do processador
- \Process (WaWorkerHost) \Bytes privados bytes
- Falhas de \Memory\Page/s
- \.a memória .NET CLR (_global_)\% tempo no GC
- \LogicalDisk (C:) \Bytes de gravação de bytes/s
- \LogicalDisk (C:) \Bytes de leitura de bytes/s
- \LogicalDisk (D:) \Bytes de gravação de bytes/s
- \LogicalDisk (D:) \Bytes de leitura de bytes/s

Se a configuração estiver definida corretamente, mas você ainda não conseguir ver os dados da métrica, use as diretrizes a seguir para ajudá-lo a solucionar problemas.


## <a name="azure-diagnostics-is-not-starting"></a>Diagnóstico do Azure não está iniciando
Para obter informações sobre por que Diagnóstico do Azure falha ao iniciar, consulte os arquivos **DiagnosticsPluginLauncher. log** e **DiagnosticsPlugin. log** no local dos arquivos de log que foi fornecido anteriormente.

Se esses logs indicarem `Monitoring Agent not reporting success after launch`, isso significa que houve uma falha ao iniciar o MonAgentHost. exe. Examine os logs no local indicado para `MonAgentHost log file` na seção anterior.

A última linha dos arquivos de log contém o código de saída.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Se você encontrar um código de saída **negativo** , consulte a [tabela de código de saída](#azure-diagnostics-plugin-exit-codes) na [seção de referências](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Os dados de diagnóstico não são registrados no armazenamento do Azure
Determine se nenhum dos dados está aparecendo ou se alguns dos dados estão aparecendo.

### <a name="diagnostics-infrastructure-logs"></a>Logs de infraestrutura de diagnóstico
O diagnóstico registra todos os erros nos logs de infraestrutura de diagnóstico. Verifique se você habilitou a [captura de logs de infraestrutura de diagnóstico em sua configuração](#how-to-check-diagnostics-extension-configuration). Em seguida, você pode procurar rapidamente quaisquer erros relevantes que apareçam na tabela de `DiagnosticInfrastructureLogsTable` em sua conta de armazenamento configurada.

### <a name="no-data-is-appearing"></a>Nenhum dado está aparecendo
O motivo mais comum para que os dados de eventos não apareçam é que as informações da conta de armazenamento estão definidas incorretamente.

Solução: corrija sua configuração de diagnóstico e reinstale o diagnóstico.

Se a conta de armazenamento estiver configurada corretamente, acesse o acesso remoto na máquina e verifique se *DiagnosticsPlugin. exe* e *MonAgentCore. exe* estão em execução. Se eles não estiverem em execução, siga as etapas em [diagnóstico do Azure não está iniciando](#azure-diagnostics-is-not-starting).

Se os processos estiverem em execução, vá para [os dados estão sendo capturados localmente?](#is-data-getting-captured-locally) e siga as instruções.

Se isso não resolver o problema, tente:

1. Desinstalar o agente
2. Remover C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics de diretório
3. Instalar o agente novamente


### <a name="part-of-the-data-is-missing"></a>Parte dos dados está ausente
Se você estiver obtendo alguns dados, mas não todos, isso significa que o pipeline de coleta/transferência de dados está definido corretamente. Siga as subseções aqui para restringir o problema.

#### <a name="is-the-collection-configured"></a>A coleção está configurada?
A configuração de diagnóstico contém instruções para um determinado tipo de dados a serem coletados. [Examine sua configuração](#how-to-check-diagnostics-extension-configuration) para verificar se você está procurando apenas os dados que você configurou para a coleção.

#### <a name="is-the-host-generating-data"></a>O host está gerando dados?
- **Contadores de desempenho**: Abra o PerfMon e verifique o contador.

- **Logs de rastreamento**: acesso remoto à VM e adicione um TextWriterTraceListener ao arquivo de configuração do aplicativo.  Consulte https://msdn.microsoft.com/library/sk36c28t.aspx para configurar o ouvinte de texto.  Verifique se o elemento `<trace>` tem `<trace autoflush="true">`.<br />
Se você não vir os logs de rastreamento sendo gerados, consulte mais informações sobre logs de rastreamento ausentes.

- **Rastreamentos ETW**: acesso remoto à VM e instalar o Perfview.  No PerfView, execute o**comando** **File**  >  User  > **Listen etwprovder1**  > **etwprovider2**e assim por diante. O comando **Listen** diferencia maiúsculas de minúsculas e não pode haver espaços entre a lista separada por vírgulas de provedores de ETW. Se o comando não for executado, você poderá selecionar o botão **log** no canto inferior direito da ferramenta Perfview para ver o que tentou executar e qual foi o resultado.  Supondo que a entrada esteja correta, uma nova janela é exibida. Em alguns segundos, você começará a ver os rastreamentos de ETW.

- **Logs de eventos**: acesso remoto à VM. Abra `Event Viewer` e, em seguida, verifique se os eventos existem.

#### <a name="is-data-getting-captured-locally"></a>Os dados estão sendo capturados localmente?
Em seguida, verifique se os dados estão sendo capturados localmente.
Os dados são armazenados localmente em `*.tsf` arquivos no repositório local para dados de diagnóstico. Diferentes tipos de logs são coletados em diferentes `.tsf` arquivos. Os nomes são semelhantes aos nomes de tabela no armazenamento do Azure.

Por exemplo, `Performance Counters` ser coletados no `PerformanceCountersTable.tsf`. Os logs de eventos são coletados no `WindowsEventLogsTable.tsf`. Use as instruções na seção [extração de log local](#local-log-extraction) para abrir os arquivos da coleção local e verifique se eles estão sendo coletados no disco.

Se você não vir os logs sendo coletados localmente e já tiver verificado que o host está gerando dados, provavelmente terá um problema de configuração. Revise sua configuração com cuidado.

Examine também a configuração que foi gerada para MonitoringAgent MaConfig. xml. Verifique se há uma seção que descreve a origem de log relevante. Em seguida, verifique se ele não é perdido na conversão entre a configuração de diagnóstico e a configuração do agente de monitoramento.

#### <a name="is-data-getting-transferred"></a>Os dados estão sendo transferidos?
Se você verificou que os dados estão sendo capturados localmente, mas ainda não o vê em sua conta de armazenamento, execute as seguintes etapas:

- Verifique se você forneceu uma conta de armazenamento correta e se não transferiu as chaves para a conta de armazenamento determinada. Para os serviços de nuvem do Azure, às vezes vemos que as pessoas não atualizam `useDevelopmentStorage=true`.

- Verifique se a conta de armazenamento fornecida está correta. Verifique se você não tem restrições de rede que impeçam que os componentes atinjam pontos de extremidade de armazenamento público. Uma maneira de fazer isso é o acesso remoto ao computador e, em seguida, tentar gravar algo na mesma conta de armazenamento.

- Por fim, você pode verificar quais falhas estão sendo relatadas pelo agente de monitoramento. O agente de monitoramento grava seus logs no `maeventtable.tsf`, que está localizado no repositório local para dados de diagnóstico. Siga as instruções na seção [extração de log local](#local-log-extraction) para abrir esse arquivo. Em seguida, tente determinar se há `errors` que indicam falhas de leitura para arquivos locais gravando no armazenamento.

### <a name="capturing-and-archiving-logs"></a>Capturando e arquivando logs
Se você estiver pensando em entrar em contato com o suporte, a primeira coisa que eles podem pedir a você é coletar logs do seu computador. Você pode economizar tempo fazendo isso por conta própria. Execute o utilitário `CollectGuestLogs.exe` no caminho do utilitário de coleta de logs. Ele gera um arquivo. zip com todos os logs do Azure relevantes na mesma pasta.

## <a name="diagnostics-data-tables-not-found"></a>Tabelas de dados de diagnóstico não encontradas
As tabelas no armazenamento do Azure que contêm eventos ETW são nomeadas usando o seguinte código:

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
| Provider = "prov1" &lt;ID do evento = "1"/&gt; |WADEvent + MD5 ("prov1") + "1" |
| Provider = "prov1" &lt;ID do evento = "2" eventDestination = "dest1"/&gt; |WADdest1 |
| Provider = "prov1" &lt;eventos/&gt; |WADDefault + MD5 ("prov1") |
| Provider = "prov2" &lt;eventDestination de eventos = "dest2"/&gt; |WADdest2 |

## <a name="references"></a>Referências

### <a name="how-to-check-diagnostics-extension-configuration"></a>Como verificar a configuração da extensão de diagnóstico
A maneira mais fácil de verificar sua configuração de extensão é ir para [Azure Resource Explorer](http://resources.azure.com)e, em seguida, ir para a máquina virtual ou serviço de nuvem onde a extensão de diagnóstico do Azure (IaaSDiagnostics/PaaDiagnostics) é.

Como alternativa, a área de trabalho remota na máquina e examine o arquivo de configuração Diagnóstico do Azure descrito na seção caminho de artefatos de log.

Em ambos os casos, pesquise **Microsoft. Azure. Diagnostics**e, em seguida, para o campo **xmlCfg** ou **WadCfg** .

Se você estiver pesquisando em uma máquina virtual e o campo **WadCfg** estiver presente, isso significará que a configuração está no formato JSON. Se o campo **xmlCfg** estiver presente, significa que a configuração está em XML e é codificada em base64. Você precisa [decodificá-lo](https://www.bing.com/search?q=base64+decoder) para ver o XML que foi carregado pelo diagnóstico.

Para a função de serviço de nuvem, se você escolher a configuração do disco, os dados serão codificados em base64, portanto, você precisa [decodificá-los](https://www.bing.com/search?q=base64+decoder) para ver o XML que foi carregado pelo diagnóstico.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Diagnóstico do Azure códigos de saída do plug-in
O plug-in retorna os seguintes códigos de saída:

| Código de saída | Descrição |
| --- | --- |
| 0 |Êxito. |
| -1 |Erro genérico. |
| -2 |Não é possível carregar o arquivo RCF.<p>Esse erro interno deve ocorrer somente se o iniciador de plug-in do agente convidado for invocado manualmente incorretamente na VM. |
| -3 |Não é possível carregar o arquivo de configuração de diagnóstico.<p><p>Solução: causada por um arquivo de configuração que não está passando pela validação do esquema. A solução é fornecer um arquivo de configuração que esteja em conformidade com o esquema. |
| -4 |Outra instância do diagnóstico do agente de monitoramento já está usando o diretório de recursos local.<p><p>Solução: especifique um valor diferente para **LocalResourceDirectory**. |
| -6 |O iniciador de plug-in do agente convidado tentou iniciar o diagnóstico com uma linha de comando inválida.<p><p>Esse erro interno deve ocorrer somente se o iniciador de plug-in do agente convidado for invocado manualmente incorretamente na VM. |
| -10 |O plug-in de diagnóstico saiu com uma exceção sem tratamento. |
| -11 |O agente convidado não pôde criar o processo responsável por iniciar e monitorar o agente de monitoramento.<p><p>Solução: Verifique se há recursos do sistema suficientes disponíveis para iniciar novos processos.<p> |
| -101 |Argumentos inválidos ao chamar o plug-in de diagnóstico.<p><p>Esse erro interno deve ocorrer somente se o iniciador de plug-in do agente convidado for invocado manualmente incorretamente na VM. |
| -102 |O processo de plug-in não pode se inicializar.<p><p>Solução: Verifique se há recursos do sistema suficientes disponíveis para iniciar novos processos. |
| -103 |O processo de plug-in não pode se inicializar. Especificamente, não é possível criar o objeto do agente.<p><p>Solução: Verifique se há recursos do sistema suficientes disponíveis para iniciar novos processos. |
| -104 |Não é possível carregar o arquivo RCF fornecido pelo agente convidado.<p><p>Esse erro interno deve ocorrer somente se o iniciador de plug-in do agente convidado for invocado manualmente incorretamente na VM. |
| -105 |O plug-in de diagnóstico não pode abrir o arquivo de configuração de diagnóstico.<p><p>Esse erro interno só deve acontecer se o plug-in de diagnóstico for invocado manualmente incorretamente na VM. |
| -106 |Não é possível ler o arquivo de configuração de diagnóstico.<p><p>Causado por um arquivo de configuração que não está passando pela validação do esquema. <br><br>Solução: forneça um arquivo de configuração que esteja em conformidade com o esquema. Para obter mais informações, consulte [como verificar a configuração da extensão de diagnóstico](#how-to-check-diagnostics-extension-configuration). |
| -107 |A passagem do diretório de recursos para o agente de monitoramento é inválida.<p><p>Esse erro interno deve acontecer apenas se o agente de monitoramento for invocado manualmente incorretamente na VM.</p> |
| -108 |Não é possível converter o arquivo de configuração de diagnóstico no arquivo de configuração do agente de monitoramento.<p><p>Esse erro interno só deve acontecer se o plug-in de diagnóstico for invocado manualmente com um arquivo de configuração inválido. |
| -110 |Erro de configuração de diagnóstico geral.<p><p>Esse erro interno só deve acontecer se o plug-in de diagnóstico for invocado manualmente com um arquivo de configuração inválido. |
| -111 |Não é possível iniciar o agente de monitoramento.<p><p>Solução: Verifique se há recursos do sistema suficientes disponíveis. |
| -112 |Erro geral |

### <a name="local-log-extraction"></a>Extração de log local
O agente de monitoramento coleta logs e artefatos como arquivos de `.tsf`. O arquivo de `.tsf` não é legível, mas você pode convertê-lo em uma `.csv` da seguinte maneira:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Um novo arquivo chamado `<relevantLogFile>.csv` é criado no mesmo caminho que o arquivo de `.tsf` correspondente.

>[!NOTE]
> Você só precisa executar esse utilitário no arquivo. TSF principal (por exemplo, PerformanceCountersTable. TSF). Os arquivos que o acompanham (por exemplo, PerformanceCountersTables_\*\*001. TSF, PerformanceCountersTables_\*\*002. TSF e assim por diante) são processados automaticamente.

### <a name="more-about-missing-trace-logs"></a>Mais sobre logs de rastreamento ausentes

>[!NOTE]
> As informações a seguir se aplicam principalmente aos serviços de nuvem do Azure, a menos que você tenha configurado o DiagnosticsMonitorTraceListener em um aplicativo em execução em sua VM IaaS.

- Verifique se o **DiagnosticMonitorTraceListener** está configurado no Web. config ou app. config.  Isso é configurado por padrão em projetos de serviço de nuvem. No entanto, alguns clientes o comentam, o que faz com que as instruções Trace não sejam coletadas pelo diagnóstico.

- Se os logs não estiverem sendo gravados do método **OnStart** ou **Run** , verifique se o **DiagnosticMonitorTraceListener** está no app. config.  Por padrão, ele está no Web. config, mas isso se aplica somente ao código em execução no w3wp. exe. Portanto, você precisa dele em app. config para capturar rastreamentos que estão em execução no WaIISHost. exe.

- Verifique se você está usando **Diagnostics. Trace. TraceXXX** em vez de **Diagnostics. Debug. WriteXXX.** As instruções de depuração são removidas de uma compilação de versão.

- Verifique se o código compilado realmente tem as **linhas Diagnostics. Trace** (use reflector, ILDASM ou ILSpy para verificar). Os comandos **Diagnostics. Trace** são removidos do binário compilado, a menos que você use o símbolo de compilação condicional de rastreamento. Esse é um problema comum que ocorre quando você está usando o MSBuild para criar um projeto.   

## <a name="known-issues-and-mitigations"></a>Problemas conhecidos e mitigações
Aqui está uma lista de problemas conhecidos com mitigações conhecidas:

**1. dependência do .NET 4,5**

A extensão do Windows Diagnóstico do Azure tem uma dependência de tempo de execução no .NET 4,5 Framework ou posterior. No momento da gravação, todos os computadores provisionados para os serviços de nuvem do Azure, bem como todas as imagens oficiais baseadas em máquinas virtuais do Azure, têm o .NET 4,5 ou posterior instalado.

Ainda é possível encontrar uma situação em que você tente executar a extensão do Windows Diagnóstico do Azure em um computador que não tenha o .NET 4,5 ou posterior. Isso acontece quando você cria seu computador a partir de uma imagem ou instantâneo antigo, ou quando você coloca seu próprio disco personalizado.

Isso geralmente se manifesta como um código de saída **255** ao executar **DiagnosticsPluginLauncher. exe.** A falha ocorre devido à seguinte exceção sem tratamento:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Mitigação:** Instale o .NET 4,5 ou posterior em seu computador.

**2. os dados de contadores de desempenho estão disponíveis no armazenamento, mas não aparecem no portal**

A experiência do portal nas máquinas virtuais mostra determinados contadores de desempenho por padrão. Se você não vir os contadores de desempenho e souber que os dados estão sendo gerados porque estão disponíveis no armazenamento, verifique o seguinte:

- Se os dados no armazenamento têm nomes de contadores em inglês. Se os nomes dos contadores não estiverem em inglês, o gráfico de métrica do portal não conseguirá reconhecê-lo. **Mitigação**: altere o idioma do computador para inglês para contas do sistema. Para fazer isso, selecione **painel de controle**  > **região**  > **configurações de cópia**de  > **administrativas** . Em seguida, desmarque a **tela de boas-vindas e as contas do sistema** para que o idioma personalizado não seja aplicado à conta do sistema.

- Se você estiver usando curingas (\*) em seus nomes de contadores de desempenho, o portal não poderá correlacionar o contador configurado e coletado quando os contadores de desempenho forem enviados para o coletor de armazenamento do Azure. **Mitigação**: para ter certeza de que você pode usar curingas e fazer com que o portal expanda o (\*), encaminhe seus contadores de desempenho para o [coletor "Azure monitor"](diagnostics-extension-schema.md#diagnostics-extension-111).

