---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: b4234acb2ce32980a268e389cb31de9a57ed18e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131908"
---
Quando se clona a configuração de outra ranhura de implantação, a configuração clonada é editável. Alguns elementos de configuração seguem o conteúdo através de uma troca (não específica de ranhura), enquanto outros elementos de configuração permanecem na mesma ranhura após uma troca (específica de ranhura). As seguintes listas mostram as definições que mudam quando troca as ranhuras.

**Definições trocadas:**

* Configurações gerais, tais como versão-quadro, tomadas web de 32/64 bits
* Definições de aplicativos (pode ser configurada para ficar com uma ranhura)
* Cordas de ligação (pode ser configurada para ficar colada a uma ranhura)
* Mapeamentos de manipuladores
* Certificados públicos
* Conteúdo webJobs
* Conexões híbridas *
* Integração de rede virtual *
* Pontos finais de serviço *
* Rede de Entrega de Conteúdos Azure *

Estão previstas características marcadas com um asterisco (*) para serem destrocadas. 

**Definições que não são trocadas:**

* Pontos finais de publicação
* Nomes de domínio personalizados
* Certificados não públicos e definições TLS/SSL
* Definições de escala
* Programadores WebJobs
* Restrições IP
* Sempre ligado
* Definições de diagnóstico
* Partilha de recursos de origem cruzada (CORS)

> [!NOTE]
> Algumas definições de aplicações que se aplicam a definições não trocadas também não são trocadas. Por exemplo, uma vez que as definições `WEBSITE_HTTPLOGGING_RETENTION_DAYS` `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` de diagnóstico não são trocadas, as configurações de aplicações relacionadas como e também não são trocadas, mesmo que não apareçam como configurações de slot.
>
