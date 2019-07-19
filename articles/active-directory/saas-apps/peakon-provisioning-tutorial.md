---
title: 'Tutorial: Configurar o provisionamento automático de usuário de pico com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o pico.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: cc572206e20a1f2ef1a77efb8120ad4d2f805174
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67847941"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Tutorial: Configurar o pico para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas em pico e Azure Active Directory (AD do Azure) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para picos.

> [!NOTE]
>  Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte automatizar o [provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tenha os seguintes pré-requisitos

* Um inquilino do Azure AD.
* [Um locatário de pico](https://peakon.com/us/pricing/).
* Uma conta de usuário em pico com permissões de administrador.

## <a name="assigning-users-to-peakon"></a>Atribuindo usuários ao pico

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao pico. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao pico, seguindo as instruções aqui:

* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Dicas importantes para atribuir usuários ao pico 

* É recomendável que um único usuário do Azure AD seja atribuído ao pico para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao pico, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="set-up-peakon-for-provisioning"></a>Configurar o pico do provisionamento

1.  Entre em seu [console do administrador de pico](https://app.Peakon.com/login). Clique em **configuração**. 

    ![Console de administração de pico](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Selecione **integrações**.
    
    ![Pico-provisão do funcionário](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Habilite o **provisionamento de funcionários**.

    ![Pico-provisão do funcionário](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Copie os valores para a **URL do SCIM 2,0** e o **token de portador OAuth**. Esses valores serão inseridos no campo **URL do locatário** e **token secreto** na guia provisionamento do seu aplicativo de pico no portal do Azure.

    ![Criar token de pico](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Adicionar pico da Galeria

Para configurar o pico para o provisionamento automático de usuário com o Azure AD, você precisa adicionar o pico da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **pico**, selecione **pico** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Pico na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Configurando o provisionamento automático de usuário para o pico 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos em pico com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o pico, seguindo as instruções fornecidas no tutorial de [pico de logon único](peakon-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Para configurar o provisionamento automático de usuário para pico no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **pico**.

    ![O link de pico na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira os valores de token de **portador OAuth** e **URL do scim 2,0** recuperados anteriormente na **URL do locatário** e no **token secreto** , respectivamente. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao pico. Se a conexão falhar, verifique se sua conta de pico tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

8. Clique em **Guardar**.

9. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o pico**.

    ![Mapeamentos de usuário de pico](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Examine os atributos de usuário que são sincronizados do Azure AD para o pico na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário em pico para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos de usuário de pico](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no pico.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Todos os atributos de usuário personalizados em picos devem ser estendidos da extensão de usuário de SCIM personalizada `urn:ietf:params:scim:schemas:extension:peakon:2.0:User`de pico.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
## <a name="next-steps"></a>Passos Seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)