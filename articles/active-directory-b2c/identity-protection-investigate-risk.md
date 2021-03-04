---
title: Investigar o risco com o Azure Ative Directory B2C Identity Protection
description: Saiba como investigar utilizadores arriscados e deteções na Azure AD B2C Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8919285f31e04a51ce10afe3313b28cf86b64ee0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055720"
---
# <a name="investigate-risk-with-identity-protection-in-azure-ad-b2c"></a>Investigar risco com proteção de identidade em Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

A Proteção de Identidade fornece uma deteção contínua de riscos para o seu inquilino Azure AD B2C. Permite que as organizações descubram, investiguem e remediam riscos baseados na identidade. A Proteção de Identidade vem com relatórios de risco que podem ser usados para investigar riscos de identidade em inquilinos Azure AD B2C. Neste artigo, aprende-se a investigar e a mitigar riscos.

## <a name="overview"></a>Descrição Geral

A Azure AD B2C Identity Protection fornece dois relatórios. O relatório *dos utilizadores arriscados* é onde os administradores podem descobrir quais os utilizadores que estão em risco e detalhes sobre deteções. O relatório *de deteção de riscos* fornece informações sobre cada deteção de risco, incluindo o tipo, outros riscos desencadeados ao mesmo tempo, localização de tentativa de inscrição, e muito mais.

Cada relatório lança uma lista de todas as deteções para o período apresentado no topo do relatório. Os relatórios podem ser filtrados utilizando os filtros em toda a parte superior do relatório. Os administradores podem optar por descarregar os dados ou utilizar [a MS Graph API e a Microsoft Graph PowerShell SDK](../active-directory/identity-protection/howto-identity-protection-graph-api.md) para exportar continuamente os dados.

## <a name="service-limitations-and-considerations"></a>Limitações e considerações de serviço

Ao utilizar a Proteção de Identidade, considere o seguinte:

- A Proteção de Identidade está em padrão.
- A Proteção de Identidade está disponível para identidades locais e sociais, como google ou Facebook. Para identidades sociais, o Acesso Condicional deve ser ativado. A deteção é limitada porque as credenciais de conta social são geridas pelo fornecedor de identidade externa.
- Nos inquilinos Azure AD B2C, apenas está disponível um subconjunto das deteções de [risco de proteção de identidade Azure AD.](../active-directory/identity-protection/overview-identity-protection.md) As seguintes deteções de risco são suportadas pela Azure AD B2C:  

|Tipo de deteção de risco  |Descrição  |
|---------|---------|
| Viagem atípica     | Iniciar sômposições a partir de um local atípico baseado nas recentes insusagens do utilizador.        |
|Endereço IP anónimo     | Iniciar sôs-in a partir de um endereço IP anónimo (por exemplo: navegador Tor, VPNs anonimizadores).        |
|Endereço IP ligado a malware     | Iniciar sção a partir de um endereço IP ligado a malware.         |
|Propriedades de inícios de sessão desconhecidos     | Iniciar s presenciação com propriedades que não vimos recentemente para o utilizador dado.        |
|Admin confirmou utilizador comprometido    | Um administrador indicou que um utilizador foi comprometido.             |
|Spray de senha     | Iniciar s-in através de um ataque de spray de senha.      |
|Inteligência de ameaça Azure AD     | Fontes internas e externas da Microsoft identificaram um padrão de ataque conhecido.        |

## <a name="pricing-tier"></a>Escalão de preço

Azure AD B2C Premium P2 é necessário para algumas funcionalidades de Proteção de Identidade. Se necessário, [altere o seu nível de preços Azure AD B2C para Premium P2](./billing.md). A tabela a seguir resume as características de Proteção de Identidade e o nível de preços exigido.  

|Funcionalidade   |P1   |P2|
|----------|:-----------:|:------------:|
|Relatório dos utilizadores de risco     |&#x2713; |&#x2713; |
|Utilizadores arriscados relatam detalhes  | |&#x2713; |
|Utilizadores arriscados relatam remediação    | &#x2713; |&#x2713; |
|Relatório de deteção de riscos   |&#x2713;|&#x2713;|
|Detalhes do relatório de deteções de riscos  ||&#x2713;|
|Reportar download |  &#x2713;| &#x2713;|
|Acesso a API de Gráfico MS |  &#x2713;| &#x2713;|

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="investigate-risky-users"></a>Investigate risky users (Investigar utilizadores de risco)

Com as informações fornecidas pelo relatório de utilizadores arriscados, os administradores podem encontrar:

- Que utilizadores estão em risco, tiveram riscos remediados ou tiveram o risco de ser dispensados?
- Detalhes sobre deteções
- História de todos os insus máximos de risco
- História do risco
 
Os administradores podem então optar por tomar medidas sobre estes eventos. Os administradores podem escolher:

- Redefinir a senha do utilizador
- Confirmar o compromisso do utilizador
- Descartar o risco do utilizador
- Bloquear o utilizador de iniciar sessão
- Investigue mais além usando Azure ATP

### <a name="navigating-the-risky-users-report"></a>Navegar no relatório de utilizadores arriscados

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

1. Sob **os serviços Azure**, selecione **Azure AD B2C**. Ou utilize a caixa de pesquisa para encontrar e selecionar **Azure AD B2C**.

1. Em **Segurança**, selecione **utilizadores arriscados (Pré-visualização)**.

   ![Utilizadores de risco](media/identity-protection-investigate-risk/risky-users.png)

    A seleção de entradas individuais expande uma janela de detalhes abaixo das deteções. A visão de detalhes permite que os administradores investiguem e realizem ações em cada deteção.

    ![Ações de utilizadores arriscados](media/identity-protection-investigate-risk/risky-users-report-actions.png)


## <a name="risk-detections-report"></a>Relatório de deteção de riscos

O relatório de deteção de riscos contém dados filtrantes até aos últimos 90 dias (três meses).

Com as informações fornecidas pelo relatório de deteção de riscos, os administradores podem encontrar:

- Informação sobre cada deteção de risco, incluindo o tipo.
- Outros riscos desencadeados ao mesmo tempo.
- Local de tentativa de inscrição.

Os administradores podem então optar por voltar ao relatório de risco ou de inscrição do utilizador para tomar medidas com base nas informações recolhidas.

### <a name="navigating-the-risk-detections-report"></a>Navegar no relatório de deteções de risco

1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em **Segurança**, selecione **deteções de risco (pré-visualização)**.

   ![Deteções de riscos](media/identity-protection-investigate-risk/risk-detections.png)


## <a name="next-steps"></a>Passos seguintes

- [Adicionar Acesso Condicional a um fluxo de utilizador.](conditional-access-user-flow.md)