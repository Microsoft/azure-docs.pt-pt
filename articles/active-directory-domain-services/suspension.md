---
title: Domínios suspensos no Azure AD Domain Services | Microsoft Docs
description: Saiba mais sobre os diferentes Estados de integridade para um domínio gerenciado AD DS do Azure e como restaurar um domínio suspenso.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 31a1c7cd72d57b9c680452d5e84f8fe78f47cebb
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71693319"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Entender os Estados de integridade e resolver domínios suspensos no Azure Active Directory Domain Services

Quando Azure Active Directory Domain Services (Azure AD DS) não é capaz de atender a um domínio gerenciado por um longo período de tempo, ele coloca o domínio gerenciado em um estado suspenso. Se um domínio gerenciado permanecer em um estado suspenso, ele será excluído automaticamente. Para manter a integridade do domínio gerenciado do Azure AD DS e evitar a suspensão, resolva todos os alertas da maneira mais rápida possível.

Este artigo explica por que os domínios gerenciados são suspensos e como recuperar um domínio suspenso.

## <a name="overview-of-managed-domain-states"></a>Visão geral dos Estados de domínio gerenciado

Por meio do ciclo de vida de um domínio gerenciado AD DS do Azure, há Estados diferentes que indicam sua integridade. Se o domínio gerenciado relatar um problema, resolva rapidamente a causa subjacente para impedir que o Estado continue a degradar.

