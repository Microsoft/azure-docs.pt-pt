---
title: Configurar uma chave de segurança como o seu método de verificação - Azure AD
description: Como configurar a sua página de informações de Segurança (pré-visualização) para verificar a sua identidade para utilizar uma chave de segurança Fast Identity Online (FIDO2) como método de verificação.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: 016a09432499c5fd2be8fd488c7ef7a644c00bca
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628338"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Configurar uma chave de segurança como o seu método de verificação

Pode utilizar as chaves de segurança como um método de entrada sem palavras-passe dentro da sua organização. Uma chave de segurança é um dispositivo físico que é usado com um PIN único para iniciar sessão no seu trabalho ou conta escolar. Como as chaves de segurança exigem que tenha o dispositivo físico e algo que só você sabe, é considerado um método de verificação mais forte do que um nome de utilizador e senha.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Se não vir a opção chave de segurança, é possível que a sua organização não lhe permita usar esta opção para verificação. Neste caso, terá de escolher outro método ou contactar o balcão de ajuda da sua organização para obter mais assistência.

## <a name="security-verification-versus-password-reset-authentication"></a>Verificação de segurança versus autenticação de reset de palavra-passe

Os métodos de informação de segurança são utilizados tanto para a verificação de segurança de dois fatores como para o reset de palavras-passe. No entanto, nem todos os métodos podem ser utilizados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| Aplicação de autenticação | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Mensagens de texto | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Telefonemas | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Chave de segurança | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Conta de e-mail | Apenas a autenticação de reset da palavra-passe. Terá de escolher outro método para verificação de dois fatores. |
| Perguntas de segurança | Apenas a autenticação de reset da palavra-passe. Terá de escolher outro método para verificação de dois fatores. |

## <a name="what-is-a-security-key"></a>O que é uma chave de segurança?

