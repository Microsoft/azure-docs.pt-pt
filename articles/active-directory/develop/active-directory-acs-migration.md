---
title: Migrar do serviço de controle de acesso do Azure | Microsoft Docs
description: Saiba mais sobre as opções para mover aplicativos e serviços do ACS (serviço de controle de acesso) do Azure.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3daf44ee29a2f7b29eec9215876ca6edc18a5800
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325046"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Como: Migrardo Serviço de Controlo de Acesso do Microsoft Azure

Serviço de Controle de Acesso do Microsoft Azure (ACS), um serviço do Azure Active Directory (Azure AD), será desativado em 7 de novembro de 2018. Os aplicativos e serviços que atualmente usam o controle de acesso devem ser totalmente migrados para um mecanismo de autenticação diferente. Este artigo descreve as recomendações para os clientes atuais, à medida que você planeja substituir o uso do controle de acesso. Se você não usar o controle de acesso no momento, não precisará realizar nenhuma ação.

## <a name="overview"></a>Descrição geral

O controle de acesso é um serviço de autenticação de nuvem que oferece uma maneira fácil de autenticar e autorizar usuários para acesso aos seus aplicativos e serviços Web. Ele permite que muitos recursos de autenticação e autorização sejam fatorados fora do seu código. O controle de acesso é usado principalmente por desenvolvedores e arquitetos de Microsoft .NET clientes, aplicativos Web ASP.NET e serviços Web do Windows Communication Foundation (WCF).

Os casos de uso do controle de acesso podem ser divididos em três categorias principais:

- Autenticação em determinados serviços de nuvem da Microsoft, incluindo o Azure Service Bus e o Dynamics CRM. Os aplicativos cliente obtêm tokens do controle de acesso para autenticar para esses serviços para executar várias ações.
- Adicionar autenticação a aplicativos Web, personalizados e predefinidos (como o SharePoint). Usando a autenticação "passiva" do controle de acesso, os aplicativos Web podem dar suporte à entrada com um conta Microsoft (anteriormente chamado Live ID) e com contas do Google, Facebook, Yahoo, Azure AD e Serviços de Federação do Active Directory (AD FS) (AD FS).
- Proteção de serviços Web personalizados com tokens emitidos pelo controle de acesso. Usando a autenticação "ativa", os serviços Web podem garantir que eles permitam acesso somente a clientes conhecidos que se autenticaram com o controle de acesso.

Cada um desses casos de uso e suas estratégias de migração recomendadas são discutidos nas seções a seguir.

> [!WARNING]
> Na maioria dos casos, alterações significativas de código são necessárias para migrar aplicativos e serviços existentes para tecnologias mais novas. Recomendamos que você comece imediatamente a planejar e executar a migração para evitar possíveis interrupções ou tempo de inatividade.

O controle de acesso tem os seguintes componentes:

- Um serviço de token de segurança (STS), que recebe solicitações de autenticação e emite tokens de segurança em retorno.
- O portal clássico do Azure, no qual você cria, exclui e habilita e desabilita namespaces do Access Control.
- Um portal de gerenciamento de controle de acesso separado, no qual você personaliza e configura namespaces do controle de acesso.
- Um serviço de gerenciamento, que pode ser usado para automatizar as funções dos portais.
- Um mecanismo de regra de transformação de token, que pode ser usado para definir uma lógica complexa para manipular o formato de saída de tokens que controlam os problemas de controle de acesso.

Para usar esses componentes, você deve criar um ou mais namespaces do Access Control. Um *namespace* é uma instância dedicada do controle de acesso com a qual seus aplicativos e serviços se comunicam. Um namespace é isolado de todos os outros clientes do controle de acesso. Outros clientes do controle de acesso usam seus próprios namespaces. Um namespace no controle de acesso tem uma URL dedicada semelhante a esta:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Toda a comunicação com o STS e as operações de gerenciamento são feitas nesta URL. Você usa caminhos diferentes para finalidades diferentes. Para determinar se seus aplicativos ou serviços usam o controle de acesso, monitore qualquer tráfego&lt;para&gt;https://namespace. AccessControl.Windows.net. Qualquer tráfego para essa URL é manipulado pelo controle de acesso e precisa ser descontinuado. 

