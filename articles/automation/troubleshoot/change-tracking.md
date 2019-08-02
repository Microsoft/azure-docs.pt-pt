---
title: Solucionando problemas com o Azure Controle de Alterações
description: Este artigo fornece informações sobre a solução de problemas Controle de Alterações
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: bobbytreed
ms.author: robreed
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: beb0b89bdbf143c89a83c0813313a8bbda7235d4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564849"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Resolver Problemas do Controlo de Alterações e do Inventário

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Cenário Registros de Controle de Alterações não estão sendo exibidos para computadores Windows

#### <a name="issue"></a>Problema

Você não vê nenhum resultado de inventário ou Controle de Alterações para computadores Windows que são integrados para Controle de Alterações.

#### <a name="cause"></a>Causa

Esse erro pode ser causado pelos seguintes motivos:

1. O **Microsoft Monitoring Agent** não está em execução
2. A comunicação de volta com a conta de automação está sendo bloqueada.
3. Os pacotes de gerenciamento para Controle de Alterações não são baixados.
4. A VM que está sendo integrada pode ter vindo de um computador clonado que não foi Sysprep com o Microsoft Monitoring Agent instalado.

#### <a name="resolution"></a>Resolução

1. Verifique se o **Microsoft Monitoring Agent** (HealthService. exe) está em execução no computador.
1. Verifique **Visualizador de eventos** no computador e procure todos os eventos que tenham a palavra `changetracking` em si.
1. Visite o [planejamento de rede](../automation-hybrid-runbook-worker.md#network-planning) para saber mais sobre quais endereços e portas precisam ser permitidos para que controle de alterações funcionem.
1. Verifique se os seguintes pacotes de gerenciamento de Controle de Alterações e inventário existem localmente:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. Se estiver usando uma imagem clonada, execute o Sysprep na imagem primeiro e instale o agente de Microsoft Monitoring Agent após o fato.

Se essas soluções não resolverem o problema e você entrar em contato com o suporte, você poderá executar os seguintes comandos para coletar o diagnóstico no agente

No computador do agente, navegue até `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` e execute os seguintes comandos:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Por padrão, o rastreamento de erro é habilitado, se você quiser habilitar mensagens de erro detalhadas como o exemplo `VER` anterior, use o parâmetro. Para rastreamentos de informações `INF` , use ao `StartTracing.cmd`invocar.

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você poderá arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
