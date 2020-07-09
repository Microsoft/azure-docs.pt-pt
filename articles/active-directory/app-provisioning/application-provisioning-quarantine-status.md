---
title: Estatuto de Provisionamento de Aplicação de Quarentena / Microsoft Docs
description: Quando tiver configurado um pedido de provisionamento automático do utilizador, saiba o que significa um estado de provisionamento de Quarentena e como limpá-lo.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/28/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: e5c0b00873cd97b255eff7e001f8b54cf0397462
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024575"
---
# <a name="application-provisioning-in-quarantine-status"></a>Provisionamento de pedidos em estado de quarentena

O serviço de fornecimento de Azure AD monitoriza a saúde da sua configuração e coloca aplicações pouco saudáveis num estado de "quarentena". Se a maioria ou todas as chamadas feitas contra o sistema-alvo falharem consistentemente devido a um erro, por exemplo, credenciais de administração inválidas, o trabalho de provisionamento é marcado como em quarentena.

Enquanto estiver em quarentena, a frequência dos ciclos incrementais é gradualmente reduzida para uma vez por dia. O trabalho de provisionamento é removido da quarentena depois de todos os erros serem corrigidos e o ciclo de sincronização seguinte começar. Se o trabalho de provisionamento permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento é deficiente (deixa de funcionar).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Como sei se a minha candidatura está em quarentena?

Há três formas de verificar se um pedido está em quarentena:
  
- No portal Azure, navegue para a **Azure Ative Directory**  >  **Enterprise applications**  >  &lt; *applications Name* &gt;  >  **Provisioning** and review the progress bar for a quarantine message.   

  ![Barra de estado de provisionamento que mostra o estado de quarentena](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- No portal Azure, navegue para **Azure Ative Directory**  >  **Audit Logs** > filtro on **Activity: Quarantine** e rever o histórico de quarentena. Embora a vista na barra de progresso, tal como acima descrita, mostre se o provisionamento está atualmente em quarentena, os registos de auditoria permitem-lhe ver o histórico de quarentena para uma aplicação. 

- Utilize o pedido de Gráfico microsoft [Obter sincronizaçãoJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) para obter programáticamente o estado do trabalho de provisionamento:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Verifique o seu e-mail. Quando um pedido é colocado em quarentena, um e-mail de notificação único é enviado. Se a razão de quarentena mudar, é enviado um e-mail atualizado mostrando a nova razão para a quarentena. Se não vir um e-mail:

  - Certifique-se de que especificou um **Email de Notificação** válido na configuração de provisionamento para a aplicação.
  - Certifique-se de que não existe filtragem de spam na caixa de entrada de e-mail de notificação.
  - Certifique-se de que não foi subscrita de e-mails.

## <a name="why-is-my-application-in-quarantine"></a>Porque é que o meu pedido está em quarentena?

|Descrição|Ação Recomendada|
|---|---|
|**Problema de conformidade SCIM:** Foi devolvida uma resposta HTTP/404 Não Encontrada em vez da resposta HTTP/200 OK esperada. Neste caso, o serviço de fornecimento de Ad Azure fez um pedido ao pedido-alvo e recebeu uma resposta inesperada.|Consulte a secção de credenciais de administração para ver se o pedido requer especificar o URL do inquilino e certifique-se de que o URL está correto. Se não vir um problema, contacte o desenvolvedor da aplicação para garantir que o seu serviço está em conformidade com o SCIM. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Credenciais inválidas:** Ao tentar autorizar o acesso ao pedido de destino, recebemos uma resposta da aplicação-alvo que indica que as credenciais fornecidas são inválidas.|Navegue para a secção de credenciais de administração da configuração de provisionamento UI e autorize o acesso novamente com credenciais válidas. Se a aplicação estiver na galeria, reveja o tutorial de configuração da aplicação para quaisquer etapas adicionais necessárias.|
|**Funções duplicadas:** As funções importadas de determinadas aplicações como a Salesforce e a Zendesk devem ser únicas. |Navegue para o [manifesto](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) de aplicação no portal Azure e retire a função duplicada.|

 Um pedido do Microsoft Graph para obter o estatuto do trabalho de provisionamento mostra a seguinte razão para a quarentena:

- `EncounteredQuarantineException`indica que foram fornecidas credenciais inválidas. O serviço de prestação de serviços não é capaz de estabelecer uma ligação entre o sistema de origem e o sistema-alvo.

- `EncounteredEscrowProportionThreshold`indica que o provisionamento excedeu o limiar de depósito. Esta condição ocorre quando mais de 60% dos eventos de provisionamento falharam.

- `QuarantineOnDemand`significa que detetamos um problema com a sua aplicação e a definimos manualmente para a quarentena.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Como consigo tirar a minha candidatura da quarentena?

Primeiro, resolva a questão que fez com que o pedido fosse colocado em quarentena.

- Verifique as definições de provisionamento da aplicação para se certificar de que [introduziu credenciais de administrador válidas](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). A Azure AD deve ser capaz de estabelecer uma confiança com a aplicação-alvo. Certifique-se de que introduziu credenciais válidas e que a sua conta tem as permissões necessárias.

- Reveja os [registos de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para investigar melhor os erros que estão a causar quarentena e resolver o erro. Aceda aos registos de provisionamento no portal Azure indo para **Azure Ative Directory** &gt; **Enterprise Apps** &gt; **Provisioning registos (pré-visualização)** na secção **Atividade.**

Depois de resolver o problema, reinicie o trabalho de provisão. Algumas alterações nas definições de provisionamento da aplicação, tais como mapeamentos de atributos ou filtros de deteção, reiniciarão automaticamente o provisionamento para si. A barra de progresso na página de **Provisionamento** da aplicação indica quando o provisionamento começou pela última vez. Se precisar de reiniciar manualmente o trabalho de fornecimento, utilize um dos seguintes métodos:  

- Utilize o portal Azure para reiniciar o trabalho de provisionamento. Na página de **Provisionamento** da aplicação em **Definições**, selecione **Limpar o estado e reiniciar a sincronização** e definir o Estado de **Provisionamento** para **On**. Esta ação reinicia totalmente o serviço de prestação de serviços, o que pode demorar algum tempo. Um ciclo inicial completo será executado novamente, o que limpa as cauções, remove a aplicação da quarentena e limpa quaisquer marcas de água.

- Utilize o Microsoft Graph para [reiniciar o trabalho de provisionamento](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Terá controlo total sobre o que reinicia. Pode optar por limpar as cauções (reiniciar o contador de cauções que se acumula para o estado de quarentena), limpar a quarentena (para remover a aplicação da quarentena) ou marcas de água límpias. utilize o seguinte pedido:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
       
Substitua "{id}" pelo valor do ID da aplicação e substitua "{jobId}" pelo [ID do trabalho de sincronização](https://docs.microsoft.com/graph/api/resources/synchronization-configure-with-directory-extension-attributes?view=graph-rest-beta&tabs=http#list-synchronization-jobs-in-the-context-of-the-service-principal). 

