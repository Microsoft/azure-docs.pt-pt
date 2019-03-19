---
title: Como remover o acesso de um utilizador a uma aplicação | Documentos da Microsoft
description: Aprenda a remover o acesso de um utilizador a uma aplicação
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: db9ac671b616e2b5af59c033d08768c9e572876f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105374"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Como remover o acesso de um utilizador a uma aplicação

Este artigo ajuda-o a compreender como remover o acesso de um utilizador a uma aplicação.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Quero remover a atribuição de um utilizador específico ou do grupo a uma aplicação

Para remover um utilizador ou a atribuição de grupo a uma aplicação, siga os passos apresentados no [remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artigo.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Quero desativar todos os acessos a uma aplicação para cada utilizador

Para desativar todos os utilizadores inícios de sessão a uma aplicação, siga os passos apresentados no [desativar utilizador-inícios de sessão de uma aplicação empresarial no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artigo.

## <a name="i-want-to-delete-an-application-entirely"></a>Quero eliminar uma aplicação totalmente

Para **eliminar uma aplicação**, siga estas instruções:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende eliminar.

7. Assim que o aplicativo é carregado, clique em **elimine** ícone da aplicação superior **descrição geral** painel.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Quero desativar todas as operações de consentimento de utilizador futuras para qualquer aplicação

Desativar o consentimento do utilizador para o diretório completo impedir que os utilizadores finais consentir qualquer aplicação. Os administradores ainda podem consentir em nome de utilizador. Para obter mais informações sobre a aplicação de consentimento e, por isso que pode ou não querer fazer isso, leia [consentimento de administrador e de utilizador de compreensão](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Consulte também [permissões e consentimento](../develop/v2-permissions-and-consent.md).

Para **desativar todas as operações de consentimento de utilizador futuras no seu diretório inteiro**, siga estas instruções:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Azure Active Directory** 

3.  Clique em **aplicações empresariais** no menu de navegação.

5.  Clique em **definições de utilizador**.

6.  Definir o **os utilizadores podem permitir que as aplicações aos dados da empresa em nome deles** alternar para **não** e clique no botão guardar.


## <a name="next-steps"></a>Passos Seguintes

[Gerir o acesso a aplicações](what-is-access-management.md)
