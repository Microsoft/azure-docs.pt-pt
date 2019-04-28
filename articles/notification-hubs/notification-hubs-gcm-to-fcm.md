---
title: Os Hubs de notificação do Azure e a migração do Google Firebase Cloud Messaging (FCM)
description: Descreve como os Hubs de notificação do Azure lida com o GCM da Google para a migração do FCM.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: jowargo
ms.openlocfilehash: 4cbfc67bc66e84b4743f3326db40872241e5d474
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61458302"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Os Hubs de notificação do Azure e a migração do Google Firebase Cloud Messaging (FCM)

## <a name="current-state"></a>Estado atual

Quando o Google anunciou sua migração do Google Cloud Messaging (GCM) para Firebase Cloud Messaging (FCM), serviços de push, como a nossa tinha que ajustar como enviámos notificações para dispositivos Android para acomodar a alteração.

Vamos atualizar nosso back-end de serviço, em seguida, publicadas atualizações à nossa API e SDKs conforme necessário. Na nossa implementação, começámos por tornar a decisão de manter a compatibilidade com esquemas existentes de notificação de GCM para minimizar o impacto de cliente. Isso significa que podemos atualmente enviar notificações para dispositivos Android que utilizam o FCM no modo de legado do FCM. Por fim, queremos adicionar suporte ao verdadeiro FCM, incluindo os novos recursos e o formato do payload. Isso é uma mudança de longo prazo e a migração atual se concentra em manter a compatibilidade com aplicativos existentes e SDKs. Pode usar o GCM ou SDKs de FCM na sua aplicação (juntamente com o nosso SDK) e verificamos se que a notificação é enviada corretamente.

Alguns clientes receberam recentemente um e-mail de aviso de Google sobre aplicações com um ponto de extremidade do GCM para notificações. Isso era apenas um aviso e nada é quebrado – Android as da sua aplicação notificações são ainda enviadas para a Google para processamento e Google ainda as processa. Alguns clientes que especificado o ponto de extremidade do GCM explicitamente na respetiva configuração do serviço ainda estavam usando o ponto de extremidade preterido. Nós já tinha sido identificados essa lacuna e foram trabalhar para corrigir o problema ao Google enviado o e-mail.

Isso foi substituído esse ponto de final preterido e a correção está implementada.

## <a name="going-forward"></a>No futuro

FAQ do FCM da Google diz que não tem de fazer nada. Na [FCM FAQ](https://developers.google.com/cloud-messaging/faq), Google, disse "SDKs de cliente e tokens GCM continuarão a funcionar indefinidamente. No entanto, não será capaz de atingir a versão mais recente de serviços do Google Play na sua aplicação Android, a menos que migrar para o FCM."

Se a sua aplicação utiliza a biblioteca do GCM, vá em frente e siga as instruções da Google para atualizar para a biblioteca do FCM na sua aplicação. O SDK é compatível com qualquer um, para que não tenha que atualizar qualquer item na sua aplicação do nosso lado (desde que está atualizado com sua versão do SDK).

## <a name="questions-and-answers"></a>Perguntas e respostas

Eis algumas respostas a perguntas comuns que ouvimos de clientes:

**P:** O que preciso fazer para ser compatível, a data limite (actual do Google data limite é 29 de Maio e pode ser alterada)?

**R:** Nada. Iremos manter compatibilidade com o esquema de notificação existente do GCM. A chave do GCM irá continuar a funcionar normalmente serão a todos os SDKs do GCM e bibliotecas utilizadas pela sua aplicação.

IF/quando optar por atualizar para o FCM SDKs e bibliotecas para tirar partido dos novos recursos, a chave do GCM continuarão a funcionar. Pode passar a utilizar uma chave FCM se desejar, mas certifique-se de que está a adicionar Firebase ao seu projeto existente do GCM ao criar o novo projeto Firebase. Isso garantirá a compatibilidade com versões anteriores com os seus clientes que estejam a executar versões mais antigas da aplicação que ainda utilizam bibliotecas e SDKs do GCM.

Se estiver criando um novo projeto do FCM e não a anexar ao projeto GCM existente, depois de atualizar os Hubs de notificação com o novo segredo do FCM perderá a capacidade para enviar notificações push para as suas instalações de aplicações atual, uma vez que a nova chave FCM tem nenhum link com o GCM antigo projeto.

**P:** Por que estou a receber este e-mail sobre a pontos de extremidade do GCM antigos a ser utilizado? O que é necessário fazer?

**R:** Nada. Vamos ter sido migrar para novos pontos de extremidade e será concluídos em breve, para que nenhuma alteração é necessária. Nada é quebrado, o ponto de extremidade em falta um causada simplesmente mensagens de aviso da Google.

**P:** Como pode posso fazer a transição para o novo FCM SDKs e bibliotecas sem quebrar os utilizadores existentes?

R: Atualizar em qualquer altura. Google ainda não anunciou qualquer descontinuação de bibliotecas e SDKs existentes do GCM. Para garantir que não danifiquem notificações push para os utilizadores existentes, certifique-se de quando criar o novo projeto Firebase que está a associar seu projeto existente do GCM. Isto irá garantir Firebase novos segredos irão funcionar para usuários que executam as versões mais antigas da sua aplicação com o GCM SDKs e bibliotecas, bem como os novos utilizadores da sua aplicação com FCM SDKs e bibliotecas.

**P:** Quando posso utilizar novas funcionalidades do FCM e esquemas para as minhas notificações?

**R:** Uma vez que está a publicar uma atualização à nossa API e SDKs, mantenha-se atento – Esperamos que tenha algo para nos próximos meses.
