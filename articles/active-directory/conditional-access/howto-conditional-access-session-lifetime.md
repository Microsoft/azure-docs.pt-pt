---
title: Gestão de sessão de autenticação configurada - Diretório Ativo Azure
description: Personalize a configuração da sessão de autenticação do Azure AD, incluindo o sinal do utilizador na frequência e persistência da sessão do navegador.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9c0c88064c00c97de7dc58a500910e81c04eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263286"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Configure gestão de sessão de autenticação com Acesso Condicional

Em implementações complexas, as organizações podem ter a necessidade de restringir as sessões de autenticação. Alguns cenários podem incluir:

* Acesso a recursos a partir de um dispositivo não gerido ou partilhado
* Acesso a informação sensível a partir de uma rede externa
* Utilizadores de alto impacto
* Aplicações comerciais críticas

Os controlos de Acesso Condicional permitem-lhe criar políticas que visam casos específicos de utilização dentro da sua organização sem afetar todos os utilizadores.

Antes de mergulhar em detalhes sobre como configurar a política, vamos examinar a configuração padrão.

## <a name="user-sign-in-frequency"></a>Frequência de inscrição do utilizador

A frequência de inatividade define o período de tempo antes de um utilizador ser convidado a iniciar o seu sessão quando tentar aceder a um recurso.

A configuração predefinida do Diretório Ativo Azure (Azure AD) para o sinal de utilizador na frequência é uma janela rolante de 90 dias. Pedir credenciais aos utilizadores muitas vezes parece uma coisa sensata a fazer, mas pode dar a volta: os utilizadores que são treinados para introduzir as suas credenciais sem pensar podem fornecer-lhes involuntariamente um pedido de credencial malicioso.

Pode parecer alarmante não pedir a um utilizador que volte a entrar, na realidade qualquer violação das políticas de TI irá revogar a sessão. Alguns exemplos incluem (mas não se limitam a) uma alteração de senha, um dispositivo incompatível ou desativação de conta. Também pode revogar explicitamente [as sessões dos utilizadores utilizando o PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). A configuração padrão da AD Azure resume-se a "não pedir aos utilizadores que forneçam as suas credenciais se a postura de segurança das suas sessões não tiver mudado".

A definição de frequência sessão funciona com aplicações que implementaram protocolos OAUTH2 ou OIDC de acordo com as normas. A maioria das aplicações nativas da Microsoft para Windows, Mac e Mobile, incluindo as seguintes aplicações web, cumprem a definição.

- Palavra, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portal de administração O365
- Exchange Online
- SharePoint e OneDrive
- Clientes web de equipas
- Dynamics CRM Online
- Portal do Azure

### <a name="user-sign-in-frequency-and-device-identities"></a>Frequência de inscrição do utilizador e identidades do dispositivo

Se tiver a AD Azure unida, a AD Azure híbrida, ou dispositivos registados em Azure AD, quando um utilizador desbloquear o seu dispositivo ou sinais interactivamente, este evento também irá satisfazer o sinal na política de frequências. Nos seguintes 2 exemplos, a frequência de início de sessão do utilizador está definida para 1 hora:

Exemplo 1:

- Às 00:00, um utilizador inicia o check-in no seu dispositivo de ad ad do Windows 10 Azure e começa a trabalhar num documento armazenado no SharePoint Online.
- O utilizador continua a trabalhar no mesmo documento no seu dispositivo durante uma hora.
- Às 01:00, o utilizador é solicitado a iniciar o seu início com base no requisito de frequência de início de sessão na política de Acesso Condicional configurada pelo seu administrador.

Exemplo 2:

- Às 00:00, um utilizador inicia o check-in no seu dispositivo de ad ad do Windows 10 Azure e começa a trabalhar num documento armazenado no SharePoint Online.
- Às 00:30, o utilizador levanta-se e faz uma pausa a bloquear o dispositivo.
- Às 00:45, o utilizador regressa da pausa e desbloqueia o dispositivo.
- Às 01:45, o utilizador é solicitado a iniciar o seu início com base no requisito de frequência de início de sessão na política de Acesso Condicional configurada pelo seu administrador desde que o último sinal de acesso aconteceu às 00:45.

## <a name="persistence-of-browsing-sessions"></a>Persistência de sessões de navegação

Uma sessão de navegador persistente permite que os utilizadores permaneçam inscritos após o fecho e reabertura da janela do navegador.

O padrão da AD Azure para a persistência da sessão de navegador permite que os utilizadores de dispositivos pessoais escolham se persistem a sessão mostrando um "Stay signed in?" pronta após autenticação bem sucedida. Se a persistência do navegador estiver configurada em AD FS utilizando a orientação no artigo [AD FS Single Sign-On Settings,](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)vamos cumprir essa política e persistir também a sessão de AD Azure. Também pode configurar se os utilizadores do seu inquilino vêem o "Stay in?" solicitar alterando a definição adequada no painel de marca da empresa no portal Azure utilizando a orientação no artigo Personalize a sua página de entrada de [Anúncio sacana](../fundamentals/customize-branding.md)do Azure .

