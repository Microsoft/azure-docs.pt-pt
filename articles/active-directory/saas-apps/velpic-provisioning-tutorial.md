---
title: 'Tutorial: Configurar velpic para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador à Velpic.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b7a6c2c9b7ecb0b160f7481d95f7682f3f7a109
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064126"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Tutorial: Configurar velpic para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos necessários para realizar em Velpic e Azure AD para fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para Velpic.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um inquilino do Azure Active Directory
* Um inquilino velpic com o [plano da Enterprise](https://www.velpic.com/pricing.html) ou melhor habilitado
* Uma conta de utilizador em Velpic com permissões admina

## <a name="assigning-users-to-velpic"></a>Atribuir utilizadores a Velpic

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação em Azure AD serão sincronizados. 

Antes de configurar e ativar o serviço de provisionamento, terá de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à sua aplicação Velpic. Uma vez decidido, pode atribuir estes utilizadores à sua aplicação Velpic seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Dicas importantes para atribuir utilizadores à Velpic

* Recomenda-se que um único utilizador da AD Azure seja atribuído à Velpic para testar a configuração de provisionamento. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador à Velpic, deve selecionar a função **Utilizador** ou outra função específica de aplicação válida (se disponível) no diálogo de atribuição. Note que a função **de Acesso Predefinido** não funciona para o fornecimento, e estes utilizadores serão ignorados.

## <a name="configuring-user-provisioning-to-velpic"></a>Configurar o fornecimento de utilizadores à Velpic

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador da Velpic que aprovisiona a API, e configurando o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas em Velpic com base na atribuição de utilizador e grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar o Single Sign-On baseado em SAML para Velpic, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Para configurar o fornecimento automático de conta de utilizador à Velpic em Azure AD:

1. No [portal Azure,](https://portal.azure.com)navegue até ao **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

2. Se já configurou velpic para um único sinal, procure a sua instância de Velpic utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **velpic** na galeria de aplicações. Selecione Velpic a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

3. Selecione a sua instância de Velpic e, em seguida, selecione o separador **Provisioning.**

4. Detete o **modo de provisionamento** para **automático**.

    ![Provisionamento Velpic](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Sob a secção **de Credenciais de Administrador,** insere o **URL&Secret Token** de Velpic. (Pode encontrar estes valores na sua conta Velpic: **Gerir** a **integração** >  > **Plugin** > **SCIM)**

    ![Valores de Autorização](./media/velpic-provisioning-tutorial/Velpic2.png)

6. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Velpic. Se a ligação falhar, certifique-se de que a sua conta Velpic tem permissões de Administrador e tente novamente o passo 5.

7. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro no campo de email de **notificação** e verifique a caixa de verificação abaixo.

8. Clique em **Guardar**.

9. Na secção Mapeamentos, **selecione Synchronize Azure Ative Directory Users to Velpic**.

10. Na secção **DeMapeamentos de Atributos,** reveja os atributos do utilizador que serão sincronizados de Azure AD a Velpic. Note que os atributos selecionados como propriedades **correspondentes** serão utilizados para combinar as contas de utilizador em Velpic para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

11. Para ativar o serviço de provisionamento de AD Azure para velpic, altere o Estado de **Provisionamento** para **Ligado** na secção **Definições**

12. Clique em **Guardar**.

Isto iniciará a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos à Velpic na secção Utilizadores e Grupos. Note que a sincronização inicial demorará mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos relatórios de atividadede provisionamento, que descrevem todas as ações realizadas pelo serviço de provisionamento.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)