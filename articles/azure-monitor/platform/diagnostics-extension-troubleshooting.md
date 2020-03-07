---
title: Extensão de Diagnóstico saturado de Problemas
description: Problemas de resolução de problemas ao utilizar diagnósticos Azure em Máquinas Virtuais Azure, Tecido de Serviço ou Serviços cloud.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: 043369bd6112c4cac36539bbd764393d889439c0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384485"
---
# <a name="azure-diagnostics-troubleshooting"></a>Tiroteio de problemas em Azure Diagnostics
Este artigo descreve informações de resolução de problemas relevantes para a utilização de Diagnósticos Azure. Para obter mais informações sobre diagnósticos Azure, consulte a visão geral do [Azure Diagnostics.](diagnostics-extension-overview.md)

## <a name="logical-components"></a>Componentes lógicos
**Diagnóstico Plugin Launcher (DiagnosticsPluginLauncher.exe)** : Lança a extensão de Diagnóstico Azure. Serve como o processo de ponto de entrada.

**DiagnósticoPlugin (DiagnosticsPlugin.exe)** : Configures, lança e gere a vida útil do agente de monitorização. É o processo principal que é lançado pelo lançador.

**Monitor (MonAgent\*.exe processes)** : Monitores, recolhe e transfere os dados de diagnóstico.  

## <a name="logartifact-paths"></a>Caminhos de log/artefacto
Seguem-se os caminhos para alguns troncos e artefactos importantes. Referimo-nos a esta informação ao longo do resto do documento.

### <a name="azure-cloud-services"></a>Cloud Services do Azure
| Artefacto | Caminho |
| --- | --- |
| **Ficheiro de configuração de Diagnóstico Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versão>\Config.txt |
| **Ficheiros de registo** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versão>\ |
| **Loja local para dados de diagnóstico** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>. Loja de Diagnóstico\WAD0107\Tables |
| **Ficheiro de configuração do agente de monitorização** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>. DiagnosticStore\WAD0107\Configuração\MaConfig.xml |
| **Pacote de extensão azure diagnóstico** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<versão> |
| **Caminho de utilidade de recolha de log** | %SystemDrive%\Packages\GuestAgent\ |
| **Ficheiro de registo MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>. DiagnosticStore\WAD0107\Configuração\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Máquinas virtuais
| Artefacto | Caminho |
| --- | --- |
| **Ficheiro de configuração de Diagnóstico Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<versão>\RuntimeSettings |
| **Ficheiros de registo** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Loja local para dados de diagnóstico** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Ficheiro de configuração do agente de monitorização** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuração\MaConfig.xml |
| **Ficheiro de estado** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<versão>\Status |
| **Pacote de extensão azure diagnóstico** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Caminho de utilidade de recolha de log** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **Ficheiro de registo MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuração\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Dados métricos não aparecem no portal Azure
O Azure Diagnostics fornece dados métricos que podem ser exibidos no portal Azure. Se tiver problemas em ver os dados no portal, verifique a tabela\* WADMetrics na conta de armazenamento do Azure Diagnostics para ver se os registos métricos correspondentes existem e certifique-se de que o fornecedor de [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) Microsoft.Insights está registado.

Aqui, a **Chave de Partição** da tabela é o ID de recursos, máquina virtual ou conjunto de escala de máquina virtual. **RowKey** é o nome métrico (também conhecido como o nome do contador de desempenho).

Se o ID do recurso estiver incorreto, verifique a **configuração** do **diagnóstico** > **Métricas** > **ResourceId** para ver se o ID do recurso está corretamente definido.