## <a name="configuring-authentication-session-controls"></a>Configurar os controlos da sessão de autenticação

O Acesso Condicional é uma capacidade Azure AD Premium e requer uma licença premium. Se quiser saber mais sobre o Acesso Condicional, veja [o que é o Acesso Condicional no Diretório Ativo Azure?](overview.md#license-requirements)

> [!WARNING]
> Se estiver a utilizar a funcionalidade de [vida token configurável](../develop/active-directory-configurable-token-lifetimes.md) atualmente em pré-visualização pública, por favor note que não suportamos criar duas políticas diferentes para a mesma combinação de utilizador ou app: uma com esta funcionalidade e outra com funcionalidade de vida token configurável. A Microsoft planeia retirar a funcionalidade de vida token configurável no dia 1 de maio de 2020 e substituí-la pela funcionalidade de gestão da sessão de autenticação De acesso Condicional.  

### <a name="policy-1-sign-in-frequency-control"></a>Política 1: Controlo de frequências de intenção

1. Criar nova política
1. Escolha todas as condições necessárias para o ambiente do cliente, incluindo as aplicações de nuvem alvo.

   > [!NOTE]
   > Recomenda-se definir a mesma frequência de autenticação para as principais aplicações do Microsoft Office, como o Exchange Online e o SharePoint Online, para uma melhor experiência do utilizador.

1. Vá à**Sessão** de **Controlos** > de Acesso e clique **na frequência de iniciar sessão**
1. Insira o valor exigido de dias e horas na primeira caixa de texto
1. Selecione um valor de **Horas** ou **Dias** a partir do dropdown
1. Salve a sua política

![Política de Acesso Condicional configurada para sinal de frequência](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

No Azure AD, os dispositivos Windows registados no dispositivo são considerados um pedido de solicitação. Por exemplo, se tiver configurado o Sign o 'Sign' em frequência até 24 horas para aplicações do Office, os utilizadores de dispositivos Windows registados em AD Azure irão satisfazer a política de frequência satisfar o Signinin in, insindo no dispositivo e não serão solicitados novamente na abertura das aplicações do Office.

Se configurar diferentes frequências de acesso para diferentes aplicações web que estão a decorrer na mesma sessão de navegador, a política mais rigorosa será aplicada a ambas as aplicações porque todas as aplicações que executam na mesma sessão de navegador partilham uma única sessão.

### <a name="policy-2-persistent-browser-session"></a>Política 2: Sessão persistente do navegador

1. Criar nova política
1. Escolha todas as condições necessárias.

   > [!NOTE]
   > Por favor, note que este controlo requer escolher "Todas as Aplicações cloud" como condição. A persistência da sessão de navegador é controlada por token de autenticação. Todos os separadores de uma sessão de navegador partilham um único token de sessão e, portanto, todos eles devem partilhar o estado de persistência.

1. Vá à**Sessão** de **Controlos** > de Acesso e clique na **sessão persistente do navegador**
1. Selecione um valor a partir de dropdown
1. Salvar-lhe a política

![Política de Acesso Condicional configurada para navegador persistente](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> A configuração persistente da Sessão de Navegador no Acesso Condicional Azure AD irá substituir o "Stay signed in?" definição no painel de marca da empresa no portal Azure para o mesmo utilizador se tiver configurado ambas as políticas.

## <a name="validation"></a>Validação

Utilize a ferramenta What-If para simular um login do utilizador para a aplicação-alvo e outras condições com base na configuração da sua apólice. Os controlos de gestão da sessão de autenticação aparecem no resultado da ferramenta.

![Acesso Condicional O que se a ferramenta resultar](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Implementação da Política

Para garantir que a sua política funcione como esperado, a melhor prática recomendada é testá-la antes de a lançar em produção. Idealmente, use um inquilino de teste para verificar se a sua nova política funciona como pretendido. Para mais informações, consulte o artigo [As melhores práticas de acesso condicional no Diretório Ativo Azure.](best-practices.md)

## <a name="next-steps"></a>Passos seguintes

* Se quiser saber configurar uma política de Acesso Condicional, consulte o artigo [Requer MFA para aplicações específicas com acesso condicional ao Diretório Ativo Azure](app-based-mfa.md).
* Se estiver pronto para configurar as políticas de Acesso Condicional para o seu ambiente, consulte o artigo [As melhores práticas de acesso condicional no Diretório Ativo Azure](best-practices.md).
