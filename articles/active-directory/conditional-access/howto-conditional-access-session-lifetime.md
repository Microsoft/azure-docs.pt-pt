---
title: Configurar gestão de sessão de autenticação - Azure Ative Directory
description: Personalize a configuração da sessão de autenticação AZure AD, incluindo a frequência de sessão de sessão de inscrição do utilizador e a persistência da sessão de navegador.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/23/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6deb0c005b5ed7daf5b30bea8a65ee70d8b460c5
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837469"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Configurar a gestão de sessões de autenticação com o Acesso Condicional

Em implementações complexas, as organizações podem ter a necessidade de restringir as sessões de autenticação. Alguns cenários podem incluir:

* Acesso a recursos a partir de um dispositivo não gerido ou partilhado
* Acesso a informação sensível a partir de uma rede externa
* Utilizadores de alto impacto
* Aplicações empresariais críticas

Os controlos de Acesso Condicional permitem-lhe criar políticas que direcionem casos específicos de utilização dentro da sua organização sem afetar todos os utilizadores.

Antes de mergulhar em detalhes sobre como configurar a política, vamos examinar a configuração padrão.

## <a name="user-sign-in-frequency"></a>Frequência de inscrição do utilizador

A frequência de inscrição define o período de tempo antes de um utilizador ser convidado a iniciar novamente o seu sing quando tenta aceder a um recurso.

A configuração padrão do Azure Ative Directory (Azure AD) para a frequência de inscrição do utilizador é uma janela rolante de 90 dias. Pedir credenciais aos utilizadores muitas vezes parece uma coisa sensata a fazer, mas pode dar a volta por cima: os utilizadores que são treinados para introduzir as suas credenciais sem pensar podem fornecer-lhes involuntariamente uma solicitação de credencial maliciosa.

Pode parecer alarmante não pedir a um utilizador que volte a entrar, na realidade qualquer violação das políticas de TI irá revogar a sessão. Alguns exemplos incluem (mas não se limitam a) uma alteração de senha, um dispositivo incompatível ou desativação de conta. Também pode revogar explicitamente [as sessões dos utilizadores utilizando o PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true). A configuração padrão AD AD do Azure resume-se a "não pedir aos utilizadores que forneçam as suas credenciais se a postura de segurança das suas sessões não tiver mudado".

A definição de frequência de início funciona com aplicações que implementaram protocolos OAUTH2 ou OIDC de acordo com as normas. A maioria das aplicações nativas da Microsoft para Windows, Mac e Mobile, incluindo as seguintes aplicações web, cumprem a definição.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portal microsoft 365 Admin
- Exchange Online
- SharePoint e OneDrive
- Teams cliente web
- Dynamics CRM Online
- Portal do Azure

A definição de frequência de inscrição também funciona com aplicações SAML, desde que não larguem os seus próprios cookies e sejam redirecionados de volta para Azure AD para autenticação regularmente.

### <a name="user-sign-in-frequency-and-multi-factor-authentication"></a>Frequência de inscrição do utilizador e autenticação de vários fatores

Frequência de entrada anteriormente aplicada apenas à autenticação do primeiro fator em dispositivos que estavam aderidos a Azure AD, a AD Híbrida Azure juntou-se e a Azure AD registada. Não havia uma forma fácil de os nossos clientes reefiarem a autenticação de vários fatores (MFA) nesses dispositivos. Com base no feedback do cliente, a frequência de inscrição também se aplicará a MFA.

