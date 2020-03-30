---
title: Como e por que motivo são adicionadas aplicações ao AAD
titleSuffix: Microsoft identity platform
description: O que significa que uma aplicação seja adicionada à AD Azure e como é que eles chegam lá?
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: d47ed3a4cd4fbdcb69b956d3c8418f70a71cf44f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263143"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Como e por que as aplicações são adicionadas ao Azure AD

Existem duas representações de candidaturas em Azure AD:

* [Objetos](app-objects-and-service-principals.md#application-object) de aplicação - Embora existam [exceções,](#notes-and-exceptions)os objetos de aplicação podem ser considerados a definição de uma aplicação.
* [Diretores](app-objects-and-service-principals.md#service-principal-object) de serviço - Pode ser considerado um exemplo de uma aplicação. Os diretores de serviço geralmente referenciam um objeto de aplicação, e um objeto de aplicação pode ser referenciado por vários diretores de serviço em todos os diretórios.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>O que são objetos de aplicação e de onde vêm?

Pode gerir objetos de [aplicação](app-objects-and-service-principals.md#application-object) no portal Azure através da experiência Registos de [Aplicações.](https://aka.ms/appregistrations) Os objetos de aplicação descrevem a aplicação à AD Azure e podem ser consideradas a definição da aplicação, permitindo que o serviço saiba como emitir fichas para a aplicação com base nas suas definições. O objeto de aplicação só existirá no seu diretório de residência, mesmo que seja um serviço de apoio a candidaturas multi-inquilinos em outros diretórios. O objeto de aplicação pode incluir qualquer um dos seguintes (bem como informações adicionais não mencionadas aqui):

* Nome, logotipo e editor
* Redirecionamento de URIs
* Segredos (chaves simétricas e/ou assimétricas utilizadas para autenticar a aplicação)
* Dependências da API (OAuth)
* APIs/recursos/âmbitos publicados (OAuth)
* Funções de aplicativo (RBAC)
* Metadados e configuração sSO
* Fornecimento de metadados e configuração do utilizador
* Metadados e configuração proxy

Os objetos de aplicação podem ser criados através de múltiplas vias, incluindo:

* Inscrições de candidatura no portal Azure
* Criar uma nova aplicação utilizando o Visual Studio e configurá-lo para usar a autenticação Azure AD
* Quando um administrador adiciona uma aplicação da galeria de aplicações (que também criará um diretor de serviço)
* Utilização do Microsoft Graph API ou PowerShell para criar uma nova aplicação
* Muitos outros, incluindo várias experiências de desenvolvimento em Azure e em experiências de explorador estoque da API em centros de desenvolvimento

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>O que são os diretores de serviço e de onde vêm?

Pode gerir [os principais de serviços](app-objects-and-service-principals.md#service-principal-object) no portal Azure através da experiência [De aplicações empresariais.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) Os diretores de serviço são o que rege uma aplicação que liga a Azure AD e pode ser considerada a instância da aplicação no seu diretório. Para qualquer aplicação, pode ter, no máximo, um objeto de aplicação (que está registado num diretório "casa") e um ou mais objetos principais de serviço que representam instâncias da aplicação em cada diretório em que atua. 

O diretor de serviço pode incluir:

* Uma referência de volta a um objeto de aplicação através da propriedade id aplicação
* Registos de atribuições locais de aplicação de utilizadores e de grupo
* Registos de permissões de utilizadores locais e administradores concedidas ao pedido
  * Por exemplo: permissão para a aplicação aceder a um e-mail de um determinado utilizador
* Registos de políticas locais, incluindo a política de acesso condicional
* Registos de configurações locais alternativas para uma aplicação
  * Regras de transformação de sinistros
  * Mapeamentos de atributos (fornecimento de utilizadores)
  * Funções de aplicativo específicas para o diretório (se a aplicação suporta funções personalizadas)
  * Nome ou logotipo específico do diretório

Tal como os objetos de aplicação, os principais de serviço também podem ser criados através de múltiplas vias, incluindo:

* Quando os utilizadores assinam uma aplicação de terceiros integrada com a Azure AD
  * Durante o início do sessão, é solicitado aos utilizadores que autorizem a aplicação a aceder ao seu perfil e outras permissões. A primeira pessoa a dar consentimento faz com que um diretor de serviço que represente o pedido seja adicionado ao diretório.
* Quando os utilizadores assinam serviços online da Microsoft como [o Office 365](https://products.office.com/)
  * Quando subscreve o Office 365 ou inicia um ensaio, um ou mais diretores de serviço são criados no diretório que representa os vários serviços que são utilizados para fornecer toda a funcionalidade associada ao Office 365.
  * Alguns serviços do Office 365, como o SharePoint, criam os principais de serviços de forma contínua para permitir uma comunicação segura entre componentes, incluindo fluxos de trabalho.
* Quando um administrador adiciona uma aplicação da galeria de aplicações (isto também criará um objeto de aplicação subjacente)
* Adicione uma aplicação para usar o Proxy de [Aplicação AD Azure](/azure/active-directory/manage-apps/application-proxy)
* Ligue uma aplicação para um único sinal utilizando o SAML ou o sinal único de senha (SSO)
* Programáticamente através da Microsoft Graph API ou PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Como estão os objetos de aplicação e os diretores de serviço relacionados uns com os outros?

Uma aplicação tem um objeto de aplicação no seu diretório de residência que é referenciado por um ou mais diretores de serviço em cada um dos diretórios onde opera (incluindo o diretório de residência da aplicação).

![Mostra relação entre objetos de aplicativos e diretores de serviço][apps_service_principals_directory]

No diagrama anterior, a Microsoft mantém dois diretórios internamente (mostrados à esquerda) que utiliza para publicar aplicações:

* Um para aplicações da Microsoft (diretório de serviços da Microsoft)
* Uma para aplicações de terceiros pré-integradas (diretório de galerias de aplicações)

Os editores/fornecedores de aplicações que se integrem com a Azure AD são obrigados a ter um diretório editorial (apresentado à direita como "Algum Diretório SaaS").

As aplicações que se adiciona (representada como **App (sua)** no diagrama) incluem:

* Apps que desenvolveu (integradas com a Azure AD)
* Aplicativos que ligou para um único sign-on
* Apps que publicou usando o proxy de aplicação DaD Azure

### <a name="notes-and-exceptions"></a>Notas e exceções

* Nem todos os diretores de serviço apontam para um objeto de aplicação. Quando a Azure AD foi originalmente construída, os serviços prestados às candidaturas eram mais limitados e o diretor de serviço era suficiente para estabelecer uma identidade de aplicação. O diretor de serviço original estava mais próximo da conta de serviço do Windows Server Ative Directory. Por esta razão, ainda é possível criar diretores de serviço através de diferentes vias, como usar o Azure AD PowerShell, sem primeiro criar um objeto de aplicação. O Microsoft Graph API requer um objeto de aplicação antes de criar um diretor de serviço.
* Nem todas as informações acima descritas são atualmente expostas programáticamente. Os seguintes só estão disponíveis na UI:
  * Regras de transformação de sinistros
  * Mapeamentos de atributos (fornecimento de utilizadores)
* Para obter informações mais detalhadas sobre o principal de serviço e objetos de aplicação, consulte a documentação de referência da Microsoft Graph API:
  * [Aplicação](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)
  * [Diretor de Serviço](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Porque é que as aplicações se integram com a Azure AD?

As candidaturas são adicionadas à Azure AD para alavancar um ou mais dos serviços que presta, incluindo:

* Autenticação e autorização de pedido
* Autenticação e autorização do utilizador
* SSO usando federação ou senha
* Fornecimento e sincronização dos utilizadores
* Controlo de acesso baseado em funções - Utilize o diretório para definir funções de aplicação para realizar controlos de autorização baseados em funções num pedido
* Serviços de autorização OAuth - Utilizados pelo Office 365 e outras aplicações da Microsoft para autorizar o acesso a APIs/recursos
* Publicação de aplicações e procuração - Publique uma aplicação de uma rede privada para a internet

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Quem tem permissão para adicionar aplicações à minha instância de Anúncio Salé- Azure?

Embora existam algumas tarefas que apenas os administradores globais podem fazer (como adicionar aplicações a partir da galeria de aplicações e configurar uma aplicação para usar o Proxy de Aplicação) por padrão todos os utilizadores do seu diretório têm o direito de registar objetos de aplicação que estão desenvolvendo e discrição sobre quais as aplicações que partilham/dar acesso aos seus dados organizacionais por consentimento. Se uma pessoa for o primeiro utilizador do seu diretório a iniciar sessão com um pedido e conceder o consentimento, isso criará um diretor de serviço no seu inquilino; caso contrário, as informações de concessão de consentimento serão armazenadas no principal do serviço existente.

Permitir que os utilizadores se registem e consintam com as aplicações pode inicialmente parecer preocupante, mas tenha em mente o seguinte:


* As aplicações têm sido capazes de alavancar o Diretório Ativo do Windows Server para a autenticação do utilizador durante muitos anos sem exigir que a aplicação seja registada ou gravada no diretório. Agora, a organização terá melhorado a visibilidade para exatamente quantas aplicações estão a usar o diretório e com que finalidade.
* Delegar estas responsabilidades para com os utilizadores nega a necessidade de um registo e processo de publicação de aplicações orientados pela administração. Com os Serviços da Federação de Diretório Ativo (ADFS), era provável que um administrador tivesse de adicionar um pedido como parte de base em nome dos seus desenvolvedores. Agora os desenvolvedores podem autosserviço.
* Os utilizadores que se inscrevam em aplicações que utilizem as suas contas de organização para fins comerciais é uma coisa boa. Se posteriormente saírem da organização, perderão automaticamente o acesso à sua conta na aplicação que estavam a utilizar.
* Ter um registo dos dados partilhados com que aplicação é uma coisa boa. Os dados são mais transportáveis do que nunca e é útil ter um registo claro de quem partilhou quais os dados com que aplicações.
* Os proprietários da API que usam a AD Azure para a OAuth decidem exatamente quais as permissões que os utilizadores podem conceder às aplicações e quais as permissões que exigem que um administrador concorde. Apenas os administradores podem consentir com âmbitos maiores e permissões mais significativas, enquanto o consentimento do utilizador é alargado aos dados e capacidades dos próprios utilizadores.
* Quando um utilizador adiciona ou permite que uma aplicação aceda aos seus dados, o evento pode ser auditado para que possa ver os Relatórios de Auditoria dentro do portal Azure para determinar como uma aplicação foi adicionada ao diretório.

Se ainda pretender impedir que os utilizadores do seu diretório registem aplicações e que se inscrevam em aplicações sem aprovação de administradores, existem duas definições que pode alterar para desativar essas capacidades:

* Para evitar que os utilizadores consintam com as aplicações em seu próprio nome:
  1. No portal Azure, vá à secção de [definições](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) do Utilizador sob aplicações da Enterprise.
  2. Alterar **os Utilizadores podem consentir em aplicações que acedam aos dados** da empresa em seu nome para **O**.
     
     > [!NOTE]
     > Se decidir desligar o consentimento do utilizador, será necessário um administrador para consentir com qualquer nova aplicação que o utilizador precise de utilizar.

* Para evitar que os utilizadores registem as suas próprias aplicações:
  1. No portal Azure, vá à secção de [definições](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) do Utilizador sob o Diretório Ativo Azure
  2. Alterar **os Utilizadores podem registar aplicações** para **Nº**.

> [!NOTE]
> A própria Microsoft utiliza a configuração predefinida com utilizadores capazes de registar aplicações e consentir com aplicações em seu próprio nome.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
