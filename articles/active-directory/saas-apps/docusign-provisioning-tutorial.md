---
title: 'Tutorial: Configure DocuSign para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88b65c8e8962ad8420ded47da1a343672123c589
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058183"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Tutorial: Configure DocuSign para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos necessários para executar no DocuSign e Azure AD para fornecer e desfornecer automaticamente contas de utilizador de Azure AD para DocuSign.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um inquilino de diretório Azure Ative.
*   Uma subscrição ativada por um único sinal do DocuSign.
*   Uma conta de utilizador no DocuSign com permissões de Team Admin.

## <a name="assigning-users-to-docusign"></a>Atribuir utilizadores ao DocuSign

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o serviço de provisionamento, tem de decidir quais os utilizadores e/ou grupos em AD Azure que representam os utilizadores que precisam de acesso à sua aplicação DocuSign. Uma vez decidido, pode atribuir estes utilizadores à sua aplicação DocuSign seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Dicas importantes para atribuir utilizadores ao DocuSign

*   Recomenda-se que um único utilizador da AD Azure seja atribuído ao DocuSign para testar a configuração de provisionamento. Os utilizadores adicionais podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador ao DocuSign, deve selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

> [!NOTE]
> A Azure AD não suporta o fornecimento de grupo com a aplicação Docusign, apenas os utilizadores podem ser aprovisionados.

## <a name="enable-user-provisioning"></a>Ativar o fornecimento de utilizadores

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador do DocuSign que aprovisiona a API, e configurando o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas no DocuSign com base na atribuição de utilizador e grupo em AD Azure.

> [!Tip]
> Também pode optar por ativar o Single Sign-On baseado em SAML para docuSign, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o fornecimento da conta do utilizador:

O objetivo desta secção é delinear como permitir o fornecimento de contas de utilizadores do Diretório Ativo ao DocuSign.

1. No [portal Azure,](https://portal.azure.com)navegue até ao **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

1. Se já configurou o DocuSign para um único sinal, procure a sua instância de DocuSign utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **DocuSign** na galeria de aplicações. Selecione DocuSign a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância de DocuSign e, em seguida, selecione o separador **Provisioning.**

1. Detete o **modo de provisionamento** para **automático**. 

    ![provisionamento](./media/docusign-provisioning-tutorial/provisioning.png)

1. No âmbito da secção **credenciais de administrador,** forneça as seguintes definições de configuração:
   
    a. Na caixa de texto 'Nome de **utilizador' do Administrador,** digite um nome de conta DocuSign que tenha o perfil **de Administrador** do Sistema em DocuSign.com atribuído.
   
    b. Na caixa de texto **'Password' Admin,** digite a palavra-passe para esta conta.

1. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação DocuSign.

1. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de fornecimento e verifique a caixa de verificação.

1. Clique em **Guardar.**

1. Na secção Mapeamentos, **selecione Synchronize Azure Ative Directory Users to DocuSign.**

1. Na secção **DeMapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD para DocuSign. Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no DocuSign para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

1. Para ativar o serviço de provisionamento de AD Azure para docuSign, altere o Estado de **Provisionamento** para **On** na secção Definições

1. Clique em **Guardar.**

Inicia a sincronização inicial de quaisquer utilizadores atribuídos ao DocuSign na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos registos de atividades de provisionamento, que descrevem todas as ações realizadas pelo serviço de provisionamento na sua aplicação DocuSign.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar um único signo](docusign-tutorial.md)
