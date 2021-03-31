---
title: Azure Notification Hubs e a migração de Mensagens Cloud (FCM) do Google Firebase
description: Descreve como o Azure Notification Hubs dirige o Google GCM para a migração da FCM.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "80127029"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Hubs de notificação Azure e migração de mensagens cloud do Google Firebase

## <a name="current-state"></a>Estado atual

Quando a Google anunciou a sua migração do Google Cloud Messaging (GCM) para firebase Cloud Messaging (FCM), serviços de push como o nosso tiveram de ajustar a forma como enviámos notificações para dispositivos Android para acomodar a mudança.

Atualizámos o backend do nosso serviço e depois publicámos atualizações para as nossas API e SDKs, conforme necessário. Com a nossa implementação, tomámos a decisão de manter a compatibilidade com os esquemas de notificação GCM existentes para minimizar o impacto do cliente. Isto significa que atualmente enviamos notificações para dispositivos Android usando FCM em Modo Legado FCM. Em última análise, queremos adicionar um verdadeiro suporte à FCM, incluindo as novas funcionalidades e o formato de carga útil. Trata-se de uma mudança a longo prazo e a migração atual está focada em manter a compatibilidade com as aplicações e os ODS existentes. Pode utilizar os SDKs GCM ou FCM na sua aplicação (juntamente com o nosso SDK) e certificamo-nos de que a notificação é enviada corretamente.

Alguns clientes receberam recentemente um e-mail da Google alertando sobre aplicações usando um ponto final GCM para notificações. Isto foi apenas um aviso, e nada está quebrado – as notificações android da sua aplicação ainda são enviadas para o Google para processamento e a Google ainda processa as mesmas. Alguns clientes que especificaram explicitamente o ponto final do GCM na sua configuração de serviço ainda usavam o ponto final precotado. Já tínhamos identificado esta lacuna e estávamos a trabalhar na resolução do problema quando o Google enviou o e-mail.

Substituímos o ponto final prevadido e a correção é implantada.

## <a name="going-forward"></a>Seguir em frente

O FCM FAQ do Google diz que não tens de fazer nada. Nas [FAQ da FCM,](https://developers.google.com/cloud-messaging/faq)a Google disse que "os SDKs clientes e os tokens GCM continuarão a funcionar indefinidamente. No entanto, não será capaz de direcionar a versão mais recente dos Serviços Google Play na sua aplicação Android, a menos que migrar para a FCM."

Se a sua aplicação utilizar a biblioteca GCM, siga as instruções da Google para fazer o upgrade para a biblioteca FCM na sua aplicação. O nosso SDK é compatível com qualquer um dos dois, pelo que não terá de atualizar nada na sua app do nosso lado (desde que esteja atualizado com a nossa versão SDK).

## <a name="questions-and-answers"></a>Perguntas e respostas

Aqui estão algumas respostas para perguntas comuns que ouvimos dos clientes:

**Q:** O que preciso de fazer para ser compatível com a data de corte (a data limite atual da Google é 29 de maio e pode mudar)?

**A:** Nada, nada. Manteremos a compatibilidade com o esquema de notificação GCM existente. A sua tecla GCM continuará a funcionar normalmente, assim como quaisquer SDKs e bibliotecas GCM utilizadas pela sua aplicação.

Se/quando decidir fazer upgrade para os SDKs e bibliotecas da FCM para tirar partido de novas funcionalidades, a sua chave GCM continuará a funcionar. Pode mudar para usar uma chave FCM se desejar, mas certifique-se de que está a adicionar a Base de Fogo ao seu projeto GCM existente ao criar o novo projeto Firebase. Isto garantirá retrocompatibilidade com os seus clientes que estão a executar versões mais antigas da app que ainda utilizam SDKs E bibliotecas GCM.

Se estiver a criar um novo projeto FCM e não se ligar ao projeto GCM existente, uma vez que atualiza os Hubs de Notificação com o novo segredo da FCM perderá a capacidade de fazer notificações para as suas atuais instalações de aplicações, uma vez que a nova chave FCM não tem qualquer ligação ao antigo projeto GCM.

**Q:** Porque estou a receber este e-mail sobre os antigos pontos finais da GCM a serem usados? O que tenho que fazer?

**A:** Nada, nada. Temos vindo a migrar para os novos pontos finais e vamos estar concluídos em breve, pelo que não é necessária qualquer alteração. Nada está partido, o nosso único ponto final perdido simplesmente causou mensagens de aviso do Google.

**Q:** Como posso transitar para os novos SDKs e bibliotecas da FCM sem quebrar os utilizadores existentes?

A: Upgrade a qualquer momento. A Google ainda não anunciou qualquer depreciação dos SDKs e bibliotecas existentes. Para garantir que não quebra notificações push para os seus utilizadores existentes, certifique-se de que quando criar o novo projeto Firebase está associado ao seu projeto GCM existente. Isto irá garantir que novos segredos da Firebase funcionarão para os utilizadores que executam as versões mais antigas da sua aplicação com SDKs e bibliotecas GCM, bem como novos utilizadores da sua app com SDKs e bibliotecas FCM.

**Q:** Quando posso utilizar novas funcionalidades e esquemas da FCM para as minhas notificações?

**A:** Assim que publicarmos uma atualização para os nossos API e SDKs, fique atento – esperamos ter algo para si nos próximos meses.
