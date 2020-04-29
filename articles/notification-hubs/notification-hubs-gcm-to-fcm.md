---
title: Hubs de notificação Azure e a migração de Mensagens Cloud (FCM) da Base de Fogo do Google
description: Descreve como o Azure Notification Hubs aborda o Google GCM à migração da FCM.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80127029"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Hubs de notificação Azure e migração de mensagens cloud da base de incêndio do Google

## <a name="current-state"></a>Estado atual

Quando a Google anunciou a sua migração do Google Cloud Messaging (GCM) para o Firebase Cloud Messaging (FCM), serviços de push como o nosso tiveram de ajustar a forma como enviamos notificações para dispositivos Android para acomodar a mudança.

Atualizámos o nosso backend de serviço, depois publicámos atualizações para os nossos API e SDKs, se necessário. Com a nossa implementação, tomámos a decisão de manter a compatibilidade com os schemas de notificação gcm existentes para minimizar o impacto do cliente. Isto significa que atualmente enviamos notificações para dispositivos Android usando fcm no Modo Legado FCM. Em última análise, queremos adicionar um verdadeiro suporte à FCM, incluindo as novas funcionalidades e formato de carga útil. Trata-se de uma mudança a longo prazo e a migração atual centra-se na manutenção da compatibilidade com as aplicações existentes e os SDKs. Pode utilizar os SDKs GCM ou FCM na sua aplicação (juntamente com o nosso SDK) e certificamo-nos de que a notificação é enviada corretamente.

Alguns clientes receberam recentemente um e-mail da Google alertando sobre aplicações usando um ponto final GCM para notificações. Este foi apenas um aviso, e nada está quebrado – as notificações do Android da sua aplicação ainda são enviadas para a Google para processamento e a Google ainda as processa. Alguns clientes que especificaram explicitamente o ponto final do GCM na sua configuração de serviço ainda usavam o ponto final depreciado. Já tínhamos identificado esta lacuna e estávamos a trabalhar na resolução do problema quando a Google enviou o e-mail.

Substituímos o ponto final depreciado e a correção está implantada.

## <a name="going-forward"></a>Indo para a frente

A FAQ da FCM da Google diz que não tens de fazer nada. Na [FCM FAQ,](https://developers.google.com/cloud-messaging/faq)a Google disse que "os SDKs de clientes e as fichas gcm continuarão a funcionar indefinidamente. No entanto, não poderá direcionar a versão mais recente do Google Play Services na sua aplicação Android, a menos que emigra para a FCM."

Se a sua aplicação utilizar a biblioteca GCM, siga as instruções da Google para fazer o upgrade para a biblioteca da FCM na sua aplicação. O nosso SDK é compatível com qualquer um deles, pelo que não terá de atualizar nada na sua aplicação do nosso lado (desde que esteja atualizado com a nossa versão SDK).

## <a name="questions-and-answers"></a>Perguntas e respostas

Aqui estão algumas respostas a perguntas comuns que ouvimos dos clientes:

**Q:** O que preciso fazer para ser compatível até à data limite (a data limite atual da Google é 29 de maio e pode mudar)?

**A:** Nada, nada. Manteremos a compatibilidade com o esquema de notificação gcm existente. A sua chave GCM continuará a funcionar normalmente como qualquer GCM SDKs e bibliotecas utilizadas pela sua aplicação.

Se/quando decidir fazer upgrade para os SDKs e bibliotecas da FCM para tirar partido de novas funcionalidades, a sua chave GCM continuará a funcionar. Pode mudar para a utilização de uma chave FCM, se desejar, mas certifique-se de que está a adicionar base de fogo ao seu projeto GCM existente ao criar o novo projeto Firebase. Isto garantirá a retrocompatibilidade com os seus clientes que estão a executar versões mais antigas da app que ainda utilizam SDKs gcm e bibliotecas.

Se estiver a criar um novo projeto FCM e não se ligar ao projeto GCM existente, uma vez que atualiza os Centros de Notificação com o novo segredo da FCM, perderá a capacidade de empurrar notificações para as instalações atuais da aplicação, uma vez que a nova chave FCM não tem qualquer ligação com o antigo projeto GCM.

**Q:** Porque é que estou a receber este e-mail sobre os antigos pontos finais da GCM a serem usados? O que tenho que fazer?

**A:** Nada, nada. Temos vindo a migrar para os novos pontos finais e estaremos concluídos em breve, pelo que não é necessária qualquer alteração. Nada está partido, o nosso único ponto final perdido simplesmente causou mensagens de aviso do Google.

**Q:** Como posso transitar para os novos SDKs e bibliotecas da FCM sem quebrar os utilizadores existentes?

A: Upgrade a qualquer momento. A Google ainda não anunciou qualquer depreciação dos SDKs e bibliotecas gcm existentes. Para garantir que não quebra notificações push aos utilizadores existentes, certifique-se de que quando criar o novo projeto Firebase está a associar-se ao seu projeto GCM existente. Isto irá garantir que novos segredos da Firebase funcionarão para os utilizadores que executam as versões mais antigas da sua aplicação com SDKs e bibliotecas GCM, bem como novos utilizadores da sua aplicação com SDKs e bibliotecas FCM.

**Q:** Quando posso usar novas funcionalidades da FCM e schemas para as minhas notificações?

**A:** Assim que publicarmos uma atualização para os nossos API e SDKs, fique atento – esperamos ter algo para si nos próximos meses.
