---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: 0691b1a531ffebbb2c368bdb37dd4d8025fb4a4e
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623710"
---
Quando você clona a configuração de outro slot de implantação, a configuração clonada é editável. Alguns elementos de configuração seguem o conteúdo em uma permuta (não específico do slot), enquanto outros elementos de configuração permanecem no mesmo slot após uma permuta (específica do slot). As listas a seguir mostram as configurações que são alteradas quando você permuta os slots.

**Configurações que são trocadas**:

* Configurações gerais, como versão do Framework, 32/64 bits, Web Sockets
* Configurações do aplicativo (podem ser configuradas para serem transfixadas em um slot)
* Cadeias de conexão (podem ser configuradas para aderir a um slot)
* Mapeamentos de processador
* Configurações de monitoramento e diagnóstico
* Certificados públicos
* Conteúdo de trabalhos Web
* Conexões híbridas *
* Integração de rede virtual *
* Pontos de extremidade de serviço *
* Rede de distribuição de conteúdo do Azure *

Os recursos marcados com um asterisco (*) estão planejados para serem adesivos no slot. 

**Configurações que não são trocadas**:

* Pontos de extremidade de publicação
* Nomes de domínio personalizados
* Certificados privados e associações SSL
* Definições de dimensionamento
* Agendadores de trabalhos Web
* Restrições de IP
* Always On
* Configurações de protocolo (HTTPS, versão de TLS, certificados de cliente)
* Configurações do log de diagnóstico
* CORS (compartilhamento de recursos entre origens)

<!-- VNET and hybrid connections not yet sticky to slot -->