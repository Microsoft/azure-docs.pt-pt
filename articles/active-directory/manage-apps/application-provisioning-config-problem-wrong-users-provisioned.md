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

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Depois que o aplicativo for carregado, clique em **usuários e grupos** no menu de navegação esquerdo do aplicativo.

8. Para abrir o painel **Adicionar atribuição** , clique no botão **Adicionar** na parte superior da lista **usuários e grupos** .

9. Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de e-mail** do utilizador estiver interessado em atribuir para o **procurar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **usuário** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do usuário ou a logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Opcional:** se quiser **adicionar mais do que um utilizador**, tipo em outro **nome completo** ou **endereço de e-mail** para o **procurar por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos utilizadores selecionados.

Se o provisionamento estiver configurado e já estiver em execução para um aplicativo, novos usuários deverão ser provisionados para um aplicativo em aproximadamente 10 minutos. Verifique os **logs de auditoria** para obter detalhes.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Atribuir um grupo diretamente a um aplicativo como administrador

Para atribuir um ou mais grupos a um aplicativo diretamente, siga estas etapas:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Depois que o aplicativo for carregado, clique em **usuários e grupos** no menu de navegação esquerdo do aplicativo.

8. Para abrir o painel **Adicionar atribuição** , clique no botão **Adicionar** na parte superior da lista **usuários e grupos** .

9. Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome do grupo completo** do grupo estiver interessado em atribuir para o **pesquisar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **grupo** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil ou à logótipo para adicionar o utilizador para o grupo de **selecionados** lista.

12. **Opcional:** se quiser **adicionar mais de um grupo**, tipo em outro **nome do grupo inteiro** no **procurar por nome ou endereço de e-mail** caixa de pesquisa, e Clique na caixa de verificação para adicionar este grupo para o **selecionados** lista.

13. Quando tiver terminado de selecionar os grupos, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos grupos que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos grupos selecionados.

Se o provisionamento estiver configurado e já estiver em execução para um aplicativo, novos usuários contidos no grupo deverão ser provisionados para um aplicativo em aproximadamente 10 minutos. Verifique os **logs de auditoria** para obter detalhes.

>[!IMPORTANT]
>Provisionamento do nome do grupo e dos detalhes do grupo, além dos membros, se houver suporte para alguns aplicativos. Você pode habilitar ou desabilitar essa funcionalidade habilitando ou desabilitando o **mapeamento** para objetos de grupo mostrados na guia **provisionamento** . 
>
>

Se o provisionamento de grupos estiver habilitado, certifique-se de examinar os mapeamentos de atributo para garantir que um campo apropriado esteja sendo usado para a "ID correspondente". Essa ID de correspondência pode ser o nome de exibição ou alias de email. O grupo e seus membros não serão provisionados se a propriedade correspondente estiver vazia ou não for preenchida para um grupo no Azure AD.

## <a name="next-steps"></a>Passos seguintes
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatizar o aprovisionamento e o desaprovisionamento de utilizadores em Aplicações SaaS com o Azure Active Directory)
