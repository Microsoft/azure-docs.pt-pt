---
title: Gerir espaços de trabalho do Azure Sentinel em escala
description: Saiba como gerir eficazmente o Azure Sentinel em recursos de clientes delegados.
ms.date: 06/17/2020
ms.topic: how-to
ms.openlocfilehash: 70810ca54c62e73d5dec76495eb5179b84db60fe
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105273"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>Gerir espaços de trabalho do Azure Sentinel em escala

Como prestador de serviços, pode ter a bordo vários inquilinos de clientes para o [Farol de Azure.](../overview.md) O Azure Lighthouse permite que os prestadores de serviços realizem operações em escala em vários inquilinos do Azure Ative Directory (Azure AD) de uma só vez, tornando as tarefas de gestão mais eficientes.

O Azure Sentinel fornece analítica de segurança e inteligência de ameaças, fornecendo uma única solução para deteção de alerta, visibilidade de ameaças, caça proativa e resposta a ameaças. Com o Azure Lighthouse, você pode gerir vários espaços de trabalho Azure Sentinel em todos os inquilinos em escala. Isto permite que cenários como executar consultas em vários espaços de trabalho, ou criar livros de trabalho para visualizar e monitorizar dados a partir das suas fontes de dados conectadas para obter insights. IP como consultas e playbooks permanecem no seu inquilino gerente, mas pode ser usado para realizar gestão de segurança nos inquilinos do cliente.

Este tópico fornece uma visão geral de como usar [o Azure Sentinel](../../sentinel/overview.md) de uma forma escalável para a visibilidade interina e serviços de segurança geridos.

## <a name="architectural-considerations"></a>Considerações arquitetónicas

Para um prestador de serviços de segurança gerido (MSSP) que queira construir uma oferta de Segurança como serviço utilizando o Azure Sentinel, um único centro de operações de segurança (SOC) pode ser necessário para monitorizar, gerir e configurar centralmente vários espaços de trabalho do Azure Sentinel implantados dentro de inquilinos individuais do cliente. Da mesma forma, empresas com vários inquilinos da AD Azure podem querer gerir centralmente vários espaços de trabalho Azure Sentinel implantados em seus inquilinos.

Este modelo centralizado de implantação tem as seguintes vantagens:

- A propriedade dos dados permanece com cada inquilino gerido.
- Suporta requisitos para armazenar dados dentro dos limites geográficos.
- Garante o isolamento de dados, uma vez que os dados para vários clientes não são armazenados no mesmo espaço de trabalho. 
- Evita a exfiltração de dados dos inquilinos geridos, ajudando a garantir a conformidade com os dados.
- Os custos relacionados são cobrados a cada inquilino gerido, em vez do inquilino gerente.
- Os dados de todas as fontes de dados e conectores de dados que estejam integrados com o Azure Sentinel (tais como Registos de Atividade ad AZure, registos do Office 365 ou alertas de Proteção de Ameaças da Microsoft) permanecerão dentro de cada inquilino do cliente.
- Reduz a latência da rede.
- Fácil de adicionar ou remover novas subsidiárias ou clientes.

## <a name="granular-role-based-access-control-rbac"></a>Controlo de acesso baseado em funções granulares (RBAC)

Cada subscrição de cliente que um MSSP irá gerir deve ser [a bordo do Farol de Azure.](onboard-customer.md) Isto permite que os utilizadores designados no inquilino gerente acedam e realizem operações de gestão em espaços de trabalho Azure Sentinel implantados em inquilinos de clientes.

Ao criar as suas autorizações, pode atribuir as funções incorporadas do Azure Sentinel aos utilizadores, grupos ou diretores de serviços no seu inquilino-gerente:

- [Leitor de Azure Sentinel](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel socorrista](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Colaborador do Azure Sentinel](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Também pode querer atribuir funções adicionais incorporadas para executar funções adicionais. Para obter informações sobre funções específicas que possam ser usadas com Azure Sentinel, consulte [permissões em Azure Sentinel](../../sentinel/roles.md).

Uma vez que tenha acedido aos seus clientes, os utilizadores designados podem iniciar sessão no seu inquilino gerente e [aceder diretamente ao espaço de trabalho do cliente Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md) com as funções que foram atribuídas.

## <a name="view-and-manage-incidents-across-workspaces"></a>Ver e gerir incidentes em espaços de trabalho

Se você está gerindo recursos Azure Sentinel para vários clientes, você pode ver e gerir incidentes em vários espaços de trabalho em vários inquilinos ao mesmo tempo. Para obter mais informações, consulte [Work with incidents in many workspaces at once](../../sentinel/multiple-workspace-view.md) e Extend [Azure Sentinel através de espaços de trabalho e inquilinos.](../../sentinel/extend-sentinel-across-workspaces-tenants.md)

> [!NOTE]
> Certifique-se de que os utilizadores do seu inquilino de gestão foram designados para ler e escrever permissões em todos os espaços de trabalho que são geridos. Se um utilizador tiver apenas permissões lidas em alguns espaços de trabalho, podem ser mostradas mensagens de aviso ao selecionar incidentes nesses espaços de trabalho, e o utilizador não poderá modificar esses incidentes ou quaisquer outros que tenha selecionado com eles (mesmo que tenha permissões para os outros).

## <a name="configure-playbooks-for-mitigation"></a>Configurar livros para mitigação

[Os livros de reprodução](../../sentinel/tutorial-respond-threats-playbook.md) podem ser utilizados para mitigação automática quando um alerta é acionado. Estes livros podem ser executados manualmente ou podem ser executados automaticamente quando os alertas específicos são acionados. Os playbooks podem ser implementados quer no inquilino gerente quer no inquilino do cliente, com os procedimentos de resposta configurados com base nos quais os utilizadores do arrendatário terão de tomar medidas em resposta a uma ameaça à segurança.

## <a name="create-cross-tenant-workbooks"></a>Criar livros de inquilinos cruzados

[Os livros de trabalho do Azure Monitor em Azure Sentinel ajudam-no](../../sentinel/overview.md#workbooks) a visualizar e a monitorizar dados das suas fontes de dados ligadas para obter informações. Pode utilizar os modelos de livro incorporados no Azure Sentinel ou criar livros de trabalho personalizados para os seus cenários.

Você pode implementar livros de trabalho no seu inquilino gerente e criar dashboards à escala para monitorizar e consultar dados através dos inquilinos do cliente. Para obter mais informações, consulte [a monitorização do espaço de trabalho.](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks) Note que algumas capacidades não são [suportadas em vários espaços de trabalho.](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces)

Também pode implementar livros diretamente num inquilino individual que gere para cenários específicos desse cliente.

## <a name="run-queries-across-azure-sentinel-workspaces"></a>Executar consultas em todos os espaços de trabalho do Azure Sentinel

Pode criar e guardar consultas de Log Analytics para deteção de ameaças centralmente no inquilino gestor. Estas consultas podem então ser executadas em todos os espaços de trabalho do Azure Sentinel dos seus clientes utilizando o operador da União e a expressão do espaço de trabalho (). Para obter mais informações, consulte [consulta cross-workspace](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying).

## <a name="use-automation-for-cross-workspace-management"></a>Use a automatização para gestão de espaços de trabalho cruzadas

Você pode usar a automatização para gerir vários espaços de trabalho do Azure Sentinel e configurar [consultas de caça,](../../sentinel/hunting.md)playbooks e livros de trabalho. Para obter mais informações, consulte [a gestão do espaço de trabalho transversal utilizando a automação.](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation)

Note que algumas capacidades não são [suportadas atualmente em vários espaços de trabalho.](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces)

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [Azure Sentinel.](../../sentinel/overview.md)
- Reveja a [página de preços do Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
- Conheça as [experiências de gestão de inquilinos cruzados.](../concepts/cross-tenant-management-experience.md)

