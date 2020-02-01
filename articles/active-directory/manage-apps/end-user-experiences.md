---
title: Experiências de utilizador final para aplicações - Diretório Ativo Azure
description: O Azure Ative Directory (Azure AD) fornece várias formas personalizáveis de implementar aplicações para utilizadores finais na sua organização.
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
ms.openlocfilehash: 4f9ff446bae0a010b12e84172d5af60c6ca81c0e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896897"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Experiências de utilizador final para aplicações no Diretório Ativo do Azure

O Azure Ative Directory (Azure AD) fornece várias formas personalizáveis de implementar aplicações para utilizadores finais na sua organização:

* Painel de acesso Azure AD
* Lançador de aplicações office 365
* Início de sessão direto em aplicações federadas
* Ligações avançadas para aplicações federadas, baseadas em palavras-passe ou existentes

Qual o(s) método que escolhe s implementar na sua organização é a sua discrição.

## <a name="azure-ad-access-panel"></a>Painel de acesso Azure AD

O Painel de Acesso da https://myapps.microsoft.com é um portal baseado na web que permite a um utilizador final com uma conta organizacional no Azure Ative Directory visualizar e lançar aplicações baseadas na nuvem a que foi concedido acesso pelo administrador da AD Azure. Se for um utilizador final com [o Azure Ative Directory Premium,](https://azure.microsoft.com/pricing/details/active-directory/)também pode utilizar capacidades de gestão de grupos de self-service através do Painel de Acesso.

![Screenshot mostra o portal do Painel de Acesso AD Azure](media/what-is-single-sign-on/azure-ad-access-panel.png)

Por padrão, todos os aplicativos são listados juntos em uma única página. Mas pode usar coleções para agrupar aplicações relacionadas e apresentá-las num separador, tornando-as mais fáceis de encontrar. Por exemplo, pode utilizar coleções para criar agrupamentos lógicos de candidaturas para funções específicas de trabalho, tarefas, projetos, e assim por diante. Para obter informações, consulte [como utilizar as coleções das Minhas Apps para personalizar os painéis](access-panel-collections.md)de acesso ao utilizador. 

O Painel de Acesso é separado do portal Azure e não exige que os utilizadores tenham uma subscrição Azure ou uma subscrição do Office 365.

Para obter mais informações sobre o painel de acesso da AD Azure, consulte a [introdução ao painel](../user-help/active-directory-saas-access-panel-introduction.md)de acesso .

## <a name="office-365-application-launcher"></a>Lançador de aplicações office 365

Para as organizações que implementaram o Office 365, as aplicações atribuídas aos utilizadores através do Azure AD também estarão presentes no portal do Office 365 em [https://portal.office.com/myapps. ](https://portal.office.com/myapps) Isto torna mais fácil e conveniente para os utilizadores de uma organização lançar as suas apps sem ter de usar um segundo portal, sendo a solução de lançamento recomendada para as organizações que utilizam o Office 365.

![Screenshot mostra o portal Office 365](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Para mais informações sobre o lançador de aplicações do Office 365, consulte A sua aplicação aparecer no lançador de [aplicações do Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Início de sessão direto em aplicações federadas

A maioria das aplicações federadas que suportam saml 2.0, WS-Federation ou OpenID conectam também suportam a capacidade de os utilizadores iniciarem a aplicação, e depois são assinados através da AD Azure, quer por redirecção automática, quer clicando num link para iniciar sessão. Isto é conhecido como prestador de serviços iniciado sessão de sinalização e aplicações mais federadas na galeria de aplicações Azure AD suportam esta situação (ver a documentação ligada ao único assistente de configuração de sinalização da aplicação no portal Azure para mais detalhes).

![Exemplo de um sinal de aplicativo móvel na página](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Links de sinalização direto

A Azure AD também suporta links de inscrição simples diretos para aplicações individuais que suportam o único sign-on baseado em palavra-passe, o único signo ligado e qualquer forma de inscrição única federada.

Estes links são URLs especificamente concebidos que enviam um utilizador através do processo de iniciação de acesso a Anúncio sem exigir que o utilizador os lance a partir do painel de acesso Azure AD ou do Office 365. Estes **URLs** de acesso ao Utilizador podem ser encontrados sob as propriedades das aplicações empresariais disponíveis. No portal Azure, selecione **aplicações**de > De **Diretório Ativo azure.** Selecione a aplicação e, em seguida, selecione **Propriedades**.

![Exemplo do URL de acesso ao utilizador nas propriedades do Twitter](media/end-user-experiences/direct-sign-on-link.png)

Estes links podem ser copiados e colados em qualquer lugar que pretenda fornecer uma ligação de inscrição à aplicação selecionada. Isto pode estar num e-mail, ou em qualquer portal personalizado baseado na Web que tenha configurado para o acesso à aplicação do utilizador. Aqui está um exemplo de um URL de assinatura direta da AD Azure para o Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Semelhante aos URLs específicos da organização para o painel de acesso, pode personalizar ainda mais este URL adicionando um dos domínios ativos ou verificados para o seu diretório após o domínio myapps.microsoft.com. Isto garante que qualquer marca organizacional é carregada imediatamente na página de entrada sem que o utilizador precise de introduzir primeiro o seu ID do utilizador:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Quando um utilizador autorizado clica num destes links específicos para aplicações, vê primeiro a sua página de entrada organizacional (assumindo que ainda não estão inscritos), e após o início o início do início o registo é redirecionado para a sua aplicação sem parar no painel de acesso primeiro. Se o utilizador estiver a perder pré-requisitos para aceder à aplicação, como a extensão do navegador de sinal único baseada em palavra-passe, o link irá levar o utilizador a instalar a extensão em falta. O URL de ligação também permanece constante se a configuração de inscrição única para as alterações da aplicação.

Estas ligações utilizam os mesmos mecanismos de controlo de acesso que o painel de acesso e o Office 365, e apenas os utilizadores ou grupos que tenham sido atribuídos à aplicação no portal Azure poderão autenticar com sucesso. No entanto, qualquer utilizador que não esteja autorizado verá uma mensagem explicando que não lhes foi concedido acesso, e é-lhes dado um link para carregar o painel de acesso para visualizar as aplicações disponíveis para as quais têm acesso.

## <a name="next-steps"></a>Passos seguintes

Para planos de implantação, consulte os planos de [implantação do Diretório Ativo do Azure](../fundamentals/active-directory-deployment-plans.md)
