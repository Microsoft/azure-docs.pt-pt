---
title: Migrar a partir do serviço de controle de acesso do Azure | Documentos da Microsoft
description: Saiba mais sobre as opções para mover aplicações e serviços a partir do serviço de controlo de acesso de Azure (ACS).
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/03/2018
ms.author: celested
ms.reviewer: jlu, annaba, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed2164fc122621a4ba2aeb4bb0797db02b10a4bf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449143"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Como: Migrardo Serviço de Controlo de Acesso do Microsoft Azure

Microsoft Azure controlo de acesso Service (ACS), um serviço do Azure Active Directory (Azure AD), será descontinuado a 7 de Novembro de 2018. Aplicações e serviços que a utilizam atualmente o controlo de acesso devem ser totalmente migrados para um mecanismo de autenticação diferentes até lá. Este artigo descreve as recomendações para os clientes atuais, à medida que planeia preterir a utilização do controlo de acesso. Se não utilizar atualmente o controlo de acesso, não precisa de tomar qualquer ação.

## <a name="overview"></a>Descrição geral

Controlo de acesso é um serviço de autenticação de cloud que oferece uma forma fácil de autenticar e autorizar utilizadores para o acesso aos seus serviços e aplicações web. Ele permite que muitas funcionalidades de autenticação e autorização para ser fatorados de seu código. Controlo de acesso é usado principalmente por desenvolvedores e arquitetos de clientes .NET da Microsoft, aplicações web ASP.NET e serviços da web Windows Communication Foundation (WCF).

Casos de utilização para controlo de acesso podem ser divididos em três categorias principais:

- Autenticar para determinados serviços cloud da Microsoft, incluindo o Azure Service Bus e o Dynamics CRM. Aplicativos cliente obter os tokens de controlo de acesso para autenticar a estes serviços para executar várias ações.
- Adicionar autenticação para aplicações web, personalizadas e previamente incluídas em pacotes (como o SharePoint). Ao utilizar a autenticação de "passiva" do controlo de acesso, os aplicativos da web podem suportar início de sessão com uma conta Microsoft (anteriormente conhecido como Live ID) e com as contas do Google, Facebook, Yahoo, do Azure AD e os serviços de Federação do Active Directory (AD FS).
- Protegendo serviços da web personalizado com tokens emitidos pelo controlo de acesso. Ao utilizar a autenticação de "ativa", serviços da web podem certificar-se de que permitem acesso apenas aos clientes conhecidos que foram autenticadas com controlo de acesso.

Cada um destes utilizar casos e sua migração recomendado estratégias são abordadas nas secções seguintes.

> [!WARNING]
> Na maioria dos casos, as alterações significativas de código são necessários para migrar serviços e aplicações existentes para as tecnologias mais recentes. Recomendamos que imediatamente comece a planear e executar sua migração para evitar potenciais falhas ou tempo de inatividade.

Controlo de acesso tem os seguintes componentes:

- Um serviço de tokens seguros (STS), que recebe pedidos de autenticação e emite tokens de segurança em troca.
- O portal clássico do Azure, onde pode criar, eliminar e ativar e desativar espaços de nomes do controlo de acesso.
- Um controlo de acesso gestão portal separado, onde personalizar e configurar espaços de nomes do controlo de acesso.
- Um serviço de gestão, que pode utilizar para automatizar as funções os portais.
- Um motor de regras da transformação de token, que pode ser usado para definir uma lógica complexa para manipular o formato de saída de tokens que emite o controlo de acesso.

Para utilizar estes componentes, tem de criar um ou mais espaços de nomes de controlo de acesso. R *espaço de nomes* é uma instância dedicada do controlo de acesso que seus aplicativos e serviços comunicam com. Um espaço de nomes é isolado de todos os outros clientes de controlo de acesso. Outros clientes de controlo de acesso usar seus próprios espaços de nomes. Um espaço de nomes no controlo de acesso tem um URL dedicado que é semelhante a este:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Todas as comunicações com o STS e operações de gestão são efetuadas neste URL. Utilize caminhos diferentes para diferentes fins. Para determinar se seus aplicativos ou serviços utilizam o controlo de acesso, monitorar qualquer tráfego para https://&lt;espaço de nomes&gt;. accesscontrol.windows.net. Qualquer tráfego para este URL é processado pelo controlo de acesso e tem de ser interrompida. 

