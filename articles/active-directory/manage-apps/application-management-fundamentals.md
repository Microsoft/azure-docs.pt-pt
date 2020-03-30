---
title: 'Gestão de aplicações: Boas práticas e recomendações / Microsoft Docs'
description: Conheça as melhores práticas e recomendações para a gestão de aplicações no Diretório Ativo Azure. Saiba mais sobre o uso de aplicações de provisionamento automático e publicação no local com procuração de aplicação.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6534efb6fcd07ee3b9f3979cabf2feb77496a8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085294"
---
# <a name="application-management-best-practices"></a>Boas práticas de gestão de aplicações
Este artigo contém recomendações e boas práticas para gerir aplicações no Azure Ative Directory (Azure AD), utilizando o provisionamento automático e publicando aplicações no local com Procuração de Aplicações.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>App cloud e recomendações de inscrição única
| Recomendação | Comentários |
| --- | --- |
| Consulte a galeria de aplicações da AD Azure para obter aplicações  | A Azure AD tem uma galeria que contém milhares de aplicações pré-integradas que estão habilitadas com um único sign-on da Enterprise (SSO). Para obter orientação específica para a configuração de aplicações, consulte os [tutoriais da aplicação SaaS](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).  | 
| Utilize SSO federado baseado em SAML  | Quando uma aplicação o suporta, utilize SSO federado, baseado em SAML com AD Azure em vez de SSO e ADFS baseados em palavras-passe.  | 
| Use SHA-256 para a assinatura de certificado  | A Azure AD utiliza o algoritmo SHA-256 por padrão para assinar a resposta SAML. Utilize sha-256 a menos que a aplicação exija SHA-1 (ver Opções de assinatura de certificado e problema de [inscrição](certificate-signing-options.md) de [aplicação](application-sign-in-problem-application-error.md).)  | 
| Exigir a atribuição do utilizador  | Por predefinição, os utilizadores podem aceder às suas aplicações empresariais sem serem atribuídos às mesmas. No entanto, se a aplicação expor funções, ou se pretender que a aplicação apareça no painel de acesso de um utilizador, requer a atribuição do utilizador. (Consulte [a orientação do Programador para integrar aplicações](developer-guidance-for-integrating-applications.md).)  | 
| Implemente o painel de acesso das Minhas Aplicações aos seus utilizadores | O [painel](end-user-experiences.md) `https://myapps.microsoft.com` de acesso é um portal baseado na Web que fornece aos utilizadores um único ponto de entrada para as suas aplicações baseadas na nuvem atribuídas. À medida que são adicionadas capacidades adicionais como gestão de grupo e reset de senha de autosserviço, os utilizadores podem encontrá-las no painel de acesso. Consulte [o Plano de implantação de um painel](access-panel-deployment-plan.md)de acesso .
| Utilizar a atribuição de grupo  | Se estiver incluído na sua subscrição, atribua grupos a uma aplicação para que possa delegar a gestão de acesso contínua ao proprietário do grupo. (Consulte [a orientação do Programador para integrar aplicações](developer-guidance-for-integrating-applications.md).)   | 
| Estabelecer um processo de gestão de certificados | O tempo máximo de vida de um certificado de assinatura é de três anos. Para prevenir ou minimizar a interrupção devido à caducidade de um certificado, utilize funções e listas de distribuição de e-mail para garantir que as notificações de alteração relacionadas com certificados sejam monitorizadas de perto. |

