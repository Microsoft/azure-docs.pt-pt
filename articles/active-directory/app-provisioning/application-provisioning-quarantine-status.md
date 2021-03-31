---
title: Estatuto de Provisionamento de | de Quarentena Microsoft Docs
description: Quando tiver configurado um pedido de provisionamento automático do utilizador, saiba o que significa um estado de provisionamento de Quarentena e como limpá-lo.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 49590c46588ad0d0f1c1b7b095679a3c3fce96eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579506"
---
# <a name="application-provisioning-in-quarantine-status"></a>Provisionamento de pedidos em estado de quarentena

O serviço de fornecimento AZure AD monitoriza a saúde da sua configuração. Também coloca aplicativos insalubres em estado de "quarentena". Se a maioria, ou todas, das chamadas feitas contra o sistema-alvo falharem consistentemente, então o trabalho de provisionamento está marcado como em quarentena. Um exemplo de falha é um erro recebido devido a credenciais de administração inválidas.

Enquanto estava em quarentena:
- A frequência dos ciclos incrementais é gradualmente reduzida para uma vez por dia.
- O trabalho de provisionamento é removido da quarentena depois de todos os erros serem corrigidos e o ciclo de sincronização seguinte começar. 
- Se o trabalho de provisionamento permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento é deficiente (deixa de funcionar).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Como sei se a minha candidatura está em quarentena?

Há três formas de verificar se um pedido está em quarentena:
  
