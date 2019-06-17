---
title: Como resolver problemas com o agente do Log Analytics para Windows | Documentos da Microsoft
description: Descreva os sintomas, causas e resolução de problemas mais comuns com o agente do Log Analytics para Windows no Azure Monitor.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2019
ms.author: magoedte
ms.openlocfilehash: afa4483677336e9a887908a8cccf9590eed27af3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120113"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Como resolver problemas com o agente do Log Analytics para Windows 

Este artigo disponibiliza ajuda a resolver problemas de erros, poderá deparar-se com o agente do Log Analytics para Windows no Azure Monitor e sugere possíveis soluções para resolvê-los.

Se nenhum destes passos resolver, também estão disponíveis os seguintes canais de suporte:

* Clientes com suporte Premier benefícios pode abrir um pedido de suporte com [Premier](https://premier.microsoft.com/).
* Os clientes com contratos de suporte do Azure, podem abrir um pedido de suporte [no portal do Azure](https://manage.windowsazure.com/?getsupport=true).
* Visite a página de comentários do Log Analytics para rever as ideias submetidas e bugs [ https://aka.ms/opinsightsfeedback ](https://aka.ms/opinsightsfeedback) ou um novo de ficheiros. 

## <a name="important-troubleshooting-sources"></a>Origens de resolução de problemas importantes

 Para ajudar na resolução de problemas relacionados com o agente do Log Analytics para Windows, o agente de registos de eventos para registo de eventos do Windows, especificamente em *de aplicações e Services\Operations Manager*.  

## <a name="connectivity-issues"></a>Problemas de conectividade

Se o agente está a comunicar através de um servidor proxy ou firewall, pode haver restrições em vigor a impedir a comunicação do computador de origem e o serviço do Azure Monitor. Se a comunicação é bloqueada, uma configuração incorreta, registo com uma área de trabalho pode falhar enquanto a tentar instalar o agente, configurar a agente após a instalação para reportar a uma área de trabalho adicional, a falha de comunicação do agente após o registo com êxito. Esta secção descreve os métodos para resolver problemas relacionados com este tipo de problema com o agente do Windows. 

Verifique se que a firewall ou proxy está configurado para permitir que as seguintes portas e URLs descritos na tabela seguinte. Também é de confirmar inspeção de HTTP não está ativada para o tráfego da web, como ele pode impedir um canal do TLS seguro entre o agente e o Azure Monitor.  

|Recursos do Agente|Portas |Direção |Inspeção de HTTPS direto|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.oms.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.blob.core.windows.net |Porta 443 |Saída|Sim |  
|*.azure-automation.net |Porta 443 |Saída|Sim |  

Para informações de firewall necessárias para o Azure Government, consulte [gestão do Azure Government](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Existem várias formas, pode verificar se o agente está a comunicar corretamente com o Azure Monitor.

- Ativar a [avaliação do Estado de funcionamento do agente do Azure Log Analytics](../insights/solution-agenthealth.md) na área de trabalho. A partir do dashboard do Estado de funcionamento do agente, ver o **contagem de agentes sem resposta** coluna para ver rapidamente se o agente está listado.  

- Execute a seguinte consulta para confirmar se que o agente está a enviar um heartbeat para a área de trabalho que está configurado para reportar a. Substitua <ComputerName> com o nome real da máquina.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Se o computador está a comunicar corretamente com o serviço, a consulta deverá devolver um resultado. Se a consulta não devolveu um resultado, verifique primeiro se que o agente é configurado para reportar para a área de trabalho correta. Se estiver configurado corretamente, avance para o passo 3 e procurar o registo de eventos do Windows para identificar se o agente está a registar o problema que poderá estar a impedi-lo de comunicar com o Azure Monitor.

- Outro método para identificar um problema de conectividade é ao executar o **TestCloudConnectivity** ferramenta. A ferramenta é instalada por predefinição com o agente na pasta *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*. A partir da linha de comandos elevada, navegue para a pasta e execute a ferramenta. A ferramenta devolve os resultados e destaques em que o teste falha (por exemplo, se ele estava relacionado a uma determinada porta/URL que foi bloqueada). 

    ![Resultados de execução da ferramenta de TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtro da *Operations Manager* registo de eventos por **origens de eventos** - *módulos do serviço de estado de funcionamento*, *HealthService*, e *o conector do serviço* e filtre por **nível do evento** *aviso* e *erro* para confirmar se ele já escreveu eventos a partir do tabela seguinte. Se forem, reveja os passos de resolução incluídos para cada evento possíveis.

    |ID do Evento |source |Descrição |Resolução |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Serviço de estado de funcionamento |Falha na ligação para o serviço do agente |Este erro pode ocorrer quando o agente não consegue comunicar diretamente ou através de um servidor de firewall/proxy para o serviço do Azure Monitor. Certifique-se de que as definições de proxy do agente ou que o firewall/proxy de rede permite o tráfego TCP do computador para o serviço.|
    |2138 |Módulos do serviço de estado de funcionamento |Proxy requer autenticação |Configure as definições de proxy de agente e especifique o nome de utilizador/palavra-passe necessária para autenticar com o servidor proxy. |
    |2129 |Módulos do serviço de estado de funcionamento |Falha na ligação/com falhas negociação de SSL |Verifique as definições de TCP/IP do adaptador de rede e as definições de proxy de agente.|
    |2127 |Módulos do serviço de estado de funcionamento |Falha de envio de dados recebido o código de erro |Se isso só acontece periodicamente durante o dia, apenas pode ser uma anomalia aleatória que pode ser ignorada. Monitor para compreender a frequência com que ele acontece. Caso isso aconteça, muitas vezes, ao longo do dia, primeiro verifique a configuração de rede e as definições de proxy. Se a descrição inclui código de erro HTTP 404 e é a primeira vez que o agente tentar enviar dados para o serviço, ele incluirá um 500 Erro com um código de 404 erro interna. 404 significa que não foi encontrado, que indica que a área de armazenamento para a nova área de trabalho ainda está a ser aprovisionada. Na próxima repetição, dados com êxito irão escrever para a área de trabalho conforme o esperado. Um erro HTTP 403 pode indicar um problema de credenciais ou permissão. Há mais informações, incluídas com o erro 403 para ajudar a resolver o problema.|
    |4000 |Conector do serviço |Falha na resolução de nome DNS |A máquina não foi possível resolver o endereço da Internet utilizado ao enviar dados para o serviço. Isso pode ser definições de resolução DNS no seu computador, as definições de proxy incorreta ou talvez um problema temporário de DNS com o seu fornecedor. Caso isso aconteça periodicamente, ele pode ser causado por um problema transitório relacionadas à rede.|
    |4001 |Conector do serviço |Falha na ligação ao serviço. |Este erro pode ocorrer quando o agente não consegue comunicar diretamente ou através de um servidor de firewall/proxy para o serviço do Azure Monitor. Certifique-se de que as definições de proxy do agente ou que o firewall/proxy de rede permite o tráfego TCP do computador para o serviço.|
    |4002 |Conector do serviço |O serviço devolveu o código de estado HTTP 403 em resposta a uma consulta. Contacte o administrador de serviços para o estado de funcionamento do serviço. A consulta será repetida mais tarde. |Este erro é escrito durante a fase de registro inicial do agente e verá uma URL semelhante ao seguinte: *https://<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. Um erro 403 significa proibida de código e pode ser causado por um ID de área de trabalho escrita incorretamente ou uma chave ou a dados e a hora é incorreta no computador. Se o tempo é + /-15 minutos da hora atual, em seguida, integração falha. Para corrigir este problema, atualize a data e/ou do fuso horário do seu computador Windows.|

## <a name="data-collection-issues"></a>Problemas da recolha de dados

Depois do agente está instalado e relatórios para sua área de trabalho configurada ou áreas de trabalho, poderá deixar de receber a configuração, a recolher ou desempenho de reencaminhamento, de logs ou outros dados para o serviço consoante o que está ativado e direcionado para o computador. É necessário determinar se:

- É um tipo de dados específico ou de todos os dados que não está disponíveis na área de trabalho?
- É o tipo de dados especificado por uma solução ou especificado como parte da configuração de recolha de dados da área de trabalho?
- Quantos computadores são afetados? É um único ou vários computadores que relatam a área de trabalho?
- Ele estava funcionando e parar num determinado momento do dia ou ele nunca foi recolhido? 
- A consulta de pesquisa de registo que está a utilizar é sintaticamente correta? 
- O agente nunca recebeu a configuração do Azure Monitor?

É o primeiro passo na resolução de problemas determinar se o computador está a enviar um evento de heartbeat.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Se a consulta devolve resultados, em seguida, terá de determinar se um tipo de dados específico não é recolhido e reencaminhado para o serviço. Isto pode ser causado pelo agente não receber configuração atualizada do serviço ou algum outro sintoma impedir que o agente a funcionar normalmente. Execute os seguintes passos para resolver.

1. Abra uma linha de comandos elevada no computador e reinicie o serviço de agente escrevendo `net stop healthservice && net start healthservice`.
2. Abra o *Operations Manager* registo de eventos e procure **IDs de eventos** *7023, 7024, 7025, 7028* e *1210* de **eventos origem** *HealthService*.  Estes eventos indicam que o agente está a receber com êxito configuração do Azure Monitor e que estão ativamente a monitorizar o computador. A descrição do evento para evento ID 1210 também irá especificar na última linha todas as soluções e informações que estão incluídas no âmbito da monitorização no agente.  

    ![Descrição de 1210 do ID do evento](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Se após vários minutos não vir os dados esperados na visualização ou os resultados da consulta, dependendo se está a visualizar os dados a partir de uma solução ou de resposta, do *Operations Manager* registo de eventos, procure **eventos origens** *HealthService* e *módulos do serviço de estado de funcionamento* e filtrar por **nível do evento** *aviso* e *Erro* para confirmar se ele já escreveu eventos da seguinte tabela.

    |ID do Evento |source |Descrição |Resolução |
    |---------|-------|------------|
    |8000 |HealthService |Este evento irá especificar se um fluxo de trabalho relacionados ao desempenho, eventos ou outro tipo de dados recolhidos não consegue reencaminhar para o serviço de ingestão à área de trabalho. | ID de evento 2136 de origem HealthService é escrito em conjunto com este evento e pode indicar o agente não consegue comunicar com o serviço, possivelmente devido a configuração incorreta das definições de proxy e de autenticação, indisponibilidade de rede ou a firewall da rede / proxy não permite tráfego TCP do computador para o serviço.| 
    |10102 e 10103 |Módulos do serviço de estado de funcionamento |Fluxo de trabalho não foi possível resolver a origem de dados. |Isto pode ocorrer se o contador de desempenho especificado ou a instância não existe no computador ou está incorretamente definida nas definições de dados de área de trabalho. Se se tratar de um utilizador especificado pelo [contador de desempenho](data-sources-performance-counters.md#configuring-performance-counters), certifique-se de que as informações especificadas está a seguir o formato correto e existe nos computadores de destino. |
    |26002 |Módulos do serviço de estado de funcionamento |Fluxo de trabalho não foi possível resolver a origem de dados. |Isto pode ocorrer se o registo de eventos do Windows especificado não existe no computador. Este erro pode ser ignorado com segurança se o computador não é esperado que este registo de eventos registado, caso contrário, se se tratar de um utilizador especificado pelo [registo de eventos](data-sources-windows-events.md#configuring-windows-event-logs), certifique-se de que as informações especificadas estão corretas. |

