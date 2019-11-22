---
title: 'Tutorial: provisionamento de usuário para asana-Azure AD'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o asana.
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
ms.openlocfilehash: 60a237e4295f17ce37f622022d318e9f2aff24d7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276592"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Tutorial: configurar o asana para o provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa executar no asana e no Azure Active Directory (Azure AD) para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o asana.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure AD
* Um locatário do asana com um plano [Enterprise](https://www.asana.com/pricing) ou melhor habilitado
* Uma conta de usuário no asana com permissões de administrador

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API do asana](https://asana.com/developers/api-reference/users), que está disponível para o asana.

## <a name="assign-users-to-asana"></a>Atribuir usuários ao asana

O Azure AD usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você deve decidir quais usuários no Azure AD precisam de acesso ao seu aplicativo asana. Em seguida, você pode atribuir esses usuários ao seu aplicativo asana seguindo estas instruções:

[Atribuir um usuário a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Dicas importantes para atribuir usuários ao asana

Recomendamos que você atribua um único usuário do Azure AD ao asana para testar a configuração de provisionamento. Usuários adicionais podem ser atribuídos posteriormente.

## <a name="configure-user-provisioning-to-asana"></a>Configurar o provisionamento de usuário para o asana

Esta seção orienta você pela conexão do Azure AD à API de provisionamento de conta de usuário do asana. Você também configura o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no asana com base nas atribuições de usuário no Azure AD.

> [!TIP]
> Para habilitar o logon único baseado em SAML para o asana, siga as instruções fornecidas na [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Para configurar o provisionamento automático de conta de usuário para o asana no Azure AD

1. Na [portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory** > **aplicativos empresariais** > **todos os aplicativos** .

1. Se você já tiver configurado o asana para logon único, pesquise sua instância do asana usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **asana** na Galeria de aplicativos. Selecione **asana** nos resultados da pesquisa e adicione-o à lista de aplicativos.

1. Selecione sua instância do asana e, em seguida, selecione a guia **provisionamento** .

1. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento do asana](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. Na seção **credenciais de administrador** , siga estas instruções para gerar o token e inseri-lo no **token secreto**:

    a. Entre no [asana](https://app.asana.com) usando sua conta de administrador.

    b. Selecione a foto do perfil na barra superior e selecione as configurações atuais de nome da organização.

    c. Vá para a guia **contas de serviço** .

    d. Selecione **adicionar conta de serviço**.

    e. Atualize o **nome** e **sobre** o e a foto do perfil, conforme necessário. Copie o token no **token**e selecione-o em **salvar alterações**.

1. Na portal do Azure, selecione **testar conexão** para garantir que o Azure ad possa se conectar ao aplicativo asana. Se a conexão falhar, verifique se sua conta do asana tem permissões de administrador e tente a etapa **testar conexão** novamente.

1. Insira o endereço de email de uma pessoa ou grupo que você deseja receber notificações de erro de provisionamento no **email de notificação**. Marque a caixa de seleção abaixo.

1. Selecione **Guardar**.

1. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para asana**.

1. Na seção **mapeamentos de atributo** , examine os atributos de usuário a serem sincronizados do Azure ad para o asana. Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no asana para operações de atualização. Selecione **salvar** para confirmar as alterações. Para obter mais informações, consulte [Personalizar mapeamentos de atributo de provisionamento de usuário](../manage-apps/customize-application-attributes.md).

1. Para habilitar o serviço de provisionamento do Azure AD para o asana, na seção **configurações** , altere o **status de provisionamento** para **ativado**.

1. Selecione **Guardar**.

Agora, a sincronização inicial é iniciada para todos os usuários atribuídos ao asana na seção **usuários** . A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Use a seção **detalhes de sincronização** para monitorar o progresso e siga os links para os logs de atividade de provisionamento. Os logs de auditoria descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo asana.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatório sobre o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configure single sign-on](asana-tutorial.md) (Configurar o início de sessão único)
