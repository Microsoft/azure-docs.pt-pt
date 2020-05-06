---
title: Configuração alertas e notificações para Instância Gerida (portal Azure)
description: Utilize o portal Azure para criar alertas SQL Managed Instance, que podem desencadear notificações ou automatização quando as condições que especifica forem satisfeitas.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 05/04/2020
ms.openlocfilehash: 0e7c4cde684f393fd98ada46393948c5a62efa2f
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82800862"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Criar alertas para a Instância Gerida Azure SQL utilizando o portal Azure

## <a name="overview"></a>Descrição geral

Este artigo mostra como configurar alertas para bases de dados em Azure SQL Managed Instance Database utilizando o portal Azure. Os alertas podem enviar-lhe um e-mail, ligar para um gancho web, executar função Azure, réfica, ligar para um sistema de bilhética compatível com ITSM externo, ligar-lhe para o telefone ou enviar uma mensagem de texto quando alguma métrica, como é por exemplo, tamanho de armazenamento, ou uso de CPU, atinge um limiar predefinido. Este artigo também fornece as melhores práticas para definir períodos de alerta.

Pode receber um alerta com base em métricas de monitorização ou eventos nos seus serviços Azure.

* **Valores métricos** - O alerta dispara quando o valor de uma métrica especificada atravessa um limiar que atribui em qualquer direção. Isto é, dispara tanto quando a condição é cumprida pela primeira vez e depois quando essa condição já não está a ser satisfeita.

Pode configurar um alerta para fazer o seguinte quando aciona:

* Envie notificações por e-mail ao administrador de serviços e coadministradores
* Envie e-mail para e-mails adicionais que especifice.
* Ligue para um número de telefone com pedido de voz
* Enviar mensagem de texto para um número de telefone
* Chamar um webhook
* Ligue para a função Azure
* Ligue para o livro de corridas Azure
* Ligue para um sistema compatível com bilhética externa ITSM

Pode configurar e obter informações sobre regras de alerta utilizando as seguintes interfaces:

