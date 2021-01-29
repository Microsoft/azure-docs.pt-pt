---
title: Compreenda o sign-on único baseado em palavra-passe (SSO) para aplicações no Azure Ative Directory
description: Compreenda o sign-on único baseado em palavra-passe (SSO) para aplicações no Azure Ative Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: 2bb190ea01098e8e7b21a00f54fc37cd817f3743
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053284"
---
# <a name="understand-password-based-single-sign-on"></a>Compreender o sign-on único baseado em palavra-passe

Na [série quickstart](view-applications-portal.md) sobre gestão de aplicações, aprendeu a usar a Azure AD como Fornecedor de Identidade (IdP) para uma aplicação. No guia quickstart, configura sSO baseado em SAML ou OIDC. Outra opção é sSO baseado em palavra-passe. Este artigo entra em mais detalhes sobre a opção SSO baseada em palavra-passe. 

Esta opção está disponível para qualquer website com uma página de início de sção HTML. SSO baseado em palavra-passe também é conhecido como abóbada de senha. O SSO baseado em palavras-passe permite-lhe gerir o acesso ao utilizador e palavras-passe a aplicações web que não suportam a federação de identidade. Também é útil onde vários utilizadores precisam de partilhar uma única conta, como as contas de aplicações de redes sociais da sua organização.

O SSO baseado em palavras-passe é uma ótima maneira de começar a integrar as aplicações em AZure AD rapidamente, e permite-lhe:

- Ativar um único s-on para os seus utilizadores armazenando e reproduzindo nomes de utilizador e palavras-passe

- Aplicações de suporte que requerem vários campos de entrada para aplicações que requerem mais do que apenas o nome de utilizador e os campos de palavra-passe para iniciar seduções

- Personalize as etiquetas do nome de utilizador e dos campos de palavra-passe que os seus utilizadores vêem nas [Minhas Apps](../user-help/my-apps-portal-end-user-access.md) quando introduzem as suas credenciais

- Permita que os seus utilizadores forneçam os seus próprios nomes de utilizador e palavras-passe para quaisquer contas de aplicação existentes que estejam a digitar manualmente.

- Permitir que um membro do grupo de negócios especifique os nomes de utilizador e palavras-passe atribuídos a um utilizador utilizando a funcionalidade de Acesso à [Aplicação de Self-Service](./manage-self-service-access.md)

-   Permitir que um administrador especifique um nome de utilizador e uma palavra-passe para ser usado por indivíduos ou grupos quando iniciar a sua sção na aplicação com a funcionalidade 'Credenciais de Atualização' 

## <a name="before-you-begin"></a>Antes de começar

A utilização do Azure AD como seu Fornecedor de Identidade (IdP) e configurar um único sign-on (SSO) pode ser simples ou complexo dependendo da aplicação que está a ser utilizada. Algumas aplicações podem ser configuradas com apenas algumas ações. Outros requerem uma configuração aprofundada. Para aumentar rapidamente o conhecimento, caminhe pela [série quickstart](view-applications-portal.md) sobre gestão de aplicações. Se a aplicação que está a adicionar é simples, então provavelmente não precisa ler este artigo. Se a aplicação que está a adicionar necessitar de configuração personalizada e precisar de utilizar SSO baseado em palavra-passe, então este artigo é para si.

