---
title: 'Gestão de aplicações: Boas práticas e recomendações | Microsoft Docs'
description: Saiba as melhores práticas e recomendações para a gestão de aplicações no Azure Ative Directory. Saiba como utilizar aplicações automáticas de fornecimento e publicação no local com Aplicação Proxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23c688d9b2e118ef29303d435bb83ef02ad36105
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259139"
---
# <a name="application-management-best-practices"></a>Gestão de aplicações boas práticas

Este artigo contém recomendações e boas práticas para gerir aplicações no Azure Ative Directory (Azure AD), utilizando o fornecimento automático e publicando aplicações no local com Aplicação Proxy.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Aplicativo cloud e recomendações únicas de inscrição
| Recomendação | Comentários |
| --- | --- |
| Consulte a galeria de aplicações AZure AD para obter aplicações  | A Azure AD tem uma galeria que contém milhares de aplicações pré-integradas que são ativadas com um único sign-on da Enterprise (SSO). Para obter orientação de configuração específica de aplicações, consulte a [Lista de tutoriais de aplicações SaaS](../saas-apps/tutorial-list.md).  | 
| Utilizar SSO federado baseado em SAML  | Quando uma aplicação o suporta, utilize SSO federado, baseado em SAML com Azure AD em vez de SSO e ADFS baseados em palavra-passe.  | 
| Utilize SHA-256 para a assinatura do certificado  | A Azure AD usa o algoritmo SHA-256 por padrão para assinar a resposta SAML. Utilize SHA-256 a menos que a aplicação exija SHA-1 (ver [opções](certificate-signing-options.md) de assinatura de certificado e [problema de inscrição de pedidos](application-sign-in-problem-application-error.md).)  | 
| Exigir atribuição do utilizador  | Por predefinição, os utilizadores podem aceder às suas aplicações empresariais sem serem atribuídos às mesmos. No entanto, se a aplicação expor as funções, ou se pretender que a aplicação apareça nas Minhas Apps de um utilizador, requer a atribuição do utilizador.  | 
| Implementar as minhas apps para os seus utilizadores | [My Apps](end-user-experiences.md) at é um portal baseado na `https://myapps.microsoft.com` web que fornece aos utilizadores um único ponto de entrada para as suas aplicações baseadas na nuvem atribuídas. À medida que são adicionadas capacidades adicionais como a gestão do grupo e o reset da palavra-passe de autosserviço, os utilizadores podem encontrá-los nas Minhas Apps. Ver [Plan My Apps implementação](my-apps-deployment-plan.md).
| Utilizar atribuição de grupo  | Se incluído na sua subscrição, atribua grupos a uma aplicação para que possa delegar a gestão de acesso contínua ao proprietário do grupo.  | 
| Estabelecer um processo de gestão de certificados | O prazo máximo de vida de um certificado de assinatura é de três anos. Para prevenir ou minimizar a interrupção devido à expiração de um certificado, utilize funções e listas de distribuição de e-mails para garantir que as notificações de alteração relacionadas com certificados são monitorizadas de perto. |

## <a name="provisioning-recommendations"></a>Recomendações de provisionamento
| Recomendação | Comentários |
| --- | --- |
| Use tutoriais para configurar o provisionamento com aplicações na nuvem | Consulte a [Lista de tutoriais de aplicações saaS](../saas-apps/tutorial-list.md) para obter orientação passo a passo sobre a configuração do provisionamento para a aplicação de galeria que pretende adicionar. |
| Utilize registos de provisionamento (pré-visualização) para monitorizar o estado | Os [registos de fornecimento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) dão detalhes sobre todas as ações realizadas pelo serviço de fornecimento, incluindo o estatuto para cada utilizadores. |
| Atribuir um grupo de distribuição ao e-mail de notificação de provisionamento | Para aumentar a visibilidade dos alertas críticos enviados pelo serviço de fornecimento, atribua um grupo de distribuição à definição de E-mails de Notificação. |


