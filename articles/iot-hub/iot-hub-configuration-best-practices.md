---
title: Práticas recomendadas de configuração do dispositivo para o Hub IoT do Azure | Microsoft Docs
description: Saiba mais sobre as práticas recomendadas para usar o gerenciamento automático de dispositivos para minimizar tarefas repetitivas e complexas envolvidas no gerenciamento de dispositivos IoT em escala.
author: chrisgre
ms.author: chrisgre
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: a3b70af71c2ce19835ac2ef8fc8ceed79ca5fe1a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889536"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Práticas recomendadas para a configuração do dispositivo em uma solução de IoT

O gerenciamento automático de dispositivos no Hub IoT do Azure automatiza muitas tarefas repetitivas e complexas de gerenciar as frotas de dispositivos grandes durante todo o ciclo de vida deles. Este artigo define muitas das práticas recomendadas para as várias funções envolvidas no desenvolvimento e operação de uma solução de IoT.

* **Fabricante/integrador de hardware de IOT:** Fabricantes de hardware de IoT, integradores montando hardware de vários fabricantes ou fornecedores fornecendo hardware para uma implantação de IoT fabricada ou integrada por outros fornecedores. Envolvidos no desenvolvimento e na integração de firmware, sistemas operacionais incorporados e software incorporado.

* **Desenvolvedor de soluções de IOT:** O desenvolvimento de uma solução de IoT normalmente é feito por um desenvolvedor de soluções. Esse desenvolvedor pode fazer parte de uma equipe interna ou um integrador de sistemas especializado nesta atividade. O desenvolvedor da solução de IoT pode desenvolver vários componentes da solução de IoT do zero, integrar vários componentes padrão ou de código-fonte aberto ou personalizar um [acelerador de solução de IOT](/azure/iot-accelerators/).

* **Operador de solução de IOT:** Depois que a solução de IoT é implantada, ela requer operações de longo prazo, monitoramento, atualizações e manutenção. Essas tarefas podem ser feitas por uma equipe interna que consiste em especialistas em tecnologia da informação, em operações de hardware e em equipes de manutenção e em especialistas de domínio que monitoram o comportamento correto da infraestrutura de IoT geral.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Entender o gerenciamento automático de dispositivos para configurar dispositivos IoT em escala

O gerenciamento automático de dispositivos inclui os muitos benefícios do [dispositivo gêmeos](iot-hub-devguide-device-twins.md) e do [módulo gêmeos](iot-hub-devguide-module-twins.md) para sincronizar os Estados desejados e relatados entre a nuvem e os dispositivos. [As configurações automáticas de dispositivo](iot-hub-auto-device-config.md) atualizam automaticamente grandes conjuntos de gêmeos e resumem o progresso e a conformidade. As etapas de alto nível a seguir descrevem como o gerenciamento automático de dispositivos é desenvolvido e usado:

* O **fabricante/integrador de hardware de IOT** implementa recursos de gerenciamento de dispositivos em um aplicativo inserido usando [dispositivos gêmeos](iot-hub-devguide-device-twins.md). Esses recursos podem incluir atualizações de firmware, instalação de software e atualização e gerenciamento de configurações.

* O **desenvolvedor da solução de IOT** implementa a camada de gerenciamento de operações de gerenciamento de dispositivos usando [dispositivos gêmeos](iot-hub-devguide-device-twins.md) e [configurações de dispositivo automáticas](iot-hub-auto-device-config.md). A solução deve incluir a definição de uma interface de operador para executar tarefas de gerenciamento de dispositivos.

* O **operador de solução de IOT** usa a solução de IOT para executar tarefas de gerenciamento de dispositivos, especialmente para agrupar dispositivos, iniciar alterações de configuração como atualizações de firmware, monitorar o progresso e solucionar problemas que surgem.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricante/integrador de hardware de IoT

Veja a seguir as práticas recomendadas para fabricantes de hardware e integradores que lidam com o desenvolvimento de software incorporado:

