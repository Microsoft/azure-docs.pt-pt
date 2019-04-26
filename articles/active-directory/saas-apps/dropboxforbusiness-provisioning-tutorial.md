---
title: 'Tutorial: Configurar o Dropbox para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d53d10b036a37489be0b7aae6208880044b766a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60279997"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Tutorial: Configurar o Dropbox for Business para aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar a os passos que necessários para executar na Dropbox para empresas e o Azure AD aprovisionar e desaprovisionar contas de utilizador do Azure AD para o Dropbox for Business.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

*   Um inquilino do Azure Active directory.
*   Dropbox for Business início de sessão único na subscrição ativado.
*   Uma conta de utilizador no Dropbox para empresas com permissões de administrador da equipa.

## <a name="assigning-users-to-dropbox-for-business"></a>Atribuir utilizadores a Dropbox para empresas

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, precisa decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder ao seu Dropbox para a aplicação de negócio. Depois de decidir, pode atribuir esses usuários para seu Dropbox para a aplicação de negócio ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Dicas importantes para atribuir utilizadores a Dropbox para empresas

*   Recomenda-se que um único utilizador do Azure AD é atribuído ao Dropbox para empresas testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador para o Dropbox for Business, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinido" não funciona para o aprovisionamento....

## <a name="enable-automated-user-provisioning"></a>Ativar o aprovisionamento automatizado do utilizador

Esta secção orienta-o ao longo da ligação do Azure AD para a Dropbox para a API de aprovisionamento da conta de utilizador da empresa e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no Dropbox para empresas com base no utilizador e grupo atribuição no Azure AD.

>[!Tip]
>Também pode optar por ativada baseado em SAML início de sessão único para o Dropbox for Business, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar o aprovisionamento de contas de utilizador automáticas:

1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

2. Se já tiver configurado o Dropbox for Business para início de sessão único, procure a sua instância do Dropbox para a empresa usando o campo de pesquisa. Caso contrário, selecione **Add** e procure **Dropbox para empresas** na Galeria de aplicações. Selecione o Dropbox for Business resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância do Dropbox para empresas, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** ao **automática**. 

    ![a aprovisionar](./media/dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. Sob o **credenciais de administrador** secção, clique em **autorizar**. É aberto um Dropbox para caixa de diálogo de início de sessão de negócios numa nova janela do browser.

6. Sobre o **iniciar sessão no Dropbox para ligar com o Azure AD** caixa de diálogo, iniciar sessão no seu Dropbox para o inquilino de negócios.

     ![Aprovisionamento de utilizadores](./media/dropboxforbusiness-provisioning-tutorial/ic769518.png "aprovisionamento de utilizadores")

7. Confirme que pretende conceder permissão ao Azure Active Directory para efetuar alterações ao seu Dropbox para o inquilino de negócios. Clique em **permitir**.
    
      ![Aprovisionamento de utilizadores](./media/dropboxforbusiness-provisioning-tutorial/ic769519.png "aprovisionamento de utilizadores")

8. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar ao seu Dropbox para a aplicação de negócio. Se a ligação falhar, certifique-se de seu Dropbox para a conta de negócios tem permissões de administrador de equipe e tente a **"Autorizar"** passo novamente.

9. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **notificação por E-Mail** campo e marque a caixa de verificação.

10. Clique em **guardar.**

11. Na secção de mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para o Dropbox for Business.**

12. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD para o Dropbox for Business. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de usuário no Dropbox para empresas para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

13. Para ativar o Azure AD para o Dropbox for Business do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** na secção de definições

14. Clique em **guardar.**

Ele começa a sincronização inicial de todos os utilizadores e/ou grupos atribuídos à Dropbox para empresas na secção utilizadores e grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para os registos de atividades, que descrevem a todas as ações executadas pelo serviço de aprovisionamento no seu Dropbox para a aplicação de negócio de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o início de sessão único](dropboxforbusiness-tutorial.md)
