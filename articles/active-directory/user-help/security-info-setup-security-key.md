---
title: Configurar uma chave de segurança como seu método de verificação – Azure AD
description: Como configurar sua página de informações de segurança (versão prévia) para verificar sua identidade para usar uma chave de segurança do Fast Identity online (FIDO2) como seu método de verificação.
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
ms.openlocfilehash: ee868ded0dc3a46f2ec20d0141aa2576cbf71f45
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062341"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Configurar uma chave de segurança como seu método de verificação

Você pode usar as chaves de segurança como um método de entrada sem senha dentro de sua organização. Uma chave de segurança é um dispositivo físico que é usado com um PIN exclusivo para entrar em sua conta corporativa ou de estudante. Como as chaves de segurança exigem que você tenha o dispositivo físico e algo que só saiba, ele é considerado um método de verificação mais forte do que um nome de usuário e senha.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Se você não vir a opção chave de segurança, é possível que sua organização não permita que você use essa opção para verificação. Nesse caso, você precisará escolher outro método ou entrar em contato com o suporte técnico da sua organização para obter mais assistência.

## <a name="security-verification-versus-password-reset-authentication"></a>Verificação de segurança versus autenticação de redefinição de senha

Os métodos de informações de segurança são usados para a verificação de segurança de dois fatores e para a redefinição de senha. No entanto, nem todos os métodos podem ser usados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| Aplicativo autenticador | Verificação de dois fatores e autenticação de redefinição de senha. |
| Mensagens de texto | Verificação de dois fatores e autenticação de redefinição de senha. |
| Chamadas telefônicas | Verificação de dois fatores e autenticação de redefinição de senha. |
| Chave de segurança | Verificação de dois fatores e autenticação de redefinição de senha. |
| Conta de email | Somente autenticação de redefinição de senha. Você precisará escolher outro método para verificação de dois fatores. |
| Questões de segurança | Somente autenticação de redefinição de senha. Você precisará escolher outro método para verificação de dois fatores. |

## <a name="what-is-a-security-key"></a>O que é uma chave de segurança?

