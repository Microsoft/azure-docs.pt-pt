---
title: Resolução de problemas Extensão de Diagnóstico Azure
description: Problemas de resolução de problemas ao utilizar diagnósticos Azure em Máquinas Virtuais Azure, Tecido de Serviço ou Cloud Services.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: 431b19595fbe2f5bc1f989e712c9c104af8e839b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711523"
---
# <a name="azure-diagnostics-troubleshooting"></a>Resolução de problemas do Diagnóstico do Azure
Este artigo descreve informações de resolução de problemas que são relevantes para a utilização de Azure Diagnostics. Para obter mais informações sobre os diagnósticos Azure, consulte [a visão geral do Azure Diagnostics](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Componentes lógicos
**Lançador plugin de diagnóstico (DiagnosticsPluginLauncher.exe)**: Lança a extensão Azure Diagnostics. Serve como o processo de ponto de entrada.

**Diagnósticos Plugin (DiagnosticsPlugin.exe)**: Configura, lança e gere a vida útil do agente de monitorização. É o principal processo que é lançado pelo lançador.

**Monitoring Agent (MonAgent \* .exe processos)**: Monitores, recolhes e transfere os dados de diagnóstico.  

## <a name="logartifact-paths"></a>Caminhos de log/artefacto
Seguem-se os caminhos para alguns importantes troncos e artefactos. Referimo-nos a esta informação ao longo do resto do documento.

### <a name="azure-cloud-services"></a>Cloud Services do Azure
| Artefacto | Caminho |
| --- | --- |
| **Arquivo de configuração de DiagnósticoS Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics \<version>\Config.txt |
| **Registar ficheiros** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Loja local para dados de diagnóstico** | C:\Recursos\Diretório \<CloudServiceDeploymentID> \<RoleName> . . Loja de Diagnóstico\WAD0107\Tabelas |
| **Ficheiro de configuração do agente de monitorização** | C:\Recursos\Diretório \<CloudServiceDeploymentID> . \<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Pacote de extensão Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Caminho de utilidade de recolha de registos** | %SystemDrive%\Packages\GuestAgent\ |
| **Ficheiro de log monAgentHost** | C:\Recursos\Diretório \<CloudServiceDeploymentID> \<RoleName> . . Loja de Diagnóstico\WAD0107\Configuração\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Máquinas virtuais
| Artefacto | Caminho |
| --- | --- |
| **Arquivo de configuração de DiagnósticoS Azure** | C:\Pacotes\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<version> \RuntimeSettings |
| **Registar ficheiros** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Loja local para dados de diagnóstico** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion> \WAD0107\Tabelas |
| **Ficheiro de configuração do agente de monitorização** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Ficheiro de estado** | C:\Pacotes\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<version> \Status |
| **Pacote de extensão Azure Diagnostics** | C:\Pacotes\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Caminho de utilidade de recolha de registos** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **Ficheiro de log monAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion> \WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Os dados métricos não aparecem no portal do Azure
O Azure Diagnostics fornece dados métricos que podem ser apresentados no portal Azure. Se tiver problemas em ver os dados no portal, verifique a tabela WADMetrics \* na conta de armazenamento Azure Diagnostics para ver se existem os registos métricos correspondentes e certifique-se de que o fornecedor de [recursos](../../azure-resource-manager/management/resource-providers-and-types.md) Microsoft.Insights está registado.

Aqui, a **PartitionKey** da tabela é o ID de recursos, máquina virtual ou conjunto de escala de máquina virtual. **RowKey** é o nome métrico (também conhecido como o nome do contador de desempenho).

Se o ID do recurso estiver incorreto, verifique **as métricas** de **configuração** de diagnóstico  >    >  **ResourceId** para ver se o ID do recurso está corretamente definido.

Se não houver dados para a métrica específica, verifique o **Diagnostics Configuration**  >  **PerformanceCounter** para ver se a métrica (contador de desempenho) está incluída. Ativamos os seguintes contadores por padrão:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ASP.NET Candidaturas(Total)\Pedidos/Sec
- \ASP.NET Aplicações(Total)\Erros Total/Sec
- \ASP.NET\Pedidos em fila
- \ASP.NET\Pedidos rejeitados
- \Tempo do processador (w3wp) \%
- \Processo(w3wp)\Bytes privados
- \Tempo do processador \WaIISHost) \%
- \Processo(WaIISHost)\Bytes privados
- \Tempo do processador \WaWorkerHost) \%
- \Processo(WaWorkerHost)\Bytes privados
- \Memória\Falhas de página/seg
- \.NET CLR Memory(_Global_) \% Tempo em GC
- \LogicalDisk(C:)\Disk Write Bytes/sec
- \LogicalDisk(C:)\Disk Read Bytes/sec
- \LogicalDisk(D:)\Disk Write Bytes/sec
- \LogicalDisk(D:)\Disk Read Bytes/sec

