---
title: Migrar do Serviço de Controlo de Acesso Azure  Microsoft Docs
description: Conheça as opções para movimentar aplicações e serviços do Serviço de Controlo de Acesso Do Azure (ACS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ms.openlocfilehash: 63ace9af31dd284c61fae188744b24361f33c170
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377908"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Como: Migrar do Serviço de Controlo de Acesso Azure

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O Microsoft Azure Access Control Service (ACS), um serviço do Azure Ative Directory (Azure AD), será retirado a 7 de novembro de 2018. As aplicações e serviços que utilizam atualmente o Controlo de Acesso devem ser totalmente migrados para um mecanismo de autenticação diferente até lá. Este artigo descreve recomendações para os clientes atuais, uma vez que planeia depreciar o seu uso do Controlo de Acesso. Se não utilizar atualmente o Controlo de Acesso, não precisa de tomar qualquer medida.

## <a name="overview"></a>Descrição geral

O Access Control é um serviço de autenticação em nuvem que oferece uma forma fácil de autenticar e autorizar os utilizadores a acederem às suas aplicações e serviços web. Permite que muitas funcionalidades de autenticação e autorização sejam tidas em conta fora do seu código. O Access Control é usado principalmente por programadores e arquitetos de clientes Microsoft .NET, ASP.NET aplicações web e serviços web da Fundação de Comunicação do Windows (WCF).

Os casos de utilização do Controlo de Acesso podem ser divididos em três categorias principais:

- Autenticação a certos serviços na nuvem da Microsoft, incluindo o Azure Service Bus e o Dynamics CRM. As aplicações do cliente obtêm fichas do Controlo de Acesso para autenticar estes serviços para realizar várias ações.
- Adicionar autenticação a aplicações web, personalizadas e pré-embaladas (como o SharePoint). Utilizando a autenticação "passiva" do Access Control, as aplicações web podem suportar o início de sessão com uma conta Microsoft (anteriormente LIVE ID), e com contas do Google, Facebook, Yahoo, Azure AD e Ative Directory Federation Services (AD FS).
- Garantir serviços web personalizados com fichas emitidas pelo Access Control. Utilizando a autenticação "ativa", os serviços web podem garantir que permitem o acesso apenas a clientes conhecidos que tenham autenticado com controlo de acesso.

Cada um destes casos de utilização e as suas estratégias de migração recomendadas são discutidos nas seguintes secções.

> [!WARNING]
> Na maioria dos casos, são necessárias alterações significativas de código para migrar as aplicações e serviços existentes para tecnologias mais recentes. Recomendamos que comece imediatamente a planear e executar a sua migração para evitar eventuais interrupções ou tempo de inatividade.

O Controlo de Acesso tem os seguintes componentes:

- Um serviço de fichas seguras (STS), que recebe pedidos de autenticação e emite fichas de segurança em troca.
- O portal clássico Azure, onde cria, elimina e ativa e desativa espaços de nomes do Controle de Acesso.
- Um portal separado de gestão do Controlo de Acesso, onde personaliza e configura espaços de nome sinuoso de Controlo de Acesso.
- Um serviço de gestão, que pode utilizar para automatizar as funções dos portais.
- Um motor de regra de transformação simbólica, que pode usar para definir lógica complexa para manipular o formato de saída de fichas que o Controlo de Acesso emite.

Para utilizar estes componentes, tem de criar um ou mais espaços de nomes do Controlo de Acesso. Um *espaço de nome* é um caso dedicado de Controlo de Acesso com o que as suas aplicações e serviços comunicam. Um espaço de nome está isolado de todos os outros clientes do Access Control. Outros clientes do Access Control usam os seus próprios espaços de nome. Um espaço de nome no Controlo de Acesso tem um URL dedicado que se parece com este:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Toda a comunicação com o STS e as operações de gestão são feitas neste URL. Usas caminhos diferentes para diferentes propósitos. Para determinar se as suas aplicações ou serviços utilizam o Access Control, monitorize para qualquer tráfego para https://&lt;espaço de nome&gt;.accesscontrol.windows.net. Qualquer tráfego para este URL é manuseado pelo Controlo de Acesso, e precisa de ser descontinuado. 

A exceção a isto é qualquer tráfego para `https://accounts.accesscontrol.windows.net`. O tráfego para este URL já é tratado por um serviço diferente e **não é** afetado pela depreciação do Controlo de Acesso. 

Para mais informações sobre o Controlo de Acesso, consulte o [Access Control Service 2.0 (arquivado)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Descubra qual das suas aplicações será impactada

Siga os passos nesta secção para saber qual das suas aplicações será impactada pela reforma da ACS.

### <a name="download-and-install-acs-powershell"></a>Descarregue e instale acs PowerShell

1. Vá à Galeria PowerShell e baixe [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
2. Instale o módulo em execução

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Obtenha uma lista de todos os comandos possíveis executando

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Para obter ajuda num comando específico, corra:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    onde `[Command-Name]` é o nome do comando ACS.

### <a name="list-your-acs-namespaces"></a>Listar os seus espaços de nome ACS

1. Ligue-se ao ACS utilizando o cmdlet **Connect-AcsAccount.**
  
    Poderá ter de executar `Set-ExecutionPolicy -ExecutionPolicy Bypass` antes de poder executar comandos e ser administrador dessas subscrições para executar os comandos.

2. Enumere as suas subscrições Azure disponíveis utilizando o cmdlet **Get-AcsSubscription.**
3. Enumere os seus espaços de nome ACS utilizando o cmdlet **Get-AcsNamespace.**

### <a name="check-which-applications-will-be-impacted"></a>Verifique quais as aplicações que serão impactadas

1. Use o espaço de nome do passo anterior e vá para `https://<namespace>.accesscontrol.windows.net`

    Por exemplo, se um dos espaços de nome for um teste de contoso, vá para `https://contoso-test.accesscontrol.windows.net`

2. No âmbito das **relações Trust**, selecione **as aplicações do partido Relying** para ver a lista de aplicações que serão impactadas pela reforma da ACS.
3. Repita os passos 1-2 para qualquer outro espaço de nome ACS que tenha.

## <a name="retirement-schedule"></a>Horário de reforma

A partir de novembro de 2017, todos os componentes do Controlo de Acesso estão totalmente suportados e operacionais. A única restrição é que não é possível criar novos espaços de nome sinuoso de Controlo de [Acesso através do portal clássico Azure.](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Aqui está o horário para depreciar os componentes do Controlo de Acesso:

- **novembro de 2017**: A experiência de administrador adia do Azure no portal clássico Azure [está reformada.](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/) Neste ponto, a gestão do espaço de nome para controlo de acesso está disponível num novo URL dedicado: `https://manage.windowsazure.com?restoreClassic=true`. Utilize este URl para visualizar os espaços de nomeexistentes, ativar e desativar espaços de nomes, e para eliminar espaços de nome, se assim o desejar.
- 2 de abril de **2018**: O portal clássico azure está completamente reformado, o que significa que a gestão do espaço de nome do Access Control já não está disponível através de qualquer URL. Neste ponto, não é possível desativar ou ativar, excluir ou enumerar os seus espaços de nome sinuoso controlo de acesso. No entanto, o portal de gestão do Controlo de Acesso estará totalmente funcional e localizado em `https://\<namespace\>.accesscontrol.windows.net`. Todos os outros componentes do Controlo de Acesso continuam a funcionar normalmente.
- **7 de novembro de 2018**: Todos os componentes do Controlo de Acesso estão permanentemente desligados. Isto inclui o portal de gestão do Controlo de Acesso, o serviço de gestão, sTS e o motor de regra de transformação simbólica. Neste ponto, quaisquer pedidos enviados para o Controlo de Acesso (localizados no espaço de nome \<\>.accesscontrol.windows.net) falham. Você deveria ter migrado todas as aplicações e serviços existentes para outras tecnologias muito antes deste tempo.

> [!NOTE]
> Uma política desativa espaços de nome que não solicitaram um símbolo por um período de tempo. A partir do início de setembro de 2018, este período de tempo encontra-se atualmente em 14 dias de inatividade, mas este será encurtado para 7 dias de inatividade nas próximas semanas. Se tiver espaços de nome sintetizadores de Controlo de Acesso que estão atualmente desativados, pode descarregar e instalar o [ACS PowerShell](#download-and-install-acs-powershell) para reativar o espaço de nomes.

## <a name="migration-strategies"></a>Estratégias de migração

As seguintes secções descrevem recomendações de alto nível para migrar do Controlo de Acesso para outras tecnologias da Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Clientes dos serviços de nuvem da Microsoft

Cada serviço na nuvem da Microsoft que aceita fichas emitidas pelo Access Control suporta agora pelo menos uma forma alternativa de autenticação. O mecanismo de autenticação correto varia para cada serviço. Recomendamos que consulte a documentação específica para cada serviço para orientação oficial. Por conveniência, cada conjunto de documentação é fornecido aqui:

| Serviço | Orientação |
| ------- | -------- |
| Service Bus do Azure | [Migrar para assinaturas de acesso partilhado](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Estafeta de ônibus de serviço Azure | [Migrar para assinaturas de acesso partilhado](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Cache gerido em Azure | [Migrar para Azure Cache para Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Mercado de Dados Azure | [Migrar para as APIs dos Serviços Cognitivos](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Serviços BizTalk | [Migrar para a funcionalidade de Aplicações Lógicas do Serviço de Aplicações Azure](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Serviços Azure Media | [Emigrar para a autenticação da AD Azure](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Atualizar o agente de backup Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>Clientes SharePoint

Os clientes SharePoint 2013, 2016 e SharePoint Online há muito que utilizam acs para fins de autenticação na nuvem, nas instalações e cenários híbridos. Algumas funcionalidades do SharePoint e casos de utilização serão afetadas pela reforma da ACS, enquanto outras não. O quadro abaixo resume a orientação de migração para algumas das funcionalidades sharePoint mais populares que alavancam o ACS:

| Funcionalidade | Orientação |
| ------- | -------- |
| Autenticação de utilizadores da Azure AD | Anteriormente, a Azure AD não suportava fichas SAML 1.1 exigidas pelo SharePoint para autenticação, e a ACS foi utilizada como um intermediário que tornava o SharePoint compatível com os formatos de token Azure AD. Agora, pode [ligar o SharePoint diretamente ao Azure AD utilizando o Azure AD App Gallery SharePoint na aplicação de instalações](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial). |
| [Autenticação de aplicativos e autenticação servidor-a-servidor no SharePoint nas instalações](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Não afetado pela reforma da ACS; nenhuma mudança necessária. | 
| [Baixa autorização de confiança para add-ins SharePoint (fornecedor hospedado e SharePoint hospedado)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Não afetado pela reforma da ACS; nenhuma mudança necessária. |
| [Pesquisa híbrida em nuvem sharePoint](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Não afetado pela reforma da ACS; nenhuma mudança necessária. |

### <a name="web-applications-that-use-passive-authentication"></a>Aplicações web que usam autenticação passiva

Para aplicações web que utilizam o Controlo de Acesso para autenticação do utilizador, o Controlde acesso fornece as seguintes funcionalidades e capacidades para desenvolvedores e arquitetos de aplicações web:

- Integração profunda com a Windows Identity Foundation (WIF).
- Federação com contas Google, Facebook, Yahoo, Azure Ative Directory e AD FS, e contas da Microsoft.
- Apoio aos seguintes protocolos de autenticação: OAuth 2.0 Draft 13, WS-Trust e Web Services Federation (WS-Federation).
- Suporte para os seguintes formatos simbólicos: JSON Web Token (JWT), SAML 1.1, SAML 2.0 e Simple Web Token (SWT).
- Uma experiência de descoberta do reino doméstico, integrada no WIF, que permite aos utilizadores escolhero tipo de conta que usam para iniciar sessão. Esta experiência é hospedada pela aplicação web e é totalmente personalizável.
- Transformação simbólica que permite uma rica personalização das reclamações recebidas pela aplicação web do Access Control, incluindo:
    - Passar por reclamações de fornecedores de identidade.
    - Adicionando reclamações personalizadas adicionais.
    - Lógica simples se-então para emitir reivindicações em determinadas condições.

Infelizmente, não há um serviço que ofereça todas estas capacidades equivalentes. Deve avaliar quais as capacidades do Controlo de Acesso que necessita e, em seguida, escolher entre utilizar o [Diretório Ativo Azure,](https://azure.microsoft.com/develop/identity/signin/)o [Diretório Ativo Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C), ou outro serviço de autenticação em nuvem.

#### <a name="migrate-to-azure-active-directory"></a>Migrar para o Diretório Ativo Azure

Um caminho a considerar é integrar as suas apps e serviços diretamente com a Azure AD. A Azure AD é o fornecedor de identidade baseado na nuvem para o trabalho da Microsoft ou contas escolares. A Azure AD é o fornecedor de identidade do Office 365, Azure, e muito mais. Fornece capacidades de autenticação federadas semelhantes ao Controlo de Acesso, mas não suporta todas as funcionalidades de Controlo de Acesso. 

O exemplo principal é a federação com fornecedores de identidade social, como facebook, Google e Yahoo. Se os seus utilizadores iniciarem sessão com este tipo de credenciais, o Azure AD não é a solução para si. 

A Azure AD também não suporta necessariamente os mesmos protocolos de autenticação que o Access Control. Por exemplo, embora tanto o Access Control como o Azure AD suportem o OAuth, existem diferenças subtis entre cada implementação. Diferentes implementações exigem que modifique o código como parte de uma migração.

No entanto, a Azure AD fornece várias vantagens potenciais para os clientes do Access Control. Apoia de forma nativa o trabalho da Microsoft ou as contas escolares alojadas na nuvem, que são comumente utilizadas pelos clientes do Access Control. 

Um inquilino azure aD também pode ser federado para um ou mais casos de Diretório Ativo no local via AD FS. Desta forma, a sua aplicação pode autenticar utilizadores e utilizadores baseados na nuvem que estão hospedados no local. Também apoia o protocolo WS-Federation, o que torna relativamente simples integrar-se com uma aplicação web utilizando o WIF.

A tabela seguinte compara as funcionalidades do Controlo de Acesso que são relevantes para aplicações web com as funcionalidades que estão disponíveis em Azure AD. 

A um nível elevado, *o Azure Ative Directory é provavelmente a melhor escolha para a sua migração se permitir que os utilizadores inscrevam apenas com o seu trabalho da Microsoft ou contas escolares*.

| Capacidade | Suporte ao Controlo de Acesso | Suporte da AD Azure |
| ---------- | ----------- | ---------------- |
| **Tipos de contas** | | |
| Trabalho da Microsoft ou contas escolares | Suportado | Suportado |
| Contas do Diretório Ativo do Servidor do Windows e DoD FS AD |- Apoiado via federação com um inquilino da AD Azure <br />- Apoiado via federação direta com AD FS | Apenas apoiado através da federação com um inquilino da AD Azure | 
| Contas de outros sistemas de gestão de identidade da empresa |- Possível via federação com um inquilino da AD Azure <br />- Apoiado via federação direta | Possível via federação com um inquilino da AD Azure |
| Contas da Microsoft para uso pessoal | Suportado | Suportado através do protocolo Azure AD v2.0 OAuth, mas não sobre quaisquer outros protocolos | 
| Contas facebook, Google, Yahoo | Suportado | Não apoiado |
| **Protocolos e compatibilidade sdk** | | |
| WIF | Suportado | Apoiadas, mas instruções limitadas estão disponíveis |
| WS-Federation | Suportado | Suportado |
| OAuth 2.0 | Apoio ao Projeto 13 | Suporte para RFC 6749, a especificação mais moderna |
| WS-Trust | Suportado | Não suportado |
| **Formatos simbólicos** | | |
| JWT | Suportado em Beta | Suportado |
| SAML 1.1 | Suportado | Pré-visualização |
| SAML 2.0 | Suportado | Suportado |
| SWT | Suportado | Não suportado |
| **Personalizações** | | |
| Descoberta de reino doméstico personalizável/UI de recolha de conta | Código transferível que pode ser incorporado em apps | Não suportado |
| Faça upload de certificados de assinatura de token personalizados | Suportado | Suportado |
| Personalizar reclamações em fichas |- Passar por pedidos de entrada de fornecedores de identidade<br />- Obtenha acesso ao fornecedor de identidade como reivindicação<br />- Emitir reclamações de saída com base nos valores dos créditos de entrada<br />- Emitir reclamações de produção com valores constantes |- Não pode passar por reclamações de fornecedores de identidade federados<br />- Não é possível obter acesso do fornecedor de identidade como reivindicação<br />- Não pode emitir reclamações de saída com base em valores de créditos de entrada<br />- Pode emitir reclamações de produção com valores constantes<br />- Pode emitir reclamações de saída com base em propriedades dos utilizadores sincronizados com a Azure AD |
| **Automatização** | | |
| Automatizar tarefas de configuração e gestão | Suportado através do Serviço de Gestão de Controlo de Acesso | Suportado usando a Microsoft Graph API |

Se decidir que o Azure AD é o melhor caminho de migração para as suas aplicações e serviços, deve estar ciente de duas formas de integrar a sua app com o Azure AD.

Para utilizar a WS-Federation ou a WIF para integrar com a Azure AD, recomendamos seguir a abordagem descrita na [Configuração federada de um único signo para uma aplicação não-galeria](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). O artigo refere-se à configuração da AD Azure para um único sign-on baseado em SAML, mas também trabalha para configurar a WS-Federation. Seguir esta abordagem requer uma licença Azure AD Premium. Esta abordagem tem duas vantagens:

- Obtém-se toda a flexibilidade da personalização de token azure. Pode personalizar as reclamações emitidas pela Azure AD para corresponder às reclamações emitidas pelo Access Control. Isto inclui especialmente a alegação de identificação do utilizador ou identificação de nome. Para continuar a receber iDentifiers de utilizador consistentes para os seus utilizadores depois de alterar as tecnologias, certifique-se de que as iDs de utilizador emitidas pela Azure AD correspondem às emitidas pelo Access Control.
- Pode configurar um certificado de assinatura de token específico da sua aplicação e com uma vida útil que controla.

> [!NOTE]
> Esta abordagem requer uma licença Azure AD Premium. Se for cliente do Access Control e necessitar de uma licença premium para a configuração de um único sinal para uma aplicação, contacte-nos. Teremos todo o gosto em fornecer licenças de desenvolvimento para que possa usar.

Uma abordagem alternativa é seguir esta amostra de [código,](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)que dá instruções ligeiramente diferentes para a criação da WS-Federation. Esta amostra de código não utiliza WIF, mas sim o ASP.NET 4.5 OWIN middleware. No entanto, as instruções para o registo de aplicações são válidas para aplicações que utilizam o WIF, e não requerem uma licença Azure AD Premium. 

Se escolher esta abordagem, tem de compreender a assinatura de um [capotamento de chave em Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Esta abordagem utiliza a chave de assinatura global da AD Azure para emitir fichas. Por predefinição, o WIF não atualiza automaticamente as teclas de sinalização. Quando a Azure AD gira as suas teclas de assinatura globais, a implementação do WIF tem de estar preparada para aceitar as alterações. Para mais informações, consulte [informações importantes sobre a assinatura](https://msdn.microsoft.com/library/azure/dn641920.aspx)de um capotamento de chave em Azure AD .

Se conseguir integrar-se com o Azure AD através dos protocolos OpenID Connect ou OAuth, recomendamos que o faça. Dispomos de documentação e orientação extensiva sobre como integrar o Azure AD na sua aplicação web disponível no nosso guia de [desenvolvimento da AD Azure.](https://aka.ms/aaddev)

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrar para o Diretório Ativo Azure B2C

O outro caminho de migração a considerar é Azure AD B2C. O Azure AD B2C é um serviço de autenticação em nuvem que, tal como o Access Control, permite aos programadores subcontratarem a sua lógica de autenticação e gestão de identidade para um serviço na nuvem. É um serviço pago (com níveis gratuitos e premium) que é projetado para aplicações viradas para o consumidor que podem ter até milhões de utilizadores ativos.

Tal como o Access Control, uma das características mais atrativas do Azure AD B2C é que suporta vários tipos diferentes de contas. Com o Azure AD B2C, pode inscrever os utilizadores utilizando a sua conta Microsoft, ou Facebook, Google, LinkedIn, GitHub ou Yahoo, e muito mais. O Azure AD B2C também suporta "contas locais", ou nome de utilizador e palavras-passe que os utilizadores criam especificamente para a sua aplicação. O Azure AD B2C também fornece uma rica extensibility que pode usar para personalizar os seus fluxos de entrada. 

No entanto, o Azure AD B2C não suporta a amplitude dos protocolos de autenticação e dos formatos simbólicos que os clientes do Access Control podem necessitar. Também não é possível utilizar o Azure AD B2C para obter fichas e consultas para obter informações adicionais sobre o utilizador do fornecedor de identidade, Microsoft ou de outra forma.

O quadro seguinte compara as funcionalidades do Controlo de Acesso que são relevantes para aplicações web com as que estão disponíveis no Azure AD B2C. A um nível elevado, *o Azure AD B2C é provavelmente a escolha certa para a sua migração se a sua aplicação estiver virada para o consumidor, ou se apoiar muitos tipos diferentes de contas.*

| Capacidade | Suporte ao Controlo de Acesso | Suporte Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Tipos de contas** | | |
| Trabalho da Microsoft ou contas escolares | Suportado | Suportado através de políticas personalizadas  |
| Contas do Diretório Ativo do Servidor do Windows e DoD FS AD | Apoiado via federação direta com AD FS | Apoiado via federação SAML usando políticas personalizadas |
| Contas de outros sistemas de gestão de identidade da empresa | Apoiado através da federação direta através da WS-Federação | Apoiado via federação SAML usando políticas personalizadas |
| Contas da Microsoft para uso pessoal | Suportado | Suportado | 
| Contas facebook, Google, Yahoo | Suportado | Facebook e Google suportados de forma nativa, Yahoo suportado através da federação OpenID Connect usando políticas personalizadas |
| **Protocolos e compatibilidade sdk** | | |
| Windows Identity Foundation (WIF) | Suportado | Não suportado |
| WS-Federation | Suportado | Não suportado |
| OAuth 2.0 | Apoio ao Projeto 13 | Suporte para RFC 6749, a especificação mais moderna |
| WS-Trust | Suportado | Não suportado |
| **Formatos simbólicos** | | |
| JWT | Suportado em Beta | Suportado |
| SAML 1.1 | Suportado | Não suportado |
| SAML 2.0 | Suportado | Não suportado |
| SWT | Suportado | Não suportado |
| **Personalizações** | | |
| Descoberta de reino doméstico personalizável/UI de recolha de conta | Código transferível que pode ser incorporado em apps | UI totalmente personalizável através de CSS personalizado |
| Faça upload de certificados de assinatura de token personalizados | Suportado | Chaves de assinatura personalizadas, não certificados, suportadas através de políticas personalizadas |
| Personalizar reclamações em fichas |- Passar por pedidos de entrada de fornecedores de identidade<br />- Obtenha acesso ao fornecedor de identidade como reivindicação<br />- Emitir reclamações de saída com base nos valores dos créditos de entrada<br />- Emitir reclamações de produção com valores constantes |- Pode passar por reclamações de fornecedores de identidade; políticas personalizadas necessárias para algumas reivindicações<br />- Não é possível obter acesso do fornecedor de identidade como reivindicação<br />- Pode emitir reclamações de saída com base nos valores dos pedidos de entrada através de políticas personalizadas<br />- Pode emitir reclamações de produção com valores constantes através de políticas personalizadas |
| **Automatização** | | |
| Automatizar tarefas de configuração e gestão | Suportado através do Serviço de Gestão de Controlo de Acesso |- Criação de utilizadores permitidos através da API do Microsoft Graph<br />- Não pode criar inquilinos, candidaturas ou políticas B2C programáticamente |

Se decidir que o Azure AD B2C é o melhor caminho de migração para as suas aplicações e serviços, comece com os seguintes recursos:

- [Documentação Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Políticas personalizadas Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Preços B2C Azure AD](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrar para a Identidade Ping ou Auth0

Em alguns casos, pode descobrir que o Azure AD e o Azure AD B2C não são suficientes para substituir o Controlo de Acesso nas suas aplicações web sem fazer grandes alterações de código. Alguns exemplos comuns podem incluir:

- Aplicações web que usam WIF ou WS-Federation para iniciar sessão com fornecedores de identidade social, como google ou Facebook.
- Aplicações web que realizam a federação direta a um fornecedor de identidade empresarial sobre o protocolo WS-Federação.
- Aplicações web que requerem o token de acesso emitido por um fornecedor de identidade social (como google ou Facebook) como uma reivindicação nas fichas emitidas pelo Access Control.
- Aplicações web com regras complexas de transformação de token que Azure AD ou Azure AD B2C não podem reproduzir.
- Aplicações web multi-inquilinos que usam ACS para gerir centralmente a federação a muitos fornecedores de identidade diferentes

Nestes casos, é melhor considerar a migração da sua aplicação web para outro serviço de autenticação em nuvem. Recomendamos explorar as seguintes opções. Cada uma das seguintes opções oferece capacidades semelhantes ao Controlo de Acesso:

|     |     |
| --- | --- |
| ![Esta imagem mostra o logótipo Auth0](./media/active-directory-acs-migration/rsz-auth0.png) | [O Auth0](https://auth0.com/acs) é um serviço de identidade na nuvem flexível que criou [orientações de migração de alto nível para os clientes do Access Control](https://auth0.com/acs), e suporta quase todas as funcionalidades que a ACS faz. |
| ![Esta imagem mostra o logótipo da Identidade Ping](./media/active-directory-acs-migration/rsz-ping.png) | [A Ping Identity](https://www.pingidentity.com) oferece duas soluções semelhantes à ACS. PingOne é um serviço de identidade na nuvem que suporta muitas das mesmas funcionalidades que acs, e PingFederate é um produto de identidade similar no local que oferece mais flexibilidade. Consulte a orientação de [aposentadoria ACS da Ping](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) para obter mais detalhes sobre a utilização destes produtos. |

O nosso objetivo em trabalhar com a Ping Identity e a Auth0 é garantir que todos os clientes do Access Control tenham uma rota de migração para as suas apps e serviços que minimizem a quantidade de trabalho necessário para se deslocarem do Controlo de Acesso.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Serviços web que utilizam autenticação ativa

Para serviços web que são protegidos com fichas emitidas pelo Access Control, o Access Control oferece as seguintes funcionalidades e capacidades:

- Capacidade de registar uma ou mais *identidades* de serviço no seu espaço de nome sinuoso de Controlo de Acesso. As identidades de serviço podem ser usadas para solicitar fichas.
- Suporte para os protocolos OAuth WRAP e OAuth 2.0 Draft 13 para solicitar fichas, utilizando os seguintes tipos de credenciais:
    - Uma simples senha que é criada para a identidade de serviço
    - Um SWT assinado utilizando uma chave simétrica ou certificado X509
    - Um símbolo SAML emitido por um fornecedor de identidade fidedigno (tipicamente, um caso AD FS)
- Suporte para os seguintes formatos simbólicos: JWT, SAML 1.1, SAML 2.0 e SWT.
- Regras simples de transformação de fichas.

As identidades de serviço no Controlo de Acesso são normalmente utilizadas para implementar a autenticação servidor-a-servidor. 

#### <a name="migrate-to-azure-active-directory"></a>Migrar para o Diretório Ativo Azure

A nossa recomendação para este tipo de fluxo de autenticação é migrar para o [Diretório Ativo Azure.](https://azure.microsoft.com/develop/identity/signin/) A Azure AD é o fornecedor de identidade baseado na nuvem para o trabalho da Microsoft ou contas escolares. A Azure AD é o fornecedor de identidade do Office 365, Azure, e muito mais. 

Também pode utilizar o Azure AD para autenticação servidor-a-servidor utilizando a implementação da AD Azure da concessão de credenciais de cliente OAuth. A tabela seguinte compara as capacidades do Controlo de Acesso na autenticação servidor-servidor com as que estão disponíveis em Azure AD.

| Capacidade | Suporte ao Controlo de Acesso | Suporte da AD Azure |
| ---------- | ----------- | ---------------- |
| Como registar um serviço web | Criar uma parte de base no portal de gestão do Controlo de Acesso | Criar uma aplicação web Azure AD no portal Azure |
| Como registar um cliente | Criar uma identidade de serviço no portal de gestão de Controlo de Acesso | Criar mais uma aplicação web Azure AD no portal Azure |
| Protocolo utilizado |- Protocolo OAuth WRAP<br />- OAuth 2.0 Projeto 13 concessão de credenciais de cliente | Concessão de credenciais de cliente OAuth 2.0 |
| Métodos de autenticação do cliente |- Senha simples<br />- SWT assinado<br />- Ficha SAML de um fornecedor de identidade federado |- Senha simples<br />- JWT assinado |
| Formatos simbólicos |- JWT<br />- SAML 1.1<br />- SAML 2.0<br />- SWT<br /> | Apenas JWT |
| Transformação de token |- Adicionar reclamações personalizadas<br />- Lógica simples de emissão se-então reivindicar | Adicionar reclamações personalizadas | 
| Automatizar tarefas de configuração e gestão | Suportado através do Serviço de Gestão de Controlo de Acesso | Suportado usando a Microsoft Graph API |

Para obter orientações sobre a implementação de cenários servidor-servidor, consulte os seguintes recursos:

- Secção de serviço ao serviço do guia de desenvolvimento da [AD Azure](https://aka.ms/aaddev)
- [Amostra de código Daemon usando credenciais simples de cliente de senha](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Amostra de código Daemon usando credenciais de cliente de certificado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrar para a Identidade Ping ou Auth0

Em alguns casos, pode descobrir que as credenciais de cliente da AD Azure e a implementação da bolsa OAuth não são suficientes para substituir o Controlo de Acesso na sua arquitetura sem grandes alterações de código. Alguns exemplos comuns podem incluir:

- Autenticação servidor-a-servidor utilizando formatos simbólicos que não jWTs.
- Autenticação servidor-a-servidor utilizando um token de entrada fornecido por um fornecedor de identidade externo.
- Autenticação servidor-a-servidor com regras de transformação simbólica que o Azure AD não pode reproduzir.

Nestes casos, pode considerar migrar a sua aplicação web para outro serviço de autenticação em nuvem. Recomendamos explorar as seguintes opções. Cada uma das seguintes opções oferece capacidades semelhantes ao Controlo de Acesso:

|     |     |
| --- | --- |
| ![Esta imagem mostra o logótipo Auth0](./media/active-directory-acs-migration/rsz-auth0.png) | [O Auth0](https://auth0.com/acs) é um serviço de identidade na nuvem flexível que criou [orientações de migração de alto nível para os clientes do Access Control](https://auth0.com/acs), e suporta quase todas as funcionalidades que a ACS faz. |
| ![Esta imagem mostra o logótipo da Identidade Ping](./media/active-directory-acs-migration/rsz-ping.png) | [A Ping Identity](https://www.pingidentity.com) oferece duas soluções semelhantes à ACS. PingOne é um serviço de identidade na nuvem que suporta muitas das mesmas funcionalidades que acs, e PingFederate é um produto de identidade similar no local que oferece mais flexibilidade. Consulte a orientação de [aposentadoria ACS da Ping](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) para obter mais detalhes sobre a utilização destes produtos. |

O nosso objetivo em trabalhar com a Ping Identity e a Auth0 é garantir que todos os clientes do Access Control tenham uma rota de migração para as suas apps e serviços que minimizem a quantidade de trabalho necessário para se deslocarem do Controlo de Acesso.

#### <a name="passthrough-authentication"></a>Autenticação de passagem

Para a autenticação passthrough com transformação arbitrária de token, não existe tecnologia equivalente da Microsoft ao ACS. Se é disso que os seus clientes precisam, o Auth0 pode ser aquele que fornece a solução de aproximação mais próxima.

## <a name="questions-concerns-and-feedback"></a>Perguntas, preocupações e feedback

Entendemos que muitos clientes do Access Control não encontrarão um caminho de migração claro depois de lerem este artigo. Pode precisar de ajuda ou orientação para determinar o plano certo. Se quiser discutir os seus cenários e perguntas de migração, por favor deixe um comentário nesta página.
