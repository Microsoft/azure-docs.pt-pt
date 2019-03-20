---
title: 'Tutorial: Configurar o G Suite para o aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como aprovisionar e desaprovisionar contas de utilizador do Azure AD ao G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b78cb45d83cd9bc9bc973ec7a09cb75a8b111744
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224153"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutorial: Configurar o G Suite para o aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe como aprovisionar e desaprovisionar contas de utilizador do Azure Active Directory (Azure AD) ao G Suite.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com G Suite, terá dos seguintes itens:

- Uma subscrição do Azure
- Um G Suite logon único habilitado subscrição
- Uma subscrição do Google Apps ou a subscrição de Google Cloud Platform.

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-g-suite"></a>Atribuir utilizadores a G Suite

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o serviço de aprovisionamento, terá de decidir quais os utilizadores ou grupos no Azure AD precisam de acesso à sua aplicação. Depois que fizer essa decisão, pode atribuir estes utilizadores à sua aplicação ao seguir as instruções em [atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> Recomendamos que um único utilizador do Azure AD ser atribuído ao G Suite para testar a configuração de aprovisionamento. Pode atribuir utilizadores e grupos adicionais mais tarde.
> 
> Quando atribui um utilizador ao G Suite, selecione o **usuário** ou **grupo** função na caixa de diálogo de atribuição. O **acesso predefinido** função não funciona para o aprovisionamento.

## <a name="enable-automated-user-provisioning"></a>Ativar o aprovisionamento automatizado do utilizador

Esta secção orienta-o ao longo do processo de ligar o Azure AD para a API do G Suite de aprovisionamento da conta de utilizador. Ele também ajuda a configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no G Suite com base no utilizador e a atribuição de grupo no Azure AD.

>[!TIP]
>Também pode optar por ativar baseado em SAML início de sessão único para conjuntos de G, seguindo as instruções no [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o aprovisionamento de contas de utilizadores automático

> [!NOTE]
> Outra opção viável para automatizar o aprovisionamento de utilizadores para o G Suite é usar [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en). GADS Aprovisiona as identidades do Active Directory no local para o G Suite. Por outro lado, a solução neste tutorial Aprovisiona os utilizadores do Active Directory do Azure (nuvem) e grupos com capacidade de correio eletrónico ao G Suite. 

1. Inicie sessão para o [consola de administração de aplicações do Google](https://admin.google.com/) com a sua conta de administrador e, em seguida, selecione **segurança**. Se não vir a ligação, poderá estar oculta sob o **More Controls** menu na parte inferior do ecrã.
   
    ![Selecione a segurança.][10]

1. Sobre o **Security** página, selecione **referência da API**.
   
    ![Selecione a referência da API.][15]

1. Selecione **acesso à API ativar**.
   
    ![Selecione a referência da API.][16]

   > [!IMPORTANT]
   > Para cada utilizador que pretende aprovisionar ao G Suite, nome de utilizadores no Azure Active Directory *tem* ser vinculado a um domínio personalizado. Por exemplo, aquela aparência, como os nomes de utilizador bob@contoso.onmicrosoft.com não serão aceites pela G Suite. Por outro lado, bob@contoso.com é aceite. Pode alterar o domínio de um utilizador existente ao editar as respetivas propriedades no Azure AD. Incluímos instruções sobre como definir um domínio personalizado para o Azure Active Directory e o G Suite nos passos seguintes.
      
1. Se ainda não adicionou um nome de domínio personalizado para o Azure Active Directory, em seguida, siga os passos seguintes:
  
    a. Na [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **do Active Directory**. Na lista de diretório, selecione o diretório. 

    b. Selecione **nome de domínio** no painel de navegação esquerdo e, em seguida, selecione **Add**.
     
     ![Domain](./media/google-apps-provisioning-tutorial/domain_1.png)

     ![Adicionar domínio](./media/google-apps-provisioning-tutorial/domain_2.png)

    c. Escreva o seu nome de domínio para o **nome de domínio** campo. Este nome de domínio deve ser o mesmo nome de domínio que pretende utilizar para o G Suite. Em seguida, selecione o **Adicionar domínio** botão.
     
     ![Nome de domínio](./media/google-apps-provisioning-tutorial/domain_3.png)

    d. Selecione **seguinte** para ir para a página de verificação. Para verificar que é proprietário deste domínio, edite os registos DNS do domínio, de acordo com os valores que são fornecidos nesta página. Pode optar por verificar com uma **registros MX** ou **registos TXT**, consoante o que selecionou para o **tipo de registo** opção. 
    
    Para obter instruções mais abrangentes sobre como verificar nomes de domínio com o Azure AD, consulte [adicionar seu próprio nome de domínio para o Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
     ![Domain](./media/google-apps-provisioning-tutorial/domain_4.png)

    e. Repita os passos anteriores para todos os domínios que pretende adicionar ao seu diretório.

   > [!NOTE]
   >  Para o aprovisionamento de utilizador, o domínio personalizado deve corresponder ao nome de domínio da origem do Azure AD. Se não forem iguais, poderá conseguir resolver o problema com a implementação de personalização de mapeamento do atributo.


1. Agora que tiver verificado todos os domínios com o Azure AD, tem de verificá-los novamente com o Google Apps. Para cada domínio que já não estiver registado com o Google, siga os passos seguintes:
   
    a. Na [consola de administração de aplicações do Google](https://admin.google.com/), selecione **domínios**.
     
     ![Selecionar domínios][20]

    b. Selecione **adicionar um domínio ou um alias de domínio**.
     
     ![Adicionar um novo domínio][21]

    c. Selecione **adicionar outro domínio**e, em seguida, escreva o nome do domínio que pretende adicionar.
     
     ![Escreva o nome de domínio][22]

    d. Selecione **continuar e verificar a propriedade do domínio**. Em seguida, siga os passos para validar que é o nome de domínio de seu. Para obter instruções abrangentes sobre como verificar o seu domínio com o Google, consulte [Verifique a propriedade de site com o Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Repita os passos anteriores para quaisquer domínios adicionais que pretende adicionar ao Google Apps.
     
     > [!WARNING]
     > Se alterar o domínio principal para o seu inquilino do G Suite, e se já tiver configurado o início de sessão único com o Azure AD, em seguida, terá de repetir o passo #3 no passo 2: Ative o início de sessão único.
       
1. Na [consola de administração de aplicações do Google](https://admin.google.com/), selecione **funções de administrador**.
   
     ![Selecione as aplicações do Google][26]

1. Determine que conta de administrador que pretende utilizar para gerir o aprovisionamento de utilizadores. Para o **função de administrador** dessa conta, editar o **privilégios** para essa função. Lembre-se de que ativar todas as **privilégios de administrador do API** para que esta conta pode ser utilizada para o aprovisionamento.
   
     ![Selecione as aplicações do Google][27]
   
    > [!NOTE]
    > Se estiver a configurar um ambiente de produção, a prática recomendada é criar uma conta de administrador no G Suite especificamente para este passo. Estas contas tem de ter uma função de administrador associada a eles e que tenha os privilégios necessários de API.
     
1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory** > **aplicações empresariais** > **todas as aplicações** secção.

1. Se já tiver configurado o G Suite para início de sessão único, procure na sua instância do G Suite, utilizando o campo de pesquisa. Caso contrário, selecione **Add**e, em seguida, procure **G Suite** ou **Google Apps** na Galeria de aplicações. Selecione a sua aplicação resultados da pesquisa e, em seguida, adicioná-lo à sua lista de aplicações.

1. Selecione a sua instância do G Suite e, em seguida, selecione o **aprovisionamento** separador.

1. Definir o **modo de aprovisionamento** ao **automática**. 

       ![Provisioning](./media/google-apps-provisioning-tutorial/provisioning.png)

1. Sob o **credenciais de administrador** secção, selecione **autorizar**. Abre uma caixa de diálogo de autorização do Google numa nova janela do browser.

1. Confirme que pretende conceder permissão ao Azure Active Directory para fazer alterações ao seu inquilino do G Suite. Selecione **Aceitar**.
    
       ![Confirm permissions.][28]

1. No portal do Azure, selecione **Testar ligação** para garantir que as do Azure AD pode ligar-se à sua aplicação. Se a ligação falhar, certifique-se de que a sua conta G Suite tem permissões de administrador da equipa. Em seguida, tente o **autorizar** passo novamente.

1. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **notificação por E-Mail** campo. Em seguida, selecione a caixa de verificação.

1. Selecione **guardar.**

1. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory ao Google Apps**.

1. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD ao G Suite. Os atributos que são **correspondência** propriedades são usadas de acordo com as contas de utilizador no G Suite para operações de atualização. Selecione **guardar** para consolidar as alterações.

1. Para ativar o Azure AD para o G Suite, o serviço de aprovisionamento, altere o **estado de aprovisionamento** ao **no** na **definições**.

1. Selecione **Guardar**.

Este processo inicia a sincronização inicial de quaisquer utilizadores ou grupos que são atribuídos ao G Suite na secção utilizadores e grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos enquanto o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para os registos de atividades de aprovisionamento. Estes registos descrevem todas as ações que são executadas pelo serviço de aprovisionamento na sua aplicação.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configure single sign-on](google-apps-tutorial.md) (Configurar o início de sessão único)



<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