Se a configuração estiver corretamente definida, mas ainda não conseguir ver os dados métricos, utilize as seguintes diretrizes para o ajudar a resolver problemas.


## <a name="azure-diagnostics-is-not-starting"></a>O Diagnóstico do Azure não está a iniciar
Para obter informações sobre o motivo pelo qual o Azure Diagnostics não começou, consulte os **ficheiros DiagnosticsPluginLauncher.log** e **DiagnosticsPlugin.log ficheiros** no local dos ficheiros de registo que foram fornecidos anteriormente.

Se estes registos `Monitoring Agent not reporting success after launch` indicarem, significa que houve uma falha no lançamento MonAgentHost.exe. Veja os registos no local indicado `MonAgentHost log file` na secção anterior.

A última linha dos ficheiros de registo contém o código de saída.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Se encontrar um código de saída **negativo,** consulte a [tabela de códigos de saída](#azure-diagnostics-plugin-exit-codes) na secção [Referências](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Os dados de diagnóstico não são registados no Armazenamento do Microsoft Azure
Determine se nenhum dos dados está a aparecer ou se alguns dos dados estão a aparecer.

### <a name="diagnostics-infrastructure-logs"></a>Registos de infraestruturas de diagnóstico
O diagnóstico regista todos os erros nos registos de infraestruturas de Diagnóstico. Certifique-se de que ativou a [captura de registos de infraestruturas de Diagnóstico na sua configuração](#how-to-check-diagnostics-extension-configuration). Em seguida, pode procurar rapidamente quaisquer erros relevantes que apareçam `DiagnosticInfrastructureLogsTable` na tabela na sua conta de armazenamento configurada.

### <a name="no-data-is-appearing"></a>Não aparecem dados
A razão mais comum para que os dados do evento não apareçam é que a informação da conta de armazenamento é definida incorretamente.

Solução: Corrija a configuração de Diagnóstico e reinstale diagnósticos.

Se a conta de armazenamento estiver configurada corretamente, o acesso remoto à máquina e verifique se *DiagnosticsPlugin.exe* e *MonAgentCore.exe* estão a funcionar. Se não estiverem a correr, siga os passos em [Azure Diagnostics não está a começar](#azure-diagnostics-is-not-starting).

Se os processos estiverem a decorrer, vá a [dados de São ser capturados localmente?](#is-data-getting-captured-locally)

Se isto não resolver o problema, tente:

1. Desinstalar o agente
2. Remover diretório C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics
3. Volte a instalar o agente


### <a name="part-of-the-data-is-missing"></a>Falta parte dos dados.
Se estiver a obter alguns dados, mas não todos, significa que o pipeline de recolha/transferência de dados está definido corretamente. Siga as subsecções aqui para reduzir a questão.

#### <a name="is-the-collection-configured"></a>A coleção está configurada?
A configuração de Diagnóstico contém instruções para que um determinado tipo de dados seja recolhido. [Reveja](#how-to-check-diagnostics-extension-configuration) a sua configuração para verificar se está apenas à procura de dados que configura para a recolha.

#### <a name="is-the-host-generating-data"></a>O hospedeiro está a gerar dados?
- **Contadores de desempenho**: Abra o perfmon e verifique o balcão.

- **Registos de rastreios**: Acesso remoto ao VM e adicionar um TextWriterTraceListener ao ficheiro config da aplicação.  Consulte https://msdn.microsoft.com/library/sk36c28t.aspx para configurar o ouvinte de texto.  Certifique-se de que o `<trace>` elemento tem `<trace autoflush="true">` .<br />
Se não vir vestígios de registos a serem gerados, consulte mais sobre registos de vestígios em falta.

- **Vestígios da ETW**: Acesso remoto ao VM e instalação perfView.  No PerfView, executar o Comando do Utilizador **de**  >    >  **Ficheiros Ouvir etwprovder1**  >  **etwprovider2**, e assim por diante. O comando **Listen** é sensível a casos, e não pode haver espaços entre a lista separada por vírgula de fornecedores ETW. Se o comando não funcionar, pode selecionar o botão **Registar** no canto inferior direito da ferramenta Perfview para ver o que tentou executar e qual foi o resultado.  Assumindo que a entrada está correta, aparece uma nova janela. Em poucos segundos, começas a ver vestígios da ETW.

- **Registos de eventos:** Acesso remoto ao VM. Abra, `Event Viewer` e, em seguida, certifique-se de que os eventos existem.

#### <a name="is-data-getting-captured-locally"></a>Os dados estão a ser capturados localmente?
Em seguida, certifique-se de que os dados estão a ser capturados localmente.
Os dados são armazenados localmente em `*.tsf` ficheiros na loja local para dados de diagnóstico. Diferentes tipos de registos são recolhidos em `.tsf` diferentes ficheiros. Os nomes são semelhantes aos nomes de tabelas no Azure Storage.

Por exemplo, `Performance Counters` recolha-se em `PerformanceCountersTable.tsf` . Os registos de eventos são recolhidos em `WindowsEventLogsTable.tsf` . Utilize as instruções na secção local de [extração de registos](#local-log-extraction) para abrir os ficheiros de recolha local e verificar se os vê a serem recolhidos no disco.

Se não vir registos a serem recolhidos localmente e já tiver verificado que o anfitrião está a gerar dados, é provável que tenha um problema de configuração. Reveja cuidadosamente a sua configuração.

Reveja também a configuração gerada para monitoraragente MaConfig.xml. Verifique se existe uma secção que descreve a fonte de registo relevante. Verifique então se não se perde na tradução entre a configuração de Diagnóstico e a configuração do agente de monitorização.

#### <a name="is-data-getting-transferred"></a>Os dados estão a ser transferidos?
Se verificou que os dados estão a ser capturados localmente, mas ainda não os vê na sua conta de armazenamento, tome os seguintes passos:

- Verifique se forneceu uma conta de armazenamento correta e que não rolou as chaves para a conta de armazenamento dada. Para o Azure Cloud Services, às vezes vemos que as pessoas não atualizam `useDevelopmentStorage=true` .

- Verifique se a conta de armazenamento fornecida está correta. Certifique-se de que não tem restrições de rede que impeçam os componentes de chegar aos pontos finais de armazenamento público. Uma maneira de fazer isso é aceder remotamente à máquina, e depois tentar escrever algo para a mesma conta de armazenamento.

- Finalmente, pode ver que falhas estão a ser reportadas pelo agente de monitorização. O agente de monitorização escreve os seus `maeventtable.tsf` logins, que está localizado na loja local para dados de diagnóstico. Siga as instruções na secção local de [extração de registos](#local-log-extraction) para a abertura deste ficheiro. Em seguida, tente determinar se existem `errors` falhas de leitura para ficheiros locais que escrevem para armazenamento.

### <a name="capturing-and-archiving-logs"></a>Captação e arquivo de troncos
Se está a pensar em contactar o suporte, a primeira coisa que lhe podem pedir é que recolha registos da sua máquina. Podes poupar tempo fazendo isso sozinho. Executar o `CollectGuestLogs.exe` utilitário no caminho de utilidade de recolha de log. Gera um ficheiro .zip com todos os registos Azure relevantes na mesma pasta.

## <a name="diagnostics-data-tables-not-found"></a>Tabelas de dados de diagnóstico não encontradas
As tabelas de armazenamento Azure que detêm eventos ETW são nomeadas usando o seguinte código:

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
Este código gera quatro tabelas:

| Evento | Nome da tabela |
| --- | --- |
| fornecedor="prov1" &lt; Evento id="1" /&gt; |WADEvent+MD5 ("prov1")+"1" |
| fornecedor="prov1" &lt; Evento id="2" eventDestination="dest1" /&gt; |WADdest1 |
| fornecedor="prov1" &lt; DefaultEvents /&gt; |WADDefault+MD5 ("prov1") |
| fornecedor="prov2" &lt; DefaultEvents eventDestination="dest2" /&gt; |WADdest2 |

## <a name="references"></a>Referências

### <a name="how-to-check-diagnostics-extension-configuration"></a>Como verificar a configuração da extensão de Diagnóstico
A forma mais fácil de verificar a configuração da extensão é ir ao [Azure Resource Explorer](https://resources.azure.com), e depois ir para a máquina virtual ou serviço de nuvem onde está a extensão Azure Diagnostics (IaaSDiagnostics / PaaDiagnostics).

Alternativamente, o ambiente de trabalho remoto na máquina e veja o ficheiro de configuração de diagnóstico Azure que é descrito na secção de caminhos de artefactos de Log.

Em qualquer dos casos, procure por **Microsoft.Azure.Diagnostics** e, em seguida, para o campo **xmlCfg** ou **WadCfg.**

Se estiver a pesquisar numa máquina virtual e o campo **WadCfg** estiver presente, significa que o config está no formato JSON. Se o campo **xmlCfg** estiver presente, significa que o config está em XML e está codificado com base64. É preciso [descodificá-lo](https://www.bing.com/search?q=base64+decoder) para ver o XML que foi carregado por Diagnósticos.

Para a função de serviço de nuvem, se escolher a configuração do disco, os dados estão codificados com base64, pelo que é necessário [descodificá-lo](https://www.bing.com/search?q=base64+decoder) para ver o XML que foi carregado por Diagnósticos.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Códigos de saída plugin Azure Diagnostics
O plugin devolve os seguintes códigos de saída:

| Código de saída | Descrição |
| --- | --- |
| 0 |Com êxito. |
| -1 |Erro genérico. |
| -2 |Incapaz de carregar o ficheiro RCF.<p>Este erro interno só deverá ocorrer se o lançador de plugin do agente convidado for invocado manualmente incorretamente no VM. |
| -3 |Não é possível carregar o ficheiro de configuração de Diagnóstico.<p><p>Solução: Causada por um ficheiro de configuração que não passa na validação do esquema. A solução é fornecer um ficheiro de configuração que cumpra o esquema. |
| -4 |Outro caso do agente de monitorização Diagnostics já está a utilizar o diretório de recursos local.<p><p>Solução: Especificar um valor diferente para **o LocalResourceDirectory**. |
| -6 |O lançador de plugin do agente convidado tentou lançar Diagnósticos com uma linha de comando inválida.<p><p>Este erro interno só deverá ocorrer se o lançador de plugin do agente convidado for invocado manualmente incorretamente no VM. |
| -10 |O plugin de Diagnóstico sai com uma exceção não manipulada. |
| -11 |O agente convidado não foi capaz de criar o processo responsável pelo lançamento e monitorização do agente de monitorização.<p><p>Solução: Verifique se existem recursos suficientes do sistema para lançar novos processos.<p> |
| -101 |Argumentos inválidos ao chamar o plugin de Diagnóstico.<p><p>Este erro interno só deverá ocorrer se o lançador de plugin do agente convidado for invocado manualmente incorretamente no VM. |
| -102 |O processo plugin é incapaz de se inicializar.<p><p>Solução: Verifique se existem recursos suficientes do sistema para lançar novos processos. |
| -103 |O processo plugin é incapaz de se inicializar. Especificamente, é incapaz de criar o objeto do madeireiro.<p><p>Solução: Verifique se existem recursos suficientes do sistema para lançar novos processos. |
| -104 |Incapaz de carregar o ficheiro RCF fornecido pelo agente convidado.<p><p>Este erro interno só deverá ocorrer se o lançador de plugin do agente convidado for invocado manualmente incorretamente no VM. |
| -105 |O plugin diagnostics não pode abrir o ficheiro de configuração de Diagnóstico.<p><p>Este erro interno só deve ocorrer se o plugin de Diagnóstico for invocado manualmente incorretamente no VM. |
| -106 |Não é possível ler o ficheiro de configuração de Diagnóstico.<p><p>Causado por um ficheiro de configuração que não passa na validação do esquema. <br><br>Solução: Forneça um ficheiro de configuração que cumpra o esquema. Para obter mais informações, consulte [como verificar a configuração da extensão de diagnóstico](#how-to-check-diagnostics-extension-configuration). |
| -107 |O bilhete de recurso para o agente de monitorização é inválido.<p><p>Este erro interno só deve ocorrer se o agente de monitorização for invocado manualmente incorretamente no VM.</p> |
| -108 |Não é possível converter o ficheiro de configuração de Diagnóstico no ficheiro de configuração do agente de monitorização.<p><p>Este erro interno só deverá ocorrer se o plugin de Diagnóstico for invocado manualmente com um ficheiro de configuração inválido. |
| -110 |Erro de configuração de Diagnóstico Geral.<p><p>Este erro interno só deverá ocorrer se o plugin de Diagnóstico for invocado manualmente com um ficheiro de configuração inválido. |
| -111 |Incapaz de ligar o agente de monitorização.<p><p>Solução: Verifique se existem recursos suficientes do sistema. |
| -112 |Erro geral |

### <a name="local-log-extraction"></a>Extração de registo local
O agente de monitorização recolhe registos e artefactos como `.tsf` ficheiros. O `.tsf` ficheiro não é legível, mas pode convertê-lo em uma `.csv` seguinte:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Um novo ficheiro chamado `<relevantLogFile>.csv` é criado no mesmo caminho que o ficheiro `.tsf` correspondente.

>[!NOTE]
> Basta executar este utilitário com o ficheiro principal .tsf (por exemplo, PerformanceCountersTable.tsf). Os ficheiros que acompanham (por exemplo, PerformanceCountersTables_ \* \* 001.tsf, PerformanceCountersTables_ \* \* 002.tsf, e assim por diante) são processados automaticamente.

### <a name="more-about-missing-trace-logs"></a>Mais sobre registos de vestígios em falta

>[!NOTE]
> As seguintes informações aplicam-se principalmente aos Serviços de Nuvem Azure, a menos que tenha configurado o DiagnosticsMonitorTraceListener numa aplicação que está a ser executada no seu IaaS VM.

- Certifique-se de que o **DiagnosticMonitorTraceListener** está configurado no web.config ou app.config.  Isto é configurado por padrão em projetos de serviço em nuvem. No entanto, alguns clientes comentam-no, o que faz com que as declarações de traços não sejam recolhidas através de diagnósticos.

- Se os registos não estiverem a ser escritos a partir do método **OnStart** ou **Run,** certifique-se de que o **DiagnosticMonitorTraceListener** está na app.config.  Por predefinição está na web.config, mas isso só se aplica ao código que funciona dentro de w3wp.exe. Por isso, precisas dele em app.config para capturar vestígios que estão a correr em WaIISHost.exe.

- Certifique-se de que está a utilizar **Diagnósticos.TraceXXX** em vez de **Diagnostics.Debug.WriteXXX.** As declarações de Debug são removidas de uma construção de libertação.

- Certifique-se de que o código compilado tem efetivamente as **linhas Diagnostics.Trace** (utilize refletor, ildasm ou ILSpy para verificar). **Os comandos de rastreio.Os** comandos trace são removidos do binário compilado, a menos que utilize o símbolo de compilação condicional TRACE. Este é um problema comum que ocorre quando se está a usar a msbuild para construir um projeto.   

## <a name="known-issues-and-mitigations"></a>Questões e mitigações conhecidas
Aqui está uma lista de questões conhecidas com mitigações conhecidas:

**1. .NET 4.5 dependência**

A extensão de diagnóstico do Windows Azure tem uma dependência de tempo de funcionamento na estrutura .NET 4.5 ou posterior. No momento da escrita, todas as máquinas que estão a ser adusídas para os Serviços Azure Cloud, bem como todas as imagens oficiais baseadas em máquinas virtuais Azure, têm .NET 4.5 ou posteriormente instaladas.

Ainda é possível encontrar uma situação em que tente executar a Extensão de Diagnóstico do Windows Azure numa máquina que não tenha .NET 4.5 ou posterior. Isto acontece quando cria a sua máquina a partir de uma imagem ou instantâneo antigo, ou quando traz o seu próprio disco personalizado.

Isto geralmente manifesta-se como um código de saída **255** quando executa **DiagnosticsPluginLauncher.exe.** A falha ocorre devido à seguinte exceção não manipulada:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Mitigação:** Instale .NET 4.5 ou posteriormente na sua máquina.

**2. Os dados dos contadores de desempenho estão disponíveis no armazenamento, mas não aparecem no portal**

A experiência do portal nas máquinas virtuais mostra determinados contadores de desempenho por padrão. Se não vir os contadores de desempenho e souber que os dados estão a ser gerados porque estão disponíveis no armazenamento, verifique o seguinte:

- Se os dados no armazenamento têm nomes de contador em inglês. Se os nomes de contadores não estiverem em inglês, o gráfico métrico do portal não poderá reconhecê-lo. **Mitigação**: Mude a língua da máquina para inglês para as contas do sistema. Para isso, selecione  >    >    >  **Definições** de Cópia Administrativa da Região do Painel de Controlo . Em seguida, desmarcar as contas do **ecrã de boas-vindas e** do sistema para que o idioma personalizado não seja aplicado na conta do sistema.

- Se estiver a utilizar wildcards \* nos nomes dos contadores de desempenho, o portal não poderá correlacionar o contador configurado e recolhido quando os contadores de desempenho forem enviados para a pia do Azure Storage. **Mitigação**: Para se certificar de que pode utilizar wildcards e fazer com que o portal expanda o ( \* ), encaminhe os seus contadores de desempenho para o lavatório do Azure Monitor.
