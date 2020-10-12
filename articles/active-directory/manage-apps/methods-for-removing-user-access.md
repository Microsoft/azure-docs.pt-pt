---
title: Como remover o acesso de um utilizador a uma aplicação Microsoft Docs
description: Compreender como remover o acesso de um utilizador a uma aplicação
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/17/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f9626c256755e2fce81b593d95b8680f4bb55ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763164"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Como remover o acesso de um utilizador a uma aplicação

Este artigo ajuda-o a entender como remover o acesso de um utilizador a uma aplicação.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Quero remover a atribuição de um utilizador ou grupo específico a uma aplicação

Para remover uma atribuição de utilizador ou grupo a uma aplicação, siga os passos listados na [atribuição de um utilizador ou grupo de uma aplicação da empresa no artigo do Azure Ative Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Quero desativar todo o acesso a uma aplicação para cada utilizador

Para desativar todas as insuposições do utilizador para uma aplicação, siga os [passos listados nas insusagens do utilizador de Disable para uma aplicação empresarial no artigo do Azure Ative Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)

## <a name="i-want-to-delete-an-application-entirely"></a>Quero apagar uma aplicação inteiramente

Para **eliminar uma aplicação,** siga estas instruções:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**

2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**

4. Clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.

5. Clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**

6. Selecione a aplicação que pretende eliminar.

7. Uma vez que a aplicação é carregada, clique em **Eliminar** o ícone do painel **de visão geral** da aplicação superior.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Quero desativar todas as futuras operações de consentimento do utilizador a qualquer aplicação

Desativar o consentimento do utilizador para todo o seu diretório impede que os utilizadores finais consintam em qualquer aplicação. Os administradores ainda podem consentir em nome do utilizador. Para obter mais informações sobre o consentimento da aplicação e por que pode ou não desejar fazê-lo, leia [o consentimento do utilizador e da administração.](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent) Consulte também, [Permissões e consentimento.](../develop/v2-permissions-and-consent.md)

Para **desativar todas as futuras operações de consentimento do utilizador em todo o seu diretório,** siga estas instruções:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **extensão do Diretório Ativo Azure** 

3.  Clique nas **aplicações da Enterprise** no menu de navegação.

5.  Clique nas **definições do Utilizador**.

6.  Definir os **Utilizadores pode permitir que as aplicações acedam aos dados da empresa em seu nome** para alternar para **No** e clicar no botão Guardar.


## <a name="next-steps"></a>Passos seguintes

[Gerir o acesso a apps](what-is-access-management.md)
