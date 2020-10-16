---
title: 'Tutorial: Configurar o Serviço de Segurança Web Symantec (WSS) para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para fornecimento e desprovisionamento de contas de utilizador automáticas ao Symantec Web Security Service (WSS).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: 7b4a5e76ec8dad66bf300cc3e1d55471383f83ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91285847"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Tutorial: Configurar o Serviço de Segurança Web Symantec (WSS) para o provisionamento automático do utilizador

O objetivo deste tutorial é demonstrar os passos a serem realizados no Symantec Web Security Service (WSS) e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desprovisionamento automaticamente de utilizadores e/ou grupos ao Symantec Web Security Service (WSS).

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino do Serviço de Segurança Web Symantec (WSS)](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Uma conta de utilizador no Symantec Web Security Service (WSS) com permissões de administração.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Atribuir utilizadores ao Symantec Web Security Service (WSS)

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Symantec Web Security Service (WSS). Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Symantec Web Security Service (WSS) seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Dicas importantes para a atribuição de utilizadores ao Symantec Web Security Service (WSS)

* Recomenda-se que um único utilizador Azure AD seja designado ao Symantec Web Security Service (WSS) para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Symantec Web Security Service (WSS), deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Configuração Symantec Web Security Service (WSS) para provisionamento

Antes de configurar o Serviço de Segurança Web Symantec (WSS) para o fornecimento automático de utilizadores com AD Azure, terá de permitir o fornecimento scim no Symantec Web Security Service (WSS).

1. Inscreva-se na sua [consola de administração Symantec Web Security Service](https://portal.threatpulse.com/login.jsp). Navegar para o Serviço **de Soluções.**  >  **Service**

    ![Serviço de Segurança Web Symantec (WSS)](media/symantec-web-security-service/service.png)

2. Navegar para integrações de **manutenção**de conta  >  **Integrations**  >  **nova integração.**

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Selecione **utilizadores de terceiros & Grupos Sincronizados**. 

    ![Screenshot da opção de & Grupos de Utilizadores de Terceiros.](media/symantec-web-security-service/third-party-users.png)

4.  Copie o **URL SCIM** e **o Token.** Estes valores serão introduzidos no campo **URL** e **Secret Token** no separador Provisioning da sua aplicação Symantec Web Security Service (WSS) no portal Azure.

    ![Screenshot da caixa de diálogo new integration com as caixas de texto S C I M U R e Token chamadas.](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Adicione o Symantec Web Security Service (WSS) da galeria

Para configurar o Symantec Web Security Service (WSS) para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar o Symantec Web Security Service (WSS) da galeria de aplicações AD AZure à sua lista de aplicações geridas pela SaaS.

**Para adicionar o Symantec Web Security Service (WSS) na galeria de aplicações AD Azure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira o **Serviço de Segurança Web Symantec,** selecione o **Symantec Web Security Service** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Symantec Web Security Service (WSS) na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Configurar o fornecimento automático de utilizadores ao Symantec Web Security Service (WSS)

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Symantec Web Security Service (WSS) com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar um único sinal de symantec baseado em Symantec Web Security Service (WSS), seguindo as instruções fornecidas no [tutorial de assinatura única do Symantec Web Security Service (WSS).](symantec-tutorial.md) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Symantec Web Security Service (WSS) em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Symantec Web Security Service**.

    ![O link Symantec Web Security Service (WSS) na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção credenciais de administração, insira os valores **SCIM URL** e **Token** recuperados anteriormente em URL de **Inquilino** e **Token Secreto,** respectivamente. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Serviço de Segurança Web Symantec. Se a ligação falhar, certifique-se de que a sua conta Symantec Web Security Service (WSS) tem permissões de administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Symantec Web Security Service (WSS)**.

    ![Screenshot da secção Mappings com a opção Synchronize Azure Ative Directory Users para symantec Web Security Service W S S.](media/symantec-web-security-service/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Ad AD a Symantec Web Security Service (WSS) na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Symantec Web Security Service (WSS) para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Screenshot da secção de mapeamento de atribuição mostrando 16 propriedades correspondentes.](media/symantec-web-security-service/userattribute.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para Symantec Web Security Service**.

    ![Screenshot da secção Mappings com os Synchronize Azure Ative Directory Groups para symantec Web Security Service W S S opcionado.](media/symantec-web-security-service/groupmapping.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Symantec Web Security Service (WSS) na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são usados para combinar com os grupos no Symantec Web Security Service (WSS) para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Screenshot da secção de mapeamento de atribuição mostrando três propriedades correspondentes.](media/symantec-web-security-service/groupattribute.png)

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o Serviço de Segurança Web Symantec, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao Symantec Web Security Service (WSS) escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser efetua do que as sincronizações subsequentes. Para obter mais informações sobre o tempo que os utilizadores e/ou grupos demorarão a providenciar, consulte [quanto tempo demorará a providenciar aos utilizadores](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pode utilizar a secção **Estado Atual** para monitorizar o progresso e seguir links para o seu relatório de atividade de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure no Symantec Web Security Service (WSS). Para obter mais informações, [consulte verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
