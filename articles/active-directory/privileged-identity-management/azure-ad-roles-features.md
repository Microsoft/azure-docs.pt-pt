---
title: Gerenciando funções do Azure AD no Privileged Identity Management (PIM) | Microsoft Docs
description: Como gerenciar funções do Azure AD para Privileged Identity Management de atribuição (PIM)
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
ms.openlocfilehash: 69c633dd3e28047d542e16a58211f997669188f0
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896423"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Recursos de gerenciamento para funções do Azure AD no Privileged Identity Management

A experiência de gerenciamento das funções do Azure AD no Privileged Identity Management foi atualizada para unificar a forma como as funções do Azure AD e as funções de recurso do Azure são gerenciadas. Anteriormente, Privileged Identity Management para as funções de recursos do Azure tinham alguns recursos principais que não estavam disponíveis para as funções do Azure AD.

Com a atualização sendo distribuída no momento, estamos mesclando as duas em uma única experiência de gerenciamento e, nela, você obtém a mesma funcionalidade para as funções do Azure AD como as funções de recursos do Azure. Este artigo informa sobre os recursos atualizados e quaisquer requisitos.

## <a name="time-bound-assignments"></a>Atribuições de limite de tempo

Anteriormente, em Privileged Identity Management para as funções do Azure AD, você estava familiarizado com as atribuições de função com dois Estados possíveis – *elegíveis* e *permanentes*. Agora você pode definir uma hora de início e de término para cada tipo de atribuição. Essa adição fornece quatro possíveis Estados nos quais você pode fazer uma atribuição:

- Qualificado permanentemente
- Ativo permanentemente
- Qualificado, com datas de início/término especificadas para atribuição
- Ativo, com datas de início/término especificadas para atribuição

Em muitos casos, mesmo que você não queira que os usuários tenham funções qualificadas de atribuição e ativação todas as vezes, você ainda pode proteger sua organização do Azure AD definindo um tempo de expiração para atribuições. Por exemplo, se você tiver alguns usuários temporários qualificados, considere definir uma expiração para removê-los automaticamente da atribuição de função quando seu trabalho for concluído.

## <a name="new-role-settings"></a>Novas configurações de função

Também estamos adicionando novas configurações para as funções do Azure AD. Anteriormente, você podia definir apenas as configurações de ativação por função. Ou seja, as configurações de ativação, como requisitos de autenticação multifator e requisitos de incidente/solicitação de tíquete, foram aplicadas a todos os usuários qualificados para uma função especificada. Agora, você pode configurar se um usuário individual precisa executar a autenticação multifator antes de poder ativar uma função. Além disso, você pode ter controle avançado sobre seus emails de Privileged Identity Management relacionados a funções específicas.

## <a name="extend-and-renew-assignments"></a>Estender e renovar atribuições

Assim que você descobrir a atribuição de tempo limite, a primeira pergunta que você pode fazer é o que acontece se uma função tiver expirado? Nesta nova versão, fornecemos duas opções para este cenário:

- Estender – quando uma atribuição de função se aproxima de sua expiração, o usuário pode usar Privileged Identity Management para solicitar uma extensão para essa atribuição de função
- Renovar – quando uma atribuição de função tiver expirado, o usuário poderá usar Privileged Identity Management para solicitar uma renovação para essa atribuição de função

As duas ações iniciadas pelo usuário exigem uma aprovação de um administrador global ou de um administrador de função com privilégios. Os administradores não precisarão mais estar no negócio de gerenciar essas expirações. Eles só precisam aguardar as solicitações de extensão ou renovação e aprová-las se a solicitação for válida.

## <a name="api-changes"></a>Alterações de API

Quando os clientes tiverem a versão atualizada distribuída para sua organização do Azure AD, a API do Graph existente deixará de funcionar. Você deve fazer a transição para usar o [API do Graph para funções de recurso do Azure](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta). Para gerenciar funções do Azure AD usando essa API, troque `/azureResources` por `/aadroles` na assinatura e use a ID de diretório para o `resourceId`.

Nós experimentamos o nosso melhor para entrar em contato com todos os clientes que estão usando a API anterior para que eles saibam sobre essa alteração antes do tempo. Se a sua organização Azure AD foi transferida para a nova versão e ainda depende da antiga API, contacte a equipa em pim_preview@microsoft.com.

## <a name="powershell-change"></a>Alteração do PowerShell

Para clientes que estão usando o módulo Privileged Identity Management PowerShell para funções do Azure AD, o PowerShell deixará de funcionar com a atualização. No lugar dos cmdlets anteriores, você deve usar os cmdlets Privileged Identity Management dentro do módulo PowerShell de visualização do Azure AD. Instale o módulo do PowerShell do Azure AD do [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Os cmdlets são criados com base em API do Graph.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Remover ou atualizar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de função no Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
