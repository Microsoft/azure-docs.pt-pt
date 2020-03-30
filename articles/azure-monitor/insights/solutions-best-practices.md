---
title: Solução de gestão nas melhores práticas do Azure Microsoft Docs
description: ''
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 837fb87d73698961ec1550b122840563d1707f8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663203"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Boas práticas para criar soluções de gestão em Azure (Pré-visualização)
> [!NOTE]
> Esta é uma documentação preliminar para a criação de soluções de gestão no Azure que estão atualmente em pré-visualização. Qualquer esquema descrito abaixo está sujeito a alterações.  

Este artigo fornece as melhores práticas para [a criação](solutions-solution-file.md) de um ficheiro de solução de gestão no Azure.  Estas informações serão atualizadas à medida que forem identificadas as melhores práticas adicionais.

## <a name="data-sources"></a>Origens de dados
- As fontes de dados podem ser [configuradas com um modelo](../../azure-monitor/platform/template-workspace-configuration.md)de Gestor de Recursos, mas não devem ser incluídas num ficheiro de solução.  A razão é que configurar fontes de dados não é atualmente idempotente, o que significa que a sua solução pode substituir a configuração existente no espaço de trabalho do utilizador.<br><br>Por exemplo, a sua solução pode exigir eventos de aviso e erro do registo do evento Da Aplicação.  Se especificar isto como uma fonte de dados na sua solução, arrisca-se a remover eventos de Informação se o utilizador tiver isto configurado no seu espaço de trabalho.  Se incluiu todos os eventos, poderá estar a recolher eventos de informação excessiva no espaço de trabalho do utilizador.

- Se a sua solução necessitar de dados de uma das fontes de dados padrão, então deve defini-lo como um pré-requisito.  Indicar na documentação que o cliente deve configurar a fonte de dados por si só.  
- Adicione uma mensagem de [Verificação](../../azure-monitor/platform/view-designer-tiles.md) de Fluxo de Dados a quaisquer pontos de vista da sua solução para instruir o utilizador sobre fontes de dados que precisam de ser configuradas para que os dados necessários sejam recolhidos.  Esta mensagem é exibida no azulejo da vista quando os dados necessários não são encontrados.


## <a name="runbooks"></a>Runbooks
- Adicione um [calendário de Automação](../../automation/automation-schedules.md) para cada livro de execução na sua solução que precisa de ser executado num horário.
- Inclua o [módulo IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) na sua solução para ser utilizado através de livros de execução que escrevem dados para o repositório Log Analytics.  Configure a solução para [fazer referência](solutions-solution-file.md#solution-resource) a este recurso de modo a que permaneça se a solução for removida.  Isto permite que várias soluções partilhem o módulo.
- Utilize [variáveis de Automação](../../automation/automation-schedules.md) para fornecer valores à solução que os utilizadores possam querer mudar mais tarde.  Mesmo que a solução esteja configurada para conter a variável, o seu valor ainda pode ser alterado.

## <a name="views"></a>Vistas
- Todas as soluções devem incluir uma única vista que seja exibida no portal do utilizador.  A vista pode conter múltiplas peças de [visualização](../../azure-monitor/platform/view-designer-parts.md) para ilustrar diferentes conjuntos de dados.
- Adicione uma mensagem de [Verificação](../../azure-monitor/platform/view-designer-tiles.md) de Fluxo de Dados a quaisquer pontos de vista da sua solução para instruir o utilizador sobre fontes de dados que precisam de ser configuradas para que os dados necessários sejam recolhidos.
- Configure a solução para [conter](solutions-solution-file.md#solution-resource) a vista de modo a que seja removida se a solução for removida.

## <a name="alerts"></a>Alertas
- Defina a lista de destinatários como parâmetro no ficheiro de solução para que o utilizador possa defini-los quando instalar a solução.
- Configure a solução para as regras de alerta [de referência](solutions-solution-file.md#solution-resource) para que o utilizador possa alterar a sua configuração.  Podem querer fazer alterações como modificar a lista de destinatários, alterar o limiar do alerta ou desativar a regra de alerta. 


## <a name="next-steps"></a>Passos seguintes
* Caminhe pelo processo básico de conceção e construção de uma solução de [gestão.](solutions-creating.md)
* Aprenda a criar um ficheiro de [solução.](solutions-solution-file.md)
* [Adicione pesquisas e alertas guardados](solutions-resources-searches-alerts.md) para a sua solução de gestão.
* [Adicione pontos de vista](solutions-resources-views.md) à sua solução de gestão.
* [Adicione livros de execução automation e outros recursos](solutions-resources-automation.md) à sua solução de gestão.

