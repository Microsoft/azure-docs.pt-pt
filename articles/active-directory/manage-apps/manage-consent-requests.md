---
title: Gerenciando consentimento para aplicativos e avaliando solicitações de consentimento-Azure AD
description: Saiba como gerenciar solicitações de consentimento quando o consentimento do usuário é desabilitado ou restrito e como avaliar uma solicitação de consentimento de administrador em todo o locatário para um aplicativo.
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
ms.openlocfilehash: 0c6a77fa8c6c48c9c3d4f2eba3ba56ac9114707a
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122993"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Gerenciamento de consentimento para aplicativos e avaliação de solicitações de consentimento

A Microsoft [recomenda](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations) desabilitar o consentimento do usuário final para aplicativos. Isso centralizará o processo de tomada de decisões com a equipe de administrador de identidade e segurança de sua organização.

Após o consentimento do usuário final ser desabilitado ou restrito, há várias considerações importantes para garantir que sua organização permaneça segura e, ao mesmo tempo, permita que aplicativos críticos de negócios sejam usados. Essas etapas são cruciais para minimizar o impacto na equipe de suporte da sua organização e nos administradores de ti, ao mesmo tempo em que impedem o uso de contas não gerenciadas em aplicativos de terceiros.

## <a name="process-changes-and-education"></a>Processar alterações e educação

 1. Considere habilitar o [fluxo de trabalho de consentimento do administrador (versão prévia)](configure-admin-consent-workflow.md) para permitir que os usuários solicitem a aprovação do administrador diretamente da tela de consentimento.

 2. Verifique se todos os administradores entendem as [permissões e a estrutura de consentimento](../develop/consent-framework.md), como funciona o [prompt de consentimento](../develop/application-consent-experience.md) e como [avaliar uma solicitação de consentimento de administrador em todo o locatário](#evaluating-a-request-for-tenant-wide-admin-consent).
 3. Examine os processos existentes de sua organização para que os usuários solicitem a aprovação do administrador de um aplicativo e faça atualizações, se necessário. Se os processos forem alterados:
    * Atualize a documentação, o monitoramento, a automação e assim por diante relevantes.
    * Comunique alterações no processo a todos os usuários, desenvolvedores, equipes de suporte e administradores de ti afetados.

## <a name="auditing-and-monitoring"></a>Auditoria e monitorização

1. [Faça auditoria de aplicativos e permissões concedidas](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions) em sua organização para garantir que nenhum aplicativo não garantido ou suspeito tenha recebido acesso aos dados anteriormente.

2. Examine [detectar e corrigir concessões de consentimento ilícitos no Office 365](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) para obter práticas recomendadas adicionais e proteções contra aplicativos suspeitos solicitando o consentimento do OAuth.

3. Se sua organização tiver a licença apropriada:

    * Use [recursos adicionais de auditoria de aplicativo OAuth no Microsoft Cloud app Security](https://docs.microsoft.com/azure/cloud-app-security/investigate-risky-oauth).
    * Use [Azure monitor pastas de trabalho para monitorar as permissões e a](../reports-monitoring/howto-use-azure-monitor-workbooks.md) atividade relacionada ao consentimento. A pasta de trabalho de *informações de consentimento* fornece uma exibição de aplicativos por número de solicitações de consentimento com falha. Isso pode ser útil para priorizar os aplicativos para que os administradores examinem e decidam se desejam conceder a eles o consentimento do administrador.

### <a name="additional-considerations-for-reducing-friction"></a>Considerações adicionais para reduzir o conflito

Para minimizar o impacto em aplicativos confiáveis e críticos para os negócios que já estão em uso, considere conceder proativamente o consentimento do administrador para aplicativos que têm um grande número de concessões de consentimento do usuário:

1. Faça um inventário dos aplicativos já adicionados à sua organização com alto uso, com base nos logs de entrada ou na atividade de concessão de consentimento. Um [script](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) do PowerShell pode ser usado para descobrir rapidamente e facilmente aplicativos com um grande número de concessões de consentimento do usuário.

2. Avalie os principais aplicativos que ainda não receberam o consentimento do administrador.

   > [!IMPORTANT]
   > Avalie cuidadosamente um aplicativo antes de conceder o consentimento do administrador em todo o locatário, mesmo que muitos usuários na organização já tenham consentido por si mesmos.

3. Para cada aplicativo aprovado, conceda consentimento de administrador em todo o locatário usando um dos métodos documentados abaixo.

4. Para cada aplicativo aprovado, considere [restringir o acesso do usuário](configure-user-consent.md).

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Avaliando uma solicitação de consentimento de administrador em todo o locatário

Conceder consentimento de administrador em todo o locatário é uma operação confidencial.  As permissões serão concedidas em nome de toda a organização e podem incluir permissões para tentar operações altamente privilegiadas. Por exemplo, gerenciamento de função, acesso completo a todas as caixas de correio ou todos os sites e representação completa do usuário.

Antes de conceder o consentimento do administrador em todo o locatário, você deve garantir que confia no aplicativo e no editor do aplicativo, para o nível de acesso que você está concedendo. Se você não tiver certeza de que entendeu quem controla o aplicativo e por que o aplicativo está solicitando as permissões, *não conceda o consentimento*.

A lista a seguir fornece algumas recomendações a serem consideradas ao avaliar uma solicitação para conceder consentimento de administrador.

* **Entenda as [permissões e a estrutura de consentimento](../develop/consent-framework.md) na plataforma Microsoft Identity.**

* **Entenda a diferença entre [permissões delegadas e permissões de aplicativo](../develop/v2-permissions-and-consent.md#permission-types).**

   As permissões de aplicativo permitem que o aplicativo acesse os dados de toda a organização, sem nenhuma interação do usuário. As permissões delegadas permitem que o aplicativo atue em nome de um usuário que, em algum momento, foi conectado ao aplicativo.

* **Entenda as permissões que estão sendo solicitadas.**

   As permissões solicitadas pelo aplicativo são listadas no [prompt de consentimento](../develop/application-consent-experience.md). Expandir o título da permissão exibirá a descrição da permissão. A descrição das permissões de aplicativo geralmente terminará em "sem um usuário conectado". A descrição de permissões delegadas geralmente terminará com "em nome do usuário conectado". As permissões para a API de Microsoft Graph são descritas em [referência de permissões de Microsoft Graph] – consulte a documentação para outras APIs para entender as permissões que elas expõem.

   Se você não entender uma permissão que está sendo solicitada, *não conceda o consentimento*.

* **Entenda qual aplicativo está solicitando permissões e quem publicou o aplicativo.**

   Tenha cuidado com aplicativos mal-intencionados tentando se parecer com outros aplicativos.

   Se você sentir a legitimidade de um aplicativo ou de seu editor, *não conceda o consentimento*. Em vez disso, busque uma confirmação adicional (por exemplo, diretamente do editor do aplicativo).

* **Verifique se as permissões solicitadas estão alinhadas com os recursos que você espera do aplicativo.**

   Por exemplo, um aplicativo que ofereça gerenciamento de sites do SharePoint pode exigir acesso delegado para ler todos os conjuntos de sites, mas não precisa necessariamente de acesso completo a todas as caixas de correio ou privilégios de representação completa no diretório.

   Se você suspeitar que o aplicativo está solicitando mais permissões do que o necessário, *não conceda o consentimento*. Contate o editor do aplicativo para obter mais detalhes.

## <a name="granting-consent-as-an-administrator"></a>Concedendo consentimento como administrador

### <a name="granting-tenant-wide-admin-consent"></a>Concedendo consentimento de administrador em todo o locatário

Consulte [conceder consentimento de administrador em todo o locatário a um aplicativo](grant-admin-consent.md) para obter instruções passo a passo para conceder consentimento de administrador em todo o locatário do portal do Azure, usando o PowerShell do Azure ad ou o próprio prompt de consentimento.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Concedendo consentimento em nome de um usuário específico

Em vez de conceder consentimento para toda a organização, um administrador também pode usar o [API do Graph do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) para conceder consentimento a permissões delegadas em nome de um único usuário. Para fazer isso, envie uma solicitação de `POST` para criar uma entidade [OAuth2PermissionGrant](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) em que `consentType` está definido como "principal" e `principalId` é definido como a ID de objeto para o usuário em nome do qual o consentimento está sendo concedido.

## <a name="limiting-user-access-to-applications"></a>Limitando o acesso do usuário a aplicativos

O acesso dos usuários aos aplicativos ainda pode ser limitado mesmo quando o consentimento do administrador em todo o locatário tiver sido concedido. Para obter mais informações sobre como exigir a atribuição de usuário a um aplicativo, consulte [métodos para atribuir usuários e grupos](methods-for-assigning-users-and-groups.md).

Para obter mais uma visão geral mais ampla, incluindo como lidar com cenários complexos adicionais, consulte [usando o Azure ad para o gerenciamento de acesso do aplicativo](what-is-access-management.md).

## <a name="next-steps"></a>Passos seguintes

[Cinco passos para proteger a sua infraestrutura de identidade](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[Configurar o fluxo de trabalho de consentimento do administrador](configure-admin-consent-workflow.md)

[Configurar como os usuários finais concordam com os aplicativos](configure-user-consent.md)

[Permissões e consentimento na plataforma Microsoft Identity](../develop/active-directory-v2-scopes.md)

[Azure AD no StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)