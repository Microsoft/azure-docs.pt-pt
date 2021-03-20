---
title: Permissões de consentimento de aplicativos para funções personalizadas no Azure Ative Directory | Microsoft Docs
description: Pré-visualizar permissões de consentimento da aplicação para funções Azure AD personalizadas no portal Azure, PowerShell ou API de gráfico.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: psignoret
ms.custom: it-pro
ms.openlocfilehash: f9c2c15bbfcf9a9271e629ef26c11ecc4cbaaa6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98740113"
---
# <a name="app-consent-permissions-for-custom-roles-in-azure-active-directory"></a>Permissões de consentimento de aplicativos para funções personalizadas no Azure Ative Directory

Este artigo contém as permissões de consentimento de aplicações atualmente disponíveis para definições de funções personalizadas no Azure Ative Directory (Azure AD). Neste artigo, encontrará as permissões necessárias para alguns cenários comuns relacionados com o consentimento da aplicação e permissões.

## <a name="required-license-plan"></a>Plano de licença exigido

A utilização desta funcionalidade requer uma licença Azure AD Premium P1 para a sua organização Azure AD. Para encontrar a licença certa para os seus requisitos, veja [Comparação das funcionalidades disponíveis geralmente das edições Gratuita, Básica e Premium](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="app-consent-permissions"></a>Permissões de consentimento da aplicação

Utilize as permissões listadas neste artigo para gerir as políticas de consentimento da aplicação, bem como a permissão para conceder consentimento às apps.

> [!NOTE]
> O portal de administração Azure AD ainda não suporta a adição das permissões enumeradas neste artigo a uma definição de papel de diretório personalizado. Você deve [usar Azure AD PowerShell para criar um papel de diretório personalizado](custom-create.md#create-a-role-using-powershell) com as permissões listadas neste artigo.

### <a name="granting-delegated-permissions-to-apps-on-behalf-of-self-user-consent"></a>Concessão de permissões delegadas a apps em nome de si próprio (consentimento do utilizador)

Para permitir que os utilizadores concedam consentimento a aplicações em nome de si próprios (consentimento do utilizador), sujeito a uma política de consentimento da aplicação.

- microsoft.diretório/serviçoPrincipals/managePermissionGrantsForSelf. {id}

Quando `{id}` for substituído pelo ID de uma política de consentimento de [aplicações](../manage-apps/manage-app-consent-policies.md) que definirá as condições que devem ser cumpridas para que esta permissão seja ativa.

Por exemplo, para permitir que os utilizadores concedam o consentimento em seu próprio nome, sujeito à política de consentimento de aplicações incorporada com `microsoft-user-default-low` ID, você usaria a permissão `...managePermissionGrantsForSelf.microsoft-user-default-low` .

### <a name="granting-permissions-to-apps-on-behalf-of-all-admin-consent"></a>Concessão de permissões a apps em nome de todos (consentimento administrativo)

Para delegar o consentimento administrativo do inquilino para apps, tanto para permissões delegadas como permissões de aplicação (funções de aplicação):

- microsoft.diretório/serviçoPrincipals/managePermissionGrantsForAll. {id}

Quando `{id}` for substituído pela ID de uma política de consentimento de [aplicações](../manage-apps/manage-app-consent-policies.md) que definirá as condições que devem ser cumpridas para que esta permissão seja utilizável.

Por exemplo, para permitir que os atribui-se a funções para conceder o consentimento administrativo a aplicações sujeitas a uma política de consentimento de [aplicações](../manage-apps/manage-app-consent-policies.md) personalizadas com ID, `low-risk-any-app` você usaria a permissão `microsoft.directory/servicePrincipals/managePermissionGrantsForAll.low-risk-any-app` .

### <a name="managing-app-consent-policies"></a>Gerir políticas de consentimento de aplicações

Para delegar a criação, atualização e eliminação das políticas de consentimento de [aplicações.](../manage-apps/manage-app-consent-policies.md)

- microsoft.diretório/permissãoSpolias/criar
- microsoft.diretório/permissãoSpolíticas/standard/read
- microsoft.diretório/permissãoSpolíticas/básico/atualização
- microsoft.diretório/permissãoSpolíticas/excluir

## <a name="full-list-of-permissions"></a>Lista completa de permissões

Permissão | Description
---------- | -----------
microsoft.diretório/serviçoPrincipals/managePermissionGrantsForSelf. {id} | Concede a capacidade de consentir com apps em nome de si próprio (consentimento do utilizador), sujeito à política de consentimento da `{id}` aplicação.
microsoft.diretório/serviçoPrincipals/managePermissionGrantsForAll. {id} | Concede a permissão para consentir com aplicações em nome de todos (consentimento administrativo em todo o inquilino), sujeito à política de consentimento da `{id}` app.
microsoft.diretório/permissãoSpolíticas/standard/read | Concede a capacidade de ler políticas de consentimento de aplicativos.
microsoft.diretório/permissãoSpolíticas/básico/atualização | Concede a capacidade de atualizar propriedades básicas sobre as políticas de consentimento de aplicações existentes.
microsoft.diretório/permissãoSpolias/criar | Concede a capacidade de criar políticas de consentimento de aplicativos.
microsoft.diretório/permissãoSpolíticas/excluir | Concede a capacidade de eliminar políticas de consentimento de aplicações.

## <a name="next-steps"></a>Passos seguintes

- [Crie funções personalizadas utilizando o portal Azure, Azure AD PowerShell e Graph API](custom-create.md)
- [Ver as atribuições para um papel personalizado](../roles/view-assignments.md)
