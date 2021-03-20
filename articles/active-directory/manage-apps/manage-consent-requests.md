---
title: Gerir o consentimento dos pedidos e avaliar os pedidos de consentimento no Azure Ative Directory
description: Saiba como gerir os pedidos de consentimento quando o consentimento do utilizador é desativado ou restrito, e como avaliar um pedido de consentimento administrativo em todo o inquilino para um pedido no Diretório Ativo Azure.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/27/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.openlocfilehash: 189a89276d922665dd1ad0fbacc77ba499137048
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99253107"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Gerir o consentimento dos pedidos e avaliar os pedidos de consentimento

A Microsoft recomenda a [desativação](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations) do consentimento do utilizador final para as aplicações. Isto centralizará o processo de tomada de decisão com a equipa de segurança e administrador de identidade da sua organização.

Após o consentimento do utilizador final ser desativado ou restringido, existem várias considerações importantes para garantir que a sua organização permanece segura, permitindo ainda a utilização de aplicações críticas empresariais. Estes passos são cruciais para minimizar o impacto na equipa de suporte da sua organização e administradores de TI, ao mesmo tempo que impedem o uso de contas nãogeridas em aplicações de terceiros.

## <a name="process-changes-and-education"></a>Mudanças de processo e educação

 1. Considere permitir que o fluxo de trabalho de [consentimento de administração (pré-visualização)](configure-admin-consent-workflow.md) permita que os utilizadores solicitem a aprovação do administrador diretamente a partir do ecrã de consentimento.

 2. Certifique-se de que todos os administradores compreendem as permissões e o [quadro de consentimento,](../develop/consent-framework.md)como funciona o pedido de [consentimento](../develop/application-consent-experience.md) e como avaliar um pedido de consentimento administrativo em todo [o cliente.](#evaluating-a-request-for-tenant-wide-admin-consent)
 3. Reveja os processos existentes da sua organização para que os utilizadores solicitem a aprovação do administrador para uma aplicação e estoirem atualizações se necessário. Se os processos forem alterados:
    * Atualizar a documentação relevante, monitorização, automação, e assim por diante.
    * Comunicar alterações de processos a todos os utilizadores afetados, desenvolvedores, equipas de suporte e administradores de TI.

## <a name="auditing-and-monitoring"></a>Auditoria e monitorização

1. [Aplicações de auditoria e permissões concedidas](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) na sua organização para garantir que nenhuma aplicação injustificada ou suspeita foi previamente autorizada a ter acesso aos dados.

2. [Reveja detete e remediar as concessões de consentimento ilícitos no Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) para práticas adicionais e salvaguardas contra aplicações suspeitas que solicitam o consentimento da OAuth.

3. Se a sua organização tiver a licença adequada:

    * Utilize funcionalidades adicionais [de auditoria de aplicações OAuth no Microsoft Cloud App Security](/cloud-app-security/investigate-risky-oauth).
    * Utilize [livros de trabalho do Monitor Azure para monitorizar permissões e atividades relacionadas](../reports-monitoring/howto-use-azure-monitor-workbooks.md) com o consentimento. O *manual do Consent Insights* fornece uma visão das aplicações pelo número de pedidos de consentimento falhados. Isto pode ser útil para priorizar os pedidos para os administradores reverem e decidirem se lhes concedem o consentimento administrativo.

### <a name="additional-considerations-for-reducing-friction"></a>Considerações adicionais para reduzir o atrito

Para minimizar o impacto em aplicações fidedignas e críticas ao negócio que já estão em uso, considere conceder proativamente o consentimento do administrador a aplicações que tenham um elevado número de subsídios de consentimento do utilizador:

1. Faça um inventário das aplicações já adicionadas à sua organização com elevado uso, com base em registos de login ou atividade de concessão de consentimento. Um [script](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) PowerShell pode ser usado para descobrir aplicações de forma rápida e fácil com um grande número de bolsas de consentimento do utilizador.

2. Avalie as principais aplicações que ainda não receberam o consentimento da administração.

   > [!IMPORTANT]
   > Avalie cuidadosamente uma aplicação antes de conceder o consentimento administrativo do inquilino, mesmo que muitos utilizadores da organização já tenham consentido por si mesmos.

3. Para cada pedido aprovado, conceda o consentimento administrativo do arrendatário utilizando um dos métodos documentados abaixo.

4. Para cada aplicação aprovada, considere [restringir o acesso do utilizador.](configure-user-consent.md)

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Avaliação de um pedido de consentimento administrativo em todo o inquilino

Conceder consentimento administrativo a todo o inquilino é uma operação sensível.  As permissões serão concedidas em nome de toda a organização, e podem incluir permissões para tentar operações altamente privilegiadas. Por exemplo, gestão de funções, acesso total a todas as caixas de correio ou a todos os sites, e personificação completa do utilizador.

Antes de conceder o consentimento administrativo ao arrendatário, deve garantir que confia na aplicação e no editor de aplicações, para o nível de acesso que está a conceder. Se não está confiante de que compreende quem controla a aplicação e por que razão o pedido está a solicitar as permissões, *não conceda consentimento*.

A lista a seguir fornece algumas recomendações a considerar na avaliação de um pedido de concessão de consentimento administrativo.

* **Compreenda as [permissões e o enquadramento](../develop/consent-framework.md) de consentimento na plataforma de identidade da Microsoft.**

* **Compreenda a diferença entre [permissões delegadas e permissões de inscrição.](../develop/v2-permissions-and-consent.md#permission-types)**

   As permissões de aplicação permitem que a aplicação aceda aos dados de toda a organização, sem qualquer interação do utilizador. As permissões delegadas permitem que o pedido aja em nome de um utilizador que em algum momento foi assinado na aplicação.

* **Compreenda as permissões que estão a ser pedidas.**

   As permissões solicitadas pelo pedido estão listadas no [pedido de consentimento.](../develop/application-consent-experience.md) Expandir o título de permissão apresentará a descrição da permissão. A descrição das permissões de aplicação geralmente termina em "sem um utilizador inscrito". A descrição das permissões delegadas geralmente termina com "em nome do utilizador inscrito". Permissões para a API do Gráfico microsoft são descritas no [Microsoft Graph Permissions Reference](/graph/permissions-reference) - consulte a documentação para outras APIs para entender as permissões que expõem.

   Se não entender a solicitação de uma autorização, *não conceda o consentimento*.

* **Compreenda qual o pedido que solicita permissões e quem publicou o pedido.**

   Desconfie de aplicações maliciosas que tentam parecer com outras aplicações.

   Se duvidar da legitimidade de um pedido ou do seu editor, *não conceda consentimento*. Em vez disso, procure confirmação adicional (por exemplo, diretamente do editor da aplicação).

* **Certifique-se de que as permissões solicitadas estão alinhadas com as funcionalidades que espera da aplicação.**

   Por exemplo, uma aplicação que ofereça a gestão do site SharePoint pode exigir acesso delegado para ler todas as coleções do site, mas não precisaria necessariamente de acesso total a todas as caixas de correio, ou privilégios de imitação completa no diretório.

   Se suspeitar que o pedido está a solicitar mais permissões do que necessita, *não conceda consentimento.* Contacte o editor de aplicações para obter mais detalhes.

## <a name="granting-consent-as-an-administrator"></a>Concessão de consentimento como administrador

### <a name="granting-tenant-wide-admin-consent"></a>Concessão de consentimento administrativo em todo o inquilino
Consulte [o consentimento da administração do grant para um pedido](grant-admin-consent.md) de instruções passo a passo para conceder o consentimento administrativo do porta-administração do inquilino a partir do portal Azure, utilizando a Azure AD PowerShell, ou a partir do consentimento.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Concessão de consentimento em nome de um utilizador específico
Em vez de conceder o consentimento para toda a organização, um administrador também pode usar a [API do Gráfico da Microsoft](/graph/use-the-api) para conceder consentimento a permissões delegadas em nome de um único utilizador. Para mais informações, consulte [Obter acesso em nome de um utilizador](/graph/auth-v2-user).

## <a name="limiting-user-access-to-applications"></a>Limitação do acesso dos utilizadores às aplicações
O acesso dos utilizadores às aplicações ainda pode ser limitado mesmo quando o consentimento administrativo do arrendatário foi concedido. Para obter mais informações sobre como exigir a atribuição do utilizador a uma aplicação, consulte [métodos para atribuir utilizadores e grupos.](./assign-user-or-group-access-portal.md)

Para obter uma visão geral mais ampla, incluindo como lidar com cenários complexos adicionais, consulte [a utilização do Azure AD para a gestão do acesso a aplicações.](what-is-access-management.md)

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Desativar todas as futuras operações de consentimento do utilizador para qualquer aplicação
Desativar o consentimento do utilizador para todo o seu diretório impede que os utilizadores finais consintam em qualquer aplicação. Os administradores ainda podem consentir em nome do utilizador. Para saber mais sobre o consentimento da aplicação e por que pode ou não querer consentir, leia [o consentimento do utilizador e da administração](../develop/howto-convert-app-to-be-multi-tenant.md).

Para desativar todas as futuras operações de consentimento do utilizador em todo o seu diretório, siga estes passos:
1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**
2.  Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.
3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
4.  Selecione **Utilizadores e grupos** no menu de navegação.
5.  Selecione **Definições do utilizador**.
6.  Desativar todas as futuras operações de consentimento do utilizador, definindo os **Utilizadores, pode permitir que as aplicações acedam aos seus dados** para **alternar** para No e clicar no botão **Guardar.**

## <a name="next-steps"></a>Passos seguintes
* [Cinco passos para garantir a sua infraestrutura de identidade](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [Configure o fluxo de trabalho de consentimento administrativo](configure-admin-consent-workflow.md)
* [Configurar a forma como os utilizadores finais concedem consentimento às aplicações](configure-user-consent.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](../develop/v2-permissions-and-consent.md)