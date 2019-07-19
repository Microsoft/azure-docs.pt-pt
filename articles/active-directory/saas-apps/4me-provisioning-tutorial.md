---
title: 'Tutorial: Configurar o 4me para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o 4me.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 55aab6546efa323d1ddcd242cf75281c15e8e0e1
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849644"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Tutorial: Configurar o 4me para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no 4me e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no 4me.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte automatizar o [provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do 4me](https://www.4me.com/trial/)
* Uma conta de usuário no 4me com permissões de administrador.

## <a name="add-4me-from-the-gallery"></a>Adicionar o 4me da Galeria

Antes de configurar o 4me para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o 4me da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o 4me da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **4me**, selecione **4me** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![4me na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Atribuindo usuários ao 4me

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao 4me. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao 4me seguindo as instruções aqui:

* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Dicas importantes para atribuir usuários ao 4me

* É recomendável que um único usuário do Azure AD seja atribuído ao 4me para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao 4me, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Configurando o provisionamento automático de usuário para o 4me 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no 4me com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o 4me, seguindo as instruções fornecidas no [tutorial de logon único do 4me](4me-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para 4me no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **4me**.

    ![O link do 4me na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Para recuperar a **URL do locatário** e o **token secreto** da sua conta do 4me, siga o passo a passos, conforme descrito na etapa 6.

6. Entre no console do administrador do 4me. Navegue até **configurações**.

    ![Configurações de 4me](media/4me-provisioning-tutorial/4me01.png)

    Digite os **aplicativos** na barra de pesquisa.

    ![aplicativos 4me](media/4me-provisioning-tutorial/4me02.png)

    Abra a lista suspensa **scim** para recuperar o token secreto e o ponto de extremidade SCIM.

    ![4me SCIM](media/4me-provisioning-tutorial/4me03.png)

7. Ao preencher os campos mostrados na etapa 5, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao 4me. Se a conexão falhar, verifique se sua conta do 4me tem permissões de administrador e tente novamente.

    ![Certificado de](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para 4me**.

    ![Mapeamentos de usuário 4me](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. Examine os atributos de usuário que são sincronizados do Azure AD para o 4me na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no 4me para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Mapeamentos de usuário 4me](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para 4me**.

    ![Mapeamentos de usuário 4me](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. Examine os atributos de grupo que são sincronizados do Azure AD para o 4me na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no 4me para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Mapeamentos de grupo 4me](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD para o 4me, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

16. Defina os usuários e/ou grupos que você deseja provisionar para o 4me escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no 4me.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* 4me tem diferentes URLs de ponto de extremidade de SCIM para ambientes de teste e produção. O primeiro termina com **. QA** enquanto o último termina com **. com**
* os tokens secretos gerados pelo 4me têm uma data de expiração de um mês a partir da geração.
* 4me não dá suporte a operações de **exclusão**

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)