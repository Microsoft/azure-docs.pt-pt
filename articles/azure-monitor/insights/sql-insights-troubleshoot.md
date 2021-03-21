---
title: Insights SQL de resolução de problemas (pré-visualização)
description: Resolução de problemas sql insights no Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 85a3505dd347b96036c28c85c089afa04e3e3bd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609832"
---
# <a name="troubleshooting-sql-insights-preview"></a>Insights SQL de resolução de problemas (pré-visualização)
Para resolver problemas de recolha de dados em insights SQL, verifique o estado da máquina de monitorização no **separador perfil 'Gerir'.** Este terá um dos seguintes estados:

- Recolha 
- Não colecionar 
- Recolha com erros 
 
Clique no **Estado** para perfurar para ver registos e mais detalhes, o que pode ajudá-lo a resolver o problema. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="Estado da máquina de monitorização":::

## <a name="not-collecting-state"></a>Não coletando estado 
A máquina de monitorização tem um estado de *não recolha* se não houver dados na *InsightsMetrics* para SQL nos últimos 10 minutos. 

Os insights SQL utilizam a seguinte consulta para recuperar esta informação:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Verifique se existem registos da Telegraf que ajudem a identificar a causa principal do problema. Se houver entradas de registo, pode clicar em *Não recolher* e verificar os registos e a resolução de informações de resolução de problemas para problemas comuns. 


Se não houver registos, deve verificar os registos da máquina virtual de monitorização para os seguintes serviços instalados por duas extensões de máquinas virtuais:

- Microsoft.Azure.monitor.AzureMonitorLinuxAgent 
  - Serviço: mdsd 
- Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension 
  - Serviço: wli 
  - Serviço: ms-telegraf 
  - Serviço: td-agent-bit-wli 
  - Registo de extensão para verificar falhas de instalação: /var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 



### <a name="wli-service-logs"></a>registos de serviço wli 

Registos de serviço: `/var/log/wli.log`

Para ver registos recentes: `tail -n 100 -f /var/log/wli.log`
 

Se vir o seguinte registo de erros, indica um problema com o serviço **mdsd.**

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```


### <a name="telegraf-service-logs"></a>Registos de serviço telegraf 

Registos de serviço: `/var/log/ms-telegraf/telegraf.log`

Para ver registos recentes: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log` Para ver registos de erros e avisos recentes: `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

 A configuração utilizada pela telegraf é gerada pelo serviço wli e colocada em: `/etc/ms-telegraf/telegraf.d/wli`
 
Se uma má configuração for gerada, o serviço ms-telegraf pode não ter sido iniciado, verifique se o serviço ms-telegraf está em funcionamento com o comando: `service ms-telegraf status`

Para ver mensagens de erro do serviço telegraf executá-lo manualmente com o seguinte comando: 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>registos de serviços mdsd 

Verifique [as limitações atuais](../agents/azure-monitor-agent-overview.md#current-limitations) para o agente Azure Monitor. 


Registos de serviço:  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

Para ver erros recentes: `tail -n 100 -f /var/log/mdsd.err`

 Se necessitar de contactar o suporte, recolha as seguintes informações: 

- Logins `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 
- Iniciar sessão `/var/log/waagent.log` 
- Logins `/var/log/mdsd*`
- Ficheiros em `/etc/mdsd.d/`
- Arquivo `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>Inválido monitorização da configuração da máquina virtual

Uma das causas do estado de *não recolha* é quando tem uma configuração de máquina virtual de monitorização inválida.  Segue-se a configuração padrão:

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

Esta configuração especifica os tokens de substituição a utilizar na configuração do perfil na sua máquina virtual de monitorização. Também permite que você faça referências a segredos do Azure Key Vault, para que você não tenha manter valores secretos em qualquer configuração, o que é fortemente recomendado.

#### <a name="secrets"></a>Segredos
Segredos são fichas cujos valores são recuperados no tempo de corrida de um Cofre de Chaves Azure. Um segredo é definido por um par de referências de Cofre chave e nome secreto. Isto permite ao Azure Monitor obter o valor dinâmico do segredo e usá-lo é referências config a jusante.

Pode definir quantos segredos necessários na configuração, incluindo segredos armazenados em Cofres-Chave separados.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

As permissões de acesso ao Cofre de Chaves são fornecidas a uma Identidade de Serviço Gerido na máquina virtual de monitorização. O Azure Monitor espera que o Cofre-Chave forneça pelo menos segredos para obter permissão para a máquina virtual. Pode ative-lo a partir do portal Azure, PowerShell, CLI ou modelo de Gestor de Recursos.

#### <a name="parameters"></a>Parâmetros
Os parâmetros são fichas que podem ser referenciadas na configuração do perfil através da templating JSON. Os parâmetros têm um nome e um valor. Os valores podem ser qualquer tipo JSON, incluindo objetos e matrizes. Um parâmetro é referenciado no perfil config usando o seu nome nesta convenção `.Parameters.<name>` .

Os parâmetros podem fazer referência a segredos no Cofre de Chaves usando a mesma convenção. Por exemplo, `sqlAzureConnections` faz referências ao segredo `telegrafPassword` utilizando a `$telegrafPassword` convenção.

No tempo de execução, todos os parâmetros e segredos serão resolvidos e fundidos com a configuração de perfil para construir a configuração real a ser usada na máquina.

> [!NOTE]
> Os nomes dos parâmetros de `sqlAzureConnections` , e são todos `sqlVmConnections` `sqlManagedInstanceConnections` necessários na configuração, mesmo que não tenha cordas de ligação, irá fornecer algumas delas.


## <a name="collecting-with-errors-state"></a>Recolha com erros estado
A máquina de monitorização estará no estado *Recolhendo com erros* se houver pelo menos um registo *InsightsMetrics,* mas também existem erros na tabela *Operação.*

Os insights SQL utilizam as seguintes consultas para recuperar esta informação:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```
Operation 
 | where OperationCategory == "WorkloadInsights" 
 | summarize Errors = countif(OperationStatus == 'Error') 
```

Para casos comuns, fornecemos conhecimentos de resolução de problemas na nossa visão de registos: 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Vista de registos de resolução de problemas.":::



## <a name="next-steps"></a>Passos seguintes

- Obtenha detalhes sobre [a ativação de insights SQL](sql-insights-enable.md).