A exceção é qualquer tráfego para `https://accounts.accesscontrol.windows.net`. O tráfego para este URL já é processado por um serviço diferente e **není** afetados pela descontinuação do controlo de acesso. 

Para obter mais informações sobre o controlo de acesso, consulte [2.0 de serviço de controlo de acesso (arquivados)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Descubra quais das suas aplicações vão ser afetadas

Siga os passos nesta secção para saber quais das suas aplicações vão ser afetadas por extinção de ACS.

### <a name="download-and-install-acs-powershell"></a>Transferir e instalar o PowerShell de ACS

1. Aceda à galeria do PowerShell e transfira [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
1. Instalar o módulo através da execução

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

1. Obter uma lista de todos os comandos de possíveis através da execução

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Para obter ajuda sobre um comando específico, execute:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    onde `[Command-Name]` é o nome do comando ACS.

### <a name="list-your-acs-namespaces"></a>Liste os espaços de nomes do ACS

1. Ligar ao ACS utilizando o **Connect-AcsAccount** cmdlet.
  
    Poderá ter de executar `Set-ExecutionPolicy -ExecutionPolicy Bypass` antes de pode executar comandos e ser o administrador nessas subscrições para executar os comandos.

1. Listar as subscrições do Azure disponíveis com o **Get-AcsSubscription** cmdlet.
1. Listar a sua espaços de nomes do ACS com o **Get-AcsNamespace** cmdlet.

### <a name="check-which-applications-will-be-impacted"></a>Verificar as aplicações que vão ser afetadas

1. Utilize o espaço de nomes do passo anterior e aceda a `https://<namespace>.accesscontrol.windows.net`

    Por exemplo, se for um dos espaços de nomes contoso-test, vá para `https://contoso-test.accesscontrol.windows.net`

1. Sob **relações de confiança**, selecione **da entidade Confiadora de terceiros** para ver a lista de aplicações que vão ser afetadas por extinção de ACS.
1. Repita os passos 1 a 2 para outros namespace(s) do ACS que tem.

## <a name="retirement-schedule"></a>Agenda de extinção

A partir de Novembro de 2017, todos os componentes de controlo de acesso são totalmente suportadas e operacional. A única restrição é que [não é possível criar novos espaços de nomes do controlo de acesso através do portal clássico do Azure](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Aqui está o agendamento para descontinuar os componentes de controlo de acesso:

- **Novembro de 2017**:  Os administradores do Azure AD experiência no portal clássico do Azure [foi extinto](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). Neste momento, a gestão de espaço de nomes para controlo de acesso está disponível um URL dedicado, novos: `https://manage.windowsazure.com?restoreClassic=true`. Utilize este URl para ver os espaços de nomes existentes, ativar e desativar espaços de nomes e eliminar espaços de nomes, caso pretenda.
- **2 de Abril de 2018**: Portal clássico do Azure é completamente extinguido, que significa que a gestão de espaço de nomes do controlo de acesso já não está disponível através de qualquer URL. Neste momento, não é possível desativar ou ativar, eliminar ou enumerar os espaços de nomes do controlo de acesso. No entanto, o portal de gestão de controlo de acesso será totalmente funcional e localizado em `https://\<namespace\>.accesscontrol.windows.net`. Todos os outros componentes de controlo de acesso continuam a funcionar normalmente.
- **7 de Novembro de 2018**: Todos os componentes de controlo de acesso permanentemente são encerrados. Isto inclui o portal de gestão de controlo de acesso, o serviço de gestão, STS e o motor de regras de transformação de token. Neste momento, todos os pedidos enviados para o controlo de acesso (localizado em \<espaço de nomes\>. accesscontrol.windows.net) falhar. Deve migração todas as aplicações e serviços existentes para outras tecnologias bem antes desta data.

> [!NOTE]
> Desativa a uma política de espaços de nomes não pediu um token para um período de tempo. A partir do início de Setembro de 2018, este período de tempo está atualmente em 14 dias de inatividade, mas isso vai ser baixou para 7 dias de inatividade nas próximas semanas. Se tiver espaços de nomes do controlo de acesso que estão atualmente desativados, pode [transferir e instalar o ACS PowerShell](#download-and-install-acs-powershell) para reativar o namespace(s).

## <a name="migration-strategies"></a>Estratégias de migração

As secções seguintes descrevem as recomendações de alto nível para a migração de controlo de acesso a outras tecnologias da Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Clientes de serviços cloud da Microsoft

Cada serviço cloud da Microsoft que aceita tokens que são emitidos pelo controlo de acesso agora dá suporte, pelo menos, uma forma alternativa de autenticação. O mecanismo de autenticação corretas varia para cada serviço. Recomendamos que consulte a documentação específica para cada serviço para obter diretrizes oficiais. Para sua comodidade, cada conjunto de documentação é fornecido aqui:

| Serviço | Orientação |
| ------- | -------- |
| Service Bus do Azure | [Migrar para assinaturas de acesso partilhado](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Reencaminhamento do Azure Service Bus | [Migrar para assinaturas de acesso partilhado](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Cache gerida do Azure | [Migrar para a Cache do Azure para Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| O Azure DataMarket | [Migrar para as APIs serviços cognitivos](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Serviços BizTalk | [Migrar para o recurso do Logic Apps do App Service do Azure](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Serviços de multimédia do Azure | [Migrar para a autenticação do Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Atualize o agente de cópia de segurança do Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>Clientes do SharePoint

2016, do SharePoint 2013 e SharePoint Online clientes há muito tempo usaram ACS para fins de autenticação na nuvem, no local e cenários híbridos. Alguns recursos do SharePoint e casos de utilização serão afetados pela desativação dos ACS, enquanto outros não irão. A tabela abaixo resume as orientações de migração para algumas das mais popular SharePoint ACS que tire partido de funcionalidades:

| Funcionalidade | Orientação |
| ------- | -------- |
| Autenticação de utilizadores do Azure AD | Anteriormente, o Azure AD não oferecia suporte necessários para o SharePoint para a autenticação de tokens de SAML 1.1 e o ACS foi utilizado como um intermediário que efetuou o SharePoint compatíveis com formatos de token do Azure AD. Agora, pode [ligar o SharePoint diretamente ao Azure AD através do SharePoint de Galeria de aplicações do Azure AD na aplicação de local](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial). |
| [Autenticação da aplicação e a autenticação de servidor a servidor no SharePoint no local](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Não são afetados pela desativação de ACS; sem alterações necessárias. | 
| [Autorização de fidedignidade baixa de SharePoint suplementos (fornecedor alojada e o SharePoint hospedado)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Não são afetados pela desativação de ACS; sem alterações necessárias. |
| [Pesquisa do SharePoint na cloud híbrida](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Não são afetados pela desativação de ACS; sem alterações necessárias. |

### <a name="web-applications-that-use-passive-authentication"></a>Aplicações Web que utilizam autenticação passiva

Para aplicativos da web que usam o controle de acesso para a autenticação de utilizador, o controlo de acesso fornece as seguintes funcionalidades e capacidades para arquitetos e desenvolvedores de aplicativos de web:

- Integração profunda com o Windows Identity Foundation (WIF).
- Federação com o Google, Facebook, Yahoo, Azure Active Directory, AD FS contas e e contas da Microsoft.
- Suporte para os seguintes protocolos de autenticação: 13 de rascunho de OAuth 2.0, WS-Trust e Federação de serviços da Web (WS-Federation).
- Suporte para os seguintes formatos de token: JSON Web Token (JWT), SAML 1.1, SAML 2.0 e Simple Web Token (SWT).
- Uma experiência de deteção de realm inicial, integrada no WIF, que permite aos utilizadores escolher o tipo de conta que utilizam para iniciar sessão. Esta experiência é hospedada pelo aplicativo web e é totalmente personalizável.
- Token de transformação que permite a personalização avançada de afirmações recebidas pelo aplicativo web do controlo de acesso, incluindo:
    - Passar afirmações a partir de fornecedores de identidade.
    - Adicionar declarações personalizadas adicionais.
    - Lógica de if-then simples para emitir afirmações sob determinadas condições.

Infelizmente, não um serviço que oferece todos esses recursos equivalentes. Deve avaliar quais recursos do controle de acesso de que precisa e, em seguida, escolha entre a utilização [do Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C), ou outro da autenticação na nuvem serviço.

#### <a name="migrate-to-azure-active-directory"></a>Migrar para o Azure Active Directory

Um caminho para considerar está a integrar as aplicações e serviços diretamente com o Azure AD. O Azure AD é o fornecedor de identidade com base na cloud da Microsoft trabalham contas escolares ou profissionais. O Azure AD é o fornecedor de identidade para o Office 365, Azure e muito mais. Ele fornece semelhante federado capacidades de autenticação para o controlo de acesso, mas não suporta todas as funcionalidades de controlo de acesso. 

O principal exemplo é o federação com fornecedores de identidade social, como o Facebook, Google e Yahoo. Se os seus utilizadores iniciar sessão com esses tipos de credenciais, do Azure AD não é a solução para si. 

Azure AD também não suporta necessariamente os protocolos de autenticação mesmo exata como controlo de acesso. Por exemplo, embora o controlo de acesso e o Azure AD suportam OAuth, existem diferenças sutis entre cada implementação. Implementações diferentes que modificasse o código como parte de uma migração.

No entanto, do Azure AD fornece várias vantagens potenciais para os clientes de controlo de acesso. Ele nativamente suporta Microsoft contas escolares ou alojadas na cloud, que são frequentemente utilizadas por clientes de controlo de acesso. 

Um inquilino do Azure AD também pode ser federado para uma ou mais instâncias do Active Directory no local através do AD FS. Dessa forma, a aplicação pode autenticar utilizadores com base na cloud e utilizadores que estão alojadas no local. Também suporta o protocolo WS-Federation, que faz com que seja relativamente simples integrar com uma aplicação web com o WIF.

A tabela seguinte compara as funcionalidades de controlo de acesso que são relevantes para aplicativos web com esses recursos que estão disponíveis no Azure AD. 

Num alto nível *do Azure Active Directory é provavelmente a melhor opção para a sua migração se permitir que os utilizadores iniciem sessão em apenas com a Microsoft contas escolares ou*.

| Capacidade | Suporte de controlo de acesso | Suporte do Azure AD |
| ---------- | ----------- | ---------------- |
| **Tipos de contas** | | |
| Contas de escolar ou profissional da Microsoft | Suportadas | Suportadas |
| Contas do Active Directory e o AD FS do Windows Server |-Suportadas através de federação com o inquilino do Azure AD <br />-Suportadas através de Federação direta com o AD FS | Apenas suportado através de federação com o inquilino do Azure AD | 
| Contas a partir de outros sistemas de gestão de identidade empresarial |-Possível através de federação com o inquilino do Azure AD <br />-Suportadas através de Federação direta | Possível através de federação com o inquilino do Azure AD |
| Contas da Microsoft para uso pessoal | Suportadas | Suportado através do protocolo OAuth de v2.0 do Azure AD, mas não através de quaisquer outros protocolos | 
| Contas do Facebook, Google, Yahoo | Suportadas | Não é suportado de nenhuma forma |
| **Protocolos e a compatibilidade SDK** | | |
| WIF | Suportadas | Estão disponíveis instruções suportadas, mas limitadas |
| WS-Federation | Suportadas | Suportadas |
| OAuth 2.0 | Suporte para rascunho 13 | Suporte para RFC 6749, a especificação de mais moderna |
| WS-Trust | Suportadas | Não suportado |
| **Formatos de token** | | |
| JWT | Suportado na versão Beta | Suportadas |
| SAML 1.1 | Suportadas | Pré-visualização |
| SAML 2.0 | Suportadas | Suportadas |
| SWT | Suportadas | Não suportado |
| **Personalizações** | | |
| Realm inicial personalizável da interface do Usuário de deteção/conta-separação | Código para download que pode ser incorporado nas aplicações | Não suportado |
| Carregar certificados de assinatura de token personalizados | Suportadas | Suportadas |
| Personalizar afirmações nos tokens |-Passar por meio de afirmações de entrada de fornecedores de identidade<br />-Obter token de acesso de fornecedor de identidade como uma afirmação<br />-Emitir afirmações de saída com base nos valores de afirmações de entrada<br />-Emitir afirmações de saída com valores de constantes |-Não é possível passar de declarações de fornecedores de identidade federada<br />-Não é possível obter token de acesso de fornecedor de identidade como uma afirmação<br />-Não é possível emitir afirmações de saída com base nos valores de afirmações de entrada<br />-Pode emitir afirmações de saída com valores de constantes<br />-Podem emitir afirmações de saída com base nas propriedades de utilizadores sincronizados com o Azure AD |
| **Automatização** | | |
| Automatizar tarefas de configuração e gestão | Suportado através do serviço de gestão de controle de acesso | Suportado através do Microsoft Graph e do Azure AD Graph API |

Se decidir que o Azure AD é o melhor caminho de migração das suas aplicações e serviços, deve estar ciente de duas formas de integrar a sua aplicação com o Azure AD.

Para usar WS-Federation ou o WIF para integrar com o Azure AD, recomendamos o seguinte, a abordagem descrita no [configurar o início de sessão único federado para uma aplicação de externas à galeria](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). O artigo refere-se para configurar o Azure AD para baseado em SAML início de sessão único, mas também funciona para a configuração de WS-Federation. Seguir essa abordagem requer uma licença do Azure AD Premium. Essa abordagem tem duas vantagens:

- Obtenha total flexibilidade de personalização de token do Azure AD. Pode personalizar as afirmações que são emitidas pelo Azure AD para corresponder as afirmações que são emitidas pelo controlo de acesso. Especialmente, isto inclui a afirmação de identificador de nome ou ID de utilizador. Para continuar a receber os identificadores de usuário consistente para os seus utilizadores depois de alterar as tecnologias, certifique-se de que o usuário IDs emitido pela correspondência do Azure AD os emitidos pelo controlo de acesso.
- Pode configurar um certificado de assinatura de tokens de mensagens em fila que é específico para seu aplicativo e com uma duração que controla.

> [!NOTE]
> Essa abordagem exige uma licença do Azure AD Premium. Se for um cliente de controlo de acesso e requerem uma licença premium para configurar o início de sessão único para uma aplicação, contacte-nos. Podemos ficará satisfeitos fornecer licenças de desenvolvedor para que possa utilizar.

Uma abordagem alternativa passa por seguir [este exemplo de código](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), que dá instruções um pouco diferentes para configurar WS-Federation. Este exemplo de código não utiliza o WIF, mas em vez disso, o middleware da OWIN do ASP.NET 4.5. No entanto, as instruções para o registo de aplicação são válidas para aplicações que utilizam o WIF e não necessitam de uma licença do Azure AD Premium. 

Se escolher esta abordagem, precisa entender [iniciar o rollover da chave de sessão do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Esta abordagem utiliza o Azure AD global de chave para emitir tokens de assinatura. Por predefinição, o WIF não atualiza automaticamente as chaves de assinatura. Quando o Azure AD roda as chaves de assinatura global, sua implementação do WIF precisa estar preparado para aceitar as alterações. Para obter mais informações, consulte [informações importantes sobre o rollover da chave de assinatura no Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx).

Se pode integrar com o Azure AD com os protocolos de OpenID Connect ou do OAuth, é recomendável fazê-lo. Temos documentação extensa sobre e orientações sobre como integrar o Azure AD na sua aplicação web disponível no nosso [Guia do programador do Azure AD](https://aka.ms/aaddev).

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrar para o Azure Active Directory B2C

Outro caminho de migração a ter em consideração é o Azure AD B2C. O Azure AD B2C é um serviço de autenticação em nuvem que, como controle de acesso, permite que os desenvolvedores a fim de terceirizar sua lógica de gerenciamento de identidade e autenticação para um serviço em nuvem. É um serviço pago (com os escalões gratuito e premium), que foi concebido para aplicações destinadas ao consumidor que podem ter até milhões de utilizadores ativos.

Como controle de acesso, é um dos recursos mais atraentes do Azure AD B2C que suporta muitos tipos diferentes de contas. Com o Azure AD B2C, pode criar uma sessão de segurança de utilizadores utilizando a respetiva conta Microsoft, ou contas do Facebook, Google, LinkedIn, GitHub ou Yahoo e muito mais. O Azure AD B2C também oferece suporte a "contas locais", ou nome de utilizador e palavras-passe que os utilizadores criam especificamente para a sua aplicação. O Azure AD B2C também fornece extensibilidade sofisticado que pode usar para personalizar os fluxos de início de sessão. 

No entanto, do Azure AD B2C não suporta a variedade de protocolos de autenticação de token de formatos e esse controlo de acesso, os clientes podem exigir. Também não é possível utilizar do Azure AD B2C para obter tokens e de consulta para obter mais informações sobre o utilizador do fornecedor de identidade, Microsoft ou de outro modo.

A tabela seguinte compara as funcionalidades de controlo de acesso que são relevantes para aplicativos web com os que estão disponíveis no Azure AD B2C. Num alto nível, *do Azure AD B2C provavelmente é a escolha correta para a sua migração se seu aplicativo consumidor ou se ele oferece suporte a muitos tipos diferentes de contas.*

| Capacidade | Suporte de controlo de acesso | Suporte do Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Tipos de contas** | | |
| Contas de escolar ou profissional da Microsoft | Suportadas | Suportado através de políticas personalizadas  |
| Contas do Active Directory e o AD FS do Windows Server | Suportado através de Federação direta com o AD FS | Suportado através de Federação de SAML, ao utilizar políticas personalizadas |
| Contas a partir de outros sistemas de gestão de identidade empresarial | Suportado através de Federação direta por meio do WS-Federation | Suportado através de Federação de SAML, ao utilizar políticas personalizadas |
| Contas da Microsoft para uso pessoal | Suportadas | Suportadas | 
| Contas do Facebook, Google, Yahoo | Suportadas | Facebook e Google suportados nativamente, o Yahoo suportada através de Federação OpenID Connect utilizando as políticas personalizadas |
| **Protocolos e a compatibilidade SDK** | | |
| Windows Identity Foundation (WIF) | Suportadas | Não suportado |
| WS-Federation | Suportadas | Não suportado |
| OAuth 2.0 | Suporte para rascunho 13 | Suporte para RFC 6749, a especificação de mais moderna |
| WS-Trust | Suportadas | Não suportado |
| **Formatos de token** | | |
| JWT | Suportado na versão Beta | Suportadas |
| SAML 1.1 | Suportadas | Não suportado |
| SAML 2.0 | Suportadas | Não suportado |
| SWT | Suportadas | Não suportado |
| **Personalizações** | | |
| Realm inicial personalizável da interface do Usuário de deteção/conta-separação | Código para download que pode ser incorporado nas aplicações | Interface do Usuário totalmente personalizável através do CSS personalizado |
| Carregar certificados de assinatura de token personalizados | Suportadas | Assinatura chaves personalizadas, não certificados, suportadas através de políticas personalizadas |
| Personalizar afirmações nos tokens |-Passar por meio de afirmações de entrada de fornecedores de identidade<br />-Obter token de acesso de fornecedor de identidade como uma afirmação<br />-Emitir afirmações de saída com base nos valores de afirmações de entrada<br />-Emitir afirmações de saída com valores de constantes |-Podem passar por declarações de fornecedores de identidade políticas personalizadas necessárias para algumas afirmações<br />-Não é possível obter token de acesso de fornecedor de identidade como uma afirmação<br />-Podem emitir afirmações de saída com base nos valores de afirmações de entrada através de políticas personalizadas<br />-Pode emitir afirmações de saída com valores de constantes por meio de políticas personalizadas |
| **Automatização** | | |
| Automatizar tarefas de configuração e gestão | Suportado através do serviço de gestão de controle de acesso |-Criação de utilizadores permitidos através do Azure AD Graph API<br />-Não é possível criar inquilinos de B2C, aplicações ou políticas programaticamente |

Se decidir que o Azure AD B2C é o melhor caminho de migração das suas aplicações e serviços, começar com os seguintes recursos:

- [Documentação do Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Políticas personalizadas do Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Os preços do Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrar para a Ping Identity ou Auth0

Em alguns casos, pode achar que o Azure AD e o Azure AD B2C não são suficientes para substituir o controlo de acesso em seus aplicativos web sem fazer alterações importantes no código. Alguns exemplos comuns podem incluir:

- Aplicações Web que utilizam o WIF ou WS-Federation para início de sessão com fornecedores de identidade de redes sociais, como o Google ou Facebook.
- Aplicações Web que executam o Federação direta para um fornecedor de identidade da empresa através do protocolo WS-Federation.
- Aplicativos Web que exigem o token de acesso emitido por um fornecedor de identidade social (por exemplo, o Google ou Facebook) como uma afirmação nos tokens emitidos pelo controlo de acesso.
- Aplicações Web com regras de transformação complexas de token que, do Azure AD ou do Azure AD, B2C não consegue reproduzir.
- Aplicações web de vários inquilinos que utilizam o ACS para gerir centralmente o Federação para muitos fornecedores de identidade diferente

Nestes casos, poderá querer considerar a migração da sua aplicação web para outro serviço de autenticação em nuvem. Recomendamos a explorar as seguintes opções. Cada uma das seguintes opções oferecem recursos semelhantes ao controlo de acesso:

|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) é um serviço de identidade de cloud flexíveis que tenha criado [orientação de migração de alto nível para os clientes de controlo de acesso](https://auth0.com/acs)e suporta quase todas as funcionalidades que o ACS faz. |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [A ping Identity](https://www.pingidentity.com) oferece duas soluções semelhantes às ACS. PingOne é um serviço de identidade de cloud que oferece suporte a muitos dos mesmos recursos como o ACS e PingFederate é semelhante no produto de identidade no local que oferece mais flexibilidade. Consulte a [orientações de extinção de ACS de Ping](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) para obter mais detalhes sobre como usar esses produtos. |

Nosso objetivo no trabalho com a Ping Identity e Auth0 é garantir que todos os clientes de controlo de acesso tem um caminho de migração das respetivas aplicações e serviços que minimiza a quantidade de trabalho necessário para mover de controlo de acesso.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Serviços da Web que utilizam a autenticação do Active Directory

Para serviços da web que estão protegidos por tokens emitidos pelo controlo de acesso, o controlo de acesso oferece as seguintes funcionalidades e capacidades:

- Capacidade de registrar um ou mais *identidades do serviço* no seu espaço de nomes do controlo de acesso. Identidades de serviço podem ser utilizadas para pedir tokens.
- Suporte para o OAuth WRAP e OAuth 2.0 rascunho 13 em protocolos para pedir tokens, utilizando os seguintes tipos de credenciais:
    - Uma palavra-passe simple que é criada para a identidade de serviço
    - A sessão iniciada SWT com uma chave simétrica ou X509 certificado
    - Um token SAML emitido por um fornecedor de identidade fidedignos (normalmente, uma instância do AD FS)
- Suporte para os seguintes formatos de token: JWT, SAML 1.1, SAML 2.0 e SWT.
- Regras de transformação de token simples.

Identidades do serviço no controlo de acesso são normalmente utilizadas para implementar a autenticação de servidor a servidor. 

#### <a name="migrate-to-azure-active-directory"></a>Migrar para o Azure Active Directory

Nossa recomendação para este tipo de fluxo de autenticação é migrar para o [do Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). O Azure AD é o fornecedor de identidade com base na cloud da Microsoft trabalham contas escolares ou profissionais. O Azure AD é o fornecedor de identidade para o Office 365, Azure e muito mais. 

Também pode utilizar o Azure AD para autenticação de servidor para servidor utilizando a implementação do Azure AD de concessão de credenciais de cliente OAuth. A tabela seguinte compara as funcionalidades de controlo de acesso na autenticação de servidor para servidor com as que estão disponíveis no Azure AD.

| Capacidade | Suporte de controlo de acesso | Suporte do Azure AD |
| ---------- | ----------- | ---------------- |
| Como registar-se um serviço web | Criar uma entidade confiadora no portal de gestão do controlo de acesso | Criar uma aplicação web do Azure AD no portal do Azure |
| Como registar-se um cliente | Criar uma identidade de serviço no portal de gestão de controlo de acesso | Criar outra aplicação de web do Azure AD no portal do Azure |
| Protocolo utilizado |-Protocolo de OAuth WRAP<br />-Concessão de credenciais de cliente de 13 de rascunho OAuth 2.0 | Concessão de credenciais de cliente OAuth 2.0 |
| Métodos de autenticação de cliente |-Palavra-passe simples<br />-SWT assinado<br />-Token SAML de um fornecedor de identidade federada |-Palavra-passe simples<br />- Signed JWT |
| Formatos de token |- JWT<br />-SAML 1.1<br />- SAML 2.0<br />- SWT<br /> | JWT apenas |
| Transformação de token |-Adicionar declarações personalizadas<br />-Lógica de emissão de afirmação de if-then simples | Adicionar declarações personalizadas | 
| Automatizar tarefas de configuração e gestão | Suportado através do serviço de gestão de controle de acesso | Suportado através do Microsoft Graph e do Azure AD Graph API |

Para obter orientações sobre cenários de implementação-para-servidor, consulte os seguintes recursos:

- Secção de serviços a [Guia do programador do Azure AD](https://aka.ms/aaddev)
- [Exemplo de código de daemon com credenciais de cliente de palavra-passe simples](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Exemplo de código de daemon com credenciais de cliente de certificado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrar para a Ping Identity ou Auth0

Em alguns casos, pode achar que as credenciais de cliente do Azure AD e o OAuth concedem implementação não são suficientes para substituir o controlo de acesso na sua arquitetura sem alterações importantes no código. Alguns exemplos comuns podem incluir:

- Autenticação de servidor para servidor usando formatos de token que não seja JWTs.
- Autenticação de servidor para servidor com um token de entrada fornecido pelo fornecedor de identidade externo.
- Autenticação de servidor para servidor com as regras de transformação de token do Azure AD não consegue reproduzir.

Nestes casos, pode optar por migrar a sua aplicação web para outro serviço de autenticação em nuvem. Recomendamos a explorar as seguintes opções. Cada uma das seguintes opções oferecem recursos semelhantes ao controlo de acesso:

|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) é um serviço de identidade de cloud flexíveis que tenha criado [orientação de migração de alto nível para os clientes de controlo de acesso](https://auth0.com/acs)e suporta quase todas as funcionalidades que o ACS faz. |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [A ping Identity](https://www.pingidentity.com) oferece duas soluções semelhantes às ACS. PingOne é um serviço de identidade de cloud que oferece suporte a muitos dos mesmos recursos como o ACS e PingFederate é semelhante no produto de identidade no local que oferece mais flexibilidade. Consulte a [orientações de extinção de ACS de Ping](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) para obter mais detalhes sobre como usar esses produtos. |

Nosso objetivo no trabalho com a Ping Identity e Auth0 é garantir que todos os clientes de controlo de acesso tem um caminho de migração das respetivas aplicações e serviços que minimiza a quantidade de trabalho necessário para mover de controlo de acesso.

#### <a name="passthrough-authentication"></a>Autenticação pass-through

Para autenticação pass-through com a transformação de token arbitrária, não existe nenhuma tecnologia Microsoft equivalente no ACS. Se for esse que seus clientes necessitam, Auth0 seja o que oferece a solução de aproximação mais próxima.

## <a name="questions-concerns-and-feedback"></a>Perguntas, questões e comentários

Estamos cientes de que muitos clientes de controlo de acesso não encontrará um caminho claro de migração depois de ler este artigo. Poderá ter alguns assistência ou diretrizes para determinar o plano adequado. Se gostaria de discutir os seus cenários de migração e perguntas, deixe um comentário nesta página.
