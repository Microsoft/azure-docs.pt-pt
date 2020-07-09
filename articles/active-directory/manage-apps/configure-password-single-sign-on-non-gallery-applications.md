---
title: Como configurar o sign-on único da palavra-passe para aplicações AD AZure / Microsoft Docs
description: Como configurar o sign-on único da palavra-passe (SSO) para as suas aplicações empresariais AZure AD na plataforma de identidade microsoft (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/10/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 043adc309c3480865eb9aa7a7bff8d35e85bc78a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763504"
---
# <a name="configure-password-single-sign-on"></a>Configurar o sign-on único da palavra-passe

Quando [adiciona uma aplicação](add-gallery-app.md) de galeria ou uma [aplicação web não-galeria](add-non-gallery-app.md) às suas Aplicações empresariais AZure AD, uma das opções únicas de inscrição disponível para si é o [único sinal de assinatura baseado em palavra-passe.](what-is-single-sign-on.md#password-based-sso) Esta opção está disponível para qualquer web com uma página de início de sção HTML. O SSO baseado em palavras-passe, também referido como cofre de palavras-passe, permite-lhe gerir o acesso ao utilizador e palavras-passe a aplicações web que não suportam a federação de identidade. Também é útil para cenários em que vários utilizadores precisam de partilhar uma única conta, como é o caso das contas de aplicações de redes sociais da sua organização. 

O SSO baseado em palavras-passe é uma ótima maneira de começar a integrar as aplicações em AZure AD rapidamente, e permite-lhe:

-   Ativar o **Único S-On para os seus utilizadores** armazenando e reproduzindo de forma segura nomes de utilizadores e palavras-passe para a aplicação que integrou com Azure AD

-   **Aplicações de suporte que requerem vários campos de entrada** para aplicações que requerem mais do que apenas o nome de utilizador e os campos de palavra-passe para iniciar seduções

-   **Personalize as etiquetas** dos campos de entrada de nome de utilizador e palavra-passe que os seus utilizadores vêem no Painel de Acesso à [Aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando introduzem as suas credenciais

-   Permita que os seus **utilizadores** forneçam os seus próprios nomes de utilizador e palavras-passe para quaisquer contas de aplicação existentes que estejam a digitar manualmente no Painel de Acesso à [Aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Permitir que um **membro do grupo de negócios** especifique os nomes de utilizador e palavras-passe atribuídos a um utilizador utilizando a funcionalidade de Acesso à [Aplicação de Self-Service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Permitir que um **administrador** especifique um nome de utilizador e uma palavra-passe para ser usado por indivíduos ou grupos ao iniciar a sessão na aplicação utilizando a funcionalidade 'Credenciais de Atualização' 

## <a name="before-you-begin"></a>Before you begin

Se a aplicação não tiver sido adicionada ao seu inquilino Azure AD, consulte [Adicionar uma aplicação](add-gallery-app.md) de galeria ou [adicionar uma aplicação não-galeria](add-non-gallery-app.md).

## <a name="open-the-app-and-select-password-single-sign-on"></a>Abra a aplicação e selecione a palavra-passe de um único sinal

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador de aplicações em nuvem ou como administrador de aplicação para o seu inquilino AZure AD.

2. Navegue para aplicações **da Azure Ative Directory**  >  **Enterprise**. Aparece uma amostra aleatória das aplicações no seu inquilino AZure AD. 

3. No menu **'Tipo de Aplicação',** selecione **Todas as aplicações**e, em seguida, selecione **Aplicar**.

4. Introduza o nome da aplicação na caixa de pesquisa e, em seguida, selecione a aplicação a partir dos resultados.

5. Na secção **'Gerir',** selecione **'Único's sign-on'**. 

6. Selecione **password-baseada**.

7. Insira o URL da página de entrada web da aplicação. Esta cadeia deve ser a página que inclui o campo de entrada do nome de utilizador.

   ![S-base único baseado em palavra-passe](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Selecione **Guardar**. O Azure AD tenta analisar a página de entrada de inscrição para uma entrada de nome de utilizador e uma entrada de senha. Se a tentativa for bem sucedida, está feito. 
 
> [!NOTE]
> O seu próximo passo é [atribuir utilizadores ou grupos à aplicação.](methods-for-assigning-users-and-groups.md) Depois de ter atribuído utilizadores e grupos, pode fornecer credenciais para serem usadas em nome de um utilizador quando iniciarem sessão na aplicação. Selecione **Utilizadores e grupos**, selecione a caixa de verificação para a linha do utilizador ou grupo e, em seguida, clique em **''''Update Credentials' (')** Em seguida, introduza o nome de utilizador e a palavra-passe a utilizar em nome do utilizador ou do grupo. Caso contrário, os utilizadores serão solicitados a introduzir as credenciais por si mesmos no lançamento.
 

## <a name="manual-configuration"></a>Configuração manual

Se a tentativa de análise do AD do Azure falhar, pode configurar a inscrição manualmente.

1. Na ** \<application name> Configuração**, selecione ** \<application name> Configurações únicas de sinalização de senha** para visualizar a página de **inscrição de configuração.** 

2. Selecione **os campos de inscrição de deteção manual**. Instruções adicionais que descrevem a deteção manual dos campos de inscrição aparecem.

   ![Configuração manual do único sinal baseado em palavra-passe](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Selecione **campos de inscrição de captura**. Uma página de estado de captura abre num novo separador, mostrando que a **captura de metadados**de mensagem está atualmente em andamento .

4. Se a caixa **de extensão do painel de acesso aparecer** num novo separador, selecione Instal **Now** para instalar a extensão do navegador de extensão de acesso seguro das **minhas aplicações.** (A extensão do navegador requer Microsoft Edge, Chrome ou Firefox.) Em seguida, instale, lance e ative a extensão e refresque a página do estado de captura.

   A extensão do navegador abre então outro separador que exibe o URL introduzido.
5. No separador com o URL introduzido, passe pelo processo de inscrição. Preencha o nome de utilizador e os campos de senha e tente iniciar seduca. (Não é preciso fornecer a senha correta.)

   Um aviso pede-lhe para salvar os campos de inscrição capturados.
6. Selecione **OK**. A extensão do navegador atualiza a página de estado de captura com a mensagem **Metadados foi atualizado para a aplicação**. O separador do navegador fecha.

7. Na página de configuração AD AD **Azure,** selecione **Ok, consegui iniciar sação na aplicação com sucesso.**

8. Selecione **OK**.

Após a captura da página de entrada, pode atribuir utilizadores e grupos, e pode configurar políticas de credenciais tal como [aplicações SSO de palavra-passe](what-is-single-sign-on.md)regular .

> [!NOTE]
> Pode carregar um logotipo de azulejo para a aplicação utilizando o botão **upload logo** no **separador Configurar** para a aplicação.

## <a name="next-steps"></a>Próximos passos

- [Atribuir utilizadores ou grupos à aplicação](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático da conta de utilizador](../app-provisioning/configure-automatic-user-provisioning-portal.md)