[![Assine na frequência e MFA](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart-small.png)](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart.png#lightbox)

### <a name="user-sign-in-frequency-and-device-identities"></a>Frequência de inscrição do utilizador e identidades do dispositivo

Se tiver a Azure AD aderido, a Azure AD híbrida junta-se, ou dispositivos registados AZURE AD, quando um utilizador desbloqueia o seu dispositivo ou sinais interativamente, este evento também irá satisfazer a política de frequência de inscrição. Nos dois exemplos de frequência de inscrição do utilizador é definida para 1 hora:

Exemplo 1:

- Às 00:00, um utilizador assina o seu dispositivo AD Azure Windows 10 e começa a trabalhar num documento armazenado no SharePoint Online.
- O utilizador continua a trabalhar no mesmo documento no seu dispositivo durante uma hora.
- Às 01:00, o utilizador é solicitado a iniciar novamente a sedência com base no requisito de frequência de inscrição na política de Acesso Condicional configurado pelo seu administrador.

Exemplo 2:

- Às 00:00, um utilizador assina o seu dispositivo AD Azure Windows 10 e começa a trabalhar num documento armazenado no SharePoint Online.
- Às 00:30, o utilizador levanta-se e faz uma pausa para bloquear o dispositivo.
- Às 00:45, o utilizador regressa da pausa e desbloqueia o dispositivo.
- Às 01:45, o utilizador é solicitado a iniciar novamente a sedência com base no requisito de frequência de inscrição na política de Acesso Condicional configurado pelo seu administrador desde que a última inscrição aconteceu às 00:45.

## <a name="persistence-of-browsing-sessions"></a>Persistência de sessões de navegação

Uma sessão de navegador persistente permite que os utilizadores permaneçam assinados após fechar e reabrir a janela do navegador.

O padrão AD AD AZure para persistência da sessão de navegador permite que os utilizadores em dispositivos pessoais escolham se persistem a sessão mostrando um "Stay signed in?" pronta após a autenticação bem sucedida. Se a persistência do navegador estiver configurada em AD FS utilizando a orientação no artigo [AD FS Single Sign-On Definições,](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)cumpriremos essa política e persistiremos também na sessão AD AD do Azure. Também pode configurar se os utilizadores do seu inquilino vêem o "Stay signed in?" pronta alterando a definição adequada no painel de marca da empresa no portal Azure utilizando a orientação no artigo [Personalize a sua página de inserção AZure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Configurar controlos de sessão de autenticação

O Acesso Condicional é uma capacidade AZure AD Premium e requer uma licença premium. Se quiser saber mais sobre o Acesso Condicional, veja [o que é o Acesso Condicional no Diretório Ativo Azure?](overview.md#license-requirements)

> [!WARNING]
> Se estiver a utilizar a funcionalidade [de vida útil de token configurável](../develop/active-directory-configurable-token-lifetimes.md) atualmente em visualização pública, por favor, note que não suportamos a criação de duas políticas diferentes para a mesma combinação de utilizador ou app: uma com esta funcionalidade e outra com uma funcionalidade de vida simbólica. A Microsoft planeia retirar a funcionalidade de vida útil de token configurável para a atualização e a sessão de token lifes a 30 de janeiro de 2021 e substituí-la pela funcionalidade de gestão da sessão de autenticação de acesso condicional.  
>
> Antes de ativar a frequência de inscrição, certifique-se de que outras definições de reauthenciacation estão desativadas no seu inquilino. Se estiver ativado "Remember MFA em dispositivos fidedignos", certifique-se de desativá-lo antes de utilizar a frequência de início de sposição, uma vez que utilizar estas duas definições em conjunto pode levar a que os utilizadores sejam inesperadamente informados. Para saber mais sobre as solicitações de reauestatura e a vida útil da sessão, consulte o artigo, [otimize as solicitações de reauestação e compreenda a vida útil da sessão para autenticação multi-factor Azure AD](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

### <a name="policy-1-sign-in-frequency-control"></a>Política 1: Controlo de frequência de inscrição

1. Criar nova política
1. Escolha todas as condições necessárias para o ambiente do cliente, incluindo as aplicações de nuvem-alvo.

   > [!NOTE]
   > Recomenda-se definir a frequência de pedido de autenticação igual para as principais aplicações do Microsoft Office, como o Exchange Online e o SharePoint Online, para melhor experiência do utilizador.

1. Aceda à sessão **de controlos** de  >  **acesso** e clique na frequência de **inscrição**
1. Insira o valor exigido de dias e horas na primeira caixa de texto
1. Selecione um valor de **Horas** ou **Dias** a partir do dropdown
1. Salve a sua política

![Política de acesso condicional configurada para a frequência de inscrição](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

No Azure AD os dispositivos Windows registados no computador são considerados uma solicitação. Por exemplo, se tiver configurado a frequência de inscrição para 24 horas para aplicações do Office, os utilizadores em dispositivos Windows registados em Azure irão satisfazer a política de frequência de inscrição ao iniciar a sessão no dispositivo e não serão novamente solicitados ao abrir aplicações do Office.

### <a name="policy-2-persistent-browser-session"></a>Política 2: Sessão de navegador persistente

1. Criar nova política
1. Escolha todas as condições necessárias.

   > [!NOTE]
   > Por favor, note que este controlo requer escolher "Todas as Aplicações cloud" como uma condição. A persistência da sessão do navegador é controlada por sinal de sessão de autenticação. Todos os separadores de uma sessão de navegador partilham um token de uma única sessão e, portanto, todos devem partilhar o estado de persistência.

1. Vá à Sessão **de Controlos de**  >  **Acesso** e clique **na sessão de navegador persistente**
1. Selecione um valor a partir do dropdown
1. Salvar-lhe a política

![Política de acesso condicional configurada para navegador persistente](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> A configuração persistente da Sessão de Navegador no Acesso Condicionado AD Azure substituirá o "Permanência assinada?" definição no painel de marca da empresa no portal Azure para o mesmo utilizador se tiver configurado ambas as políticas.

## <a name="validation"></a>Validação

Utilize a ferramenta What-If para simular um login do utilizador para a aplicação alvo e outras condições com base na configuração da sua política. Os controlos de gestão da sessão de autenticação aparecem no resultado da ferramenta.

![Acesso condicional E se os resultados da ferramenta](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Implementação da Política

Para garantir que a sua política funciona como esperado, a melhor prática recomendada é testá-la antes de a lançar para a produção. Idealmente, use um inquilino de teste para verificar se a sua nova apólice funciona como pretendido. Para mais informações, consulte o artigo [Planear uma implementação de Acesso Condicional.](plan-conditional-access.md)

## <a name="next-steps"></a>Passos seguintes

* Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, consulte o artigo [Planeie uma implementação de Acesso Condicional](plan-conditional-access.md).