---
title: 'Tutorial: configurar o Salesforce para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32f3c439460ddc61dbf08fc4e8d7b7a000aa20f9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849178"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Tutorial: configurar o Salesforce para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas necessárias para executar no Salesforce e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Salesforce.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure Active Directory
* Um locatário do Salesforce.com

> [!IMPORTANT]
> Se você estiver usando uma conta de avaliação do Salesforce.com, não será possível configurar o provisionamento automatizado de usuários. As contas de avaliação não têm o acesso à API necessário habilitado até que sejam adquiridas. Você pode contornar essa limitação usando uma conta de [desenvolvedor](https://developer.salesforce.com/signup) gratuita para concluir este tutorial.

Se você estiver usando um ambiente de área restrita Salesforce, consulte o [tutorial de integração do Salesforce sandbox](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Atribuindo usuários ao Salesforce

Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários ou grupos no Azure AD precisam de acesso ao seu aplicativo Salesforce. Depois de tomar essa decisão, você pode atribuir esses usuários ao seu aplicativo Salesforce seguindo as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Dicas importantes para atribuir usuários ao Salesforce

* É recomendável que um único usuário do Azure AD seja atribuído ao Salesforce para testar a configuração de provisionamento. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Salesforce, você deve selecionar uma função de usuário válida. A função de "acesso padrão" não funciona para provisionamento

    > [!NOTE]
    > Este aplicativo importa perfis do Salesforce como parte do processo de provisionamento, que o cliente pode querer selecionar ao atribuir usuários no Azure AD. Observe que os perfis que são importados do Salesforce aparecem como funções no Azure AD.

## <a name="enable-automated-user-provisioning"></a>Habilitar provisionamento automatizado de usuários

Esta seção orienta você pela conexão do Azure AD com a [API de provisionamento de conta de usuário do Salesforce-v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)e pela configuração do serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no Salesforce com base na atribuição de usuário e de grupo no Azure AD.

> [!Tip]
> Você também pode optar por habilitar o logon único baseado em SAML para o Salesforce, seguindo as instruções fornecidas em [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o provisionamento automático de conta de usuário

O objetivo desta seção é descrever como habilitar o provisionamento de usuário de Active Directory contas de usuário para o Salesforce.

1. Na [portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > aplicativos empresariais > todos os aplicativos** .

2. Se você já tiver configurado o Salesforce para logon único, pesquise sua instância do Salesforce usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Salesforce** na Galeria de aplicativos. Selecione Salesforce nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione sua instância do Salesforce e, em seguida, selecione a guia **provisionamento** .

4. Defina o **modo de provisionamento** como **automático**.

    ![provisionamento](./media/salesforce-provisioning-tutorial/provisioning.png)

5. Na seção **credenciais de administrador** , forneça as seguintes definições de configuração:

    a. Na caixa de texto **nome de usuário administrador** , digite um nome de conta do Salesforce que tenha o perfil de **administrador do sistema** em Salesforce.com atribuído.

    b. Na caixa de texto **senha do administrador** , digite a senha dessa conta.

6. Para obter seu token de segurança do Salesforce, abra uma nova guia e entre na mesma conta do administrador do Salesforce. No canto superior direito da página, clique em seu nome e em **configurações**.

    ![Habilitar o provisionamento automático de usuário](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Habilitar o provisionamento automático de usuário")

7. No painel de navegação esquerdo, clique em **minhas informações pessoais** para expandir a seção relacionada e, em seguida, clique em **Redefinir meu token de segurança**.
  
    ![Habilitar o provisionamento automático de usuário](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Habilitar o provisionamento automático de usuário")

8. Na página **Redefinir token de segurança** , clique no botão **Redefinir token de segurança** .

    ![Habilitar o provisionamento automático de usuário](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Habilitar o provisionamento automático de usuário")

9. Verifique a caixa de entrada de email associada a essa conta de administrador. Procure um email de Salesforce.com que contenha o novo token de segurança.

10. Copie o token, vá para a janela do Azure AD e cole-o no campo **token secreto** .

11. A **URL do locatário** deverá ser inserida se a instância do Salesforce estiver na nuvem do governo do Salesforce. Caso contrário, é opcional. Insira a URL do locatário usando o formato "https://\<sua instância\>. my.salesforce.com", substituindo \<\> da instância pelo nome da sua instância do Salesforce.

12. No portal do Azure, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao seu aplicativo Salesforce.

13. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento e marque a caixa de seleção abaixo.

14. Clique em **salvar.**  

15. Na seção mapeamentos, selecione **sincronizar Azure Active Directory usuários para o Salesforce.**

16. Na seção **mapeamentos de atributo** , examine os atributos de usuário que são sincronizados do Azure ad para o Salesforce. Observe que os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Salesforce para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

17. Para habilitar o serviço de provisionamento do Azure AD para o Salesforce, altere o **status de provisionamento** para **ativado** na seção Configurações

18. Clique em **salvar.**

> [!NOTE]
> Depois que os usuários são provisionados no aplicativo Salesforce, o administrador precisa definir as configurações específicas de idioma para eles. Consulte este artigo para obter mais detalhes sobre [a](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) configuração de idioma.

Isso inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Salesforce na seção usuários e grupos. Observe que a sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo Salesforce.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="common-issues"></a>Problemas comuns
* Se você estiver tendo problemas para autorizar o acesso ao Salesforce, verifique o seguinte:
    * As credenciais usadas têm acesso de administrador ao Salesforce.
    * A versão do Salesforce que você está usando dá suporte a Acesso via Web (por exemplo, Developer, Enterprise, sandbox e edições ilimitadas do Salesforce.)
    * O acesso à API Web está habilitado para o usuário.
* O serviço de provisionamento do Azure AD dá suporte à linguagem de provisionamento, à localidade e ao fuso horário de um usuário. Esses atributos estão nos mapeamentos de atributo padrão, mas não têm um atributo de origem padrão. Verifique se você selecionou o atributo de origem padrão e se o atributo de origem está no formato esperado pelo SalesForce. Por exemplo, localeSidKey para inglês (Estados Unidos) é en_US. Examine as diretrizes fornecidas [aqui](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) para determinar o formato localeSidKey apropriado. Os formatos languageLocaleKey podem ser encontrados [aqui](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5). Além de garantir que o formato esteja correto, talvez seja necessário garantir que o idioma esteja habilitado para seus usuários, conforme descrito [aqui](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5). 
* **SalesforceLicenseLimitExceeded:** Não foi possível criar o usuário no aplicativo de destino porque não há licenças disponíveis para esse usuário. Adquira licenças adicionais para o aplicativo de destino ou examine as atribuições de usuário e a configuração de mapeamento de atributo para garantir que os usuários corretos sejam atribuídos com os atributos corretos.
* **SalesforceDuplicateUserName:** O usuário não pode ser provisionado porque tem um ' nome de usuário ' Salesforce.com que está duplicado em outro locatário Salesforce.com.  No Salesforce.com, os valores para o atributo ' username ' devem ser exclusivos em todos os locatários do Salesforce.com.  Por padrão, o userPrincipalName de um usuário em Azure Active Directory se torna seu ' username ' em Salesforce.com.   Você tem duas opções.  Uma opção é localizar e renomear o usuário com o ' nome de usuário ' duplicado no outro locatário do Salesforce.com, se você administrar esse outro locatário também.  A outra opção é remover o acesso do usuário Azure Active Directory para o locatário Salesforce.com com o qual seu diretório está integrado. Tentaremos novamente essa operação na próxima tentativa de sincronização. 
* **SalesforceRequiredFieldMissing:** O Salesforce requer que determinados atributos estejam presentes no usuário para criar ou atualizar o usuário com êxito. Este usuário não tem um dos atributos necessários. Certifique-se de que atributos como email e alias sejam preenchidos em todos os usuários que você deseja que sejam provisionados no Salesforce. Você pode definir o escopo de usuários que não têm esses atributos usando [filtros de escopo baseados em atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 
* O mapeamento de atributo padrão para provisionamento para o Salesforce inclui a expressão SingleAppRoleAssignments para mapear appRoleAssignments no Azure AD para ProfileName no Salesforce. Verifique se os usuários não têm várias atribuições de função de aplicativo no Azure AD, pois o mapeamento de atributo só dá suporte ao provisionamento de uma função. 
* O Salesforce exige que as atualizações de email sejam aprovadas manualmente antes de serem alteradas. Como resultado, você poderá ver várias entradas nos logs de provisionamento para atualizar o email do usuário (até que a alteração de email tenha sido aprovada).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar logon único](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
