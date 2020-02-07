---
title: Como configurar o único sign-on de senha para aplicações da AD Azure  Microsoft Docs
description: Como configurar o insessão individual de senha (SSO) para as suas aplicações empresariais Azure AD na plataforma de identidade da Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563bda275b73f76b042b5e57a9909ca78c504bb3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063531"
---
# <a name="configure-password-single-sign-on"></a>Configure o sinal único da palavra-passe

Quando [adiciona uma aplicação](add-gallery-app.md) de galeria ou uma [aplicação web não-galeria](add-non-gallery-app.md) às suas Aplicações Empresariais AD Azure, uma das opções únicas de inscrição disponível para si é a [única assinatura baseada em palavra-passe](what-is-single-sign-on.md#password-based-sso). Esta opção está disponível para qualquer web com uma página de início de sessão HTML. O SSO baseado em palavra-passe, também referido como abóbada de senhas, permite-lhe gerir o acesso do utilizador e palavras-passe a aplicações web que não suportam a federação de identidade. Também é útil para cenários em que vários utilizadores precisam de partilhar uma única conta, como nas contas de aplicações da sua organização nas redes sociais. 

O SSO baseado em palavra-passe é uma ótima maneira de começar a integrar aplicações em Azure AD rapidamente, e permite-lhe:

-   Ative **o Single Sign-on para os seus utilizadores** armazenando e reproduzindo de forma segura os nomes de utilizador e palavras-passe para a aplicação que integrou com o Azure AD

-   **Aplicações de suporte que requerem vários campos de entrada** para aplicações que requerem mais do que apenas username e campos de palavra-passe para iniciar sessão

-   **Personalize as etiquetas** do nome de utilizador e dos campos de entrada de palavras-passe que os utilizadores vêem no Painel de Acesso à [Aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando introduzem as suas credenciais

-   Permita que os seus **utilizadores** forneçam os seus próprios nomes de utilizador e palavras-passe para quaisquer contas de aplicação existentes que estejam a digitar manualmente no Painel de Acesso à [Aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Permitir que um **membro do grupo empresarial** especifique os nomes de utilizador e as palavras-passe atribuídas a um utilizador utilizando a funcionalidade de acesso a [aplicações self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Permitir que um **administrador** especifique um nome de utilizador e uma palavra-passe a utilizar por indivíduos ou grupos ao iniciar sessão na aplicação utilizando a funcionalidade 'Credenciais de actualização' 

## <a name="before-you-begin"></a>Antes de começar

Se a aplicação não tiver sido adicionada ao seu inquilino DaD Azure, consulte [Adicionar uma aplicação](add-gallery-app.md) de galeria ou [adicionar uma aplicação não-galeria](add-non-gallery-app.md).

## <a name="open-the-app-and-select-password-single-sign-on"></a>Abra a aplicação e selecione um único sinal de senha

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador de aplicação em nuvem ou administrador de candidatura para o seu inquilino Azure AD.

2. Navegue para **as aplicações**de Diretório Ativo > **Azure.** Aparece uma amostra aleatória das aplicações no seu inquilino DaD Azure. 

3. No menu **Tipo de Aplicação,** selecione **Todas as aplicações,** e, em seguida, selecione **Aplicar**.

4. Introduza o nome da aplicação na caixa de pesquisa e, em seguida, selecione a aplicação a partir dos resultados.

5. Na secção **Gerir,** selecione **single sign-on**. 

6. Selecione **baseado em palavra-passe**.

7. Introduza o URL da página de entrada baseada na web da aplicação. Esta cadeia deve ser a página que inclui o campo de entrada do nome de utilizador.

   ![Inscrição única baseada em palavra-passe](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Selecione **Guardar**. A Azure AD tenta analisar a página de entrada para uma entrada de nome de utilizador e uma entrada de senha. Se a tentativa for bem sucedida, estás feito. 
 
> [!NOTE]
> O próximo passo é [atribuir utilizadores ou grupos à aplicação.](methods-for-assigning-users-and-groups.md) Depois de ter atribuído utilizadores e grupos, pode fornecer credenciais para ser usado em nome de um utilizador quando iniciar sessão na aplicação. **Selecione Utilizadores e grupos,** selecione a caixa de verificação para a linha do utilizador ou do grupo e, em seguida, clique em **'Credenciais de actualização'.** Em seguida, introduza o nome de utilizador e a palavra-passe para serem utilizados em nome do utilizador ou do grupo. Caso contrário, os utilizadores serão solicitados a introduzir as credenciais no momento do lançamento.
 

## <a name="manual-configuration"></a>Configuração manual

Se a tentativa de análise da Azure AD falhar, pode configurar o início manual.

1. Em **\<nome de aplicação> Configuração**, selecione Configurar \<nome de **aplicação> Definições de inscrição individual de palavra-passe** para visualizar a página de início de **'Configurar'.** 

2. Selecione **manualmente detetar campos de sessão**. São apresentados instruções adicionais que descrevem a deteção manual dos campos de inscrição.

   ![Configuração manual do único sign-on baseado em palavra-passe](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Selecione **os campos de inscrição de captura**. Uma página de estado de captura abre num novo separador, mostrando que a captura de metadados de mensagem **está em andamento.**

4. Se a caixa **de extensão do painel** de acesso aparecer num novo separador, selecione Instalar **agora** para instalar a extensão de extensão de extensão de sinais de acesso das **minhas aplicações.** (A extensão do navegador requer Microsoft Edge, Chrome ou Firefox.) Em seguida, instale, lance e ative a extensão e refresque a página de estado de captura.

   A extensão do navegador abre então outro separador que exibe o URL introduzido.
5. No separador com o URL introduzido, passe pelo processo de entrada. Preencha o nome de utilizador e os campos de palavra-passe e tente iniciar sessão. (Não tem de fornecer a senha correta.)

   Um pedido pede-lhe para salvar os campos de inscrição capturados.
6. Selecione **OK**. A extensão do navegador atualiza a página de estado de captura com a mensagem **Metadados foi atualizado para a aplicação**. O separador de navegador fecha.

7. Na página de inscrição do Azure AD **Configure,** selecione **Ok, consegui iniciar sessão na aplicação com sucesso**.

8. Selecione **OK**.

Após a captura da página de entrada, pode atribuir utilizadores e grupos, e pode configurar políticas credenciais tal como [aplicações regulares de siso](what-is-single-sign-on.md)de senha .

> [!NOTE]
> Pode fazer o upload de um logótipo de azulejos para a aplicação utilizando o botão **'Logotipo upload'** no separador **Configurar** para a aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir utilizadores ou grupos à aplicação](methods-for-assigning-users-and-groups.md)
- [Configurar o fornecimento automático de conta de utilizador](../app-provisioning/configure-automatic-user-provisioning-portal.md)
