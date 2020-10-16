---
title: Como e por que motivo são adicionadas aplicações ao AAD
titleSuffix: Microsoft identity platform
description: O que significa que uma aplicação seja adicionada à Azure AD e como é que elas chegam lá?
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: bccdb830fba4d55266dc9eff8f06c5ac4e3f2a34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706188"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Como e por que as aplicações são adicionadas ao Azure AD

Existem duas representações de candidaturas em Azure AD:

* [Objetos de aplicação](app-objects-and-service-principals.md#application-object) - Embora existam [exceções,](#notes-and-exceptions)os objetos de aplicação podem ser considerados a definição de uma aplicação.
* [Principados de serviço -](app-objects-and-service-principals.md#service-principal-object) Pode ser considerado um caso de um pedido. Os diretores de serviço geralmente referem um objeto de aplicação, e um objeto de aplicação pode ser referenciado por vários diretores de serviço em todos os diretórios.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>O que são objetos de aplicação e de onde vêm?

Pode gerir [objetos de aplicação](app-objects-and-service-principals.md#application-object) no portal Azure através da experiência Registos de [Aplicações.](https://aka.ms/appregistrations) Os objetos de aplicação descrevem a aplicação para Azure AD e podem ser considerados a definição da aplicação, permitindo que o serviço saiba como emitir fichas para a aplicação com base nas suas definições. O objeto de aplicação só existirá no seu diretório de origem, mesmo que seja uma aplicação multi-arrendatário que suporta diretores de serviço em outros diretórios. O objeto da aplicação pode incluir qualquer um dos seguintes (bem como informações adicionais não mencionadas aqui):

* Nome, logotipo e editor
* Redirecionar URIs
* Segredos (chaves simétricas e/ou assimétricas utilizadas para autenticar a aplicação)
* Dependências da API (OAuth)
* APIs/recursos/âmbitos publicados (OAuth)
* Funções de aplicativo (RBAC)
* Metadados e configuração SSO
* Utilizador a apresentando metadados e configuração
* Metadados e configuração proxy

Os objetos de aplicação podem ser criados através de múltiplas vias, incluindo:

* Inscrições de candidatura no portal Azure
* Criar uma nova aplicação utilizando o Visual Studio e configurá-la para usar a autenticação AD AZure
* Quando um administrador adiciona uma aplicação da galeria de aplicações (que também criará um principal serviço)
* Utilizar a API ou PowerShell do Microsoft Graph para criar uma nova aplicação
* Muitos outros, incluindo várias experiências de desenvolvedores em Azure e em experiências de exploradores API em centros de desenvolvimento

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>O que são diretores de serviço e de onde vêm?

Pode gerir [os principais serviços](app-objects-and-service-principals.md#service-principal-object) no portal Azure através da experiência [Aplicações Enterprise.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) Os diretores de serviço são o que rege uma aplicação que liga a Azure AD e podem ser considerados o caso da aplicação no seu diretório. Para qualquer pedido, pode ter no máximo um objeto de aplicação (que está registado num diretório "casa") e um ou mais objetos principais de serviço que representam instâncias do pedido em todos os diretórios em que atua. 

O diretor de serviço pode incluir:

* Uma referência de volta a um objeto de aplicação através da propriedade de ID de aplicação
* Registos de atribuições de funções de aplicação de utilizadores e grupos locais
* Registos de permissões de utilizador e administração locais concedidas à aplicação
  * Por exemplo: permissão para a aplicação aceder a um e-mail de um determinado utilizador
* Registos de políticas locais, incluindo a política de acesso condicional
* Registos de configurações locais alternativas para uma aplicação
  * Regras de transformação de sinistros
  * Mapeamentos de atributos (fornecimento de utilizador)
  * Funções de aplicação específicas do diretório (se a aplicação suporta funções personalizadas)
  * Nome ou logotipo específico do diretório

Tal como os objetos de aplicação, os principais de serviço também podem ser criados através de múltiplas vias, incluindo:

* Quando os utilizadores insinuem-se numa aplicação de terceiros integrada com a Azure AD
  * Durante a sessão de sessão, os utilizadores são convidados a dar permissão à aplicação para aceder ao seu perfil e outras permissões. A primeira pessoa a dar o seu consentimento faz com que um diretor de serviço que represente o pedido seja adicionado ao diretório.
* Quando os utilizadores iniciarem sôms nos serviços online da Microsoft como [o Microsoft 365](https://products.office.com/)
  * Quando subscreve o Microsoft 365 ou inicia um teste, um ou mais diretores de serviço são criados no diretório que representa os vários serviços que são utilizados para fornecer toda a funcionalidade associada à Microsoft 365.
  * Alguns serviços da Microsoft 365, como o SharePoint, criam principais de serviços numa base contínua para permitir uma comunicação segura entre componentes, incluindo fluxos de trabalho.
* Quando um administrador adiciona uma aplicação da galeria de aplicações (isto também criará um objeto de aplicação subjacente)
* Adicione uma aplicação para usar o [Azure AD Application Proxy](../manage-apps/application-proxy.md)
* Conecte um pedido de sinal único utilizando o SSL ou o sign-on único da palavra-passe (SSO)
* Programáticamente através da Microsoft Graph API ou PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Como estão os objetos de aplicação e os principais de serviço relacionados entre si?

Um requerimento tem um objeto de aplicação no seu diretório de origem que é referenciado por um ou mais diretores de serviço em cada um dos diretórios onde opera (incluindo o diretório de residência do pedido).

![Mostra relação entre objetos de aplicação e diretores de serviço][apps_service_principals_directory]

No diagrama anterior, a Microsoft mantém dois diretórios internos (mostrados à esquerda) que utiliza para publicar aplicações:

* Um para apps da Microsoft (diretório de serviços da Microsoft)
* Um para aplicações de terceiros pré-integradas (diretório de galerias de aplicações)

Os editores/fornecedores de aplicações que se integram com a Azure AD são obrigados a ter um diretório editorial (mostrado à direita como "Some SaaS Directory").

As aplicações que se adiciona (representadas como **App (sua)** no diagrama) incluem:

* Aplicativos que desenvolveu (integrado com Azure AD)
* Aplicativos que ligou para um único sign-on
* Aplicativos que publicou usando o proxy de aplicação AD Azure

### <a name="notes-and-exceptions"></a>Notas e exceções

* Nem todos os diretores de serviço apontam para um objeto de aplicação. Quando a Azure AD foi originalmente construída, os serviços prestados aos pedidos eram mais limitados e o principal de serviço era suficiente para estabelecer uma identidade de pedido. O principal do serviço original estava mais próximo da conta de serviço do Windows Server Ative Directory. Por esta razão, ainda é possível criar principais serviços através de diferentes vias, como a utilização do Azure AD PowerShell, sem antes criar um objeto de aplicação. A API do Microsoft Graph requer um objeto de aplicação antes de criar um principal de serviço.
* Nem todas as informações descritas acima estão atualmente expostas programáticamente. Os seguintes estão disponíveis apenas na UI:
  * Regras de transformação de sinistros
  * Mapeamentos de atributos (fornecimento de utilizador)
* Para obter informações mais detalhadas sobre o principal do serviço e os objetos de aplicação, consulte a documentação de referência da Microsoft Graph API:
  * [Aplicação](/graph/api/resources/application?view=graph-rest-1.0)
  * [Principal de Serviço](/graph/api/resources/serviceprincipal?view=graph-rest-beta)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Porque é que as aplicações se integram com a Azure AD?

As aplicações são adicionadas à Azure AD para alavancar um ou mais dos serviços que presta, incluindo:

* Autenticação e autorização de pedidos
* Autenticação e autorização do utilizador
* SSO usando federação ou senha
* Provisão e sincronização do utilizador
* Controlo de acesso baseado em funções - Utilize o diretório para definir funções de aplicação para executar controlos de autorização baseados em funções numa aplicação
* Serviços de autorização OAuth - Usados pela Microsoft 365 e outras aplicações da Microsoft para autorizar o acesso a APIs/recursos
* Publicação de aplicações e procuração - Publicar uma aplicação de uma rede privada para a internet

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Quem tem permissão para adicionar aplicações à minha instância AD Azure?

Embora existam algumas tarefas que apenas os administradores globais podem fazer (como adicionar aplicações a partir da galeria de aplicações e configurar uma aplicação para usar o Application Proxy) por padrão todos os utilizadores do seu diretório têm o direito de registar objetos de aplicação que estão a desenvolver e discrição sobre quais aplicações partilham/dão acesso aos seus dados organizacionais através do consentimento. Se uma pessoa for o primeiro utilizador do seu diretório a inscrever-se num pedido e conceder o seu consentimento, isso criará um principal serviço no seu inquilino; caso contrário, as informações de concessão de consentimento serão armazenadas no titular do serviço existente.

Permitir que os utilizadores se registem e consintam em aplicações pode inicialmente soar preocupante, mas tenha em mente o seguinte:


* As aplicações têm sido capazes de alavancar o Windows Server Ative Directory para a autenticação do utilizador durante muitos anos sem exigir que a aplicação seja registada ou gravada no diretório. Agora, a organização terá uma visibilidade melhorada para exatamente quantas aplicações estão usando o diretório e para que finalidade.
* Delegar estas responsabilidades para com os utilizadores nega a necessidade de um processo de registo e publicação de candidaturas orientadas para a administração. Com os Serviços da Federação de Diretório Ativo (ADFS) era provável que um administrador tivesse de adicionar uma aplicação como parte dependente em nome dos seus desenvolvedores. Agora os desenvolvedores podem autosserviço.
* Os utilizadores que se inscrevam em aplicações que utilizam as suas contas de organização para fins comerciais é uma coisa boa. Se posteriormente abandonarem a organização, perderão automaticamente o acesso à sua conta na aplicação que utilizavam.
* Ter um registo dos dados partilhados com que aplicação é uma coisa boa. Os dados são mais transportáveis do que nunca e é útil ter um registo claro de quem partilhou quais os dados com que aplicações.
* Os proprietários da API que usam a Azure AD para a OAuth decidem exatamente quais as permissões que os utilizadores podem conceder às aplicações e quais as permissões que requerem que um administrador concorde. Apenas os administradores podem consentir com âmbitos maiores e permissões mais significativas, enquanto o consentimento do utilizador é traçado para os dados e capacidades dos próprios utilizadores.
* Quando um utilizador adiciona ou permite que uma aplicação aceda aos seus dados, o evento pode ser auditado para que possa ver os Relatórios de Auditoria dentro do portal Azure para determinar como uma aplicação foi adicionada ao diretório.

Se ainda quiser impedir que os utilizadores do seu diretório registem aplicações e se inscrevam em aplicações sem a aprovação do administrador, existem duas definições que pode alterar para desativar essas capacidades:

* Para evitar que os utilizadores consintam em aplicações em seu próprio nome:
  1. No portal Azure, aceda à secção [de definições](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) de Utilizador sob aplicações da Enterprise.
  2. Alterar **Os Utilizadores podem consentir que as aplicações acedam aos dados da empresa em seu nome** para o **Nº.**
     
     > [!NOTE]
     > Se decidir desativar o consentimento do utilizador, será necessário um administrador para consentir com qualquer nova aplicação que um utilizador precise de utilizar.

* Para evitar que os utilizadores registem as suas próprias aplicações:
  1. No portal Azure, aceda à secção [de definições](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) do Utilizador no Diretório Ativo Azure
  2. Alterar **Os Utilizadores podem registar aplicações** para **Nº.**

> [!NOTE]
> A própria Microsoft utiliza a configuração padrão com utilizadores capazes de registar aplicações e consentir em aplicações em seu próprio nome.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg