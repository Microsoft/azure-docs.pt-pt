---
title: 'Tutorial: Configure Jive para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Jive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6fbfdbe7-d66c-4305-9fea-76d6a6a92830
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602eed65745eea1fd9096508c442a27ea79bcba9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77057739"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Tutorial: Configure Jive para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos necessários para realizar em Jive e Azure AD para fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para Jive.

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes itens:

*   Um inquilino de diretório Azure Ative.
*   Um sinal único jive na subscrição ativada.
*   Uma conta de utilizador em Jive com permissões team admin.

## <a name="assigning-users-to-jive"></a>Atribuir utilizadores à Jive

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o serviço de provisionamento, tem de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à sua aplicação Jive. Uma vez decidido, pode atribuir estes utilizadores à sua aplicação Jive seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Dicas importantes para atribuir utilizadores à Jive

*   Recomenda-se que um único utilizador da AD Azure seja atribuído à Jive para testar a configuração de provisionamento. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

*   Ao atribuir um utilizador ao Jive, deve selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="enable-user-provisioning"></a>Ativar o fornecimento de utilizadores

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador da Jive que aprovisiona a API, e configurando o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas em Jive com base na atribuição de utilizador e grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar o Single Sign-On baseado em SAML para jive, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o fornecimento da conta do utilizador:

O objetivo desta secção é delinear como permitir o fornecimento de contas de utilizadores do Diretório Ativo à Jive.
Como parte deste procedimento, é-lhe exigido que forneça um sinal de segurança do utilizador que precisa solicitar a partir de Jive.com.

1. No [portal Azure,](https://portal.azure.com)navegue até ao **Azure Ative Directory > Enterprise Apps > todas as aplicações.**

1. Se já configurou o Jive para um único sinal, procure a sua instância de Jive utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **Jive** na galeria de aplicações. Selecione Jive a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância de Jive e, em seguida, selecione o separador **Provisioning.**

1. Detete o **modo de provisionamento** para **automático**. 

    ![provisionamento](./media/jive-provisioning-tutorial/provisioning.png)

1. No âmbito da secção **credenciais de administrador,** forneça as seguintes definições de configuração:
   
    a. Na caixa de texto **Jive Admin User Name,** digite um nome de conta Jive que tenha o perfil **de Administrador** do Sistema em Jive.com atribuído.
   
    b. Na caixa de texto **jive Admin Password,** digite a palavra-passe para esta conta.
   
    c. Na caixa de texto **do Jive Tenant URL,** digite o URL do inquilino Jive.
      
      > [!NOTE]
      > O URL do inquilino Jive é URL que é usado pela sua organização para iniciar sessão na Jive.  
      > Normalmente, o URL tem o seguinte formato: **www.\< organização\>.jive.com**.          

1. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Jive.

1. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro no campo de email de **notificação** e verifique a caixa de verificação abaixo.

1. Clique em **Guardar.**

1. Na secção Mapeamentos, **selecione Synchronize Azure Ative Directory Users to Jive.**

1. Na secção **DeMapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD para Jive. Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Jive para operações de atualização. Selecione o botão Guardar para elegiro qualquer alteração.

1. Para ativar o serviço de provisionamento de AD Azure para jive, altere o Estado de **Provisionamento** para **On** na secção Definições

1. Clique em **Guardar.**

Inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos à Jive na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a realizar do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos registos de atividades de provisionamento, que descrevem todas as ações realizadas pelo serviço de provisionamento na sua aplicação Jive.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar um único signo](jive-tutorial.md)
