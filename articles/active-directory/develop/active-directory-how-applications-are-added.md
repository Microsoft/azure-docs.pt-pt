---
title: Como e por que os aplicativos são adicionados ao Azure Active Directory
titleSuffix: Microsoft identity platform
description: O que significa que um aplicativo seja adicionado ao Azure AD e como eles ficam lá?
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/04/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: elisol, lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: ebf6b9a07e775c76188dcebece011b01e90fbcf5
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803446"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Como e por que os aplicativos são adicionados ao Azure AD

Há duas representações de aplicativos no Azure AD: 
* [Objetos de aplicativo](app-objects-and-service-principals.md#application-object) -embora haja [exceções](#notes-and-exceptions), os objetos de aplicativo podem ser considerados a definição de um aplicativo.
* [Entidades de serviço](app-objects-and-service-principals.md#service-principal-object) – podem ser consideradas uma instância de um aplicativo. As entidades de serviço geralmente fazem referência a um objeto de aplicativo e um objeto de aplicativo pode ser referenciado por várias entidades de serviço entre os diretórios.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>O que são objetos de aplicativo e de onde eles vêm?
Você pode gerenciar [objetos de aplicativo](app-objects-and-service-principals.md#application-object) no portal do Azure por meio da experiência de [registros do aplicativo](https://aka.ms/appregistrations) . Os objetos de aplicativo descrevem o aplicativo para o Azure AD e podem ser considerados a definição do aplicativo, permitindo que o serviço saiba como emitir tokens para o aplicativo com base em suas configurações. O objeto de aplicativo só existirá em seu diretório base, mesmo se for um aplicativo multilocatário que dá suporte a entidades de serviço em outros diretórios. O objeto de aplicativo pode incluir qualquer um dos seguintes (bem como informações adicionais não mencionadas aqui):
* Nome, logotipo e Publicador
* Redirecionar URIs
* Segredos (chaves simétricas e/ou assimétricas usadas para autenticar o aplicativo)
* Dependências de API (OAuth)
* APIs/recursos/escopos publicados (OAuth)
* Funções de aplicativo (RBAC)
* Metadados e configuração de SSO
* Configuração e metadados de provisionamento do usuário
* Configuração e metadados de proxy

Os objetos de aplicativo podem ser criados por meio de vários caminhos, incluindo:
* Registros de aplicativo no portal do Azure
* Criando um novo aplicativo usando o Visual Studio e Configurando-o para usar a autenticação do Azure AD
* Quando um administrador adiciona um aplicativo da Galeria de aplicativos (que também criará uma entidade de serviço)
* Usando a API Microsoft Graph, o Azure AD API do Graph ou o PowerShell para criar um novo aplicativo
* Muitos outros incluindo várias experiências de desenvolvedor no Azure e em experiências do Gerenciador de API entre os centros de desenvolvedores

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>O que são as entidades de serviço e de onde elas vêm?
Você pode gerenciar [entidades de serviço](app-objects-and-service-principals.md#service-principal-object) no portal do Azure por meio da experiência de [aplicativos empresariais](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) . As entidades de serviço são o que governa um aplicativo se conectando ao Azure AD e pode ser considerado a instância do aplicativo em seu diretório. Para qualquer aplicativo específico, ele pode ter no máximo um objeto de aplicativo (que é registrado em um diretório "Home") e um ou mais objetos de entidade de serviço que representam instâncias do aplicativo em todos os diretórios nos quais ele atua. 

A entidade de serviço pode incluir:

* Uma referência de volta a um objeto de aplicativo por meio da propriedade ID do aplicativo
* Registros de atribuições de função de aplicativo de usuário e grupo locais
* Registros de permissões de usuário local e de administrador concedidas ao aplicativo
  * Por exemplo: permissão para que o aplicativo acesse o email de um usuário específico
* Registros de políticas locais, incluindo a política de acesso condicional
* Registros de configurações locais alternativas para um aplicativo
  * Regras de transformação de declarações
  * Mapeamentos de atributo (provisionamento de usuário)
  * Funções de aplicativo específicas do diretório (se o aplicativo oferecer suporte a funções personalizadas)
  * Nome ou logotipo específico do diretório

Assim como os objetos de aplicativo, as entidades de serviço também podem ser criadas por meio de vários caminhos, incluindo:

* Quando os usuários entram em um aplicativo de terceiros integrado ao Azure AD
  * Durante a entrada, os usuários são solicitados a conceder permissão ao aplicativo para acessar seu perfil e outras permissões. A primeira pessoa a dar consentimento faz com que uma entidade de serviço represente o aplicativo a ser adicionado ao diretório.
* Quando os usuários entram no Microsoft serviços online como o [Office 365](https://products.office.com/)
  * Quando você assina o Office 365 ou inicia uma avaliação, uma ou mais entidades de serviço são criadas no diretório que representa os vários serviços que são usados para fornecer toda a funcionalidade associada ao Office 365.
  * Alguns serviços do Office 365, como o SharePoint, criam entidades de serviço em uma base contínua para permitir a comunicação segura entre componentes, incluindo fluxos de trabalho.
* Quando um administrador adiciona um aplicativo da Galeria de aplicativos (isso também criará um objeto de aplicativo subjacente)
* Adicionar um aplicativo para usar o [proxy de aplicativo do AD do Azure](/azure/active-directory/manage-apps/application-proxy)
* Conectar um aplicativo para logon único usando SAML ou SSO (logon único) de senha
* Programaticamente por meio do API do Graph do Azure AD ou do PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Como os objetos de aplicativo e as entidades de serviço estão relacionados entre si?

Um aplicativo tem um objeto de aplicativo em seu diretório base que é referenciado por uma ou mais entidades de serviço em cada um dos diretórios onde ele opera (incluindo o diretório base do aplicativo).

![Mostra a relação entre os objetos de aplicativo e as entidades de serviço][apps_service_principals_directory]

No diagrama anterior, a Microsoft mantém dois diretórios internamente (mostrados à esquerda) que ele usa para publicar aplicativos:

* Um para aplicativos da Microsoft (diretório de serviços da Microsoft)
* Um para aplicativos de terceiros previamente integrados (diretório da Galeria de aplicativos)

Editores/fornecedores de aplicativos que se integram ao Azure AD precisam ter um diretório de publicação (mostrado à direita como "algum diretório SaaS").

Os aplicativos que você mesmo adiciona (representados como **aplicativo (seu)** no diagrama) incluem:

* Aplicativos que você desenvolveu (integrado ao Azure AD)
* Aplicativos que você conectou para logon único
* Aplicativos que você publicou usando o proxy de aplicativo do Azure AD

### <a name="notes-and-exceptions"></a>Observações e exceções

* Nem todas as entidades de serviço apontam de volta para um objeto de aplicativo. Quando o Azure AD foi originalmente criado, os serviços fornecidos aos aplicativos eram mais limitados e a entidade de serviço era suficiente para estabelecer uma identidade de aplicativo. A entidade de serviço original estava mais perto da forma para a conta de serviço de Active Directory do Windows Server. Por esse motivo, ainda é possível criar entidades de serviço por meio de caminhos diferentes, como usar o PowerShell do Azure AD, sem primeiro criar um objeto de aplicativo. O API do Graph do Azure AD requer um objeto de aplicativo antes de criar uma entidade de serviço.
* Nem todas as informações descritas acima são atualmente expostas por meio de programação. Os itens a seguir estão disponíveis apenas na interface do usuário:
  * Regras de transformação de declarações
  * Mapeamentos de atributo (provisionamento de usuário)
* Para obter informações mais detalhadas sobre a entidade de serviço e objetos de aplicativo, consulte a documentação de referência da API REST do Azure AD Graph:
  * [Aplicativo](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Entidade de serviço](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Por que os aplicativos se integram ao Azure AD?

Os aplicativos são adicionados ao Azure AD para aproveitar um ou mais dos serviços que ele fornece, incluindo:

* Autenticação e autorização de aplicativos
* Autenticação e autorização de usuário
* SSO usando Federação ou senha
* Provisionamento e sincronização de usuários
* Controle de acesso baseado em função-use o diretório para definir funções de aplicativo para executar verificações de autorização baseadas em função em um aplicativo
* Serviços de autorização OAuth-usados pelo Office 365 e outros aplicativos da Microsoft para autorizar o acesso a APIs/recursos
* Publicação de aplicativos e proxy – publique um aplicativo de uma rede privada para a Internet

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Quem tem permissão para adicionar aplicativos à minha instância do Azure AD?

Embora haja algumas tarefas que somente os administradores globais podem fazer (como adicionar aplicativos da Galeria de aplicativos e configurar um aplicativo para usar o proxy de aplicativo) por padrão, todos os usuários em seu diretório têm direitos para registrar objetos de aplicativo que Eles estão desenvolvendo e se deparam com quais aplicativos eles compartilham/fornecem acesso a seus dados organizacionais por meio de consentimento. Se uma pessoa for o primeiro usuário em seu diretório para entrar em um aplicativo e conceder consentimento, isso criará uma entidade de serviço em seu locatário; caso contrário, as informações de concessão de consentimento serão armazenadas na entidade de serviço existente.

Permitir que os usuários se registrem e consentissem em aplicativos podem inicialmente se parecer com a relação, mas tenha em mente o seguinte:


* Os aplicativos foram capazes de aproveitar o Windows Server Active Directory para autenticação de usuário por muitos anos sem exigir que o aplicativo seja registrado ou registrado no diretório. Agora, a organização terá visibilidade aprimorada para exatamente quantos aplicativos estão usando o diretório e para qual finalidade.
* A delegação dessas responsabilidades aos usuários nega a necessidade de um processo de publicação e registro de aplicativo controlado por administrador. Com o Serviços de Federação do Active Directory (AD FS) (ADFS) era provável que um administrador tivesse que adicionar um aplicativo como uma terceira parte confiável em nome de seus desenvolvedores. Agora os desenvolvedores podem autoatendimento.
* Os usuários que entrarem em aplicativos que usam suas contas organizacionais para fins comerciais é uma coisa boa. Se eles subseqüentemente deixarem a organização, eles perderão automaticamente o acesso à sua conta no aplicativo que estavam usando.
* Ter um registro de quais dados foram compartilhados com qual aplicativo é algo bom. Os dados são mais transportáveis do que nunca e é útil ter um registro claro de quem compartilhou quais dados com quais aplicativos.
* Proprietários de API que usam o Azure AD para OAuth decidem exatamente quais permissões os usuários podem conceder a aplicativos e quais permissões exigem que um administrador concorde. Somente os administradores podem consentir maiores escopos e permissões mais significativas, enquanto o consentimento do usuário é definido para os próprios dados e funcionalidades dos usuários.
* Quando um usuário adiciona ou permite que um aplicativo acesse seus dados, o evento pode ser auditado para que você possa exibir os relatórios de auditoria dentro do portal do Azure para determinar como um aplicativo foi adicionado ao diretório.

Se você ainda quiser impedir que os usuários em seu diretório registrem aplicativos e entrem em aplicativos sem aprovação do administrador, há duas configurações que podem ser alteradas para desativar esses recursos:

* Para impedir que os usuários consentissem em aplicativos em seu próprio nome:
  1. Na portal do Azure, vá para a seção [configurações de usuário](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) em aplicativos empresariais.
  2. Alterar **os usuários podem consentir os aplicativos que acessam os dados da empresa em seu nome** para **não**.
     
     > [!NOTE]
     > Se você decidir desativar o consentimento do usuário, será necessário que um administrador consentido em qualquer novo aplicativo que um usuário precise usar.

* Para impedir que os usuários registrem seus próprios aplicativos:
  1. Na portal do Azure, vá para a seção [configurações do usuário](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) em Azure Active Directory
  2. Alterar **os usuários podem registrar aplicativos** **no.**

> [!NOTE]
> A própria Microsoft usa a configuração padrão com os usuários capazes de registrar aplicativos e dar consentimento a aplicativos em seu próprio nome.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
