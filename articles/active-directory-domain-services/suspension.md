---
title: Domínios suspensos nos serviços de domínio Azure AD | Microsoft Docs
description: Conheça os diferentes estados de saúde para um domínio gerido Azure AD DS e como restaurar um domínio suspenso.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: b0b48e7ad494386052e6d94c32d7215b3f4f0202
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618829"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Compreenda os estados de saúde e resolva domínios suspensos nos Serviços de Domínio do Diretório Ativo do Azure

Quando o Azure Ative Directory Domain Services (Azure AD DS) não consegue servir um domínio gerido por um longo período de tempo, coloca o domínio gerido num estado suspenso. Se um domínio gerido permanecer num estado suspenso, é automaticamente eliminado. Para manter o seu Azure AD DS gerido domínio saudável e evitar a suspensão, resolva quaisquer alertas o mais rápido possível.

Este artigo explica por que os domínios geridos são suspensos e como recuperar um domínio suspenso.

## <a name="overview-of-managed-domain-states"></a>Visão geral dos estados de domínio geridos

Através do ciclo de vida de um domínio gerido, existem diferentes estados que indicam a sua saúde. Se o domínio gerido reportar um problema, resolva rapidamente a causa subjacente para impedir que o Estado continue a degradar-se.

![A progressão dos estados que um domínio gerido leva para a suspensão](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Um domínio gerido pode estar num dos seguintes estados:

* [Em Execução](#running-state)
* [Precisa de atenção](#needs-attention-state)
* [Suspenso](#suspended-state)
* [Eliminado](#deleted-state)

## <a name="running-state"></a>Estado de funcionamento

Um domínio gerido que está configurado corretamente e sem problemas está no estado *de Corrida.* Este é o estado desejado para um domínio gerido.

### <a name="what-to-expect"></a>O que esperar

* A plataforma Azure pode monitorizar regularmente a saúde do domínio gerido.
* Os controladores de domínio para o domínio gerido são remendados e atualizados regularmente.
* As alterações do Azure Ative Directory são regularmente sincronizadas para o domínio gerido.
* As cópias de segurança regulares são tomadas para o domínio gerido.

## <a name="needs-attention-state"></a>Precisa de estado de atenção

Um domínio gerido com um ou mais problemas que precisam de ser corrigidos está no estado *de atenção das Necessidades.* A página de saúde do domínio gerido lista os alertas, e indica onde há um problema.

Alguns alertas são transitórios e são automaticamente resolvidos pela plataforma Azure. Para outros alertas, pode corrigir o problema seguindo as etapas de resolução fornecidas. Há um alerta crítico, abra um pedido de [apoio do Azure][azure-support] para assistência adicional para resolução de problemas.

Um exemplo de alerta é quando há um grupo restritivo de segurança de rede. Nesta configuração, a plataforma Azure pode não ser capaz de atualizar e monitorizar o domínio gerido. Um alerta é gerado, e o estado muda para *Necessidades de atenção*.

Para obter mais informações, consulte [Como resolver alertas para um domínio gerido][resolve-alerts].

### <a name="what-to-expect"></a>O que esperar

Quando um domínio gerido se encontra no estado *de Atenção às Necessidades,* a plataforma Azure pode não ser capaz de monitorizar, corrigir, atualizar ou fazer dados de back-up regularmente. Em alguns casos, como uma configuração de rede inválida, os controladores de domínio para o domínio gerido podem ser inacessíveis.

* O domínio gerido encontra-se num estado pouco saudável e a monitorização contínua da saúde pode parar até que o alerta seja resolvido.
* Os controladores de domínio para o domínio gerido não podem ser remendados ou atualizados.
* As alterações do Azure Ative Directory podem não ser sincronizadas com o domínio gerido.
* As cópias de segurança para o domínio gerido não podem ser tomadas.
* Se resolver alertas não críticos que estão a afetar o domínio gerido, a saúde deve regressar ao estado *de Funcionamento.*
* Os alertas críticos são desencadeados para problemas de configuração onde a plataforma Azure não consegue chegar aos controladores de domínio. Se estes alertas críticos não forem resolvidos dentro de 15 dias, o domínio gerido entra no estado *suspenso.*

## <a name="suspended-state"></a>Estado suspenso

Um domínio gerido entra no estado **suspenso** por uma das seguintes razões:

* Um ou mais alertas críticos não foram resolvidos em 15 dias.
    * Os alertas críticos podem ser causados por uma configuração errada que bloqueia o acesso aos recursos que são necessários pela Azure AD DS. Por exemplo, o alerta [AADDS104: Erro de rede][alert-nsg] está por resolver há mais de 15 dias no domínio gerido.
* Há um problema de faturação com a assinatura Azure ou a subscrição do Azure expirou.

Os domínios geridos são suspensos quando a plataforma Azure não consegue gerir, monitorizar, remendar ou fazer o back up do domínio. Um domínio gerido permanece em estado *suspenso* por 15 dias. Para manter o acesso ao domínio gerido, resolva imediatamente os alertas críticos.

### <a name="what-to-expect"></a>O que esperar

O seguinte comportamento é experimentado quando um domínio gerido está no estado *suspenso:*

* Os controladores de domínio para o domínio gerido são desavisionados e não são alcançáveis dentro da rede virtual.
* O acesso seguro do LDAP ao domínio gerido através da internet, se ativado, deixa de funcionar.
* Existem falhas na autenticação do domínio gerido, iniciando sessão em VMs ligados ao domínio ou ligando-se sobre LDAP/LDAPS.
* As cópias de segurança para o domínio gerido já não são tomadas.
* A sincronização com a Azure AD para.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Como sabe se o seu domínio gerido está suspenso?

Você vê um [alerta][resolve-alerts] na página Azure AD DS Health no portal Azure que nota que o domínio está suspenso. O estado do domínio também mostra *suspenso.*

### <a name="restore-a-suspended-domain"></a>Restaurar um domínio suspenso

Para restaurar a saúde de um domínio gerido que está no estado *suspenso,* complete os seguintes passos:

1. No portal Azure, procure e selecione **os serviços de Domínio**.
1. Escolha o seu domínio gerido na lista, como *aaddscontoso.com,* selecione **Saúde**.
1. Selecione o alerta, como *AADDS503* ou *AADDS504,* dependendo da causa da suspensão.
1. Escolha o link de resolução fornecido no alerta e siga os passos para resolvê-lo.

Um domínio gerido só pode ser restaurado até à data da última cópia de segurança. A data da sua última cópia de segurança é apresentada na página **Saúde** do domínio gerido. Quaisquer alterações que ocorreram após a última cópia de segurança não serão restauradas. As cópias de segurança para um domínio gerido são armazenadas até 30 dias. As cópias de segurança com mais de 30 dias são eliminadas.

Depois de resolver alertas quando o domínio gerido estiver no estado *suspenso,* abra um pedido de apoio do [Azure][azure-support] para regressar a um estado saudável. Se houver uma cópia de segurança com menos de 30 dias, o suporte do Azure pode restaurar o domínio gerido.

## <a name="deleted-state"></a>Estado apagado

Se um domínio gerido permanecer no estado *suspenso* por 15 dias, é eliminado. Este processo é irrecuperável.

### <a name="what-to-expect"></a>O que esperar

Quando um domínio gerido entra no estado *eliminado,* vê-se o seguinte comportamento:

* Todos os recursos e backups para o domínio gerido são eliminados.
* Não se pode restaurar o domínio gerido. Tem de criar um domínio gerido por substituição para reutilizar o Azure AD DS.
* Depois de ser apagado, não é cobrado para o domínio gerido.

## <a name="next-steps"></a>Passos seguintes

Para manter o seu domínio gerido saudável e minimizar o risco de ser suspenso, aprenda a [resolver alertas para o seu domínio gerido][resolve-alerts].

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
