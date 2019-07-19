---
title: 'Azure Active Directory Domain Services: Domínios suspensos | Microsoft Docs'
description: Suspensão e exclusão de domínio gerenciado
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 781a81589032c290cef7342e7210ee36f388b22a
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233990"
---
# <a name="understand-the-suspended-states-for-an-azure-active-directory-domain-services-managed-domain"></a>Entender os Estados suspensos para um Azure Active Directory Domain Services domínio gerenciado

Quando Azure Active Directory Domain Services (Azure AD DS) não é capaz de atender a um domínio gerenciado por um longo período de tempo, ele coloca o domínio gerenciado em um estado suspenso. Este artigo explica por que os domínios gerenciados são suspensos e como corrigir um domínio suspenso.


## <a name="states-your-managed-domain-can-be-in"></a>Estados em que seu domínio gerenciado pode estar

![Linha do tempo de domínio suspenso](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

O gráfico anterior descreve os possíveis Estados em que um domínio gerenciado do Azure AD DS pode estar.

### <a name="running-state"></a>Estado "em execução"
Um domínio gerenciado que está configurado corretamente e operando regularmente está no estado **executando** .

**O que esperar**
* A Microsoft pode monitorar regularmente a integridade do seu domínio gerenciado.
* Os controladores de domínio para seu domínio gerenciado são corrigidos e atualizados regularmente.
* As alterações de Azure Active Directory são sincronizadas regularmente com seu domínio gerenciado.
* Backups regulares são feitos para seu domínio gerenciado.


### <a name="needs-attention-state"></a>Estado "requer atenção"
Um domínio gerenciado está no estado **precisa de atenção** se um ou mais problemas exigirem que um administrador tome uma providência. A página de integridade do seu domínio gerenciado lista um ou mais alertas neste estado.

Por exemplo, se você tiver configurado um NSG restritivo para sua rede virtual, a Microsoft talvez não consiga atualizar e monitorar seu domínio gerenciado. Essa configuração inválida dispara um alerta que coloca seu domínio gerenciado no estado "requer atenção".

Cada alerta tem um conjunto de etapas de resolução. Alguns alertas são transitórios e são resolvidos automaticamente pelo serviço. Você pode resolver alguns outros alertas seguindo as instruções nas etapas de resolução correspondentes para esse alerta. Para alguns alertas críticos, você precisa contatar o suporte da Microsoft para obter uma resolução.

Para obter mais informações, consulte [como solucionar problemas de alertas em um domínio gerenciado](troubleshoot-alerts.md).

**O que esperar**

Em alguns casos (por exemplo, se você tiver uma configuração de rede inválida), os controladores de domínio para seu domínio gerenciado podem estar inacessíveis. Quando seu domínio gerenciado está no estado "requer atenção", a Microsoft não pode garantir que ele será monitorado, corrigido, atualizado ou submetido a backup regularmente.

* Seu domínio gerenciado está em um estado não íntegro e o monitoramento de integridade contínuo pode parar até que o alerta seja resolvido.
* Os controladores de domínio para seu domínio gerenciado não podem ser corrigidos ou atualizados.
* As alterações de Azure Active Directory podem não ser sincronizadas com o domínio gerenciado.
* Os backups para seu domínio gerenciado podem ser obtidos, se possível.
* Se você resolver alertas que estão afetando seu domínio gerenciado, talvez seja possível restaurá-los para o estado "em execução".
* Alertas críticos são disparados para problemas de configuração em que a Microsoft não consegue acessar seus controladores de domínio. Se esses alertas não forem resolvidos em 15 dias, o domínio gerenciado será colocado no estado "suspenso".


### <a name="the-suspended-state"></a>O estado "suspenso"
Um domínio gerenciado é colocado no estado **suspenso** pelos seguintes motivos:

* Um ou mais alertas críticos não foram resolvidos em 15 dias. Alertas críticos podem ser causados por uma configuração incorreta que bloqueia o acesso a recursos que são necessários para o Azure AD DS.
    * Por exemplo, o alerta [AADDS104: O erro](alert-nsg.md) de rede não foi resolvido por mais de 15 dias no domínio gerenciado.
* Há um problema de cobrança com sua assinatura do Azure ou sua assinatura do Azure expirou.

Os domínios gerenciados são suspensos quando a Microsoft não consegue gerenciar, monitorar, aplicar patch ou fazer backup do domínio em uma base contínua.

**O que esperar**
* Os controladores de domínio para seu domínio gerenciado são desprovisionados e não podem ser acessados dentro da rede virtual.
* LDAP Seguro acesso ao domínio gerenciado pela Internet (se estiver habilitado) para de funcionar.
* Você percebe falhas na autenticação do domínio gerenciado, no logon em máquinas virtuais ingressadas no domínio ou na conexão por LDAP/LDAPs.
* Os backups de seu domínio gerenciado não são mais utilizados.
* A sincronização com o Azure AD é interrompida.

Depois de resolver o alerta, o domínio gerenciado entra no estado "suspenso". Em seguida, você precisa entrar em contato com o suporte.
O suporte pode restaurar seu domínio gerenciado, mas somente se um backup com menos de 30 dias existir.

O domínio gerenciado permanece apenas em um estado suspenso por 15 dias. Para recuperar o domínio gerenciado, a Microsoft recomenda que você resolva alertas críticos imediatamente.


### <a name="deleted-state"></a>Estado "excluído"
Um domínio gerenciado que permanece no estado "suspenso" por 15 dias é **excluído**.

**O que esperar**
* Todos os recursos e backups para o domínio gerenciado são excluídos.
* Você não pode restaurar o domínio gerenciado e precisa criar um novo domínio gerenciado para usar o Azure AD DS.
* Depois de excluída, você não será cobrado pelo domínio gerenciado.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Como saber se o domínio gerenciado está suspenso?
Você verá um [alerta](troubleshoot-alerts.md) na página do Azure AD DS Health no portal do Azure que declara que o domínio está suspenso. O estado do domínio também mostra "suspenso".


## <a name="restore-a-suspended-domain"></a>Restaurar um domínio suspenso
Para restaurar um domínio que está no estado "suspenso", execute as seguintes etapas:

1. Vá para a [página de Azure Active Directory Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure.
2. Selecione o domínio gerenciado.
3. No painel esquerdo, selecione **integridade**.
4. Selecione o alerta. A ID do alerta será AADDS503 ou AADDS504, dependendo da causa da suspensão.
5. Selecione o link de resolução fornecido no alerta. Em seguida, siga as etapas para resolver o alerta.

Seu domínio gerenciado só pode ser restaurado até a data do último backup. A data do último backup é exibida na página integridade do seu domínio gerenciado. Quaisquer alterações ocorridas após o último backup não serão restauradas. Os backups de um domínio gerenciado são armazenados por até 30 dias. Os backups com mais de 30 dias são excluídos.


## <a name="next-steps"></a>Passos seguintes
- [Resolver alertas para seu domínio gerenciado](troubleshoot-alerts.md)
- [Leia mais sobre Azure Active Directory Domain Services](overview.md)
- [Contate a equipe do produto](contact-us.md)

## <a name="contact-us"></a>Contacte-nos
Entre em contato com a equipe de produto Azure Active Directory Domain Services para [compartilhar comentários ou para obter suporte](contact-us.md).
