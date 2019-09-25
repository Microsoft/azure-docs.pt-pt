---
title: Segurança dos hubs de notificação
description: Este tópico explica a segurança para os hubs de notificação do Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 753493100bbdb34255574656a47217560e2d321a
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213057"
---
# <a name="notification-hubs-security"></a>Segurança dos hubs de notificação

## <a name="overview"></a>Descrição geral

Este tópico descreve o modelo de segurança dos hubs de notificação do Azure.

## <a name="shared-access-signature-security-sas"></a>Segurança de assinatura de acesso compartilhado (SAS)

Os hubs de notificação implementam um esquema de segurança em nível de entidade chamado SAS (assinatura de acesso compartilhado). Cada regra contém um nome, um valor de chave (segredo compartilhado) e um conjunto de direitos, conforme explicado em [declarações de segurança](#security-claims). Ao criar um hub de notificação, duas regras são criadas automaticamente: uma com direitos de **escuta** (que o aplicativo cliente usa) e outra com **todos os** direitos (que o back-end do aplicativo usa).

Ao executar o gerenciamento de registro de aplicativos cliente, se as informações enviadas por meio de notificações não forem confidenciais (por exemplo, atualizações do clima), uma maneira comum de acessar um hub de notificação é fornecer o valor de chave da regra de acesso somente de escuta para o aplicativo cliente, e para fornecer o valor de chave da regra acesso completo ao back-end do aplicativo.

Os aplicativos não devem inserir o valor de chave nos aplicativos cliente da Windows Store, em vez disso, fazer com que o aplicativo cliente o recupere do back-end do aplicativo na inicialização.

A chave com acesso de **escuta** permite que um aplicativo cliente se registre em qualquer marca. Se seu aplicativo precisar restringir registros a marcas específicas para clientes específicos (por exemplo, quando as marcas representam IDs de usuário), o back-end do aplicativo deve executar os registros. Para obter mais informações, consulte [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md). Observe que dessa forma, o aplicativo cliente não terá acesso direto aos hubs de notificação.

## <a name="security-claims"></a>Declarações de segurança

Semelhante a outras entidades, as operações do hub de notificação são permitidas para três declarações de segurança: **Ouça**, **envie**e **gerencie**.

| Afirmação   | Descrição                                          | Operações permitidas |
| ------- | ---------------------------------------------------- | ------------------ |
| Escutar  | Criar/atualizar, ler e excluir registros únicos | Criar/atualizar registro<br><br>Ler registro<br><br>Ler todos os registros de um identificador<br><br>Excluir registro |
| Enviar    | Enviar mensagens para o Hub de notificação                | Enviar mensagem |
| Gerir  | CRUDs em hubs de notificação (incluindo atualização de credenciais do PNS e chaves de segurança) e ler registros com base em marcas |Criar/atualizar/ler/excluir hubs de notificação<br><br>Ler registros por marca |

Os hubs de notificação aceitam tokens de assinatura gerados com chaves compartilhadas configuradas diretamente no Hub de notificação.

Não é possível enviar uma notificação para mais de um namespace. Os namespaces são contêineres lógicos para hubs de notificação e não estão envolvidos no envio de notificações.
As políticas de acesso no nível de namespace (credenciais) podem ser usadas para operações em nível de namespace, por exemplo: listando hubs de notificação, criando ou excluindo hubs de notificação, etc. Somente as políticas de acesso no nível do Hub permitirão que você envie notificações.
