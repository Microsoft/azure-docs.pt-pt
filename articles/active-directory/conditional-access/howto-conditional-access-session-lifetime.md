---
title: Configurar a gestão de sessão de autenticação com o Azure Active Directory condicional acesso
description: Personalize a configuração de sessão de autenticação do Azure AD incluindo o início de sessão do utilizador na frequência e o navegador persistência da sessão.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e15cf9b2e10a581c72a5035b52be47c3e2c9dfda
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112331"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Configurar a gestão de sessão de autenticação com o acesso condicional

Nas implementações complexas, as organizações podem ter uma necessidade para restringir as sessões de autenticação. Alguns cenários poderão incluir:

* Acesso a recursos de um dispositivo partilhado ou não gerido
* Acesso a informações confidenciais a partir de uma rede externa
* Utilizadores de elevado impacto
* Aplicações empresariais

Controlos de acesso condicionais permitem-lhe criar políticas que se destinam a casos de utilização específicos na sua organização sem afetar todos os utilizadores.

Antes de explorar detalhes sobre como configurar a política, vamos examinar a configuração predefinida.

## <a name="user-sign-in-frequency"></a>Frequência de início de sessão de utilizador

Frequência de início de sessão define o período de tempo antes de um utilizador é-lhe pedido para iniciar sessão novamente durante a tentativa de aceder a um recurso.

A configuração do Azure Active Directory (Azure AD) para início de sessão do utilizador em frequência é uma janela sem interrupção de 90 dias. Solicitar credenciais ao usuário, muitas vezes, parece que algo sensato para o fazer, mas pode backfire: os utilizadores que estão treinados para introduzir as respetivas credenciais sem pensar podem inadvertidamente fornecem-las para uma solicitação de credenciais malicioso.

Pode parecer alarmante não pedir para um utilizador inicie sessão novamente durante 90 dias, na realidade, que qualquer violação de diretivas de TI irá revogar a sessão. Alguns exemplos incluem (mas não se limitam a) uma alteração de palavra-passe, um dispositivo de incompatibilidade ou conta desativar. Pode também explicitamente [revogar sessões dos utilizadores com o PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). A configuração predefinida do Azure AD se resume "não pedir aos utilizadores para fornecerem as respetivas credenciais se não tiver sido alterado a postura de segurança de suas sessões".

Frequência de início de sessão definição funciona com aplicações que implementaram OATH2 ou OIDC protocolos, de acordo com os padrões. A maioria das aplicações nativas da Microsoft para Windows, Mac e dispositivos móveis está em conformidade com a definição.

## <a name="persistence-of-browsing-sessions"></a>Persistência de sessões de navegação

Uma sessão do browser persistente permite aos utilizadores manter a sessão iniciada depois de fechar e reabrir a sua janela do browser.

A predefinição do Azure AD para a persistência da sessão de browser permite aos utilizadores nos dispositivos pessoais para escolher se pretende manter a sessão, mostrando um "Mantenha-se com sessão iniciada?" pedir após a autenticação com êxito. Se a persistência do navegador está configurada no AD FS através da orientação no artigo [único início de sessão nas definições do AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), iremos está em conformidade com essa política e manter o Azure também a sessão do AD. Também pode configurar se os utilizadores no seu inquilino consulte o "Mantenha-se com sessão iniciada?" linha de comandos, alterando a definição adequada da empresa, imagem corporativa do painel no portal do Azure através da orientação no artigo [personalizar a página de início de sessão do Azure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Configurar os controlos de sessão de autenticação

Acesso condicional é um recurso do Azure AD Premium e requer uma licença premium. Se gostaria de saber mais sobre o acesso condicional, consulte o artigo [o que é o acesso condicional no Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Se estiver a utilizar o [duração do token configurável](../develop/active-directory-configurable-token-lifetimes.md) funcionalidade atualmente em pré-visualização pública, tenha em atenção que não é suportado criar duas políticas diferentes para a mesma combinação de utilizador ou aplicação: um com esta funcionalidade e outro com funcionalidade de duração do token configuráveis. A Microsoft planeja extinguir a funcionalidade de duração do token configurável em 1º de Novembro e substituí-lo com a funcionalidade de gestão de sessão de autenticação de acesso condicional.  

### <a name="policy-1-sign-in-frequency-control"></a>Política de 1: Frequência de início de sessão de controlo

1. Criar nova política
1. Escolha as condições de todas as necessárias para o ambiente do cliente, incluindo as aplicações de cloud de destino.

   > [!NOTE]
   > Recomenda-se para definir a frequência de prompt de autenticação igual para principais aplicações do Microsoft Office, como o Exchange Online e SharePoint Online para melhor experiência de utilizador.

1. Aceda a **controlos de acesso** > **sessão** e clique em **frequência de início de sessão**
1. Introduza o valor necessário de dias e horas na primeira caixa de texto
1. Selecione um valor de **horas** ou **dias** no menu pendente
1. Guardar a política

![Política de acesso condicional configurada para início de sessão na frequência](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

No Azure AD registado Windows dispositivos início de sessão para o dispositivo é considerado uma linha de comandos. Por exemplo, se tiver configurado o início de sessão na frequência de 24 horas para aplicações do Office, os utilizadores no Azure AD registado Windows dispositivos irão satisfazer o política de frequência de início de sessão ao iniciar sessão no dispositivo e não-á novamente ao abrir as aplicações do Office.

Se tiver configurado a frequência de início de sessão diferente para as aplicações web diferente que estão em execução na mesma sessão de browser, a política mais rigorosas será aplicada para ambas as aplicações, uma vez que todas as aplicações em execução na mesma sessão de browser partilham um token de sessão único.

### <a name="policy-2-persistent-browser-session"></a>Política de 2: Sessão de browser persistente

1. Criar nova política
1. Escolha todas as condições necessárias.

   > [!NOTE]
   > Tenha em atenção que este controle requer escolher "Todas as aplicações de Cloud" como uma condição. Persistência da sessão de browser é controlada pelo token de sessão de autenticação. Todos os separadores numa sessão de browser compartilham um token de sessão único e, portanto, todos eles têm de partilhar o estado de persistência.

1. Aceda a **controlos de acesso** > **sessão** e clique em **sessão de browser persistente**
1. Selecione um valor da lista pendente
1. Guardar a política

![Política de acesso condicional configurada para o navegador persistente](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Configuração de sessão de Browser persistente no acesso condicional do Azure AD irá substituir o "Mantenha-se com sessão iniciada?" configuração da identidade visual painel no portal do Azure para o mesmo utilizador, se tiver configurado a ambas as políticas de empresa.

## <a name="validation"></a>Validação

Utilize a ferramenta de hipóteses para simular um início de sessão do utilizador para o aplicativo de destino e outras condições com base em como tiver configurado a sua política. Os controlos de gestão de sessão de autenticação apresentado no resultado da ferramenta.

![Resultados da ferramenta If e de acesso condicional](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Implementação da política

Para certificar-se de que a política funciona conforme esperado, a prática recomendada é testá-lo antes de a disponibilizar para produção. O ideal é que utilize um inquilino de teste para verificar se a sua nova política funciona como pretendido. Para obter mais informações, consulte o artigo [melhores práticas para acesso condicional no Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Passos Seguintes

* Se quiser saber como configurar uma política de acesso condicional, veja o artigo [exigir MFA para aplicações específicas com o Azure Active Directory condicional acesso](app-based-mfa.md).
* Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, veja o artigo [melhores práticas para acesso condicional no Azure Active Directory](best-practices.md).
