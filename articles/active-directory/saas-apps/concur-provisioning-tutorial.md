---
title: 'Tutorial: Conigure Concur para fornecimento automático de utilizadores com diretório Azure Ative Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Concur.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: edb21287b30f8ba77d6312ec6b456e20aa260598
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358217"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Tutorial: Conigure Concur para o fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que precisa de executar em Concur e Azure AD para automaticamente fornecendo e desprovisionar contas de utilizadores de Azure AD para Concur.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que já tem os seguintes itens:

*   Um inquilino de diretório Azure Ative.
*   Uma assinatura ativada por um único acordo.
*   Uma conta de utilizador em Concur com permissões da Team Admin.

## <a name="assigning-users-to-concur"></a>Atribuir utilizadores à Concur

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em AZure AD são sincronizados.

Antes de configurar e ativar o serviço de fornecimento, tem de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à sua app Concur. Uma vez decididos, pode atribuir estes utilizadores à sua app Concur seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-concur"></a>Dicas importantes para atribuir utilizadores à Concur

*   Recomenda-se que um único utilizador AD Azure seja designado para a Concur para testar a configuração de provisionamento. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador ao Concur, tem de selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="enable-user-provisioning"></a>Permitir o fornecimento de utilizadores

Esta secção guia-o através da ligação do seu AZure AD à conta de utilizador da Concur que fornece a API, e configura o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas em Concur com base na atribuição de utilizador e grupo em Azure AD.

> [!Tip] 
> Pode também optar por ativar Sign-On única baseada em SAML para Concur, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se elogiem mutuamente.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o provisionamento da conta de utilizador:

O objetivo desta secção é delinear como permitir o fornecimento de contas de utilizadores do Ative Directory para a Concur.

Para ativar aplicações no Serviço de Despesas, tem de haver uma configuração e utilização adequadas de um perfil de Administração de Serviço Web. Não adicione a função de administrador WS ao seu perfil de administrador existente que utiliza para funções administrativas T&E.

Os Consultores Concur ou o administrador do cliente devem criar um perfil distinto de Administrador de Serviço Web e o administrador do Cliente deve utilizar este perfil para as funções de Administrador de Serviços Web (por exemplo, ativar aplicações). Estes perfis devem ser mantidos separados do perfil diário de administração T&E do administrador do cliente (o perfil de administração T&E não deve ter a função WSAdmin atribuída).

Quando criar o perfil a utilizar para ativar a aplicação, insira o nome do administrador do cliente nos campos de perfil do utilizador. Isto atribui a propriedade ao perfil. Uma vez criados um ou mais perfis, o cliente deve iniciar sessão com este perfil para clicar no botão " *Enable* " para uma App de Parceiros dentro do menu Serviços Web.

Pelas seguintes razões, esta ação não deve ser feita com o perfil que utilizam para a administração normal de T&E.

* O cliente tem de ser aquele que clica em " *Sim* " na janela de diálogo que é exibida após a ativação de uma aplicação. Este clique reconhece que o cliente está disposto a que a aplicação Partner aceda aos seus dados, para que você ou o Parceiro não possam clicar no botão Sim.

* Se um administrador de clientes que tenha ativado uma aplicação utilizando o perfil de administração T&E deixar a empresa (resultando na inatividade do perfil), quaisquer aplicações ativadas através desse perfil não funcionam até que a aplicação esteja ativada com outro perfil de administração WS ativo. É por isso que é suposto criares perfis distintos da WS Admin.

* Se um administrador deixar a empresa, o nome associado ao perfil WS Admin pode ser alterado para o administrador de substituição se desejar sem afetar a app ativada porque esse perfil não necessita de inativar.

**Para configurar o provisionamento do utilizador, execute os seguintes passos:**

1. Aceda ao seu **inquilino.**

2. A partir do menu **Administração,** selecione **Serviços Web.**
   
    ![Inquilino concur](./media/concur-provisioning-tutorial/IC721729.png "Inquilino concur")

3. No lado esquerdo, a partir do painel **de Serviços Web,** selecione **Enable Partner Application**.
   
    ![Ativar a aplicação de parceiros](./media/concur-provisioning-tutorial/ic721730.png "Ativar a aplicação de parceiros")

4. A partir da lista **de Aplicações Ativa,** selecione **Azure Ative Directory** e, em seguida, clique em **Enable**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Clique **em Sim** para fechar o diálogo De Ação De **Confirmação.**
   
    ![Confirmar ação](./media/concur-provisioning-tutorial/ic721732.png "Confirmar ação")

6. No [portal Azure,](https://portal.azure.com)consulte a secção **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

7. Se já tiver configurado Concur para um único sign-on, procure a sua instância de Concur usando o campo de pesquisa. Caso contrário, **selecione Add** e procure Por **Concur** na galeria de aplicações. Selecione Concur a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

8. Selecione a sua instância de Concur e, em seguida, selecione o **separador Provisioning.**

9. Defina o **Modo de Aprovisionamento** como **Automático**. 
 
    ![Screenshot do separador Provisioning para Concur no portal Azure. O modo de provisionamento está definido para Automático e o botão de ligação de teste é realçado.](./media/concur-provisioning-tutorial/provisioning.png)

10. Na secção **Credenciais de Administração,** insira o nome de **utilizador** e a **palavra-passe** do seu administrador Concur.

11. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Concur. Se a ligação falhar, certifique-se de que a sua conta Concur tem permissões da Team Admin.

12. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **de e-mail de notificação** e verifique a caixa de verificação.

13. Clique **em Guardar.**

14. Na secção Mappings, selecione **Synchronize Azure Ative Directory Users para Concur.**

15. Na secção **De Mapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD para Concur. Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador em Concur para operações de atualização. Selecione o botão Guardar para confirmar as alterações.

16. Para permitir o serviço de prestação de Azure AD para Concur, altere o **Estado de Provisionamento** para **On** na secção **Definições**

17. Clique **em Guardar.**

Agora pode criar uma conta de teste. Aguarde até 20 minutos para verificar se a conta foi sincronizada para Concur.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o único sign-on](concur-tutorial.md)