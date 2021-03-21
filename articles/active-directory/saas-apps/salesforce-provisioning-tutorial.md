---
title: 'Tutorial: Configure Salesforce para o fornecimento automático de utilizadores com diretório Azure Ative| Microsoft Docs'
description: Aprenda os passos necessários para realizar na Salesforce e Azure AD para obter e desa provisionar automaticamente as contas de utilizadores da Azure AD à Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: 2a4d3f3f9465b8813cdf6ee26760d819d73a08c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94353109"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Tutorial: Configure Salesforce para o fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar os passos necessários para executar na Salesforce e Azure AD para fornecimento e desaquivisionamento automática de contas de utilizadores do Azure AD para a Salesforce.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino de diretório Azure Ative
* Um inquilino Salesforce.com

> [!IMPORTANT]
> Se estiver a utilizar uma conta de teste Salesforce.com, então não poderá configurar o fornecimento automatizado de utilizadores. As contas de ensaio não têm o acesso apieiro necessário até serem adquiridas. Você pode contornar esta limitação usando uma [conta de desenvolvedor](https://developer.salesforce.com/signup) gratuito para completar este tutorial.

Se estiver a utilizar um ambiente Salesforce Sandbox, consulte o [tutorial de integração da Salesforce Sandbox.](./salesforce-sandbox-tutorial.md)

## <a name="assigning-users-to-salesforce"></a>Atribuir utilizadores à Salesforce

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em AZure AD são sincronizados.

Antes de configurar e ativar o serviço de fornecimento, tem de decidir quais os utilizadores ou grupos em Azure AD que precisam de acesso à sua aplicação Salesforce. Depois de tomar esta decisão, pode atribuir estes utilizadores à sua app Salesforce seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Dicas importantes para atribuir utilizadores à Salesforce

* Recomenda-se que um único utilizador AZure AD seja designado à Salesforce para testar a configuração de provisionamento. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à Salesforce, deve selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento

    > [!NOTE]
    > Esta aplicação importa perfis da Salesforce como parte do processo de provisionamento, que o cliente pode querer selecionar ao atribuir utilizadores em Azure AD. Por favor, note que os perfis que são importados da Salesforce aparecem como Roles in Azure AD.

## <a name="enable-automated-user-provisioning"></a>Permitir o fornecimento automatizado de utilizadores

Esta secção guia-o através da ligação do seu AZure AD à [conta de utilizador da Salesforce que fornece a API - v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)- e configura o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas na Salesforce com base na atribuição de utilizadores e grupos no Azure AD.

> [!Tip]
> Pode também optar por ativar Sign-On única baseada em SAML para a Salesforce, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se elogiem mutuamente.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o provisionamento automático da conta de utilizador

O objetivo desta secção é delinear como permitir o fornecimento de contas de utilizador do Ative Directory à Salesforce.

1. No [portal Azure,](https://portal.azure.com)consulte a secção **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

2. Se já configurar a Salesforce para um único sign-on, procure a sua instância de Salesforce utilizando o campo de pesquisa. Caso contrário, **selecione Add** e procure por **Salesforce** na galeria de aplicações. Selecione Salesforce a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

3. Selecione a sua instância de Salesforce e, em seguida, selecione o **separador Provisioning.**

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![O Screenshot mostra a página de Provisionamento salesforce, com o modo de provisionamento definido para Automático e outros valores que pode definir.](./media/salesforce-provisioning-tutorial/provisioning.png)

5. Na secção **Credenciais de Administração,** forneça as seguintes definições de configuração:

    a. Na caixa de texto do **nome de utilizador Admin,** digite um nome de conta Salesforce que tenha o perfil **de Administrador do Sistema** em Salesforce.com atribuído.

    b. Na caixa de texto da **Palavra-passe Admin,** digite a palavra-passe para esta conta.

6. Para obter o seu token de segurança Salesforce, abra um novo separador e inscreva-se na mesma conta de administração da Salesforce. No canto superior direito da página, clique no seu nome e, em seguida, clique em **Definições**.

    ![A screenshot mostra o link Definições selecionado.](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Permitir o fornecimento automático do utilizador")

7. No painel de navegação à esquerda, clique em **My Personal Information** para expandir a secção relacionada e, em seguida, clique em Redefinir o **Token de Segurança**.
  
    ![A screenshot mostra reset My Security Token selecionado a partir de My Personal Information.](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Permitir o fornecimento automático do utilizador")

8. Na página **reset Security Token,** clique no botão **Reset Security Token.**

    ![O Screenshot mostra a página de Token de Segurança de Repouso, com texto explicativo e a opção de redefinir o token de segurança](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Permitir o fornecimento automático do utilizador")

9. Verifique a caixa de entrada de e-mail associada a esta conta de administração. Procure um e-mail de Salesforce.com que contenha o novo símbolo de segurança.

10. Copie o token, vá à sua janela AD Azure e cole-a no campo **Secret Token.**

11. O **URL do inquilino** deve ser introduzido se o caso de Salesforce estiver na Nuvem do Governo salesforce. Caso contrário, é opcional. Insira o URL do inquilino utilizando o formato de "https:// \<your-instance\> .my.salesforce.com", \<your-instance\> substituindo-o pelo nome da sua instância Salesforce.

12. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Salesforce.

13. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de provisionamento e verifique a caixa de verificação abaixo.

14. Clique **em Guardar.**  

15. Na secção Mappings, selecione **Synchronize Azure Ative Directory Users to Salesforce.**

16. Na secção **De mapeamentos de atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD para Salesforce. Note que os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas de utilizador na Salesforce para operações de atualização. Selecione o botão Guardar para confirmar as alterações.

17. Para permitir o serviço de prestação de Ad Azure para a Salesforce, altere o **Estado de Provisionamento** para **On** na secção Definições

18. Clique **em Guardar.**

> [!NOTE]
> Uma vez que os utilizadores são a provisionados na aplicação Salesforce, o administrador precisa configurar as definições específicas do idioma para os mesmos. Consulte [este](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) artigo para obter mais detalhes sobre a configuração do idioma.

Isto inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos à Salesforce na secção Utilizadores e Grupos. Note que a sincronização inicial demora mais tempo a ser efetuada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos enquanto o serviço estiver em funcionamento. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o provisionamento de registos de atividades, que descrevem todas as ações realizadas pelo serviço de fornecimento na sua aplicação Salesforce.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="common-issues"></a>Problemas comuns
* Se tiver problemas que autorizem o acesso à Salesforce, certifique-se de que:
    * As credenciais usadas têm acesso administrativo à Salesforce.
    * A versão da Salesforce que está a utilizar suporta o Web Access (por exemplo, Developer, Enterprise, Sandbox e edições Ilimitadas da Salesforce.)
    * O acesso à API web está ativado para o utilizador.
* O serviço de fornecimento de Azure AD suporta o fornecimento de linguagem, localidade e timeZone para um utilizador. Estes atributos estão nos mapeamentos de atributos predefinidos, mas não têm um atributo de origem padrão. Certifique-se de que seleciona o atributo de origem predefinido e que o atributo de origem está no formato esperado pela SalesForce. Por exemplo, o localeSidKey para inglês (Estados Unidos) é en_US. Reveja as orientações [fornecidas aqui](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) para determinar o formato local apropriado DoSidKey. Os formatos LanguageLocaleKey podem ser consultados [aqui.](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5) Além de garantir que o formato está correto, poderá ter de garantir que o idioma está ativado para os seus utilizadores, conforme [descrito aqui.](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) 
* **SalesforceLicenseLimitExceed:** O utilizador não pôde ser criado na aplicação-alvo porque não existem licenças disponíveis para este utilizador. Ou obtém licenças adicionais para a aplicação-alvo, ou revê as suas atribuições de utilizador e atribui a configuração de mapeamento para garantir que os utilizadores corretos são atribuídos com os atributos corretos.
* **SalesforceDuplicateUserName:** O utilizador não pode ser provisionado porque tem um Salesforce.com 'Username' que é duplicado em outro Salesforce.com inquilino.Em Salesforce.com, os valores para o atributo 'Username' devem ser únicos em todos os Salesforce.com inquilinos.Por padrão, o nome de utilizadorPrincipalName de um utilizador no Azure Ative Directory torna-se o seu 'Username' em Salesforce.com.Tem duas opções.Uma opção é encontrar e renomear o utilizador com o duplicado 'Username' na outra Salesforce.com inquilino, se administrar também esse outro inquilino.A outra opção é remover o acesso do utilizador do Azure Ative Directory ao inquilino Salesforce.com com o qual o seu diretório está integrado. Vamos voltar a tentar esta operação na próxima tentativa de sincronização. 
* **SalesforceRequiredFieldMissing:** A Salesforce requer que certos atributos estejam presentes no utilizador para criar ou atualizar com sucesso o utilizador. Este utilizador está a perder um dos atributos necessários. Certifique-se de que atributos como e-mail e pseudónimo são preenchidos em todos os utilizadores que gostaria de ser a provisionado na Salesforce. Pode procurar utilizadores que não tenham estes atributos usando [filtros de scoping baseados em atributos.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 
* O mapeamento de atributos predefinidos para o provisionamento à Salesforce inclui a expressão SingleAppRoleAssignments para mapear aloções de adição em Azure AD ao ProfileName em Salesforce. Certifique-se de que os utilizadores não possuem múltiplas atribuições de funções de aplicação no AD AZure, uma vez que o mapeamento de atributos suporta apenas o provisionamento de uma função. 
* A Salesforce exige que as atualizações de email sejam aprovadas manualmente antes de serem alteradas. Como resultado, pode ver várias entradas nos registos de provisionamento para atualizar o e-mail do utilizador (até que a alteração de e-mail tenha sido aprovada).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o único sign-on](./salesforce-tutorial.md)