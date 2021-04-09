---
title: 'Tutorial: Configurar zscaler Private Access (ZPA) para fornecimento automático de utilizadores com Azure Ative Directy | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desaparável de contas de utilizadores ao Zscaler Private Access (ZPA).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 14708ddcc5c0e06ee58f5e9db5945c4e9f1a1d08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937149"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Tutorial: Configurar zscaler acesso privado (ZPA) para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Zscaler Private Access (ZPA) e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos ao Zscaler Private Access (ZPA).

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino de Acesso Privado Zscaler (ZPA)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Uma conta de utilizador em Zscaler Private Access (ZPA) com permissões de administração.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Atribuir utilizadores ao Zscaler Private Access (ZPA)

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Zscaler Private Access (ZPA). Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Zscaler Private Access (ZPA) seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Dicas importantes para a atribuição de utilizadores ao Zscaler Private Access (ZPA)

* Recomenda-se que um único utilizador Azure AD seja atribuído ao Zscaler Private Access (ZPA) para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Zscaler Private Access (ZPA), deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Configurar o Zscaler Private Access (ZPA) para o provisionamento

1. Inscreva-se na sua [Consola de Administração Zscaler Private Access (ZPA).](https://admin.private.zscaler.com/) Navegue para a **Administração > Configuração IdP**.

    ![Consola de administração Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Verifique se se está configurado um IdP para o único sinal de **inscrição.** Se não estiver configurado nenhum IdP, adicione um clicando no ícone mais no canto superior direito do ecrã.

    ![Zscaler Private Access (ZPA) Add SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Siga através do assistente **de configuração Add IdP** para adicionar um IdP. Deixe o campo **de sinalização único** definido para **o Utilizador**. Forneça um **Nome** e selecione os **Domínios** da lista de drop down. Clique em **Seguinte** para navegar para a próxima janela.

    ![Zscaler Private Access (ZPA) Add IdP](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Descarregue o **Certificado de Prestador de Serviços.** Clique em **Seguinte** para navegar para a próxima janela.

    ![Certificado Zscaler Private Access (ZPA) SP](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. Na janela seguinte, faça o upload do **Certificado de Prestador de Serviços** descarregado anteriormente.

    ![Certificado de upload Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Desloque-se para baixo para fornecer o **URL de inscrição única** e **idp ID Entity ID**.

    ![ID de acesso privado Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Desloque-se para **ativar o SCIM Sync**. Clique no **botão Gerar Novo Token.** Copie o **Token do Portador.** Este valor será introduzido no campo Secret Token no separador De Provisioning da sua aplicação Zscaler Private Access (ZPA) no portal Azure.

    ![Zscaler Private Access (ZPA) Criar Token](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Para localizar o URL do **inquilino,** navegue para a **Administração > Configuração IdP**. Clique no nome da configuração IdP recentemente adicionada listada na página.

    ![Nome do Idp de Acesso Privado Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Percorra para baixo para ver o **ponto final do fornecedor de serviços SCIM** no final da página. Copie o **ponto final do fornecedor de serviços SCIM**. Este valor será introduzido no campo URL do Inquilino no separador De Provisionamento da sua aplicação Zscaler Private Access (ZPA) no portal Azure.

    ![Zscaler Private Access (ZPA) SCIM URL](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Adicione Zscaler Private Access (ZPA) da galeria

Antes de configurar o Zscaler Private Access (ZPA) para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar zscaler Private Access (ZPA) da galeria de aplicações AD Azure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Zscaler Private Access (ZPA) da galeria de aplicações AZure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **zscaler Private Access (ZPA)**, selecione **Zscaler Private Access (ZPA)** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Zscaler Private Access (ZPA) na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Configurar o fornecimento automático do utilizador ao Zscaler Private Access (ZPA) 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Zscaler Private Access (ZPA) com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para zscaler private access (ZPA) seguindo as instruções fornecidas no [tutorial de acesso privado Zscaler (ZPA) único.](./zscalerprivateaccess-tutorial.md) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

> [!NOTE]
> Quando os utilizadores e grupos são provisionados ou des provisionados, recomendamos reiniciar periodicamente o provisionamento para garantir que os membros do grupo sejam corretamente atualizados. Fazer um reinício forçará o nosso serviço a reavaliar todos os grupos e a atualizar os membros.  

> [!NOTE]
> Para saber mais sobre o ponto final scim do Zscaler Private Access, consulte [isto](https://www.zscaler.com/partners/microsoft).

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Zscaler Private Access (ZPA) em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Zscaler Private Access (ZPA)**.

    ![O link Zscaler Private Access (ZPA) na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **credenciais de administração,** insira o valor **endpoint do fornecedor de serviço SCIM** recuperado anteriormente no **URL do inquilino.** Insira o valor **do Token do Portador** recuperado anteriormente em Secret **Token**. Clique em **'Testar' Ligação** para garantir que o Azure AD pode ligar-se ao Zscaler Private Access (ZPA). Se a ligação falhar, certifique-se de que a sua conta Zscaler Private Access (ZPA) tem permissões de administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Zscaler Private Access (ZPA)**.

    ![Mapeamentos de utilizadores de acesso privado Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Zscaler Private Access (ZPA) na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Zscaler Private Access (ZPA) para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Zscaler Private Access (ZPA)**.

    ![Mapeamentos do grupo Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Zscaler Private Access (ZPA) na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos em Zscaler Private Access (ZPA) para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do grupo Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o Acesso Privado Zscaler (ZPA), altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao Zscaler Private Access (ZPA) escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Azure AD no Zscaler Private Access (ZPA).

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)