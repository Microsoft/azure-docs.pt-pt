---
title: Problemas de resolução de problemas com o agente Log Analytics para windows
description: Descreva os sintomas, as causas e a resolução para os problemas mais comuns com o agente Log Analytics para windows in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 4e2531d511193586ef4605cc3732968b6db28d9f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613518"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Como resolver problemas com o agente do Log Analytics para Windows 

Este artigo fornece falhas de resolução de problemas que pode experimentar com o agente Log Analytics para Windows no Azure Monitor e sugere possíveis soluções para os resolver.

Se nenhum destes passos funcionar para si, estão também disponíveis os seguintes canais de suporte:

* Os clientes com benefícios de suporte Premier podem abrir um pedido de apoio com [o Premier.](https://premier.microsoft.com/)
* Os clientes com contratos de apoio Azure podem abrir um pedido de apoio [no portal Azure.](https://manage.windowsazure.com/?getsupport=true)
* Visite a página de Feedback do Log Analytics para rever as ideias e bugs submetidos [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) ou arquivar uma nova. 

## <a name="log-analytics-troubleshooting-tool"></a>Ferramenta de resolução de problemas de log analytics

A Ferramenta de resolução de problemas do Agente de Ação de Registos Windows é uma coleção de scripts PowerShell projetados para ajudar a encontrar e diagnosticar problemas com o Agente De Análise de Registo. É automaticamente incluído com o agente aquando da instalação. Executar a ferramenta deve ser o primeiro passo para diagnosticar um problema.

### <a name="how-to-use"></a>Como utilizar
1. Abrir o pedido de PowerShell como administrador na máquina onde o Agente de Análise de Registo está instalado.
1. Navegue para o diretório onde a ferramenta está localizada.
   * `cd "C:\Program Files\Microsoft Monitoring Agent\Agent\Troubleshooter"`
1. Execute o script principal usando este comando:
   * `.\GetAgentInfo.ps1`
1. Selecione um cenário de resolução de problemas.
1. Siga as instruções na consola. (Nota: os passos de registo de vestígios requerem uma intervenção manual para parar a recolha de registos. Com base na reprodutibilidade da emissão, aguarde a duração do tempo e prima 's' para parar a recolha de registos e avançar para o passo seguinte).

   As localizações do ficheiro de resultados são registadas após a conclusão e uma nova janela do explorador que realça o mesmo é aberta.

### <a name="installation"></a>Instalação
A Ferramenta de Resolução de Problemas é automaticamente incluída após a instalação do Agente Desanalção de Registos 10.20.18053.0 e em diante.

### <a name="scenarios-covered"></a>Cenários cobertos
Abaixo está uma lista de cenários verificados pela Ferramenta de Resolução de Problemas:

- Agente que não reporte dados ou dados de batimentos cardíacos em falta
- Falha na implementação da extensão do agente
- Agente a despenhar-se
- Agente consumindo CPU/memória elevado
- Falhas de instalação/desinstalação
- Problema de registos personalizados
- Emissão do OMS Gateway
- Emissão de contadores de desempenho
- Recolha todos os registos

>[!NOTE]
>Por favor, execute a ferramenta de resolução de problemas quando tiver um problema. Ao abrir um bilhete, ter os registos inicialmente ajudará muito a nossa equipa de suporte a resolver o seu problema mais rapidamente.

## <a name="important-troubleshooting-sources"></a>Importantes fontes de resolução de problemas

 Para ajudar na resolução de problemas relacionados com o agente Log Analytics para windows, o agente regista eventos no Registo de Eventos do Windows, especificamente no *âmbito do Application and Services\Operations Manager*.  

## <a name="connectivity-issues"></a>Problemas de conectividade

Se o agente estiver a comunicar através de um servidor ou firewall proxy, pode haver restrições no local que impedem a comunicação do computador de origem e do serviço Azure Monitor. Caso a comunicação seja bloqueada, devido a uma configuração errada, o registo com um espaço de trabalho pode falhar enquanto tenta instalar o agente ou configurar o agente pós-configuração para reportar a um espaço de trabalho adicional. A comunicação do agente pode falhar após o registo bem sucedido. Esta secção descreve os métodos para resolver este tipo de problemas com o agente Windows.

Verifique duas vezes se a firewall ou o proxy estão configurados para permitir as seguintes portas e URLs descritos na tabela seguinte. Confirme também que a inspeção HTTP não está ativada para o tráfego web, uma vez que pode impedir um canal TLS seguro entre o agente e o Azure Monitor.  

|Recursos do Agente|Portas |Direção |Inspeção de HTTPS direto|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porto 443 |Saída|Yes |  
|*.oms.opinsights.azure.com |Porto 443 |Saída|Yes |  
|*.blob.core.windows.net |Porto 443 |Saída|Yes |  
|*.agentsvc.azure-automation.net |Porto 443 |Saída|Yes |  

Para obter informações sobre firewall necessárias para o Governo de Azure, consulte [a gestão do Governo Azure](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). Se pretender utilizar o Azure Automation Hybrid Runbook Worker para ligar e registar-se com o serviço de Automação para utilizar runbooks ou soluções de gestão no seu ambiente, deve ter acesso ao número de porta e aos URLs descritos em [Configurar a sua rede para o Trabalhador de Runbook Híbrido.](../../automation/automation-hybrid-runbook-worker.md#network-planning) 

Existem várias formas de verificar se o agente está a comunicar com sucesso com o Azure Monitor.

- Ativar a [avaliação do Agente Azure Analytics Health](../insights/solution-agenthealth.md) no espaço de trabalho. Do painel de instrumentos de saúde, veja a coluna **contagem de agentes sem resposta** para ver rapidamente se o agente está listado.  

- Faça a seguinte consulta para confirmar que o agente está a enviar um batimento cardíaco para o espaço de trabalho a que está configurado para se apresentar. `<ComputerName>`Substitua-a pelo nome real da máquina.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Se o computador estiver a comunicar com sucesso com o serviço, a consulta deverá devolver um resultado. Se a consulta não tiver retornado um resultado, verifique primeiro se o agente está configurado para reportar ao espaço de trabalho correto. Se estiver configurado corretamente, proceda ao passo 3 e procure no Registo de Eventos do Windows para identificar se o agente está a registar o problema que poderá estar a impedi-lo de comunicar com o Azure Monitor.

- Outro método para identificar um problema de conectividade é executando a ferramenta **TestCloudConnectivity.** A ferramenta é instalada por defeito com o agente na pasta *%SystemRoot%\Ficheiros de programa\Microsoft Monitoring Agent \Agent*. A partir de um pedido de comando elevado, navegue até à pasta e execute a ferramenta. A ferramenta devolve os resultados e os destaques onde o teste falhou (por exemplo, se estava relacionado com uma determinada porta/URL que estava bloqueada). 

    ![Resultados da execução da ferramenta TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtrar o registo de eventos *do Gestor de Operações* por **fontes de eventos**  -  *Health Service Modules,* *HealthService* e *Service Connector* e filtrar por *Aviso* e *Erro* **de Nível** de Evento para confirmar se tem eventos escritos a partir da tabela seguinte. Se forem, reveja as etapas de resolução incluídas para cada evento possível.

    |ID do Evento |Fonte |Descrição |Resolução |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Serviço de Estado de Funcionamento |A ligação ao serviço do agente falhou |Este erro pode ocorrer quando o agente não pode comunicar diretamente ou através de um servidor de firewall/proxy para o serviço Azure Monitor. Verifique as definições de procuração do agente ou que a firewall/proxy da rede permite o tráfego TCP do computador para o serviço.|
    |2138 |Módulos do Serviço de Saúde |Proxy requer autenticação |Configure as definições de procuração do agente e especifique o nome de utilizador/palavra-passe necessário para autenticar com o servidor proxy. |
    |2129 |Módulos do Serviço de Saúde |Ligação falhada/Negociação TLS falhada |Verifique as definições do adaptador de rede TCP/IP e as definições de procuração de agentes.|
    |2127 |Módulos do Serviço de Saúde |Falha no envio de dados recebidos código de erro |Se acontecer periodicamente durante o dia, pode ser apenas uma anomalia aleatória que pode ser ignorada. Monitorize para entender a frequência com que acontece. Se acontecer com frequência ao longo do dia, verifique primeiro a configuração da sua rede e as definições de procuração. Se a descrição incluir o código de erro HTTP 404 e for a primeira vez que o agente tenta enviar dados para o serviço, incluirá um erro de 500 com um código de erro interior 404. 404 significa não encontrado, o que indica que a área de armazenamento do novo espaço de trabalho ainda está a ser a provisionada. Na próxima repetição, os dados escreverão com sucesso para o espaço de trabalho como esperado. Um erro HTTP 403 pode indicar um problema de permissão ou credenciais. Há mais informações incluídas no erro 403 para ajudar a resolver o problema.|
    |4000 |Conector de serviço |Resolução de nome DNS falhou |A máquina não conseguiu resolver o endereço de Internet utilizado no envio de dados para o serviço. Isto pode ser configurações de resolver DNS na sua máquina, configurações de procuração incorretas ou talvez um problema temporário de DNS com o seu fornecedor. Se acontecer periodicamente, pode ser causado por uma questão transitória relacionada com a rede.|
    |4001 |Conector de serviço |A ligação ao serviço falhou. |Este erro pode ocorrer quando o agente não pode comunicar diretamente ou através de um servidor de firewall/proxy para o serviço Azure Monitor. Verifique as definições de procuração do agente ou que a firewall/proxy da rede permite o tráfego TCP do computador para o serviço.|
    |4002 |Conector de serviço |O serviço devolveu o código de estado HTTP 403 em resposta a uma consulta. Consulte o administrador de serviço para a saúde do serviço. A consulta será novamente julgada mais tarde. |Este erro está escrito durante a fase inicial de registo do agente e verá um URL semelhante ao seguinte: *https:// \<workspaceID> .oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. Um código de erro 403 significa proibido e pode ser causado por um ID ou chave do espaço de trabalho mal descrito, ou os dados e o tempo estão incorretos no computador. Se o tempo for de +/- 15 minutos da hora atual, então o embarque falha. Para corrigir isto, atualize a data e/ou o tempo de tempo do seu computador Windows.|

## <a name="data-collection-issues"></a>Questões de recolha de dados

Após a instalação do agente e reportar ao seu espaço de trabalho ou espaço de trabalho configurado, poderá deixar de receber a configuração, recolher ou encaminhar desempenhos, registos ou outros dados para o serviço, dependendo do que está ativado e direcionando o computador. É necessário determinar se:

- É um tipo de dados específico ou todos os dados que não estão disponíveis no espaço de trabalho?
- O tipo de dados é especificado por uma solução ou especificado como parte da configuração de recolha de dados do espaço de trabalho?
- Quantos computadores são afetados? É um único ou vários computadores reportando para o espaço de trabalho?
- Estava a funcionar e parou numa determinada hora do dia, ou nunca foi recolhido? 
- A consulta de pesquisa de registos está a usar sintáticamente correta? 
- O agente já recebeu a sua configuração do Azure Monitor?

O primeiro passo na resolução de problemas é determinar se o computador está a enviar um evento cardíaco.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Se a consulta retornar os resultados, então precisa determinar se um determinado tipo de dados não é recolhido e reencaminhado para o serviço. Isto pode ser causado pelo agente não receber uma configuração atualizada do serviço, ou algum outro sintoma que impeça o agente de funcionar normalmente. Execute os seguintes passos para uma maior resolução de problemas.

1. Abra uma solicitação de comando elevada no computador e reinicie o serviço de agente digitando `net stop healthservice && net start healthservice` .
2. Abra o registo de eventos *do Gestor de Operações* e procure **iDs** de eventos *7023, 7024, 7025, 7028* e *1210* da fonte de **evento** *HealthService.*  Estes eventos indicam que o agente está a receber com sucesso a configuração do Azure Monitor e estão a monitorizar ativamente o computador. A descrição do evento para o ID 1210 do evento também especificará na última linha todas as soluções e Insights que estão incluídas no âmbito de monitorização do agente.  

    ![ID do evento 1210 descrição](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Se ao fim de vários minutos não ver os dados esperados nos resultados ou visualização da consulta, dependendo se estiver a visualizar os dados a partir de uma solução ou insight, a partir do registo de eventos do Gestor de *Operações,* procure **por fontes de eventos** *HealthService* e Health Service *Modules* e filter por Event **Level** *Warning* and *Error* para confirmar se tem eventos escritos a partir da tabela seguinte.

    |ID do Evento |Fonte |Descrição |Resolução |
    |---------|-------|------------|
    |8000 |HealthService |Este evento especificará se um fluxo de trabalho relacionado com desempenho, evento ou outro tipo de dados recolhidos não é capaz de encaminhar para o serviço de ingestão para o espaço de trabalho. | O ID do evento 2136 da fonte HealthService é escrito juntamente com este evento e pode indicar que o agente não consegue comunicar com o serviço, possivelmente devido à configuração errada das definições de procuração e autenticação, à interrupção da rede ou à firewall/procuração da rede não permite o tráfego TCP do computador para o serviço.| 
    |10102 e 10103 |Módulos do Serviço de Saúde |O fluxo de trabalho não conseguiu resolver a fonte de dados. |Isto pode ocorrer se o contador de desempenho especificado ou a instância não existir no computador ou estiver incorretamente definido nas definições de dados do espaço de trabalho. Se este for um [contador](data-sources-performance-counters.md#configuring-performance-counters)de desempenho especificado pelo utilizador, verifique se as informações especificadas estão a seguir o formato correto e que existem nos computadores-alvo. |
    |26002 |Módulos do Serviço de Saúde |O fluxo de trabalho não conseguiu resolver a fonte de dados. |Isto pode ocorrer se o registo de eventos do Windows especificado não existir no computador. Este erro pode ser ignorado com segurança se não se espera que o computador tenha este registo de eventos registado, caso contrário, se se trata de um registo de [eventos](data-sources-windows-events.md#configuring-windows-event-logs)especificado pelo utilizador, verifique se as informações especificadas estão corretas. |
