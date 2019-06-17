---
title: Ativar um piloto do Multi-Factor Authentication do Azure
description: Neste tutorial, vai ativar a Multi-Factor Authentication do Azure AD para um grupo piloto de utilizadores
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a229eef3d49964e71becf066d67f60b18da6fc18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113250"
---
# <a name="tutorial-complete-an-azure-multi-factor-authentication-pilot-roll-out"></a>Tutorial: Concluir uma implementação piloto do Azure multi-factor Authentication

Neste tutorial, explica como configurar uma política de acesso condicional ativar multi-factor Authentication (MFA do Azure) ao iniciar sessão portal do Azure. A política é implementada e testada num grupo específico de utilizadores piloto. Implementação do MFA do Azure através do acesso condicional proporciona uma flexibilidade significativa para as organizações e os administradores em comparação comparados o método tradicional de imposto.

> [!div class="checklist"]
> * Ativar a Multi-Factor Authentication do Azure
> * Testar Multi-Factor Authentication do Azure

## <a name="prerequisites"></a>Pré-requisitos

* Um inquilino do Azure AD em funcionamento com, pelo menos, uma licença de avaliação ativada.
* Uma conta com privilégios de Administrador Global.
* Um utilizador de teste não-administrador com uma palavra-passe saber para teste, se precisar de criar um utilizador, consulte o artigo [início rápido: Adicionar novos utilizadores ao Azure Active Directory](../add-users-azure-active-directory.md).
* Um grupo piloto para testar do qual o utilizador não administrador seja membro. Se precisar de criar um grupo, veja o artigo [Criar um grupo e adicionar membros no Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-azure-multi-factor-authentication"></a>Ativar a Multi-Factor Authentication do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador Global.
1. Navegue até **do Azure Active Directory**, **acesso condicional**
1. Selecione **Nova política**
1. Nomeie a sua política **Piloto de MFA**
1. Sob **utilizadores e grupos**, selecione o botão de opção **Selecionar utilizadores e grupos**
    * Selecione o grupo piloto criado como parte da secção de pré-requisitos deste artigo
    * Clique em **Concluído**
1. Sob **Aplicações na cloud**, selecione o botão de opção **Selecionar aplicações**
    * A aplicação da cloud para o portal do Azure é **Gestão do Microsoft Azure**
    * Clique em **Selecionar**
    * Clique em **Concluído**
1. Ignore a secção **Condições**
1. Sob **Conceder**, certifique-se de que o botão de opção **Conceder acesso** está selecionado
    * Assinale a caixa **Pedir a autenticação multifator**
    * Clique em **Selecionar**
1. Ignore a secção **Sessão**
1. Defina o botão de alternar **Ativar política** para **Ativado**
1. Clique em **Criar**

## <a name="test-azure-multi-factor-authentication"></a>Testar Multi-Factor Authentication do Azure

Para provar que a sua política de acesso condicional funciona, testar o início de sessão num recurso que não deve exigir a MFA e, em seguida, no portal do Azure que requer MFA.

1. Abra uma nova janela de browser no modo InPrivate ou incógnito e navegue para [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Inicie sessão com o utilizador de teste criado como parte da secção de pré-requisitos deste artigo e tenha em atenção que ele não lhe deve pedir para concluir a MFA.
   * Feche a janela do browser.
2. Abra uma nova janela de browser no modo InPrivate ou incógnito e navegue para [https://portal.azure.com](https://portal.azure.com).
   * Inicie sessão com o utilizador de teste criado como parte da secção de pré-requisitos deste artigo e tenha em atenção que agora deve ser preciso registar-se e utilizar o Multi-Factor Authentication do Azure.
   * Feche a janela do browser.

## <a name="clean-up-resources"></a>Limpar recursos

Se decidir que já não quer utilizar a funcionalidade que configurou como parte deste tutorial, faça a alteração seguinte.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue até **do Azure Active Directory**, **acesso condicional**.
1. Selecione a política de acesso condicional que criou.
1. Clique em **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ativou o Multi-Factor Authentication do Azure. Avance para o próximo tutorial para ver como o Azure Identity Protection pode ser integrado na reposição personalizada de palavra-passe e nas experiências de Multi-Factor Authentication.

> [!div class="nextstepaction"]
> [Avaliar o risco de início de sessão](tutorial-risk-based-sspr-mfa.md)
