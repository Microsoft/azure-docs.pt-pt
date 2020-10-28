---
title: 'Tutorial: Configure NetSuite OneWorld para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o NetSuite OneWorld.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 8406ee5647d02cc917a0fdb1daf2355611bb781d
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792213"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Tutorial: Configurar o NetSuite para o fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que precisa de executar no NetSuite OneWorld e AD Azure para provisões automáticas e desavisagem de contas de utilizadores de Azure AD para NetSuite.

> [!NOTE]
> Esta integração atualmente autentica-se utilizando a autenticação básica (nome de utilizador e senha). A NetSuite implementou um requisito de autenticação multi-factor que impede os clientes de utilizarem esta integração a menos que tenham uma isenção deste requisito. Estamos a trabalhar com a NetSuite para atualizar esta integração para um método de autenticação mais recente para permitir que os clientes sem isenção a utilizem novamente. Atualizaremos este documento com um ETA assim que um estiver disponível.

Ação recomendada: Por favor, aguarde até lançarmos uma atualização do comportamento de autenticação para esta integração ou contacte o suporte da NetSuite para inquirir sobre uma isenção ao requisito de autenticação de vários fatores.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que já tem os seguintes itens:

*   Um inquilino de diretório Azure Ative.
*   Uma subscrição netSuite OneWorld. Note que o fornecimento automático de utilizadores é atualmente suportado apenas com o NetSuite OneWorld.
*   Uma conta de utilizador na Netsuite com permissões de administrador.
*   A integração com a Azure AD requer uma isenção de 2FA. Entre em contato com a equipa de apoio da NetSuite para solicitar esta isenção.

## <a name="assigning-users-to-netsuite-oneworld"></a>Atribuir utilizadores ao NetSuite OneWorld

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em AZure AD são sincronizados.

Antes de configurar e ativar o serviço de fornecimento, tem de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à sua app NetSuite. Uma vez decididos, pode atribuir estes utilizadores à sua app NetSuite seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Dicas importantes para atribuir utilizadores ao NetSuite OneWorld

*   Recomenda-se que um único utilizador AD Azure seja designado para a NetSuite para testar a configuração de provisionamento. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador ao NetSuite, tem de selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="enable-user-provisioning"></a>Ativar o fornecimento de utilizadores

Esta secção guia-o através da ligação do seu AZure AD à conta de utilizador da NetSuite que fornece a API, e configura o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas no NetSuite com base na atribuição de utilizadores e grupos em Azure AD.

> [!TIP] 
> Pode também optar por ativar Sign-On única baseada em SAML para a NetSuite, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se elogiem mutuamente.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o provisionamento da conta de utilizador:

O objetivo desta secção é delinear como permitir o fornecimento de contas de utilizador do Ative Directory ao NetSuite.

1. No [portal Azure,](https://portal.azure.com)consulte a secção **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

1. Se já configurar o NetSuite para um único sinal, procure a sua instância de NetSuite utilizando o campo de pesquisa. Caso contrário, **selecione Add** e procure **netSuite** na galeria de aplicações. Selecione NetSuite a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância de NetSuite e, em seguida, selecione o **separador Provisioning.**

1. Defina o **Modo de Aprovisionamento** como **Automático** . 

    ![A screenshot mostra a página netSuite Provisioning, com o modo de provisionamento definido para Automático e outros valores que pode definir.](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Na secção **Credenciais de Administração,** forneça as seguintes definições de configuração:
   
    a. Na caixa de texto **do Nome de Utilizador Admin,** digite um nome de conta NetSuite que tenha o perfil de Administrador do **Sistema** em NetSuite.com atribuído.
   
    b. Na caixa de texto da **Palavra-passe Admin,** digite a palavra-passe para esta conta.
      
1. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua app NetSuite.

1. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de provisionamento e verifique a caixa de verificação.

1. Clique **em Guardar.**

1. Na secção Mappings, selecione **Synchronize Azure Ative Directory Users para NetSuite.**

1. Na secção **De Mapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD para NetSuite. Note que os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador no NetSuite para operações de atualização. Selecione o botão Guardar para confirmar as alterações.

1. Para permitir o serviço de prestação de Ad Azure para o NetSuite, altere o **Estado de Provisionamento** para **On** na secção Definições

1. Clique **em Guardar.**

Inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos à NetSuite na secção Utilizadores e Grupos. Note que a sincronização inicial demora mais tempo a ser efetuada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos enquanto o serviço estiver em funcionamento. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para registos de atividade de provisionamento, que descrevem todas as ações realizadas pelo serviço de fornecimento na sua app NetSuite.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o único sign-on](netsuite-tutorial.md)
