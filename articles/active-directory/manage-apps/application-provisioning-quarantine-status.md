---
title: Status de provisionamento de aplicativo de quarentena | Microsoft Docs
description: Quando você configurou um aplicativo para provisionamento automático de usuário, saiba o que é um status de provisionamento de quarentena e como limpá-lo.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3ad689fb57c51d0deb698a723b93e6175bdbb5c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73882885"
---
# <a name="application-provisioning-in-quarantine-status"></a>Provisionamento de aplicativo no status de quarentena

O serviço de provisionamento do Azure AD monitora a integridade de sua configuração e coloca aplicativos não íntegros em um estado de "quarentena". Se a maioria ou todas as chamadas feitas no sistema de destino falharem consistentemente devido a um erro, por exemplo, credenciais de administrador inválidas, o trabalho de provisionamento será marcado como em quarentena.

Durante a quarentena, a frequência de ciclos incrementais é reduzida gradualmente para uma vez por dia. O trabalho de provisionamento é removido da quarentena depois que todos os erros são corrigidos e o próximo ciclo de sincronização é iniciado. Se o trabalho de provisionamento permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento será desabilitado (interromperá a execução).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Como fazer saber se meu aplicativo está em quarentena?

Há três maneiras de verificar se um aplicativo está em quarentena:
  
- Na portal do Azure, navegue até **Azure Active Directory** > **aplicativos empresariais** > &lt;*nome do aplicativo*&gt; > **provisionamento** e role até a barra de progresso na parte inferior.  

  ![Barra de status de provisionamento mostrando o status de quarentena](media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Use a solicitação de Microsoft Graph [obter synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) para obter programaticamente o status do trabalho de provisionamento:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Verifique seu email. Quando um aplicativo é colocado em quarentena, um email de notificação de uso único é enviado. Se o motivo da quarentena for alterado, um email atualizado será enviado mostrando o novo motivo para quarentena. Se você não vir um email:

  - Verifique se você especificou um **email de notificação** válido na configuração de provisionamento para o aplicativo.
  - Verifique se não há nenhuma filtragem de spam na caixa de entrada de email de notificação.
  - Certifique-se de que você não cancelou a assinatura dos emails.

## <a name="why-is-my-application-in-quarantine"></a>Por que meu aplicativo está em quarentena?

Uma solicitação de Microsoft Graph para obter o status do trabalho de provisionamento mostra o seguinte motivo para quarentena:

- `EncounteredQuarantineException` indica que foram fornecidas credenciais inválidas. O serviço de provisionamento não pode estabelecer uma conexão entre o sistema de origem e o sistema de destino.

- `EncounteredEscrowProportionThreshold` indica que o provisionamento excedeu o limite de caução. Essa condição ocorre quando mais de 60% dos eventos de provisionamento falharam.

- `QuarantineOnDemand` significa que detectamos um problema com seu aplicativo e o definimos manualmente como quarentena.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Como fazer obter meu aplicativo fora de quarentena?

Primeiro, resolva o problema que fez com que o aplicativo fosse colocado em quarentena.

- Verifique as configurações de provisionamento do aplicativo para certificar-se de que você [inseriu credenciais de administrador válidas](configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). O Azure AD deve ser capaz de estabelecer uma relação de confiança com o aplicativo de destino. Verifique se você inseriu credenciais válidas e se sua conta tem as permissões necessárias.

- Examine os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para investigar melhor quais erros estão causando a quarentena e resolver o erro. Acesse os logs de provisionamento no portal do Azure acessando **Azure Active Directory** &gt; **aplicativos empresariais** &gt; **os logs de provisionamento (versão prévia)** na seção **atividade** .

Depois de resolver o problema, reinicie o trabalho de provisionamento. Determinadas alterações nas configurações de provisionamento do aplicativo, como mapeamentos de atributo ou filtros de escopo, reiniciarão automaticamente o provisionamento para você. A barra de progresso na página de **provisionamento** do aplicativo indica quando o provisionamento foi iniciado pela última vez. Se você precisar reiniciar o trabalho de provisionamento manualmente, use um dos seguintes métodos:  

- Use o portal do Azure para reiniciar o trabalho de provisionamento. Na página de **provisionamento** do aplicativo em **configurações**, selecione **limpar estado e reiniciar sincronização** e defina o **status de provisionamento** como **ativado**. Essa ação reinicia completamente o serviço de provisionamento, o que pode levar algum tempo. Um ciclo inicial completo será executado novamente, o que limpa as caução, remove o aplicativo da quarentena e limpa as marcas d' água.

- Use Microsoft Graph para [reiniciar o trabalho de provisionamento](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Você terá controle total sobre o que reinicia. Você pode optar por limpar as caução (para reiniciar o contador de caução que se acumula para o status de quarentena), limpar a quarentena (para remover o aplicativo da quarentena) ou limpar as marcas d' água. Use a seguinte solicitação:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`