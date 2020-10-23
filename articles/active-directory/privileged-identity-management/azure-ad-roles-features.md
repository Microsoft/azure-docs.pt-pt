---
title: Azure AD tem funções na Gestão de Identidade Privilegiada Microsoft Docs
description: Como gerir as funções de Azure AD para a atribuição de Gestão de Identidade Privilegiada (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 07/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4478c9c286c06d5d6c5593195a0e93abd286b8c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371516"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Capacidades de gestão para funções de Ad Azure em Gestão de Identidade Privilegiada

A experiência de gestão das funções de Azure AD na Gestão de Identidade Privilegiada foi atualizada para unificar a forma como as funções de AZure AD e os recursos Azure são geridos. Anteriormente, a Gestão de Identidade Privilegiada para funções de recursos Azure teve um par de funcionalidades-chave que não estavam disponíveis para funções Azure AD.

Com a atualização a ser lançada atualmente, estamos a fundir os dois numa única experiência de gestão, e nele obtém-se a mesma funcionalidade para os papéis de Ad Azure como para funções de recursos Azure. Este artigo informa-o sobre as funcionalidades atualizadas e quaisquer requisitos.

## <a name="time-bound-assignments"></a>Atribuições com prazos

Anteriormente, existiam dois Estados possíveis para atribuições de funções: *elegíveis* e *permanentes.* Agora também pode definir um tempo de início e fim para cada tipo de atribuição. Esta adição dá-lhe quatro estados possíveis nos quais pode colocar uma missão:

- Elegível permanentemente
- Ativo permanentemente
- Elegíveis, com datas de início e fim especificadas para a atribuição
- Ativo, com datas de início e fim especificadas para a atribuição

Em muitos casos, mesmo que não queira que os utilizadores tenham uma atribuição elegível e ativar funções de cada vez, ainda pode proteger a sua organização Azure AD, definindo um prazo de validade para as atribuições. Por exemplo, se tiver alguns utilizadores temporários que sejam elegíveis, considere definir uma expiração para removê-los automaticamente da atribuição de funções quando o seu trabalho estiver concluído.

## <a name="new-role-settings"></a>Novas definições de funções

Também estamos adicionando novas configurações para os papéis AZURE AD.

- **Anteriormente,** só era possível configurar as definições de ativação por função. Ou seja, foram aplicadas a todos os utilizadores elegíveis para uma função específica as definições de ativação, tais como requisitos de autenticação de vários fatores e requisitos de bilhetes de incidente/pedido.
- **Agora,** pode configurar se um utilizador individual precisa de realizar a autenticação de vários fatores antes de poder ativar uma função. Além disso, pode ter um controlo avançado sobre os seus e-mails de Gestão de Identidade Privilegiada relacionados com funções específicas.

## <a name="extend-and-renew-assignments"></a>Alargar e renovar atribuições

Assim que descobrires a atribuição de tempo, a primeira pergunta que podes fazer é o que acontece se um papel expirar? Nesta nova versão, oferecemos duas opções para este cenário:

- **Prorrogação**: Quando uma atribuição de funções se aproxima da sua expiração, o utilizador pode utilizar a Gestão de Identidade Privilegiada para solicitar uma extensão para essa atribuição de funções
- **Renovar:** Quando uma atribuição de funções tiver expirado, o utilizador pode utilizar a Gestão de Identidade Privilegiada para solicitar uma renovação para essa atribuição de funções

Ambas as ações iniciadas pelo utilizador requerem uma aprovação de um administrador global ou administrador privilegiado. Os administradores deixarão de precisar de estar no negócio de gerir estas caducidades. Só precisam de aguardar os pedidos de extensão ou renovação e aprová-los se o pedido for válido.

## <a name="api-changes"></a>Alterações na API

Quando os clientes tiverem a versão atualizada lançada para a sua organização AZure AD, a API de gráfico existente deixará de funcionar. Tem de fazer a transição para utilizar a [API do gráfico para funções de recurso Azure](/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta). Para gerir as funções de AD Azure utilizando essa API, troque `/azureResources` com a assinatura e utilize o `/aadroles` ID do Diretório para o `resourceId` .

Tentámos dar o nosso melhor para chegar a todos os clientes que estão a usar a API anterior para os informar sobre esta mudança com antecedência. Se a sua organização AZure AD foi transferida para a nova versão e ainda depende da antiga API, contacte a equipa em pim_preview@microsoft.com .

## <a name="powershell-change"></a>Mudança powerShell

Para os clientes que estão a utilizar o módulo PowerShell de Gestão de Identidade Privilegiada para funções AD Azure, o PowerShell deixará de trabalhar com a atualização. No lugar dos cmdlets anteriores, deve utilizar os cmdlets de Gestão de Identidade Privilegiada dentro do módulo PowerShell de pré-visualização Azure AD. Instale o módulo Azure AD PowerShell da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Pode agora [ler a documentação e as amostras para as operações pim neste módulo PowerShell](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Passos seguintes

- [Atribuir um papel personalizado AZure AD](azure-ad-custom-roles-assign.md)
- [Remova ou atualize uma atribuição de função personalizada Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configure uma atribuição de função personalizada Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de função em Azure AD](../roles/permissions-reference.md)