## <a name="provisioning-recommendations"></a>Recomendações de fornecimento
| Recomendação | Comentários |
| --- | --- |
| Use tutoriais para configurar o provisionamento com aplicações na nuvem | Consulte os [tutoriais da aplicação SaaS](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para obter orientação passo a passo sobre a configuração do fornecimento para a app da galeria que pretende adicionar. |
| Utilize registos de provisionamento (pré-visualização) para monitorizar o estado | Os [registos](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) de fornecimento dão detalhes sobre todas as ações realizadas pelo serviço de provisionamento, incluindo o estatuto dos utilizadores individuais. |
| Atribuir um grupo de distribuição ao e-mail de notificação de fornecimento | Para aumentar a visibilidade dos alertas críticos enviados pelo serviço de provisionamento, atribuir um grupo de distribuição à definição de Emails de Notificação. |


## <a name="application-proxy-recommendations"></a>Recomendações de Procuração de Aplicação
| Recomendação | Comentários |
| --- | --- |
| Utilizar procuração de aplicação para acesso remoto a recursos internos | Aplicação Proxy é recomendado para dar aos utilizadores remotos acesso a recursos internos, substituindo a necessidade de uma VPN ou procuração inversa. Não se destina a aceder a recursos a partir da rede corporativa, pois poderia acrescentar latência.
| Use domínios personalizados | Configure domínios personalizados para as suas aplicações (consulte [Configurar domínios personalizados)](application-proxy-configure-custom-domain.md)para que os URLs para os utilizadores e entre aplicações funcionem dentro ou fora da sua rede. Também poderá controlar a sua marca e personalizar os seus URLs.  Ao utilizar nomes de domínio personalizados, planeie adquirir um certificado público a partir de uma autoridade de certificados fidedigna da Microsoft. A Procuração de Aplicações Azure suporta certificados padrão,[(wildcard),](application-proxy-wildcard.md)ou san. (Ver [Planeamento de Procuração](application-proxy-deployment-plan.md)de Aplicações .) |
| Sincronizar os utilizadores antes de implementar o Proxy da Aplicação | Antes de implementar o proxy da aplicação, sincronizar as identidades dos utilizadores a partir de um diretório no local ou criá-las diretamente em Azure AD. A sincronização de identidade permite que a AD Azure autentique os utilizadores antes de lhes conceder acesso às aplicações publicadas pela App Proxy. Também fornece as informações necessárias para o identificador do utilizador para realizar uma única inscrição (SSO). (Ver [Planeamento de Procuração](application-proxy-deployment-plan.md)de Aplicações .) |
| Siga as nossas dicas para alta disponibilidade e equilíbrio de carga | Para saber como o tráfego flui entre os utilizadores, conectores proxy de aplicação e servidores de aplicações back-end, e para obter dicas para otimizar o desempenho e o equilíbrio de carga, consulte [Alta disponibilidade e equilíbrio de carga dos seus conectores e aplicações](application-proxy-high-availability-load-balancing.md)do Application Proxy. |
| Utilize vários conectores | Utilize dois ou mais conectores proxy de aplicação para uma maior resiliência, disponibilidade e escala (ver [conectores proxy de aplicação).](application-proxy-connectors.md) Crie grupos de conectores e certifique-se de que cada grupo de conectores tem pelo menos dois conectores (três conectores são ótimos). |
| Localize servidores de conector perto dos servidores de aplicações e certifique-se de que estão no mesmo domínio | Para otimizar o desempenho, localize fisicamente o servidor do conector perto dos servidores de aplicações (consulte considerações de topoologia da [rede).](application-proxy-network-topology.md) Além disso, os servidores do servidor de conector e aplicações web devem pertencer ao mesmo domínio de Diretório Ativo, ou devem abranger domínios de confiança. Esta configuração é necessária para SSO com autenticação integrada do Windows (IWA) e Delegação Limitada Kerberos (KCD). Se os servidores estiverem em diferentes domínios, terá de utilizar uma delegação baseada em recursos para o SSO (consulte [o KCD para um único sinal com procuração](application-proxy-configure-single-sign-on-with-kcd.md)de aplicação ). |
| Ativar atualizações automáticas para conectores | Ative atualizações automáticas para os seus conectores para as mais recentes funcionalidades e correções de erros. A Microsoft fornece suporte direto para a versão mais recente do conector e uma versão anterior. (Ver histórico da versão de [versão de lançamento do Application Proxy](application-proxy-release-version-history.md).) |
| Contorne o seu representante no local | Para uma manutenção mais fácil, configure o conector para contornar o seu proxy no local para que se ligue diretamente aos serviços Azure. (Ver [Conectores proxy de aplicação e servidores proxy](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Utilize proxy de aplicação ad azure sobre procuração de aplicação web | Utilize o Proxy de Aplicação AD Azure para a maioria dos cenários no local. O Proxy de Aplicação Web só é preferido em cenários que requerem um servidor proxy para AD FS e onde não pode utilizar domínios personalizados no Diretório Ativo do Azure. (Ver [migração de procuração de aplicação](application-proxy-migration.md).) |
