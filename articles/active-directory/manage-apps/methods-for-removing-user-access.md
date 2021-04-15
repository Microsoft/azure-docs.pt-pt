---
title: Como remover o acesso de um utilizador a uma aplicação no Azure Ative Directory
description: Compreender como remover o acesso de um utilizador a uma aplicação no Azure Ative Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: iangithinji
ms.openlocfilehash: 958abc5f9be443d66037a6d9fe8d8779e6e37e0e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379592"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Como remover o acesso de um utilizador a uma aplicação

Este artigo ajuda-o a entender como remover o acesso de um utilizador a uma aplicação.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Quero remover a atribuição de um utilizador ou grupo específico a uma aplicação

Para remover uma atribuição de utilizador ou grupo a uma aplicação, siga os passos listados na [atribuição de um utilizador ou grupo de uma aplicação da empresa no artigo do Azure Ative Directory.](./assign-user-or-group-access-portal.md)

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Quero desativar todo o acesso a uma aplicação para cada utilizador

Para desativar todas as insuposições do utilizador para uma aplicação, siga os [passos listados nas insusagens do utilizador de Disable para uma aplicação empresarial no artigo do Azure Ative Directory.](./disable-user-sign-in-portal.md)

## <a name="i-want-to-delete-an-application-entirely"></a>Quero apagar uma aplicação inteiramente

A [Série Quickstart em Gestão de Aplicações](delete-application-portal.md) inclui orientações sobre a eliminação de uma aplicação do seu inquilino Azure Ative Directory.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Quero desativar todas as futuras operações de consentimento do utilizador a qualquer aplicação

Desativar o consentimento do utilizador para todo o seu diretório impede que os utilizadores finais consintam em qualquer aplicação. Os administradores ainda podem consentir em nome do utilizador. Para obter mais informações sobre o consentimento da aplicação e por que pode ou não desejar fazê-lo, leia [o consentimento do utilizador e da administração.](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent) Consulte também, [Permissões e consentimento.](../develop/v2-permissions-and-consent.md)

Para **desativar todas as futuras operações de consentimento do utilizador em todo o seu diretório,** siga estas instruções:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **extensão do Diretório Ativo Azure** 

3.  Clique nas **aplicações da Enterprise** no menu de navegação.

5.  Clique nas **definições do Utilizador**.

6.  Definir os **Utilizadores pode permitir que as aplicações acedam aos dados da empresa em seu nome** para alternar para **No** e clicar no botão Guardar.


## <a name="next-steps"></a>Passos seguintes

[Gerir o acesso a aplicações](what-is-access-management.md)