---
title: Resolver problemas de verificação de dois passos - Azure Active Directory | Documentos da Microsoft
description: Fornece instruções para os utilizadores sobre o que fazer se eles se deparam um problema com o Azure multi-factor Authentication e verificação de dois passos.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: lizross
ms.reviewer: kexia
ms.collection: M365-identity-device-management
ms.openlocfilehash: b74049833b055caa112c346b74798893f2c0febf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60476675"
---
# <a name="get-help-with-two-step-verification"></a>Obter ajuda para a verificação de dois passos

Verificação de dois passos é uma funcionalidade de segurança a que sua organização está a utilizar para proteger as suas contas. A verificação de dois passos é mais segura do que utilizar apenas uma palavra-passe, porque depende de duas formas de autenticação: algo que os utilizadores sabem e algo que têm com eles. Algo que conhece é a palavra-passe, enquanto a algo que tem com é o seu telemóvel ou num dispositivo. Utilizar a verificação pode ajudar a parar os hackers mal-intencionados de iniciar sessão como, mesmo que eles recebem a palavra-passe.

Embora a Microsoft oferece verificação de dois passos, é sua organização que decide se usar o recurso. Não é possível optar pela se a sua organização obriga-lo, tal como não é possível optar por utilizar uma palavra-passe para proteger a sua conta.

>[!Note]
>Se procura-se para obter mais informações sobre como utilizar a verificação de dois passos com sua conta Microsoft pessoal, consulte a [sobre a verificação de dois passos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) artigo.

## <a name="why-do-i-need-to-use-another-verification-method"></a>Por que motivo é necessário utilizar outro método de verificação?

Há vários motivos por que poderá ter de utilizar um método de verificação alternativo com a sua conta. Por exemplo:

- **Esqueceu-se ao seu telefone ou dispositivo.** Alguns dias, deixe o seu telemóvel em casa, mas ainda tem de iniciar sessão no trabalho. Em primeiro lugar, deve tentar iniciar sessão utilizando um método diferente que não precise de seu telefone.

- **Perdeu o seu telemóvel ou tem um novo número de telefone.** Se perdeu o seu telemóvel ou ter obtido um novo número, pode iniciar sessão com um método diferente ou peça ao seu administrador para limpar as definições. É altamente recomendável permitir que o seu administrador de saber se o seu telemóvel tiver sido perdido ou roubado, para que as atualizações adequadas podem ser feitas à sua conta. Depois das definições forem verificadas, será solicitado a [registar para a verificação de](multi-factor-authentication-end-user-first-time.md) da próxima vez que iniciar sessão.

- **Não a obter o texto de autenticação ou de uma chamada telefónica.** Há vários motivos por que não poderá obter o texto ou chamada telefónica. Se conseguiu fazer com textos ou chamadas telefónicas no passado, isto é provavelmente um problema com o fornecedor de telefone, não a sua conta. Se tiver muitas vezes, atrasos devido a um sinal ruim, recomendamos que utilize o [aplicação Microsoft Authenticator](user-help-auth-app-download-install.md) no seu dispositivo móvel. Esta aplicação pode gerar códigos de segurança aleatório para o início de sessão, sem necessidade de qualquer sinal de célula ou a ligação à Internet.<br><br>Se estiver a tentar receber uma mensagem de texto, peça um amigo para texto como um teste para se certificar-se de que pode obter uma e, se obteve várias mensagens, certifique-se de estiver a utilizar o código do mais atual.

- **As palavras-passe de aplicação não estão funcionando.** As palavras-passe de aplicação substituem a palavra-passe normal para aplicativos de área de trabalho mais antigos que não suportam a verificação de dois passos. Em primeiro lugar, certifique-se de que escreveu corretamente a palavra-passe. Se de que não corrigi-lo, tente iniciar sessão no [criar uma nova palavra-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md) ou contactar o administrador para [eliminar suas palavras-passe de aplicação existentes](../authentication/howto-mfa-userdevicesettings.md) para que possa criar um novo.

## <a name="sign-in-using-another-verification-method"></a>Inicie sessão com outro método de verificação

1. Inicie sessão na sua conta normalmente e escolha o **inscrever-se de outra maneira** ligar sobre o **verificação em dois passos** página.

    ![Alterar método de verificação de início de sessão](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Se não vir a **iniciar sessão de outra maneira** link, isso significa que ainda não configurar outros métodos de verificação. Terá de contactar o administrador para obter ajuda a iniciar sessão na sua conta. Depois de iniciar sessão, certifique-se de que adicionar métodos de verificação adicional. Para obter mais informações sobre como adicionar métodos de verificação, consulte a [gerir as definições de verificação de dois passos](multi-factor-authentication-end-user-manage-settings.md) artigo.<br><br>Se vir a ligação, mas ainda não vir quaisquer outros métodos de verificação, terá de contactar o administrador para obter ajuda a iniciar sessão na sua conta.

2. Escolha seu método de verificação alternativo e continuar com o processo de verificação de dois passos.

3. Depois que estiver na sua conta, pode atualizar seus métodos de verificação (se necessário). Para obter mais informações sobre adicionar ou alterar seus métodos, consulte a [gerir as definições de verificação de dois passos](multi-factor-authentication-end-user-manage-settings.md) artigo.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Não encontrei uma resposta para meu problema

Se já tentou estes passos, mas é ainda em execução em problemas, contacte o administrador para obter mais ajuda.

## <a name="related-topics"></a>Tópicos relacionados

* [Gerir as definições de verificação de dois passos](multi-factor-authentication-end-user-manage-settings.md)

* [FAQ de aplicação Microsoft Authenticator](user-help-auth-app-faq.md)
