---
title: Versões do MFA do Azure e planos de consumo - Azure Active Directory
description: Informações sobre o cliente de multi-factor Authentication e os diferentes métodos e as versões disponíveis.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 765b9aff898cbea763d66a20db91b3390cc48bb4
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310005"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Como obter o Azure multi-factor Authentication

Quando se trata de proteger as suas contas, verificação de dois passos deve ser padrão em sua organização. Esta funcionalidade é especialmente importante para as contas que tem acesso a recursos privilegiado. Por esse motivo, a Microsoft oferece as funcionalidades de verificação de dois passos básicos para o Office 365 e administradores do Azure Active Directory (Azure AD) para sem custam adicionais. Se quiser atualizar os recursos para os seus administradores ou expandir a verificação de dois passos para o restante dos seus utilizadores, pode comprar o multi-factor Authentication do Azure de várias formas.

> [!IMPORTANT]
> Este artigo destina-se para ser um guia para ajudar a compreender as diferentes formas de comprar o Azure multi-factor Authentication. Para obter detalhes específicos sobre preços e faturação, deve sempre consultar os [página de preços do multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versões disponíveis do multi-factor Authentication do Azure

A tabela seguinte descreve as diferenças entre três versões da autenticação multifator:

| Versão | Descrição |
| --- | --- |
| Multi-Factor Authentication para Office 365 <br> Microsoft 365 Empresas |Esta versão funciona exclusivamente em aplicações do Office 365 e é gerido a partir do portal do Office 365 ou do Microsoft 365. Os administradores podem [proteger os recursos do Office 365 com verificação de dois passos](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Esta versão faz parte de uma subscrição do Office 365 ou do Microsoft 365 empresas. |
| Multi-factor Authentication para administradores do Azure AD | Utilizadores atribuídos a função de Administrador Global do Azure AD em inquilinos do Azure AD podem ativar a verificação de dois passos, sem custos adicionais.|
| Multi-Factor Authentication do Azure | Muitas vezes referido como a versão "completa", o multi-factor Authentication do Azure oferece o mais diversificado conjunto de capacidades. Ele fornece opções de configuração adicionais através da [portal do Azure](https://portal.azure.com), relatórios avançados e o suporte para uma variedade de locais e aplicações na cloud. O Azure multi-factor Authentication é uma funcionalidade do [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features)e pode ser implementada na cloud ou no local. |

> [!NOTE]
> Novos clientes já não podem comprar o multi-factor Authentication do Azure como uma autónoma oferta em vigor a partir 1 de Setembro de 2018. Multi-factor authentication irá continuar a estar disponível como uma funcionalidade no licenças do Azure AD Premium.

## <a name="feature-comparison-of-versions"></a>Comparação de recursos de versões

A tabela seguinte fornece uma lista dos recursos que estão disponíveis nas várias versões do multi-factor Authentication.

> [!NOTE]
> Esta tabela de comparação aborda os recursos que fazem parte de cada versão do multi-factor Authentication. Se tiver o serviço de multi-factor Authentication do Azure completo, algumas funcionalidades poderão não estar disponíveis consoante utilize ou [MFA na nuvem ou MFA no local](concept-mfa-whichversion.md).
>

| Funcionalidade | Multi-Factor Authentication para Office 365 | Multi-factor Authentication para administradores do Azure AD | Multi-Factor Authentication do Azure |
| --- |:---:|:---:|:---:|
| Proteger contas de administrador do Azure AD com a MFA |● |● (apenas para contas de Administrador Global do Azure AD) |● |
| Aplicação móvel como um segundo fator |● |● |● |
| Chamada telefónica como segundo fator de |● |● |● |
| SMS como um segundo fator |● |● |● |
| Palavras-passe de aplicação para clientes que não suportam MFA |● |● |● |
| Controlo de administração sobre métodos de verificação |● |● |● |
| Proteger contas de não-administrador com a MFA |● (apenas para aplicações do Office 365) | |● |
| Modo PIN | | |● |
| Alerta de fraudes | | |● |
| Relatórios do MFA | | |● |
| Omissão de Uso Individual | | |● |
| Saudações personalizadas para chamadas telefónicas | | |● |
| ID do chamador personalizado para chamadas telefónicas | | |● |
| IPs Fidedignos | | |● |
| Memorizar MFA para dispositivos fidedignos |● |● |● |
| MFA para aplicações no local | | |● |

> [!IMPORTANT]
> A partir de Março de 2019 as opções de chamada telefónica não estarão disponível para os utilizadores do MFA e o SSPR em inquilinos gratuitos/avaliação do Azure AD. Mensagens SMS não são afetadas por esta alteração. Chamada telefónica continuará a estar disponíveis para os utilizadores na pago inquilinos do Azure AD. Esta alteração só afeta o inquilinos gratuitos/avaliação do Azure AD.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Como ativar o multi-factor Authentication para administradores do Azure AD

Utilizadores atribuídos a função de Administrador Global de inquilinos do Azure AD podem ativar a verificação de dois passos para suas contas de Administrador Global do AD do Azure sem custos adicionais. Se estiver a utilizar uma Account Microsoft, pode se registrar para através da orientação encontrada no artigo de suporte de conta Microsoft, a autenticação multifator [sobre a verificação de dois passos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Se não estiver a utilizar uma Account Microsoft, ativar o multi-factor authentication para os administradores globais através da orientação encontrada no artigo [como requerer verificação de dois passos para um utilizador ou grupo](howto-mfa-userstates.md).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Como comprar o multi-factor Authentication do Azure

Se gostaria de todas as funcionalidades oferecidas pelo Azure multi-factor Authentication, existem várias opções:

### <a name="option-1---licenses-that-include-mfa"></a>Opção 1 - licenças que incluem o MFA

Comprar licenças que incluem o Azure multi-factor Authentication, como o Azure Active Directory Premium ou um pacote de licença que inclui o Azure AD Premium e atribuí-los aos seus utilizadores no Azure Active Directory.

### <a name="option-2---mfa-consumption-based-model"></a>Opção 2 - modelo baseado no consumo de MFA

Esta opção já não está disponível para novos clientes em vigor a partir de 1 de Setembro de 2018.

Em vigor a partir de 1 de Setembro de 2018 novos auth fornecedores já não podem ser criados. Fornecedores de autenticação existentes podem continuar a ser utilizado e atualizados. Multi-factor authentication irá continuar a ser uma funcionalidade disponível em licenças do Azure AD Premium.

Quando utilizar um fornecedor de autenticação do multi-factor do Azure, existem dois modelos de utilização disponíveis que são faturadas por meio da sua subscrição do Azure:

1. **Por utilizador ativado** – para empresas que pretendem ativar a verificação de dois passos para um número fixo de funcionários que precisar regularmente de autenticação. Faturação por utilizador baseia-se no número de utilizadores ativados para a MFA no seu inquilino do Azure AD e o servidor de MFA do Azure. Se os utilizadores ativados para o MFA do Azure AD e o servidor de MFA do Azure, a sincronização de domínio (Azure AD Connect) está ativada, e Contamos o maior conjunto de utilizadores. Se a sincronização de domínio não está ativada, então Contamos a soma de todos os utilizadores ativados para o MFA no Azure AD e o servidor MFA do Azure. A faturação é rateada e comunicada ao sistema de comércio diariamente.

   > [!NOTE]
   > Exemplo de faturação 1: Terá de 5000 utilizadores ativados para o MFA hoje mesmo. O sistema MFA divide esse número por 31 e 161.29 usuários de relatórios para esse dia. Amanhã é habilitar 15 mais usuários, para que o sistema MFA relatórios 161.77 utilizadores para esse dia. No final do ciclo de faturação, o número total de utilizadores faturados no âmbito da sua subscrição do Azure adiciona até em torno de 5000.
   >
   > Exemplo de faturação 2: Tiver uma mistura de utilizadores com licenças e os utilizadores sem, pelo que terá de um fornecedor de MFA do Azure por utilizador para constituir a diferença. Existem 4.500 Enterprise Mobility + licenças de segurança no seu inquilino, mas de 5000 utilizadores ativados para o MFA. Sua subscrição do Azure é cobrada a 500 usuários, rateada e comunicada diariamente como 16.13 utilizadores.
   >

1. **Por autenticação** – para empresas que pretendem ativar a verificação de dois passos para um grande grupo de utilizadores que precisam com pouca frequência a autenticação. A faturação baseia-se no número de pedidos de verificação de dois passos, independentemente se essas verificações com êxito ou serão negadas. Esta faturação é apresentada a declaração de utilização do Azure nos pacotes de 10 autenticações e é comunicada diariamente.

   > [!NOTE]
   > Exemplo de faturação 3: Hoje em dia, o serviço de MFA do Azure recebeu 3,105 pedidos de verificação de dois passos. A subscrição do Azure é cobrada por 310.5 pacotes de autenticação.
   >

É importante observar que pode ter licenças, mas ainda será faturado com base no consumo de configuração. Se configurar um fornecedor de MFA do Azure por autenticação, a faturação para cada pedido de verificação de dois passos, mesmo aqueles efetuados pelos utilizadores que possuem licenças. Se configurar um fornecedor de MFA do Azure por utilizador no domínio que não está ligado ao seu inquilino do Azure AD, a faturação por utilizador ativado, mesmo que os utilizadores tiverem licenças no Azure AD.

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais detalhes de preços, consulte [preços de MFA do Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Escolha se pretende implementar o MFA do Azure [na cloud ou no local](concept-mfa-whichversion.md)
