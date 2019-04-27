---
title: Solução de gestão no melhores práticas do Azure | Documentos da Microsoft
description: ''
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: d6d2414935bb5d1f095ad2b200acafa97b3b9b32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596644"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Melhores práticas para criar soluções de gestão no Azure (pré-visualização)
> [!NOTE]
> Esta é a documentação preliminar para a criação de soluções de gestão no Azure que estão atualmente em pré-visualização. Qualquer esquema descrita abaixo está sujeitas a alterações.  

Este artigo fornece as práticas recomendadas para [criando um arquivo de solução de gestão](solutions-solution-file.md) no Azure.  Estas informações serão atualizadas conforme as melhores práticas adicionais são identificadas.

## <a name="data-sources"></a>Origens de dados
- Origens de dados podem ser [configurado com um modelo do Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), mas não devem ser incluídos num arquivo de solução.  O motivo é que configurar origens de dados não é atualmente idempotentes, que significa que a sua solução poderia substituir a configuração existente na área de trabalho do utilizador.<br><br>Por exemplo, a sua solução pode exigir eventos de aviso e erro do registo de eventos do aplicativo.  Se especificar isso como uma origem de dados na sua solução, corre o risco de remover os eventos de informação se o usuário tivesse esta configuração em sua área de trabalho.  Se tiver incluído todos os eventos, pode ser a recolher eventos excessivos de informações na área de trabalho do utilizador.

- Se sua solução necessitar de dados a partir de uma das origens de dados padrão, deve definir isso como um pré-requisito.  Estado na documentação de que o cliente tem de configurar a origem de dados por conta própria.  
- Adicionar uma [verificação de fluxo de dados](../../azure-monitor/platform/view-designer-tiles.md) mensagem para todas as vistas na sua solução para instruir o utilizador em origens de dados que devem ser configurados para os dados necessários a serem recolhidos.  Esta mensagem é apresentada no mosaico da exibição quando os dados necessários não foi encontrados.


## <a name="runbooks"></a>Runbooks
- Adicionar uma [agenda da automatização](../../automation/automation-schedules.md) para cada runbook na sua solução de que precisa ser executado com base numa agenda.
- Incluir o [IngestionAPI módulo](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) na sua solução para ser utilizada pelos runbooks escrita de dados no repositório do Log Analytics.  Configurar a solução para [referência](solutions-solution-file.md#solution-resource) este recurso, de modo que ele permanece se a solução é removida.  Isso permite que várias soluções partilhar o módulo.
- Uso [variáveis de automatização](../../automation/automation-schedules.md) para fornecer valores para a solução que os usuários podem querer alterar mais tarde.  Mesmo que a solução estiver configurada para conter a variável, seu valor ainda pode ser alterado.

## <a name="views"></a>Vistas
- Todas as soluções devem incluir uma única exibição, que é apresentada no portal do utilizador.  A vista pode conter vários [partes de visualização](../../azure-monitor/platform/view-designer-parts.md) para ilustrar a diferentes conjuntos de dados.
- Adicionar uma [verificação de fluxo de dados](../../azure-monitor/platform/view-designer-tiles.md) mensagem para todas as vistas na sua solução para instruir o utilizador em origens de dados que devem ser configurados para os dados necessários a serem recolhidos.
- Configurar a solução para [contêm](solutions-solution-file.md#solution-resource) o modo de exibição, de modo que a TI da removidos se a solução é removida.

## <a name="alerts"></a>Alertas
- Defina a lista de destinatários como um parâmetro no arquivo da solução, para que o utilizador pode defini-las durante a instalação da solução.
- Configurar a solução para [referência](solutions-solution-file.md#solution-resource) as regras de alerta para que esse utilizador pode alterar a respetiva configuração.  Podem querer fazer alterações, como modificar a lista de destinatários, alterar o limiar do alerta ou desativar a regra de alerta. 


## <a name="next-steps"></a>Passos Seguintes
* Percorrer o processo básico de [estruturação e criação de uma solução de gestão](solutions-creating.md).
* Saiba como [criar um arquivo de solução](solutions-solution-file.md).
* [Adicionar pesquisas guardadas e alertas](solutions-resources-searches-alerts.md) à sua solução de gestão.
* [Adicionar vistas](solutions-resources-views.md) à sua solução de gestão.
* [Adicionar runbooks de automatização e outros recursos](solutions-resources-automation.md) à sua solução de gestão.

