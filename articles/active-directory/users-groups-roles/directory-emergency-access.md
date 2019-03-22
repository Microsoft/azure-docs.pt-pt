---
title: Gerir contas de administrador de acesso de emergência - Azure Active Directory | Documentos da Microsoft
description: Este artigo descreve como utilizar contas de acesso de emergência para ajudar a evitar inadvertidamente a ser impedido de aceder ao seu inquilino do Azure Active Directory (Azure AD).
services: active-directory
author: markwahl-msft
ms.author: curtand
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f430a832ffb35b95d0bf4eff2d82be5ecc3d865c
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224945"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Gerir contas de acesso de emergência no Azure AD

É importante que impeça a ser inadvertidamente impedido de aceder ao seu inquilino do Azure Active Directory (Azure AD) porque não é possível iniciar sessão ou ativar a conta de um usuário individual existente como administrador. Pode mitigar o impacto de inadvertida falta de acesso administrativo através da criação de dois ou mais *contas de acesso de emergência* no seu inquilino.

Contas de acesso de emergência são altamente privilegiadas e não são atribuídas a indivíduos específicos. Contas de acesso de emergência estão limitadas a emergência ou "break glass" cenários onde não não possível utilizar contas administrativas normais. As organizações têm de manter uma meta de restringir a utilização da conta de emergência para apenas as horas em que é absolutamente necessário.

Este artigo fornece diretrizes para gerir contas de acesso de emergência no Azure AD.

## <a name="when-would-you-use-an-emergency-access-account"></a>Quando utilizar uma conta de acesso de emergência?

Uma organização poderá ter de utilizar uma conta de acesso de emergência nas seguintes situações:

- As contas de utilizador forem federadas e Federação está atualmente indisponível devido a uma quebra de rede de célula ou uma falha do fornecedor de identidade. Por exemplo, se o anfitrião do fornecedor de identidade no seu ambiente tornou-se para baixo, os utilizadores poderão não é possível iniciar sessão quando do Azure AD redireciona para o seu fornecedor de identidade.
- Os administradores são registados através do multi-factor Authentication e todos os seus dispositivos individuais estão disponíveis ou o serviço está indisponível. Os utilizadores poderão não é possível concluir a multi-factor Authentication para ativar uma função. Por exemplo, uma indisponibilidade de rede de célula está a impedi-los de responder a chamadas telefónicas ou receber mensagens de texto, os mecanismos de autenticação de dois únicos que eles registado para o respetivo dispositivo.
- A pessoa com acesso de Administrador Global mais recente tiver saído da organização. O Azure AD impede que a última conta de Administrador Global a ser eliminada, mas não impede a conta seja eliminado ou desativado no local. De qualquer situação poderá fazer a organização não é possível recuperar a conta.
- Circunstâncias imprevistas, tais como um desastre natural emergência, durante o qual um telefone celular ou a outras redes podem não estar disponíveis. 

## <a name="create-two-cloud-based-emergency-access-accounts"></a>Criar duas contas de acesso de emergência com base na cloud

Crie duas ou mais contas de acesso de emergência. Estas contas devem ser contas apenas na cloud que utilizam o \*. domínio onmicrosoft.com e que não são federados ou sincronizados a partir de um ambiente no local.

Quando configurar estas contas, têm de ser cumpridos os seguintes requisitos:

- As contas de acesso de emergência não devem ser associadas a qualquer utilizador individual na organização. Certifique-se de que as suas contas não estiverem ligadas com qualquer celulares fornecido pelo funcionário, tokens de hardware que viajam com funcionários individuais ou outras credenciais de funcionário específico. Esta precaução abrange instâncias em que um funcionário é inacessível quando a credencial é necessária. É importante certificar-se de que todos os dispositivos registados são mantidos num local conhecido e seguro que tenha vários meios de comunicação com o Azure AD.
- O mecanismo de autenticação utilizado para uma conta de acesso de emergência deve ser diferente daquele usado pelo seu outras contas administrativas, incluindo outras contas de acesso de emergência.  Por exemplo, se o seu administrador normal início de sessão é por meio da MFA no local, em seguida, MFA do Azure seria um mecanismo diferente.  No entanto se a MFA do Azure é a parte principal da autenticação para as suas contas administrativas, considere uma abordagem diferente para eles, tais como utilizar o acesso condicional com um fornecedor MFA de terceiros.
- O dispositivo ou a credencial não deve expirar ou ser no âmbito de limpeza automática devido à falta de utilização.  
- Deve efetuar a atribuição de função de Administrador Global permanente para as suas contas de acesso de emergência. 


### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Excluir pelo menos uma conta de telefone a autenticação multifator

Para reduzir o risco de um ataque resultantes de uma palavra-passe comprometida, do Azure AD recomenda que requer autenticação multifator para todos os utilizadores individuais. Esse grupo inclui administradores e todos os outros (por exemplo, responsáveis financeiros) cuja conta comprometida seria ter um impacto significativo.

No entanto, não, pelo menos, uma das suas contas de acesso de emergência deve ter o mesmo mecanismo de autenticação multifator, como as outras contas de não emergência. Isto inclui soluções de terceiros multi-factor authentication. Se tiver uma política de acesso condicional para exigir [multi-factor authentication para cada administrador](../authentication/howto-mfa-userstates.md) para o Azure AD e outro software ligado como aplicações de serviço (SaaS), deve excluir contas de acesso de emergência nesta requisito e configurar um mecanismo diferente em vez disso. Além disso, certifique-se de que as contas não têm uma política de autenticação multifator por utilizador.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Excluir pelo menos uma conta de políticas de acesso condicional

