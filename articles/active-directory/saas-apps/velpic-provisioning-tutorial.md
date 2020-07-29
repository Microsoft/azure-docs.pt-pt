---
title: 'Tutorial: Configurar o Velpic para o provisionamento automático do utilizador com o Azure Ative Directory Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à Velpic.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77064126"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Tutorial: Configurar Velpic para o Provisionamento Automático do Utilizador

O objetivo deste tutorial é mostrar-lhe os passos que precisa de executar em Velpic e AZure AD para provisões automáticas e desavisagem de contas de utilizadores de Azure AD a Velpic.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino do Azure Active Directory
* Um inquilino Velpic com o [plano da Enterprise](https://www.velpic.com/pricing.html) ou melhor habilitado
* Uma conta de utilizador em Velpic com permissões de Administração

## <a name="assigning-users-to-velpic"></a>Atribuir utilizadores à Velpic

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em AZure AD serão sincronizados. 

Antes de configurar e ativar o serviço de fornecimento, terá de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à sua aplicação Velpic. Uma vez decididos, pode atribuir estes utilizadores à sua aplicação Velpic seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Dicas importantes para atribuir utilizadores à Velpic

* Recomenda-se que um único utilizador AD Azure seja designado para a Velpic para testar a configuração de provisionamento. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à Velpic, deve selecionar a função **Do Utilizador** ou outra função específica de aplicação válida (se disponível) no diálogo de atribuição. Note que a função **de Acesso Predefinido** não funciona para o provisionamento, e estes utilizadores serão ignorados.

## <a name="configuring-user-provisioning-to-velpic"></a>Configurar o fornecimento de utilizadores à Velpic

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador da Velpic que fornece a API e configura o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas em Velpic com base na atribuição de utilizadores e grupos em Azure AD.

> [!TIP]
> Pode também optar por ativar um único sign-on baseado em SAML para Velpic, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se elogiem mutuamente.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Para configurar o fornecimento automático de conta de utilizador à Velpic em Azure AD:

1. No [portal Azure,](https://portal.azure.com)consulte a secção **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

2. Se já configurar o Velpic para um único sinal, procure a sua instância de Velpic utilizando o campo de pesquisa. Caso contrário, **selecione Add** e procure **Velpic** na galeria de aplicações. Selecione Velpic a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

3. Selecione a sua instância de Velpic e, em seguida, selecione o **separador Provisioning.**

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Provisão Velpic](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Sob a secção **de Credenciais de Administração,** insira o URL do **inquilino&Token Secreto** de Velpic. (Pode encontrar estes valores na sua conta Velpic: **Gerir**  >  **Integração**  >  **Plugin**  >  **SCIM**)

    ![Valores de Autorização](./media/velpic-provisioning-tutorial/Velpic2.png)

6. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Velpic. Se a ligação falhar, certifique-se de que a sua conta Velpic tem permissões de Administração e tente novamente o passo 5.

7. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **de e-mail de notificação** e verifique a caixa de verificação abaixo.

8. Clique em **Guardar**.

9. Na secção Mappings, selecione **Synchronize Azure Ative Directory Users to Velpic**.

10. Na secção **De Mapeamentos de Atributos,** reveja os atributos do utilizador que serão sincronizados de Azure AD a Velpic. Note que os atributos selecionados como propriedades **de correspondência** serão utilizados para combinar as contas do utilizador em Velpic para operações de atualização. Selecione o botão Guardar para escoar quaisquer alterações.

11. Para ativar o serviço de prestação de Ad Azure para o Velpic, altere o **Estado de Provisionamento** para **On** na secção **Definições**

12. Clique em **Guardar**.

Isto iniciará a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos à Velpic na secção Utilizadores e Grupos. Note que a sincronização inicial demorará mais tempo a ser efetuada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos enquanto o serviço estiver em funcionamento. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para relatórios de atividade de provisionamento, que descrevem todas as ações realizadas pelo serviço de fornecimento.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Próximos passos

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)