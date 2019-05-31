---
title: 'Azure Active Directory Domain Services: Suspenso domínios | Documentos da Microsoft'
description: A suspensão do domínio gerido e eliminação
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: a4e43518ce70c040b8243034c52e4b215548a47c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246723"
---
# <a name="suspended-domains"></a>Domínios suspensos
Quando o Azure Active Directory Domain Services (Azure AD DS) não consegue atender a um domínio gerido por um longo período de tempo, coloca o domínio gerido num estado suspenso. Este artigo explica por que os domínios geridos são suspensas e para saber como corrigir um domínio suspenso.


## <a name="states-your-managed-domain-can-be-in"></a>Podem ter Estados de seu domínio gerido

![Linha cronológica de domínio suspenso](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

O gráfico anterior descreve os possíveis Estados de um domínio gerido do Azure AD DS pode estar em.

### <a name="running-state"></a>"Estado"em execução
Pertença a um domínio gerido, que está configurado corretamente e operacionais regularmente os **em execução** estado.

**O que esperar**
* Microsoft regularmente pode monitorizar o estado de funcionamento do seu domínio gerido.
* Controladores de domínio para o seu domínio gerido estão corrigidos e atualizados regularmente.
* Alterações do Azure Active Directory regularmente são sincronizadas com o seu domínio gerido.
* Cópias de segurança regulares direcionadas para o seu domínio gerido.


### <a name="needs-attention-state"></a>"Needs Attention" Estado
Um domínio gerido está na **requer atenção** se um ou mais problemas exigem que um administrador que a ação de estado. A página de estado de funcionamento do seu domínio gerido apresenta uma lista de um ou mais alertas neste estado.

Por exemplo, se tiver configurado um NSG restritivo da rede virtual, Microsoft poderá não conseguir atualizar e monitorizar o seu domínio gerido. Esta configuração inválida aciona um alerta que coloca o seu domínio gerido para o estado de "Requer atenção".

Cada alerta tem um conjunto de passos de resolução. Alguns alertas são passageiros e são resolvidas automaticamente pelo serviço. Pode resolver alguns outras alertas ao seguir as instruções nos passos de resolução correspondente para esse alerta. Para alguns alertas críticos, terá de contactar o suporte da Microsoft para obter uma resolução.

Para obter mais informações, consulte [como resolver problemas de alertas num domínio gerido](troubleshoot-alerts.md).

**O que esperar**

Em alguns casos (por exemplo, se tiver uma configuração de rede inválido), os controladores de domínio para o seu domínio gerido poderão estar inacessíveis. Quando o seu domínio gerido está no estado "Requer atenção", a Microsoft não garante que ele irá ser monitorizado, corrigido, atualizou ou Backup regularmente.

* O domínio gerido está em mau estado de funcionamento e monitorização de estado de funcionamento em curso poderá parar até que o alerta seja resolvido.
* Controladores de domínio para o seu domínio gerido não podem ser corrigidos ou atualizados.
* Alterações do Azure Active Directory não podem ser sincronizadas com o seu domínio gerido.
* As cópias de segurança para o seu domínio gerido poderão ficar, se possível.
* Se resolver alertas que estejam a afetar o seu domínio gerido, poderá restaurá-lo para o estado "Em execução".
* Alertas críticos são acionados para os problemas de configuração em que a Microsoft é não é possível alcançar os controladores de domínio. Se tais alertas não são resolvidos no prazo de 15 dias, o seu domínio gerido é colocado no estado "Suspenso".


### <a name="the-suspended-state"></a>O estado "Suspenso"
Um domínio gerido é colocado **suspenso** estado pelos seguintes motivos:

* Um ou mais alertas críticos ainda não foi resolvidos em 15 dias. Alertas críticos podem ser causados por uma configuração incorreta que bloqueia o acesso aos recursos necessários para o Azure AD DS.
    * Por exemplo, o alerta [AADDS104: Erro de rede](alert-nsg.md) tem sido não resolvidos por mais de 15 dias no domínio gerido.
* Existe um problema de faturação com a sua subscrição do Azure ou a sua subscrição do Azure expirou.

Domínios geridos são suspensas quando a Microsoft não consegue gerir, monitorizar, aplicar um patch ou criar cópias de segurança do domínio de forma contínua.

**O que esperar**
* Controladores de domínio para o seu domínio gerido são desconfiguradas e não estão acessíveis dentro da rede virtual.
* Acesso de Secure LDAP para o domínio gerido através da internet (se estiver ativada) deixa de funcionar.
* Observe falhas na autenticação para o domínio gerido, fazendo logon em máquinas de virtuais associados a um domínio ou ligar através de LDAP/LDAPS.
* As cópias de segurança para o seu domínio gerido já não são feitas.
* Interrompe a sincronização com o Azure AD.

Depois de resolver o alerta, o seu domínio gerido vai para o estado "Suspenso". Em seguida, terá de contactar o suporte.
Suporte poderá restaurar o seu domínio gerido, mas existe apenas se uma cópia de segurança que é inferior a 30 dias de antiguidade.

Apenas o domínio gerido permanece num estado suspenso durante 15 dias. Para recuperar o seu domínio gerido, a Microsoft recomenda que resolva alertas críticos imediatamente.


### <a name="deleted-state"></a>Estado "Eliminado"
É um domínio gerido, que permanece no estado "Suspenso" durante 15 dias **Deleted**.

**O que esperar**
* Todas as cópias de segurança para o domínio gerido e de recursos são eliminadas.
* Não é possível restaurar o domínio gerido e tem de criar um novo domínio gerido para utilizar o Azure AD DS.
* Depois de eliminado, não são faturadas para o domínio gerido.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Como sabe se o seu domínio gerido está suspenso?
Verá uma [alerta](troubleshoot-alerts.md) na página de estado de funcionamento do Azure AD DS no portal do Azure, que declara que o domínio está suspensa. O estado do domínio também mostra "Suspenso".


## <a name="restore-a-suspended-domain"></a>Restaurar um domínio suspenso
Para restaurar um domínio que está no estado "Suspenso", siga os passos seguintes:

1. Vá para o [página do Azure Active Directory Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure.
2. Selecione o domínio gerido.
3. No painel esquerdo, selecione **estado de funcionamento**.
4. Selecione o alerta. O ID do alerta será AADDS503 ou AADDS504, dependendo da causa de suspensão.
5. Selecione a ligação de resolução que é fornecida no alerta. Em seguida, siga os passos para resolver o alerta.

Só pode ser restaurado o seu domínio gerido para a data da última cópia de segurança. A data da última cópia de segurança é apresentada na página de estado de funcionamento do seu domínio gerido. Quaisquer alterações que ocorreram após a não ser restaurada a última cópia de segurança. As cópias de segurança para um domínio gerido são armazenadas durante 30 dias. Cópias de segurança que têm mais de 30 dias são eliminadas.


## <a name="next-steps"></a>Passos Seguintes
- [Resolver alertas para o seu domínio gerido](troubleshoot-alerts.md)
- [Leia mais sobre o Azure Active Directory Domain Services](overview.md)
- [Contacte a equipa de produto](contact-us.md)

## <a name="contact-us"></a>Contacte-nos
Contacte a equipa de produto do Azure Active Directory Domain Services para [partilhar comentários ou para o suporte](contact-us.md).
