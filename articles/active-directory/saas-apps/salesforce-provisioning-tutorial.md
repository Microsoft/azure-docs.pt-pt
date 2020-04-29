---
title: 'Tutorial: Configure Salesforce para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Salesforce.
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
ms.openlocfilehash: 5b8038896a11b65e835ce71f5fc34e85723cc91a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77060526"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Tutorial: Configure Salesforce para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar os passos necessários para executar na Salesforce e na Azure AD para fornecer e desfornecer automaticamente contas de utilizadores da Azure AD à Salesforce.

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino de diretório Azure Ative
* Um inquilino Salesforce.com

> [!IMPORTANT]
> Se estiver a utilizar uma conta de teste Salesforce.com, não poderá configurar o fornecimento automatizado de utilizadores. As contas de experimentação não têm o acesso a API necessário até serem adquiridas. Pode contornar esta limitação utilizando uma conta de [desenvolvedor](https://developer.salesforce.com/signup) gratuito para completar este tutorial.

Se estiver a utilizar um ambiente Salesforce Sandbox, consulte o tutorial de [integração salesforce Sandbox](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Atribuir utilizadores à Salesforce

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o serviço de provisionamento, tem de decidir quais os utilizadores ou grupos da AD Azure que precisam de acesso à sua aplicação Salesforce. Depois de ter feito esta decisão, pode atribuir estes utilizadores à sua aplicação Salesforce seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Dicas importantes para atribuir utilizadores à Salesforce

* Recomenda-se que um único utilizador da AD Azure seja atribuído à Salesforce para testar a configuração de provisionamento. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador à Salesforce, deve selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento

    > [!NOTE]
    > Esta aplicação importa perfis da Salesforce como parte do processo de provisionamento, que o cliente pode querer selecionar ao atribuir utilizadores em Azure AD. Por favor, note que os perfis que são importados da Salesforce aparecem como Papéis em Azure AD.

## <a name="enable-automated-user-provisioning"></a>Ativar o fornecimento automatizado de utilizadores

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador da Salesforce que [aprovisiona a API - v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm), e configurando o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas na Salesforce com base na atribuição de utilizadores e grupos em Azure AD.

> [!Tip]
> Também pode optar por ativar o Single Sign-On baseado em SAML para a Salesforce, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o fornecimento automático de conta de utilizador

O objetivo desta secção é delinear como permitir o fornecimento de contas de utilizadores do Diretório Ativo à Salesforce.

1. No [portal Azure,](https://portal.azure.com)navegue até ao **Azure Ative Directory > Enterprise Apps > todas as aplicações.**

2. Se já configurou a Salesforce para um único sinal, procure a sua instância de Salesforce utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **Salesforce** na galeria de aplicações. Selecione Salesforce a partir dos resultados da pesquisa e adicione-a à sua lista de aplicações.

3. Selecione a sua instância de Salesforce e, em seguida, selecione o separador **Provisioning.**

4. Detete o **modo de provisionamento** para **automático**.

    ![provisionamento](./media/salesforce-provisioning-tutorial/provisioning.png)

5. No âmbito da secção **credenciais de administrador,** forneça as seguintes definições de configuração:

    a. Na caixa de texto **'Username' da Admin,** digite um nome de conta Salesforce que tenha o perfil **de Administrador** do Sistema em Salesforce.com atribuído.

    b. Na caixa de texto **'Password' Admin,** digite a palavra-passe para esta conta.

6. Para obter o seu token de segurança Salesforce, abra um novo separador e assine na mesma conta de administração da Salesforce. No canto superior direito da página, clique no seu nome e, em seguida, clique em **Definições**.

    ![Ativar o fornecimento automático de utilizadores](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Ativar o fornecimento automático de utilizadores")

7. No painel de navegação à esquerda, clique em **Minhas Informações Pessoais** para expandir a secção relacionada e, em seguida, clique em **Redefinir o meu token**de segurança .
  
    ![Ativar o fornecimento automático de utilizadores](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Ativar o fornecimento automático de utilizadores")

8. Na página **Reset Security Token,** clique em redefinir o botão **Reset Security Token.**

    ![Ativar o fornecimento automático de utilizadores](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Ativar o fornecimento automático de utilizadores")

9. Verifique a caixa de entrada de e-mail associada a esta conta de administração. Procure um e-mail de Salesforce.com que contenha o novo símbolo de segurança.

10. Copie o símbolo, vá para a sua janela azure d.D., e cole-o no campo **Secreto Token.**

11. O URL do **Arrendatário** deve ser introduzido se a instância da Salesforce estiver na Nuvem do Governo salesforce. Caso contrário, é opcional. Introduza o URL do inquilino\<utilizando\>o formato \<"https://\> sua instância .my.salesforce.com", substituindo a sua instância pelo nome da sua instância Salesforce.

12. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Salesforce.

13. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de fornecimento e verifique a caixa de verificação abaixo.

14. Clique em **Guardar.**  

15. Na secção Mapeamentos, **selecione Synchronize Azure Ative Directory Users to Salesforce.**

16. Na secção **DeMapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD para Salesforce. Note que os atributos selecionados como propriedades **correspondentes** são usados para corresponder às contas de utilizador no Salesforce para operações de atualização. Selecione o botão Guardar para elegiro qualquer alteração.

17. Para ativar o serviço de provisionamento de AD Azure para a Salesforce, altere o Estado de **Provisionamento** para **On** na secção Definições

18. Clique em **Guardar.**

> [!NOTE]
> Uma vez que os utilizadores são aprovisionados na aplicação Salesforce, o administrador precisa de configurar as definições específicas do idioma para os mesmos. Consulte [este](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) artigo para mais detalhes sobre a configuração do idioma.

Isto inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos à Salesforce na secção Utilizadores e Grupos. Note que a sincronização inicial demora mais tempo a realizar do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos registos de atividades de provisionamento, que descrevem todas as ações realizadas pelo serviço de provisionamento na sua aplicação Salesforce.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="common-issues"></a>Problemas comuns
* Se tiver problemas que autorizem o acesso à Salesforce, certifique-se do seguinte:
    * As credenciais utilizadas têm acesso administrativo à Salesforce.
    * A versão da Salesforce que está a usar suporta o Web Access (por exemplo, Developer, Enterprise, Sandbox e Edições Ilimitadas da Salesforce.)
    * O acesso à Web API está ativado para o utilizador.
* O serviço de provisionamento de AD Azure suporta o fornecimento de linguagem, localidade e timeZone para um utilizador. Estes atributos estão nos mapeamentos de atributopadrão, mas não têm um atributo de origem padrão. Certifique-se de que seleciona o atributo de origem predefinido e que o atributo de origem está no formato esperado pelo SalesForce. Por exemplo, o localsidKey para inglês (Estados Unidos) é en_US. Reveja as orientações [fornecidas aqui](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) para determinar o formato localsidKey adequado. Os formatos idiomaLocaleKey podem ser encontrados [aqui](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5). Além de garantir que o formato está correto, poderá ser necessário garantir que o idioma está ativado para os seus utilizadores, tal como descrito [aqui](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5). 
* **SalesforceLicenseLimitExceeded:** O utilizador não pôde ser criado na aplicação-alvo porque não existem licenças disponíveis para este utilizador. Ou obtém licenças adicionais para a aplicação alvo, ou revê as atribuições do utilizador e atribui a configuração de mapeamento para garantir que os utilizadores corretos são atribuídos com os atributos corretos.
* **SalesforceDuplicateUserName:** O utilizador não pode ser provisionado porque tem um Salesforce.com 'Username' que é duplicado em outro Salesforce.com inquilino.Em Salesforce.com, os valores para o atributo 'Username' devem ser únicos em todos os Salesforce.com inquilinos.Por padrão, o utilizador PrincipalName no Diretório Ativo Do Azure torna-se o seu 'Username' em Salesforce.com.Tem duas opções.Uma opção é encontrar e renomear o utilizador com o duplicado 'Username' no outro Salesforce.com inquilino, se você administrar o outro inquilino também.A outra opção é remover o acesso do utilizador do Diretório Ativo Azure ao Salesforce.com inquilino com o qual o seu diretório está integrado. Vamos voltar a tentar esta operação na próxima tentativa de sincronização. 
* **SalesforceRequiredFieldMissing:** A Salesforce requer que certos atributos estejam presentes no utilizador para criar ou atualizar com sucesso o utilizador. Falta um dos atributos necessários a este utilizador. Certifique-se de que atributos como e-mail e pseudónimo são povoados em todos os utilizadores que gostaria de ser aprovisionado na Salesforce. Pode examinar os utilizadores que não possuem estes atributos utilizando filtros de [deteção baseados em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 
* O mapeamento de atributo padrão para o fornecimento à Salesforce inclui a expressão SingleAppRoleAssignments para mapear appRoleAssignments em Azure AD para ProfileName em Salesforce. Certifique-se de que os utilizadores não possuem múltiplas atribuições de aplicações em Azure AD, uma vez que o mapeamento do atributo apenas suporta o fornecimento de uma função. 
* A Salesforce exige que as atualizações de e-mail sejam aprovadas manualmente antes de serem alteradas. Como resultado, poderá ver várias entradas nos registos de fornecimento para atualizar o e-mail do utilizador (até que a alteração de e-mail seja aprovada).


## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar um único signo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
