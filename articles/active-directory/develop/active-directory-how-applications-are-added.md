---
title: Como e por que os aplicativos são adicionados ao Azure Active Directory
description: O que significa para uma aplicação a ser adicionado ao Azure AD e como chegarem lá?
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol, lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: 257f7b66163b72141ceb6405768e912a263fb14b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124223"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Como e por que os aplicativos são adicionados ao Azure AD

Existem duas representações de aplicações no Azure AD: 
* [Objetos de aplicativo](app-objects-and-service-principals.md#application-object) – apesar de existirem [exceções](#notes-and-exceptions), objetos do aplicativo podem ser considerados a definição de um aplicativo.
* [Principais de serviço](app-objects-and-service-principals.md#service-principal-object) -pode ser considerado uma instância de um aplicativo. Principais de serviço geralmente fazem referência a um objeto de aplicativo e um objeto de aplicativo pode ser referenciado por várias entidades de segurança do serviço em diretórios.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Quais são os objectos da aplicação e onde eles vêm?
Pode gerir [objectos da aplicação](app-objects-and-service-principals.md#application-object) no portal do Azure através do [registos das aplicações](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade) experiência. Objectos da aplicação descrevem o aplicativo para o Azure AD e podem ser considerados a definição da aplicação, permitindo que o serviço para saber como emitir tokens para a aplicação com base nas suas definições. O objeto de aplicativo só existirá no seu diretório raiz, mesmo se for uma aplicação multi-inquilino que suporta os principais de serviço noutros diretórios. O objeto de aplicativo pode incluir qualquer um dos seguintes procedimentos (como informações adicionais, bem como não mencionadas aqui):
* Nome, o logótipo e o publicador
* URLs de Resposta
* Segredos (simétricas e/ou assimétricas as chaves utilizadas para autenticar a aplicação)
* Dependências de API (OAuth)
* APIs/recursos/âmbitos do publicados (OAuth)
* Funções (RBAC) da aplicação
* Metadados SSO e configuração
* Aprovisionamento de metadados e configuração de utilizadores
* Metadados de proxy e configuração

Objetos de aplicativo podem ser criados por meio de vários caminhos, incluindo:
* Registos de aplicação no portal do Azure
* Criando um novo aplicativo usando o Visual Studio e configurando-o para utilizar a autenticação do Azure AD
* Quando um administrador adiciona uma aplicação da Galeria de aplicações (que também irá criar um serviço principal)
* Com o Microsoft Graph API, o Azure AD Graph API ou o PowerShell para criar uma nova aplicação
* Muitos outros incluindo várias experiências de desenvolvimento no Azure e na API experiências com o explorer em centros de desenvolvedores

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Quais são os principais de serviço e onde eles vêm?
Pode gerir [principais de serviço](app-objects-and-service-principals.md#service-principal-object) no portal do Azure através do [aplicações empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) experiência. Principais de serviço são o que regem uma aplicação a ligar ao Azure AD e podem ser considerados a instância da aplicação no seu diretório. Para um determinado aplicativo, ele pode ter no máximo um objeto de aplicativo (que é registado num diretório "página inicial") e um ou mais objetos principais de serviço que representa instâncias da aplicação de cada diretório em que ele funciona. 

O principal de serviço pode incluir:

* Fazer uma cópia de uma referência a um objeto de aplicativo por meio da propriedade de ID de aplicação
* Registos de utilizador local e as atribuições de funções de aplicação de grupo
* Registos de permissões de utilizador e administrador locais concedidas à aplicação
  * Por exemplo: permissão para a aplicação para aceder ao e-mail de um utilizador específico
* Registos de políticas locais, incluindo a política de acesso condicional
* Registos de alternativas definições locais para uma aplicação
  * Regras de transformação de afirmações
  * Mapeamentos de atributos (aprovisionamento de utilizadores)
  * Funções de diretório específico de aplicação (se o aplicativo oferece suporte a funções personalizadas)
  * Nome de diretório específico ou logótipo

Como objetos de aplicativo, os principais de serviço também podem ser criados por meio de vários caminhos, incluindo:

* Quando os utilizadores iniciam sessão a uma aplicação de terceiros integradas no Azure AD
  * Durante o início de sessão, é pedido aos utilizadores para conceder permissão à aplicação para aceder ao seu perfil e outras permissões. A primeira pessoa para dar consentimento faz com que um principal de serviço que representa o aplicativo a ser adicionado ao diretório.
* Quando os utilizadores iniciam sessão ao Microsoft online services como [do Office 365](https://products.office.com/)
  * Quando subscreve o Office 365 ou iniciar uma versão de avaliação, uma ou mais principais de serviço são criados no diretório que representa os vários serviços que são utilizados para fornecer todas as funcionalidades associadas do Office 365.
  * Alguns serviços do Office 365 como o SharePoint criar principais de serviço de forma contínua para permitir a comunicação segura entre componentes, incluindo fluxos de trabalho.
* Quando um administrador adiciona uma aplicação da Galeria de aplicações (isso também irá criar um objeto de aplicativo subjacente)
* Adicionar uma aplicação para utilizar o [Proxy de aplicações do Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Ligar uma aplicação para início de sessão único sobre a utilização de SAML ou palavra-passe de início de sessão único (SSO)
* Através de programação com o Azure AD Graph API ou o PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Como são objetos de aplicação e os principais de serviço relacionados entre si?
Um aplicativo tem um objeto de aplicativo no respetivo diretório raiz que é referenciado por um ou mais principais de serviço em cada um dos diretórios de onde ele opera (incluindo o diretório de raiz do aplicativo).
![Um diagrama que ilustra como os objetos de aplicação e os principais de serviço interagem entre si e instâncias do Azure AD.][apps_service_principals_directory]

No diagrama anterior, a Microsoft tem dois diretórios internamente (mostrado no lado esquerdo) que utiliza para publicar aplicações:

* Um para Microsoft Apps (diretório de serviços da Microsoft)
* Um para aplicações previamente integradas de terceiros (diretório de Galeria de aplicações)

Os publicadores/fornecedores de aplicativos que integrar com o Azure AD têm de ter um diretório de publicação (mostrado à direita como "Diretório de SaaS alguns").

Aplicativos que adiciona a próprio (representados como **aplicação (sua)** no diagrama) incluem:

* Aplicações que desenvolveu (integradas com o Azure AD)
* Aplicações ligadas para início de sessão único
* Aplicações publicadas com o proxy de aplicações do Azure AD

### <a name="notes-and-exceptions"></a>Notas e exceções
* Nem todos os principais de serviço do ponto de volta para um objeto de aplicativo. Quando o Azure AD foi originalmente criado os serviços fornecidos aos aplicativos eram mais limitados e o principal de serviço foi suficiente para estabelecer uma identidade de aplicação. O principal de serviço original era mais próximo na forma da conta de serviço do Windows Server Active Directory. Por esse motivo, é possível criar principais de serviço por meio de caminhos diferentes, tais como utilizar o Azure AD PowerShell, sem criar primeiramente um objeto de aplicativo. O Azure AD Graph API requer um objeto de aplicativo antes de criar um serviço principal.
* Nem todas as informações descritas acima atualmente é exposta por meio de programação. O seguinte só está disponível na interface do Usuário:
  * Regras de transformação de afirmações
  * Mapeamentos de atributos (aprovisionamento de utilizadores)
* Para obter informações mais detalhadas sobre o principal de serviço e os objetos de aplicação, consulte a documentação de referência da API de REST do Azure AD Graph:
  * [Aplicação](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Principal de serviço](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Por que os aplicativos integram com o Azure AD?
São adicionadas aplicações ao Azure AD para tirar partido de um ou mais dos serviços que oferece, incluindo:

* Aplicação de autenticação e autorização
* Autorização e autenticação de utilizador
* SSO com palavra-passe ou Federação
* Aprovisionamento de utilizadores e sincronização
* Utilize o diretório para definir funções de aplicação para executar a autorização baseada em funções de controlo de acesso baseado em Funções - verifica num aplicativo
* Serviços de autorização de OAuth - utilizados pelo Office 365 e outras aplicações da Microsoft para autorizar o acesso a APIs/recursos
* Publicação de aplicações e o proxy - publicar uma aplicação de uma rede privada à internet

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Quem tem permissão para adicionar aplicações para a minha instância do Azure AD?
Embora haja algumas tarefas que apenas os administradores globais podem efetuar (como adicionar aplicativos a partir da Galeria de aplicações e configurar uma aplicação para utilizar o Proxy de aplicações) por predefinição todos os utilizadores no seu diretório de ter direitos para registar a aplicação objetos que eles estão o desenvolvimento e a decisão sobre quais aplicativos que partilham/dar acesso aos dados organizacionais através de consentimento. Se uma pessoa é o primeiro utilizador no seu diretório para iniciar sessão a uma aplicação e conceder autorização, o que irá criar um principal de serviço no seu inquilino; caso contrário, serão armazenadas as informações de concessão de consentimento no principal de serviço existente.

Permitir que os utilizadores registar e dar consentimento a aplicações pode inicialmente som relacionadas, mas tenha em atenção o seguinte:


* Aplicativos foram capazes de tirar partido do Windows Server Active Directory para autenticação de utilizador por muitos anos sem a necessidade do aplicativo para ser registado ou gravadas no diretório. Agora a organização vai ter visibilidade melhorada para exatamente quantas aplicações estão a utilizar o diretório e com que finalidade.
* Delegar essas responsabilidades aos utilizadores refuta a necessidade de um registo de aplicação orientada por administrador e o processo de publicação. Com o Active Directory Federation Services (ADFS) era provável que um administrador tinha que adicionar uma aplicação como uma entidade confiadora em nome dos programadores. Agora os desenvolvedores podem Self-Service.
* Os utilizadores iniciar sessão em aplicações através das respetivas contas de organização para fins comerciais são algo bom. Se, em seguida, eles os deixam a organização que automaticamente eles perderão o acesso à sua conta na aplicação que estavam a utilizar.
* Ter um registo de dados que foi partilhados com a qual o aplicativo é algo bom. Os dados são mais transportable que nunca e é útil ter um registo claro de quem partilhado que dados quais aplicativos.
* Os proprietários de API que utilizam o Azure AD para OAuth decidir exatamente quais permissões os usuários podem conceder aos aplicativos e as permissões que requerem um administrador para aceitar. Apenas os administradores podem dar consentimento a permissões mais significativas e os âmbitos de maior, enquanto o consentimento do utilizador tem um âmbito para os recursos e os dados dos utilizadores.
* Quando um utilizador adiciona ou permite que um aplicativo aceder aos respetivos dados, o evento pode ser auditado para que pode ver os relatórios de auditoria no portal do Azure para determinar a forma como foi adicionada uma aplicação para o diretório.

Se pretender continuar a impedir que os utilizadores no seu diretório de registo de aplicações e de iniciar sessão em aplicações sem a aprovação de administrador, existem duas definições que podem ser alteradas para desativar a esses recursos:

* Impedir os utilizadores de consentir a aplicações no seu próprio benefício:
  1. No portal do Azure, vá para o [definições de utilizador](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) secção em aplicações empresariais.
  2. Alteração **os utilizadores podem dar consentimento a aplicações acedam aos dados da empresa em nome deles** ao **não**. 
     
     > [!NOTE]
     > Se optar por desativar o consentimento do utilizador, um administrador será necessário para dar consentimento para qualquer novo aplicativo que o utilizador precisa de utilizar.    
* Para impedir que os utilizadores registar as suas aplicações:
  1. No portal do Azure, vá para o [definições de utilizador](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) secção em Azure Active Directory
  2. Alteração **os utilizadores podem registar aplicações** ao **não**.

> [!NOTE]
> A própria Microsoft utiliza a configuração predefinida, com os utilizadores podem registar aplicações e autorizar aplicações em seu próprio nome.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg

