---
title: "Invocar um Runbook da Automatização do Azure a partir de um Alerta do Log Analytics | Microsoft Docs"
description: "Este artigo disponibiliza uma descrição geral de como invocar um runbook da Automatização a partir de um alerta do Microsoft OMS Log Analytics."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2017
ms.author: magoedte
ms.openlocfilehash: 10b445f8fcaa80182119e47f37ffb11240a46869
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2017
---
# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>Invocar um runbook da Automatização do Azure a partir de um Alerta do OMS Log Analytics

Se for configurado um alerta no Log Analytics para criar um registo de alerta quando os resultados corresponderem aos critérios, como um pico prolongado na utilização do processador ou um processo de aplicação crítico para a funcionalidade de uma aplicação empresarial falhar e escrever um evento correspondente no registo de eventos do Windows, esse alerta pode executar automaticamente um runbook da Automatização numa tentativa de tentar remediar sozinho o problema.  

Existem duas opções para chamar um runbook durante a configuração do alerta, tais como:

1. Utilizar um Webhook.
   * Esta é a única opção disponível se a sua área de trabalho do OMS não estiver associada a uma conta de Automatização.
   * Se já tiver uma conta de Automatização ligada a uma área de trabalho do OMS, esta opção continua disponível.  

2. Selecionar um runbook diretamente.
   * Esta opção só está disponível se a área de trabalho do OMS estiver ligada a uma conta de Automatização.

## <a name="calling-a-runbook-using-a-webhook"></a>Utilizar um webhook para chamar um runbook

Um webhook permite-lhe iniciar um runbook específico na Automatização do Azure através de um único pedido HTTP. Antes de configurar o [alerta do Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules) para chamar o runbook com um webhook como ação do alerta, tem de criar primeiro um webhook para o runbook chamado com este método. Efetue os passos no artigo [Create a Webhook](automation-webhooks.md#creating-a-webhook) (Criar um Webhook) e não se esqueça de registar o URL do webhook, para que o possa referenciar enquanto configura a regra do alerta.   

## <a name="calling-a-runbook-directly"></a>Chamar um runbook diretamente

Se tiver instalada e configurada a oferta Automatização e Controlo na área de trabalho do OMS, quando configurar a opção das ações do Runbook para o alerta, pode ver todos os runbooks na lista pendente **Selecionar runbook** e selecionar o runbook específico que quer executar como resposta ao alerta. O runbook selecionado pode ser executado numa área de trabalho na cloud do Azure ou numa função de trabalho de runbook híbrida. Após a criação do alerta com a opção do runbook, é criado um webhook para o runbook. Pode ver o webhook ao aceder à conta de Automatização e navegar para o painel do webhook do runbook selecionado. Se eliminar o alerta, o webhook não é eliminado, mas o utilizador pode eliminá-lo manualmente. Não há problema se o webhook não for eliminado; é apenas um item órfão que tem de ser eliminado, a um dado momento, para manter uma conta de Automatização organizada.  

## <a name="characteristics-of-a-runbook-for-both-options"></a>Características de um runbook (para ambas as opções)

Ambos os métodos para chamar o runbook a partir do alerta do Log Analytics têm características que tem de perceber antes de configurar as regras do alerta.

* Tem de ter um parâmetro de entrada do runbook com o nome **WebhookData** e que seja do tipo **Object**. Pode ser obrigatório ou opcional. O alerta utiliza este parâmetro de entrada para transmitir os resultados da pesquisa ao runbook.

    ```powershell
    param  
        (  
        [Parameter (Mandatory=$true)]  
        [object] $WebhookData  
        )
    ```
*  Tem de ter código para converter o WebhookData num objeto do PowerShell.

    ```powershell
    $SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
    ```

    *$SearchResult* é uma matriz de objetos; cada objeto contém os campos com valores de um resultado de pesquisa

## <a name="example-walkthrough"></a>Instruções de exemplo

Para demonstrar como este processo funciona, vamos utilizar o runbook gráfico de exemplo seguinte, que inicia um serviço do Windows.<br><br> ![Runbook Gráfico de Início de Serviço do Windows](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

O runbook tem um parâmetro de entrada do tipo **Object** com o nome **WebhookData** e inclui os dados do webhook transmitidos do alerta que contém \*.SearchResult\*.<br><br> ![Parâmetros de entrada do runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

Neste exemplo, criámos, no Log Analytics, dois campos personalizados, \*SvcDisplayName\_CF\* e \*SvcState\_CF\*, para extrair o nome a apresentar e o estado do serviço (ou seja, em execução ou parado) a partir do evento escrito no registo de eventos do Sistema. Depois, criámos uma regra de alerta com a consulta de pesquisa `Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"`, para que possamos detetar quando o serviço Print Spooler é parado no sistema Windows. Pode ser qualquer serviço de interesse, mas este exemplo faz referência a um dos serviços pré-existentes que estão incluídos no SO Windows. A ação do alerta está configurada para executar o nosso runbook utilizado neste exemplo e executado na Função de Trabalho de Runbook Híbrida, que está ativada no sistema de destino.   

A atividade do código do runbook **Get Service Name from LA** converte a cadeia em formato JSON num tipo de objeto e filtra o item \*SvcDisplayName\_CF\* para extrair o nome a apresentar do serviço do Windows e transmite este valor para a atividade seguinte, que verifica se o serviço está parado antes de tentar reiniciá-lo. *SvcDisplayName_CF* é um [campo personalizado](../log-analytics/log-analytics-custom-fields.md) criado no Log Analytics para demonstrar este exemplo.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

Quando o serviço para, a regra do alerta no Log Analytics deteta uma correspondência, aciona o runbook e envia o contexto do alerta para este. O runbook toma medidas para verificar se o serviço está parado e, se for esse o caso, tenta reiniciá-lo, verificar se foi iniciado corretamente e produz os resultados.     

Em alternativa, se a sua conta de Automatização não estiver ligada à área de trabalho do OMS, configuraria a regra de alerta com uma ação de webhook para acionar o runbook e configuraria o runbook para converter a cadeia em formato JSON e filtrar em \*.SearchResult\*, seguindo a orientação mencionada anteriormente.    

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os alertas no Log Analytics e como criá-los, veja [Alerts in Log Analytics (Alertas no Log Analytics)](../log-analytics/log-analytics-alerts.md).

* Para saber como utilizar webhooks para acionar runbooks, veja [Azure Automation webhooks (Webhooks da Automatização do Azure)](automation-webhooks.md).
