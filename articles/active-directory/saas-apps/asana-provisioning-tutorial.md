---
title: 'Tutorial: Fornecimento de utilizadores para Asana - Azure AD'
description: Aprenda a configurar o Diretório Ativo azure para fornecer automaticamente e desfornecer contas de utilizador à Asana.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: abeac030db419f7fb7d561df5dcd407684f20ca2
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058936"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Tutorial: Configure Asana para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos necessários para realizar no Asana e azure Ative Directory (Azure AD) para fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para Asana.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure AD
* Um inquilino da Asana com um plano [da Enterprise](https://www.asana.com/pricing) ou melhor habilitado
* Uma conta de utilizador em Asana com permissões de administração

> [!NOTE]
> A integração do fornecimento de AD Azure baseia-se na [API Asana,](https://asana.com/developers/api-reference/users)que está disponível para a Asana.

## <a name="assign-users-to-asana"></a>Atribuir utilizadores a Asana

A Azure AD utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores afetados a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o serviço de provisionamento, deve decidir quais os utilizadores do Azure AD que precisam de ter acesso à sua app Asana. Em seguida, pode atribuir estes utilizadores à sua app Asana seguindo as instruções aqui:

[Atribuir um utilizador a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Dicas importantes para atribuir utilizadores à Asana

Recomendamos que atribua um único utilizador da AD Azure à Asana para testar a configuração de provisionamento. Os utilizadores adicionais podem ser atribuídos mais tarde.

## <a name="configure-user-provisioning-to-asana"></a>Configure o fornecimento de utilizadores à Asana

Esta secção guia-o através da ligação do seu AD Azure à aprovisionamento da conta de utilizador da Asana. Configura também o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas em Asana com base nas atribuições de utilizadores em Azure AD.

> [!TIP]
> Para ativar o único sinal de entrada baseado em SAML para asana, siga as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal instável pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se complementem.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Para configurar o fornecimento automático de conta de utilizador à Asana em Azure AD

1. No [portal Azure,](https://portal.azure.com)navegue pelo **Diretório Ativo do Azure** > **Aplicações Empresariais** > **todas as aplicações.**

1. Se já configurou a Asana para uma única inscrição, procure a sua instância de Asana utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **a Asana** na galeria de aplicações. Selecione **Asana** a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância de Asana e, em seguida, selecione o separador **Provisioning.**

1. Definir **o modo de provisionamento** para **automático**.

    ![Provisionamento asana](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. Sob a secção **de Credenciais de Administrador,** siga estas instruções para gerar o símbolo e insira-o em **Ficha Secreta:**

    a. Inscreva-se na [Asana](https://app.asana.com) utilizando a sua conta de administração.

    b. Selecione a foto de perfil a partir da barra superior e selecione as definições de nome da organização.

    c. Vá ao separador Contas de **Serviço.**

    d. Selecione **Adicionar Conta de Serviço**.

    e. Atualizar **nome** e **sobre** e a foto de perfil conforme necessário. Copie o símbolo em **Token**e selecione-o em **'Guardar alterações**' .

1. No portal Azure, selecione **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Asana. Se a ligação falhar, certifique-se de que a sua conta Asana tem permissões de administração e tente novamente o passo de Ligação de **Teste.**

1. Insira o endereço de e-mail de uma pessoa ou grupo que deseja receber notificações de erro no Email de **Notificação**. Selecione a caixa de verificação por baixo.

1. Selecione **Guardar**.

1. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Asana**.

1. Na secção **DeMapeamentos** de Atributos, reveja os atributos do utilizador a serem sincronizados de Azure AD para Asana. Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Asana para operações de atualização. Selecione **Guardar** para cometer quaisquer alterações. Para mais informações, consulte [Personalizar os mapeamentos de atributos](../app-provisioning/customize-application-attributes.md)de fornecimento de utilizador .

1. Para ativar o serviço de provisionamento de AD Azure para a Asana, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

1. Selecione **Guardar**.

Agora começa a sincronização inicial para quaisquer utilizadores atribuídos à Asana na secção **Utilizadores.** A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Utilize a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos registos de atividade de provisionamento. Os registos de auditoria descrevem todas as ações realizadas pelo serviço de provisionamento na sua app Asana.

Para obter mais informações sobre como ler os registos de fornecimento de AD Azure, consulte [relatório sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configure single sign-on](asana-tutorial.md) (Configurar o início de sessão único)
