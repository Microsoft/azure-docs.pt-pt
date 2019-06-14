---
title: 'Tutorial: Configurar área de segurança do Salesforce para o aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Salesforce Sandbox.
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
ms.openlocfilehash: 1e0a4eed020728bea5de196eebe438947ae509e4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515685"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Tutorial: Configurar área de segurança do Salesforce para o aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar a os passos que necessários para executar na Sandbox do Salesforce e o Azure AD para aprovisionar e desaprovisionar contas de utilizador do Azure AD para a área de segurança do Salesforce.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

*   Um inquilino do Azure Active directory.
*   Um inquilino válido para a área de segurança do Salesforce para o trabalho ou área de segurança do Salesforce para educação. Pode utilizar uma conta de avaliação gratuita de um dos serviços.
*   Uma conta de utilizador na Sandbox do Salesforce com permissões de administrador da equipa.

## <a name="assigning-users-to-salesforce-sandbox"></a>Atribuir utilizadores a área de segurança do Salesforce

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o serviço de aprovisionamento, terá de decidir quais os utilizadores ou grupos no Azure AD precisam de acesso à sua aplicação de proteção de segurança do Salesforce. Depois que fizer essa decisão, pode atribuir estes utilizadores à sua aplicação de proteção de segurança do Salesforce ao seguir as instruções no [atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Dicas importantes para atribuir utilizadores a área de segurança do Salesforce

* Recomenda-se que um único utilizador do Azure AD é atribuído a área de segurança do Salesforce para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para a área de segurança do Salesforce, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinido" não funciona para o aprovisionamento.

> [!NOTE]
> Esta aplicação importa funções personalizadas a partir de Sandbox do Salesforce como parte do processo de aprovisionamento, o cliente poderá querer selecionar durante a atribuição de utilizadores.

## <a name="enable-automated-user-provisioning"></a>Ativar o aprovisionamento automatizado do utilizador

Esta secção orienta-o ao longo da ligação do Azure AD para a API de aprovisionamento da conta de utilizador da área de segurança de Salesforce e configurar o serviço de aprovisionamento para criar, atualizar e desativar contas no Salesforce Sandbox com base em utilizadores e grupos de utilizador atribuído atribuição no Azure AD.

>[!Tip]
>Também pode optar por ativada baseado em SAML início de sessão único para área de segurança do Salesforce, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o aprovisionamento de contas de utilizadores automático

O objetivo desta seção é descrever como ativar o aprovisionamento de utilizadores de contas de utilizador do Active Directory para a área de segurança do Salesforce.

1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

1. Se já tiver configurado a área de segurança do Salesforce para o início de sessão único, procure a sua instância do Salesforce área de segurança usando o campo de pesquisa. Caso contrário, selecione **Add** e procure **Salesforce Sandbox** na Galeria de aplicações. Selecione a área de segurança do Salesforce resultados da pesquisa e adicioná-lo à sua lista de aplicações.

1. Selecione a sua instância do Salesforce Sandbox, em seguida, selecione o **aprovisionamento** separador.

1. Definir o **modo de aprovisionamento** ao **automática**.

    ![Aprovisionamento](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. Sob o **credenciais de administrador** secção, forneça as seguintes definições de configuração:
   
    a. Na **nome de utilizador administrador** caixa de texto, tipo de nome que tenha de conta de uma área de segurança do Salesforce a **administrador de sistema** perfil no site Salesforce.com atribuído.
   
    b. Na **palavra-passe de administrador** caixa de texto, escreva a palavra-passe para esta conta.

1. Para obter a segurança de área de segurança do Salesforce token, abra um novo separador e iniciar sessão na mesma conta de administrador da área de segurança do Salesforce. No canto superior direito da página, clique no seu nome e, em seguida, clique em **definições**.

     ![Ativar aprovisionamento automático de utilizadores](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "ativar aprovisionamento automático de utilizadores")

1. No painel de navegação esquerdo, clique em **minhas informações pessoais** para expandir a secção relacionada e, em seguida, clique em **repor minha segurança Token**.
  
    ![Ativar aprovisionamento automático de utilizadores](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "ativar aprovisionamento automático de utilizadores")

1. Sobre o **repor o Token de segurança** página, clique no **repor o Token de segurança** botão.

    ![Ativar aprovisionamento automático de utilizadores](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "ativar aprovisionamento automático de utilizadores")

1. Verifique a caixa de entrada de e-mail associada a esta conta de administrador. Procure uma mensagem de e-mail das Sandbox.com do Salesforce que contém o novo token de segurança.

1. Copie o token, vá para a janela do Azure AD e colá-lo no **segredo de Token** campo.

1. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação de proteção de segurança do Salesforce.

1. Na **notificação por E-Mail** , insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento e marque a caixa de verificação.

1. Clique em **guardar.**  
    
1.  Na secção de mapeamentos, selecione **sincronizar do Azure Active Directory Users para área de segurança do Salesforce.**

1. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD para a área de segurança do Salesforce. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador na Sandbox do Salesforce para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

1. Para ativar o Azure AD para Salesforce Sandbox do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** na secção de definições

1. Clique em **guardar.**

Ele começa a sincronização inicial de todos os utilizadores e/ou grupos atribuídos à área de segurança do Salesforce na secção utilizadores e grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para aprovisionamento de registos de atividade, que descrevem a todas as ações executadas pelo serviço de aprovisionamento na aplicação de proteção de segurança do Salesforce.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o início de sessão único](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