* **Implementar [dispositivo gêmeos](iot-hub-devguide-device-twins.md):** O dispositivo gêmeos habilita a sincronização da configuração desejada da nuvem e para relatar a configuração atual e as propriedades do dispositivo. A melhor maneira de implementar dispositivos gêmeos em aplicativos inseridos é por meio dos [SDKs do Azure IOT](https://github.com/Azure/azure-iot-sdks). Dispositivos gêmeos são mais adequados para configuração porque eles:

    * Suporte à comunicação bidirecional.
    * Permitir Estados de dispositivo conectado e desconectado.
    * Siga o princípio de consistência eventual.
    * São totalmente consultável na nuvem.

* **Estruturar os dispositivos de dispositivo para o gerenciamento de dispositivos:** O dispositivo "/" deve ser estruturado de modo que as propriedades de gerenciamento de dispositivos sejam agrupadas logicamente em seções. Isso permitirá que as alterações de configuração sejam isoladas sem afetar outras seções do. Por exemplo, crie uma seção dentro das propriedades desejadas para o firmware, outra seção para software e uma terceira seção para configurações de rede. 

* **Relate atributos de dispositivo que são úteis para o gerenciamento de dispositivos:** Atributos como Make de dispositivo físico e modelo, firmware, sistema operacional, número de série e outros identificadores são úteis para relatórios e como parâmetros para direcionar as alterações de configuração.

* **Defina os Estados principais para relatar o status e o progresso:** Os Estados de nível superior devem ser enumerados para que possam ser relatados ao operador. Por exemplo, uma atualização de firmware relataria o status como atual, baixando, aplicando, em andamento e erro. Defina campos adicionais para obter mais informações sobre cada Estado.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções de IoT

Veja a seguir as práticas recomendadas para desenvolvedores de soluções de IoT que estão criando sistemas baseados no Azure:

* **Implementar [dispositivo gêmeos](iot-hub-devguide-device-twins.md):** O dispositivo gêmeos habilita a sincronização da configuração desejada da nuvem e para relatar a configuração atual e as propriedades do dispositivo. A melhor maneira de implementar dispositivos gêmeos em aplicativos de soluções de nuvem é por meio dos [SDKs do IOT do Azure](https://github.com/Azure/azure-iot-sdks). Dispositivos gêmeos são mais adequados para configuração porque eles:

    * Suporte à comunicação bidirecional.
    * Permitir Estados de dispositivo conectado e desconectado.
    * Siga o princípio de consistência eventual.
    * São totalmente consultável na nuvem.

* **Organizar dispositivos usando marcas de dispositivo de entrelaçamento:** A solução deve permitir que o operador defina anéis de qualidade ou outros conjuntos de dispositivos com base em várias estratégias de implantação, como canário. A organização do dispositivo pode ser implementada em sua solução usando marcas e [consultas](iot-hub-devguide-query-language.md)do dispositivo. A organização do dispositivo é necessária para permitir que os lançamentos de configuração sejam com segurança e precisão.

* **Implementar [configurações de dispositivo automáticas](iot-hub-auto-device-config.md):** As configurações automáticas de dispositivos implantam e monitoram alterações de configuração em grandes conjuntos de dispositivos IoT por meio de dispositivos gêmeos.

   Configurações automáticas de destino definem conjuntos de dispositivos gêmeos por meio da **condição de destino,** que é uma consulta em marcas de dispositivo ou Propriedades relatadas. O **conteúdo de destino** é o conjunto de propriedades desejadas que será definido dentro do dispositivo de destino gêmeos. O conteúdo de destino deve ser alinhado com a estrutura de entrelaçamento do dispositivo definida pelo fabricante/integrador de hardware de IoT. As **métricas** são consultas em Propriedades relatadas do dispositivo e também devem ser alinhadas com a estrutura de entrelaçamento do dispositivo definida pelo fabricante/integrador de hardware de IOT.

   As configurações automáticas do dispositivo são executadas pela primeira vez logo após a configuração ser criada e, em seguida, em intervalos de cinco minutos. Eles também se beneficiam do Hub IoT que executa operações de dispositivos de velocidade de dispositivo a uma taxa que nunca excederá os [limites de limitação](iot-hub-devguide-quotas-throttling.md) para leituras e atualizações do dispositivo.

* **Usar o [serviço de provisionamento de dispositivos](../iot-dps/how-to-manage-enrollments.md):** os desenvolvedores de soluções devem usar o serviço de provisionamento de dispositivos para atribuir marcas de dispositivo para novos dispositivos, de modo que eles serão automaticamente configurados por **configurações de dispositivo automáticas** que são destinadas a gêmeos com essa marca. 

## <a name="iot-solution-operator"></a>Operador de solução de IoT

Veja a seguir as práticas recomendadas para operadores de solução de IoT que usam uma solução de IoT criada no Azure:

* **Organizar dispositivos para gerenciamento:** A solução de IoT deve definir ou permitir a criação de anéis de qualidade ou outros conjuntos de dispositivos com base em várias estratégias de implantação, como canário. Os conjuntos de dispositivos serão usados para distribuir alterações de configuração e para executar outras operações de gerenciamento de dispositivo em escala.

* **Realizar alterações de configuração usando uma distribuição em fases:**  Uma distribuição em fases é um processo geral no qual um operador implanta alterações em um conjunto abrangente de dispositivos IoT. O objetivo é fazer alterações gradualmente para reduzir o risco de fazer alterações significativas em larga escala.  O operador deve usar a interface da solução para criar uma [configuração de dispositivo automática](iot-hub-auto-device-config.md) e a condição de direcionamento deve ter como destino um conjunto inicial de dispositivos (como um grupo canário). O operador deve validar a alteração de configuração no conjunto inicial de dispositivos.

   Depois que a validação for concluída, o operador atualizará a configuração automática do dispositivo para incluir um conjunto maior de dispositivos. O operador também deve definir a prioridade para que a configuração seja maior que outras configurações atualmente destinadas a esses dispositivos. A distribuição pode ser monitorada usando as métricas relatadas pela configuração automática do dispositivo.

* **Executar reversões em caso de erros ou configurações incorretas:**  Uma configuração de dispositivo automática que causa erros ou configurações incorretas pode ser revertida alterando a **condição de destino** para que os dispositivos não atendam mais à condição de direcionamento. Verifique se outra configuração automática de dispositivo de prioridade mais baixa ainda é direcionada para esses dispositivos. Verifique se a reversão foi bem-sucedida exibindo as métricas: a configuração revertida não deve mais mostrar o status de dispositivos não direcionados e as métricas da segunda configuração agora devem incluir contagens para os dispositivos que ainda estão direcionados.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como implementar dispositivos gêmeos em [entender e usar dispositivos gêmeos no Hub IOT](iot-hub-devguide-device-twins.md).

* Percorra as etapas para criar, atualizar ou excluir uma configuração de dispositivo automática em [configurar e monitorar dispositivos IOT em escala](iot-hub-auto-device-config.md).

* Implementar um padrão de atualização de firmware usando dispositivos gêmeos e configurações de dispositivo automáticas no [tutorial: implementar um processo de atualização de firmware de dispositivo](tutorial-firmware-update.md).
