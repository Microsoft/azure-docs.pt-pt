---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "71129701"
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
* Definições de registo de diagnóstico
* Partilha de recursos de origem cruzada (CORS)

> [!NOTE]
> Algumas definições de aplicações que se aplicam a definições não trocadas também não são trocadas. Por exemplo, uma vez que as definições `WEBSITE_HTTPLOGGING_RETENTION_DAYS` de `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` registo de diagnóstico não são trocadas, as configurações de aplicações relacionadas como e também não são trocadas, mesmo que não apareçam como configurações de slot.
>
