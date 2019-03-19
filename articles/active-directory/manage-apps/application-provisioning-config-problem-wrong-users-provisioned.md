---
title: Conjunto errado de utilizadores estão a ser aprovisionados para um aplicativo de galeria do Azure AD | Documentos da Microsoft
description: Saiba como pode descobrir por que um conjunto diferente dos utilizadores estão a ser aprovisionados para um aplicativo que aquelas que o esperado
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
ms.date: 09/20/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a68a4caf3d32aa88a61a8e1639bdd08d67599d4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083305"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Conjunto errado de utilizadores estão a ser aprovisionados para um aplicativo de galeria do Azure AD

Os utilizadores que são aprovisionados para a aplicação é orientado basicamente pelos utilizadores e grupos foram **atribuídos** à aplicação.

Utilize os seguintes recursos para saber como verificar quais os utilizadores e grupos foram atribuídos a uma aplicação no Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Atribuir um utilizador diretamente como um administrador

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Assim que o aplicativo é carregado, clique em **utilizadores e grupos** no menu de navegação do lado esquerdo da aplicação.

8. Para abrir o **adicionar atribuição** painel, clique nas **Add** botão na parte superior do **utilizadores e grupos** lista.

9. Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de e-mail** do utilizador estiver interessado em atribuir para o **procurar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **usuário** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do usuário ou a logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Opcional:** Se quiser **adicionar mais de um usuário**, tipo em outro **nome completo** ou **endereço de e-mail** para o **procurar por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos utilizadores selecionados.

Se o aprovisionamento está configurado e já em execução para uma aplicação, os novos utilizadores devem ser aprovisionados para um aplicativo em aproximadamente 10 minutos. Verifique os **registos de auditoria** para obter detalhes.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Atribuir um grupo diretamente a uma aplicação como administrador

Para atribuir um ou mais grupos diretamente a uma aplicação, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Assim que o aplicativo é carregado, clique em **utilizadores e grupos** no menu de navegação do lado esquerdo da aplicação.

8. Para abrir o **adicionar atribuição** painel, clique nas **Add** botão na parte superior do **utilizadores e grupos** lista.

9. Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome do grupo completo** do grupo estiver interessado em atribuir para o **pesquisar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **grupo** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil ou à logótipo para adicionar o utilizador para o grupo de **selecionados** lista.

12. **Opcional:** Se quiser **adicionar mais de um grupo**, tipo em outro **nome do grupo completo** no **procurar por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este grupo para o **selecionados** lista.

13. Quando tiver terminado de selecionar os grupos, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos grupos que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos grupos selecionados.

Se o aprovisionamento está configurado e já em execução para uma aplicação, os novos utilizadores contidos no grupo devem ser aprovisionados para um aplicativo em aproximadamente 10 minutos. Verifique os **registos de auditoria** para obter detalhes.

>[!IMPORTANT]
>Aprovisionamento do nome do grupo e detalhes do grupo, além de membros, se for suportado para alguns aplicativos. É possível habilitar ou desabilitar essa funcionalidade ativando ou desativando a **mapeamento** para objetos de grupo, mostrados na **aprovisionamento** separador. 
>
>

Se o provisionamento de grupos estiver ativado, certifique-se de que reveja os mapeamentos de atributos para garantir que um campo adequado, está a ser utilizado para o ID"correspondente". Este ID correspondente pode ser o alias de e-mail ou nome de exibição. O grupo e seus membros não são aprovisionados se a propriedade correspondente estiver vazio ou não preenchida para um grupo no Azure AD.

## <a name="next-steps"></a>Passos Seguintes
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatizar o aprovisionamento e o desaprovisionamento de utilizadores em Aplicações SaaS com o Azure Active Directory)
