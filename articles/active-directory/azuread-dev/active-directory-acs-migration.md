---
title: Migrar do Serviço de Controlo de Acessos Azure / Microsoft Docs
description: Conheça as opções de mudança de apps e serviços do Serviço de Controlo de Acesso Azure (ACS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: how-to
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: eda648a4d00a0ab4a51c66510060ce16421972ff
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020015"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Como: Migrar do Serviço de Controlo de Acesso Azure

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O Microsoft Azure Access Control Service (ACS), um serviço do Azure Ative Directory (Azure AD), será reformado a 7 de novembro de 2018. As aplicações e serviços que atualmente utilizam o Controlo de Acesso devem ser totalmente migrados para um mecanismo de autenticação diferente até lá. Este artigo descreve recomendações para os clientes atuais, uma vez que pretende depreciar o seu uso do Controlo de Acesso. Se não utilizar atualmente o Controlo de Acesso, não precisa de tomar nenhuma medida.

## <a name="overview"></a>Descrição geral

O Access Control é um serviço de autenticação em nuvem que oferece uma forma fácil de autenticar e autorizar os utilizadores a acederem às suas aplicações e serviços web. Permite que muitas funcionalidades de autenticação e autorização sejam tidas em conta no seu código. O Access Control é usado principalmente por desenvolvedores e arquitetos de clientes Microsoft .NET, ASP.NET aplicações web e serviços web da Windows Communication Foundation (WCF).

As caixas de utilização do Controlo de Acesso podem ser divididas em três categorias principais:

- Autenticação em certos serviços na nuvem da Microsoft, incluindo Azure Service Bus e Dynamics CRM. As aplicações do cliente obtêm fichas do Access Control para autenticar estes serviços para realizar várias ações.
- Adicionar autenticação a aplicações web, personalizadas e pré-embaladas (como o SharePoint). Ao utilizar a autenticação "passiva" do Access Control, as aplicações web podem suportar o sôm-in com uma conta Microsoft (anteriormente ID ao vivo), e com contas do Google, Facebook, Yahoo, Azure AD e Ative Directory Federation Services (AD FS).
- Garantir serviços web personalizados com fichas emitidas pelo Access Control. Ao utilizarem a autenticação "ativa", os serviços web podem garantir que permitem o acesso apenas a clientes conhecidos que tenham autenticado com o Controlo de Acesso.

Cada um destes casos de utilização e as suas estratégias de migração recomendadas são discutidos nas seguintes secções.

> [!WARNING]
> Na maioria dos casos, são necessárias alterações significativas no código para migrar as aplicações e serviços existentes para tecnologias mais recentes. Recomendamos que comece imediatamente a planear e executar a sua migração para evitar eventuais interrupções ou tempo de inatividade.

O Controlo de Acesso tem os seguintes componentes:

- Um serviço de token seguro (STS), que recebe pedidos de autenticação e emite fichas de segurança em troca.
- O portal clássico Azure, onde cria, apaga e permite e desativa espaços de nomes do Access Control.
- Um portal de gestão de controlo de acesso separado, onde personaliza e configura espaços de nomes do Access Control.
- Um serviço de gestão, que pode utilizar para automatizar as funções dos portais.
- Um motor de regra de transformação simbólica, que pode usar para definir lógica complexa para manipular o formato de saída dos tokens que o Access Control problema.

Para utilizar estes componentes, tem de criar um ou mais espaços de nomes do Controlo de Acesso. Um *espaço de nome* é uma instância dedicada do Controlo de Acesso com a qual as suas aplicações e serviços comunicam. Um espaço de nome é isolado de todos os outros clientes do Access Control. Outros clientes do Access Control usam os seus próprios espaços de nome. Um espaço de nome no Access Control tem um URL dedicado que se parece com este:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Toda a comunicação com o STS e as operações de gestão são feitas neste URL. Usas caminhos diferentes para diferentes propósitos. Para determinar se as suas aplicações ou serviços utilizam o Access Control, monitorize qualquer tráfego para https:// &lt; espaço de &gt; nomes .accesscontrol.windows.net. Qualquer tráfego para este URL é tratado pelo Access Control e precisa ser descontinuado. 

A exceção a isto é qualquer tráfego para `https://accounts.accesscontrol.windows.net` . O tráfego para este URL já é tratado por um serviço diferente e **não é** afetado pela depreciação do Controlo de Acesso. 

Para obter mais informações sobre o Controlo de Acesso, consulte [o Access Control Service 2.0 (arquivado)](/previous-versions/azure/azure-services/hh147631(v=azure.100)).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Descubra quais das suas aplicações serão impactadas

Siga os passos nesta secção para saber qual das suas aplicações será impactada pela reforma da ACS.

### <a name="download-and-install-acs-powershell"></a>Descarregue e instale o ACS PowerShell

1. Vá à PowerShell Gallery e baixe [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
2. Instale o módulo executando

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Obtenha uma lista de todos os comandos possíveis correndo

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Para obter ajuda num comando específico, corra:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    onde `[Command-Name]` está o nome do comando ACS.

### <a name="list-your-acs-namespaces"></a>Enunse os seus espaços de nomes ACS

1. Ligue-se ao ACS utilizando o **cmdlet Connect-AcsAccount.**
  
    Pode ter de correr antes de `Set-ExecutionPolicy -ExecutionPolicy Bypass` poder executar comandos e ser o administrador dessas subscrições para executar os comandos.

2. Enuma as suas subscrições Azure disponíveis utilizando o cmdlet **Get-AcsSubscription.**
3. Enuma os seus espaços de nome ACS utilizando o cmdlet **Get-AcsNamespace.**

### <a name="check-which-applications-will-be-impacted"></a>Verifique quais aplicações serão impactadas

1. Use o espaço de nome do passo anterior e vá para `https://<namespace>.accesscontrol.windows.net`

    Por exemplo, se um dos espaços de nome é contoso-teste, vá para `https://contoso-test.accesscontrol.windows.net`

2. No **âmbito das relações trust**, selecione **aplicações do partido Relying** para ver a lista de aplicações que serão impactadas pela reforma da ACS.
3. Repita os passos 1-2 para qualquer outro espaço de nome ACS que tenha.

## <a name="retirement-schedule"></a>Horário de aposentadoria

A partir de novembro de 2017, todos os componentes do Controlo de Acesso estão totalmente suportados e operacionais. A única restrição é que [não é possível criar novos espaços de nomes para controlo de acesso através do portal clássico Azure.](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Aqui está o horário para depreciar os componentes do Controlo de Acesso:

- **novembro de 2017**: A experiência de administração Azure AD no portal clássico Azure [é aposentada.](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/) Neste momento, a gestão do namespace para controlo de acesso está disponível num novo URL dedicado: `https://manage.windowsazure.com?restoreClassic=true` . Utilize este URl para visualizar os espaços de nome existentes, ative e desativar espaços de nomes e eliminar espaços de nome, se assim o desejar.
- **2 de abril de 2018**: O portal clássico do Azure está completamente reformado, o que significa que a gestão do espaço de identificação do Access Control já não está disponível através de qualquer URL. Neste ponto, não é possível desativar ou ativar, eliminar ou enumerar os seus espaços de nomes do Access Control. No entanto, o portal de gestão do Controlo de Acesso estará totalmente funcional e localizado em `https://\<namespace\>.accesscontrol.windows.net` . Todos os outros componentes do Controlo de Acesso continuam a funcionar normalmente.
- **7 de novembro de 2018**: Todos os componentes do Controlo de Acesso estão permanentemente desligados. Isto inclui o portal de gestão do Controlo de Acesso, o serviço de gestão, STS e o motor de regra de transformação simbólica. Neste momento, quaisquer pedidos enviados para o Controlo de Acesso (localizados em \<namespace\> .accesscontrol.windows.net) falham. Você deveria ter migrado todas as aplicações e serviços existentes para outras tecnologias muito antes deste tempo.

> [!NOTE]
> Uma política desativa espaços de nome que não tenham solicitado um token por um período de tempo. No início de setembro de 2018, este período de tempo está atualmente em 14 dias de inatividade, mas este será encurtado para 7 dias de inatividade nas próximas semanas. Se tiver espaços de nomes do Access Control que estão atualmente desativados, pode [descarregar e instalar o ACS PowerShell](#download-and-install-acs-powershell) para reativar o espaço de nomes.

## <a name="migration-strategies"></a>Estratégias de migração

As seguintes secções descrevem recomendações de alto nível para migrar do Controlo de Acesso para outras tecnologias da Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Clientes dos serviços de cloud da Microsoft

Cada serviço de cloud da Microsoft que aceita fichas que são emitidas pelo Access Control suporta agora pelo menos uma forma alternativa de autenticação. O mecanismo de autenticação correto varia para cada serviço. Recomendamos que consulte a documentação específica de cada serviço para orientação oficial. Por conveniência, cada conjunto de documentação é fornecido aqui:

| Serviço | Orientação |
| ------- | -------- |
| Azure Service Bus | [Migrar para assinaturas de acesso partilhado](../../service-bus-messaging/service-bus-migrate-acs-sas.md) |
| Relé de ônibus de serviço Azure | [Migrar para assinaturas de acesso partilhado](../../azure-relay/relay-migrate-acs-sas.md) |
| Cache gerido azure | [Migrar para a Cache do Azure para Redis](../../azure-cache-for-redis/cache-faq.md) |
| Azure DataMarket | [Migrar para as APIs dos Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/) |
| Serviços BizTalk | [Migrar para a funcionalidade de Aplicações Lógicas do Serviço de Aplicações Azure](https://azure.microsoft.com/services/cognitive-services/) |
| Serviços de Multimédia do Azure | [Migrar para a autenticação AD AZure](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Atualize o agente de backup Azure](../../backup/backup-azure-file-folder-backup-faq.md) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>Clientes SharePoint

Os clientes SharePoint 2013, 2016 e SharePoint Online há muito que utilizam ACS para fins de autenticação na nuvem, nas instalações e cenários híbridos. Algumas funcionalidades do SharePoint e casos de utilização serão afetados pela reforma da ACS, enquanto outros não. A tabela abaixo resume a orientação de migração para alguns dos recursos mais populares do SharePoint que alavancam o ACS:

| Funcionalidade | Orientação |
| ------- | -------- |
| Autenticação de utilizadores da Azure AD | Anteriormente, a Azure AD não suportava fichas SAML 1.1 exigidas pelo SharePoint para autenticação, e a ACS foi utilizada como um intermediário que tornava o SharePoint compatível com os formatos Aken AD Azure. Agora, pode [ligar o SharePoint diretamente ao Azure AD utilizando o Azure AD App Gallery SharePoint na aplicação de instalações.](../saas-apps/sharepoint-on-premises-tutorial.md) |
| [A autenticação da aplicação & autenticação servidor-servidor no SharePoint nas instalações](/SharePoint/security-for-sharepoint-server/authentication-overview) | Não afetado pela reforma da ACS; não são necessárias alterações. | 
| [Baixa autorização de confiança para add-ins SharePoint (fornecedor hospedado e SharePoint hospedado)](/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Não afetado pela reforma da ACS; não são necessárias alterações. |
| [Pesquisa híbrida em nuvem sharePoint](/archive/blogs/spses/cloud-hybrid-search-service-application) | Não afetado pela reforma da ACS; não são necessárias alterações. |

### <a name="web-applications-that-use-passive-authentication"></a>Aplicações web que utilizam a autenticação passiva

Para aplicações web que usam o Controlo de Acesso para autenticação do utilizador, o Access Control fornece as seguintes funcionalidades e capacidades para desenvolvedores e arquitetos de aplicações web:

- Integração profunda com a Windows Identity Foundation (WIF).
- Federação com contas google, Facebook, Yahoo, Azure Ative e AD FS, e contas microsoft.
- Suporte para os seguintes protocolos de autenticação: OAuth 2.0 Draft 13, WS-Trust e Web Services Federation (WS-Federation).
- Suporte para os seguintes formatos simbólicos: JSON Web Token (JWT), SAML 1.1, SAML 2.0 e Simple Web Token (SWT).
- Uma experiência de descoberta de reinos domésticos, integrada na WIF, que permite aos utilizadores escolherem o tipo de conta que usam para iniciar scontabilidade. Esta experiência é hospedada pela aplicação web e é totalmente personalizável.
- Transformação simbólica que permite uma personalização rica das reclamações recebidas pela aplicação web do Access Control, incluindo:
    - Passar por reclamações de fornecedores de identidade.
    - Adicionando reclamações personalizadas adicionais.
    - Lógica simples se-então para emitir reclamações sob determinadas condições.

Infelizmente, não há um único serviço que ofereça todas estas capacidades equivalentes. Deve avaliar quais as capacidades do Controlo de Acesso de que necessita e, em seguida, escolher entre utilizar [o Azure Ative Directory](https://azure.microsoft.com/develop/identity/signin/), [O Azure Ative Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) ou outro serviço de autenticação em nuvem.

#### <a name="migrate-to-azure-active-directory"></a>Migrar para o Diretório Ativo Azure

Um caminho a ter em conta é integrar as suas apps e serviços diretamente com o Azure AD. Azure AD é o fornecedor de identidade baseado na nuvem para o trabalho da Microsoft ou contas escolares. Azure AD é o fornecedor de identidade da Microsoft 365, Azure, e muito mais. Fornece capacidades de autenticação federadas semelhantes ao Controlo de Acesso, mas não suporta todas as funcionalidades de Controlo de Acesso. 

O principal exemplo é a federação com fornecedores de identidade social, como facebook, Google e Yahoo. Se os seus utilizadores iniciarem sação com este tipo de credenciais, o Azure AD não é a solução para si. 

A Azure AD também não suporta necessariamente os mesmos protocolos de autenticação que o Access Control. Por exemplo, embora tanto o Access Control como o Azure AD suportem OAuth, existem diferenças subtis entre cada implementação. Implementações diferentes requerem que você modifique código como parte de uma migração.

No entanto, a Azure AD oferece várias vantagens potenciais para os clientes do Access Control. Suporta de forma nativa o trabalho da Microsoft ou as contas escolares hospedadas na nuvem, que são comumente usadas pelos clientes do Access Control. 

Um inquilino Azure AD também pode ser federado para um ou mais casos de Ative Directory no local via AD FS. Desta forma, a sua aplicação pode autenticar utilizadores e utilizadores baseados na nuvem que estão hospedados no local. Também apoia o protocolo WS-Federation, o que torna relativamente simples integrar-se com uma aplicação web utilizando WIF.

A tabela seguinte compara as funcionalidades do Controlo de Acesso que são relevantes para as aplicações web com as funcionalidades que estão disponíveis no Azure AD. 

A um nível elevado, *o Azure Ative Directory é provavelmente a melhor escolha para a sua migração se deixar que os utilizadores assinem apenas com o seu trabalho microsoft ou contas escolares.*

| Funcionalidade | Suporte ao Controlo de Acessos | Suporte AZURE AD |
| ---------- | ----------- | ---------------- |
| **Tipos de contas** | | |
| Trabalho da Microsoft ou contas escolares | Suportado | Suportado |
| Contas do Windows Server Ative Directy e AD FS |- Apoiado via federação com um inquilino da AD Azure <br />- Apoiado através de federação direta com AD FS | Só apoiado via federação com um inquilino da AD Azure | 
| Contas de outros sistemas de gestão de identidade da empresa |- Possível via federação com um inquilino da AD Azure <br />- Apoiado através de federação direta | Possível via federação com um inquilino AD Azure |
| Microsoft contabiliza uso pessoal | Suportado | Suportado através do protocolo Azure AD v2.0 OAuth, mas não sobre quaisquer outros protocolos | 
| Contas facebook, Google, Yahoo | Suportado | Não apoiado de forma alguma |
| **Protocolos e compatibilidade da SDK** | | |
| WIF | Suportado | Instruções suportadas, mas limitadas estão disponíveis |
| WS-Federation | Suportado | Suportado |
| OAuth 2.0 | Apoio ao Projeto 13 | Suporte para RFC 6749, a especificação mais moderna |
| WS-Trust | Suportado | Não suportado |
| **Formatos token** | | |
| JWT | Suportado em Beta | Suportado |
| SAML 1.1 | Suportado | Pré-visualizar |
| SAML 2.0 | Suportado | Suportado |
| SWT | Suportado | Não suportado |
| **Personalizações** | | |
| Descoberta de reinos domésticos personalizáveis descoberta/uI de recolha de conta | Código transferível que pode ser incorporado em apps | Não suportado |
| Carregar certificados de assinatura de token personalizados | Suportado | Suportado |
| Personalizar reclamações em fichas |- Passar por pedidos de entrada de fornecedores de identidade<br />- Obter ficha de acesso do fornecedor de identidade como reivindicação<br />- Emitir reclamações de saída com base em valores de sinistros de entrada<br />- Emitir reclamações de saída com valores constantes |- Não é possível passar por reclamações de fornecedores de identidade federados<br />- Não é possível obter acesso a um token do fornecedor de identidade como reivindicação<br />- Não é possível emitir pedidos de produção com base em valores de sinistros de entrada<br />- Pode emitir reclamações de saída com valores constantes<br />- Pode emitir reclamações de saída com base em propriedades de utilizadores sincronizados com a Azure AD |
| **Automatização** | | |
| Automatizar tarefas de configuração e gestão | Suportado através do Serviço de Gestão de Controlo de Acesso | Suportado usando a API do Gráfico microsoft |

Se decidir que o Azure AD é o melhor caminho de migração para as suas aplicações e serviços, deve estar ciente de duas formas de integrar a sua app com a Azure AD.

Para utilizar WS-Federation ou WIF para integrar com a Azure AD, recomendamos seguir a abordagem descrita em [Configure federado único sinal para uma aplicação não-galeria](../manage-apps/configure-saml-single-sign-on.md). O artigo refere-se à configuração do Azure AD para um único sign-on baseado em SAML, mas também funciona para configurar a WS-Federation. Seguindo esta abordagem requer uma licença Azure AD Premium. Esta abordagem tem duas vantagens:

- Obtém-se a flexibilidade total da personalização do Azure AD. Pode personalizar as reclamações emitidas pela Azure AD para corresponder às reclamações emitidas pelo Access Control. Isto inclui especialmente a reclamação do ID do utilizador ou do identificador de nomes. Para continuar a receber IDentifiers de utilizador consistentes para os seus utilizadores depois de alterar as tecnologias, certifique-se de que os IDs do utilizador emitidos pela Azure AD correspondem aos emitidos pelo Access Control.
- Você pode configurar um certificado de assinatura simbólica específico para a sua aplicação, e com uma vida inteira que você controla.

> [!NOTE]
> Esta abordagem requer uma licença Azure AD Premium. Se é cliente do Access Control e precisa de uma licença premium para configurar um único sinal para uma aplicação, contacte-nos. Teremos todo o gosto em fornecer licenças de desenvolvimento para que possa utilizar.

Uma abordagem alternativa é seguir [esta amostra de código,](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)que dá instruções ligeiramente diferentes para a criação da WS-Federação. Esta amostra de código não utiliza WIF, mas sim o middleware ASP.NET 4.5 OWIN. No entanto, as instruções para o registo de aplicações são válidas para aplicações que usam WIF, e não requerem uma licença AZure AD Premium. 

Se escolher esta abordagem, tem de compreender a assinatura da [chave de capotamento em Azure AD](../develop/active-directory-signing-key-rollover.md). Esta abordagem utiliza a chave de assinatura global Azure AD para emitir fichas. Por predefinição, a WIF não atualiza automaticamente as teclas de assinatura. Quando o Azure AD roda as suas chaves de assinatura global, a sua implementação wif precisa estar preparada para aceitar as alterações. Para obter mais informações, consulte [informações importantes sobre a assinatura da chave de capotamento em Azure AD](/previous-versions/azure/dn641920(v=azure.100)).

Se puder integrar-se com a Azure AD através dos protocolos OpenID Connect ou OAuth, recomendamos que o faça. Dispomos de documentação e orientação extensiva sobre como integrar o Azure AD na sua aplicação web disponível no nosso [guia de desenvolvimento AZure AD.](../develop/index.yml)

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrar para O Diretório Ativo B2C

O outro caminho de migração a considerar é Azure AD B2C. O Azure AD B2C é um serviço de autenticação em nuvem que, tal como o Access Control, permite aos desenvolvedores subcontratar a sua lógica de autenticação e gestão de identidade a um serviço de cloud. É um serviço pago (com níveis gratuitos e premium) que é projetado para aplicações viradas para o consumidor que podem ter até milhões de utilizadores ativos.

Tal como o Access Control, uma das funcionalidades mais atrativas do Azure AD B2C é que suporta muitos tipos de contas diferentes. Com o Azure AD B2C, pode iniciar súbência nos utilizadores utilizando a sua conta Microsoft, ou Facebook, Google, LinkedIn, GitHub ou Yahoo, e muito mais. O Azure AD B2C também suporta "contas locais", ou nome de utilizador e palavras-passe que os utilizadores criam especificamente para a sua aplicação. O Azure AD B2C também fornece uma extensibilidade rica que pode usar para personalizar os seus fluxos de entrada. 

No entanto, o Azure AD B2C não suporta a amplitude de protocolos de autenticação e formatos simbólicos que os clientes do Access Control podem necessitar. Também não pode utilizar o Azure AD B2C para obter fichas e consulta para obter informações adicionais sobre o utilizador do fornecedor de identidade, Microsoft ou de outra forma.

A tabela seguinte compara as funcionalidades do Controlo de Acesso que são relevantes para as aplicações web com as que estão disponíveis no Azure AD B2C. A um nível elevado, *o Azure AD B2C é provavelmente a escolha certa para a sua migração se a sua aplicação estiver virada para o consumidor, ou se suporta vários tipos de contas diferentes.*

| Funcionalidade | Suporte ao Controlo de Acessos | Suporte Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Tipos de contas** | | |
| Trabalho da Microsoft ou contas escolares | Suportado | Suportado através de políticas personalizadas  |
| Contas do Windows Server Ative Directy e AD FS | Apoiado através de federação direta com AD FS | Apoiado através da federação SAML através de políticas personalizadas |
| Contas de outros sistemas de gestão de identidade da empresa | Apoiado através de federação direta através de WS-Federation | Apoiado através da federação SAML através de políticas personalizadas |
| Microsoft contabiliza uso pessoal | Suportado | Suportado | 
| Contas facebook, Google, Yahoo | Suportado | Facebook e Google suportados nativamente, Yahoo suportado via OpenID Connect federação usando políticas personalizadas |
| **Protocolos e compatibilidade da SDK** | | |
| Windows Identity Foundation (WIF) | Suportado | Não suportado |
| WS-Federation | Suportado | Não suportado |
| OAuth 2.0 | Apoio ao Projeto 13 | Suporte para RFC 6749, a especificação mais moderna |
| WS-Trust | Suportado | Não suportado |
| **Formatos token** | | |
| JWT | Suportado em Beta | Suportado |
| SAML 1.1 | Suportado | Não suportado |
| SAML 2.0 | Suportado | Não suportado |
| SWT | Suportado | Não suportado |
| **Personalizações** | | |
| Descoberta de reinos domésticos personalizáveis descoberta/uI de recolha de conta | Código transferível que pode ser incorporado em apps | UI totalmente personalizável via CSS personalizado |
| Carregar certificados de assinatura de token personalizados | Suportado | Chaves de assinatura personalizadas, não certificados, suportados através de políticas personalizadas |
| Personalizar reclamações em fichas |- Passar por pedidos de entrada de fornecedores de identidade<br />- Obter ficha de acesso do fornecedor de identidade como reivindicação<br />- Emitir reclamações de saída com base em valores de sinistros de entrada<br />- Emitir reclamações de saída com valores constantes |- Pode passar por reclamações de fornecedores de identidade; políticas personalizadas necessárias para algumas reivindicações<br />- Não é possível obter acesso a um token do fornecedor de identidade como reivindicação<br />- Pode emitir reclamações de saída com base em valores de pedidos de entrada através de políticas personalizadas<br />- Pode emitir reclamações de saída com valores constantes através de políticas personalizadas |
| **Automatização** | | |
| Automatizar tarefas de configuração e gestão | Suportado através do Serviço de Gestão de Controlo de Acesso |- Criação de utilizadores autorizados a utilizar a API do Microsoft Graph<br />- Não é possível criar inquilinos b2C, aplicações ou políticas programáticas |

Se decidir que o Azure AD B2C é o melhor caminho de migração para as suas aplicações e serviços, comece pelos seguintes recursos:

- [Documentação Azure AD B2C](../../active-directory-b2c/overview.md)
- [Políticas personalizadas do Azure AD B2C](../../active-directory-b2c/custom-policy-overview.md)
- [Preços Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrar para a Identidade do Ping ou Auth0

Em alguns casos, poderá descobrir que a Azure AD e a Azure AD B2C não são suficientes para substituir o Controlo de Acesso nas suas aplicações web sem fazer grandes alterações de código. Alguns exemplos comuns podem incluir:

- As aplicações web que usam WIF ou WS-Federation para iniciar sismo com fornecedores de identidade social, como google ou Facebook.
- Aplicações web que executam federação direta a um fornecedor de identidade empresarial ao longo do protocolo WS-Federation.
- Aplicações web que requerem o token de acesso emitido por um fornecedor de identidade social (como Google ou Facebook) como uma reivindicação nos tokens emitidos pelo Access Control.
- Aplicações web com regras complexas de transformação de símbolos que Azure AD ou Azure AD B2C não podem reproduzir.
- Aplicações web multi-arrendatários que usam ACS para gerir centralmente a federação a muitos fornecedores de identidade diferentes

Nestes casos, poderá considerar a migração da sua aplicação web para outro serviço de autenticação em nuvem. Recomendamos explorar as seguintes opções. Cada uma das seguintes opções oferece capacidades semelhantes ao Controlo de Acesso:

![Esta imagem mostra o logótipo Auth0](./media/active-directory-acs-migration/rsz-auth0.png) 

[O Auth0](https://auth0.com/acs) é um serviço de identidade em nuvem flexível que criou [orientações de migração de alto nível para os clientes do Access Control](https://auth0.com/acs), e suporta quase todas as funcionalidades que a ACS faz.

![Esta imagem mostra o logotipo da Identidade ping](./media/active-directory-acs-migration/rsz-ping.png)

[Ping Identity](https://www.pingidentity.com) oferece duas soluções semelhantes à ACS. PingOne é um serviço de identidade em nuvem que suporta muitas das mesmas funcionalidades que o ACS, e o PingFederate é um produto de identidade semelhante nas instalações que oferece mais flexibilidade. Consulte a [orientação de aposentadoria ACS da Ping](https://www.pingidentity.com/en/company/blog/posts/2017/migrating-from-microsoft-acs-to-ping-identity.html) para obter mais detalhes sobre a utilização destes produtos.

O nosso objetivo em trabalhar com a Ping Identity e a Auth0 é garantir que todos os clientes do Access Control tenham uma rota de migração para as suas apps e serviços que minimizem a quantidade de trabalho necessário para se deslocarem do Controlo de Acesso.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Serviços web que utilizam a autenticação ativa

Para serviços web que são protegidos com fichas emitidas pelo Access Control, o Access Control oferece as seguintes funcionalidades e capacidades:

- Capacidade de registar uma ou mais *identidades de serviço* no seu espaço de nomes Access Control. As identidades de serviço podem ser usadas para solicitar fichas.
- Apoio ao OAuth WRAP e ao OAuth 2.0 Projeto 13 protocolos para pedido de fichas, utilizando os seguintes tipos de credenciais:
    - Uma senha simples criada para a identidade do serviço
    - Um SWT assinado utilizando uma chave simétrica ou certificado X509
    - Um símbolo SAML emitido por um fornecedor de identidade fidedigno (normalmente, uma instância AD FS)
- Suporte para os seguintes formatos simbólicos: JWT, SAML 1.1, SAML 2.0 e SWT.
- Regras simples de transformação simbólica.

As identidades de serviço no Controlo de Acesso são normalmente utilizadas para implementar a autenticação do servidor-para-servidor. 

#### <a name="migrate-to-azure-active-directory"></a>Migrar para o Diretório Ativo Azure

A nossa recomendação para este tipo de fluxo de autenticação é migrar para [o Azure Ative Directory.](https://azure.microsoft.com/develop/identity/signin/) Azure AD é o fornecedor de identidade baseado na nuvem para o trabalho da Microsoft ou contas escolares. Azure AD é o fornecedor de identidade da Microsoft 365, Azure, e muito mais. 

Também pode utilizar o AZure AD para autenticação servidor-a-servidor utilizando a implementação AZure AD da concessão de credenciais de cliente OAuth. A tabela seguinte compara as capacidades do Controlo de Acesso na autenticação servidor-servidor com as que estão disponíveis no AZure AD.

| Funcionalidade | Suporte ao Controlo de Acessos | Suporte AZURE AD |
| ---------- | ----------- | ---------------- |
| Como registar um serviço web | Criar uma festa de adu contar no portal de gestão do Controlo de Acesso | Criar uma aplicação web AZure AD no portal Azure |
| Como registar um cliente | Criar uma identidade de serviço no portal de gestão do Controlo de Acesso | Criar outra aplicação web AZure AD no portal Azure |
| Protocolo utilizado |- Protocolo de WRAP OAuth<br />- OAuth 2.0 Esboço 13 concessão de credenciais de cliente | Concessão de credenciais de cliente OAuth 2.0 |
| Métodos de autenticação do cliente |- Senha simples<br />- SWT assinado<br />- Ficha SAML de um fornecedor de identidade federado |- Senha simples<br />- JWT assinado |
| Formatos token |- JWT<br />- SAML 1.1<br />- SAML 2.0<br />- SWT<br /> | Apenas JWT |
| Transformação simbólica |- Adicionar reclamações personalizadas<br />- Lógica simples de emissão de reclamação se-então | Adicionar reclamações personalizadas | 
| Automatizar tarefas de configuração e gestão | Suportado através do Serviço de Gestão de Controlo de Acesso | Suportado usando a API do Gráfico microsoft |

Para obter orientações sobre a implementação de cenários servidor-a-servidor, consulte os seguintes recursos:

- Secção de serviço-a-serviço do guia de [desenvolvimento Azure AD](../develop/index.yml)
- [Amostra de código Daemon usando credenciais simples do cliente de senha](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Amostra de código Daemon usando credenciais de cliente certificado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrar para a Identidade do Ping ou Auth0

Em alguns casos, poderá descobrir que as credenciais de clientes Azure AD e a implementação da concessão OAuth não são suficientes para substituir o Controlo de Acesso na sua arquitetura sem grandes alterações de código. Alguns exemplos comuns podem incluir:

- Autenticação servidor-a-servidor utilizando formatos simbólicos que não jWTs.
- Autenticação servidor-servidor utilizando um token de entrada fornecido por um fornecedor de identidade externo.
- Autenticação servidor-a-servidor com regras de transformação simbólicas que o AZure AD não consegue reproduzir.

Nestes casos, poderá considerar migrar a sua aplicação web para outro serviço de autenticação em nuvem. Recomendamos explorar as seguintes opções. Cada uma das seguintes opções oferece capacidades semelhantes ao Controlo de Acesso:

![Esta imagem mostra o logótipo Auth0](./media/active-directory-acs-migration/rsz-auth0.png)

[O Auth0](https://auth0.com/acs) é um serviço de identidade em nuvem flexível que criou [orientações de migração de alto nível para os clientes do Access Control](https://auth0.com/acs), e suporta quase todas as funcionalidades que a ACS faz.

![Esta imagem mostra o logótipo Ping Identity ](./media/active-directory-acs-migration/rsz-ping.png)
 [Ping Identity](https://www.pingidentity.com) oferece duas soluções semelhantes à ACS. PingOne é um serviço de identidade em nuvem que suporta muitas das mesmas funcionalidades que o ACS, e o PingFederate é um produto de identidade semelhante nas instalações que oferece mais flexibilidade. Consulte a [orientação de aposentadoria ACS da Ping](https://www.pingidentity.com/en/company/blog/posts/2017/migrating-from-microsoft-acs-to-ping-identity.html) para obter mais detalhes sobre a utilização destes produtos.

O nosso objetivo em trabalhar com a Ping Identity e a Auth0 é garantir que todos os clientes do Access Control tenham uma rota de migração para as suas apps e serviços que minimizem a quantidade de trabalho necessário para se deslocarem do Controlo de Acesso.

#### <a name="passthrough-authentication"></a>Autenticação passthrough

Para a autenticação passthrough com transformação arbitrária de token, não existe tecnologia equivalente da Microsoft para ACS. Se é disso que os seus clientes precisam, a Auth0 pode ser a que fornece a solução de aproximação mais próxima.

## <a name="questions-concerns-and-feedback"></a>Perguntas, preocupações e feedback

Sabemos que muitos clientes do Access Control não encontrarão um caminho de migração claro depois de lerem este artigo. Pode precisar de ajuda ou orientação para determinar o plano certo. Se quiser discutir os seus cenários e questões de migração, por favor deixe um comentário nesta página.