---
title: Tarefas permitidas em diferentes Estados ou Estados dos serviços BizTalk | Documentos da Microsoft
description: 'As ações/operações permitidas no Estado diferente do MABS: parar, iniciar, reiniciar, suspender, retomar, eliminar, dimensionar, atualizar a configuração e fazer cópia de segurança'
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: aea738f3-ec76-4099-a41b-e17fea9e252f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: mandia
ms.openlocfilehash: 71463b523f0afdf63a7c89ff2b3137ae8d091b3b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086705"
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>O que pode e não pode fazer usando o estado do serviço BizTalk

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Dependendo do estado atual do serviço BizTalk, existem operações que pode ou não é possível executar o serviço BizTalk.

Por exemplo, aprovisionar um novo BizTalk service. Quando for concluída com êxito, o BizTalk service está em `active` estado. No Estado ativo, pode parar, suspender e eliminar o serviço BizTalk. Se parar o serviço BizTalk e stop falha, então o BizTalk service vai para um `StopFailed` estado. Na `StopFailed` de estado, pode reiniciar o serviço BizTalk. Se tentar uma operação que não é permitida, como retomar, ocorre o seguinte erro:

`Operation not allowed`

## <a name="view-the-possible-states"></a>Visualizar os Estados possíveis

As tabelas seguintes listam as operações ou ações que podem ser feitas quando o serviço do BizTalk está num estado específico. Um ✔ significa que a operação é permitida nesse Estado. Uma entrada em branco significa que não é possível efetuar a operação enquanto com esse Estado.

| Estado do serviço | Iniciar | Parar | Reiniciar | Suspender | Retomar | Eliminar | Escala | Atualizar <br/> Configuração | Cópia de segurança |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Ativa |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| Desativado |  |  |  |  |  | ✔ | |  |  | 
| Suspenso |  |  |  |  | ✔ | ✔ | |  | ✔ |
| Parada | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| Falha na atualização de serviço |  |  |  |  |  | ✔ | |  |  | 
| DisableFailed |  |  |  |  |  | ✔ | |  |  | 
| EnableFailed |  |  |  |  |  | ✔ | |  |  | 
| StartFailed <br/> StopFailed <br/> RestartFailed | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| SuspendedFailed <br/> ResumeFailed|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| CreatedFailed <br/> RestoreFailed |  |  |  |  |  | ✔ | |  |  | 
| ConfigUpdateFailed  |  |  | ✔ |  |  | ✔ | |✔ | |
| ScaleFailed |  |  |  |  |  | ✔ |✔ | |  |



## <a name="see-also"></a>Consultar Também
* [O que pode fazer os separadores dashboard, monitorizar e dimensionar nos serviços BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [O que fazer com as edições Developer, Basic, Standard e Premium dos serviços BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Como criar cópias de segurança e restaurar um BizTalk Service](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Limitação explicado nos serviços BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [Obter os controle de acesso e do Service Bus emissor nome e o emissor valores de chave para o seu serviço BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Como posso começar a utilizar o SDK dos Serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=302335)