![A progressão de Estados que um domínio gerenciado AD DS do Azure leva em direção à suspensão](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Um domínio gerenciado do Azure AD DS pode estar em um dos seguintes Estados:

* [Executado](#running-state)
* [Precisa de atenção](#needs-attention-state)
* [Suspenso](#suspended-state)
* [Excluí](#deleted-state)

## <a name="running-state"></a>Estado de execução

Um domínio gerenciado do Azure AD DS que está configurado corretamente e em execução sem problemas está no estado *executando* . Esse é o estado desejado para um domínio gerenciado.

### <a name="what-to-expect"></a>O que esperar

* A plataforma Azure pode monitorar regularmente a integridade do domínio gerenciado.
* Os controladores de domínio para o domínio gerenciado são corrigidos e atualizados regularmente.
* As alterações de Azure Active Directory são sincronizadas regularmente com o domínio gerenciado.
* Backups regulares são feitos para o domínio gerenciado.

## <a name="needs-attention-state"></a>Precisa de estado de atenção

Um domínio gerenciado AD DS do Azure com um ou mais problemas que precisam ser corrigidos está no estado *precisa de atenção* . A página de integridade do domínio gerenciado lista os alertas e indica onde há um problema. Alguns alertas são transitórios e são resolvidos automaticamente pela plataforma do Azure. Para outros alertas, você pode corrigir o problema seguindo as etapas de resolução fornecidas. Há um alerta crítico, [abra uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

Um exemplo de um alerta é quando há um grupo de segurança de rede restritivo. Nessa configuração, a plataforma do Azure pode não ser capaz de atualizar e monitorar o domínio gerenciado. Um alerta é gerado e o estado muda para *precisa de atenção*.

Para obter mais informações, consulte [como solucionar problemas de alertas para um domínio gerenciado do Azure AD DS][resolve-alerts].

### <a name="what-to-expect"></a>O que esperar

Quando um domínio gerenciado do Azure AD DS está no estado *precisa de atenção* , a plataforma do Azure pode não ser capaz de monitorar, aplicar patch, atualizar ou fazer backup de dados regularmente. Em alguns casos, como com uma configuração de rede inválida, os controladores de domínio para o domínio gerenciado podem estar inacessíveis.

* O domínio gerenciado está em um estado não íntegro e o monitoramento de integridade contínuo pode parar até que o alerta seja resolvido.
* Os controladores de domínio para o domínio gerenciado não podem ser corrigidos ou atualizados.
* As alterações do Azure Active Directory não podem ser sincronizadas com o domínio gerenciado.
* Os backups do domínio gerenciado não podem ser usados.
* Se você resolver alertas não críticos que estão afetando o domínio gerenciado, a integridade deverá retornar ao estado de *execução* .
* Alertas críticos são disparados para problemas de configuração em que a plataforma Azure não consegue acessar os controladores de domínio. Se esses alertas críticos não forem resolvidos dentro de 15 dias, o domínio gerenciado entrará no estado *suspenso* .

## <a name="suspended-state"></a>Estado suspenso

Um domínio gerenciado do Azure AD DS entra no estado **suspenso** por um dos seguintes motivos:

* Um ou mais alertas críticos não foram resolvidos em 15 dias.
    * Alertas críticos podem ser causados por uma configuração incorreta que bloqueia o acesso a recursos que são necessários para o Azure AD DS. Por exemplo, o alerta [AADDS104: O erro][alert-nsg] de rede não foi resolvido por mais de 15 dias no domínio gerenciado.
* Há um problema de cobrança com a assinatura do Azure ou a assinatura do Azure expirou.

Os domínios gerenciados são suspensos quando a plataforma Azure não pode gerenciar, monitorar, aplicar patch ou fazer backup do domínio. Um domínio gerenciado permanece em um estado *suspenso* por 15 dias. Para manter o acesso ao domínio gerenciado, resolva alertas críticos imediatamente.

### <a name="what-to-expect"></a>O que esperar

O comportamento a seguir é experimentado quando um domínio gerenciado do Azure AD DS está no estado *suspenso* :

* Os controladores de domínio para o domínio gerenciado são desprovisionados e não podem ser acessados dentro da rede virtual.
* LDAP Seguro acesso ao domínio gerenciado pela Internet, se habilitado, para de funcionar.
* Há falhas na autenticação para o domínio gerenciado, logon em VMs ingressadas no domínio ou conexão por LDAP/LDAPs.
* Os backups do domínio gerenciado não são mais utilizados.
* A sincronização com o Azure AD é interrompida.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Como saber se o domínio gerenciado está suspenso?

Você verá um [alerta][resolve-alerts] na página do Azure AD DS Health no portal do Azure que observa que o domínio está suspenso. O estado do domínio também mostra *suspenso*.

### <a name="restore-a-suspended-domain"></a>Restaurar um domínio suspenso

Para restaurar a integridade de um domínio gerenciado AD DS do Azure que está no estado *suspenso* , conclua as seguintes etapas:

1. Na portal do Azure, procure e selecione serviços de **domínio**.
1. Escolha o domínio gerenciado AD DS do Azure na lista, como *contoso.com*, e selecione **integridade**.
1. Selecione o alerta, como *AADDS503* ou *AADDS504*, dependendo da causa da suspensão.
1. Escolha o link de resolução fornecido no alerta e siga as etapas para resolvê-lo.

Um domínio gerenciado só pode ser restaurado até a data do último backup. A data do último backup é exibida na página **integridade** do domínio gerenciado. Quaisquer alterações ocorridas após o último backup não serão restauradas. Os backups de um domínio gerenciado são armazenados por até 30 dias. Os backups com mais de 30 dias são excluídos.

Depois de resolver os alertas quando o domínio gerenciado estiver no estado *suspenso* , [abra uma solicitação de suporte do Azure][azure-support] para retornar a um estado íntegro. Se houver um backup de menos de 30 dias, o suporte do Azure poderá restaurar o domínio gerenciado.

## <a name="deleted-state"></a>Estado excluído

Se um domínio gerenciado do Azure AD DS permanecer no estado *suspenso* por 15 dias, ele será excluído. Esse processo é irrecuperável.

### <a name="what-to-expect"></a>O que esperar

Quando um domínio gerenciado do Azure AD DS entra no estado *excluído* , o seguinte comportamento é visto:

* Todos os recursos e backups para o domínio gerenciado são excluídos.
* Você não pode restaurar o domínio gerenciado e precisa criar um domínio gerenciado de substituição para reutilizar o AD DS do Azure.
* Depois de excluída, você não será cobrado pelo domínio gerenciado.

## <a name="next-steps"></a>Passos seguintes

Para manter a integridade do domínio gerenciado do Azure AD DS e minimizar o risco de ele se tornar suspenso, saiba como [resolver alertas para seu domínio gerenciado][resolve-alerts].

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
