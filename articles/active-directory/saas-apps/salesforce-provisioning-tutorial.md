---
title: 'Tutorial: Configurar o Salesforce para o aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 712cc5ce62225987f8cc3ea13b5e4fd10a7d5eaf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515806"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Tutorial: Configurar o Salesforce para o aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar os passos necessários para executar no Salesforce e o Azure AD para aprovisionar automaticamente e contas de usuário desaprovisionar do Azure AD para o Salesforce.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

* Um inquilino do Azure Active directory
* Um inquilino do Salesforce.com

> [!IMPORTANT]
> Se estiver a utilizar uma conta de avaliação do Salesforce.com, em seguida, não será possível configurar o aprovisionamento automatizado do utilizador. Contas de avaliação não é necessário o acesso necessária à API ativada até que a sua compra. Pode contornar esta limitação ao utilizar um gratuito [conta de programador](https://developer.salesforce.com/signup) para concluir este tutorial.

Se estiver a utilizar um ambiente de área de segurança do Salesforce, consulte a [tutorial de integração de Salesforce Sandbox](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Atribuir utilizadores a Salesforce

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, terá de decidir quais os utilizadores ou grupos no Azure AD precisam de acesso à sua aplicação do Salesforce. Depois que fizer essa decisão, pode atribuir estes utilizadores à sua aplicação do Salesforce ao seguir as instruções no [atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Dicas importantes para atribuir utilizadores a Salesforce

* Recomenda-se que um único utilizador do Azure AD é atribuído ao Salesforce para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Salesforce, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinido" não funciona para o aprovisionamento

    > [!NOTE]
    > Esta aplicação importa perfis do Salesforce como parte do processo de aprovisionamento, o cliente poderá querer selecionar quando atribuir utilizadores no Azure AD. Tenha em atenção que os perfis que importado do Salesforce são apresentados como funções no Azure AD.

## <a name="enable-automated-user-provisioning"></a>Ativar o aprovisionamento automatizado do utilizador

Esta secção orienta-o ao longo da ligação do Azure AD para a API de aprovisionamento da conta de utilizador do Salesforce e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no Salesforce com base na atribuição de utilizadores e grupos no Azure AD.

> [!Tip]
> Também pode optar por ativada baseado em SAML início de sessão único para Salesforce, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o aprovisionamento de contas de utilizadores automático

O objetivo desta seção é descrever como ativar o aprovisionamento de utilizadores de contas de utilizador do Active Directory para o Salesforce.

1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

2. Se já tiver configurado o Salesforce para o início de sessão único, procure a sua instância do Salesforce a utilizar o campo de pesquisa. Caso contrário, selecione **Add** e procure **Salesforce** na Galeria de aplicações. Selecione o Salesforce resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância do Salesforce, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![a aprovisionar](./media/salesforce-provisioning-tutorial/provisioning.png)

5. Sob o **credenciais de administrador** secção, forneça as seguintes definições de configuração:

    a. Na **nome de utilizador administrador** caixa de texto, tipo um Salesforce o nome que tenha de conta a **administrador de sistema** perfil no site Salesforce.com atribuído.

    b. Na **palavra-passe de administrador** caixa de texto, escreva a palavra-passe para esta conta.

6. Para obter a segurança do Salesforce token, abra um novo separador e iniciar sessão na mesma conta de administrador do Salesforce. No canto superior direito da página, clique no seu nome e, em seguida, clique em **definições**.

    ![Ativar aprovisionamento automático de utilizadores](./media/salesforce-provisioning-tutorial/sf-my-settings.png "ativar aprovisionamento automático de utilizadores")

7. No painel de navegação esquerdo, clique em **minhas informações pessoais** para expandir a secção relacionada e, em seguida, clique em **repor minha segurança Token**.
  
    ![Ativar aprovisionamento automático de utilizadores](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "ativar aprovisionamento automático de utilizadores")

8. Sobre o **repor o Token de segurança** página, clique em **repor o Token de segurança** botão.

    ![Ativar aprovisionamento automático de utilizadores](./media/salesforce-provisioning-tutorial/sf-reset-token.png "ativar aprovisionamento automático de utilizadores")

9. Verifique a caixa de entrada de e-mail associada a esta conta de administrador. Procure uma mensagem de e-mail das Salesforce.com que contém o novo token de segurança.

10. Copie o token, vá para a janela do Azure AD e colá-lo no **segredo de Token** campo.

11. O **URL de inquilino** deve ser inserido se a instância do Salesforce está na Cloud de Governo do Salesforce. Caso contrário, é opcional. Introduza o URL de inquilino utilizando o formato de "https://\<sua instância\>. my.salesforce.com," substituir \<sua instância\> com o nome da sua instância do Salesforce.

12. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar-se a aplicação do Salesforce.

13. Na **notificação por E-Mail** , insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento e marque a caixa de verificação abaixo.

14. Clique em **guardar.**  

15. Na secção de mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para o Salesforce.**

16. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD para o Salesforce. Tenha em atenção que os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Salesforce para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

17. Para ativar o Azure AD para Salesforce, o serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** na secção de definições

18. Clique em **guardar.**

Esta ação inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos ao Salesforce na secção utilizadores e grupos. Tenha em atenção que a sincronização inicial exige mais para efetuar sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para aprovisionamento de registos de atividade, que descrevem a todas as ações executadas pelo serviço de aprovisionamento na sua aplicação do Salesforce.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o início de sessão único](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