* [Portal do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Interface de Linha de Comandos (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="alerting-metrics-available-for-managed-instance"></a>Métricas de alerta disponíveis para instância gerida

> [!IMPORTANT]
> As métricas de alerta estão disponíveis apenas para a sua gestão. Não estão disponíveis métricas de alerta para bases de dados individuais em caso gerido. A telemetria de diagnósticos de bases de dados está, por outro lado, disponível sob a forma de [registos](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)de diagnóstico . Os alertas sobre os registos de diagnóstico podem ser configurados a partir do produto [SQL Analytics](../azure-monitor/insights/azure-sql.md) utilizando [scripts](../azure-monitor/insights/azure-sql.md#creating-alerts-for-managed-instances) de alerta de log para instânciagerida.

As seguintes métricas de instância geridas estão disponíveis para alertar a configuração:

| Métrica | Descrição | Unidade de medida \ possíveis valores |
| :--------- | --------------------- | ----------- |
| Percentagem média de CPU | Percentagem média de utilização de CPU no período de tempo selecionado. | 0-100 (por cento) |
| IO bytes lidos | IO bytes lidos no período de tempo selecionado. | Bytes |
| IO bytes escritos | IO bytes escritos no período de tempo selecionado. | Bytes |
| IO pedidos contam | Contagem de pedidos de IO no período de tempo selecionado. | Numérico |
| Espaço de armazenamento reservado | Máximo atual. espaço de armazenamento reservado para a instância gerida. Alterações com a operação de escala de recursos. | MB (Megabytes) |
| Espaço de armazenamento usado | Espaço de armazenamento utilizado no período selecionado. Alterações com o consumo de armazenamento por bases de dados e a instância. | MB (Megabytes) |
| Contagem de núcleos virtuais | vCores provisionados para a instância gerida. Alterações com a operação de escala de recursos. | 4-80 (vCores) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta sobre uma métrica com o portal Azure

1. No [portal](https://portal.azure.com/)Azure, localize a instância gerida pela SQL que está interessado em monitorizar e selecione-a.

2. Selecione o item do menu **Metrics** na secção monitorização.

   ![Monitorização](media/sql-database-managed-instance-alerts/mi-alerting-menu-annotated.png)
  
3. No menu suspenso, selecione uma das métricas em que pretende configurar o seu alerta (o espaço de armazenamento utilizado é mostrado no exemplo).

4. Selecione período de agregação - médio, mínimo ou máximo atingido no período de tempo (Avg, Min ou Max). 

5. Selecione **nova regra de alerta**

6. No painel de regras de alerta Criar clique no **nome condição** (espaço de armazenamento utilizado é mostrado no exemplo)

   ![Definir condição](media/sql-database-managed-instance-alerts/mi-create-metrics-alert-smaller-annotated.png)

7. No painel lógico de sinal configure, defina operador, tipo de agregação e valor limiar

   * As opções de tipo de operador são maiores do que, iguais e inferiores (o valor limiar)
   * As opções do tipo de agregação são min, máx ou média (no período de agregação da granularidade)
   * O valor limiar é o valor de alerta que será avaliado com base nos critérios de agregação do operador e da agregação
   
   ![Configure_signal_logic](media/sql-database-managed-instance-alerts/mi-configure-signal-logic-annotated.png)
   
   No exemplo mostrado na imagem, o valor de 1840876 MB é utilizado representando um valor limiar de 1,8 TB. Dado que o operador no exemplo é definido para maior do que, o alerta será criado se o consumo de espaço de armazenamento na instância gerida ultrapassar 1,8 TB. Note que o valor limiar para as métricas do espaço de armazenamento deve ser expresso em MB.

8. Definir o período de avaliação - granularidade de agregação em minutos e frequência de avaliação. A frequência da avaliação irá denotar o tempo em que o sistema de alerta verificará periodicamente se o estado limite foi cumprido.

9. Selecione grupo de ação. O painel do grupo de ação aparecerá através do qual poderá selecionar um existente ou criar uma nova ação. Esta ação define que acontecerá ao desencadear um alerta (por exemplo, enviar e-mail, ligar-lhe para o telefone, executar um webhook, função Azure ou um livro de execução, por exemplo).

   ![Select_action_group](media/sql-database-managed-instance-alerts/mi-select-action-group-smaller-annotated.png)

   * Para criar um novo grupo de ação, **selecione +Criar grupo de ação**

      ![Create_action_group_alerts](media/sql-database-managed-instance-alerts/mi-create-alert-action-group-smaller-annotated.png)
   
   * Defina como pretende ser alertado: Introduza o nome do grupo de ação, o nome curto, o nome de ação e selecione Action Type. O Tipo de Ação define se será notificado por e-mail, mensagem de texto, chamada de voz, ou se talvez webhook, função Azure, runbook será executado, ou o bilhete ITSM será criado no seu sistema compatível.

      ![Define_how_to_be_alerted](media/sql-database-managed-instance-alerts/mi-add-alerts-action-group-annotated.png)

10. Preencha os detalhes da regra de alerta para os seus registos, selecione o tipo de gravidade.

      ![Rule_description](media/sql-database-managed-instance-alerts/mi-rule-details-complete-smaller-annotated.png)

   * Complete a criação da regra de alerta clicando no botão de **regra de alerta Criar.**

A nova regra de alerta tornar-se-á ativa dentro de alguns minutos e será desencadeada com base nas suas definições.

## <a name="verifying-alerts"></a>Verificação de alertas

> [!NOTE]
> Para suprimir alertas ruidosos, consulte [a Supressão de alertas utilizando regras de ação](../azure-monitor/platform/alerts-action-rules.md#suppression-of-alerts).

Ao estabelecer uma regra de alerta, verifique se está satisfeito com o gatilho de alerta e a sua frequência. Para o exemplo mostrado nesta página para configurar um alerta sobre o espaço de armazenamento utilizado, se a sua opção de alerta fosse e-mail, poderia receber e-mail tal é o que está abaixo.

   ![alert_example](media/sql-database-managed-instance-alerts/mi-email-alert-example-smaller-annotated.png)

O e-mail mostra o nome do alerta, detalhes do limiar e por que o alerta foi desencadeado ajudando-o a verificar e resolver problemas com o seu alerta. Pode utilizar **o Portal See no portal Azure** para ver o alerta recebido via e-mail no portal Azure. 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>Ver, suspender, ativar, modificar e eliminar as regras de alerta existentes

> [!NOTE]
> Os alertas existentes precisam de ser geridos a partir do menu alerts do portal Azure dashboard. Os alertas existentes não podem ser modificados a partir da lâmina de recurso Managed Instance.

Para visualizar, suspender, ativar, modificar e eliminar os alertas existentes:

1. Procure alertas utilizando a pesquisa do portal Azure. Clique em Alertas.

   ![find_alerts](media/sql-database-managed-instance-alerts/mi-manage-alerts-browse-smaller-annotated.png)

   Em alternativa, também pode clicar em Alertas na barra de navegação Azure, se o tiver configurado.

2. No painel alertas, selecione Gerir as regras de alerta.

   ![modify_alerts](media/sql-database-managed-instance-alerts/mi-manage-alert-rules-smaller-annotated.png)

   A lista de alertas existentes vai aparecer. Selecione uma regra de alerta individual para geri-la. As regras ativas existentes podem ser modificadas e sintonizadas à sua preferência. As regras ativas também podem ser suspensas sem serem suprimidas. 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o sistema de alerta do Monitor Azure, consulte [a visão geral dos alertas no Microsoft Azure](../azure-monitor/platform/alerts-overview.md)
* Saiba mais sobre alertas métricos, veja [Como funcionam os alertas métricos no Monitor Azure](../azure-monitor/platform/alerts-metric-overview.md)
* Saiba configurar um webhook em alertas, consulte [Call a webhook com um alerta métrico clássico](../azure-monitor/platform/alerts-webhooks.md)
* Saiba mais sobre configurar e gerir alertas usando powerShell, consulte regras de [ação](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)
* Saiba configurar e gerir alertas utilizando API, consulte referência [da API do Monitor Azure](https://docs.microsoft.com/rest/api/monitor/) 
