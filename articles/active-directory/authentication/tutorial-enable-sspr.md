---
title: Ativar o reposição da palavra-passe de autosserviço do Azure Ative Directory
description: Neste tutorial, aprende-se a ativar o reset da palavra-passe do Azure Ative Directory para um grupo de utilizadores e testar o processo de reset da palavra-passe.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 04/21/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c18dd231a708030e3a454ab8708e3f0f11dbecf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861828"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Tutorial: Permitir que os utilizadores desbloqueiem a sua conta ou repôs palavras-passe utilizando o reset da palavra-passe de autosserviço do Azure Ative Directory

O Azure Ative Directory (Azure AD) redefiniu a palavra-passe de autosserviço (SSPR) dá aos utilizadores a capacidade de alterar ou redefinir a sua palavra-passe, sem qualquer administrador ou envolvimento do balcão de ajuda. Se o Azure AD bloquear a conta de um utilizador ou se esquecerem da sua palavra-passe, podem seguir as instruções para se desbloquearem e voltarem ao trabalho. Esta capacidade reduz as chamadas de secretária de ajuda e a perda de produtividade quando um utilizador não pode iniciar sômata ao seu dispositivo ou a uma aplicação. Recomendamos este vídeo sobre [Como ativar e configurar a SSPR em Azure AD](https://www.youtube.com/watch?v=rA8TvhNcCvQ). Também temos um vídeo para administradores de TI na [resolução das seis mensagens de erro mais comuns do utilizador final com SSPR](https://www.youtube.com/watch?v=9RPrNVLzT8I).

> [!IMPORTANT]
> Este tutorial mostra a um administrador como ativar o reset da palavra-passe de autosserviço. Se é um utilizador final já registado para redefinição da palavra-passe de autosserviço e precisa de voltar à sua conta, aceda à página de reset da [palavra-passe](https://passwordreset.microsoftonline.com/) do Microsoft Online.
>
> Se a sua equipa de TI não tiver ativado a capacidade de redefinir a sua própria palavra-passe, contacte o seu helpdesk para obter assistência adicional.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar o reset da palavra-passe de autosserviço para um grupo de utilizadores AD Azure
> * Configurar métodos de autenticação e opções de registo
> * Teste o processo SSPR como utilizador

## <a name="prerequisites"></a>Pré-requisitos

Para terminar este tutorial, precisa dos seguintes recursos e privilégios:

* Um inquilino da Azure AD em funcionamento com pelo menos uma licença de AD Azure gratuita ou com licença de julgamento habilitada. No nível Gratuito, o SSPR só funciona para utilizadores em nuvem em Azure AD. A alteração da palavra-passe é suportada no nível Livre, mas o reset da palavra-passe não é. 
    * Para tutoriais posteriores nesta série, você precisará de um Azure AD Premium P1 ou uma licença de teste para a gravação de senha no local.
    * Se necessário, [crie gratuitamente uma conta Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma conta com privilégios *de Administrador Global.*
* Um utilizador não administrador com uma palavra-passe que conhece, como *testuser.* Você vai testar a experiência SSPR do utilizador final usando esta conta neste tutorial.
    * Se precisar de criar um utilizador, consulte [Quickstart: Adicione novos utilizadores ao Azure Ative Directory](../fundamentals/add-users-azure-active-directory.md).
* Um grupo do qual o utilizador não administrador é membro, gosta do *SSPR-Test-Group*. Você vai ativar SSPR para este grupo neste tutorial.
    * Se precisar de criar um grupo, consulte [criar um grupo básico e adicionar membros usando o Azure Ative Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavras-passe self-service

O Azure AD permite ativar SSPR para *Nenhum,* *Selecionado* ou *Todos os* utilizadores. Esta capacidade granular permite-lhe escolher um subconjunto de utilizadores para testar o processo de registo EsPR e o fluxo de trabalho. Quando estiver confortável com o processo e o momento certo para comunicar os requisitos com um conjunto mais alargado de utilizadores, pode selecionar um grupo de utilizadores para ativar o SSPR. Ou, você pode ativar SSPR para todos no inquilino AZure AD.

> [!NOTE]
> Atualmente, só é possível ativar um grupo AZure AD para SSPR utilizando o portal Azure. Como parte de uma implantação mais ampla da SSPR, a Azure AD apoia grupos aninhados. Certifique-se de que os utilizadores do grupo que escolher têm as licenças apropriadas atribuídas. Não há atualmente nenhum processo de validação destes requisitos de licenciamento.

Neste tutorial, crie sSPR para um conjunto de utilizadores em um grupo de teste. Utilize o *Grupo SSPR-Test* e forneça o seu próprio grupo AD Azure, se necessário:

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta com permissões *de administrador global.*
1. Procure e selecione **Azure Ative Directory** e, em seguida, selecione **a palavra-passe reiniciada** a partir do menu do lado esquerdo.
1. A partir da página **Propriedades,** sob a opção *Redefiniu a palavra-passe de serviço de autosserviço ativada*, selecione **Selecione grupo**
1. Navegue e selecione o seu grupo AD Azure, como *o SSPR-Test-Group,* em seguida, escolha *Select*.

    [![Selecione um grupo no portal Azure para permitir o reset da palavra-passe de autosserviço](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png)](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Para ativar o SSPR para os utilizadores selecionados, selecione **Guardar**.

## <a name="select-authentication-methods-and-registration-options"></a>Selecione métodos de autenticação e opções de registo

Quando os utilizadores precisam de desbloquear a sua conta ou redefinir a sua palavra-passe, são solicitados para outro método de confirmação. Este fator de autenticação extra garante que a Azure AD terminou apenas eventos SSPR aprovados. Pode escolher quais os métodos de autenticação a permitir, com base na informação de registo que o utilizador fornece.

1. A partir do menu no lado esquerdo da página métodos de **autenticação,** desajuste o **Número de métodos necessários para reset** para *1*.

    Para melhorar a segurança, pode aumentar o número de métodos de autenticação necessários para a SSPR.

1. Escolha os **Métodos disponíveis para os utilizadores** que a sua organização quer permitir. Para este tutorial, verifique as caixas para ativar os seguintes métodos:

    * *Notificação de aplicativos móveis*
    * *Código da aplicação móvel*
    * *E-mail*
    * *Número de telemóvel*

    Pode ativar outros métodos de autenticação, como *telefone do Office* ou *questões de Segurança,* conforme necessário para se adaptar às suas necessidades comerciais.

1. Para aplicar os métodos de autenticação, **selecione Guardar**.

Antes de os utilizadores poderem desbloquear a sua conta ou redefinir uma palavra-passe, devem registar as suas informações de contacto. A Azure AD utiliza estas informações de contacto para os diferentes métodos de autenticação estabelecidos nos passos anteriores.

Um administrador pode fornecer manualmente estas informações de contacto, ou os utilizadores podem ir a um portal de registo para fornecer a informação por si mesmos. Neste tutorial, crie a Azure AD para solicitar aos utilizadores o registo da próxima vez que se inscreverem.

1. A partir do menu no lado esquerdo da página **'Registar',** selecione *Sim* para **exigir que os utilizadores se registem ao iniciar sessão.**
1. Defina o **Número de dias antes de ser pedido aos utilizadores que voltem a confirmar as informações de autenticação** como *180*.

    É importante manter os contactos atualizados. Se existirem informações de contacto desatualizadas quando um evento SSPR começar, o utilizador poderá não conseguir desbloquear a sua conta ou redefinir a sua palavra-passe.

1. Para aplicar as definições de registo, **selecione Guardar**.

## <a name="set-up-notifications-and-customizations"></a>Configurar notificações e personalizações

Para manter os utilizadores informados sobre a atividade da conta, pode configurar o Azure AD para enviar notificações de e-mail quando um evento SSPR acontece. Estas notificações podem abranger contas de utilizador regulares e contas de administração. Para as contas de administração, esta notificação fornece outra camada de consciência quando uma palavra-passe de conta de administrador privilegiado é reposta usando SSPR. A Azure AD notificará todos os administradores globais quando alguém usar SSPR numa conta de administração.

1. A partir do menu no lado esquerdo da página **Notificações,** configurar as seguintes opções:

   * Defina a opção **Notificar os utilizadores sobre reposições de palavra-passe** como *Sim*.
   * Defina **Notificar todos os administradores quando outros administradores repõem as palavras-passe deles** como *Sim*.

1. Para aplicar as preferências de notificação, **selecione Guardar**.

Se os utilizadores precisarem de mais ajuda com o processo SSPR, pode personalizar o link "Contacte o seu administrador". O utilizador pode selecionar este link no processo de registo SSPR e quando desbloqueia a sua conta ou reinicia a sua palavra-passe. Para garantir que os seus utilizadores obtenham o suporte necessário, recomendamos vivamente que forneça um e-mail ou URL de ajuda personalizado.

1. A partir do menu no lado esquerdo da página de **personalização,** **desloque o link de ajuda** ao *Sim*.
1. No campo **de e-mail ou URL de ajuda personalizado,** forneça um endereço de e-mail ou URL de página web onde os seus utilizadores possam obter mais ajuda da sua organização, como *https: \/ /support.contoso.com/*
1. Para aplicar o link personalizado, **selecione Guardar**.

## <a name="test-self-service-password-reset"></a>Testar a reposição personalizada de palavra-passe

Com sSPR ativado e configurado, teste o processo SSPR com um utilizador que faz parte do grupo que selecionou na secção anterior, como *Test-SSPR-Group*. O exemplo a seguir utiliza a conta *testuser.* Forneça a sua própria conta de utilizador. Faz parte do grupo que habilitaste para a SSPR na primeira secção deste tutorial.

> [!NOTE]
> Quando testar o reset da palavra-passe de autosserviço, utilize uma conta não administradora. Por predefinição, o Azure AD permite o reset da palavra-passe de autosserviço para os administradores. São obrigados a usar dois métodos de autenticação para redefinir a sua senha. Para obter mais informações, consulte [as diferenças de política de reposição do administrador](concept-sspr-policy.md#administrator-reset-policy-differences).

1. Para ver o processo de registo manual, abra uma nova janela do navegador em modo InPrivate ou incógnito e navegue em *https: \/ /aka.ms/ssprsetup*. O Azure AD irá direcionar os utilizadores para este portal de registo quando iniciarem sação da próxima vez.
1. Inscreva-se com um utilizador de teste não administrador, como *testuser,* e registe os seus métodos de autenticação informações de contacto.
1. Uma vez terminado, selecione o botão marcado **Parece bom** e feche a janela do navegador.
1. Abra uma nova janela do navegador no modo InPrivate ou incógnito e navegue em *https: \/ /aka.ms/sspr*.
1. Introduza as informações da conta dos utilizadores de teste não administrador, como *testuser,* os caracteres do CAPTCHA e, em seguida, selecione **Next**.

    ![Introduza informações da conta de utilizador para redefinir a palavra-passe](media/tutorial-enable-sspr/password-reset-page.png)

1. Siga os passos de verificação para redefinir a sua palavra-passe. Quando terminar, receberá uma notificação por e-mail de que a sua palavra-passe foi reposta.

## <a name="clean-up-resources"></a>Limpar os recursos

Num tutorial posterior nesta série, irá configurar a gravação de password. Esta funcionalidade escreve alterações de palavra-passe de Azure AD SSPR de volta para um ambiente AD no local. Se quiser continuar com esta série tutorial para configurar a gravação de passwords, não desative agora a SSPR.

Se já não pretender utilizar a funcionalidade SSPR que criou como parte deste tutorial, desapedaque o estado de SSPR a **Nenhum** utilizando os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure e selecione **Azure Ative Directory** e, em seguida, selecione **a palavra-passe reiniciada** a partir do menu do lado esquerdo.
1. A partir da página **Propriedades,** sob a opção *Redefiniu a palavra-passe de serviço de autosserviço ativada*, selecione **Nenhum**.
1. Para aplicar a alteração SSPR, **selecione Guardar**.

## <a name="faqs"></a>FAQs

Esta secção explica questões comuns de administradores e utilizadores finais que experimentam SSPR:

- Porque é que os utilizadores federados esperam até 2 minutos depois de verem que **a sua palavra-passe foi reiniciada** antes de poderem utilizar palavras-passe sincronizadas a partir do local?

  Para os utilizadores federados cujas palavras-passe estão sincronizadas, a fonte de autoridade para as palavras-passe está no local. Como resultado, a SSPR atualiza apenas as palavras-passe no local. A sincronização de hash de palavra-passe de volta ao Azure AD está agendada para cada 2 minutos.

- Quando um utilizador recém-criado que está pré-preenchido com dados SSPR, como telefone e e-mail, visita a página de registo SSPR, **não perca o acesso à sua conta!** aparece como o título da página. Por que outros utilizadores que têm dados SSPR pré-povoados não vêem a mensagem?

  Um utilizador que vê **Não perca acesso à sua conta!** é membro dos grupos de registo SSPR/combinados que estão configurados para o arrendatário. Os utilizadores que não vêem **Não perdem acesso à sua conta!** Não faziam parte dos grupos de registo SSPR/combined.

- Quando alguns utilizadores passam pelo processo SSPR e reiniciam a sua palavra-passe, por que não vêem o indicador de força de senha?

  Os utilizadores que não vêem a força de senha fraca/forte têm a gravação de palavra-passe sincronizada ativada. Uma vez que a SSPR não consegue determinar a política de senha do ambiente de acesso do cliente, não pode validar a força ou fraqueza da palavra-passe. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ativou o autosserviço do Azure AD para um grupo selecionado de utilizadores. Aprendeu a:

> [!div class="checklist"]
> * Ativar o reset da palavra-passe de autosserviço para um grupo de utilizadores AD Azure
> * Configurar métodos de autenticação e opções de registo
> * Teste o processo SSPR como utilizador

> [!div class="nextstepaction"]
> [Ativar o Multi-Factor Authentication do Azure AD](./tutorial-enable-azure-mfa.md)