Se não houver dados para a métrica específica, verifique a configuração de **diagnóstico > ** **PerformanceCounter** para ver se a métrica (contador de desempenho) está incluída. Ativamos os seguintes contadores por defeito:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ASP.NET Pedidos(Total)\Pedidos/Sec
- \ASP.NET Aplicações(__Total__de erros total/sec
- \ASP.NET\Pedidos em fila
- \ASP.NET\Pedidos rejeitados
- \Processador (w3wp)\% Tempo do Processador
- \Process(w3wp)\Private Bytes
- \Process (WaIISHost)\% Tempo de processador
- \Process (WaIISHost)\Bytes Privados
- \Process (WaWorkerHost)\% Tempo do Processador
- \Process (WaWorkerHost)\Bytes Privados
- \Memória\Falhas de página/seg
- \.NET CLR Memory(_Global_)\% Time in GC
- \LogicalDisk(C:)\Disk Write Bytes/seg
- \LogicalDisk(C:)\Disk Read Bytes/seg
- \LogicalDisk(D:)\Disk Write Bytes/seg
- \LogicalDisk(D:)\Disco Lido Bytes/seg

Se a configuração estiver corretamente definida, mas ainda não conseguir ver os dados métricos, utilize as seguintes diretrizes para o ajudar a resolver problemas.


## <a name="azure-diagnostics-is-not-starting"></a>O Azure Diagnostics não está a começar
Para obter informações sobre o porquê do Azure Diagnostics não ter começado, consulte os **ficheiros DiagnosticsPluginLauncher.log** e **DiagnosticsPlugin.log** no local dos ficheiros de registo que foi fornecido anteriormente.

Se estes registos indicarem `Monitoring Agent not reporting success after launch`, significa que houve uma falha no lançamento do MonAgentHost.exe. Veja os registos no local indicado para `MonAgentHost log file` na secção anterior.

A última linha dos ficheiros de registo contém o código de saída.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Se encontrar um código de saída **negativo,** consulte a tabela de [códigos](#azure-diagnostics-plugin-exit-codes) de saída na [secção Referências](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Os dados de diagnóstico não estão registados no Armazenamento Azure
Determine se nenhum dos dados está a aparecer ou alguns dos dados estão a aparecer.

### <a name="diagnostics-infrastructure-logs"></a>Registos de infraestruturas de diagnóstico
Diagnóstico regista todos os erros nos registos de infraestruturas de Diagnóstico. Certifique-se de que ativou a [captura de registos de infraestruturas de diagnóstico na sua configuração](#how-to-check-diagnostics-extension-configuration). Em seguida, pode procurar rapidamente quaisquer erros relevantes que apareçam na tabela `DiagnosticInfrastructureLogsTable` na sua conta de armazenamento configurada.

### <a name="no-data-is-appearing"></a>Não aparecem dados
A razão mais comum para que os dados do evento não apareçam é que a informação da conta de armazenamento é definida incorretamente.

Solução: Corrija a configuração de Diagnósticos e reinstale diagnósticos.

Se a conta de armazenamento estiver corretamente configurada, aceda à máquina à distância e verifique se o *DiagnosticsPlugin.exe* e o *MonAgentCore.exe* estão em execução. Se não estiverem a funcionar, siga os passos em [Azure Diagnostics não está a começar](#azure-diagnostics-is-not-starting).

Se os processos estiverem em execução, vá a [Dados capturados localmente?](#is-data-getting-captured-locally)

Se isto não resolver o problema, tente:

1. Desinstalar o agente
2. Remover o diretório C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics
3. Instalar o agente novamente


### <a name="part-of-the-data-is-missing"></a>Falta parte dos dados
Se estiver a obter alguns dados, mas não todos, significa que o pipeline de recolha/transferência de dados está corretamente definido. Siga as subsecções aqui para reduzir a questão.

#### <a name="is-the-collection-configured"></a>A coleção está configurada?
A configuração de Diagnóstico contém instruções para que um determinado tipo de dados seja recolhido. [Reveja a sua configuração](#how-to-check-diagnostics-extension-configuration) para verificar se está apenas à procura de dados que configurado para a recolha.

#### <a name="is-the-host-generating-data"></a>O hospedeiro está a gerar dados?
- **Contadores de desempenho**: Abra perfmon e verifique o balcão.

- **Registos**de rastreio : Acesso remoto ao VM e adicione um TextWriterTraceListener ao ficheiro config da aplicação.  Consulte https://msdn.microsoft.com/library/sk36c28t.aspx para configurar o ouvinte de texto.  Certifique-se de que o elemento `<trace>` tem `<trace autoflush="true">`.<br />
Se não vir os registos a serem gerados, consulte mais sobre os registos de rastreio em falta.

- **Vestígios ETW**: Acesso remoto ao VM e instala o PerfView.  No PerfView, execute **o** Comando de **Utilizador** > ficheiro > **Ouvir etwprovder1** > **etwprovider2**, e assim por diante. O comando **Listen** é sensível a casos, e não pode haver espaços entre a lista separada da vírcula dos fornecedores de ETW. Se o comando não funcionar, pode selecionar o botão **Log** na parte inferior direita da ferramenta Perfview para ver o que tentou executar e qual foi o resultado.  Assumindo que a entrada está correta, aparece uma nova janela. Em poucos segundos, começas a ver vestígios da ETW.

- **Registos de eventos**: Acesso remoto ao VM. Abra `Event Viewer`, e depois certifique-se de que os eventos existem.

#### <a name="is-data-getting-captured-locally"></a>Os dados estão a ser capturados localmente?
Em seguida, certifique-se de que os dados estão sendo capturados localmente.
Os dados são armazenados localmente em `*.tsf` ficheiros na loja local para dados de diagnóstico. Diferentes tipos de registos são recolhidos em ficheiros diferentes `.tsf`. Os nomes são semelhantes aos nomes de mesa no Armazém Azure.

Por exemplo, `Performance Counters` ser recolhido saem `PerformanceCountersTable.tsf`. Os registos de eventos são recolhidos em `WindowsEventLogsTable.tsf`. Utilize as instruções na secção local de extração de [registos](#local-log-extraction) para abrir os ficheiros de recolha locais e verificar se os vê a serem recolhidos no disco.

Se não vir registos a serem recolhidos localmente, e já tiver verificado que o anfitrião está a gerar dados, provavelmente tem um problema de configuração. Reveja cuidadosamente a sua configuração.

Reveja também a configuração gerada para monitoringAgent MaConfig.xml. Verifique se existe uma secção que descreve a fonte de registo relevante. Em seguida, verifique se não se perde na tradução entre a configuração do Diagnóstico e a configuração do monitor.

#### <a name="is-data-getting-transferred"></a>Os dados estão a ser transferidos?
Se verificou que os dados estão a ser capturados localmente, mas ainda não os vê na sua conta de armazenamento, tome os seguintes passos:

- Verifique se forneceu uma conta de armazenamento correta e que não revirou as chaves para a conta de armazenamento. Para os Serviços Azure Cloud, às vezes vemos que as pessoas não atualizam `useDevelopmentStorage=true`.

- Verifique se a conta de armazenamento fornecida está correta. Certifique-se de que não tem restrições de rede que impeçam que os componentes cheguem a pontos finais de armazenamento público. Uma maneira de fazer isso é aceder remotamente à máquina, e depois tentar escrever algo para a mesma conta de armazenamento.

- Finalmente, pode ver que falhas estão a ser reportadas pelo Agente de Monitorização. O agente de monitorização escreve os seus registos em `maeventtable.tsf`, que está localizado na loja local para obter dados de diagnóstico. Siga as instruções na secção local de [extração](#local-log-extraction) de registo seletiva para a abertura deste ficheiro. Em seguida, tente determinar se existem `errors` que indicam falhas na leitura de ficheiros locais escrevendo para armazenamento.

### <a name="capturing-and-archiving-logs"></a>Captura e arquivamento de troncos
Se está a pensar em contactar o suporte, a primeira coisa que lhe podem pedir é recolher registos da sua máquina. Podes poupar tempo fazendo isso sozinho. Executar o utilitário `CollectGuestLogs.exe` no caminho de utilidade de recolha log. Gera um ficheiro .zip com todos os registos Azure relevantes na mesma pasta.

## <a name="diagnostics-data-tables-not-found"></a>Tabelas de dados de diagnóstico não encontradas
As tabelas no armazenamento Azure que detêm eventos ETW são nomeadas usando o seguinte código:

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
| fornecedor="prov1" &lt;Event id="1" /&gt; |WADEvent+MD5 ("prov1")+"1" |
| fornecedor="prov1" &lt;Event id="2" eventDestination="dest1" /&gt; |WADdest1 |
| fornecedor="prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5 ("prov1") |
| fornecedor="prov2" &lt;Eventos DefaultDestination="dest2" /&gt; |WADdest2 |

## <a name="references"></a>Referências

### <a name="how-to-check-diagnostics-extension-configuration"></a>Como verificar a configuração da extensão de diagnóstico
A maneira mais fácil de verificar a configuração da extensão é ir ao [Azure Resource Explorer,](https://resources.azure.com)e depois ir para a máquina virtual ou serviço de nuvem onde está a extensão de Diagnóstico Azure (IaaSDiagnostics / PaaDiagnostics).

Alternativamente, ambiente de trabalho remoto na máquina e veja o ficheiro de configuração de diagnóstico sinuoso que está descrito na secção de caminhos de artefactos de Registo.

Em qualquer dos casos, procure **microsoft.Azure.Diagnostics,** e depois para o campo **xmlCfg** ou **WadCfg.**

Se estiver a pesquisar uma máquina virtual e o campo **WadCfg** estiver presente, significa que o config está no formato JSON. Se o campo **xmlCfg** estiver presente, significa que o config está em XML e está codificado com base64. Precisa [descodificá-lo](https://www.bing.com/search?q=base64+decoder) para ver o XML que foi carregado pela Diagnostics.

Para a função de serviço na nuvem, se escolher a configuração do disco, os dados são codificados por base64, por isso precisa [de descodificá-lo](https://www.bing.com/search?q=base64+decoder) para ver o XML que foi carregado por Diagnostics.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Códigos de saída de plugin saqueados da Azure Diagnostics
O plugin devolve os seguintes códigos de saída:

| Código de saída | Descrição |
| --- | --- |
| 0 |Êxito. |
| -1 |Erro genérico. |
| -2 |Incapaz de carregar o ficheiro RCF.<p>Este erro interno só deverá ocorrer se o lançador de plugins do agente convidado for invocado manualmente incorretamente no VM. |
| -3 |Não é possível carregar o ficheiro de configuração de Diagnóstico.<p><p>Solução: Causado por um ficheiro de configuração que não passa a validação do esquema. A solução é fornecer um ficheiro de configuração que cumpra o esquema. |
| -4 |Outra instância do agente de monitorização Diagnostics já está a usar o diretório de recursos locais.<p><p>Solução: Especificar um valor diferente para **o LocalResourceDirectory**. |
| -6 |O lançador de plugin saqueta do agente convidado tentou lançar diagnósticos com uma linha de comando inválida.<p><p>Este erro interno só deverá ocorrer se o lançador de plugins do agente convidado for invocado manualmente incorretamente no VM. |
| -10 |O plugin de Diagnóstico saiu com uma exceção não tratada. |
| -11 |O agente convidado não conseguiu criar o processo responsável pelo lançamento e monitorização do agente de monitorização.<p><p>Solução: Verifique se existem recursos suficientes do sistema para lançar novos processos.<p> |
| -101 |Argumentos inválidos ao ligar para o plugin de Diagnóstico.<p><p>Este erro interno só deverá ocorrer se o lançador de plugins do agente convidado for invocado manualmente incorretamente no VM. |
| -102 |O processo de plugin é incapaz de se inicializar.<p><p>Solução: Verifique se existem recursos suficientes do sistema para lançar novos processos. |
| -103 |O processo de plugin é incapaz de se inicializar. Especificamente, é incapaz de criar o objeto madeireiro.<p><p>Solução: Verifique se existem recursos suficientes do sistema para lançar novos processos. |
| -104 |Incapaz de carregar o ficheiro rcf fornecido pelo agente convidado.<p><p>Este erro interno só deverá ocorrer se o lançador de plugins do agente convidado for invocado manualmente incorretamente no VM. |
| -105 |O plugin de Diagnóstico não pode abrir o ficheiro de configuração de Diagnóstico.<p><p>Este erro interno só deverá ocorrer se o plugin de Diagnóstico for invocado manualmente incorretamente no VM. |
| -106 |Não é possível ler o ficheiro de configuração de Diagnósticos.<p><p>Causado por um ficheiro de configuração que não passa a validação do esquema. <br><br>Solução: Forneça um ficheiro de configuração que cumpra o esquema. Para mais informações, consulte [Como verificar a configuração](#how-to-check-diagnostics-extension-configuration)da extensão de diagnóstico . |
| -107 |O passe de diretório de recursos para o agente de monitorização é inválido.<p><p>Este erro interno só deverá ocorrer se o agente de monitorização for invocado manualmente incorretamente no VM.</p> |
| -108 |Incapaz de converter o ficheiro de configuração de Diagnóstico sintetizando o ficheiro de configuração do agente de monitorização.<p><p>Este erro interno só deverá ocorrer se o plugin diagnostics for manualmente invocado com um ficheiro de configuração inválido. |
| -110 |Erro de configuração de Diagnóstico Geral.<p><p>Este erro interno só deverá ocorrer se o plugin diagnostics for manualmente invocado com um ficheiro de configuração inválido. |
| -111 |Incapaz de ligar o agente de monitorização.<p><p>Solução: Verifique se existem recursos suficientes do sistema. |
| -112 |Erro geral |

### <a name="local-log-extraction"></a>Extração de tronco local
O agente de monitorização recolhe registos e artefactos como ficheiros `.tsf`. O ficheiro `.tsf` não é legível, mas pode convertê-lo numa `.csv` seguinte:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Um novo ficheiro chamado `<relevantLogFile>.csv` é criado no mesmo caminho que o ficheiro correspondente `.tsf`.

>[!NOTE]
> Basta executar esta utilidade contra o ficheiro principal .tsf (por exemplo, PerformanceCountersTable.tsf). Os ficheiros que acompanham (por exemplo, PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002.tsf, e assim por diante) são automaticamente processados.

### <a name="more-about-missing-trace-logs"></a>Mais sobre registos de vestígios em falta

>[!NOTE]
> As seguintes informações aplicam-se principalmente aos Serviços de Nuvem Azure, a menos que tenha configurado o DiagnosticsMonitorTraceListener numa aplicação que está a ser publicada no seu VM IaaS.

- Certifique-se de que o **DiagnosticMonitorTraceListener** está configurado no web.config ou app.config.  Isto é configurado por padrão em projetos de serviço na nuvem. No entanto, alguns clientes comentam-no, o que faz com que as declarações de vestígios não sejam recolhidas por diagnósticos.

- Se os registos não estiverem a ser escritos a partir do método **OnStart** ou **Run,** certifique-se de que o **DiagnosticMonitorTraceListener** está na aplicação.config.  Por padrão, está na web.config, mas isso aplica-se apenas ao código que funciona dentro do w3wp.exe. Por isso, precisa dele em app.config para capturar vestígios que estão a ser realizados em WaIISHost.exe.

- Certifique-se de que está a utilizar **diagnósticos.Trace.TraceXXX** em vez de **Diagnostics.Debug.WriteXXX.** As declarações do Debug são removidas de uma construção de libertação.

- Certifique-se de que o código compilado tem realmente as **linhas De Diagnóstico.Trace** (utilize Refletor, ildasm ou ILSpy para verificar). **Diagnósticos.Os** comandos de vestígios são removidos do binário compilado a menos que utilize o símbolo de compilação condicional TRACE. Este é um problema comum que ocorre quando se está a usar a msbuild para construir um projeto.   

## <a name="known-issues-and-mitigations"></a>Questões e mitigações conhecidas
Aqui está uma lista de questões conhecidas com mitigações conhecidas:

**1. .NET 4.5 dependência**

A extensão de diagnóstico do Windows Azure tem uma dependência de tempo de execução na estrutura .NET 4.5 ou posterior. No momento da escrita, todas as máquinas que estão aprovisionadas para os Serviços Azure Cloud, bem como todas as imagens oficiais baseadas em máquinas virtuais Azure, têm .NET 4.5 ou posteriormente instaladas.

Ainda é possível encontrar uma situação em que se tenta executar a extensão de diagnóstico do Windows Azure numa máquina que não tem .NET 4.5 ou mais tarde. Isto acontece quando cria si a partir de uma imagem ou instantâneo antigo, ou quando traz o seu próprio disco personalizado.

Isto geralmente manifesta-se como um código de saída **255** ao executar **DiagnosticsPluginLauncher.exe.** A falha ocorre devido à seguinte exceção não tratada:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Atenuação:** Instale .NET 4.5 ou mais tarde na sua máquina.

**2. Os dados dos contadores de desempenho estão disponíveis no armazenamento, mas não aparecem no portal**

A experiência do portal nas máquinas virtuais mostra certos contadores de desempenho por padrão. Se não vir os contadores de desempenho, e sabe que os dados estão a ser gerados porque está disponível no armazenamento, verifique o seguinte:

- Se os dados armazenados têm contranomes em inglês. Se os contranomes não estiverem em inglês, o gráfico métrico do portal não será capaz de reconhecê-lo. **Mitigação**: Mude a linguagem da máquina para inglês para as contas do sistema. Para tal, selecione **Painel** de Controlo > **Região** > **Definições** **administrativas** de cópia > . Em seguida, desmarque as contas de **ecrã e sistema de welcome** para que o idioma personalizado não seja aplicado na conta do sistema.

- Se estiver a utilizar wildcards (\*) nos seus nomes de contra-desempenho, o portal não poderá correlacionar o contador configurado e recolhido quando os contadores de desempenho forem enviados para o lavatório de Armazenamento Azure. **Mitigação**: Para se certificar de que pode utilizar wildcards e fazer com que o portal expanda o (\*), encaminhe os seus contadores de desempenho para o lavatório Do Monitor Azure.

