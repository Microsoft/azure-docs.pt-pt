---
title: Alertas de configuração e notificações para Instância Gerida (portal Azure)
description: Utilize o portal Azure para criar alertas SQL Managed Instance, que podem desencadear notificações ou automatização quando as condições especificadas estiverem satisfeitas.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 05/04/2020
ms.openlocfilehash: a5a2336728d4507544fa621961c2f346746c2bb9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792655"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Criar alertas para o Azure SQL Managed Instance com o portal do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo mostra como configurar alertas para bases de dados na Base de Dados de Casos Geridos Azure SQL utilizando o portal Azure. Os alertas podem enviar-lhe um e-mail, chamar um webhook, executar uma Função do Azure, runbook, chamar um sistema de pedidos de suporte compatível com ITSM externo, ligar-lhe para o telefone ou enviar uma mensagem de texto quando alguma métrica como, por exemplo, o tamanho de armazenamento ou a utilização da CPU, atingir um limiar predefinido. Este artigo também fornece as melhores práticas para a definição de períodos de alerta.


## <a name="overview"></a>Descrição geral

Pode receber um alerta baseado em métricas de monitorização ou eventos nos seus serviços Azure.

* **Valores métricos** - O alerta dispara quando o valor de uma métrica especificada cruza um limiar que atribui em qualquer direção. Ou seja, desencadeia tanto quando a condição é primeiramente satisfeita e depois quando essa condição já não está a ser satisfeita.

Pode configurar um alerta para fazer o seguinte quando acionar:

* Envie notificações por e-mail ao administrador de serviço e coadministrators
* Envie e-mail para e-mails adicionais que especifique.
* Ligue para um número de telefone com pedido de voz
* Enviar mensagem de texto para um número de telefone
* Chamar um webhook
* Chamar função Azure
* Ligue para o runbook de Azure
* Ligue para um sistema compatível com a BILHS ITSM

Pode configurar e obter informações sobre as regras de alerta utilizando [o portal Azure, PowerShell ou o API do Azure Monitor](../../azure-monitor/platform/alerts-classic-portal.md) [REST](/rest/api/monitor/alertrules). 

## <a name="alerting-metrics-available-for-managed-instance"></a>Métricas de alerta disponíveis para instância gerida

> [!IMPORTANT]
> As métricas de alerta estão disponíveis apenas para instâncias geridas. Não estão disponíveis métricas de alerta para bases de dados individuais em casos geridos. Por outro lado, a telemetria de diagnóstico da base de dados está disponível sob a forma de registos de [diagnósticos](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export). Os alertas nos registos de diagnóstico podem ser configurados a partir do produto [SQL Analytics](../../azure-monitor/insights/azure-sql.md) utilizando scripts de alerta de registo para exemplos [geridos.](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance)

Estão disponíveis as seguintes métricas de instância geridas para a configuração de alerta:

| Métrica | Descrição | Unidade de medida \ valores possíveis |
| :--------- | --------------------- | ----------- |
| Percentagem média de CPU | Percentagem média de utilização do CPU no período de tempo selecionado. | 0-100 (por cento) |
| IO bytes ler | IO bytes lidos no período de tempo selecionado. | Bytes |
| IO bytes escritos | IO bytes escritos no período de tempo selecionado. | Bytes |
| Contagem de pedidos de IO | Contagem de pedidos de IO no período de tempo selecionado. | Numérico |
| Espaço de armazenamento reservado | Atual max. espaço de armazenamento reservado para a instância gerida. Alterações com a operação de dimensionamento de recursos. | MB (Megabytes) |
| Espaço de armazenamento utilizado | Espaço de armazenamento utilizado no período selecionado. Alterações com o consumo de armazenamento por bases de dados e o caso. | MB (Megabytes) |
| Contagem de núcleos virtuais | vCores previstos para a instância gerida. Alterações com a operação de dimensionamento de recursos. | 4-80 (vCores) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta numa métrica com o portal Azure

