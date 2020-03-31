---
title: Problemas de resolução de problemas com agente de Log Analytics para Windows
description: Descreva os sintomas, causas e resolução para os problemas mais comuns com o agente Log Analytics para Windows in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 4112555347ce1d718375fbab3f166c6f2f5deeaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333513"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Como resolver problemas com o agente do Log Analytics para Windows 

Este artigo fornece erros de resolução de problemas que poderá experimentar com o agente Log Analytics para windows in Azure Monitor e sugere possíveis soluções para os resolver.

Se nenhuma destas etapas funcionar para si, também estão disponíveis os seguintes canais de apoio:

* Os clientes com benefícios de suporte Premier podem abrir um pedido de apoio com a [Premier.](https://premier.microsoft.com/)
* Os clientes com contratos de apoio Azure podem abrir um pedido de apoio [no portal Azure.](https://manage.windowsazure.com/?getsupport=true)
* Visite a página de Feedback do Log [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) Analytics para rever as ideias e bugs enviados ou arquivar um novo. 

## <a name="important-troubleshooting-sources"></a>Fontes importantes de resolução de problemas

 Para ajudar com problemas relacionados com o agente Log Analytics para windows, o agente regista eventos no Windows Event Log, especificamente no âmbito do *Application and Services\Operations Manager*.  

## <a name="connectivity-issues"></a>Problemas de conectividade

Se o agente estiver a comunicar através de um servidor de procuração ou firewall, pode haver restrições em vigor que impedem a comunicação do computador de origem e do serviço Azure Monitor. No caso de a comunicação estar bloqueada, devido a uma configuração errada, o registo com um espaço de trabalho pode falhar enquanto tenta instalar o agente ou configurar o agente pós-configuração para reportar a um espaço de trabalho adicional. A comunicação do agente pode falhar após o registo bem sucedido. Esta secção descreve os métodos para resolver este tipo de problemas com o agente Windows.

Verifique duas vezes se a firewall ou proxy está configurada para permitir as seguintes portas e URLs descritos na tabela seguinte. Confirme também que a inspeção HTTP não está ativada para o tráfego web, pois pode impedir um canal TLS seguro entre o agente e o Monitor Azure.  

|Recursos do Agente|Portas |Direção |Inspeção de HTTPS direto|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porto 443 |Saída|Sim |  
|*.oms.opinsights.azure.com |Porto 443 |Saída|Sim |  
|*.blob.core.windows.net |Porto 443 |Saída|Sim |  

Para obter informações sobre firewall necessárias para o Governo de Azure, consulte a [gestão do Governo azure.](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs) Se planeia utilizar o Trabalhador do Livro Híbrido Azure Automation para se ligar e registar-se no serviço automationpara utilizar livros de execução ou soluções de gestão no seu ambiente, deve ter acesso ao número de porta e aos URLs descritos na Configuração da sua rede para o Trabalhador do [Livro de Execução Híbrido.](../../automation/automation-hybrid-runbook-worker.md#network-planning) 

Existem várias formas de verificar se o agente está a comunicar com sucesso com o Monitor Azure.

- Ative a avaliação do Agente de [Saúde do Agente de Análise](../insights/solution-agenthealth.md) de Log Azure no espaço de trabalho. Do painel de saúde do agente, veja a **coluna de agentes sem resposta** para ver rapidamente se o agente está listado.  

- Execute a seguinte consulta para confirmar que o agente está enviando um batimento cardíaco para o espaço de trabalho a que está configurado para reportar. Substitua-o `<ComputerName>` pelo nome real da máquina.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Se o computador estiver a comunicar com sucesso com o serviço, a consulta deve devolver um resultado. Se a consulta não devolver um resultado, verifique primeiro que o agente está configurado para reportar ao espaço de trabalho correto. Se estiver configurado corretamente, proceda ao passo 3 e procure no Windows Event Log para identificar se o agente está a registar o problema que poderá estar a impedi-lo de comunicar com o Monitor Azure.

- Outro método para identificar um problema de conectividade é executando a ferramenta **TestCloudConnectivity.** A ferramenta é instalada por padrão com o agente na pasta *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent.Agent*. A partir de um pedido de comando elevado, navegue para a pasta e execute a ferramenta. A ferramenta devolve os resultados e os destaques onde o teste falhou (por exemplo, se estivesse relacionado com uma porta/URL específica que foi bloqueada). 

    ![Resultados de execução da ferramenta TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtre o registo do evento Do Gestor de *Operações* por **fontes** - de evento Módulos de Serviço de*Saúde,* *Serviço de Saúde*e *Conector* de Serviço e filtro por *Aviso* de **Nível de Evento** e *Erro* para confirmar se tem eventos escritos a partir da tabela seguinte. Se forem, reveja os passos de resolução incluídos para cada evento possível.

    |ID do Evento |Origem |Descrição |Resolução |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Serviço de Estado de Funcionamento |Ligação ao serviço do agente falhou |Este erro pode ocorrer quando o agente não pode comunicar diretamente ou através de um servidor firewall/proxy ao serviço Azure Monitor. Verifique as definições de procuração do agente ou se a firewall/proxy de rede permite o tráfego de TCP do computador para o serviço.|
    |2138 |Módulos de Serviço de Saúde |Proxy requer autenticação |Configure as definições de procuração do agente e especifique o nome de utilizador/senha necessário para autenticar com o servidor proxy. |
    |2129 |Módulos de Serviço de Saúde |Ligação falhada/negociação tLS falhada |Verifique as definições do adaptador de rede TCP/IP e as definições de procuração do agente.|
    |2127 |Módulos de Serviço de Saúde |Falha no envio de dados recebidocódigo de erro |Se acontecer periodicamente durante o dia, pode ser apenas uma anomalia aleatória que pode ser ignorada. Monitorize para entender com que frequência acontece. Se acontecer com frequência ao longo do dia, verifique primeiro a configuração da sua rede e as definições de procuração. Se a descrição incluir o código de erro HTTP 404 e for a primeira vez que o agente tenta enviar dados para o serviço, incluirá um erro de 500 com um código de erro interior 404. 404 significa não ser encontrada, o que indica que a área de armazenamento do novo espaço de trabalho ainda está a ser disponibilizada. Na próxima retentativa, os dados escreverão com sucesso para o espaço de trabalho como esperado. Um erro HTTP 403 pode indicar uma questão de permissão ou credenciais. Há mais informações incluídas com o erro 403 para ajudar a resolver o problema.|
    |4000 |Conector de serviço |Resolução de nome dNS falhou |A máquina não conseguiu resolver o endereço de Internet utilizado no envio de dados para o serviço. Isto pode ser definições de dns resolver na sua máquina, configurações de procuração incorretas ou talvez um problema temporário de DNS com o seu fornecedor. Se acontecer periodicamente, pode ser causado por um problema transitório relacionado com a rede.|
    |4001 |Conector de serviço |A ligação ao serviço falhou. |Este erro pode ocorrer quando o agente não pode comunicar diretamente ou através de um servidor firewall/proxy ao serviço Azure Monitor. Verifique as definições de procuração do agente ou se a firewall/proxy de rede permite o tráfego de TCP do computador para o serviço.|
    |4002 |Conector de serviço |O serviço devolveu o código de estado HTTP 403 em resposta a uma consulta. Consulte o administrador de serviço para a saúde do serviço. A consulta será novamente julgada mais tarde. |Este erro é escrito durante a fase inicial de registo do agente e verá um URL semelhante ao seguinte: *https://\<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. Um código de erro 403 significa proibido e pode ser causado por um ID ou chave do espaço de trabalho enevoado, ou os dados e o tempo estão incorretos no computador. Se o tempo for de +/- 15 minutos a partir do tempo atual, então o embarque falha. Para corrigir isto, atualize a data e/ou o fuso horário do seu computador Windows.|

## <a name="data-collection-issues"></a>Questões de recolha de dados

Depois de o agente ser instalado e reportar para o seu espaço de trabalho configurado ou espaços de trabalho, pode deixar de receber configuração, recolha ou reencaminhamento de desempenho, registos ou outros dados para o serviço, dependendo do que está ativado e direcionado para o computador. É necessário determinar se:

- É um tipo de dados específico ou todos os dados que não estão disponíveis no espaço de trabalho?
- O tipo de dados é especificado por uma solução ou especificado como parte da configuração de recolha de dados do espaço de trabalho?
- Quantos computadores são afetados? É um único ou vários computadores reportando ao espaço de trabalho?
- Estava a funcionar e parou numa determinada altura do dia, ou nunca foi recolhido? 
- A consulta de pesquisa de registo que está a usar é sintáticamente correta? 
- O agente já recebeu a sua configuração do Monitor Azure?

O primeiro passo na resolução de problemas é determinar se o computador está a enviar um evento de batimentocardíaco.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Se a consulta devolver os resultados, então terá de determinar se um determinado tipo de dados não é recolhido e encaminhado para o serviço. Isto pode ser causado pelo agente não receber a configuração atualizada do serviço, ou algum outro sintoma que impeça o agente de funcionar normalmente. Execute os seguintes passos para mais problemas.

1. Abra um pedido de comando elevado no computador `net stop healthservice && net start healthservice`e reinicie o serviço do agente digitando .
2. Abra o registo de eventos do Gestor de *Operações* e procure **iDs** *7023, 7024, 7025, 7028* e *1210* da fonte do **evento** *HealthService.*  Estes eventos indicam que o agente está a receber com sucesso a configuração do Monitor Azure e estão a monitorizar ativamente o computador. A descrição do evento para o id 1210 do evento também especificará na última linha todas as soluções e Insights que estão incluídos no âmbito de monitorização do agente.  

    ![Descrição do ID do evento 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Se, após vários minutos, não vir os dados esperados nos resultados da consulta ou visualização, dependendo se está a visualizar os dados a partir de uma solução ou Insight, a partir do registo de eventos do Gestor de *Operações,* procure **por fontes** de eventoS Módulos de *Serviço de Saúde* e Serviço de *Saúde* e filtre por *Aviso* e *Erro* de **Nível** de Evento para confirmar se tem eventos escritos a partir da tabela seguinte.

    |ID do Evento |Origem |Descrição |Resolução |
    |---------|-------|------------|
    |8000 |Serviço de Saúde |Este evento especificará se um fluxo de trabalho relacionado com desempenho, evento ou outro tipo de dados recolhido não é capaz de encaminhar para o serviço para ingestão para o espaço de trabalho. | O Id de evento 2136 da fonte HealthService é escrito juntamente com este evento e pode indicar que o agente não pode comunicar com o serviço, possivelmente devido a configurações de proxy e autenticação, falha de rede ou a firewall/proxy de rede não permite o tráfego de TCP do computador para o serviço.| 
    |10102 e 10103 |Módulos de Serviço de Saúde |O fluxo de trabalho não conseguiu resolver a fonte de dados. |Isto pode ocorrer se o contador ou instância de desempenho especificado não existir no computador ou estiver incorretamente definido nas definições de dados do espaço de trabalho. Se este for um [contador](data-sources-performance-counters.md#configuring-performance-counters)de desempenho especificado pelo utilizador, verifique se as informações especificadas estão a seguir o formato correto e existem nos computadores-alvo. |
    |26002 |Módulos de Serviço de Saúde |O fluxo de trabalho não conseguiu resolver a fonte de dados. |Isto pode ocorrer se o registo de eventos do Windows especificado não existir no computador. Este erro pode ser ignorado com segurança se não se espera que o computador tenha este registo de registo de eventos, caso contrário se se trata de um registo de [evento](data-sources-windows-events.md#configuring-windows-event-logs)especificado pelo utilizador, verifique se as informações especificadas estão corretas. |

