---
title: Planos de consumo e versões do Azure MFA-Azure Active Directory
description: Informações sobre o cliente de autenticação multifator e os diferentes métodos e versões disponíveis.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78e34e0433dd362150680046c2f0ca53996063c4
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151743"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Como obter a autenticação multifator do Azure

Quando se trata de proteger suas contas, a verificação em duas etapas deve ser padrão em toda a sua organização. Esse recurso é especialmente importante para contas que têm acesso privilegiado aos recursos. Por esse motivo, a Microsoft oferece recursos básicos de verificação em duas etapas para os administradores do Office 365 e do Azure Active Directory (Azure AD) sem custo adicional. Se você quiser atualizar os recursos para seus administradores ou estender a verificação em duas etapas para o restante de seus usuários, poderá comprar a autenticação multifator do Azure de várias maneiras.

> [!IMPORTANT]
> Este artigo destina-se a ser um guia para ajudá-lo a entender as diferentes maneiras de comprar a autenticação multifator do Azure. Para obter detalhes específicos sobre preços e cobrança, você deve sempre consultar a [página de preços da autenticação multifator](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versões disponíveis da autenticação multifator do Azure

A tabela a seguir descreve as diferenças entre as versões de autenticação multifator:

| Versão | Descrição |
| --- | --- |
| Opção gratuita | Os clientes que estão utilizando os benefícios gratuitos do Azure AD podem usar os [padrões de segurança](../conditional-access/concept-conditional-access-security-defaults.md) para habilitar a autenticação multifator em seu ambiente. |
| Multi-Factor Authentication para Office 365 | Esta versão é gerenciada no portal do Office 365 ou Microsoft 365. Os administradores podem [proteger os recursos do Office 365 com a verificação em duas etapas](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Esta versão faz parte de uma assinatura do Office 365. |
| Autenticação multifator para administradores do Azure AD | Os usuários atribuídos à função de administrador global do Azure AD nos locatários do Azure AD podem habilitar a verificação em duas etapas sem custo adicional. |
| Multi-Factor Authentication do Azure | Geralmente conhecida como a versão "completa", a autenticação multifator do Azure oferece o conjunto mais avançado de recursos. Ele fornece opções de configuração adicionais por meio do [portal do Azure](https://portal.azure.com), relatórios avançados e suporte para uma variedade de aplicativos locais e na nuvem. A autenticação multifator do Azure é um recurso de [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) e [Microsoft 365 Business](https://www.microsoft.com/microsoft-365/business). |

> [!NOTE]
> Novos clientes não poderão mais comprar a autenticação multifator do Azure como uma oferta autônoma em 1º de setembro de 2018. A autenticação multifator continuará disponível como um recurso em licenças Azure AD Premium ou Microsoft 365 Business.

## <a name="feature-comparison-of-versions"></a>Comparação de recursos de versões

A tabela a seguir fornece uma lista dos recursos que estão disponíveis nas várias versões da autenticação multifator do Azure.

> [!NOTE]
> Esta tabela de comparação aborda os recursos que fazem parte de cada versão da autenticação multifator. Se você tiver o serviço completo de autenticação multifator do Azure, alguns recursos poderão não estar disponíveis dependendo se você usar [MFA na nuvem ou MFA local](concept-mfa-whichversion.md).
>

| Funcionalidade | Multi-Factor Authentication para Office 365 | Autenticação multifator para administradores do Azure AD | Multi-Factor Authentication do Azure | Predefinições de segurança | 
| --- |:---:|:---:|:---:|:---:|
| Proteger contas de administrador do Azure AD com MFA |● |● (Somente contas de administrador global do Azure AD) |● |● |
| Aplicativo móvel como um fator secundário |● |● |● |● |
| Chamada telefônica como um segundo fator |● |● |● |   |
| SMS como um fator secundário |● |● |● |   |
| Senhas de aplicativo para clientes que não dão suporte a MFA |● |● |● |   |
| Controle de administração sobre métodos de verificação |● |● |● |   |
| Proteger contas que não são de administrador com MFA |● | |● |● |
| Modo PIN | | |● |   |
| Alerta de fraudes | | |● |   |
| Relatórios do MFA | | |● |   |
| Omissão de Uso Individual | | |● |   |
| Saudações personalizadas para chamadas telefónicas | | |● |   |
| ID de chamador personalizado para chamadas telefônicas | | |● |   |
| IPs Fidedignos | | |● |   |
| Memorizar MFA para dispositivos fidedignos |● |● |● |   |
| MFA para aplicativos locais | | |● |   |

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamada telefônica não estarão disponíveis para os usuários de MFA e SSPR em locatários gratuitos/de avaliação do Azure AD. As mensagens SMS não são afetadas por essa alteração. A chamada telefônica continuará disponível para os usuários em locatários pagos do Azure AD. Essa alteração afeta apenas os locatários gratuitos/de avaliação do Azure AD.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Como ativar a autenticação multifator do Azure para administradores do Azure AD

Os usuários atribuídos à função de administrador global nos locatários do Azure AD podem habilitar a verificação em duas etapas para suas contas de administrador global do Azure AD sem custo adicional. Se você estiver usando uma conta da Microsoft, poderá se registrar para a autenticação multifator usando as diretrizes encontradas no artigo de suporte conta Microsoft, [sobre a verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Se você não estiver usando uma conta da Microsoft, ative a autenticação multifator para administradores globais usando as diretrizes encontradas no artigo [como exigir a verificação em duas etapas para um usuário ou grupo](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Como comprar a autenticação multifator do Azure

Comprar licenças que incluem a autenticação multifator do Azure, como Azure Active Directory Premium ou um pacote de licenças que inclui Azure AD Premium ou acesso condicional e atribuí-las a seus usuários no Azure Active Directory.

### <a name="consumption-based-licensing"></a>Licenciamento baseado em consumo

O licenciamento baseado em consumo não estará mais disponível para novos clientes a partir de 1º de setembro de 2018.

A partir de 1º de setembro de 2018, novos provedores de autenticação podem não ser mais criados. Provedores de autenticação existentes podem continuar a ser usados e atualizados. A autenticação multifator continuará a ser um recurso disponível em licenças Azure AD Premium.

Ao usar um provedor de autenticação multifator do Azure, há dois modelos de uso disponíveis que são cobrados por meio de sua assinatura do Azure:

1. **Por usuário habilitado** – para empresas que desejam habilitar a verificação em duas etapas para um número fixo de funcionários que precisam regularmente de autenticação. A cobrança por usuário é baseada no número de usuários habilitados para MFA em seu locatário do Azure AD e no servidor MFA do Azure. Se os usuários estiverem habilitados para MFA no Azure AD e no servidor MFA do Azure, e a sincronização de domínio (Azure AD Connect) estiver habilitada, contaremos com um conjunto maior de usuários. Se a sincronização de domínio não estiver habilitada, nós contaremos a soma de todos os usuários habilitados para MFA no Azure AD e no servidor Azure MFA. A cobrança é rateada e relatada para o sistema comercial diariamente.

   > [!NOTE]
   > Exemplo de cobrança 1: você tem 5.000 usuários habilitados para MFA hoje. O sistema MFA divide esse número em 31 e relata 161,29 usuários para esse dia. Amanhã, você habilita mais 15 usuários, portanto, o sistema MFA relata 161,77 usuários para esse dia. Ao final do ciclo de cobrança, o número total de usuários cobrados em relação à sua assinatura do Azure aumenta em cerca de 5.000.
   >
   > Exemplo de cobrança 2: você tem uma mistura de usuários com licenças e usuários sem, portanto, você tem um provedor de MFA do Azure por usuário para fazer a diferença. Há 4.500 licenças Enterprise Mobility + Security em seu locatário, mas 5.000 usuários habilitados para MFA. Sua assinatura do Azure é cobrada por 500 usuários, rateado e relatado diariamente como 16,13 usuários.
   >

1. **Por autenticação** – para empresas que desejam habilitar a verificação em duas etapas para um grande grupo de usuários que raramente precisam de autenticação. A cobrança baseia-se no número de solicitações de verificação em duas etapas, independentemente de as verificações terem sido ou não negadas. Essa cobrança aparece em sua instrução de uso do Azure em pacotes de 10 autenticações e é relatada diariamente.

   > [!NOTE]
   > Exemplo de cobrança 3: hoje, o serviço do Azure MFA recebeu solicitações de verificação de 3.105 2 etapas. Sua assinatura do Azure é cobrada por pacotes de autenticação 310,5.
   >

É importante observar que você pode ter licenças, mas ainda ser cobrado pela configuração baseada em consumo. Se você configurar um provedor de MFA por autenticação do Azure, será cobrado para cada solicitação de verificação em duas etapas, mesmo as solicitações feitas por usuários que têm licenças. Se você configurar um provedor de MFA do Azure por usuário em um domínio que não esteja vinculado ao seu locatário do Azure AD, você será cobrado por usuário habilitado, mesmo que os usuários tenham licenças no Azure AD.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais detalhes de preços, consulte [preços do Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
