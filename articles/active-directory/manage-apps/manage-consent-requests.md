---
title: Gestão do consentimento para pedidos e avaliação de pedidos de consentimento - Azure AD
description: Saiba como gerir os pedidos de consentimento quando o consentimento do utilizador é desativado ou restrito, e como avaliar um pedido de consentimento administrativo em todo o inquilino para uma aplicação.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0451fe18629a572c9b49f14924bfa50293f42a2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77367845"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Gestão do consentimento para pedidos e avaliação de pedidos de consentimento

A Microsoft [recomenda](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations) que o utilizador final desaviva às aplicações. Isto centralizará o processo de tomada de decisão com a equipa de administrador de segurança e identidade da sua organização.

Após o consentimento do utilizador final ser desativado ou restringido, existem várias considerações importantes para garantir que a sua organização se mantém segura, permitindo ainda a utilização de aplicações críticas de negócios. Estes passos são cruciais para minimizar o impacto na equipa de suporte da sua organização e administradores de TI, ao mesmo tempo que impedem a utilização de contas não geridas em aplicações de terceiros.

## <a name="process-changes-and-education"></a>Mudanças de processo e educação

 1. Considere permitir que o fluxo de trabalho de [consentimento do administrador (pré-visualização)](configure-admin-consent-workflow.md) permita que os utilizadores solicitem a aprovação do administrador diretamente do ecrã de consentimento.

 2. Certifique-se de que todos os administradores compreendem as [permissões e o quadro](../develop/consent-framework.md)de consentimento, como funciona o [consentimento](../develop/application-consent-experience.md) e como avaliar um pedido de consentimento administrativo em todo [o inquilino.](#evaluating-a-request-for-tenant-wide-admin-consent)
 3. Reveja os processos existentes da sua organização para que os utilizadores solicitem a aprovação do administrador para uma aplicação e efaçam atualizações se necessário. Se os processos forem alterados:
    * Atualizar a documentação relevante, monitorização, automação, e assim por diante.
    * Comunicar alterações de processo a todos os utilizadores afetados, desenvolvedores, equipas de suporte e administradores de TI.

## <a name="auditing-and-monitoring"></a>Auditoria e monitorização

1. [Audite aplicações e autorizou](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions) a sua organização a garantir que nenhuma aplicação injustificada ou suspeita tenha tido acesso a dados.

2. Reveja [detete e remediar as subvenções de consentimento ilícito saneadas no Office 365](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) para obter melhores práticas adicionais e salvaguardas contra pedidos suspeitos que solicitem o consentimento da OAuth.

3. Se a sua organização tiver a licença adequada:

    * Utilize funcionalidades adicionais de [auditoria de aplicações OAuth no Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth).
    * Utilize [os livros de trabalho do Monitor Azure para monitorizar permissões e](../reports-monitoring/howto-use-azure-monitor-workbooks.md) atividades relacionadas com o consentimento. O livro de trabalho *Consent Insights* fornece uma visão das aplicações através de um número de pedidos de consentimento falhados. Isto pode ser útil para priorizar os pedidos para os administradores reverem e decidirem se lhes concedem o consentimento da administração.

### <a name="additional-considerations-for-reducing-friction"></a>Considerações adicionais para reduzir o atrito

Para minimizar o impacto em aplicações de confiança e críticas ao negócio que já estão em uso, considere conceder proativamente o consentimento do administrador a aplicações que tenham um elevado número de subsídios de consentimento dos utilizadores:

1. Faça um inventário das aplicações já adicionadas à sua organização com elevado uso, com base em registos de login ou atividade de concessão de consentimento. Um [script](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) PowerShell pode ser usado para descobrir aplicações de forma rápida e fácil com um grande número de subsídios de consentimento do utilizador.

2. Avalie as principais aplicações que ainda não receberam o consentimento do administrador.

   > [!IMPORTANT]
   > Avalie cuidadosamente uma aplicação antes de conceder o consentimento do administrador em todo o inquilino, mesmo que muitos utilizadores da organização já tenham consentido por si mesmos.

3. Para cada pedido aprovado, conceda o consentimento do administrador em todo o inquilino utilizando um dos métodos documentados abaixo.

4. Para cada aplicação aprovada, considere [restringir o acesso ao utilizador](configure-user-consent.md).

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Avaliação de um pedido de consentimento administrativo em todo o inquilino

Conceder o consentimento do administrador em todo o inquilino é uma operação sensível.  As permissões serão concedidas em nome de toda a organização, e podem incluir permissões para tentar operações altamente privilegiadas. Por exemplo, gestão de papéis, acesso total a todas as caixas de correio ou todos os sites, e personificação completa do utilizador.

Antes de conceder o consentimento do administrador em todo o inquilino, deve garantir que confia na aplicação e no editor de aplicações, pelo nível de acesso que está a conceder. Se não está confiante de que entende quem controla o pedido e por que razão o pedido está a solicitar as permissões, *não conceda o consentimento*.

A lista seguinte fornece algumas recomendações a considerar na avaliação de um pedido de concessão de consentimento administrativo.

* **Compreenda as [permissões e o quadro](../develop/consent-framework.md) de consentimento na plataforma de identidade da Microsoft.**

* **Compreenda a diferença entre [permissões delegadas e permissões](../develop/v2-permissions-and-consent.md#permission-types)de candidatura.**

   As permissões de aplicação permitem que a aplicação aceda aos dados de toda a organização, sem qualquer interação do utilizador. As permissões delegadas permitem que o pedido atue em nome de um utilizador que em algum momento foi assinado no pedido.

* **Compreenda as permissões que estão a ser solicitadas.**

   As permissões solicitadas pelo pedido estão listadas no pedido de [consentimento](../develop/application-consent-experience.md). A expansão do título de permissão mostrará a descrição da permissão. A descrição das permissões de pedido terminará geralmente em "sem um utilizador inscrito". A descrição das permissões delegadas terminará geralmente com "em nome do utilizador inscrito". As permissões para a Microsoft Graph API são descritas em [Microsoft Graph Permissions Reference]- consulte a documentação para outras APIs para entender as permissões que expõem.

   Se não entende que seja solicitada uma autorização, *não conceda o consentimento.*

* **Entenda qual o pedido de permissão e quem publicou o pedido.**

   Desconfie de aplicações maliciosas tentando parecer outras aplicações.

   Se duvidar da legitimidade de um pedido ou do seu editor, *não conceda o seu consentimento.* Em vez disso, procure uma confirmação adicional (por exemplo, diretamente do editor de aplicações).

* **Certifique-se de que as permissões solicitadas estão alinhadas com as funcionalidades que espera da aplicação.**

   Por exemplo, uma aplicação que ofereça gestão do site SharePoint pode exigir acesso delegado para ler todas as coleções do site, mas não precisaria necessariamente de acesso total a todas as caixas de correio, ou privilégios de personificação completa no diretório.

   Se suspeitar que o pedido está a pedir mais permissões do que o necessário, *não conceda o consentimento.* Contacte o editor de aplicações para obter mais detalhes.

## <a name="granting-consent-as-an-administrator"></a>Concessão de consentimento como administrador

### <a name="granting-tenant-wide-admin-consent"></a>Concessão do consentimento do administrador em todo o inquilino

Consulte o consentimento do administrador de [grant em toda a administração para um pedido](grant-admin-consent.md) de instruções passo a passo para conceder o consentimento administrativo de todo o inquilino do portal Azure, usando a Azure AD PowerShell, ou do próprio consentimento.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Concessão de consentimento em nome de um utilizador específico

Em vez de conceder o consentimento para toda a organização, um administrador também pode usar a [Microsft Graph API](https://docs.microsoft.com/graph/use-the-api) para conceder o consentimento a permissões delegadas em nome de um único utilizador. Para mais informações, consulte [Ter acesso em nome de um utilizador](https://docs.microsoft.com/graph/auth-v2-user).

## <a name="limiting-user-access-to-applications"></a>Limitação do acesso dos utilizadores às aplicações

O acesso dos utilizadores às aplicações ainda pode ser limitado mesmo quando o consentimento do administrador em todo o inquilino tiver sido concedido. Para obter mais informações sobre como exigir a atribuição do utilizador a uma aplicação, consulte [métodos para atribuir utilizadores e grupos.](methods-for-assigning-users-and-groups.md)

Para uma visão geral mais ampla, incluindo como lidar com cenários complexos adicionais, consulte [a utilização de AD Azure para gestão](what-is-access-management.md)de acesso a aplicações.

## <a name="next-steps"></a>Passos seguintes

[Cinco passos para garantir a sua infraestrutura de identidade](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[Configure o fluxo de trabalho de consentimento da administração](configure-admin-consent-workflow.md)

[Configure como os utilizadores finais consentem com as aplicações](configure-user-consent.md)

[Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

[Anúncio Azure em StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)