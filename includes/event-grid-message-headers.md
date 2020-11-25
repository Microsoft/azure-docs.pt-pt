---
title: incluir ficheiro
description: incluir ficheiro
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c37e2c28eb6aee9edf4bdd97066ce5f15e7447c1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005640"
---
## <a name="message-headers"></a>Cabeçalhos de mensagem
Estas são as propriedades que recebe nos cabeçalhos de mensagens:

| Nome da propriedade | Descrição |
| ------------- | ----------- | 
| nome de subscrição aeg | Nome da subscrição do evento. |
| aeg-entrega-contagem | Número de tentativas feitas para o evento. |
| aeg-event-type | <p>Tipo de evento.</p><p>Pode ser um dos seguintes valores:</p><ul><li>SubscriçãoValidação</li><li>Notificação</li><li>AssinaturaDeletion</li></ul> | 
| versão aeg-metadados | <p>Versão metadados do evento.<p> Para **o esquema de eventos Grid,** esta propriedade representa a versão metadados e para o esquema de **eventos em nuvem,** representa a **versão spec**. </p>|
| versão aeg-data | <p>Versão de dados do evento.</p><p>Para **o esquema de eventos Grid,** esta propriedade representa a versão de dados e para o esquema de **eventos em nuvem,** não se aplica.</p> |
| aeg-output-evento-id | ID do evento Event Grid. |


