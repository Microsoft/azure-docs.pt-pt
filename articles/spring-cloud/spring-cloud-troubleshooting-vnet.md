---
title: Resolução de problemas Azure Spring Cloud em rede virtual
description: Guia de resolução de problemas para a rede virtual Azure Spring Cloud.
author: mikedodaro
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b2369a6380c7b74302d32366d0604fca616fc3ed
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698234"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>Resolução de problemas Azure Spring Cloud em redes virtuais

Este documento irá ajudá-lo a resolver vários problemas que podem surgir ao utilizar o Azure Spring Cloud em redes virtuais.

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Deparei-me com um problema na criação de um caso de serviço Azure Spring Cloud

Para criar um exemplo de Azure Spring Cloud, você deve ter permissão suficiente para implementar o caso para a rede virtual.  A instância de serviço da Cloud spring deve conceder a [permissão de serviço Azure Spring Cloud para a rede virtual.](spring-cloud-tutorial-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)

Se utilizar o portal Azure para configurar a instância de serviço Azure Spring Cloud, o portal Azure validará as permissões.

Para configurar a instância de serviço Azure Spring Cloud utilizando o [CLI Azure,](/cli/azure/get-started-with-azure-cli)verifique se:

- A subscrição está ativa.
- A localização é suportada por Azure Spring Cloud.
- O grupo de recursos, por exemplo, já está criado.
- O nome do recurso está em conformidade com a regra de nomeação. Deve conter apenas letras minúsculas, números e hífenes. O primeiro caráter tem de ser uma letra. O último caráter tem de ser uma letra ou um número. O valor deve conter de 2 a 32 caracteres.

Para configurar a instância de serviço Azure Spring Cloud utilizando o modelo de Gestor de Recursos, consulte [para Compreender a estrutura e sintaxe dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/template-syntax.md).

### <a name="common-creation-issues"></a>Questões comuns de criação

| Mensagem de Erro | Como corrigir |
|------|------|
| Os recursos criados por Azure Spring Cloud foram proibidos pela política. | Os recursos de rede serão criados quando implementar a Azure Spring Cloud na sua própria rede virtual. Por favor, verifique se tem [a Política Azure](../governance/policy/overview.md) definida para bloquear essas criações. Os recursos não criados podem ser encontrados numa mensagem de erro. |
| Desde que as sub-redes tenham associado às tabelas de rotas, desassociem-nas. | Atualmente não é suportado para implantar Azure Spring Cloud na sub-rede associada às tabelas de rotas existentes, por favor dissociá-las e tente novamente. |
| O tráfego exigido não é permitido na lista. | Consulte as [Responsabilidades do Cliente para executar a nuvem de mola Azure em VNET](spring-cloud-vnet-customer-responsibilities.md) para garantir que o tráfego necessário é permitido. |

## <a name="my-application-cant-be-registered"></a>O meu pedido não pode ser registado.

Este problema ocorre se a sua rede virtual estiver configurada com configurações de DNS personalizadas. Neste caso, a zona privada de DNS utilizada pela Azure Spring Cloud é ineficaz. Adicione o Azure DNS IP 168.63.129.16 como o servidor DNS a montante no servidor DNS personalizado.

## <a name="other-issues"></a>Outros problemas

[Resolução de problemas problemas comuns Azure Spring Cloud .](./spring-cloud-troubleshoot.md)