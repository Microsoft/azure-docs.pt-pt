---
title: 'Azure Active Directory Domain Services: Alertas de resolução de problemas | Documentos da Microsoft'
description: Resolver alertas para o Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: ergreenl
ms.openlocfilehash: 360c6c98227e52f0540b00ef136888d3d143b9fb
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951079"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Serviços de domínio do Azure AD - alertas de resolução de problemas
Este artigo fornece os guias de resolução de problemas para todos os alertas que poderá notar no seu domínio gerido.


Escolha os passos de resolução de problemas que correspondem para o ID ou uma mensagem no alerta.

| **ID do alerta** | **Mensagem de alerta** | **Resolução** |
| --- | --- | :--- |
| AADDS001 | *Secure LDAP via internet está ativada para o domínio gerido. No entanto, o acesso à porta 636 não está bloqueado através de um grupo de segurança de rede. Isso pode expor as contas de utilizador no domínio gerido a ataques de força bruta de palavra-passe.* | [Configuração incorreta do LDAP seguro](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | *Diretório do Azure AD associado com o seu domínio gerido poderá ter sido eliminado. O domínio gerido já não está a ser uma configuração suportada. Microsoft não é possível monitorizar, gerir, corrigir e sincronizar o domínio gerido.* | [Diretório em falta](#aadds100-missing-directory) |
| AADDS101 | *Não é possível ativar o Azure AD Domain Services num diretório do Azure AD B2C.* | [O Azure AD B2C está em execução neste diretório](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Um Principal de serviço necessárias para o Azure AD Domain Services funcionar corretamente foi eliminado do diretório do Azure AD. Esta configuração afeta a capacidade da Microsoft para monitorizar, gerir, corrigir e sincronizar o domínio gerido.* | [Principal de serviço em falta](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | *O intervalo de endereços IP para a rede virtual em que tiver ativado o Azure AD Domain Services está num intervalo IP público. O Azure AD Domain Services tem de estar ativados numa rede virtual com um intervalo de endereços IP privados. Esta configuração afeta a capacidade da Microsoft para monitorizar, gerir, patches e sincronizar o domínio gerido.* | [O endereço é um intervalo de IP público](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *A Microsoft é não é possível alcançar os controladores de domínio para este domínio gerido. Isto pode acontecer se um grupo de segurança de rede (NSG) configurado na sua rede virtual bloqueia o acesso ao domínio gerido. Outra razão possível é se há uma rota definida pelo utilizador esse tráfego de entrada de blocos da internet.* | [Erro de rede](active-directory-ds-troubleshoot-nsg.md) |
| AADDS105 | *O principal de serviço com o ID da aplicação "d87dcbc6-a371-462e-88e3-28ad15ec4e64" foi eliminado e, em seguida, recriado. A recriação de deixa para trás inconsistentes permissões em recursos de serviços de domínio do Azure AD necessários para atender a seu domínio gerido. Sincronização de palavras-passe no seu domínio gerido poderá ser afetada.* | [A aplicação de sincronização de palavra-passe está desatualizada](active-directory-ds-troubleshoot-service-principals.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS500 | *O domínio gerido última sincronização com o Azure AD no [date]. Os utilizadores podem não ser possível iniciar sessão no domínio gerido ou associações de grupo podem não ser sincronizadas com o Azure AD.* | [Sincronização ainda não ocorreu há algum tempo](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *O domínio gerido última cópia de segurança em [date].* | [Ainda não foi feita uma cópia de segurança há algum tempo](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *O certificado de secure LDAP para o domínio gerido irá expirar em [date]].* | [Prazo de expiração de certificado de secure LDAP](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *O domínio gerido está suspenso porque a subscrição do Azure associada ao domínio não está ativa.* | [Suspensão devido à subscrição desativada](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *O domínio gerido está suspensa devido a uma configuração inválida. O serviço foi não é possível gerir, patch, ou atualizar os controladores de domínio para o seu domínio gerido por muito tempo.* | [Suspensão devido a uma configuração inválida](#aadds504-suspension-due-to-an-invalid-configuration) |


## <a name="aadds100-missing-directory"></a>AADDS100: Diretório de falta
**Mensagem de alerta:**

*Diretório do Azure AD associado com o seu domínio gerido poderá ter sido eliminado. O domínio gerido já não está a ser uma configuração suportada. Microsoft não é possível monitorizar, gerir, corrigir e sincronizar o domínio gerido.*

**Resolução:**

Este erro é habitualmente provocado por incorretamente mover a sua subscrição do Azure para um novo Azure diretório AD e a eliminar o antigo diretório do Azure AD que continua associado ao Azure AD Domain Services.

Este erro é irrecuperável. Para resolver, tem [eliminar o domínio gerido existente](active-directory-ds-disable-aadds.md) e recriá-lo no diretório novo. Se estiver a ter problemas ao eliminar, contacte a equipa de produto do Azure Active Directory Domain Services [para obter suporte](active-directory-ds-contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: O Azure AD B2C está em execução neste diretório
**Mensagem de alerta:**

*Não é possível ativar o Azure AD Domain Services num diretório do Azure AD B2C.*

**Resolução:**

>[!NOTE]
>Para poder continuar a utilizar o Azure AD Domain Services, tem de recriar a sua instância do Azure AD Domain Services num diretório não - Azure AD B2C.

Para restaurar o seu serviço, siga estes passos:

1. [Eliminar o seu domínio gerido](active-directory-ds-disable-aadds.md) do diretório do Azure AD existente.
2. Crie um novo diretório que não é um diretório do Azure AD B2C.
3. Siga os [introdução ao](active-directory-ds-getting-started.md) guia para recriar um domínio gerido.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: O endereço é um intervalo de IP público

**Mensagem de alerta:**

*O intervalo de endereços IP para a rede virtual em que tiver ativado o Azure AD Domain Services está num intervalo IP público. O Azure AD Domain Services tem de estar ativados numa rede virtual com um intervalo de endereços IP privados. Esta configuração afeta a capacidade da Microsoft para monitorizar, gerir, patches e sincronizar o domínio gerido.*

**Resolução:**

> [!NOTE]
> Para resolver este problema, tem de eliminar o domínio gerido existente e voltar a criá-lo numa rede virtual com um intervalo de endereços IP privados. Este processo é problemático.

Antes de começar, leia os **privado espaço de endereços IP v4** secção [este artigo](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

Dentro da rede virtual, as máquinas podem efetuar pedidos aos recursos do Azure que estão no mesmo intervalo de endereços IP que estão configuradas para a sub-rede. No entanto, uma vez que a rede virtual está configurada para este intervalo, essas solicitações serão encaminhadas dentro da rede virtual e não irão aceder os recursos da web pretendido. Esta configuração pode levar a erros imprevisíveis com os serviços de domínio do Azure AD.

**Se for o proprietário o intervalo de endereços IP na internet que está configurado na sua rede virtual, este alerta pode ser ignorado. No entanto, o Azure AD Domain Services não é possível consolidar para o [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] com esta configuração, uma vez que ele pode levar a erros imprevisíveis.**


1. [Eliminar o seu domínio gerido](active-directory-ds-disable-aadds.md) do diretório.
2. Corrigir o intervalo de endereços IP para a sub-rede
  1. Navegue para o [página de redes virtuais no portal do Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Selecione a rede virtual que pretende utilizar para o Azure AD Domain Services.
  3. Clique em **espaço de endereços** em definições
  4. Atualize o intervalo de endereços ao clicar no intervalo de endereços existente e editá-lo ou adicionar um intervalo de endereços adicionais. Certificar-se de que o novo intervalo de endereços está num intervalo de IP privados. Guarde as alterações.
  5. Clique em **sub-redes** no painel de navegação esquerdo.
  6. Clique na sub-rede que pretende editar na tabela.
  7. Atualizar o intervalo de endereços e guarde as alterações.
3. Siga [guia de introdução ao utilizar o Azure AD Domain Services](active-directory-ds-getting-started.md) para recriar o seu domínio gerido. Certifique-se de que escolha uma rede virtual com um intervalo de endereços IP privados.
4. A associação ao domínio suas máquinas virtuais para o novo domínio, siga [este guia](active-directory-ds-admin-guide-join-windows-vm-portal.md).
8. Para garantir que o alerta é resolvido, verifique o estado de funcionamento do seu domínio nas duas horas.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: A sincronização não foi concluída no tempo

**Mensagem de alerta:**

*O domínio gerido última sincronização com o Azure AD no [date]. Os utilizadores podem não ser possível iniciar sessão no domínio gerido ou associações de grupo podem não ser sincronizadas com o Azure AD.*

**Resolução:**

[Verificar estado de funcionamento do seu domínio](active-directory-ds-check-health.md) para todos os alertas que possam indicar problemas na sua configuração do seu domínio gerido. Às vezes, com problemas de configuração podem bloquear a capacidade da Microsoft para sincronizar o domínio gerido. Se for capaz de resolver todos os alertas, aguarde duas horas e verificação de volta para ver se a sincronização foi concluída.

Aqui estão algumas razões comuns, por que deixa de sincronização em domínios geridos:
- Ligação de rede é bloqueada no domínio gerido. Para saber mais sobre a verificação da sua rede para problemas, leia como a [resolver problemas dos grupos de segurança de rede](active-directory-ds-troubleshoot-nsg.md) e [requisitos de rede do Azure AD Domain Services](active-directory-ds-networking.md).
-  Sincronização de palavra-passe nunca foi configurada ou foi concluída. Para configurar a sincronização de palavra-passe, leia [este artigo](active-directory-ds-getting-started-password-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Uma cópia de segurança não foi tomada há algum tempo

**Mensagem de alerta:**

*O domínio gerido última cópia de segurança em [date].*

**Resolução:**

[Verificar estado de funcionamento do seu domínio](active-directory-ds-check-health.md) para todos os alertas que possam indicar problemas na sua configuração do seu domínio gerido. Às vezes, com problemas de configuração podem bloquear a capacidade da Microsoft para sincronizar o domínio gerido. Se for capaz de resolver todos os alertas, aguarde duas horas e verificação de volta para ver se a sincronização foi concluída.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Suspensão devido à subscrição desativada

**Mensagem de alerta:**

*O domínio gerido está suspenso porque a subscrição do Azure associada ao domínio não está ativa.*

**Resolução:**

> [!WARNING]
> Se o seu domínio gerido está suspenso durante um período prolongado de tempo, é em risco de que está a ser eliminado. É melhor abordar a suspensão mais depressa possível. Para obter mais informações, visite [este artigo](active-directory-ds-suspension.md).

Para restaurar o seu serviço, [renovar a sua subscrição do Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) associados com o seu domínio gerido.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Suspensão devido a uma configuração inválida

**Mensagem de alerta:**

*O domínio gerido está suspensa devido a uma configuração inválida. O serviço foi não é possível gerir, patch, ou atualizar os controladores de domínio para o seu domínio gerido por muito tempo.*

**Resolução:**

> [!WARNING]
> Se o seu domínio gerido está suspenso durante um período prolongado de tempo, é em risco de que está a ser eliminado. É melhor abordar a suspensão mais depressa possível. Para obter mais informações, visite [este artigo](active-directory-ds-suspension.md).

[Verificar estado de funcionamento do seu domínio](active-directory-ds-check-health.md) para todos os alertas que possam indicar problemas na sua configuração do seu domínio gerido. Se pode resolver qualquer um destes alertas, fazê-lo. Depois, contacte o suporte para reativar a subscrição.


## <a name="contact-us"></a>Contacte-nos
Contacte a equipa de produto do Azure Active Directory Domain Services para [partilhar comentários ou para o suporte](active-directory-ds-contact-us.md).
