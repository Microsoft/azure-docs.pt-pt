---
title: Configurar a verificação de dois passos - Azure Active Directory | Documentos da Microsoft
description: Quando a sua empresa configura o multi-factor Authentication, será solicitado para se inscrever para verificação de dois passos. Saiba como configurá-la.
services: active-directory
keywords: como utilizar o diretório do azure, do Active Directory na cloud, tutorial do Active Directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df72d03bae8987de4998276a0be0f3ce1ec0333
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230037"
---
# <a name="set-up-my-account-for-two-step-verification"></a>Configurar a minha conta para verificação de dois passos
Verificação de dois passos é uma etapa adicional de segurança que ajuda a proteger a sua conta, tornando mais difícil para outras pessoas a entrar na. Se estiver lendo este artigo, provavelmente recebeu uma mensagem de e-mail do seu administrador escolar ou profissionais sobre o multi-factor Authentication. Ou talvez tentou iniciar sessão e recebeu uma mensagem a pedir-lhe configurar a verificação de segurança adicional. Se for esse o caso **não pode iniciar sessão até ter concluído o processo de inscrição automática**.

Este artigo ajuda-o a configurar seus **conta escolar ou profissional**. Se pretender ativar a verificação de dois passos para a sua própria conta Microsoft pessoal, veja [sobre a verificação de dois passos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>Configurar a sua conta

Quando o suporte da empresa exige que começar a utilizar a verificação de dois passos, verá um ecrã que diz **o seu administrador necessita que configure esta conta para verificação de segurança adicional**:

![Configuração](./media/multi-factor-authentication-end-user-first-time/first.png)

Para começar a utilizar, selecione **configurá-lo agora.**

Se não vir um ecrã semelhante a esta quando iniciar sessão, siga as indicações [gerir as definições de verificação de dois passos](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) para procurar a página de definições onde pode gerir as suas opções de verificação.

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Decidir como pretende verificar sua inícios de sessão

A primeira pergunta no processo de inscrição é como deseja-nos contactá-lo. Examine as opções na tabela e utilize as ligações para ir para os passos de configuração para cada método.

| Método de contacto | Descrição |
| --- | --- |
| [Aplicação móvel](#use-a-mobile-app-as-the-contact-method) |- **Receba notificações para verificação.** Esta opção envia uma notificação para a aplicação de autenticador no seu smartphone ou tablet. Ver a notificação e, se esta for legítima, selecionar **Authenticate** na aplicação. Sua empresa ou escola pode exigir que introduza um PIN antes de se autenticar.<br>- **Utilize o código de verificação.** Neste modo, a aplicação authenticator gera um código de verificação que atualiza a cada 30 segundos. Introduza o código de verificação mais recente na interface de início de sessão.<br>A aplicação Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594) e [iOS](https://go.microsoft.com/fwlink/?linkid=866594).|
| [Chamada de telefone celular ou de texto](#use-your-mobile-phone-as-the-contact-method) |- **Chamada telefónica** coloca uma chamada de voz automatizada para o número de telefone que fornecer. Responder a chamada e prima # no teclado do telefone para autenticar.<br>- **Mensagem de texto** termina uma mensagem de texto que contém um código de verificação. Seguindo a linha de texto, responder à mensagem de texto ou introduza o código de verificação fornecido para a interface de início de sessão. |
| [Chamada de telefone do escritório](#use-your-office-phone-as-the-contact-method) |Coloca uma chamada de voz automatizada para o número de telefone que fornecer. Responder a chamada e prime # no teclado do telefone para autenticar. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Utilizar uma aplicação móvel como método de contacto
Ao utilizar este método requer que instale uma aplicação de autenticador no seu telemóvel ou tablet. Os passos neste artigo baseiam-se a aplicação Microsoft Authenticator, que está disponível para [Android](https://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](https://go.microsoft.com/fwlink/?Linkid=825073).

>[!NOTE]
>Não tem de utilizar a aplicação Microsoft Authenticator. Se já estiver a utilizar outra aplicação de autenticação, pode continuar a utilizá-lo.

1. Selecione **aplicação móvel** na lista pendente.
2. Selecione **receber notificações de verificação** ou **utilizar código de verificação**, em seguida, selecione **configurar**.

   ![Ecrã de verificação de segurança adicionais](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. No seu telemóvel ou tablet, abra a aplicação e selecione **+** para adicionar uma conta. (Em dispositivos Android, selecione os três pontos, em seguida, **adicionar conta**.)
4. Especifique que pretende adicionar uma conta escolar ou profissional. É aberto o scanner de código QR no seu telemóvel. Se a câmara não está a funcionar corretamente, pode optar por introduzir manualmente as informações da empresa. Para obter mais informações, consulte [adicionar manualmente uma conta](#add-an-account-manually).  
5. Digitalize a imagem de código de QR que apareceu o ecrã para configurar a aplicação móvel.  Selecione **feito** para fechar o ecrã de código QR.  

   ![Ecrã de código QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Quando concluir a ativação no telefone, selecione **contactar-me**.  Este passo envia uma notificação ou um código de verificação para o seu telefone. Selecione **verificar**.  
7. Se a sua empresa precisar de um PIN para aprovação de verificação de início de sessão, introduza-o.

   ![Caixa para introduzir um PIN](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. Depois de concluída a introdução de PIN, selecione **fechar**. Neste momento, a verificação deve ser concluída com êxito.
9. Recomendamos que introduza o seu número de telemóvel no caso de perder o acesso à sua aplicação móvel. Especifique o seu país/região na lista pendente e introduza o seu número de telemóvel na caixa junto ao nome do país/região. Selecione **Seguinte**.
10. Neste momento, lhe for pedido para configurar palavras-passe de aplicação para aplicações não baseadas no browser, tais como o Outlook 2010 ou anterior, ou a aplicação de e-mail nativas em dispositivos Apple. Isto acontece porque algumas aplicações não suportam a verificação de dois passos. Se não utilizar estas aplicações, clique em **feito** e ignore o resto dos passos.
11. Se estiver a utilizar estas aplicações, copie a palavra-passe de aplicação fornecido e cole-o em seu aplicativo em vez da palavra-passe regular. Pode utilizar a mesma palavra-passe de aplicação para várias aplicações. Para obter mais informações, [ajuda com palavras-passe de aplicação].
12. Clique em **Concluído**.

### <a name="add-an-account-manually"></a>Adicionar manualmente uma conta
Se pretender adicionar uma conta para a aplicação móvel manualmente, em vez de usar o leitor de QR, siga estes passos.

1. Selecione o **introduzir manualmente a conta** botão.  
2. Introduza o código e o URL que são fornecidos na mesma página que mostra o código de barras. Esta informação é incluído nos **código** e **URL** caixas na aplicação móvel.

    ![Configuração](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Quando a ativação estiver concluída, selecione **contactar-me**. Este passo envia uma notificação ou um código de verificação para o seu telefone. Selecione **verificar**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Utilizar o telemóvel como método de contacto
1. Selecione **telefone de autenticação** na lista pendente.  

    ![Configuração](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Escolha o seu país/região na lista pendente e introduza o seu número de telefone celular.
3. Selecione o método que prefere utilizar com o seu telemóvel - texto ou chamada.
4. Selecione **contactar-me** para verificar o seu número de telefone. Dependendo do modo selecionado, podemos enviar-lhe uma mensagem de texto ou contactá-lo. Siga as instruções fornecidas na tela, em seguida, selecione **Verifique se**.
5. Neste momento, lhe for pedido para configurar palavras-passe de aplicação para aplicações não baseadas no browser, tais como o Outlook 2010 ou anterior, ou a aplicação de e-mail nativas em dispositivos Apple. Isto acontece porque algumas aplicações não suportam a verificação de dois passos. Se não utilizar estas aplicações, clique em **feito** e ignore o resto dos passos.
6. Se estiver a utilizar estas aplicações, copie a palavra-passe de aplicação fornecido e cole-o em seu aplicativo em vez da palavra-passe regular. Pode utilizar a mesma palavra-passe de aplicação para várias aplicações. Para obter mais informações, [ajuda com palavras-passe de aplicação].
7. Clique em **Concluído**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Utilizar o seu telefone do escritório como método de contacto
1. Selecione **telefone do escritório** na lista suspensa  

    ![Configuração](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. A caixa de número de telefone é preenchida automaticamente com as suas informações de contacto da empresa. Se o número está errado ou em falta, peça ao seu administrador para fazer alterações.
3. Selecione **contactar-me** para verificar o seu telemóvel número e podemos chamará seu número. Siga as instruções fornecidas na tela, em seguida, selecione **Verifique se**.
4. Neste momento, lhe for pedido para configurar palavras-passe de aplicação para aplicações não baseadas no browser, tais como o Outlook 2010 ou anterior, ou a aplicação de e-mail nativas em dispositivos Apple. Isto acontece porque algumas aplicações não suportam a verificação de dois passos. Se não utilizar estas aplicações, clique em **feito** e ignore o resto dos passos.
5. Se estiver a utilizar estas aplicações, copie a palavra-passe de aplicação fornecido e cole-o em seu aplicativo em vez da palavra-passe regular. Pode utilizar a mesma palavra-passe de aplicação para várias aplicações. Para mais informações, veja [quais são as palavras-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md).
6. Clique em **Concluído**.

## <a name="next-steps"></a>Passos Seguintes
* Alterar as opções preferenciais e [gerir as definições de verificação de dois passos](multi-factor-authentication-end-user-manage-settings.md)
* Configurar [palavras-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md) para aplicações nativas dos dispositivos que não suportam a verificação de dois passos.
* Veja a [aplicação Microsoft Authenticator](user-help-auth-app-download-install.md) para depressa, autenticação segura, mesmo quando não tiver o serviço de célula.
