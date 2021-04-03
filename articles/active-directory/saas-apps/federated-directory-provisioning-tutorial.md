---
title: 'Tutorial: Configurar diretório federado para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desaprovisionamento de contas de utilizadores ao Diretório Federado.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 8ca7654d930247f70d85cbc20fbbeb961223f05f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998367"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Tutorial: Configure Diretório Federado para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Diretório Federado e no Diretório Ativo Azure (Azure AD) para configurar a Azure AD para fornecimento e desprovisionamento automática de utilizadores e/ou grupos ao Diretório Federado.

> [!NOTE]
>  Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um Diretório Federado.](https://www.federated.directory/pricing)
* Uma conta de utilizador no Diretório Federado com permissões de Administração.

## <a name="assign-users-to-federated-directory"></a>Atribuir utilizadores ao Diretório Federado
O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Diretório Federado. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Diretório Federado seguindo as instruções aqui:

 * [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Dicas importantes para atribuir utilizadores ao Diretório Federado
 * Recomenda-se que um único utilizador AZure AD seja designado para o Diretório Federado para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Diretório Federado, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função Acesso Predefinido estão excluídos do provisionamento.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Criação do Diretório Federado para provisionamento

Antes de configurar o Diretório Federado para o fornecimento automático de utilizadores com Azure AD, terá de permitir o fornecimento de SCIM no Diretório Federado.

1. Inscreva-se na sua [Consola de Administrador Federado](https://federated.directory/of)

    :::image type="content" source="media/federated-directory-provisioning-tutorial/companyname.png" alt-text="Screenshot da consola de administração federada mostrando um campo para introduzir um nome da empresa. Os botões de inscrição também são visíveis." border="false":::

2. Navegue para **Diretórios > Diretórios de utilizadores** e selecione o seu inquilino. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/ad-user-directories.png" alt-text="Screenshot da consola de administração federada, com Diretórios e Diretório Federado Azure A D Test em destaque." border="false":::

3.  Para gerar um token portador permanente, navegue até **Diretório Keys > Criar Nova Chave.** 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated01.png" alt-text="Screenshot da página de chaves do Diretório da consola de administração federada. Destaca-se o novo botão de tecla Create." border="false":::

4. Crie uma chave de diretório. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated02.png" alt-text="Screenshot da página chave do diretório Create da consola de administração federada, com campos de nome e descrição e um botão de chave Criar." border="false":::
    

5. Copie o valor **do Token de acesso.** Este valor será introduzido no campo **Secret Token** no separador Provisioning da sua aplicação de Diretório Federado no portal Azure. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated03.png" alt-text="Screenshot de uma página na consola de administração federada. Um espaço reservado de acesso e um nome chave, descrição e emitente são visíveis." border="false":::
    
## <a name="add-federated-directory-from-the-gallery"></a>Adicione diretório federado da galeria

Para configurar o Diretório Federado para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar o Diretório Federado da galeria de aplicações AD AD da sua lista de aplicações geridas pelo SaaS.

**Para adicionar Diretório Federado da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **o Diretório Federado,** selecione **Diretório Federado** no painel de resultados.

    ![Diretório Federado na lista de resultados](common/search-new-app.png)

5. Navegue para o **URL** realçado abaixo num navegador separado. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage1.png" alt-text="Screenshot de uma página no portal Azure que exibe informações sobre o Diretório Federado. O valor U R L está em destaque." border="false":::

6. CLIQUE **EM REGISTAR**-

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated04.png" alt-text="Screenshot do menu principal no site do Diretório Federado. O botão 'Iniciar sessão' está realçado." border="false":::

7.  Como o Diretório Federado é uma aplicação OpenIDConnect, opte por iniciar sessão no Diretório Federado utilizando a sua conta de trabalho da Microsoft.
    
    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage3.png" alt-text="Screenshot da página de teste S C I M A D no site do Diretório Federado. O login na sua conta Microsoft está em destaque." border="false":::
 
8. Após uma autenticação bem sucedida, aceite o pedido de consentimento para a página de consentimento. A aplicação será então automaticamente adicionada ao seu inquilino e será redirecionado para a sua conta de Diretório Federado.

    ![diretório federado Adicionar SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Configuração do fornecimento automático de utilizadores ao Diretório Federado 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Diretório Federado com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Para configurar o provisionamento automático do utilizador para o Diretório Federado em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Diretório Federado.**

    ![O link do Diretório Federado na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://api.federated.directory/v2/` na URL do inquilino. Insira o valor que recuperou e guardou mais cedo do Diretório Federado em **Secret Token.** Clique em **'Testar' Ligação** para garantir que o Azure AD pode ligar-se ao Diretório Federado. Se a ligação falhar, certifique-se de que a sua conta de Diretório Federado tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Federated Directory**.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-mappings.png" alt-text="Screenshot da secção mappings. Em Nome, é destacado o Synchronize Azure Ative Directory Users to Federated Directory." border="false":::
    
    
11. Reveja os atributos do utilizador que são sincronizados de Azure AD a Diretório Federado na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas de utilizador no Diretório Federado para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-attributes.png" alt-text="Screenshot da página De mapeamentos de atributos. Uma tabela lista os atributos do Diretório Ativo Azure e do Diretório Federado e o estado de correspondência." border="false":::
    

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o Diretório Federado, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao Diretório Federado, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Ad Azure no Diretório Federado.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