- No portal Azure, navegue para a **Azure Ative Directory**  >  **Enterprise applications**  >  &lt; *applications Name* &gt;  >  **Provisioning** and review the progress bar for a quarantine message.   

  ![Barra de estado de provisionamento que mostra o estado de quarentena](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- No portal Azure, navegue para **Azure Ative Directory**  >  **Audit Logs** > filtro on **Activity: Quarantine** e rever o histórico de quarentena. A visão na barra de progresso acima descrita mostra se o provisionamento está atualmente em quarentena. Os registos de auditoria mostram o histórico de quarentena para uma aplicação. 

- Utilize o pedido de Gráfico microsoft [Obter sincronizaçãoJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta&preserve-view=true) para obter programáticamente o estado do trabalho de provisionamento:

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- Verifique o seu e-mail. Quando um pedido é colocado em quarentena, um e-mail de notificação único é enviado. Se a razão de quarentena mudar, é enviado um e-mail atualizado mostrando a nova razão para a quarentena. Se não vir um e-mail:

  - Certifique-se de que especificou um **e-mail** de notificação válido na configuração de provisionamento para a aplicação.
  - Certifique-se de que não existe filtragem de spam na caixa de entrada de e-mail de notificação.
  - Certifique-se de que não foi subscrita de e-mails.
  - Verifique se há e-mails `azure-noreply@microsoft.com`

## <a name="why-is-my-application-in-quarantine"></a>Porque é que o meu pedido está em quarentena?

|Description|Ação Recomendada|
|---|---|
|**Problema de conformidade SCIM:** Foi devolvida uma resposta HTTP/404 Não Encontrada em vez da resposta HTTP/200 OK esperada. Neste caso, o serviço de fornecimento de Ad Azure fez um pedido ao pedido-alvo e recebeu uma resposta inesperada.|Consulte a secção de credenciais de administração. Veja se a aplicação requer especificar o URL do inquilino e que o URL está correto. Se não vir um problema, contacte o desenvolvedor da aplicação para garantir que o seu serviço está em conformidade com o SCIM. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Credenciais inválidas:** Ao tentar autorizar o acesso ao pedido de destino, recebemos uma resposta da aplicação-alvo que indica que as credenciais fornecidas são inválidas.|Navegue para a secção de credenciais de administração da configuração de provisionamento UI e autorize o acesso novamente com credenciais válidas. Se a aplicação estiver na galeria, reveja o tutorial de configuração da aplicação para mais passos necessários.|
|**Funções duplicadas:** As funções importadas de determinadas aplicações como a Salesforce e a Zendesk devem ser únicas. |Navegue para o [manifesto](../develop/reference-app-manifest.md) de aplicação no portal Azure e retire a função duplicada.|

 Um pedido do Microsoft Graph para obter o estatuto do trabalho de provisionamento mostra a seguinte razão para a quarentena:
- `EncounteredQuarantineException` indica que foram fornecidas credenciais inválidas. O serviço de prestação de serviços não é capaz de estabelecer uma ligação entre o sistema de origem e o sistema-alvo.
- `EncounteredEscrowProportionThreshold` indica que o provisionamento excedeu o limiar de depósito. Esta condição ocorre quando mais de 40% dos eventos de provisionamento falharam. Para obter mais informações, consulte os detalhes do limiar de depósito abaixo.
- `QuarantineOnDemand` significa que detetamos um problema com a sua aplicação e a definimos manualmente para a quarentena.

**Limiares de caução**

Se o limiar de caução proporcional for cumprido, o trabalho de provisionamento entrará em quarentena. Esta lógica está sujeita a alterações, mas funciona aproximadamente como descrito abaixo: 

Um trabalho pode entrar em quarentena independentemente da falta de falhas conta para questões como credenciais de administração ou conformidade com o SCIM. No entanto, em geral, 5.000 falhas são o mínimo para começar a avaliar se deve ou não colocar a quarentena devido a demasiadas falhas. Por exemplo, um trabalho com 4.000 falhanços não entraria em quarentena. Mas um trabalho com 5.000 falhas desencadearia uma avaliação. Uma avaliação utiliza os seguintes critérios:  
- Se mais de 40% dos eventos de provisionamento falharem, ou se houver mais de 40.000 falhas, o trabalho de provisionamento entrará em quarentena. As falhas de referência não serão contabilizadas como parte do limiar de 40% ou do limiar de 40.000. Por exemplo, a não atualização de um gestor ou de um membro do grupo é uma falha de referência.
- Um trabalho em que 45.000 utilizadores foram a provisionados sem sucesso levaria à quarentena, uma vez que excede o limiar de 40.000.
- Um trabalho em que 30.000 utilizadores falharam o fornecimento e 5.000 foram bem sucedidos levaria à quarentena, uma vez que excede o limiar de 40% e o mínimo de 5.000.
- Um trabalho com 20.000 falhas e 100.000 de sucesso não entraria em quarentena porque não excede o limiar de 40% de falha ou o máximo de 40.000 falhanços.  
- Há um limiar absoluto de 60.000 falhas que explicam falhas de referência e não-referência. Por exemplo, 40.000 utilizadores não foram a provisionados e 21.000 atualizações de gestores falharam. O total é de 61.000 falhas e ultrapassa o limite de 60.000.


## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Como consigo tirar a minha candidatura da quarentena?

Primeiro, resolva a questão que fez com que o pedido fosse colocado em quarentena.

- Verifique as definições de provisionamento da aplicação para se certificar de que [introduziu credenciais de administrador válidas](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). A Azure AD deve estabelecer uma confiança com a aplicação-alvo. Certifique-se de que introduziu credenciais válidas e que a sua conta tem as permissões necessárias.

- Reveja os [registos de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para investigar melhor os erros que estão a causar quarentena e resolver o erro. Aceda a **Azure Ative Directory** &gt; **Enterprise Apps** &gt; **Provisioning registos (pré-visualização)** na secção **Atividade.**

Depois de resolver o problema, reinicie o trabalho de provisão. Algumas alterações nas definições de provisionamento da aplicação, tais como mapeamentos de atributos ou filtros de deteção, reiniciarão automaticamente o provisionamento para si. A barra de progresso na página de **Provisionamento** da aplicação indica quando o provisionamento começou pela última vez. Se precisar de reiniciar manualmente o trabalho de fornecimento, utilize um dos seguintes métodos:  

- Utilize o portal Azure para reiniciar o trabalho de provisionamento. Na página de **Provisionamento** da aplicação, selecione **Reatamento**. Esta ação reinicia totalmente o serviço de prestação de serviços, o que pode demorar algum tempo. Um ciclo inicial completo será executado novamente, o que limpa as cauções, remove a aplicação da quarentena e limpa quaisquer marcas de água. O serviço irá então avaliar todos os utilizadores do sistema de origem novamente e determinar se eles estão em possibilidade de provisão. Isto pode ser útil quando a sua aplicação está em quarentena, como este artigo discute, ou precisa de fazer uma alteração nos mapeamentos do seu atributo. Note que o ciclo inicial demora mais tempo a ser concluído do que o ciclo incremental típico devido ao número de objetos que precisam de ser avaliados. Pode aprender mais sobre o desempenho dos ciclos iniciais e incrementais [aqui.](application-provisioning-when-will-provisioning-finish-specific-user.md)

- Utilize o Microsoft Graph para [reiniciar o trabalho de provisionamento](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). Terá controlo total sobre o que reinicia. Pode optar por limpar as cauções (reiniciar o contador de cauções que se acumula para o estado de quarentena), limpar a quarentena (para remover a aplicação da quarentena) ou marcas de água límpias. utilize o seguinte pedido:
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

Substitua "{ID}" pelo valor do ID da aplicação e substitua "{jobId}" pelo [ID do trabalho de sincronização](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta&preserve-view=true#list-synchronization-jobs-in-the-context-of-the-service-principal).