Atualmente apoiamos vários designs e fornecedores de chaves de segurança utilizando os protocolos de autenticação sem palavras-passe Fast [Identity Online (FIDO2).](https://fidoalliance.org/fido2/) Estas chaves permitem-lhe iniciar sessão no seu trabalho ou na conta escolar para aceder aos recursos baseados na nuvem da sua organização quando estiver num dispositivo e navegador de internet suportados.

O seu administrador ou organização fornecer-lhe-á uma chave de segurança se a exigir em seu trabalho ou conta escolar. Existem diferentes tipos de teclas de segurança que pode utilizar, por exemplo, uma chave USB que liga ao seu dispositivo ou uma chave NFC que toca num leitor de NFC. Pode obter mais informações sobre a sua chave de segurança, incluindo que tipo é, a partir da documentação do fabricante.

> [!Note]
> Se não conseguir utilizar uma chave de segurança FIDO2, existem outros métodos de verificação sem palavras-passe que pode utilizar, como a aplicação Microsoft Authenticator ou o Windows Hello. Para obter mais informações sobre a aplicação Microsoft Authenticator, consulte [o que é a aplicação Autenticadora Microsoft?](user-help-auth-app-overview.md) Para mais informações sobre o Windows Hello, consulte a [visão geral do Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Antes de começar

Antes de poder registar a sua chave de segurança, o seguinte deve ser verdade:

- O seu administrador ligou esta funcionalidade para ser utilizada dentro da sua organização.

- Está num dispositivo que executa a Atualização do Windows 10 de maio de 2019 e que utiliza um navegador suportado.

- Tem uma chave de segurança física aprovada pelo seu administrador ou pela sua organização. A sua chave de segurança deve ser compatível com o FIDO2 e com a Microsoft. Se tiver alguma dúvida sobre a sua chave de segurança e se é compatível, contacte o balcão de ajuda da sua organização.

## <a name="register-your-security-key"></a>Registe a sua chave de segurança

Você deve criar a sua chave de segurança e dar-lhe um PIN único antes de poder iniciar sessão no seu trabalho ou conta escolar usando a chave. Pode ter até 10 chaves registadas na sua conta. 

1. Vá à página do https://myaccount.microsoft.com Meu **Perfil** e inscreva-se se ainda não o fez.

2. Selecione Informações de **Segurança,** selecione **método adicionar**e, em seguida, selecione a chave de **segurança** a partir da lista de drop-down do **método.**

    ![Adicionar caixa de método, com chave de segurança selecionada](media/security-info/security-info-security-key-add-method.png)

3. **Selecione Adicionar**, e, em seguida, selecione o tipo de chave de segurança que tem, seja **o dispositivo USB** ou o dispositivo **NFC**.

    ![Escolha se tem uma chave de segurança USB ou NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Se não tiver a certeza do tipo de chave de segurança que tem, consulte a documentação do fabricante. Se não tiver a certeza sobre o fabricante, contacte o balcão de ajuda da sua organização para obter assistência.

4. Tenha a sua chave de segurança fisicamente disponível e, em seguida, na caixa **de segurança,** selecione **Next**.

    ![Caixa de registo de início de chave de segurança](media/security-info/security-info-security-key-start-setup.png)

    Uma nova caixa parece ajudar a acompanhá-lo através da criação do seu novo método de entrada.

5. Na **configuração** da sua nova caixa de método de entrada, selecione **Seguinte**, e depois:

    - Se a sua chave de segurança for um dispositivo USB, insira a sua chave de segurança na porta USB do seu dispositivo.

    - Se a sua chave de segurança for um dispositivo NFC, toque na chave de segurança do leitor.

6. Digite a sua chave de segurança única PIN na caixa de **segurança do Windows** e, em seguida, selecione **OK**.

    Voltará à instalação da sua nova caixa **de métodos de entrada.**

7. Selecione **Seguinte**.

8. Volte à página de informações de **Segurança,** escreva um nome que reconhecerá mais tarde para a sua nova chave de segurança e, em seguida, selecione **Next**.

    ![Página de informações de segurança, nomeando a sua chave de segurança](media/security-info/security-info-security-key-name.png)

    A sua chave de segurança está registada e pronta para usar para iniciar sessão no seu trabalho ou conta escolar.

9. Selecione **Feito** para fechar a caixa **de segurança.**

    A página de **informações** de segurança é atualizada com as informações da chave de segurança.

    ![Página de informações de segurança, com todos os métodos registados mostrados](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Elimine uma chave de segurança das suas informações de segurança

Se perder ou deixar de querer utilizar uma chave de segurança, pode eliminar a chave das suas informações de segurança. Enquanto isto impede que a chave de segurança seja usada com o seu trabalho ou conta escolar, a chave de segurança continua a armazenar os seus dados e informações credenciais. Para eliminar os seus dados e informações credenciais da própria chave de segurança, deve seguir as instruções na secção de chave de [segurança compatível com](#reset-your-security-key) a Microsoft deste artigo.

1. Selecione o link **Eliminar** a partir da chave de segurança para remover.

2. Selecione **Ok** a partir da **caixa de segurança Eliminar.**

    A sua chave de segurança é apagada e já não poderá usá-la para iniciar sessão no seu trabalho ou na conta escolar.

>[!Important]
>Se eliminar uma chave de segurança por engano, pode registá-la novamente utilizando as instruções na secção [Como registar a sua secção](#register-your-security-key) de chave de segurança deste artigo.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Gerencie as definições da chave de segurança a partir de Definições do Windows

Pode gerir as definições da chave de segurança a partir da aplicação Definições do **Windows,** incluindo a reposição da sua chave de segurança e a criação de uma nova chave de segurança PIN.

### <a name="reset-your-security-key"></a>Redefinir a sua chave de segurança

Se pretender eliminar todas as informações de conta armazenadas na sua chave de segurança física, deve devolver a chave aos seus incumprimentos de fábrica. A reposição da chave de segurança elimina tudo da chave, permitindo-lhe recomeçar.

>[!IMPORTANT]
>A reposição da chave de segurança elimina tudo da chave, repondo-a em incumprimentos de fábrica.
>
> **Todos os dados e credenciais serão limpos.**

#### <a name="to-reset-your-security-key"></a>Para redefinir a sua chave de segurança

1. Abra a aplicação Definições do Windows, selecione **Contas,** selecione **opções de iniciar sessão,** selecione Chave de **Segurança**e, em seguida, selecione **Gerir**.

2. Insira a sua chave de segurança na porta USB ou toque no leitor NFC para verificar a sua identidade.

3. Siga as instruções no ecrã, com base no seu fabricante específico de chaves de segurança. Se o seu fabricante chave não estiver listado nas instruções no ecrã, consulte o site do fabricante para obter mais informações.

4. Selecione **Perto** para fechar o ecrã **Gerir.**

### <a name="create-a-new-security-key-pin"></a>Criar uma nova chave de segurança PIN

Pode criar uma nova chave de segurança PIN para a sua chave de segurança.

#### <a name="to-create-a-new-security-key-pin"></a>Para criar uma nova chave de segurança PIN

1. Abra a aplicação Definições do Windows, selecione **Contas,** selecione **opções de iniciar sessão,** selecione Chave de **Segurança**e, em seguida, selecione **Gerir**.

2. Insira a sua chave de segurança na porta USB ou toque no leitor NFC para verificar a sua identidade.
3. **Selecione Adicionar** a partir da área PIN da chave de **segurança,** digite e confirme o seu novo PIN de chave de segurança e, em seguida, selecione **OK**.

     A chave de segurança é atualizada com a nova chave de segurança PIN para utilização com o seu trabalho ou conta escolar. Se decidir mudar novamente o PIN, pode selecionar o botão **'Alterar'.**
4. Selecione **Perto** para fechar o ecrã **Gerir.**

## <a name="additional-security-info-methods"></a>Métodos adicionais de informação de segurança

Para registar uma chave de segurança, deve ter pelo menos um método adicional de verificação de segurança registado. Consulte a [secção De visão geral](security-info-add-update-methods-overview.md) para mais informações. 

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre métodos de verificação sem palavras-passe, leia o Anúncio Azure da Microsoft inicia a [pré-visualização pública das teclas de segurança FIDO2, permitindo o blog de logins sem palavras-passe](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) ou leia a [aplicação Microsoft Authenticator e](user-help-auth-app-overview.md) os artigos de visão geral do Windows [Hello.](https://www.microsoft.com/windows/windows-hello)

- Para obter informações mais detalhadas sobre [as chaves de segurança compatíveis com a Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Redefinir a sua palavra-passe se a perdeu ou esqueceu, a partir do portal de [reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de [reset do seu trabalho ou palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da conta da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