Atualmente apoiamos vários designs e fornecedores de chaves de segurança utilizando os protocolos de autenticação sem palavras-passe Fast [Identity Online (FIDO2).](https://fidoalliance.org/fido2/) Essas chaves permitem que você entre em sua conta corporativa ou de estudante para acessar os recursos baseados em nuvem da sua organização quando estiver em um dispositivo e navegador da Web com suporte.

O administrador ou sua organização fornecerá uma chave de segurança se precisar dela para sua conta corporativa ou de estudante. Há diferentes tipos de chaves de segurança que você pode usar, por exemplo, uma chave USB que você conecta ao seu dispositivo ou uma chave NFC que você toca em um leitor NFC. Você pode encontrar mais informações sobre sua chave de segurança, incluindo qual tipo é, da documentação do fabricante.

> [!Note]
> Se não for possível usar uma chave de segurança FIDO2, haverá outros métodos de verificação com senha que você pode usar, como o aplicativo Microsoft Authenticator ou o Windows Hello. Para obter mais informações sobre a aplicação Microsoft Authenticator, consulte [o que é a aplicação Autenticadora Microsoft?](user-help-auth-app-overview.md) Para mais informações sobre o Windows Hello, consulte a [visão geral do Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Antes de começar

Antes de poder registrar sua chave de segurança, o seguinte deve ser verdadeiro:

- O administrador ativou esse recurso para uso em sua organização.

- Você está em um dispositivo que executa o Windows 10 pode 2019 atualização e uso de um navegador com suporte.

- Você tem uma chave de segurança física aprovada por seu administrador ou sua organização. Sua chave de segurança deve ser FIDO2 e compatível com a Microsoft. Se você tiver alguma dúvida sobre sua chave de segurança e se ela for compatível, entre em contato com o suporte técnico da sua organização.

## <a name="register-your-security-key"></a>Registrar sua chave de segurança

Você deve criar sua chave de segurança e dar a ela um PIN exclusivo antes de poder entrar em sua conta corporativa ou de estudante usando a chave. Você pode ter até 10 chaves registradas com sua conta. 

1. Vá à página **do Meu Perfil** na https://myprofile.microsoft.com e inscreva-se se ainda não o fez.

2. Selecione Informações de **Segurança,** selecione **método adicionar**e, em seguida, selecione a chave de **segurança** a partir da lista de drop-down do **método.**

    ![Caixa Adicionar método, com a chave de segurança selecionada](media/security-info/security-info-security-key-add-method.png)

3. **Selecione Adicionar**, e, em seguida, selecione o tipo de chave de segurança que tem, seja **o dispositivo USB** ou o dispositivo **NFC**.

    ![Escolha se você tem um tipo de chave de segurança USB ou NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Se você não tiver certeza de qual tipo de chave de segurança tem, consulte a documentação do fabricante. Se você não tiver certeza sobre o fabricante, entre em contato com o suporte técnico da sua organização para obter ajuda.

4. Tenha a sua chave de segurança fisicamente disponível e, em seguida, na caixa **de segurança,** selecione **Next**.

    ![Caixa de registro de início da chave de segurança](media/security-info/security-info-security-key-start-setup.png)

    Uma nova caixa é exibida para ajudá-lo a configurar seu novo método de entrada.

5. Na **configuração** da sua nova caixa de método de entrada, selecione **Seguinte**, e depois:

    - Se sua chave de segurança for um dispositivo USB, insira sua chave de segurança na porta USB do seu dispositivo.

    - Se sua chave de segurança for um dispositivo NFC, toque em sua chave de segurança para seu leitor.

6. Digite a sua chave de segurança única PIN na caixa de **segurança do Windows** e, em seguida, selecione **OK**.

    Voltará à instalação da sua nova caixa **de métodos de entrada.**

7. Selecione **Seguinte**.

8. Volte à página de informações de **Segurança,** escreva um nome que reconhecerá mais tarde para a sua nova chave de segurança e, em seguida, selecione **Next**.

    ![Página informações de segurança, nomeando sua chave de segurança](media/security-info/security-info-security-key-name.png)

    Sua chave de segurança está registrada e pronta para uso para entrar em sua conta corporativa ou de estudante.

9. Selecione **Feito** para fechar a caixa **de segurança.**

    A página de **informações** de segurança é atualizada com as informações da chave de segurança.

    ![Página informações de segurança, com todos os métodos registrados mostrados](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Excluir uma chave de segurança de suas informações de segurança

Se você perdeu ou não deseja mais usar uma chave de segurança, poderá excluir a chave de suas informações de segurança. Embora isso impeça que a chave de segurança seja usada com sua conta corporativa ou de estudante, a chave de segurança continua armazenando suas informações de dados e credenciais. Para eliminar os seus dados e informações credenciais da própria chave de segurança, deve seguir as instruções na secção de chave de [segurança compatível com](#reset-your-security-key) a Microsoft deste artigo.

1. Selecione o link **Eliminar** a partir da chave de segurança para remover.

2. Selecione **Ok** a partir da **caixa de segurança Eliminar.**

    Sua chave de segurança foi excluída e você não poderá mais usá-la para entrar em sua conta corporativa ou de estudante.

>[!Important]
>Se eliminar uma chave de segurança por engano, pode registá-la novamente utilizando as instruções na secção [Como registar a sua secção](#register-your-security-key) de chave de segurança deste artigo.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Gerenciar as configurações de chave de segurança nas configurações do Windows

Pode gerir as definições da chave de segurança a partir da aplicação Definições do **Windows,** incluindo a reposição da sua chave de segurança e a criação de uma nova chave de segurança PIN.

### <a name="reset-your-security-key"></a>Redefinir sua chave de segurança

Se você quiser excluir todas as informações de conta armazenadas em sua chave de segurança física, deverá retornar a chave de volta para seus padrões de fábrica. A redefinição de sua chave de segurança exclui tudo da chave, permitindo que você recomece.

>[!IMPORTANT]
>Redefinir a chave de segurança exclui tudo da chave, redefinindo-a para os padrões de fábrica.
>
> **Todos os dados e credenciais serão limpos.**

#### <a name="to-reset-your-security-key"></a>Para redefinir sua chave de segurança

1. Abra a aplicação Definições do Windows, selecione **Contas,** selecione **opções de iniciar sessão,** selecione Chave de **Segurança**e, em seguida, selecione **Gerir**.

2. Insira sua chave de segurança na porta USB ou toque no leitor NFC para verificar sua identidade.

3. Siga as instruções na tela, com base em seu fabricante de chave de segurança específico. Se o fabricante da chave não estiver listado nas instruções na tela, consulte o site do fabricante para obter mais informações.

4. Selecione **Perto** para fechar o ecrã **Gerir.**

### <a name="create-a-new-security-key-pin"></a>Criar um novo PIN de chave de segurança

Você pode criar um novo PIN de chave de segurança para sua chave de segurança.

#### <a name="to-create-a-new-security-key-pin"></a>Para criar um novo PIN de chave de segurança

1. Abra a aplicação Definições do Windows, selecione **Contas,** selecione **opções de iniciar sessão,** selecione Chave de **Segurança**e, em seguida, selecione **Gerir**.

2. Insira sua chave de segurança na porta USB ou toque no leitor NFC para verificar sua identidade.
3. **Selecione Adicionar** a partir da área PIN da chave de **segurança,** digite e confirme o seu novo PIN de chave de segurança e, em seguida, selecione **OK**.

     A chave de segurança é atualizada com o novo PIN de chave de segurança para uso com sua conta corporativa ou de estudante. Se decidir mudar novamente o PIN, pode selecionar o botão **'Alterar'.**
4. Selecione **Perto** para fechar o ecrã **Gerir.**

## <a name="additional-security-info-methods"></a>Métodos adicionais de informações de segurança

Para registrar uma chave de segurança, você deve ter pelo menos um método de verificação de segurança adicional registrado. Consulte a [secção De visão geral](security-info-add-update-methods-overview.md) para mais informações. 

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre métodos de verificação sem palavras-passe, leia o Anúncio Azure da Microsoft inicia a [pré-visualização pública das teclas de segurança FIDO2, permitindo o blog de logins sem palavras-passe](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) ou leia a [aplicação Microsoft Authenticator e](user-help-auth-app-overview.md) os artigos de visão geral do Windows [Hello.](https://www.microsoft.com/windows/windows-hello)

- Para obter informações mais detalhadas sobre [as chaves de segurança compatíveis com a Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Redefinir a sua palavra-passe se a perdeu ou esqueceu, a partir do portal de [reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de [reset do seu trabalho ou palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da conta da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
