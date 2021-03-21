---
title: 'Tutorial: Configurar SmartFile para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao SmartFile.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 03c7efd390d4714680ed2ccd54d297b8137aaafc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348675"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Tutorial: Configurar SmartFile para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no SmartFile e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos para o SmartFile.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino SmartFile.](https://www.SmartFile.com/pricing/)
* Uma conta de utilizador no SmartFile com permissões de Administração.

## <a name="assigning-users-to-smartfile"></a>Atribuir utilizadores ao SmartFile

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao SmartFile. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao SmartFile seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Dicas importantes para atribuir utilizadores ao SmartFile

* Recomenda-se que um único utilizador Azure AD seja atribuído ao SmartFile para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao SmartFile, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="setup-smartfile-for-provisioning"></a>Configurar SmartFile para provisionamento

Antes de configurar o SmartFile para o fornecimento automático de utilizadores com AZure AD, terá de ativar o fornecimento scim no SmartFile e recolher detalhes adicionais necessários.

1. Inscreva-se na sua Consola de Administração SmartFile. Navegue para o canto superior direito da Consola de Administração SmartFile. Selecione **a chave de produtos**.

    ![Consola de administração SmartFile](media/smartfile-provisioning-tutorial/login.png)

2. Para gerar um símbolo ao portador, copie a **chave de produto** e a **palavra-passe do produto.** Cole-os num bloco de notas com um cólon entre eles.
    
     ![Screenshot da secção chave do produto com a chave de produto e caixas de texto de senha de produto chamadas.](media/smartfile-provisioning-tutorial/auth.png)

    ![Screenshot de texto simples mostrando chave de produto e senha de produto separada por um cólon.](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>Adicione SmartFile da galeria

Para configurar o SmartFile para o fornecimento automático de utilizadores com AZure AD, é necessário adicionar o SmartFile da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar SmartFile na galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **o SmartFile,** selecione **SmartFile** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![SmartFile na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Configurar o fornecimento automático de utilizadores ao SmartFile 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em SmartFile com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para o SmartFile, seguindo as instruções fornecidas no [tutorial de assinatura SmartFile Single](SmartFile-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o SmartFile em AD AZure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SmartFile**.

    ![O link SmartFile na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5.  Sob a secção **de Credenciais de Administração,** entrada `https://<SmartFile sitename>.smartfile.com/ftp/scim` na URL do **inquilino.** Um exemplo seria `https://demo1test.smartfile.com/ftp/scim` como. Introduza o valor **do símbolo** do Portador (ProductKey:ProductPassword) que recuperou anteriormente em **Secret Token**. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao SmartFile. Se a ligação falhar, certifique-se de que a sua conta SmartFile tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para SmartFile**.

    ![Mapeamentos de utilizadores smartFile](media/smartfile-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de AD AD a SmartFile na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no SmartFile para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador SmartFile](media/smartfile-provisioning-tutorial/userattribute.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to SmartFile**.

    ![Mapeamentos do grupo SmartFile](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Reveja os atributos do grupo que são sincronizados de AD AD a SmartFile na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos do SmartFile para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do grupo SmartFile](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de fornecimento de Ad Azure para SmartFile, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao SmartFile, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

    Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure no SmartFile.

    Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Limitações do conector

* O SmartFile só suporta eliminações duras. 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

 [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
