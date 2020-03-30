---
title: 'Tutorial: Configure Salesforce Sandbox para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48df954f680c6774b0aa189453615156f4f0b4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063276"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Tutorial: Configure Salesforce Sandbox para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos necessários para realizar na Salesforce Sandbox e Azure AD para fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para Salesforce Sandbox.

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes itens:

*   Um inquilino de diretório Azure Ative.
*   Um inquilino válido para salesforce Sandbox para trabalho ou Salesforce Sandbox para Educação. Pode utilizar uma conta de teste gratuita para qualquer um dos serviços.
*   Uma conta de utilizador na Salesforce Sandbox com permissões team admin.

## <a name="assigning-users-to-salesforce-sandbox"></a>Atribuir utilizadores à Salesforce Sandbox

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o serviço de provisionamento, tem de decidir quais os utilizadores ou grupos da AD Azure que precisam de acesso à sua aplicação Salesforce Sandbox. Depois de ter feito esta decisão, pode atribuir estes utilizadores à sua aplicação Salesforce Sandbox seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Dicas importantes para atribuir utilizadores à Salesforce Sandbox

* Recomenda-se que um único utilizador da AD Azure seja atribuído à Salesforce Sandbox para testar a configuração de provisionamento. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador à Salesforce Sandbox, deve selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

> [!NOTE]
> Esta aplicação importa funções personalizadas da Salesforce Sandbox como parte do processo de provisionamento, que o cliente pode querer selecionar ao atribuir os utilizadores.

## <a name="enable-automated-user-provisioning"></a>Ativar o fornecimento automatizado de utilizadores

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador da Salesforce Sandbox que presta a API, e configurao o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas na Salesforce Sandbox com base no utilizador e no grupo atribuição em Azure AD.

>[!Tip]
>Também pode optar por ativar um único signo baseado em SAML para a Salesforce Sandbox, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o fornecimento automático de conta de utilizador

O objetivo desta secção é delinear como permitir o fornecimento de contas de utilizadores do Diretório Ativo à Salesforce Sandbox.

1. No [portal Azure,](https://portal.azure.com)navegue até ao **Azure Ative Directory > Enterprise Apps > todas as aplicações.**

1. Se já configurou a Salesforce Sandbox para um único sinal, procure a sua instância de Salesforce Sandbox utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **Salesforce Sandbox** na galeria de aplicações. Selecione Salesforce Sandbox a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância de Salesforce Sandbox e, em seguida, selecione o separador **Provisioning.**

1. Detete o **modo de provisionamento** para **automático**.

    ![provisionamento](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. No âmbito da secção **credenciais de administrador,** forneça as seguintes definições de configuração:
   
    a. Na caixa de texto **'Username' da Admin,** digite um nome de conta Salesforce Sandbox que tenha o perfil **de Administrador** do Sistema no Salesforce.com atribuído.
   
    b. Na caixa de texto **'Password' Admin,** digite a palavra-passe para esta conta.

1. Para obter o seu token de segurança Salesforce Sandbox, abra um novo separador e assine na mesma conta de administração da Salesforce Sandbox. No canto superior direito da página, clique no seu nome e, em seguida, clique em **Definições**.

     ![Ativar o fornecimento automático de utilizadores](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Ativar o fornecimento automático de utilizadores")

1. No painel de navegação à esquerda, clique em **Minhas Informações Pessoais** para expandir a secção relacionada e, em seguida, clique em **Redefinir o meu token**de segurança .
  
    ![Ativar o fornecimento automático de utilizadores](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Ativar o fornecimento automático de utilizadores")

1. Na página **Reset Security Token,** clique no botão **Reset Security Token.**

    ![Ativar o fornecimento automático de utilizadores](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Ativar o fornecimento automático de utilizadores")

1. Verifique a caixa de entrada de e-mail associada a esta conta de administração. Procure um e-mail da Salesforce Sandbox.com que contenha o novo símbolo de segurança.

1. Copie o símbolo, vá para a sua janela azure d.D., e cole-o no campo **Secreto Token.**

1. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Salesforce Sandbox.

1. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de fornecimento e verifique a caixa de verificação.

1. Clique em **Guardar.**  
    
1.  Na secção Mapeamentos, selecione **Synchronize Azure Ative Directory Users to Salesforce Sandbox.**

1. Na secção **DeMapeamentos** de Atributos, reveja os atributos do utilizador que são sincronizados de Azure AD para Salesforce Sandbox. Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador na Salesforce Sandbox para operações de atualização. Selecione o botão Guardar para elegiro qualquer alteração.

1. Para ativar o serviço de provisionamento de AD Azure para a Salesforce Sandbox, altere o Estado de **Provisionamento** para **On** na secção Definições

1. Clique em **Guardar.**

Inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos à Salesforce Sandbox na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a realizar do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos registos de atividades de provisionamento, que descrevem todas as ações realizadas pelo serviço de provisionamento na aplicação Salesforce Sandbox.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar um único signo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
