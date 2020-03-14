---
title: Gerir funções de AD Azure na Gestão de Identidade Privilegiada (PIM)  Microsoft Docs
description: Como gerir funções da Azure AD para a atribuição de Gestão de Identidade Privilegiada (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5d63577cbdbdf18cb17618bdb5c9e3b5de0e44a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245983"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Capacidades de gestão para funções da Azure AD na Gestão de Identidade Privilegiada

A experiência de gestão para funções da Azure AD na Privileged Identity Management foi atualizada para unificar a forma como as funções da Azure AD e os recursos Azure são geridos. Anteriormente, a Privileged Identity Management para funções de recurso Azure teve algumas funcionalidades-chave que não estavam disponíveis para funções de AD Azure.

Com a atualização a ser lançada, estamos a fundir os dois numa única experiência de gestão, e nela obtém-se a mesma funcionalidade para funções de AD Azure que para as funções de recurso Azure. Este artigo informa-o das funcionalidades atualizadas e de quaisquer requisitos.


## <a name="time-bound-assignments"></a>Atribuição com limite de tempo

Anteriormente em Gestão de Identidade Privilegiada para funções de AD Azure, estava familiarizado com atribuições de funções com dois estados possíveis – *elegíveis* e *permanentes.* Agora pode definir um tempo de início e fim para cada tipo de atribuição. Esta adição dá-lhe quatro estados possíveis em que pode colocar uma atribuição:

- Elegíveis permanentemente
- Ativo permanentemente
- Elegíveis, com datas de início/fim especificadas para a atribuição
- Ativo, com datas de início/fim especificadas para a atribuição

Em muitos casos, mesmo que não queira que os utilizadores tenham funções de atribuição e ativação elegíveis sempre, ainda pode proteger a sua organização Azure AD, estabelecendo um prazo de validade para atribuições. Por exemplo, se tiver alguns utilizadores temporários elegíveis, considere definir uma expiração para removê-los automaticamente da atribuição de funções quando o seu trabalho estiver concluído.

## <a name="new-role-settings"></a>Novas definições de funções

Estamos também a adicionar novas definições para funções de AD Azure. Anteriormente, só podia configurar as definições de ativação numa base por função. Ou seja, foram aplicadas a todos os utilizadores elegíveis para uma função especificada as definições de ativação, tais como requisitos de autenticação de vários fatores e requisitos de bilhete sacar/solicitar. Agora, pode configurar se um utilizador individual precisa de realizar a autenticação de vários fatores antes de poder ativar uma função. Além disso, pode ter um controlo avançado sobre os seus e-mails de Gestão de Identidade Privilegiada relacionados com funções específicas.

## <a name="extend-and-renew-assignments"></a>Alargar e renovar atribuições

Assim que descobrires a atribuição do tempo, a primeira pergunta que podes fazer é o que acontece se um papel expirar? Nesta nova versão, disponibilizamos duas opções para este cenário:

- Extensão – Quando uma atribuição de funções se aproxima da sua expiração, o utilizador pode utilizar a Gestão de Identidade Privilegiada para solicitar uma extensão para essa atribuição de funções
- Renovar – Quando uma atribuição de funções tiver expirado, o utilizador pode utilizar a Gestão de Identidade Privilegiada para solicitar uma renovação para essa atribuição de funções

Ambas as ações iniciadas pelo utilizador requerem a aprovação de um administrador global ou administrador de funções privilegiado. Os administradores deixarão de ter de estar no negócio da gestão destas caducidades. Só precisam de esperar pelos pedidos de extensão ou renovação e aprová-los se o pedido for válido.

## <a name="api-changes"></a>Alterações da API

Quando os clientes tiverem a versão atualizada lançada para a sua organização Azure AD, o gráfico existente API deixará de funcionar. É necessário fazer a transição para utilizar a [API do gráfico para funções](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta)de recurso Azure . Para gerir as funções da AD Azure utilizando essa API, troque `/azureResources` com `/aadroles` na assinatura e utilize o ID do Diretório para o `resourceId`.

Temos tentado chegar ao nosso melhor para contactar todos os clientes que estão a usar a API anterior para que saibam sobre esta mudança com antecedência. Se a sua organização Azure AD foi transferida para a nova versão e ainda depende da antiga API, contacte a equipa em pim_preview@microsoft.com.

## <a name="powershell-change"></a>Mudança PowerShell

Para os clientes que estão a utilizar o módulo PowerShell de Gestão de Identidade Privilegiada para funções Azure AD, o PowerShell deixará de trabalhar com a atualização. No lugar dos cmdlets anteriores deve utilizar os cmdlets de Gestão de Identidade Privilegiada dentro do módulo PowerShell de pré-visualização Azure AD. Instale o módulo PowerShell Azure AD a partir da [Galeria PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Pode agora [ler a documentação e as amostras para operações pim neste módulo PowerShell](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Passos seguintes

- [Atribuir uma função personalizada azure AD](azure-ad-custom-roles-assign.md)
- [Remova ou atualize uma atribuição de funções personalizadas da AD Azure](azure-ad-custom-roles-update-remove.md)
- [Configure uma atribuição de funções personalizadas da Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de papéis em Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
