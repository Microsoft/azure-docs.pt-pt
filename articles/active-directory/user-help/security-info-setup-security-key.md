---
title: Crie uma chave de segurança como o seu método de verificação - Azure AD
description: Como configurar a sua página de informações de Segurança (pré-visualização) para verificar a sua identidade para utilizar uma chave de segurança Fast Identity Online (FIDO2) como método de verificação.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: c056e439deac71417ff14dcfc3f2c3c95db41946
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88797671"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Crie uma chave de segurança como o seu método de verificação

Pode utilizar as chaves de segurança como método de inscrição sem palavras-passe dentro da sua organização. Uma chave de segurança é um dispositivo físico que é usado com um PIN único para iniciar sposição na sua conta de trabalho ou escola. Porque as chaves de segurança exigem que tenha o dispositivo físico e algo que só você sabe, é considerado um método de verificação mais forte do que um nome de utilizador e senha.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Se não vir a opção chave de segurança, é possível que a sua organização não permita que use esta opção para verificação. Neste caso, terá de escolher outro método ou contactar o balcão de ajuda da sua organização para obter mais assistência.

## <a name="security-verification-versus-password-reset-authentication"></a>Verificação de segurança versus autenticação de reset de palavra-passe

Os métodos de informação de segurança são utilizados tanto para a verificação de segurança de dois fatores como para o reset da palavra-passe. No entanto, nem todos os métodos podem ser utilizados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| App autenticador | Verificação de dois fatores e autenticação de redefinição de password. |
| Mensagens de texto | Verificação de dois fatores e autenticação de redefinição de password. |
| Chamadas telefónicas | Verificação de dois fatores e autenticação de redefinição de password. |
| Chave de segurança | Verificação de dois fatores e autenticação de redefinição de password. |
| Conta de e-mail | Apenas a autenticação de redetofinição de palavra-passe. Terá de escolher outro método para a verificação de dois fatores. |
| Perguntas de segurança | Apenas a autenticação de redetofinição de palavra-passe. Terá de escolher outro método para a verificação de dois fatores. |

## <a name="what-is-a-security-key"></a>O que é uma chave de segurança?

