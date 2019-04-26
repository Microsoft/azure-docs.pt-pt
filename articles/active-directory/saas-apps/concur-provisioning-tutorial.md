---
title: 'Tutorial: Configurar o Concur para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Concur.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441aa9805f2a453e22f207238315125d2a281838
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60280438"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Tutorial: Configurar o Concur para aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar a os passos que necessários para executar no Concur e do Azure AD para aprovisionar e desaprovisionar contas de utilizador do Azure AD para Concur automaticamente.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

*   Um inquilino do Azure Active directory.
*   Um Concur início de sessão único ativado na subscrição.
*   Uma conta de utilizador no Concur com permissões de administrador da equipa.

## <a name="assigning-users-to-concur"></a>Atribuir utilizadores a Concur

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, precisa decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação Concur. Depois de decidir, pode atribuir estes utilizadores à sua aplicação Concur ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Dicas importantes para atribuir utilizadores a Concur

*   Recomenda-se que um único utilizador do Azure AD ser atribuídos a Concur para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador para Concur, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinido" não funciona para o aprovisionamento.

## <a name="enable-user-provisioning"></a>Ativar o aprovisionamento do utilizador

Esta secção orienta-o ao longo da ligação do Azure AD para a API de aprovisionamento da conta de utilizador do Concur e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no Concur com base na atribuição de utilizadores e grupos no Azure AD.

> [!Tip] 
> Também pode optar por ativada baseado em SAML início de sessão único para Concur, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o aprovisionamento de contas de utilizador:

É o objetivo desta secção descrevem como ativar o aprovisionamento de contas de utilizador do Active Directory para o Concur.

Para ativar as aplicações no serviço de despesas, há tem de ser instalação correta e o uso de um perfil de administrador de serviço da Web. Não adicione a função de administrador de WS para o seu perfil de administrador existente que utiliza para funções administrativas e despesas.

Consegue consultores ou o administrador do cliente tem de criar um perfil de administrador de serviços da Web diferentes e o administrador do cliente tem de utilizar este perfil para as funções de administrador de serviços da Web (por exemplo, permitindo aplicações). Estes perfis tem de ser mantidos separados do diária e despesas perfil admin do administrador do client (o perfil de administrador e despesas não deve ter a função de WSAdmin atribuída).

Ao criar o perfil a ser utilizado para ativar a aplicação, introduza o nome do administrador do client nos campos de perfil do usuário. Esta ação atribui a propriedade para o perfil. Depois de criar um ou mais perfis, o cliente tem de iniciar sessão com este perfil de clicar na "*ativar*" botão para uma aplicação de parceiro no menu de serviços da Web.

Pelos seguintes motivos, esta ação não deve ser feita com o perfil que utilizam para a administração de horas e despesas normal.

* O cliente tem de ser aquele que clica em "*Sim*" na janela de caixa de diálogo que é apresentada depois de uma aplicação está ativada. Esse clique reconhece que o cliente está disposto para aplicativo do parceiro para aceder aos respetivos dados, então ou o parceiro não é possível clicar nesse botão Sim.

* Se um administrador do cliente que tiver ativado uma aplicação com o administrador de horas e despesas perfil sai da empresa (o que resulta num perfil que está a ser desativado), todas as aplicações ativadas com que o perfil não funcionará até que a aplicação seja ativada com outro perfil de administrador do WS Active Directory. É por isso precisa para criar distintos WS administrador perfis.

* Se um administrador saírem da empresa, o nome associado ao perfil de administrador do WS pode ser alterado para o administrador de substituição, se assim o desejar sem afetar que a aplicação ativada porque esse perfil não precisa desativado.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **Concur** inquilino.

2. Do **Administration** menu, selecione **serviços da Web**.
   
    ![Inquilino Concur](./media/concur-provisioning-tutorial/IC721729.png "Concur inquilino")

3. No lado esquerdo, do **serviços Web** painel, selecione **ativar a aplicação de parceiro**.
   
    ![Ativar a aplicação do parceiro](./media/concur-provisioning-tutorial/ic721730.png "permitem a aplicação de parceiro")

4. Do **ativar o aplicativo** lista, selecione **Azure Active Directory**e, em seguida, clique em **ativar**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Clique em **Sim** para fechar a **confirmar ação** caixa de diálogo.
   
    ![Confirmar ação](./media/concur-provisioning-tutorial/ic721732.png "confirmar ação")

6. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

7. Se já tiver configurado o Concur para início de sessão único, procure a sua instância do Concur usando o campo de pesquisa. Caso contrário, selecione **Add** e procure **Concur** na Galeria de aplicações. Selecione o Concur resultados da pesquisa e adicioná-lo à sua lista de aplicações.

8. Selecione a sua instância de Concur, em seguida, selecione o **aprovisionamento** separador.

9. Definir o **modo de aprovisionamento** ao **automática**. 
 
    ![a aprovisionar](./media/concur-provisioning-tutorial/provisioning.png)

10. Sob o **credenciais de administrador** , digite o **nome de utilizador** e o **palavra-passe** do seu administrador de Concur.

11. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação de Concur. Se a ligação falhar, certifique-se de que a conta de Concur tem permissões de administrador da equipa.

12. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **notificação por E-Mail** campo e marque a caixa de verificação.

13. Clique em **guardar.**

14. Na secção de mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para Concur.**

15. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD para Concur. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Concur para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

16. Para ativar o Azure AD para Concur do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção

17. Clique em **guardar.**

Agora, pode criar uma conta de teste. Aguarde até 20 minutos para verificar se a conta tem sido sincronizada para Concur.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o início de sessão único](concur-tutorial.md)

