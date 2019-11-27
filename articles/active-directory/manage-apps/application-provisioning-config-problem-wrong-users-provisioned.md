---
title: Um conjunto incorreto de usuários é provisionado para um aplicativo da galeria do Azure AD
description: Saiba como descobrir por que um conjunto diferente de usuários está sendo provisionado para um aplicativo do que aqueles que você esperava
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
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77e8f31bd0adeabea79cd11553fd77a3e5ab6404
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275778"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Um conjunto incorreto de usuários está sendo provisionado para um aplicativo da galeria do Azure AD

Quais usuários são provisionados para o aplicativo é orientado principalmente por quais usuários e grupos foram **atribuídos** ao aplicativo.

Use os recursos a seguir para saber como verificar quais usuários e grupos foram atribuídos a um aplicativo no Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Atribuir um usuário diretamente como um administrador

Para atribuir um ou mais usuários a um aplicativo diretamente, siga estas etapas:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. clique em **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

   * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Depois que o aplicativo for carregado, clique em **usuários e grupos** no menu de navegação esquerdo do aplicativo.

8. Para abrir o painel **Adicionar atribuição** , clique no botão **Adicionar** na parte superior da lista **usuários e grupos** .

9. Clique no seletor **usuários e grupos** no painel **Adicionar atribuição** .

10. Digite o **nome completo** ou o **endereço de email** do usuário que você está interessado em atribuir à caixa de pesquisa **Pesquisar por nome ou endereço de email** .

11. Passe o mouse sobre o **usuário** na lista para revelar uma **caixa de seleção**. Clique na caixa de seleção ao lado da foto ou do logotipo do perfil do usuário para adicionar o usuário à lista **selecionada** .

12. **Opcional:** Se você quiser **adicionar mais de um usuário**, digite outro **nome completo** ou **endereço de email** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na CheckBox para adicionar esse usuário à lista **selecionada** .

13. Quando terminar de selecionar usuários, clique no botão **selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **selecionar função** no painel **Adicionar atribuição** para selecionar uma função a ser atribuída aos usuários selecionados.

15. Clique no botão **atribuir** para atribuir o aplicativo aos usuários selecionados.

Se o provisionamento estiver configurado e já estiver em execução para um aplicativo, novos usuários deverão ser provisionados para um aplicativo em aproximadamente 10 minutos. Verifique os **logs de auditoria** para obter detalhes.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Atribuir um grupo diretamente a um aplicativo como administrador

Para atribuir um ou mais grupos a um aplicativo diretamente, siga estas etapas:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. clique em **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

   * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Depois que o aplicativo for carregado, clique em **usuários e grupos** no menu de navegação esquerdo do aplicativo.

8. Para abrir o painel **Adicionar atribuição** , clique no botão **Adicionar** na parte superior da lista **usuários e grupos** .

9. Clique no seletor **usuários e grupos** no painel **Adicionar atribuição** .

10. Digite o **nome completo** do grupo que você está interessado em atribuir à caixa de pesquisa **Pesquisar por nome ou endereço de email** .

11. Passe o mouse sobre o **grupo** na lista para revelar uma **caixa de seleção**. Clique na caixa de seleção ao lado do logotipo ou foto de perfil do grupo para adicionar o usuário à lista **selecionada** .

12. **Opcional:** Se você quiser **adicionar mais de um grupo**, digite outro **nome de grupo completo** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na CheckBox para adicionar esse grupo à lista **selecionada** .

13. Quando terminar de selecionar grupos, clique no botão **selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **selecionar função** no painel **Adicionar atribuição** para selecionar uma função a ser atribuída aos grupos selecionados.

15. Clique no botão **atribuir** para atribuir o aplicativo aos grupos selecionados.

Se o provisionamento estiver configurado e já estiver em execução para um aplicativo, novos usuários contidos no grupo deverão ser provisionados para um aplicativo em aproximadamente 10 minutos. Verifique os **logs de auditoria** para obter detalhes.

>[!IMPORTANT]
>Provisionamento do nome do grupo e dos detalhes do grupo, além dos membros, se houver suporte para alguns aplicativos. Você pode habilitar ou desabilitar essa funcionalidade habilitando ou desabilitando o **mapeamento** para objetos de grupo mostrados na guia **provisionamento** . 
>
>

Se o provisionamento de grupos estiver habilitado, certifique-se de examinar os mapeamentos de atributo para garantir que um campo apropriado esteja sendo usado para a "ID correspondente". Essa ID de correspondência pode ser o nome de exibição ou alias de email. O grupo e seus membros não serão provisionados se a propriedade correspondente estiver vazia ou não for preenchida para um grupo no Azure AD.

## <a name="next-steps"></a>Passos seguintes
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatizar o aprovisionamento e o desaprovisionamento de utilizadores em Aplicações SaaS com o Azure Active Directory)