Durante uma emergência, não pretender uma política para bloquear o acesso para corrigir um problema. Conta de acesso de emergência, pelo menos, um deve ser excluída da todas as políticas de acesso condicional. Se tiver ativado uma [política de linha de base](../conditional-access/baseline-protection.md), deve excluir as contas de acesso de emergência.

## <a name="additional-guidance-for-hybrid-customers"></a>Orientações adicionais para clientes híbridos

É uma opção adicional para as organizações que utilizam os serviços de domínio do AD e AD FS ou o fornecedor de identidade semelhante para federar com o Azure AD configurar uma conta de acesso de emergência cuja afirmação MFA pode ser fornecida por esse fornecedor de identidade.  Por exemplo, a conta de acesso de emergência pode ser retornada por um par de certificado e chave, como um armazenados num smart card.  Quando esse utilizador é autenticado para o AD, o ADFS pode fornecer uma afirmação ao Azure AD que indica que o utilizador cumpre os requisitos de MFA.  Mesmo com essa abordagem, as organizações ainda devem ter contas de acesso de emergência com base na cloud no caso de não é possível estabelecer a Federação. 

## <a name="store-devices-and-credentials-in-a-safe-location"></a>Store dispositivos e as credenciais numa localização segura

As organizações precisam garantir que as credenciais para contas de acesso de emergência são mantidas seguras e conhecidas somente a indivíduos que têm autorização para utilizá-los. Alguns clientes usam um smart card e outras utilizam as palavras-passe. Uma palavra-passe para uma conta de acesso de emergência, normalmente, é separada em duas ou três partes, escrita em partes separadas de papel e armazenada em cofres seguras, incêndios, que se encontram em localizações seguras, em separado.

Se utilizar palavras-passe, certifique-se de que as contas têm de palavras-passe fortes que não expiram a palavra-passe. O ideal é que as palavras-passe devem ser, pelo menos, 16 carateres, há muito tempo e aleatória gerado.


## <a name="monitor-sign-in-and-audit-logs"></a>Monitorizar o início de sessão e registos de auditoria

Monitor de [registos de início de sessão no Azure AD e auditoria](../reports-monitoring/concept-sign-ins.md) para quaisquer inícios de sessão e auditar a atividade das contas de acesso de emergência. Normalmente, estas contas não devem estar a iniciar sessão e não devem estar fazendo alterações, então, usá-los é provável que sejam anómalas e exigir uma investigação de segurança.

## <a name="validate-accounts-at-regular-intervals"></a>Validar contas em intervalos regulares

Para treinar os membros da Equipe utilizar contas de acesso de emergência e validar as contas de acesso de emergência, efetue os seguintes passos mínimos em intervalos regulares:

- Certifique-se de que a equipe de monitorização de segurança estejam cientes de que a atividade de verificação de conta está em curso.
- Certifique-se de que o processo de vidro de garantia de reparação de emergência para utilizar estas contas é documentado e atual.
- Certifique-se de que os administradores e os responsáveis pela segurança, que poderão ter de executar estes passos durante uma emergência estão treinados sobre o processo.
- Atualizar as credenciais da conta, em particular, as palavras-passe, para as suas contas de acesso de emergência em seguida, confirme que as contas de acesso de emergência podem iniciar sessão e executar tarefas administrativas.
- Certifique-se de que os utilizadores não registou multi-factor Authentication ou a reposição de palavra-passe self-service (SSPR) para o dispositivo ou detalhes pessoais de qualquer utilizador individual. 
- Se as contas registadas para o multi-factor Authentication para um dispositivo, para utilização durante a ativação de início de sessão ou função, certifique-se de que o dispositivo não está acessível para todos os administradores que podem precisar usá-lo durante uma emergência. Certifique-se também de que o dispositivo pode comunicar através de, pelo menos, dois caminhos de rede que não partilham um modo comum de falha. Por exemplo, o dispositivo pode comunicar com a internet através da rede sem fio de um recurso e uma rede de fornecedor de célula.

Estes passos devem ser efetuados em intervalos regulares e para que as alterações principais:

- Pelo menos a cada 90 dias
- Quando tiver ocorrido uma alteração recente na equipe de TI, como uma alteração de tarefa, uma partidas ou uma nova contratação
- Quando tem alterado as subscrições do Azure AD na organização

## <a name="next-steps"></a>Passos Seguintes

- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](directory-admin-roles-secure.md)
- [Adicionar utilizadores a utilizar o Azure AD](../fundamentals/add-users-azure-active-directory.md) e [atribuir o novo utilizador à função de Administrador Global](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Inscreva-se para o Azure AD Premium](../fundamentals/active-directory-get-started-premium.md), se ainda não tiver já
- [Como requerer verificação de dois passos para um utilizador](../authentication/howto-mfa-userstates.md)
- [Configurar proteções adicionais para os administradores globais do Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts), se estiver a utilizar o Office 365
- [Iniciar uma revisão de acesso de administradores globais](../privileged-identity-management/pim-how-to-start-security-review.md) e [transição os administradores globais existentes para funções de administrador mais específicas](directory-assign-admin-roles.md)
