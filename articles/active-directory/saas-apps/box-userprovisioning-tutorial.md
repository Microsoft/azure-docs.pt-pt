---
title: 'Tutorial: Configure Box para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Box .
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1397b4189a9c2c15e3878687ea8c67c1da7567f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058574"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Tutorial: Configure Box para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar os passos necessários para executar em Box e Azure AD para fornecer e desfornecer automaticamente contas de utilizador de Azure AD para Box.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Box, precisa dos seguintes itens:

- Um locatário do Azure AD
- Um plano de negócios box ou melhor

> [!NOTE]
> Quando testar os passos deste tutorial, recomendamos que *não* utilize um ambiente de produção.

Para testar os passos neste tutorial, siga estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de julgamento da AD Azure, pode [ter um julgamento de um mês.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="assigning-users-to-box"></a>Atribuir utilizadores à Caixa 

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o serviço de provisionamento, tem de decidir quais os utilizadores e/ou grupos em AD Azure que representam os utilizadores que precisam de acesso à sua aplicação Box. Uma vez decidido, pode atribuir estes utilizadores à sua aplicação Box seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Atribuir utilizadores e grupos
O separador **Box > Utilizadores e Grupos** no portal Azure permite especificar quais os utilizadores e grupos que devem ter acesso à Caixa. A atribuição de um utilizador ou grupo faz com que ocorram as seguintes coisas:

* A Azure AD permite que o utilizador designado (seja por atribuição direta ou por associação de grupo) autentique à Caixa. Se um utilizador não for atribuído, o Azure AD não permite que eles instem no Box e devolve um erro na página de entrada de anúncios da AD Azure.
* Um azulejo de aplicação para Box é adicionado ao lançador de [aplicações](../manage-apps/end-user-experiences.md)do utilizador .
* Se o fornecimento automático estiver ativado, os utilizadores e/ou grupos designados são adicionados à fila de provisionamento para serem automaticamente provisionados.
  
  * Se apenas os objetos de utilizador foram configurados para serem provisionados, então todos os utilizadores diretamente atribuídos são colocados na fila de provisionamento, e todos os utilizadores que são membros de quaisquer grupos designados são colocados na fila de provisionamento. 
  * Se os objetos de grupo foram configurados para serem provisionados, então todos os objetos de grupo atribuídos são aprovisionados para box, e todos os utilizadores que são membros desses grupos. Os membros do grupo e dos utilizadores são preservados ao serem escritos à Box.

Pode utilizar o separador **Atributos > Single Sign-On** para configurar quais os atributos (ou reclamações) apresentados ao Box durante a autenticação baseada em SAML, e o separador **Atributos > Provisioning** para configurar como os atributos do utilizador e do grupo fluem do Azure AD para o Box durante as operações de provisionamento.

### <a name="important-tips-for-assigning-users-to-box"></a>Dicas importantes para atribuir utilizadores à Box 

*   Recomenda-se que um único utilizador da AD Azure atribuído à Box teste da configuração de provisionamento. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

*   Ao atribuir um utilizador à caixa, deve selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="enable-automated-user-provisioning"></a>Ativar o fornecimento automatizado de utilizadores

Esta secção guia através da ligação do seu AD Azure à conta de utilizador da Box que aprovisiona a API, e configurando o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas na Caixa com base na atribuição de utilizador e grupo em Azure AD.

Se o fornecimento automático estiver ativado, os utilizadores e/ou grupos designados são adicionados à fila de provisionamento para serem automaticamente provisionados.
    
 * Se apenas os objetos de utilizador estiverem configurados para serem aprovisionados, os utilizadores diretamente atribuídos são colocados na fila de fornecimento, e todos os utilizadores que são membros de quaisquer grupos designados são colocados na fila de provisionamento. 
    
 * Se os objetos de grupo foram configurados para serem provisionados, então todos os objetos de grupo atribuídos são aprovisionados para box, e todos os utilizadores que são membros desses grupos. Os membros do grupo e dos utilizadores são preservados ao serem escritos à Box.

> [!TIP] 
> Também pode optar por ativar o Single Sign-On para Caixa baseado em SAML, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar o fornecimento automático de conta de utilizador:

O objetivo desta secção é delinear como permitir o fornecimento de contas de utilizadores do Diretório Ativo à Caixa.

1. No [portal Azure,](https://portal.azure.com)navegue até ao **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

2. Se já configurou a Caixa para um único sinal, procure a sua instância de Box utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **Box** na galeria de aplicações. Selecione Box a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

3. Selecione a sua instância de Caixa e, em seguida, selecione o separador **Provisioning.**

4. Detete o **modo de provisionamento** para **automático**. 

    ![provisionamento](./media/box-userprovisioning-tutorial/provisioning.png)

5. Na secção **'Credenciais de Administrador',** clique **em autorizar** a abertura de um diálogo de login caixa numa nova janela do navegador.

6. No **Login para conceder acesso à** página Caixa, forneça as credenciais necessárias e, em seguida, clique em **Autorizar**. 
   
    ![Ativar o fornecimento automático de utilizadores](./media/box-userprovisioning-tutorial/IC769546.png "Ativar o fornecimento automático de utilizadores")

7. Clique em **Conceder acesso à Caixa** para autorizar esta operação e para regressar ao portal Azure. 
   
    ![Ativar o fornecimento automático de utilizadores](./media/box-userprovisioning-tutorial/IC769549.png "Ativar o fornecimento automático de utilizadores")

8. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Box. Se a ligação falhar, certifique-se de que a sua conta Box tem permissões de Team Admin e tente novamente o passo **"Autorizar".**

9. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro no campo de email de **notificação** e verifique a caixa de verificação.

10. Clique em **Guardar.**

11. Na secção Mapeamentos, **selecione Synchronize Azure Ative Directory Users to Box.**

12. Na secção **DeMapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD para Box. Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Box para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

13. Para ativar o serviço de provisionamento de AD Azure para caixa, altere o Estado de **Provisionamento** para **Ligado** na secção Definições

14. Clique em **Guardar.**

Isto inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos à Caixa na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos registos de atividades de provisionamento, que descrevem todas as ações realizadas pelo serviço de provisionamento na sua aplicação Box.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

No seu inquilino box, os utilizadores sincronizados estão listados em **Utilizadores Geridos** na **Consola De Administração**.

![Estado de integração](./media/box-userprovisioning-tutorial/IC769556.png "Estado de integração")


## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar um único signo](box-tutorial.md)
