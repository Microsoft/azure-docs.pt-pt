---
title: 'Tutorial: Fornecimento de utilizadores para Asana - Azure AD'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à Asana.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.reviewer: celested
ms.openlocfilehash: 11e0606568fdaad20f996a7ae20a622576acea15
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88549471"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Tutorial: Configurar Asana para o fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que precisa de executar no Asana e no Azure Ative Directory (Azure AD) para provisões automáticas e desavisagem de contas de utilizadores de Azure AD a Asana.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino da AD AZure
* Um inquilino asana com um plano [empresarial](https://www.asana.com/pricing) ou melhor habilitado
* Uma conta de utilizador em Asana com permissões de administração

> [!NOTE]
> A integração de provisionamento da AD Azure depende da [API asana,](https://asana.com/developers/api-reference/users)que está disponível para a Asana.

## <a name="assign-users-to-asana"></a>Atribuir utilizadores à Asana

A Azure AD utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores afetados a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o serviço de fornecimento, tem de decidir quais os utilizadores em Azure AD que precisam de acesso à sua app Asana. Em seguida, pode atribuir estes utilizadores à sua app Asana seguindo as instruções aqui:

[Atribuir um utilizador a uma aplicação da empresa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Dicas importantes para atribuir utilizadores à Asana

Recomendamos que atribua um único utilizador Azure AD à Asana para testar a configuração de provisionamento. Utilizadores adicionais podem ser atribuídos mais tarde.

## <a name="configure-user-provisioning-to-asana"></a>Configure o fornecimento de utilizadores à Asana

Esta secção guia-o através da ligação do seu AD AZure à conta de utilizador da Asana que fornece a API. Também configura o serviço de fornecimento para criar, atualizar e desativar contas de utilizador atribuídas em Asana com base em atribuições de utilizadores em Azure AD.

> [!TIP]
> Para ativar o sign-on único baseado em SAML para Asana, siga as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se complementem.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Para configurar o fornecimento automático de conta de utilizador à Asana em Azure AD

1. No [portal Azure,](https://portal.azure.com)consulte a secção **Azure Ative Directory**  >  **Enterprise Apps**All  >  **applications.**

1. Se já configurar a Asana para um único sinal, procure a sua instância de Asana utilizando o campo de pesquisa. Caso contrário, **selecione Add** e procure por **Asana** na galeria de aplicações. Selecione **Asana** a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância de Asana e, em seguida, selecione o **separador Provisioning.**

1. Desa ajuste **o modo de provisionamento** para **automático**.

    ![Provisionamento de Asana](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. Sob a secção credenciais de **administração,** siga estas instruções para gerar o símbolo e inseri-lo em **Secret Token:**

    a. Inscreva-se em [Asana](https://app.asana.com) usando a sua conta de administração.

    b. Selecione a foto de perfil da barra superior e selecione as definições atuais do nome da organização.

    c. Aceda ao separador **Contas de Serviço.**

    d. Selecione **Conta de Serviço de Adicionar**.

    e. Atualizar **Nome** e **Sobre** e a foto de perfil conforme necessário. Copie o token em **Token**e selecione-o em **Alterações de Poupança**.

1. No portal Azure, selecione **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Asana. Se a ligação falhar, certifique-se de que a sua conta Asana tem permissões de administração e tente novamente o passo **de Ligação** de Teste.

1. Insira o endereço de e-mail de uma pessoa ou grupo que pretende receber notificações de erro de provisionamento no  **E-mail de Notificação**. Selecione a caixa de verificação por baixo.

1. Selecione **Guardar**.

1. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Asana**.

1. Na secção **De Mapeamentos de Atributos,** reveja os atributos do utilizador para serem sincronizados de Azure AD a Asana. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Asana para operações de atualização. **Selecione Guardar** para escoar quaisquer alterações. Para obter mais informações, consulte [Personalizar os mapeamentos de atributos de fornecimento do utilizador.](../app-provisioning/customize-application-attributes.md)

1. Para ativar o serviço de prestação de AD Azure para Asana, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

1. Selecione **Guardar**.

Agora começa a sincronização inicial para qualquer utilizadores atribuídos a Asana na secção **Utilizadores.** A sincronização inicial demora mais tempo a ser efetuada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em funcionamento. Utilize a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para os registos de atividade de provisionamento. Os registos de auditoria descrevem todas as ações realizadas pelo serviço de fornecimento na sua app Asana.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatório sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o início de sessão único](asana-tutorial.md)
