---
title: Operações de avião de controlo e de planos de dados
description: Descreve a diferença entre as operações do avião de controlo e do plano de dados. As operações do avião de controlo são tratadas pelo Gestor de Recursos Azure. As operações de avião de dados são tratadas por um serviço.
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: f478037c3e83c1d28cc900d64512a41619628dd7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91371274"
---
# <a name="azure-control-plane-and-data-plane"></a>Avião de controlo Azure e plano de dados

As operações do Azure podem ser divididas em duas categorias - plano de controlo e plano de dados. Este artigo descreve as diferenças entre estes dois tipos de operações.

Usa o avião de controlo para gerir recursos na sua subscrição. Utiliza o plano de dados para utilizar as capacidades expostas pelo seu tipo de recurso.

Por exemplo:

* Cria-se uma máquina virtual através do plano de controlo. Após a criação da máquina virtual, interage com ela através de operações de plano de dados, como o Remote Desktop Protocol (RDP).

* Cria-se uma conta de armazenamento através do avião de controlo. Utiliza o plano de dados para ler e escrever dados na conta de armazenamento.

* Cria-se uma base de dados cosmos através do plano de controlo. Para consultar dados na base de dados do Cosmos, utilize o plano de dados.

## <a name="control-plane"></a>Plano de controlo

Todos os pedidos de operações de avião de controlo são enviados para o URL do Gestor de Recursos Azure. Esta URL varia em comparação com o ambiente Azure.

* Para a Azure global, a URL é `https://management.azure.com` .
* Para o Governo de Azure, a URL é `https://management.usgovcloudapi.net/` .
* Para a Azure Germany, a URL é `https://management.microsoftazure.de/` .
* Para o Microsoft Azure China 21Vianet, o URL é `https://management.chinacloudapi.cn` .

Para descobrir quais as operações que utilizam o URL do Gestor de Recursos Azure, consulte a [API AZURE REST](/rest/api/azure/). Por exemplo, a [operação de criação ou atualização](/rest/api/mysql/databases/createorupdate) para o MySql é uma operação de plano de controlo porque o URL de pedido é:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

O Gestor de Recursos Azure trata de todos os pedidos do avião de controlo. Aplica automaticamente as funcionalidades Azure que implementou para gerir os seus recursos, tais como:

* [Controlo de acesso baseado em funções do Azure (RBAC do Azure)](../../role-based-access-control/overview.md)
* [Azure Policy](../../governance/policy/overview.md)
* [Bloqueios de Gestão](lock-resources.md)
* [Registos de Atividade](view-activity-logs.md)

Após autenticação do pedido, o Azure Resource Manager envia-o ao fornecedor de recursos, que completa a operação.

O plano de controlo inclui dois cenários para tratamento de pedidos - "campo verde" e "campo castanho". Campo verde refere-se a novos recursos. Brown field refere-se aos recursos existentes. Ao mobilizar recursos, o Azure Resource Manager compreende quando criar novos recursos e quando atualizar os recursos existentes. Não tens de te preocupar que serão criados recursos idênticos.

## <a name="data-plane"></a>Plano de dados

Os pedidos de operações de data plane são enviados para um ponto final específico da sua instância. Por exemplo, a [operação Detet em Serviços](/rest/api/cognitiveservices/textanalytics/detect%20language/detect%20language) Cognitivos é uma operação de plano de dados porque o URL de pedido é:

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

As operações de avião de dados não se limitam à API REST. Podem necessitar de credenciais adicionais, como iniciar sessão numa máquina virtual ou servidor de bases de dados.

Características que impõem a gestão e a governação podem não ser aplicadas às operações de data plane. Tem de considerar as diferentes formas como os utilizadores interagem com as suas soluções. Por exemplo, um bloqueio que impede os utilizadores de eliminar uma base de dados não impede que os utilizadores apaguem dados através de consultas.

Pode usar algumas políticas para governar as operações de data plane. Para obter mais informações, consulte os [modos fornecedores de recursos (pré-visualização) na Política Azure](../../governance/policy/concepts/definition-structure.md#resource-provider-modes).

## <a name="next-steps"></a>Passos seguintes

* Para uma visão geral do Azure Resource Manager, consulte [o que é O Gestor de Recursos Azure?](overview.md)

* Para saber mais sobre o efeito das definições políticas sobre novos recursos e recursos existentes., consulte [avaliar o impacto de uma nova definição da Política Azure.](../../governance/policy/concepts/evaluate-impact.md)