1. No [portal](https://portal.azure.com/)Azure, localize o caso gerido que está interessado em monitorizar e selecione-o.

2. Selecione o item do menu **métricas** na secção de Monitorização.

   ![Monitorização](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. No menu suspenso, selecione uma das métricas em que pretende configurar o seu alerta (o espaço de armazenamento utilizado é mostrado no exemplo).

4. Selecione o período de agregação - médio, mínimo ou máximo atingido no período de tempo determinado (Avg, Min ou Max). 

5. Selecione **nova regra de alerta**

6. No painel de regras de alerta Criar clique no **nome 'Estado)** (o espaço de armazenamento utilizado é mostrado no exemplo)

   ![Definir condição](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. No painel de lógica de sinal de configuração, defina o operador, o tipo de agregação e o valor limiar

   * As opções do tipo operador são maiores do que, iguais e inferiores (o valor limiar)
   * As opções do tipo de agregação são min, máx ou média (no período de granularidade agregação)
   * O valor limiar é o valor de alerta que será avaliado com base nos critérios de agregação e operador
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   No exemplo mostrado na imagem, é utilizado o valor de 1840876 MB que representa um valor limiar de 1,8 TB. Como o operador no exemplo é definido para maior do que, o alerta será criado se o consumo de espaço de armazenamento na instância gerida for superior a 1,8 TB. Note que o valor limiar das métricas do espaço de armazenamento deve ser expresso em MB.

8. Decretar o período de avaliação - granularidade agregação em minutos e frequência de avaliação. A frequência da avaliação irá denotar o tempo em que o sistema de alerta verificará periodicamente se a condição limiar foi cumprida.

9. Selecione grupo de ação. O painel de grupo de ação aparecerá através do qual poderá selecionar um existente ou criar uma nova ação. Esta ação define o que acontecerá ao desencadear um alerta (por exemplo, enviar e-mails, ligar-lhe para o telefone, executar um webhook, função Azure ou um livro de execução, por exemplo).

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * Para criar um novo grupo de ação, selecione **+Criar grupo de ação**

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * Defina como pretende ser alertado: Introduza o nome do grupo de ação, o nome curto, o nome de ação e selecione O Tipo de Ação. O Action Type define se será notificado por e-mail, mensagem de texto, chamada de voz ou se talvez webhook, função Azure, runbook será executado, ou o bilhete ITSM será criado no seu sistema compatível.

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. Preencha os detalhes da regra de alerta para os seus registos, selecione o tipo de gravidade.

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * Complete a criação da regra de alerta clicando no botão **De regra de alerta Criar.**

A nova regra de alerta ficará ativa dentro de alguns minutos e será ativada com base nas suas definições.

## <a name="verifying-alerts"></a>Verificação de alertas

> [!NOTE]
> Para suprimir alertas ruidosos, consulte [supressão de alertas utilizando regras de ação](../../azure-monitor/platform/alerts-action-rules.md#suppression-of-alerts).

Ao configurar uma regra de alerta, verifique se está satisfeito com o gatilho de alerta e a sua frequência. Para o exemplo mostrado nesta página para configurar um alerta sobre o espaço de armazenamento utilizado, se a sua opção de alerta fosse e-mail, pode receber e-mail tal é o indicado abaixo.

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

O e-mail mostra o nome de alerta, detalhes do limiar e por que o alerta foi desencadeado ajudando-o a verificar e resolver problemas no seu alerta. Pode utilizar o botão **do portal Azure** para ver o alerta recebido via e-mail no portal Azure. 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>Ver, suspender, ativar, modificar e eliminar as regras de alerta existentes

> [!NOTE]
> Os alertas existentes precisam de ser geridos a partir do menu Alertas do painel do portal Azure. Os alertas existentes não podem ser modificados a partir da lâmina de recursos de Instância Gerida.

Para ver, suspender, ativar, modificar e eliminar os alertas existentes:

1. Procure alertas utilizando a pesquisa no portal Azure. Clique em Alertas.

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   Em alternativa, também pode clicar em Alertas na barra de navegação Azure, se a tiver configurada.

2. No painel alerta, selecione Gerir as regras de alerta.

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   A lista de alertas existentes vai aparecer. Selecione uma regra de alerta existente para geri-la. As regras ativas existentes podem ser modificadas e sintonizadas à sua preferência. As regras ativas também podem ser suspensas sem serem suprimidas. 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o sistema de alerta Azure Monitor, consulte [a visão geral dos alertas no Microsoft Azure](../../azure-monitor/platform/alerts-overview.md)
* Saiba mais sobre alertas métricos, veja [como os alertas métricos funcionam no Azure Monitor](../../azure-monitor/platform/alerts-metric-overview.md)
* Saiba como configurar um webhook em alertas, consulte [Call a webhook com um alerta métrico clássico](../../azure-monitor/platform/alerts-webhooks.md)
* Saiba como configurar e gerir alertas usando o PowerShell, consulte [as regras de Ação](/powershell/module/az.monitor/add-azmetricalertrulev2)
* Saiba como configurar e gerir alertas utilizando a API, consulte [a referência API do Monitor AZure](/rest/api/monitor/)