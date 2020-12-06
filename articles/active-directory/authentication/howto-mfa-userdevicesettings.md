---
title: Gerir métodos de autenticação para autenticação multi-factor Azure AD - Diretório Ativo Azure
description: Saiba como pode configurar as definições do utilizador do Azure Ative Directory para autenticação multi-factor Azure AD
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a96f01d38fb41f64336d0a2b1d2aa33a96ca9f1a
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742753"
---
# <a name="manage-user-authentication-methods-for-azure-ad-multi-factor-authentication"></a>Gerir métodos de autenticação do utilizador para autenticação multi-factor Azure AD

Os utilizadores em Azure AD têm dois conjuntos distintos de informações de contacto:  

- Informações de contacto de perfil público, que são geridas no perfil do utilizador e visíveis aos membros da sua organização. Para os utilizadores sincronizados a partir de ative directy no local, esta informação é gerida em serviços de domínio de diretório ativo do Windows Server.
- Métodos de autenticação, que são sempre mantidos privados e utilizados apenas para a autenticação, incluindo a autenticação de vários fatores (MFA). Os administradores podem gerir estes métodos na lâmina do método de autenticação de um utilizador e os utilizadores podem gerir os seus métodos na página de Informações de Segurança do MyAccount.

Ao gerir os métodos de autenticação multi-factor Azure AD para os seus utilizadores, os administradores de autenticação podem: 

1. Adicione métodos de autenticação para um utilizador específico, incluindo números de telefone utilizados para MFA.
1. Redefinir a palavra-passe de um utilizador.
1. Exigir que um utilizador se registe para MFA.
1. Revogar as sessões de MFA existentes.
1. Eliminar as palavras-passe de aplicações existentes de um utilizador  

## <a name="add-authentication-methods-for-a-user"></a>Adicionar métodos de autenticação para um utilizador 

Pode adicionar métodos de autenticação para um utilizador através do portal Azure ou do Microsoft Graph.  

> [!NOTE]
> Por razões de segurança, os campos de informações de contacto do utilizador público não devem ser utilizados para a realização de MFA. Em vez disso, os utilizadores devem preencher os seus números de métodos de autenticação para serem utilizados para mFA.  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="Adicionar métodos de autenticação do portal Azure":::

Para adicionar métodos de autenticação para um utilizador através do portal Azure:  

1. Inicie sessão no **portal do Azure**. 
1. Navegue para Utilizadores **de Diretório Ativo Azure** Todos os  >  **Users**  >  **utilizadores**. 
1. Escolha o utilizador para quem deseja adicionar um método de autenticação e selecione **métodos de autenticação.**  
1. Na parte superior da janela, selecione **+ Adicione o método de autenticação**.
   1. Selecione um método (número de telefone ou e-mail). O e-mail pode ser usado para reiniciar a auto-senha, mas não para autenticação. Ao adicionar um número de telefone, selecione um tipo de telefone e introduza o número de telefone com formato válido (por exemplo, +1 42555551234).
   1. Selecione **Adicionar**.

> [!NOTE]
> A experiência de pré-visualização permite aos administradores adicionar quaisquer métodos de autenticação disponíveis para os utilizadores, enquanto a experiência original apenas permite a atualização de métodos de telefone e telefone alternativos.

### <a name="manage-methods-using-powershell"></a>Gerir métodos utilizando o PowerShell:  

Instale o módulo Microsoft.Graph.Identity.Signins PowerShell utilizando os seguintes comandos. 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

Listar métodos de autenticação baseados no telefone para um utilizador específico.

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

Crie um método de autenticação de telemóvel para um utilizador específico.

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType “mobile” -phoneNumber "+1 7748933135"
```

Remova um método de telefone específico para um utilizador

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

Os métodos de autenticação também podem ser geridos usando APIs do Microsoft Graph, mais informações podem ser encontradas no documento [Azure AD métodos de autenticação API visão geral](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)

## <a name="manage-user-authentication-options"></a>Gerir opções de autenticação do utilizador

Se lhe for atribuída a função *de Administrador de Autenticação,* pode exigir que os utilizadores repensem a sua palavra-passe, re-registem-se para MFA ou revoguem as sessões de MFA existentes a partir do seu objeto de utilizador. Para gerir as definições do utilizador, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, selecione **Azure Active Directory** > **Utilizadores** > **Todos os utilizadores**.
1. Escolha o utilizador que deseja realizar uma ação e selecione **métodos de autenticação**. Na parte superior da janela, em seguida, escolha uma das seguintes opções para o utilizador:
   - **A Palavra-passe reinicializar** a palavra-passe do utilizador e atribui uma palavra-passe temporária que deve ser alterada no próximo sessão de sessão.
   - **Exigir re-registo MFA** faz com que, quando o utilizador assinar na próxima vez, seja solicitado que criem um novo método de autenticação MFA.
   
      > [!NOTE]
      > Os métodos de autenticação atualmente registados do utilizador não são eliminados quando um administrador requer re-inscrição para MFA. Depois de um utilizador voltar a registar-se para mFA, recomendamos que revejam as suas informações de segurança e apaguem quaisquer métodos de autenticação previamente registados que já não sejam utilizáveis.
   
   - **A Revogação das Sessões de MFA** limpa as sessões de MFA lembradas do utilizador e exige que realizem MFA da próxima vez que for exigida pela política do dispositivo.
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Gerir métodos de autenticação a partir do portal Azure":::

## <a name="delete-users-existing-app-passwords"></a>Eliminar as palavras-passe existentes dos utilizadores

Para os utilizadores que tenham definido as palavras-passe da aplicação, os administradores também podem optar por eliminar estas palavras-passe, fazendo com que a autenticação antiga falhe nessas aplicações. Estas ações podem ser necessárias se precisar de prestar assistência a um utilizador, ou precisar de redefinir os seus métodos de autenticação. As aplicações não-navegador que estavam associadas a estas palavras-passe de apps vão deixar de funcionar até que seja criada uma nova senha de aplicação. 

Para eliminar as palavras-passe da aplicação de um utilizador, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No lado esquerdo, selecione **Utilizadores do Diretório Azure Ative**  >  **Users**  >  **Todos os utilizadores**.
1. Selecione **Multi-Factor Authentication**. Pode ser necessário deslocar-se para a direita para ver esta opção de menu. Selecione a imagem de exemplo abaixo para ver a janela completa do portal Azure e a localização do menu: [ ![ Selecione a autenticação multi-factor da janela do Utilizadores em Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Verifique a caixa ao lado do utilizador ou utilizadores que deseja gerir. Uma lista de opções de passo rápido aparece à direita.
1. **Selecione Gerir as definições** do utilizador, em seguida, verifique a caixa para Eliminar todas as **palavras-passe de aplicações existentes geradas pelos utilizadores selecionados**, como mostrado no exemplo seguinte: ![ Eliminar todas as palavras-passe de aplicações existentes](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Selecione **guardar** e, em seguida, **fechar**.

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como configurar as definições individuais do utilizador. Para configurar as definições gerais do serviço de autenticação multi-factor Azure AD, consulte as definições de [autenticação multi-factor Configure Azure AD](howto-mfa-mfasettings.md).

Se os seus utilizadores precisarem de ajuda, consulte o [guia do Utilizador para autenticação multi-factor Azure AD](../user-help/multi-factor-authentication-end-user-first-time.md).
