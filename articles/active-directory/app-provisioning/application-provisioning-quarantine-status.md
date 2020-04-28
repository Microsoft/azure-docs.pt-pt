---
title: Estatuto de Prestação de Candidaturas de Quarentena [ Microsoft Docs
description: Quando configurar uma aplicação para o fornecimento automático de utilizadores, saiba o que significa um estado de provisionamento de Quarentena e como limpá-lo.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563c049bf3d1606e87db54e3b003dac987594610
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154632"
---
# <a name="application-provisioning-in-quarantine-status"></a>Fornecimento de candidaturas em estado de quarentena

O serviço de provisionamento de AD Azure monitoriza a saúde da sua configuração e coloca aplicações pouco saudáveis num estado de "quarentena". Se a maioria ou todas as chamadas efetuadas contra o sistema alvo falharem constantemente devido a um erro, por exemplo, credenciais de administração inválidas, o trabalho de provisionamento é marcado como em quarentena.

Enquanto está em quarentena, a frequência dos ciclos incrementais é gradualmente reduzida para uma vez por dia. O trabalho de provisionamento é removido da quarentena depois de todos os erros serem corrigidos e o próximo ciclo de sincronização começar. Se o emprego de provisionamento permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento é desativado (deixa de funcionar).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Como sei se a minha candidatura está em quarentena?

Há três formas de verificar se um pedido está em quarentena:
  
- No portal Azure, navegue para **a Azure Ative Directory** > Enterprise aplica o nome&gt; > de > &lt;*aplicação***Provisioning** e percorra até à barra de progresso na parte inferior.**Enterprise applications**  

  ![Barra de estado de provisionamento que mostra o estado de quarentena](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- No portal Azure, navegue para **o Azure Ative Directory** > **Audit Logs** > filtro em **Atividade: Quarentena** e revisão do histórico de quarentena. Embora a visão na barra de progresso, tal como acima descrita, mostre se o provisionamento está atualmente em quarentena, os registos de auditoria permitem-lhe ver o histórico de quarentena para uma aplicação. 

- Utilize o pedido do Microsoft Graph [Obtenha sincronizaçãoJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) para obter programáticamente o estatuto do trabalho de provisionamento:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Verifique o seu e-mail. Quando um pedido é colocado em quarentena, é enviado um e-mail de notificação único. Se a razão da quarentena mudar, um e-mail atualizado é enviado mostrando a nova razão para a quarentena. Se não vir um e-mail:

  - Certifique-se de que especificou um email de **notificação** válido na configuração de fornecimento da aplicação.
  - Certifique-se de que não há filtragem de correio publicitário não solicitado na caixa de entrada de e-mail de notificação.
  - Certifique-se de que não foi dessubscrita dos e-mails.

## <a name="why-is-my-application-in-quarantine"></a>Por que a minha candidatura está em quarentena?

Um pedido do Microsoft Graph para obter o estatuto do trabalho de provisionamento mostra a seguinte razão para a quarentena:

- `EncounteredQuarantineException`indica que foram fornecidas credenciais inválidas. O serviço de provisionamento não pode estabelecer uma ligação entre o sistema de origem e o sistema-alvo.

- `EncounteredEscrowProportionThreshold`indica que a provisão excedeu o limiar de caução. Esta condição ocorre quando mais de 60% dos eventos de provisionamento falharam.

- `QuarantineOnDemand`significa que detetámos um problema com a tua aplicação e definimo-lo manualmente para a quarentena.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Como posso tirar a minha candidatura da quarentena?

Primeiro, resolva a questão que fez com que o pedido fosse colocado em quarentena.

- Verifique as definições de provisionamento da aplicação para se certificar de que [introduziu credenciais de administrador válidas](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). A Azure AD deve ser capaz de estabelecer um fundo com a aplicação-alvo. Certifique-se de que inseriu credenciais válidas e que a sua conta tem as permissões necessárias.

- Reveja os registos de [provisionamento](../reports-monitoring/concept-provisioning-logs.md) para investigar mais aprofundadamente quais os erros que estão a causar quarentena e resolver o erro. Aceda aos registos de provisionamento no portal Azure, indo para **o Azure Ative Directory** &gt; **Enterprise Apps** &gt; **Provisioning registos (pré-visualização)** na secção **Atividade.**

Depois de resolver o problema, reinicie o trabalho de provisionamento. Algumas alterações nas definições de provisionamento da aplicação, tais como mapeamentos de atributos ou filtros de deteção, reiniciarão automaticamente o fornecimento para si. A barra de progresso na página de **provisionamento** do pedido indica quando o fornecimento começou pela última vez. Se necessitar de reiniciar o trabalho de provisionamento manualmente, utilize um dos seguintes métodos:  

- Utilize o portal Azure para reiniciar o trabalho de provisionamento. Na página de **provisionamento** da aplicação em **Definições,** selecione **clear state e reinicie** a sincronização e desemque o Estado de **Provisionamento** para **On**. Esta ação reinicia totalmente o serviço de provisionamento, que pode demorar algum tempo. Um ciclo inicial completo voltará a funcionar, o que limpa os esquástis, remove a aplicação da quarentena e limpa quaisquer marcas de água.

- Utilize o Microsoft Graph para [reiniciar o trabalho de provisionamento](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Terá supor o que reiniciar. Pode optar por limpar os escrows (para reiniciar o contador de caução que se acumula em relação ao estado de quarentena), quarentena clara (para remover a aplicação da quarentena) ou marcas de água claras. utilize o seguinte pedido:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
