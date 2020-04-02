---
title: Domínios suspensos nos Serviços de Domínio da AD Azure Microsoft Docs
description: Saiba mais sobre os diferentes estados de saúde para um domínio gerido pela AD DS azure e como restaurar um domínio suspenso.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 0b6e28fc92361fe20fc34e92386bf17199b19617
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519031"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Compreender os estados de saúde e resolver domínios suspensos nos Serviços de Domínio de Diretório Ativo azure

Quando o Azure Ative Directory Domain Services (Azure AD DS) não consegue servir um domínio gerido durante um longo período de tempo, coloca o domínio gerido num estado suspenso. Se um domínio gerido permanecer em estado suspenso, é automaticamente apagado. Para manter o seu domínio Gerido Azure AD DS saudável e evitar a suspensão, resolva quaisquer alertas o mais rápido possível.

Este artigo explica porque é que os domínios geridos são suspensos e como recuperar um domínio suspenso.

## <a name="overview-of-managed-domain-states"></a>Visão geral dos estados de domínio geridos

Através do ciclo de vida de um domínio gerido pela AD DS Azure, existem diferentes estados que indicam a sua saúde. Se o domínio gerido reportar um problema, resolva rapidamente a causa subjacente para impedir que o Estado continue a degradar-se.

![A progressão dos estados que um domínio gerido pela AD DS azure leva para a suspensão](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Um domínio gerido por AD DS azure pode estar num dos seguintes estados:

* [A executar](#running-state)
* [Precisa de atenção](#needs-attention-state)
* [Suspenso](#suspended-state)
* [Eliminado](#deleted-state)

## <a name="running-state"></a>Estado de execução

Um domínio gerido pelo Azure AD DS que está configurado corretamente e em funcionamento sem problemas está no estado *de Execução.* Este é o estado desejado para um domínio gerido.

### <a name="what-to-expect"></a>O que esperar

* A plataforma Azure pode monitorizar regularmente a saúde do domínio gerido.
* Os controladores de domínio para o domínio gerido são remendados e atualizados regularmente.
* As alterações do Diretório Ativo Azure são regularmente sincronizadas com o domínio gerido.
* Os backups regulares são levados para o domínio gerido.

## <a name="needs-attention-state"></a>Precisa estado de atenção

Um domínio gerido pela Azure AD DS com um ou mais problemas que precisam de ser corrigidos está no estado de *atenção das Necessidades.* A página de saúde do domínio gerido lista os alertas e indica onde há um problema. Alguns alertas são transitórios e são automaticamente resolvidos pela plataforma Azure. Para outros alertas, pode corrigir o problema seguindo as medidas de resolução fornecidas. Há um alerta crítico, abra um pedido de [apoio azure][azure-support] para assistência adicional para resolver problemas.

Um exemplo de alerta é quando há um grupo restritivo de segurança da rede. Nesta configuração, a plataforma Azure pode não ser capaz de atualizar e monitorizar o domínio gerido. Um alerta é gerado, e o estado muda para *Precisa de atenção*.

Para mais informações, consulte [Como resolver alertas para um domínio gerido pelo Azure AD DS][resolve-alerts].

### <a name="what-to-expect"></a>O que esperar

Quando um domínio gerido por DS Azure está no estado *needs Attention,* a plataforma Azure pode não ser capaz de monitorizar, patch, atualizar ou fazer back-up regularmente. Em alguns casos, tal como com uma configuração de rede inválida, os controladores de domínio para o domínio gerido podem ser inacessíveis.

* O domínio gerido encontra-se em estado pouco saudável e a monitorização contínua da saúde pode parar até que o alerta seja resolvido.
* Os controladores de domínio para o domínio gerido não podem ser corrigidos ou atualizados.
* As alterações do Diretório Ativo Azure não podem ser sincronizadas para o domínio gerido.
* Os backups para o domínio gerido não podem ser tomados.
* Se resolver alertas não críticos que estão a afetar o domínio gerido, a saúde deve regressar ao estado *de Execução.*
* São desencadeados alertas críticos para problemas de configuração em que a plataforma Azure não consegue chegar aos controladores de domínio. Se estes alertas críticos não forem resolvidos dentro de 15 dias, o domínio gerido entra no estado *suspenso.*

## <a name="suspended-state"></a>Estado suspenso

Um domínio gerido pela AD DS azure entra no estado **suspenso** por uma das seguintes razões:

* Um ou mais alertas críticos não foram resolvidos há 15 dias.
    * Os alertas críticos podem ser causados por uma configuração errada que bloqueia o acesso aos recursos que são necessários pelo Azure AD DS. Por exemplo, o alerta [AADDS104: O Erro][alert-nsg] de Rede está por resolver há mais de 15 dias no domínio gerido.
* Há um problema de faturação com a subscrição do Azure ou a subscrição do Azure expirou.

Os domínios geridos são suspensos quando a plataforma Azure não consegue gerir, monitorizar, remendar ou fazer o back-up do domínio. Um domínio gerido permanece em estado *suspenso* por 15 dias. Para manter o acesso ao domínio gerido, resolva imediatamente os alertas críticos.

### <a name="what-to-expect"></a>O que esperar

O seguinte comportamento é experimentado quando um domínio gerido pela AD DS azure está no estado *suspenso:*

* Os controladores de domínio para o domínio gerido são desprovisionados e não são acessíveis dentro da rede virtual.
* O acesso LDAP seguro ao domínio gerido através da internet, se ativado, deixa de funcionar.
* Existem falhas na autenticação do domínio gerido, no login em VMs unificados ao domínio ou na ligação ao LDAP/LDAPS.
* Os backups para o domínio gerido já não são tomados.
* Sincronização com a AD Azure para.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Como sabe se o seu domínio gerido está suspenso?

Vê um [alerta][resolve-alerts] na página Azure AD DS Health no portal Azure que nota que o domínio está suspenso. O estado do domínio também mostra *suspenso.*

### <a name="restore-a-suspended-domain"></a>Restaurar um domínio suspenso

Para restaurar a saúde de um domínio gerido pela AD DS azure que está no estado *suspenso,* complete os seguintes passos:

1. No portal Azure, procure e selecione serviços de **Domínio.**
1. Escolha o seu domínio gerido azure AD DS da lista, como *aaddscontoso.com,* em seguida, selecione **Health**.
1. Selecione o alerta, como *AADDS503* ou *AADDS504,* dependendo da causa da suspensão.
1. Escolha o link de resolução que é fornecido no alerta e siga os passos para resolvê-lo.

Um domínio gerido só pode ser restaurado até à data da última cópia de segurança. A data da sua última cópia de segurança é apresentada na página de **Saúde** do domínio gerido. Quaisquer alterações que ocorreram após a última cópia de segurança não serão restauradas. As cópias de segurança para um domínio gerido são armazenadas por um n.o 30 dias. As cópias de segurança com mais de 30 dias são apagadas.

Depois de resolver alertas quando o domínio gerido estiver no estado *suspenso,* abra um pedido de [apoio azure][azure-support] para voltar a um estado saudável. Se houver uma cópia de segurança a menos de 30 dias, o suporte do Azure pode restaurar o domínio gerido.

## <a name="deleted-state"></a>Estado eliminado

Se um domínio gerido pela AD DS azure permanecer no estado *suspenso* por 15 dias, é eliminado. Este processo é irrecuperável.

### <a name="what-to-expect"></a>O que esperar

Quando um domínio gerido por AD DS azure entra no estado *Apagado,* vê-se o seguinte comportamento:

* Todos os recursos e backups para o domínio gerido são eliminados.
* Não é possível restaurar o domínio gerido e é necessário criar um domínio gerido de substituição para reutilizar o Azure AD DS.
* Depois de apagado, não é cobrado pelo domínio gerido.

## <a name="next-steps"></a>Passos seguintes

Para manter o seu domínio gerido pelo Azure AD DS saudável e minimizar o risco de este ser suspenso, aprenda a [resolver alertas para o seu domínio gerido][resolve-alerts].

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