> [!IMPORTANT] 
> Existem alguns cenários em que a opção **de inscrição única** não estará na navegação para uma aplicação em **aplicações da Enterprise.** 
>
> Se a aplicação foi registada usando **registos de Aplicações,** então a capacidade de inscrição única é configurada para utilizar o OIDC OAuth por padrão. Neste caso, a opção **de inscrição única** não será apresentada na navegação ao abrigo das **aplicações da Enterprise.** Quando utiliza **as inscrições da App** para adicionar a sua aplicação personalizada, configura as opções no ficheiro manifesto. Para saber mais sobre o ficheiro manifesto, consulte o [manifesto da aplicação Azure Ative Directory](../develop/reference-app-manifest.md). Para saber mais sobre os padrões SSO, consulte [autenticação e autorização utilizando a plataforma de identidade da Microsoft.](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Outros cenários em que o único sinal de sessão estará ausente da navegação incluem quando uma aplicação é hospedada noutro inquilino ou se a sua conta não tiver as permissões necessárias (Administrador Global, Administrador de Aplicação **cloud,** Administrador de Aplicação ou proprietário do principal de serviço). As permissões também podem causar um cenário em que pode abrir **o single de sposição,** mas não será capaz de economizar. Para saber mais sobre as funções administrativas da Azure AD, consulte https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-configuration"></a>Configuração básica

Na [série quickstart](view-applications-portal.md), aprendeu a adicionar uma app ao seu inquilino, o que permite ao Azure AD saber que está a ser usado como Fornecedor de Identidade (IdP) para a aplicação. Algumas aplicações já estão pré-configuradas e mostram na galeria AZure AD. Outras aplicações não estão na galeria e você tem que criar uma app genérica e configugá-la manualmente. Dependendo da aplicação, a opção SSO baseada em palavra-passe pode não estar disponível. Se não vir a lista de opções baseada em Palavra-Passe na página de s registação única da aplicação, então não está disponível.

> [!IMPORTANT]
> A extensão do navegador My Apps é necessária para SSO baseado em palavras-passe. Para saber mais, consulte [a implementação do Plano a Minhas Aplicações.](my-apps-deployment-plan.md)

A página de configuração para SSO baseado em palavra-passe é simples. Inclui apenas o URL da página de inscrição que a aplicação utiliza. Esta cadeia deve ser a página que inclui o campo de entrada do nome de utilizador.

Depois de introduzir o URL, **selecione Guardar**. A Azure AD analisa o HTML da página de entrada de inscrição para o nome de utilizador e os campos de entrada de palavra-passe. Se a tentativa for bem sucedida, está feito.
 
O seu próximo passo é [atribuir utilizadores ou grupos à aplicação.](./assign-user-or-group-access-portal.md) Depois de ter atribuído utilizadores e grupos, pode fornecer credenciais para ser usado para um utilizador quando iniciar o sessão na aplicação. Selecione **Utilizadores e grupos**, selecione a caixa de verificação para a linha do utilizador ou grupo e, em seguida, selecione **''' ' 'Update Credentials'.** Por fim, introduza o nome de utilizador e a palavra-passe a utilizar para o utilizador ou grupo. Caso não o faça, os utilizadores serão solicitados a introduzir as credenciais no lançamento.
 

## <a name="manual-configuration"></a>Configuração manual

Se a tentativa de análise do AD do Azure falhar, pode configurar a inscrição manualmente.

1. Na **\<application name> Configuração**, selecione **\<application name> Configurações únicas de sinalização de senha** para visualizar a página de **inscrição de configuração.** 

2. Selecione **os campos de inscrição de deteção manual**. Instruções adicionais que descrevem a deteção manual dos campos de inscrição aparecem.

   ![Configuração manual do único sinal baseado em palavra-passe](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Selecione **campos de inscrição de captura**. Uma página de estado de captura abre num novo separador, mostrando que a **captura de metadados** de mensagem está atualmente em andamento .

4. Se a caixa **de extensão de aplicações minhas** aparecer num novo separador, selecione **Instal Now** para instalar a extensão do navegador **Deresso de Sudeste De aplicações Seguras.** (A extensão do navegador requer Microsoft Edge, Chrome ou Firefox.) Em seguida, instale, lance e ative a extensão e refresque a página do estado de captura.

   A extensão do navegador abre então outro separador que exibe o URL introduzido.
5. No separador com o URL introduzido, passe pelo processo de inscrição. Preencha o nome de utilizador e os campos de senha e tente iniciar seduca. (Não é preciso fornecer a senha correta.)

   Um aviso pede-lhe para salvar os campos de inscrição capturados.
6. Selecione **OK**. A extensão do navegador atualiza a página de estado de captura com a mensagem **Metadados foi atualizado para a aplicação**. O separador do navegador fecha.

7. Na página de configuração AD AD **Azure,** selecione **Ok, consegui iniciar sação na aplicação com sucesso.**

8. Selecione **OK**.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir utilizadores ou grupos à aplicação](./assign-user-or-group-access-portal.md)
- [Configurar o provisionamento automático da conta de utilizador](../app-provisioning/configure-automatic-user-provisioning-portal.md)
