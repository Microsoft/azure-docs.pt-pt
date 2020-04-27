---
title: Como remover o acesso de um utilizador a uma aplicação [ Microsoft Docs
description: Entenda como remover o acesso de um utilizador a uma aplicação
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b69502995eff88df53af3671a8e611809f83e59
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "65826106"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Como remover o acesso de um utilizador a uma aplicação

Este artigo ajuda-o a compreender como remover o acesso de um utilizador a uma aplicação.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Quero remover a atribuição de um utilizador ou grupo específico a uma aplicação

Para remover uma atribuição de utilizador ou grupo a uma aplicação, siga os passos listados no [Remove um utilizador ou atribuição de grupo de uma aplicação empresarial no artigo do Diretório Ativo do Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Quero desativar todo o acesso a uma aplicação para cada utilizador

Para desativar todos os registos de utilizadores numa aplicação, siga os passos listados nos registos de [utilizador desativados para uma aplicação empresarial no artigo do Diretório Ativo do Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)

## <a name="i-want-to-delete-an-application-entirely"></a>Quero apagar uma aplicação inteiramente

Para **eliminar uma aplicação,** siga estas instruções:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. Clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Diretório Ativo Azure.

5. Clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende eliminar.

7. Assim que a aplicação estiver carregada, clique em **Eliminar** o ícone do painel de **visão geral** da aplicação superior.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Quero desativar todas as futuras operações de consentimento do utilizador em qualquer aplicação

Desativar o consentimento do utilizador para todo o seu diretório impede que os utilizadores finais consintam em qualquer aplicação. Os administradores ainda podem consentir em nome do utilizador. Para mais informações sobre o consentimento da aplicação e por que razão pode ou não desejar fazê-lo, leia o consentimento do [utilizador e da administração.](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent) Consulte também, [Permissões e consentimento.](../develop/v2-permissions-and-consent.md)

Para **desativar todas as futuras operações**de consentimento do utilizador em todo o seu diretório, siga estas instruções:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2.  Abra a **extensão do diretório ativo azure** 

3.  Clique em **aplicações da Enterprise** no menu de navegação.

5.  Clique nas **definições do Utilizador**.

6.  O conjunto de **Utilizadores pode permitir que as aplicações acedam aos dados** da empresa em seu nome, alternando para **No** e clique no botão Guardar.


## <a name="next-steps"></a>Passos seguintes

[Gerir o acesso a apps](what-is-access-management.md)