## <a name="application-proxy-recommendations"></a>Recomendações de procuração de aplicativos
| Recomendação | Comentários |
| --- | --- |
| Utilizar Proxy de aplicação para acesso remoto a recursos internos | O Proxy da aplicação é recomendado para dar aos utilizadores remotos acesso a recursos internos, substituindo a necessidade de um VPN ou procuração inversa. Não se destina a aceder a recursos a partir da rede corporativa, pois poderia acrescentar latência.
| Use domínios personalizados | Configure os domínios personalizados para as suas aplicações (ver [domínios personalizados Configure)](application-proxy-configure-custom-domain.md)para que os URLs para utilizadores e entre aplicações funcionem dentro ou fora da sua rede. Também poderá controlar a sua marca e personalizar os seus URLs.  Ao utilizar nomes de domínio personalizados, planeie adquirir um certificado público de uma autoridade de certificados fidedigna não-Microsoft. A azure Application Proxy suporta certificados standard ([wildcard)](application-proxy-wildcard.md)ou san-based. (Ver [planeamento de procuração de aplicação](application-proxy-deployment-plan.md).) |
| Sincronizar os utilizadores antes de implementar o Proxy da Aplicação | Antes de implementar o proxy da aplicação, sincronize as identidades dos utilizadores a partir de um diretório no local ou crie-as diretamente no Azure AD. A sincronização de identidade permite que a Azure AD pré-autense os utilizadores antes de lhes conceder acesso às aplicações publicadas pela App Proxy. Também fornece as informações necessárias para o identificador do utilizador para realizar um único sinal de sso. (Ver [planeamento de procuração de aplicação](application-proxy-deployment-plan.md).) |
| Siga as nossas dicas para uma elevada disponibilidade e equilíbrio de carga | Para saber como o tráfego flui entre os utilizadores, conectores Proxy de aplicações e servidores de aplicações back-end, e para obter dicas para otimizar o desempenho e o equilíbrio de carga, consulte [alta disponibilidade e equilíbrio de carga dos conectores e aplicações do seu App Proxy.](application-proxy-high-availability-load-balancing.md) |
| Use vários conectores | Utilize dois ou mais conectores Proxy de aplicação para uma maior resiliência, disponibilidade e escala (ver [conectores Proxy de aplicação).](application-proxy-connectors.md) Crie grupos de conector e certifique-se de que cada grupo de conector tem pelo menos dois conectores (três conectores são ótimos). |
| Localize os servidores de conector perto dos servidores de aplicações e certifique-se de que estão no mesmo domínio | Para otimizar o desempenho, localize fisicamente o servidor de conector perto dos servidores de aplicações (ver [considerações de topologia da rede).](application-proxy-network-topology.md) Além disso, os servidores do servidor de conector e aplicações web devem pertencer ao mesmo domínio ative directory, ou devem abranger domínios de confiança. Esta configuração é necessária para SSO com autenticação integrada do Windows (IWA) e delegação restrita kerberos (KCD). Se os servidores estiverem em diferentes domínios, terá de utilizar a delegação baseada em recursos para SSO (ver [KCD para um único sinal de sação com procuração de aplicação).](application-proxy-configure-single-sign-on-with-kcd.md) |
| Ativar atualizações automáticas para conectores | Ativar as atualizações automáticas dos seus conectores para as funcionalidades mais recentes e correções de erros. A Microsoft fornece suporte direto para a versão mais recente do conector e uma versão anterior. (Ver histórico da [versão de lançamento do Application Proxy](application-proxy-release-version-history.md).) |
| Contorne o seu representante no local | Para uma manutenção mais fácil, configura o conector para contornar o seu representante no local para que se conecte diretamente aos serviços Azure. (Ver [conectores proxy de aplicação e servidores proxy](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Use o Proxy de aplicação AD Azure sobre o Proxy de aplicações web | Utilize o Proxy de aplicação AD Azure para a maioria dos cenários no local. O Proxy da Aplicação Web é apenas preferido em cenários que requerem um servidor proxy para AD FS e onde não é possível utilizar domínios personalizados no Azure Ative Directory. (Ver [aplicação proxy migração](application-proxy-migration.md).) |