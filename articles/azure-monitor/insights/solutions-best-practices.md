---
title: Solução de gestão nas melhores práticas do Azure
description: Neste artigo, aprenda dicas para criar um ficheiro de solução de gestão. Veja como trabalhar com fontes de dados, runbooks, vistas e alertas.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 7cb300297336edcce4294b800520ad570b12bcde
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84320935"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Melhores práticas para criar soluções de gestão em Azure (Preview)
> [!NOTE]
> Esta é a documentação preliminar para a criação de soluções de gestão no Azure que estão atualmente em pré-visualização. Qualquer esquema descrito abaixo está sujeito a alterações.  

Este artigo fornece as melhores práticas para [a criação de um ficheiro de solução de gestão](solutions-solution-file.md) no Azure.  Estas informações serão atualizadas à medida que forem identificadas as melhores práticas adicionais.

## <a name="data-sources"></a>Origens de dados
- As fontes de dados podem ser [configuradas com um modelo de Gestor de Recursos,](../../azure-monitor/platform/template-workspace-configuration.md)mas não devem ser incluídas num ficheiro de solução.  A razão é que configurar fontes de dados não é atualmente idempotente, o que significa que a sua solução pode substituir a configuração existente no espaço de trabalho do utilizador.<br><br>Por exemplo, a sua solução pode requerer eventos de Aviso e Erro a partir do registo de eventos da Aplicação.  Se especificar isto como uma fonte de dados na sua solução, arrisca-se a remover eventos de Informação se o utilizador o tiver configurado no seu espaço de trabalho.  Se incluiu todos os eventos, poderá estar a recolher eventos de informação excessivos no espaço de trabalho do utilizador.

- Se a sua solução necessitar de dados de uma das fontes de dados padrão, então deve defini-lo como um pré-requisito.  Indicar na documentação que o cliente deve configurar a fonte de dados por si só.  
- Adicione uma mensagem [de Verificação de Fluxo de Dados](../../azure-monitor/platform/view-designer-tiles.md) a quaisquer visualizações na sua solução para instruir o utilizador sobre fontes de dados que precisam de ser configuradas para que os dados necessários sejam recolhidos.  Esta mensagem é exibida no azulejo da vista quando os dados necessários não são encontrados.


## <a name="runbooks"></a>Runbooks
- Adicione um [horário de Automação](../../automation/automation-schedules.md) para cada livro de recortes na sua solução que precisa de ser executado num horário.
- Inclua o [módulo IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) na sua solução para ser usado por livros de execução que escrevem dados para o repositório Log Analytics.  Configure a solução para [fazer referência](solutions-solution-file.md#solution-resource) a este recurso de modo a que permaneça se a solução for removida.  Isto permite que várias soluções partilhem o módulo.
- Utilize [variáveis de automatização](../../automation/automation-schedules.md) para fornecer valores à solução que os utilizadores poderão querer mudar mais tarde.  Mesmo que a solução esteja configurada para conter a variável, o seu valor ainda pode ser alterado.

## <a name="views"></a>Vistas
- Todas as soluções devem incluir uma única visão que seja exibida no portal do utilizador.  A vista pode conter várias [partes de visualização](../../azure-monitor/platform/view-designer-parts.md) para ilustrar diferentes conjuntos de dados.
- Adicione uma mensagem [de Verificação de Fluxo de Dados](../../azure-monitor/platform/view-designer-tiles.md) a quaisquer visualizações na sua solução para instruir o utilizador sobre fontes de dados que precisam de ser configuradas para que os dados necessários sejam recolhidos.
- Configure a solução para [conter](solutions-solution-file.md#solution-resource) a vista de modo a que seja removida se a solução for removida.

## <a name="alerts"></a>Alertas
- Defina a lista de destinatários como um parâmetro no ficheiro de solução para que o utilizador possa defini-los quando instalar a solução.
- Configure a solução para regras de alerta de [referência](solutions-solution-file.md#solution-resource) para que os utilizadores possam alterar a sua configuração.  Podem querer fazer alterações tais como modificar a lista de destinatários, alterar o limiar do alerta ou desativar a regra de alerta. 


## <a name="next-steps"></a>Próximos passos
* Caminhe pelo processo básico de [conceção e construção de uma solução de gestão.](solutions-creating.md)
* Saiba como [criar um ficheiro de solução.](solutions-solution-file.md)
* [Adicione pesquisas e alertas guardados](solutions-resources-searches-alerts.md) à sua solução de gestão.
* [Adicione vistas](solutions-resources-views.md) à sua solução de gestão.
* [Adicione livros de automação e outros recursos](solutions-resources-automation.md) à sua solução de gestão.

