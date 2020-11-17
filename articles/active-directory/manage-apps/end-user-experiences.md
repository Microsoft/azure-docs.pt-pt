---
title: Experiências de utilizador final para aplicações - Azure Ative Directory
description: O Azure Ative Directory (Azure AD) oferece várias formas personalizáveis de implementar aplicações para utilizadores finais na sua organização.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: arvindh
ms.openlocfilehash: 9bcf45cdfbadbf16f48f8f2ac3bd0c6320a2803f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649233"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Experiências de utilizador final para aplicações no Azure Ative Directory

O Azure Ative Directory (Azure AD) oferece várias formas personalizáveis de implementar aplicações para utilizadores finais na sua organização:

* Azure AD My Apps
* Lançador de aplicações Microsoft 365
* Início de sessão direto em aplicações federadas
* Ligações avançadas para aplicações federadas, baseadas em palavras-passe ou existentes

Qual o método(s) que escolheu implementar na sua organização é a sua discrição.

## <a name="azure-ad-my-apps"></a>Azure AD My Apps

My Apps at é um portal baseado na https://myapps.microsoft.com web que permite a um utilizador final com uma conta organizacional em Azure Ative Diretório visualizar e lançar aplicações às quais lhes foi concedido acesso pelo administrador AZure AD. Se for um utilizador final com [o Azure Ative Directory Premium,](https://azure.microsoft.com/pricing/details/active-directory/)também pode utilizar as capacidades de gestão do grupo self-service através das Minhas Apps.

Por predefinição, todas as aplicações estão listadas numa única página. Mas pode usar coleções para agrupar aplicações relacionadas e apresentá-las num separador, tornando-as mais fáceis de encontrar. Por exemplo, você pode usar coleções para criar agrupamentos lógicos de aplicações para papéis de trabalho específicos, tarefas, projetos, e assim por diante. Para obter informações, consulte [Criar coleções no portal My Apps.](access-panel-collections.md) 

As minhas Apps estão separadas do portal Azure e não requer que os utilizadores tenham uma subscrição Azure ou uma subscrição do Microsoft 365.

Para mais informações sobre Azure AD My Apps, consulte a [introdução às minhas apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="microsoft-365-application-launcher"></a>Lançador de aplicações Microsoft 365

Para as organizações que implementaram o Microsoft 365, as aplicações atribuídas aos utilizadores através do Azure AD também aparecerão no portal do Office 365 em [https://portal.office.com/myapps](https://portal.office.com/myapps) . Isto torna fácil e conveniente para os utilizadores de uma organização lançar as suas apps sem ter de usar um segundo portal, e é a solução de lançamento de apps recomendada para organizações que usam o Microsoft 365.

Para obter mais informações sobre o lançador de aplicações do Office 365, consulte [se a sua aplicação aparecer no lançador de aplicações office 365.](/previous-versions/office/office-365-api/)

## <a name="direct-sign-on-to-federated-apps"></a>Início de sessão direto em aplicações federadas

A maioria das aplicações federadas que suportam SAML 2.0, WS-Federation ou OpenID connect também suportam a capacidade de os utilizadores começarem na aplicação, e depois ser assinados através do Azure AD, quer por redirecionamento automático quer clicando num link para iniciar sessão. Isto é conhecido como acesso iniciado pelo prestador de serviços, e a maioria das aplicações federadas na galeria de aplicações AD AZure suportam isso (ver a documentação ligada a partir do único assistente de configuração de sinalização da aplicação no portal Azure para mais detalhes).

## <a name="direct-sign-on-links"></a>Links de acesso direto

O Azure AD também suporta links de entrada única diretos para aplicações individuais que suportam um único sinal baseado em palavra-passe, um único sinal ligado e qualquer forma de inscrição única federada.

Estas ligações são URLs especificamente concebidos que enviam um utilizador através do processo de inscrição AZure AD para uma aplicação específica sem exigir que o utilizador as lance a partir de Azure AD My Apps ou Microsoft 365. Estes **URLs de acesso** ao Utilizador podem ser encontrados sob as propriedades das aplicações disponíveis para a empresa. No portal Azure, selecione aplicações **Azure Ative Directory**  >  **Enterprise**. Selecione a aplicação e, em seguida, **selecione Propriedades**.

![Exemplo do URL de acesso ao utilizador nas propriedades do Twitter](media/end-user-experiences/direct-sign-on-link.png)

Estes links podem ser copiados e colados em qualquer lugar que pretenda fornecer um link de inscrição para a aplicação selecionada. Isto pode estar num e-mail ou em qualquer portal personalizado baseado na Web que tenha configurado para acesso a aplicações de utilizador. Aqui está um exemplo de um URL de assinatura simples azure AD para o Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Semelhante aos URLs específicos da organização para as minhas apps, pode personalizar ainda mais este URL adicionando um dos domínios ativos ou verificados para o seu diretório após o domínio *myapps.microsoft.com.* Isto garante que qualquer marca organizacional é carregada imediatamente na página de entrada sem que o utilizador precise de introduzir primeiro o seu ID do utilizador:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Quando um utilizador autorizado clica num destes links específicos da aplicação, vê primeiro a sua página de início de sação organizacional (assumindo que ainda não está inscrita), e após o início de sação é redirecionado para a sua app sem parar primeiro nas My Apps. Se o utilizador não tiver pré-requisitos para aceder à aplicação, como a extensão do navegador de sinal único baseada na palavra-passe, então o link irá solicitar ao utilizador que instale a extensão em falta. O URL de ligação também permanece constante se a configuração de inscrição única para a aplicação mudar.

Estas ligações utilizam os mesmos mecanismos de controlo de acesso que as Minhas Apps e a Microsoft 365, sendo que apenas os utilizadores ou grupos que tenham sido designados para a aplicação no portal Azure poderão autenticar com sucesso. No entanto, qualquer utilizador que não seja autorizado verá uma mensagem explicando que não lhes foi concedido acesso, e é-lhes dado um link para carregar as Minhas Apps para visualizar as aplicações disponíveis para as quais têm acesso.

## <a name="next-steps"></a>Próximos passos

* [Quickstart Series em Gestão de Aplicações](view-applications-portal.md)
* [O que é o início de sessão único?](what-is-single-sign-on.md)
* [Integrar o Azure Ative Directory com candidaturas a iniciar guia](plan-an-application-integration.md)