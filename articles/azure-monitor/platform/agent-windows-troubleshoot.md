---
title: Solucionar problemas com o agente do Log Analytics para Windows
description: Descreva os sintomas, as causas e a resolução dos problemas mais comuns com o agente de Log Analytics para Windows no Azure Monitor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 486c68cb32b5f4c8c8a18b21d1aee139ffda45bf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75397445"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Como solucionar problemas com o agente de Log Analytics para Windows 

Este artigo fornece ajuda para solucionar erros que podem ocorrer com o Log Analytics Agent para Windows no Azure Monitor e sugere possíveis soluções para resolvê-los.

Se nenhum destes passos resolver, também estão disponíveis os seguintes canais de suporte:

* Clientes com suporte Premier benefícios pode abrir um pedido de suporte com [Premier](https://premier.microsoft.com/).
* Os clientes com contratos de suporte do Azure, podem abrir um pedido de suporte [no portal do Azure](https://manage.windowsazure.com/?getsupport=true).
* Visite a página de comentários do Log Analytics para rever as ideias submetidas e bugs [ https://aka.ms/opinsightsfeedback ](https://aka.ms/opinsightsfeedback) ou um novo de ficheiros. 

## <a name="important-troubleshooting-sources"></a>Fontes de solução de problemas importantes

 Para auxiliar na solução de problemas relacionados ao agente do Log Analytics para Windows, o agente registra eventos no log de eventos do Windows, especificamente em *Application and Services\Operations Manager*.  

## <a name="connectivity-issues"></a>Problemas de ligação

Se o agente estiver se comunicando por meio de um servidor proxy ou firewall, pode haver restrições no local, impedindo a comunicação do computador de origem e do serviço de Azure Monitor. Caso a comunicação seja bloqueada, devido à configuração incorreta, o registro com um espaço de trabalho pode falhar ao tentar instalar o agente ou configurar o agente post-setup para relatar para um espaço de trabalho adicional. A comunicação do agente pode falhar após o registro bem-sucedido. Esta seção descreve os métodos para solucionar esse tipo de problema com o agente do Windows.

Verifique se o firewall ou o proxy está configurado para permitir as seguintes portas e URLs descritas na tabela a seguir. Também confirme se a inspeção HTTP não está habilitada para o tráfego da Web, pois ela pode impedir um canal TLS seguro entre o agente e o Azure Monitor.  

|Recursos do Agente|Portas |Direção |Inspeção de HTTPS direto|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.oms.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.blob.core.windows.net |Porta 443 |Saída|Sim |  

Para obter informações de firewall necessárias para o Azure governamental, consulte [Gerenciamento do Azure governamental](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). Se você planeja usar o Hybrid Runbook Worker de automação do Azure para se conectar e se registrar no serviço de automação para usar runbooks ou soluções de gerenciamento em seu ambiente, ele deve ter acesso ao número da porta e às URLs descritas em [configurar sua rede para o Hybrid runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Há várias maneiras de verificar se o agente está se comunicando com êxito com Azure Monitor.

- Habilite a [avaliação de integridade do agente de log Analytics do Azure](../insights/solution-agenthealth.md) no espaço de trabalho. No painel Integridade do Agente, exiba a coluna **contagem de agentes sem resposta** para ver rapidamente se o agente está listado.  

- Execute a consulta a seguir para confirmar se o agente está enviando uma pulsação para o espaço de trabalho para o qual ele está configurado para relatar. Substitua `<ComputerName>` pelo nome real do computador.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Se o computador estiver se comunicando com êxito com o serviço, a consulta deverá retornar um resultado. Se a consulta não retornou um resultado, primeiro verifique se o agente está configurado para relatar para o espaço de trabalho correto. Se estiver configurado corretamente, vá para a etapa 3 e pesquise o log de eventos do Windows para identificar se o agente está registrando em log Qual problema pode estar impedindo a comunicação com o Azure Monitor.

- Outro método para identificar um problema de conectividade é executar a ferramenta **TestCloudConnectivity** . A ferramenta é instalada por padrão com o agente na pasta *%systemroot%\Program Comuns\microsoft Monitoring Agent\Agent*. Em um prompt de comando com privilégios elevados, navegue até a pasta e execute a ferramenta. A ferramenta retorna os resultados e os destaques em que o teste falhou (por exemplo, se ele estava relacionado a uma porta/URL específica que foi bloqueada). 

    ![Resultados da execução da ferramenta TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtre o log de eventos *Operations Manager* por **fontes de evento** - *módulos de serviço de integridade*, *HealthService*e *conector de serviço* e filtre por *aviso* de **nível de evento** e *erro* para confirmar se ele gravou eventos da tabela a seguir. Se estiverem, examine as etapas de resolução incluídas para cada evento possível.

    |ID do Evento |Origem |Descrição |Resolução |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Serviço de Estado de Funcionamento |Falha na conexão com o serviço do agente |Esse erro pode ocorrer quando o agente não pode se comunicar diretamente ou por meio de um servidor de firewall/proxy para o serviço de Azure Monitor. Verifique as configurações de proxy do agente ou se o firewall de rede/proxy permite o tráfego TCP do computador para o serviço.|
    |2138 |Módulos de Serviço de Integridade |Proxy requer autenticação |Defina as configurações de proxy do agente e especifique o nome de usuário/senha necessários para autenticar com o servidor proxy. |
    |2129 |Módulos de Serviço de Integridade |Falha na conexão/falha na negociação SSL |Verifique as configurações de TCP/IP do adaptador de rede e as configurações de proxy do agente.|
    |2127 |Módulos de Serviço de Integridade |Falha ao enviar dados-código de erro recebido |Se isso ocorrer apenas periodicamente durante o dia, poderia ser uma anomalia aleatória que pode ser ignorada. Monitor para entender com que frequência isso acontece. Se ocorrer muitas vezes ao longo do dia, verifique primeiro as configurações de rede e de proxy. Se a descrição incluir o código de erro HTTP 404 e for a primeira vez que o agente tentar enviar dados para o serviço, ele incluirá um erro 500 com um código de erro de 404 interno. 404 significa não encontrado, que indica que a área de armazenamento do novo espaço de trabalho ainda está sendo provisionada. Na próxima tentativa, os dados serão gravados com êxito no espaço de trabalho conforme o esperado. Um erro HTTP 403 pode indicar uma permissão ou um problema de credenciais. Há mais informações incluídas com o erro 403 para ajudar a solucionar o problema.|
    |4000 |Conector do Serviço |Falha na resolução do nome DNS |O computador não pôde resolver o endereço da Internet usado ao enviar dados para o serviço. Isso pode ser as configurações do resolvedor de DNS em seu computador, configurações de proxy incorretas ou talvez um problema de DNS temporário com seu provedor. Se ocorrer periodicamente, isso pode ser causado por um problema transitório relacionado à rede.|
    |4001 |Conector do Serviço |Falha na conexão com o serviço. |Esse erro pode ocorrer quando o agente não pode se comunicar diretamente ou por meio de um servidor de firewall/proxy para o serviço de Azure Monitor. Verifique as configurações de proxy do agente ou se o firewall de rede/proxy permite o tráfego TCP do computador para o serviço.|
    |4002 |Conector do Serviço |O serviço retornou o código de status HTTP 403 em resposta a uma consulta. Verifique com o administrador de serviços a integridade do serviço. A consulta será repetida mais tarde. |Esse erro é gravado durante a fase de registro inicial do agente e você verá uma URL semelhante à seguinte: *https://\<workspaceid >. OMS. opinsights. Azure. com/AgentService. svc/AgentTopologyRequest*. Um código de erro 403 significa proibido e pode ser causado por uma ID ou chave de espaço de trabalho digitada incorretamente, ou os dados e a hora estão incorretos no computador. Se o tempo é + /-15 minutos da hora atual, em seguida, integração falha. Para corrigir isso, atualize a data e/ou o fuso horário do computador com Windows.|

## <a name="data-collection-issues"></a>Problemas de coleta de dados

Depois que o agente é instalado e é reportado para seu espaço de trabalho ou espaços de trabalho configurados, ele pode parar de receber a configuração, coletar ou encaminhar o desempenho, os logs ou outros dados para o serviço, dependendo do que está habilitado e direcionando o computador. É necessário determinar se:

- É um tipo de dados específico ou todos os dados que não estão disponíveis no espaço de trabalho?
- O tipo de dados é especificado por uma solução ou é especificado como parte da configuração da coleta de dados do espaço de trabalho?
- Quantos computadores são afetados? É um único ou vários computadores que se reportam ao espaço de trabalho?
- Ele estava funcionando e parou em uma hora específica do dia, ou ele nunca foi coletado? 
- A consulta de pesquisa de log está sendo usada sintaticamente correta? 
- O agente já recebeu sua configuração de Azure Monitor?

A primeira etapa na solução de problemas é determinar se o computador está enviando um evento de pulsação.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Se a consulta retornar resultados, você precisará determinar se um determinado tipo de dados não é coletado e encaminhado para o serviço. Isso pode ser causado pelo agente não recebendo a configuração atualizada do serviço ou por algum outro sintoma impedindo que o agente opere normalmente. Execute as etapas a seguir para solucionar problemas adicionais.

1. Abra um prompt de comando com privilégios elevados no computador e reinicie o serviço do Agent digitando `net stop healthservice && net start healthservice`.
2. Abra o log de eventos *Operations Manager* e procure por **IDs de evento** *7023, 7024, 7025, 7028* e *1210* na **origem do evento** *HealthService*.  Esses eventos indicam que o agente está recebendo a configuração com êxito do Azure Monitor e está monitorando o computador ativamente. A descrição do evento para a ID de evento 1210 também especificará na última linha todas as soluções e informações incluídas no escopo do monitoramento no agente.  

    ![Descrição do evento ID 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Se, após vários minutos, você não vir os dados esperados nos resultados da consulta ou na visualização, dependendo de se você estiver exibindo os dados de uma solução ou insight, no log de eventos *Operations Manager* , procure por **fontes de eventos** *HealthService* e *serviço de integridade módulos* e filtre por *aviso* de **nível de evento** e *erro* para confirmar se há eventos gravados da tabela a seguir.

    |ID do Evento |Origem |Descrição |Resolução |
    |---------|-------|------------|
    |8000 |HealthService |Esse evento especificará se um fluxo de trabalho relacionado a desempenho, evento ou outro tipo de dados coletado não puder encaminhar para o serviço para ingestão no espaço de trabalho. | A ID de evento 2136 da fonte HealthService é escrita junto com esse evento e pode indicar que o agente não pode se comunicar com o serviço, possivelmente devido a uma configuração incorreta do proxy e das configurações de autenticação, interrupção de rede ou o firewall/proxy de rede não permite o tráfego TCP do computador para o serviço.| 
    |10102 e 10103 |Módulos de Serviço de Integridade |O fluxo de trabalho não pôde resolver a fonte de dados. |Isso pode ocorrer se o contador de desempenho ou a instância especificada não existir no computador ou se estiver definido incorretamente nas configurações de dados do espaço de trabalho. Se esse for um [contador de desempenho](data-sources-performance-counters.md#configuring-performance-counters)especificado pelo usuário, verifique se as informações especificadas estão seguindo o formato correto e se existem nos computadores de destino. |
    |26002 |Módulos de Serviço de Integridade |O fluxo de trabalho não pôde resolver a fonte de dados. |Isso pode ocorrer se o log de eventos do Windows especificado não existir no computador. Esse erro pode ser ignorado com segurança se o computador não tiver esse log de eventos registrado; caso contrário, se esse for um [log de eventos](data-sources-windows-events.md#configuring-windows-event-logs)especificado pelo usuário, verifique se as informações especificadas estão corretas. |