A exceção a isso é qualquer tráfego para `https://accounts.accesscontrol.windows.net`. O tráfego para essa URL já é tratado por um serviço diferente e **não é** afetado pela substituição do controle de acesso. 

Para obter mais informações sobre o controle de acesso, consulte [serviço de controle de acesso 2,0 (arquivado)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Descubra quais dos seus aplicativos serão afetados

Siga as etapas nesta seção para descobrir quais dos seus aplicativos serão afetados pela desativação do ACS.

### <a name="download-and-install-acs-powershell"></a>Baixar e instalar o ACS PowerShell

1. Vá para a Galeria do PowerShell e baixe [ACS. Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
1. Instalar o módulo executando

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

1. Obter uma lista de todos os comandos possíveis executando

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Para obter ajuda sobre um comando específico, execute:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    em `[Command-Name]` que é o nome do comando do ACS.

### <a name="list-your-acs-namespaces"></a>Listar seus namespaces do ACS

1. Conecte-se ao ACS usando o cmdlet **Connect-AcsAccount** .
  
    Talvez seja necessário executar `Set-ExecutionPolicy -ExecutionPolicy Bypass` o antes que você possa executar comandos e ser o administrador dessas assinaturas para executar os comandos.

1. Liste suas assinaturas do Azure disponíveis usando o cmdlet **Get-AcsSubscription** .
1. Liste seus namespaces do ACS usando o cmdlet **Get-AcsNamespace** .

### <a name="check-which-applications-will-be-impacted"></a>Verificar quais aplicativos serão afetados

1. Use o namespace da etapa anterior e vá para`https://<namespace>.accesscontrol.windows.net`

    Por exemplo, se um dos namespaces for contoso-Test, vá para`https://contoso-test.accesscontrol.windows.net`

1. Em **relações de confiança**, selecione **aplicativos de terceira parte confiável** para ver a lista de aplicativos que serão afetados pela desativação do ACS.
1. Repita as etapas de 1-2 para quaisquer outros namespaces do ACS que você tiver.

## <a name="retirement-schedule"></a>Agendamento de aposentadoria

A partir de novembro de 2017, todos os componentes de controle de acesso são totalmente suportados e operacionais. A única restrição é que você [não pode criar novos namespaces de controle de acesso por meio do portal clássico do Azure](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Este é o agendamento para a substituição de componentes de controle de acesso:

- **Novembro de 2017**:  A experiência de administração do Azure AD no portal clássico do Azure [é](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/)desativada. Neste ponto, o gerenciamento de namespace para o controle de acesso está disponível em uma nova URL `https://manage.windowsazure.com?restoreClassic=true`dedicada:. Use essa URl para exibir os namespaces existentes, habilitar e desabilitar namespaces e excluir namespaces, se você optar por.
- **2 de abril de 2018**: O portal clássico do Azure está completamente desativado, o que significa que o gerenciamento do namespace do controle de acesso não está mais disponível por meio de qualquer URL. Neste ponto, você não pode desabilitar ou habilitar, excluir ou enumerar seus namespaces de controle de acesso. No entanto, o portal de gerenciamento de controle de acesso será totalmente `https://\<namespace\>.accesscontrol.windows.net`funcional e localizado em. Todos os outros componentes do controle de acesso continuam a operar normalmente.
- **7 de novembro de 2018**: Todos os componentes do controle de acesso são desligados permanentemente. Isso inclui o portal de gerenciamento de controle de acesso, o serviço de gerenciamento, o STS e o mecanismo de regra de transformação de token. Neste ponto, todas as solicitações enviadas para o controle de acesso ( \<localizada\>em namespace. AccessControl.Windows.net) falham. Você deve ter migrado todos os aplicativos e serviços existentes para outras tecnologias bem antes dessa hora.

> [!NOTE]
> Uma política desabilita namespaces que não solicitaram um token por um período de tempo. A partir do início de setembro de 2018, esse período de tempo está atualmente a 14 dias de inatividade, mas isso será reduzido para 7 dias de inatividade nas próximas semanas. Se você tiver namespaces de controle de acesso que estão atualmente desabilitados, você poderá [baixar e instalar o ACS PowerShell](#download-and-install-acs-powershell) para reabilitar os namespaces.

## <a name="migration-strategies"></a>Estratégias de migração

As seções a seguir descrevem as recomendações de alto nível para a migração do controle de acesso para outras tecnologias da Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Clientes dos serviços em nuvem da Microsoft

Cada serviço de nuvem da Microsoft que aceita tokens emitidos pelo controle de acesso agora dá suporte a pelo menos uma forma alternativa de autenticação. O mecanismo de autenticação correto varia de acordo com cada serviço. Recomendamos que você consulte a documentação específica de cada serviço para obter orientação oficial. Para sua conveniência, cada conjunto de documentação é fornecido aqui:

| Serviço | Orientação |
| ------- | -------- |
| Service Bus do Azure | [Migrar para assinaturas de acesso compartilhado](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Retransmissão do barramento de serviço do Azure | [Migrar para assinaturas de acesso compartilhado](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Cache gerenciado do Azure | [Migrar para o cache do Azure para Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [Migrar para o API de Serviços Cognitivos](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Serviços BizTalk | [Migrar para o recurso de aplicativos lógicos do serviço Azure App](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Serviços de mídia do Azure | [Migrar para a autenticação do Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Atualizar o agente de backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>Clientes do SharePoint

Os clientes do SharePoint 2013, 2016 e SharePoint Online têm muito uso o ACS para fins de autenticação em cenários de nuvem, locais e híbridos. Alguns recursos do SharePoint e casos de uso serão afetados pela desativação do ACS, enquanto outros não vão. A tabela abaixo resume as diretrizes de migração para alguns dos recursos mais populares do SharePoint que aproveitam o ACS:

| Funcionalidade | Orientação |
| ------- | -------- |
| Autenticando usuários do Azure AD | Anteriormente, o Azure AD não dava suporte a tokens SAML 1,1 exigidos pelo SharePoint para autenticação, e o ACS era usado como um intermediário que tornava o SharePoint compatível com os formatos de token do Azure AD. Agora, você pode [conectar o SharePoint diretamente ao Azure ad usando aplicativo Azure ad Galeria do SharePoint no aplicativo local](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial). |
| [Autenticação de aplicativo & autenticação de servidor para servidor no SharePoint local](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Não afetado pela desativação do ACS; nenhuma alteração é necessária. | 
| [Autorização de baixa confiança para suplementos do SharePoint (provedor hospedado e SharePoint hospedado)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Não afetado pela desativação do ACS; nenhuma alteração é necessária. |
| [Pesquisa híbrida na nuvem do SharePoint](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Não afetado pela desativação do ACS; nenhuma alteração é necessária. |

### <a name="web-applications-that-use-passive-authentication"></a>Aplicativos Web que usam autenticação passiva

Para aplicativos Web que usam o controle de acesso para autenticação de usuário, o controle de acesso fornece os seguintes recursos e funcionalidades para desenvolvedores e arquitetos de aplicativos Web:

- Integração profunda com o Windows Identity Foundation (WIF).
- Federação com contas do Google, Facebook, Yahoo, Azure Active Directory e AD FS e contas da Microsoft.
- Suporte para os seguintes protocolos de autenticação: OAuth 2,0 Draft 13, WS-Trust e especificação Web Services Federation (WS-Federation).
- Suporte para os seguintes formatos de token: JSON Web token (JWT), SAML 1,1, SAML 2,0 e Simple Web token (SWT).
- Uma experiência de descoberta de realm inicial, integrada ao WIF, que permite aos usuários escolher o tipo de conta que eles usam para entrar. Essa experiência é hospedada pelo aplicativo Web e é totalmente personalizável.
- Transformação de token que permite a personalização avançada das declarações recebidas pelo aplicativo Web do controle de acesso, incluindo:
    - Passar declarações de provedores de identidade.
    - Adicionando declarações personalizadas adicionais.
    - Lógica if-then simples para emitir declarações sob determinadas condições.

Infelizmente, não há um serviço que ofereça todos esses recursos equivalentes. Você deve avaliar quais recursos do controle de acesso precisa e, em seguida, escolher entre usar [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure ad B2C) ou outro serviço de autenticação de nuvem.

#### <a name="migrate-to-azure-active-directory"></a>Migrar para o Azure Active Directory

Um caminho a ser considerado é a integração de seus aplicativos e serviços diretamente com o Azure AD. O Azure AD é o provedor de identidade baseado em nuvem para contas corporativas ou de estudante da Microsoft. O Azure AD é o provedor de identidade para o Office 365, o Azure e muito mais. Ele fornece recursos de autenticação federada semelhantes para o controle de acesso, mas não dá suporte a todos os recursos de controle de acesso. 

O exemplo principal é Federação com provedores de identidade social, como Facebook, Google e Yahoo. Se os usuários entrarem com esses tipos de credenciais, o Azure AD não será a solução para você. 

O Azure AD também não suporta necessariamente os mesmos protocolos de autenticação que o controle de acesso. Por exemplo, embora o controle de acesso e o Azure AD ofereçam suporte a OAuth, há diferenças sutis entre cada implementação. Implementações diferentes exigem que você modifique o código como parte de uma migração.

No entanto, o Azure AD fornece várias vantagens em potencial para acessar clientes de controle. Ele dá suporte nativo a contas corporativas ou de estudante da Microsoft hospedadas na nuvem, que são comumente usadas por clientes do controle de acesso. 

Um locatário do Azure AD também pode ser federado a uma ou mais instâncias do Active Directory local por meio de AD FS. Dessa forma, seu aplicativo pode autenticar usuários baseados em nuvem e usuários hospedados localmente. Ele também dá suporte ao protocolo WS-Federation, o que o torna relativamente simples de integrar a um aplicativo Web usando o WIF.

A tabela a seguir compara os recursos do controle de acesso que são relevantes para aplicativos Web com os recursos que estão disponíveis no Azure AD. 

Em um alto nível, *Azure Active Directory é provavelmente a melhor opção para sua migração se você permitir que os usuários entrem somente com suas contas corporativas ou de estudante da Microsoft*.

| Funcionalidade | Suporte ao controle de acesso | Suporte do Azure AD |
| ---------- | ----------- | ---------------- |
| **Tipos de contas** | | |
| Contas corporativas ou de estudante da Microsoft | Suportadas | Suportadas |
| Contas do Windows Server Active Directory e AD FS |-Com suporte via Federação com um locatário do Azure AD <br />-Com suporte via Federação direta com AD FS | Com suporte apenas via Federação com um locatário do Azure AD | 
| Contas de outros sistemas de gerenciamento de identidades corporativas |-Possível via Federação com um locatário do Azure AD <br />-Com suporte via Federação direta | Possível via Federação com um locatário do Azure AD |
| Contas da Microsoft para uso pessoal | Suportadas | Com suporte por meio do protocolo OAuth do Azure AD v 2.0, mas não de outros protocolos | 
| Contas do Facebook, Google, Yahoo | Suportadas | Sem suporte de qualquer |
| **Compatibilidade entre protocolos e SDK** | | |
| WIF | Suportadas | Com suporte, mas instruções limitadas estão disponíveis |
| WS-Federation | Suportadas | Suportadas |
| OAuth 2.0 | Suporte para o rascunho 13 | Suporte para RFC 6749, a especificação mais moderna |
| WS-Trust | Suportadas | Não suportado |
| **Formatos de token** | | |
| JWT | Com suporte na versão beta | Suportadas |
| SAML 1,1 | Suportadas | Pré-visualização |
| SAML 2.0 | Suportadas | Suportadas |
| SWT | Suportadas | Não suportado |
| **Personalizações** | | |
| Interface do usuário de Home realm de descoberta/seleção de conta personalizável | Código para download que pode ser incorporado em aplicativos | Não suportado |
| Carregar certificados de assinatura de token personalizados | Suportadas | Suportadas |
| Personalizar declarações em tokens |-Passar declarações de entrada de provedores de identidade<br />-Obter token de acesso do provedor de identidade como uma declaração<br />-Emitir declarações de saída com base em valores de declarações de entrada<br />-Emitir declarações de saída com valores constantes |-Não é possível passar declarações de provedores de identidade federada<br />-Não é possível obter o token de acesso do provedor de identidade como uma declaração<br />-Não é possível emitir declarações de saída com base em valores de declarações de entrada<br />-Pode emitir declarações de saída com valores constantes<br />-Pode emitir declarações de saída com base nas propriedades de usuários sincronizadas com o Azure AD |
| **Automatização** | | |
| Automatizar tarefas de configuração e gerenciamento | Com suporte via serviço de gerenciamento de controle de acesso | Com suporte via Microsoft Graph e Azure AD API do Graph |

Se você decidir que o Azure AD é o melhor caminho de migração para seus aplicativos e serviços, você deve estar ciente de duas maneiras de integrar seu aplicativo ao Azure AD.

Para usar o WS-Federation ou o WIF para integrar com o Azure AD, é recomendável seguir a abordagem descrita em [Configurar logon único federado para um aplicativo](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)inexistente na galeria. O artigo refere-se à configuração do Azure AD para logon único baseado em SAML, mas também funciona para a configuração do WS-Federation. Seguir essa abordagem requer uma licença de Azure AD Premium. Essa abordagem tem duas vantagens:

- Você Obtém a flexibilidade total da personalização do token do Azure AD. Você pode personalizar as declarações emitidas pelo Azure AD para corresponder às declarações emitidas pelo controle de acesso. Isso inclui, especialmente, a declaração de ID de usuário ou identificador de nome. Para continuar a receber identificadores de usuário consistentes para seus usuários depois de alterar as tecnologias, verifique se as IDs de usuário emitidas pelo Azure AD correspondem àquelas emitidas pelo controle de acesso.
- Você pode configurar um certificado de autenticação de tokens específico ao seu aplicativo e com um tempo de vida que você controla.

> [!NOTE]
> Essa abordagem requer uma licença de Azure AD Premium. Se você for um cliente de controle de acesso e precisar de uma licença Premium para configurar o logon único para um aplicativo, entre em contato conosco. Teremos o prazer de fornecer licenças de desenvolvedor para você usar.

Uma abordagem alternativa é seguir [Este exemplo de código](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), que fornece instruções ligeiramente diferentes para configurar o WS-Federation. Este exemplo de código não usa o WIF, mas sim o middleware ASP.NET 4,5 OWIN. No entanto, as instruções para o registro do aplicativo são válidas para aplicativos que usam o WIF e não exigem uma licença de Azure AD Premium. 

Se você escolher essa abordagem, precisará entender a [substituição de chave de assinatura no Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Essa abordagem usa a chave de assinatura global do Azure AD para emitir tokens. Por padrão, o WIF não atualiza automaticamente as chaves de assinatura. Quando o Azure AD gira suas chaves de assinatura globais, sua implementação do WIF precisa estar preparada para aceitar as alterações. Para obter mais informações, consulte [informações importantes sobre substituição de chave de assinatura no Azure ad](https://msdn.microsoft.com/library/azure/dn641920.aspx).

Se você puder se integrar ao Azure AD por meio dos protocolos de OAuth ou OpenID Connect, é recomendável fazer isso. Temos abrangentes documentações e diretrizes sobre como integrar o Azure AD ao seu aplicativo Web disponível em nosso [Guia do desenvolvedor do Azure ad](https://aka.ms/aaddev).

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrar para o Azure Active Directory B2C

O outro caminho de migração a ser considerado é Azure AD B2C. Azure AD B2C é um serviço de autenticação de nuvem que, como o Access Control, permite aos desenvolvedores terceirizar sua lógica de gerenciamento de identidade e autenticação para um serviço de nuvem. É um serviço pago (com as camadas gratuita e Premium) projetado para aplicativos voltados para o consumidor que podem ter até milhões de usuários ativos.

Como o controle de acesso, um dos recursos mais atraentes do Azure AD B2C é que ele dá suporte a muitos tipos diferentes de contas. Com o Azure AD B2C, você pode conectar usuários usando suas contas do conta Microsoft ou do Facebook, do Google, do LinkedIn, do GitHub ou do Yahoo e muito mais. O Azure AD B2C também dá suporte a "contas locais", ou nome de usuário e senhas que os usuários criam especificamente para seu aplicativo. O Azure AD B2C também fornece extensibilidade avançada que você pode usar para personalizar seus fluxos de entrada. 

No entanto, Azure AD B2C não dá suporte à amplitude de protocolos de autenticação e formatos de token que os clientes de controle de acesso podem exigir. Você também não pode usar Azure AD B2C para obter tokens e consultar informações adicionais sobre o usuário do provedor de identidade, da Microsoft ou de outra forma.

A tabela a seguir compara os recursos do controle de acesso que são relevantes para aplicativos Web com aqueles que estão disponíveis no Azure AD B2C. Em um alto nível, *Azure ad B2C é provavelmente a escolha certa para sua migração se seu aplicativo for voltado para o consumidor ou se ele der suporte a muitos tipos diferentes de contas.*

| Funcionalidade | Suporte ao controle de acesso | Suporte a Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Tipos de contas** | | |
| Contas corporativas ou de estudante da Microsoft | Suportadas | Com suporte por meio de políticas personalizadas  |
| Contas do Windows Server Active Directory e AD FS | Com suporte via Federação direta com AD FS | Com suporte via Federação SAML usando políticas personalizadas |
| Contas de outros sistemas de gerenciamento de identidades corporativas | Com suporte via Federação direta por meio do WS-Federation | Com suporte via Federação SAML usando políticas personalizadas |
| Contas da Microsoft para uso pessoal | Suportadas | Suportadas | 
| Contas do Facebook, Google, Yahoo | Suportadas | Suporte nativo ao Facebook e ao Google, o Yahoo tem suporte por meio da Federação do OpenID Connect usando políticas personalizadas |
| **Compatibilidade entre protocolos e SDK** | | |
| WIF (Windows Identity Foundation) | Suportadas | Não suportado |
| WS-Federation | Suportadas | Não suportado |
| OAuth 2.0 | Suporte para o rascunho 13 | Suporte para RFC 6749, a especificação mais moderna |
| WS-Trust | Suportadas | Não suportado |
| **Formatos de token** | | |
| JWT | Com suporte na versão beta | Suportadas |
| SAML 1,1 | Suportadas | Não suportado |
| SAML 2.0 | Suportadas | Não suportado |
| SWT | Suportadas | Não suportado |
| **Personalizações** | | |
| Interface do usuário de Home realm de descoberta/seleção de conta personalizável | Código para download que pode ser incorporado em aplicativos | Interface do usuário totalmente personalizável por meio de CSS personalizado |
| Carregar certificados de assinatura de token personalizados | Suportadas | Chaves de assinatura personalizadas, não certificados, com suporte por meio de políticas personalizadas |
| Personalizar declarações em tokens |-Passar declarações de entrada de provedores de identidade<br />-Obter token de acesso do provedor de identidade como uma declaração<br />-Emitir declarações de saída com base em valores de declarações de entrada<br />-Emitir declarações de saída com valores constantes |-Pode passar por declarações de provedores de identidade; políticas personalizadas necessárias para algumas declarações<br />-Não é possível obter o token de acesso do provedor de identidade como uma declaração<br />-Pode emitir declarações de saída com base em valores de declarações de entrada por meio de políticas personalizadas<br />-Pode emitir declarações de saída com valores constantes por meio de políticas personalizadas |
| **Automatização** | | |
| Automatizar tarefas de configuração e gerenciamento | Com suporte via serviço de gerenciamento de controle de acesso |-Criação de usuários permitidos por meio do Azure AD API do Graph<br />-Não é possível criar políticas, aplicativos ou locatários B2C programaticamente |

Se você decidir que Azure AD B2C é o melhor caminho de migração para seus aplicativos e serviços, comece com os seguintes recursos:

- [Documentação do Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2C políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Preços de Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrar para a identidade de ping ou Auth0

Em alguns casos, você pode achar que o Azure AD e o Azure AD B2C não são suficientes para substituir o controle de acesso em seus aplicativos Web sem fazer alterações importantes no código. Alguns exemplos comuns podem incluir:

- Aplicativos Web que usam o WIF ou o WS-Federation para entrar com provedores de identidade social, como Google ou Facebook.
- Aplicativos Web que executam Federação direta para um provedor de identidade corporativa em relação ao protocolo WS-Federation.
- Aplicativos Web que exigem o token de acesso emitido por um provedor de identidade social (como Google ou Facebook) como uma declaração nos tokens emitidos pelo controle de acesso.
- Aplicativos Web com regras de transformação de token complexas que não podem ser reproduzidas pelo Azure AD ou Azure AD B2C.
- Aplicativos Web multilocatários que usam o ACS para gerenciar centralmente a Federação para muitos provedores de identidade diferentes

Nesses casos, talvez você queira migrar seu aplicativo Web para outro serviço de autenticação na nuvem. Recomendamos explorar as opções a seguir. Cada uma das opções a seguir oferece recursos semelhantes ao controle de acesso:

|     |     |
| --- | --- |
| ![Esta imagem mostra o logotipo do Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | O [Auth0](https://auth0.com/acs) é um serviço de identidade de nuvem flexível que criou [diretrizes de migração de alto nível para clientes de controle de acesso](https://auth0.com/acs)e dá suporte a quase todos os recursos que o ACS faz. |
| ![Esta imagem mostra o logotipo de identidade de ping](./media/active-directory-acs-migration/rsz_ping.png) | A [identidade de ping](https://www.pingidentity.com) oferece duas soluções semelhantes ao ACS. O PingOne é um serviço de identidade de nuvem que dá suporte a muitos dos mesmos recursos que o ACS, e o PingFederate é um produto de identidade local semelhante que oferece mais flexibilidade. Consulte a orientação de desativação [do ACS do ping](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) para obter mais detalhes sobre como usar esses produtos. |

Nosso objetivo em trabalhar com o ping Identity e o Auth0 é garantir que todos os clientes do controle de acesso tenham um caminho de migração para seus aplicativos e serviços que minimize a quantidade de trabalho necessária para mover do controle de acesso.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Serviços Web que usam a autenticação ativa

Para serviços Web que são protegidos com tokens emitidos pelo controle de acesso, o Access Control oferece os seguintes recursos e funcionalidades:

- Capacidade de registrar uma ou mais *identidades de serviço* no namespace do controle de acesso. As identidades de serviço podem ser usadas para solicitar tokens.
- Suporte para os protocolos OAuth WRAP e OAuth 2,0 Draft 13 para solicitar tokens usando os seguintes tipos de credenciais:
    - Uma senha simples que é criada para a identidade do serviço
    - Um SWT assinado usando uma chave simétrica ou um certificado X509
    - Um token SAML emitido por um provedor de identidade confiável (normalmente, uma instância de AD FS)
- Suporte para os seguintes formatos de token: JWT, SAML 1,1, SAML 2,0 e SWT.
- Regras de transformação de token simples.

As identidades de serviço no controle de acesso normalmente são usadas para implementar a autenticação de servidor para servidor. 

#### <a name="migrate-to-azure-active-directory"></a>Migrar para o Azure Active Directory

Nossa recomendação para esse tipo de fluxo de autenticação é migrar para [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). O Azure AD é o provedor de identidade baseado em nuvem para contas corporativas ou de estudante da Microsoft. O Azure AD é o provedor de identidade para o Office 365, o Azure e muito mais. 

Você também pode usar o Azure AD para autenticação de servidor para servidor usando a implementação do Azure AD da concessão de credenciais de cliente OAuth. A tabela a seguir compara os recursos do controle de acesso na autenticação de servidor para servidor com os que estão disponíveis no Azure AD.

| Funcionalidade | Suporte ao controle de acesso | Suporte do Azure AD |
| ---------- | ----------- | ---------------- |
| Como registrar um serviço Web | Criar uma terceira parte confiável no portal de gerenciamento de controle de acesso | Criar um aplicativo Web do Azure AD no portal do Azure |
| Como registrar um cliente | Criar uma identidade de serviço no portal de gerenciamento de controle de acesso | Criar outro aplicativo Web do Azure AD no portal do Azure |
| Protocolo usado |-Protocolo OAuth WRAP<br />-Concessão de credenciais de cliente do OAuth 2,0 Draft 13 | Concessão de credenciais de cliente OAuth 2.0 |
| Métodos de autenticação de cliente |-Senha simples<br />-SWT assinado<br />-Token SAML de um provedor de identidade federada |-Senha simples<br />-JWT assinado |
| Formatos de token |-JWT<br />-SAML 1,1<br />-SAML 2,0<br />-SWT<br /> | Somente JWT |
| Transformação de token |-Adicionar declarações personalizadas<br />– Lógica de emissão de declaração if-then simples | Adicionar declarações personalizadas | 
| Automatizar tarefas de configuração e gerenciamento | Com suporte via serviço de gerenciamento de controle de acesso | Com suporte via Microsoft Graph e Azure AD API do Graph |

Para obter orientação sobre como implementar cenários de servidor para servidor, consulte os seguintes recursos:

- Seção serviço a serviço do [Guia do desenvolvedor do Azure ad](https://aka.ms/aaddev)
- [Exemplo de código de daemon usando credenciais de cliente de senha simples](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Exemplo de código de daemon usando credenciais de cliente de certificado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrar para a identidade de ping ou Auth0

Em alguns casos, você pode achar que as credenciais de cliente do Azure AD e a implementação de concessão OAuth não são suficientes para substituir o controle de acesso em sua arquitetura sem alterações de código principais. Alguns exemplos comuns podem incluir:

- Autenticação de servidor para servidor usando formatos de token diferentes de JWTs.
- Autenticação de servidor para servidor usando um token de entrada fornecido por um provedor de identidade externo.
- Autenticação de servidor para servidor com regras de transformação de token que o Azure AD não pode reproduzir.

Nesses casos, você pode considerar migrar seu aplicativo Web para outro serviço de autenticação de nuvem. Recomendamos explorar as opções a seguir. Cada uma das opções a seguir oferece recursos semelhantes ao controle de acesso:

|     |     |
| --- | --- |
| ![Esta imagem mostra o logotipo do Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | O [Auth0](https://auth0.com/acs) é um serviço de identidade de nuvem flexível que criou [diretrizes de migração de alto nível para clientes de controle de acesso](https://auth0.com/acs)e dá suporte a quase todos os recursos que o ACS faz. |
| ![Esta imagem mostra o logotipo de identidade de ping](./media/active-directory-acs-migration/rsz_ping.png) | A [identidade de ping](https://www.pingidentity.com) oferece duas soluções semelhantes ao ACS. O PingOne é um serviço de identidade de nuvem que dá suporte a muitos dos mesmos recursos que o ACS, e o PingFederate é um produto de identidade local semelhante que oferece mais flexibilidade. Consulte a orientação de desativação [do ACS do ping](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) para obter mais detalhes sobre como usar esses produtos. |

Nosso objetivo em trabalhar com o ping Identity e o Auth0 é garantir que todos os clientes do controle de acesso tenham um caminho de migração para seus aplicativos e serviços que minimize a quantidade de trabalho necessária para mover do controle de acesso.

#### <a name="passthrough-authentication"></a>Autenticação de passagem

Para autenticação de passagem com transformação de token arbitrária, não há nenhuma tecnologia da Microsoft equivalente ao ACS. Se isso for o que seus clientes precisam, o Auth0 pode ser aquele que fornece a solução de aproximação mais próxima.

## <a name="questions-concerns-and-feedback"></a>Perguntas, preocupações e comentários

Entendemos que muitos clientes do controle de acesso não encontrarão um caminho de migração claro depois de ler este artigo. Talvez você precise de alguma assistência ou orientação para determinar o plano certo. Se você quiser discutir seus cenários e perguntas de migração, deixe um comentário nesta página.
