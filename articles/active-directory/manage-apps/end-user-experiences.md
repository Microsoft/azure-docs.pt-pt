---
title: Experiências do usuário final para aplicativos-Azure Active Directory
description: O Azure Active Directory (AD do Azure) fornece várias maneiras personalizáveis de implantar aplicativos para usuários finais em sua organização.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 330f3100858d517666ac14fa8c206b4c06998c4f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274211"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Experiências do usuário final para aplicativos no Azure Active Directory

O Azure Active Directory (AD do Azure) fornece várias maneiras personalizáveis de implantar aplicativos para usuários finais em sua organização:

* Painel de acesso do AD do Azure
* Iniciador de aplicativos do Office 365
* Início de sessão direto em aplicações federadas
* Ligações avançadas para aplicações federadas, baseadas em palavras-passe ou existentes

Quais métodos você escolhe implantar em sua organização é seu critério.

## <a name="azure-ad-access-panel"></a>Painel de acesso do AD do Azure

O painel de acesso em https://myapps.microsoft.com é um portal baseado na Web que permite que um usuário final com uma conta institucional no Azure Active Directory exiba e inicie aplicativos baseados em nuvem aos quais eles receberam acesso pelo administrador do Azure AD. Se você for um usuário final com [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), também poderá utilizar os recursos de gerenciamento de grupo de autoatendimento por meio do painel de acesso.

![Captura de tela mostra o portal do painel de acesso do Azure AD](media/what-is-single-sign-on/azure-ad-access-panel.png)

Por padrão, todos os aplicativos são listados juntos em uma única página. Mas você pode usar espaços de trabalho para agrupar aplicativos relacionados e apresentá-los em uma guia separada, facilitando sua localização. Por exemplo, você pode usar espaços de trabalho para criar agrupamentos lógicos de aplicativos para funções de trabalho específicas, tarefas, projetos e assim por diante. Para obter informações, consulte [como usar os espaços de trabalho meus aplicativos para personalizar os painéis de acesso do usuário (versão prévia)](access-panel-workspaces.md). 

O painel de acesso é separado da portal do Azure e não exige que os usuários tenham uma assinatura do Azure ou assinatura do Office 365.

Para obter mais informações sobre o painel de acesso do AD do Azure, consulte a [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Iniciador de aplicativos do Office 365

Para organizações que implantaram o Office 365, os aplicativos atribuídos aos usuários por meio do Azure AD também aparecerão no portal do Office 365 em [https://portal.office.com/myapps](https://portal.office.com/myapps). Isso torna fácil e conveniente para os usuários em uma organização iniciarem seus aplicativos sem precisar usar um segundo portal e é a solução de inicialização de aplicativo recomendada para organizações que usam o Office 365.

![Captura de tela mostra o portal do Office 365](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Para obter mais informações sobre o iniciador de aplicativos do Office 365, consulte [fazer seu aplicativo aparecer no inicializador de aplicativos do office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Início de sessão direto em aplicações federadas

A maioria dos aplicativos federados que dão suporte a SAML 2,0, WS-Federation ou OpenID Connect também oferece suporte à capacidade para que os usuários iniciem no aplicativo e, em seguida, se conectem por meio do Azure AD pelo redirecionamento automático ou clicando em um link para entrar. Isso é conhecido como logon iniciado pelo provedor de serviços e a maioria dos aplicativos federados na Galeria de aplicativos do Azure AD dá suporte a isso (consulte a documentação vinculada do assistente de configuração de logon único do aplicativo no portal do Azure para obter detalhes).

![Exemplo de uma página de entrada de aplicativo móvel](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Links de logon direto

O AD do Azure também dá suporte a links de logon único diretos para aplicativos individuais que dão suporte ao logon único baseado em senha, logon único vinculado e qualquer forma de logon único federado.

Esses links são URLs criadas especificamente que enviam um usuário por meio do processo de entrada do Azure AD para um aplicativo específico sem exigir que o usuário os inicie no painel de acesso do Azure AD ou no Office 365. Essas **URLs de acesso do usuário** podem ser encontradas nas propriedades de aplicativos empresariais disponíveis. Na portal do Azure, selecione **Azure Active Directory** > **aplicativos empresariais**. Selecione o aplicativo e, em seguida, selecione **Propriedades**.

![Exemplo da URL de acesso do usuário nas propriedades do Twitter](media/end-user-experiences/direct-sign-on-link.png)

Esses links podem ser copiados e colados em qualquer lugar em que você queira fornecer um link de entrada para o aplicativo selecionado. Isso pode estar em um email ou em qualquer portal personalizado baseado na Web que você tenha configurado para acesso ao aplicativo do usuário. Aqui está um exemplo de uma URL de logon único direto do Azure AD para o Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Assim como as URLs específicas da organização para o painel de acesso, você pode personalizar ainda mais essa URL adicionando um dos domínios ativos ou verificados ao seu diretório após o domínio myapps.microsoft.com. Isso garante que qualquer identidade visual organizacional seja carregada imediatamente na página de entrada sem que o usuário precise inserir sua ID de usuário primeiro:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Quando um usuário autorizado clica em um desses links específicos do aplicativo, ele primeiro vê sua página de entrada organizacional (pressupondo que eles ainda não estejam conectados) e, depois que a entrada é redirecionada para seu aplicativo sem parar no painel de acesso primeiro. Se o usuário não tiver pré-requisitos para acessar o aplicativo, como a extensão do navegador de logon único baseado em senha, o link solicitará que o usuário instale a extensão ausente. A URL do link também permanecerá constante se a configuração de logon único para o aplicativo for alterada.

Esses links usam os mesmos mecanismos de controle de acesso que o painel de acesso e o Office 365, e somente os usuários ou grupos que foram atribuídos ao aplicativo no portal do Azure serão capazes de se autenticar com êxito. No entanto, qualquer usuário não autorizado verá uma mensagem explicando que não recebeu acesso e receberá um link para carregar o painel de acesso para exibir os aplicativos disponíveis aos quais eles têm acesso.

## <a name="next-steps"></a>Passos seguintes

Para planos de implantação, consulte [Azure Active Directory planos de implantação](../fundamentals/active-directory-deployment-plans.md)
