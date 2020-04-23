---
title: 'Tutorial: Configure Zscaler Private Access (ZPA) para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Saiba como configurar o Diretório Ativo do Azure para fornecer e desfornecer automaticamente contas de utilizadores ao Zscaler Private Access (ZPA).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: ee9128c3-ff02-4739-8c51-0693d8451742
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 609d2726eaaaeb49210e19f000bcc2faef1de5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064160"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Tutorial: Configure Zscaler Private Access (ZPA) para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Zscaler Private Access (ZPA) e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos para o Zscaler Private Access (ZPA).

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino de Acesso Privado Zscaler (ZPA)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Uma conta de utilizador no Zscaler Private Access (ZPA) com permissões da Admin.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Atribuir utilizadores ao Zscaler Private Access (ZPA)

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos da AD Azure que precisam de acesso ao Acesso Privado zscaler (ZPA). Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Zscaler Private Access (ZPA) seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Dicas importantes para atribuir utilizadores ao Zscaler Private Access (ZPA)

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao Zscaler Private Access (ZPA) para testar a configuração automática de fornecimento de utilizadores. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Zscaler Private Access (ZPA), deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Configurar acesso privado zscaler (ZPA) para o fornecimento

1. Inscreva-se na consola [de acesso privado zscaler (ZPA)](https://admin.private.zscaler.com/). Navegue para **a Configuração IdP da Administração >**.

    ![Consola de acesso privado zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Verifique se está configurado um IDP para **um único sinal.** Se não estiver configurado nenhum IdP, adicione um clicando no ícone plus no canto superior direito do ecrã.

    ![Acesso Privado Zscaler (ZPA) Adicionar SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Siga através do assistente de **configuração Add IDP** para adicionar um IDP. Deixe o campo de **sinalização single** definido para **o utilizador**. Forneça um **Nome** e selecione os **Domínios** da lista de descidas. Clique em **Next** para navegar para a próxima janela.

    ![Acesso Privado Zscaler (ZPA) Adicionar IdP](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Faça o download do Certificado de **Prestador de Serviços.** Clique em **Next** para navegar para a próxima janela.

    ![Certificado SP de Acesso Privado zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. Na janela seguinte, faça o upload do Certificado de Prestador de **Serviços** descarregado anteriormente.

    ![Certificado de upload de acesso privado zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Desloque-se para baixo para fornecer o URL de **inscrição único** e **ID da entidade idp**.

    ![Zscaler Private Access (ZPA) IdP ID](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Desloque-se para baixo até ativar o **SCIM Sync**. Clique em **Generate new token** botão. Copiar o **Token Bearer.** Este valor será inserido no campo Secret Token no separador de provisionamento da sua aplicação Zscaler Private Access (ZPA) no portal Azure.

    ![Acesso Privado Zscaler (ZPA) Criar Token](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Para localizar o URL do **Inquilino,** navegue para **a Administração > Configuração IDP**. Clique no nome da configuração idp recém-adicionada listada na página.

    ![Zscaler Private Access (ZPA) Idp Name](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Desloque-se para baixo para ver o Ponto Final do Prestador de **Serviços SCIM** no final da página. Copiar o ponto final do prestador de **serviços SCIM**. Este valor será inserido no campo URL do Arrendatário no separador de fornecimento da sua aplicação Zscaler Private Access (ZPA) no portal Azure.

    ![Zscaler Private Access (ZPA) SCIM URL](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Adicione o Acesso Privado Zscaler (ZPA) da galeria

Antes de configurar o Acesso Privado zscaler (ZPA) para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar o Acesso Privado zscaler (ZPA) à galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar o Acesso Privado zscaler (ZPA) da galeria de aplicações da AD Azure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o **Zscaler Private Access (ZPA)**, selecione **Zscaler Private Access (ZPA)** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Acesso Privado Zscaler (ZPA) na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Configurar o fornecimento automático de utilizadores ao Acesso Privado zscaler (ZPA) 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento da AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Acesso Privado de Zscaler (ZPA) com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para o Acesso Privado de Zscaler (ZPA), seguindo as instruções fornecidas no tutorial de acesso único do [Zscaler Private Access (ZPA).](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial) O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

> [!NOTE]
> Para saber mais sobre o ponto final do SCIM da Zscaler Private Access, consulte [isto](https://www.zscaler.com/partners/microsoft).

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para acesso privado zscaler (ZPA) em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zscaler Private Access (ZPA)**.

    ![O link Zscaler Private Access (ZPA) na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção **de Credenciais de Administrador,** insere o valor final do fornecedor de **serviços SCIM** recuperado anteriormente no URL do **Arrendatário**. Insere o valor **token** do portador recuperado anteriormente em **Secret Token**. Clique em **Ligação** de Teste para garantir que o Azure AD pode ligar-se ao Acesso Privado zscaler (ZPA). Se a ligação falhar, certifique-se de que a sua conta Zscaler Private Access (ZPA) tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Zscaler Private Access (ZPA)**.

    ![Mapeamento de utilizadores de acesso privado zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Zscaler Private Access (ZPA) na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Zscaler Private Access (ZPA) para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador de acesso privado zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Zscaler Private Access (ZPA)**.

    ![Mapeamentos do grupo Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Zscaler Private Access (ZPA) na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em Zscaler Private Access (ZPA) para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do Grupo Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para acesso privado zscaler (ZPA), altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao Zscaler Private Access (ZPA) escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure no Zscaler Private Access (ZPA).

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

