---
title: Solução de gerenciamento em práticas recomendadas do Azure | Microsoft Docs
description: ''
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 28ae01fe28b1b2d6af95567e529c7c9ae17920e4
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553930"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Práticas recomendadas para a criação de soluções de gerenciamento no Azure (versão prévia)
> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento no Azure que estão atualmente em versão prévia. Qualquer esquema descrito abaixo está sujeito a alterações.  

Este artigo fornece as práticas recomendadas para [a criação de um arquivo de solução de gerenciamento](solutions-solution-file.md) no Azure.  Essas informações serão atualizadas conforme as práticas recomendadas adicionais forem identificadas.

## <a name="data-sources"></a>Origens de dados
- As fontes de dados podem ser [configuradas com um modelo do Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), mas não devem ser incluídas em um arquivo de solução.  O motivo é que a configuração de fontes de dados não é idempotente no momento, o que significa que sua solução poderia substituir a configuração existente no espaço de trabalho do usuário.<br><br>Por exemplo, sua solução pode exigir eventos de aviso e erro do log de eventos do aplicativo.  Se você especificar isso como uma fonte de dados em sua solução, você arriscará a remover eventos de informações se o usuário tiver configurado esse código em seu espaço de trabalho.  Se você tiver incluído todos os eventos, poderá estar coletando eventos de informações excessivos no espaço de trabalho do usuário.

- Se sua solução exigir dados de uma das fontes de dados padrão, você deverá definir isso como um pré-requisito.  Estado na documentação que o cliente deve configurar a fonte de dados por conta própria.  
- Adicione uma mensagem de [verificação de fluxo de dados](../../azure-monitor/platform/view-designer-tiles.md) a quaisquer exibições em sua solução para instruir o usuário em fontes de dados que precisam ser configuradas para que os dados necessários sejam coletados.  Essa mensagem é exibida no bloco da exibição quando os dados necessários não são encontrados.


## <a name="runbooks"></a>Runbooks
- Adicione uma [agenda de automação](../../automation/automation-schedules.md) para cada runbook em sua solução que precisa ser executada em um agendamento.
- Inclua o [módulo IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) em sua solução para ser usado por runbooks gravando dados no repositório de log Analytics.  Configure a solução para [fazer referência](solutions-solution-file.md#solution-resource) a esse recurso para que ele permaneça se a solução for removida.  Isso permite que várias soluções compartilhem o módulo.
- Use [variáveis de automação](../../automation/automation-schedules.md) para fornecer valores à solução que os usuários talvez queiram alterar posteriormente.  Mesmo que a solução esteja configurada para conter a variável, seu valor ainda poderá ser alterado.

## <a name="views"></a>Vistas
- Todas as soluções devem incluir uma única exibição exibida no portal do usuário.  O modo de exibição pode conter várias [partes de visualização](../../azure-monitor/platform/view-designer-parts.md) para ilustrar diferentes conjuntos de dados.
- Adicione uma mensagem de [verificação de fluxo de dados](../../azure-monitor/platform/view-designer-tiles.md) a quaisquer exibições em sua solução para instruir o usuário em fontes de dados que precisam ser configuradas para que os dados necessários sejam coletados.
- Configure a solução para [conter](solutions-solution-file.md#solution-resource) a exibição para que ela seja removida se a solução for removida.

## <a name="alerts"></a>Alertas
- Defina a lista de destinatários como um parâmetro no arquivo de solução para que o usuário possa defini-los ao instalar a solução.
- Configure a solução para [referenciar](solutions-solution-file.md#solution-resource) regras de alerta para que o usuário possa alterar sua configuração.  Eles podem querer fazer alterações, como modificar a lista de destinatários, alterar o limite do alerta ou desabilitar a regra de alerta. 


## <a name="next-steps"></a>Passos seguintes
* Percorra o processo básico de [criação e criação de uma solução de gerenciamento](solutions-creating.md).
* Saiba como [criar um arquivo de solução](solutions-solution-file.md).
* [Adicionar alertas e pesquisas salvas](solutions-resources-searches-alerts.md) à sua solução de gerenciamento.
* [Adicione exibições](solutions-resources-views.md) à sua solução de gerenciamento.
* [Adicione runbooks de automação e outros recursos](solutions-resources-automation.md) à sua solução de gerenciamento.

