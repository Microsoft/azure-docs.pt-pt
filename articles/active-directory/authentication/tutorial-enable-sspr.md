---
title: Ativar o reposição da palavra-passe de autosserviço do Azure Ative Directory
description: Neste tutorial, aprende-se a ativar o reset da palavra-passe do Azure Ative Directory para um grupo de utilizadores e testar o processo de reset da palavra-passe.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbbf1a6aebf54c7928fe0636262055f37d7f93bd
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966547"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Tutorial: Permitir que os utilizadores desbloqueiem a sua conta ou repôs palavras-passe utilizando o reset da palavra-passe de autosserviço do Azure Ative Directory

O Azure Ative Directory (Azure AD) redefiniu a palavra-passe de autosserviço (SSPR) dá aos utilizadores a capacidade de alterar ou redefinir a sua palavra-passe, sem qualquer administrador ou envolvimento do balcão de ajuda. Se a conta de um utilizador estiver bloqueada ou se esquecerem da sua palavra-passe, podem seguir as instruções para se desbloquearem e voltarem ao trabalho. Esta capacidade reduz as chamadas de secretária de ajuda e a perda de produtividade quando um utilizador não pode iniciar sômata ao seu dispositivo ou a uma aplicação.

> [!IMPORTANT]
> Este tutorial mostra a um administrador como ativar o reset da palavra-passe de autosserviço. Se é um utilizador final já registado para redefinição da palavra-passe de autosserviço e precisa de voltar à sua conta, vá a https://aka.ms/sspr .
>
> Se a sua equipa de TI não tiver ativado a capacidade de redefinir a sua própria palavra-passe, contacte o seu helpdesk para obter assistência adicional.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar o reset da palavra-passe de autosserviço para um grupo de utilizadores AD Azure
> * Configurar métodos de autenticação e opções de registo
> * Teste o processo SSPR como utilizador

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Um inquilino da Azure AD em funcionamento com pelo menos uma licença de AD AD Azure ou licença de julgamento habilitada. No nível Gratuito, o SSPR só funciona para utilizadores em nuvem em Azure AD.
    * Para tutoriais posteriores nesta série, é necessária uma licença Azure AD Premium P1 ou uma licença de teste para a gravação de password no local.
    * Se necessário, [crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Uma conta com privilégios *de Administrador Global.*
* Um utilizador não administrador com uma palavra-passe que conhece, como *testuser.* Você testa a experiência SSPR do utilizador final usando esta conta neste tutorial.
    * Se precisar de criar um utilizador, consulte [Quickstart: Adicione novos utilizadores ao Azure Ative Directory](../fundamentals/add-users-azure-active-directory.md).
* Um grupo do qual o utilizador não administrador é membro, como o *SSPR-Test-Group*. Você ativa A SSPR para este grupo neste tutorial.
    * Se precisar de criar um grupo, veja como [criar um grupo e adicione membros no Azure Ative Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavras-passe self-service

O Azure AD permite ativar SSPR para *Nenhum,* *Selecionado*ou *Todos os* utilizadores. Esta capacidade granular permite-lhe escolher um subconjunto de utilizadores para testar o processo de registo EsPR e o fluxo de trabalho. Quando estiver confortável com o processo e puder comunicar os requisitos com um conjunto mais alargado de utilizadores, pode selecionar um grupo de utilizadores para ativar o SSPR. Ou, você pode ativar SSPR para todos no inquilino AZure AD.

> [!NOTE]
>
> Apenas um grupo AZure AD pode atualmente ser ativado para SSPR utilizando o portal Azure. Como parte de uma implantação mais ampla da SSPR, os grupos aninhados são apoiados. Certifique-se de que os utilizadores do grupo que escolher têm as licenças apropriadas atribuídas. Não há atualmente nenhum processo de validação destes requisitos de licenciamento.

Neste tutorial, configuure a SSPR para um conjunto de utilizadores num grupo de teste. No exemplo seguinte, é utilizado o grupo *SSPR-Test-Group.* Forneça o seu próprio grupo AZure AD conforme necessário:

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta com permissões *de administrador global.*
1. Procure e selecione **Azure Ative Directory**e, em seguida, escolha o reset de **Palavra-passe** do menu do lado esquerdo.
1. A partir da página **Propriedades,** sob a opção *Redefiniu a palavra-passe de serviço de auto-ar,* escolha **grupo Select**
1. Navegue e selecione o seu grupo AD Azure, como *o SSPR-Test-Group,* em seguida, escolha *Select*.

    [![Selecione um grupo no portal Azure para permitir o reset ](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png) da palavra-passe de autosserviço](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Para ativar o SSPR para os utilizadores selecionados, selecione **Guardar**.

## <a name="select-authentication-methods-and-registration-options"></a>Selecione métodos de autenticação e opções de registo

Quando os utilizadores precisam de desbloquear a sua conta ou redefinir a sua palavra-passe, são solicitados para um método adicional de confirmação. Este fator de autenticação adicional garante que apenas os eventos SSPR aprovados sejam concluídos. Pode escolher quais os métodos de autenticação a permitir, com base na informação de registo que o utilizador fornece.

1. Na página de **métodos** de autenticação a partir do menu do lado esquerdo, desajuste o **Número de métodos necessários para reset** para *1*.

    Para melhorar a segurança, pode aumentar o número de métodos de autenticação necessários para a SSPR.

1. Escolha os **Métodos disponíveis para os utilizadores** que a sua organização quer permitir. Para este tutorial, verifique as caixas para ativar os seguintes métodos:

    * *Notificação de aplicativos móveis*
    * *Código da aplicação móvel*
    * *E-mail*
    * *Número de telemóvel*

    Métodos de autenticação adicionais, como *telefone do Office* ou *questões de Segurança,* podem ser ativados conforme necessário para se adaptarem às suas necessidades comerciais.

1. Para aplicar os métodos de autenticação, **selecione Guardar**.

Antes de os utilizadores poderem desbloquear a sua conta ou redefinir uma palavra-passe, devem registar as suas informações de contacto. Estas informações de contacto são utilizadas para os diferentes métodos de autenticação configurados nos passos anteriores.

Um administrador pode fornecer manualmente estas informações de contacto, ou os utilizadores podem ir a um portal de registo para fornecer a informação por si mesmos. Neste tutorial, configuure os utilizadores a serem solicitados para o registo quando iniciarem a sua próxima sação.

1. Na página **de Registo** do menu do lado esquerdo, selecione *Sim* para exigir que **os utilizadores se registem ao iniciar sessão .**
1. É importante que os contactos se aussam. Se as informações de contacto estiverem desatualizadas quando um evento SSPR for iniciado, o utilizador poderá não conseguir desbloquear a sua conta ou redefinir a sua palavra-passe.

    Defina o **Número de dias antes de ser pedido aos utilizadores que voltem a confirmar as informações de autenticação** como *180*.
1. Para aplicar as definições de registo, **selecione Guardar**.

## <a name="configure-notifications-and-customizations"></a>Configure notificações e personalizações

Para manter os utilizadores informados sobre a atividade da conta, pode configurar notificações de e-mail a serem enviadas quando um evento SSPR acontecer. Estas notificações podem abranger contas de utilizador regulares e contas de administração. Para as contas de administração, esta notificação fornece uma camada adicional de consciência quando uma palavra-passe de conta de administrador privilegiado é reposta usando SSPR. Todos os administradores globais seriam notificados quando a SSPR for utilizada numa conta administrada.

1. Na página **de Notificações** do menu do lado esquerdo, configuure as seguintes opções:

   * Defina a opção **Notificar os utilizadores sobre reposições de palavra-passe** como *Sim*.
   * Defina **Notificar todos os administradores quando outros administradores repõem as palavras-passe deles** como *Sim*.

1. Para aplicar as preferências de notificação, **selecione Guardar**.

Se os utilizadores precisarem de ajuda adicional com o processo SSPR, pode personalizar o link para "Contactar o seu administrador". Este link é utilizado no processo de registo SSPR e quando um utilizador desbloqueia a sua conta ou repõe a sua palavra-passe. Para garantir que os seus utilizadores obtenham o suporte necessário, é altamente recomendado fornecer um e-mail ou URL de ajuda personalizado.

1. Na página de **personalização** do menu do lado esquerdo, desloque *o link de ajuda* ao **Sim**.
1. No campo **de e-mail ou URL de ajuda personalizado,** forneça um endereço de e-mail ou URL de página web onde os seus utilizadores possam obter ajuda adicional da sua organização, tais como *`https://support.contoso.com/`*
1. Para aplicar o link personalizado, **selecione Guardar**.

## <a name="test-self-service-password-reset"></a>Testar a reposição personalizada de palavra-passe

Com sSPR ativado e configurado, teste o processo SSPR com um utilizador que faz parte do grupo que selecionou na secção anterior, como o *Test-SSPR-Group*. No exemplo seguinte, utiliza-se a conta *testuser.* Forneça a sua própria conta de utilizador que faz parte do grupo que ativou para a SSPR na primeira secção deste tutorial.

> [!NOTE]
> Quando testar o reset da palavra-passe de autosserviço, utilize uma conta não administradora. Por predefinição, os administradores estão ativados para reiniciar a palavra-passe de autosserviço e são obrigados a utilizar dois métodos de autenticação para redefinir a sua palavra-passe. Para obter mais informações, consulte [as diferenças de política de reposição do administrador](concept-sspr-policy.md#administrator-reset-policy-differences).

1. Para ver o processo de registo manual, abra uma nova janela do navegador no modo InPrivate ou incógnito e navegue para [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Os utilizadores devem ser direcionados para este portal de registo quando iniciarem a sua próxima s inscrição.
1. Inscreva-se com um utilizador de teste não administrador, como *testuser,* e registe os seus métodos de autenticação informações de contacto.
1. Uma vez concluído, selecione o botão marcado **Parece bom** e feche a janela do navegador.
1. Abra uma nova janela do navegador no modo InPrivate ou incógnito e navegue para [https://aka.ms/sspr](https://aka.ms/sspr) .
1. Introduza as informações da conta dos utilizadores de teste não administrador, tais como *testuser,* os caracteres do CAPTCHA e, em seguida, selecione **Next**.

    ![Introduza informações da conta de utilizador para redefinir a palavra-passe](media/tutorial-enable-sspr/password-reset-page.png)

1. Siga os passos de verificação para redefinir a sua palavra-passe. Quando estiver concluído, deverá receber uma notificação por e-mail de que a sua palavra-passe foi reposta.

## <a name="clean-up-resources"></a>Limpar recursos

Num tutorial seguinte nesta série, configurar a gravação da palavra-passe. Esta funcionalidade escreve alterações de palavra-passe de Azure AD SSPR de volta para um ambiente AD no local. Se quiser continuar com esta série tutorial para configurar a gravação de passwords, não desative agora a SSPR.

Se já não pretender utilizar a funcionalidade SSPR que configuraste como parte deste tutorial, desafie o estado SSPR a **Nenhum** utilizando os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure e selecione **Azure Ative Directory**e, em seguida, escolha o reset de **Palavra-passe** do menu do lado esquerdo.
1. A partir da página **Propriedades,** sob a opção *Redefiniu a palavra-passe de serviço de auto-ar,* escolha **Nenhum**.
1. Para aplicar a alteração SSPR, **selecione Guardar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ativou o autosserviço do Azure AD para um grupo selecionado de utilizadores. Aprendeu a:

> [!div class="checklist"]
> * Ativar o reset da palavra-passe de autosserviço para um grupo de utilizadores AD Azure
> * Configurar métodos de autenticação e opções de registo
> * Teste o processo SSPR como utilizador

> [!div class="nextstepaction"]
> [Ativar a Multi-Factor Authentication do Azure](./tutorial-enable-azure-mfa.md)
