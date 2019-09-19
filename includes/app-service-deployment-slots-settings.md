---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129701"
---
Quando você clona a configuração de outro slot de implantação, a configuração clonada é editável. Alguns elementos de configuração seguem o conteúdo em uma permuta (não específico do slot), enquanto outros elementos de configuração permanecem no mesmo slot após uma permuta (específica do slot). As listas a seguir mostram as configurações que são alteradas quando você permuta os slots.

**Configurações que são trocadas**:

* Configurações gerais, como versão do Framework, 32/64 bits, Web Sockets
* Configurações do aplicativo (podem ser configuradas para serem transfixadas em um slot)
* Cadeias de conexão (podem ser configuradas para aderir a um slot)
* Mapeamentos de processador
* Certificados públicos
* Conteúdo de trabalhos Web
* Conexões híbridas *
* Integração de rede virtual *
* Pontos de extremidade de serviço *
* Rede de distribuição de conteúdo do Azure *

Os recursos marcados com um asterisco (*) estão planejados para serem desalternados. 

**Configurações que não são trocadas**:

* Pontos de extremidade de publicação
* Nomes de domínio personalizados
* Certificados não públicos e configurações de TLS/SSL
* Definições de dimensionamento
* Agendadores de trabalhos Web
* Restrições de IP
* Always On
* Configurações do log de diagnóstico
* CORS (compartilhamento de recursos entre origens)

> [!NOTE]
> Determinadas configurações de aplicativo que se aplicam a configurações não alternadas também são trocadas. Por exemplo, como as configurações de log de diagnóstico não são trocadas, as `WEBSITE_HTTPLOGGING_RETENTION_DAYS` configurações `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` de aplicativo relacionadas, como e também não são trocadas, mesmo que não apareçam como configurações de slot.
>