Atualmente apoiamos vários projetos e fornecedores de chaves de segurança usando os protocolos de autenticação sem palavras-passe [Fast Identity Online (FIDO2).](https://fidoalliance.org/fido2/) Estas teclas permitem-lhe iniciar seduções na sua conta de trabalho ou escola para aceder aos recursos baseados na nuvem da sua organização quando estiver num dispositivo suportado e no navegador web.

O seu administrador ou organização fornecer-lhe-á uma chave de segurança se a necessitarem para o seu trabalho ou conta escolar. Existem diferentes tipos de chaves de segurança que pode utilizar, por exemplo, uma chave USB que liga ao seu dispositivo ou uma chave NFC que toca num leitor NFC. Pode descobrir mais informações sobre a sua chave de segurança, incluindo o tipo que é, a partir da documentação do fabricante.

> [!Note]
> Se não conseguir utilizar uma chave de segurança FIDO2, existem outros métodos de verificação sem palavras-passe que pode utilizar, como a aplicação Microsoft Authenticator ou o Windows Hello. Para obter mais informações sobre a aplicação Microsoft Authenticator, consulte [o que é a aplicação microsoft Authenticator?](user-help-auth-app-overview.md) Para mais informações sobre o Windows Hello, consulte [a visão geral do Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Antes de começar

Antes de poder registar a sua chave de segurança, o seguinte deve ser verdadeiro:

- O seu administrador ligou esta funcionalidade para ser utilizada dentro da sua organização.

- Encontra-se num dispositivo que executa a atualização do Windows 10 May 2019 e utiliza um browser suportado.

- Tem uma chave de segurança física aprovada pelo seu administrador ou organização. A sua chave de segurança deve estar em conformidade com o FIDO2 e o Microsoft. Se tiver alguma dúvida sobre a sua chave de segurança e se é compatível, contacte o balcão de ajuda da sua organização.

## <a name="register-your-security-key"></a>Registe a sua chave de segurança

Tem de criar a sua chave de segurança e dar-lhe um PIN único antes de poder iniciar sessão na sua conta de trabalho ou escola utilizando a chave. Pode ter até 10 chaves registadas na sua conta. 

1. Vá à página **'O Meu Perfil'** https://myaccount.microsoft.com e inscreva-se se ainda não o fez.

2. Selecione **Informação de Segurança**, selecione **Adicionar método** e, em seguida, selecione **a tecla** de segurança a partir da lista de down-down do **método.**

    ![Adicione caixa de método, com chave de segurança selecionada](media/security-info/security-info-security-key-add-method.png)

3. **Selecione Adicionar** e, em seguida, selecione o tipo de chave de segurança que tem, **dispositivo USB** ou **dispositivo NFC**.

    ![Escolha se tem uma chave de segurança USB ou NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Se não tiver a certeza de que tipo de chave de segurança tem, consulte a documentação do fabricante. Se não tiver a certeza sobre o fabricante, contacte o balcão de ajuda da sua organização para obter assistência.

4. Tenha a sua chave de segurança fisicamente disponível e, em seguida, na caixa **de chave de Segurança,** selecione **Next**.

    ![Caixa de registo de início de chave de segurança](media/security-info/security-info-security-key-start-setup.png)

    Uma nova caixa parece ajudar a ajudá-lo a configurar o seu novo método de entrada.

5. Na **configuração** da sua nova caixa de método de entrada, selecione **Seguinte** e, em seguida, :

    - Se a sua chave de segurança for um dispositivo USB, insira a sua chave de segurança na porta USB do seu dispositivo.

    - Se a sua chave de segurança for um dispositivo NFC, toque na chave de segurança do leitor.

6. Digite o pin da chave de segurança única na caixa **de segurança** do Windows e, em seguida, selecione **OK**.

    Você voltará à **configuração** da sua nova caixa de método de entrada.

7. Selecione **Seguinte**.

8. Volte à página **de informações de Segurança,** escreva um nome que reconhecerá mais tarde para a sua nova chave de segurança e, em seguida, selecione **Next**.

    ![Página de informações de segurança, nomeando a sua chave de segurança](media/security-info/security-info-security-key-name.png)

    A sua chave de segurança está registada e pronta para que possa iniciar sôms no seu trabalho ou na sua conta escolar.

9. Selecione **Feito** para fechar a caixa **de chave de segurança.**

    A página **de informações de Segurança** é atualizada com as informações chave de segurança.

    ![Página de informações de segurança, com todos os métodos registados mostrados](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Elimine uma chave de segurança da sua informação de segurança

Se perder ou deixar de querer utilizar uma chave de segurança, pode apagar a chave da sua informação de segurança. Enquanto isto impede que a chave de segurança seja usada com o seu trabalho ou conta escolar, a chave de segurança continua a armazenar os seus dados e informações de credencial. Para eliminar os seus dados e informações credenciais da própria chave de segurança, deve seguir as instruções na secção [de chave de segurança compatível com](#reset-your-security-key) a Microsoft deste artigo.

1. Selecione o link **Eliminar** da tecla de segurança para remover.

2. Selecione **Ok** na caixa de **segurança Eliminar.**

    A sua chave de segurança foi apagada e deixará de poder usá-la para iniciar seduca no seu trabalho ou na conta escolar.

>[!Important]
>Se eliminar uma chave de segurança por engano, pode registá-la novamente usando as instruções na secção [de como registar a sua](#register-your-security-key) secção de chave de segurança deste artigo.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Gerencie as definições das chaves de segurança a partir das Definições do Windows

Pode gerir as definições das chaves de segurança a partir da aplicação **Windows Settings,** incluindo a reposição da sua chave de segurança e a criação de um novo PIN da chave de segurança.

### <a name="reset-your-security-key"></a>Repôs a sua chave de segurança

Se pretender eliminar todas as informações da conta armazenadas na sua chave de segurança física, deve devolver a chave aos padrãos de fábrica. A reposição da sua tecla de segurança elimina tudo da tecla, permitindo-lhe recomeçar.

>[!IMPORTANT]
>A reposição da sua tecla de segurança elimina tudo da tecla, repondo-a para padrão de fábrica.
>
> **Todos os dados e credenciais serão limpos.**

#### <a name="to-reset-your-security-key"></a>Para redefinir a sua chave de segurança

1. Abra a aplicação De Definições do Windows, selecione **Contas**, selecione **opções de iniciar sposição,** selecione **Chave de Segurança** e, em seguida, selecione **Gerir**.

2. Insira a sua chave de segurança na porta USB ou toque no leitor NFC para verificar a sua identidade.

3. Siga as instruções no ecrã, com base no fabricante específico da chave de segurança. Se o fabricante da chave não estiver listado nas instruções no ecrã, consulte o site do fabricante para obter mais informações.

4. Selecione **Perto** para fechar o ecrã **'Gerir'.**

### <a name="create-a-new-security-key-pin"></a>Criar um novo PIN chave de segurança

Pode criar um novo PIN de chave de segurança para a sua chave de segurança.

#### <a name="to-create-a-new-security-key-pin"></a>Para criar um novo PIN chave de segurança

1. Abra a aplicação De Definições do Windows, selecione **Contas**, selecione **opções de iniciar sposição,** selecione **Chave de Segurança** e, em seguida, selecione **Gerir**.

2. Insira a sua chave de segurança na porta USB ou toque no leitor NFC para verificar a sua identidade.
3. **Selecione Adicione** na área **PIN da chave de segurança,** escreva e confirme o seu novo PIN da chave de segurança e, em seguida, selecione **OK**.

     A chave de segurança é atualizada com o novo PIN da chave de segurança para utilização com a sua conta de trabalho ou escola. Se decidir mudar o PIN novamente, pode selecionar o botão **Alterar.**
4. Selecione **Perto** para fechar o ecrã **'Gerir'.**

## <a name="additional-security-info-methods"></a>Métodos adicionais de informação de segurança

Para registar uma chave de segurança, deve ter pelo menos um método adicional de verificação de segurança registado. Consulte a [secção Geral para](./security-info-setup-auth-app.md) mais informações. 

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre métodos de verificação sem palavras-passe, a [leitura do Azure AD da Microsoft inicia a pré-visualização pública das teclas de segurança FIDO2, permitindo o blog de login sem palavras-passe](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) ou leia o [que é a app microsoft Authenticator?](user-help-auth-app-overview.md) [](https://www.microsoft.com/windows/windows-hello)

- Para obter informações mais detalhadas sobre [as chaves de segurança compatíveis com a Microsoft](/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Reinicie a sua palavra-passe se a tiver perdido ou esquecido, a partir do [portal de reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de Reposição do seu trabalho ou [palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da Conta Da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)