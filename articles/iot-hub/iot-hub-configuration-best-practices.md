---
title: Configuração de dispositivos boas práticas para O Hub Azure IoT [ Microsoft Docs
description: Conheça as melhores práticas para utilizar a gestão automática de dispositivos para minimizar tarefas repetitivas e complexas envolvidas na gestão de dispositivos IoT em escala.
author: robinsh
ms.author: robinsh
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 67f0d9eb1fdac603ee82d568644e8ad8550d1c80
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024783"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>As melhores práticas para a configuração do dispositivo dentro de uma solução IoT

A gestão automática de dispositivos no Azure IoT Hub automatiza muitas tarefas repetitivas e complexas de gestão de grandes frotas de dispositivos ao longo de toda a sua vida. Este artigo define muitas das melhores práticas para as várias funções envolvidas no desenvolvimento e operação de uma solução IoT.

* **Fabricante/integrador de hardware IoT:** Fabricantes de hardware IoT, integradores que montam hardware de vários fabricantes, ou fornecedores que fornecem hardware para uma implementação IoT fabricada ou integrada por outros fornecedores. Envolvido no desenvolvimento e integração de firmware, sistemas operativos incorporados e software incorporado.

* **Desenvolvedor de soluções IoT:** O desenvolvimento de uma solução IoT é normalmente feito por um desenvolvedor de soluções. Este desenvolvedor pode fazer parte de uma equipa interna ou de um integrador de sistema especializado nesta atividade. O desenvolvedor de soluções IoT pode desenvolver vários componentes da solução IoT de raiz, integrar vários componentes padrão ou de código aberto, ou personalizar um acelerador de [solução IoT](/azure/iot-accelerators/).

* **Operador de solução IoT:** Após a implementação da solução IoT, requer operações a longo prazo, monitorização, atualizações e manutenção. Estas tarefas podem ser feitas por uma equipa interna que é constituída por especialistas em tecnologias da informação, operações de hardware e equipas de manutenção, e especialistas em domínios que monitorizam o comportamento correto da infraestrutura ioT global.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Compreender a gestão automática do dispositivo para configurar dispositivos IoT em escala

A gestão automática do dispositivo inclui os muitos benefícios dos [gémeos](iot-hub-devguide-device-twins.md) do dispositivo e dos [gémeos módulos](iot-hub-devguide-module-twins.md) para sincronizar os estados desejados e relatados entre a nuvem e os dispositivos. [As configurações automáticas](iot-hub-auto-device-config.md) do dispositivo atualizam automaticamente grandes conjuntos de gémeos e resumem o progresso e a conformidade. As seguintes etapas de alto nível descrevem como a gestão automática do dispositivo é desenvolvida e utilizada:

* O **fabricante/integrador de hardware IoT** implementa funcionalidades de gestão de dispositivos dentro de uma aplicação incorporada utilizando [gémeos dispositivos](iot-hub-devguide-device-twins.md). Estas funcionalidades podem incluir atualizações de firmware, instalação e atualização de software e gestão de configurações.

* O desenvolvedor de **soluções IoT** implementa a camada de gestão de operações de gestão de dispositivos utilizando [gémeos de dispositivos](iot-hub-devguide-device-twins.md) e [configurações automáticas de dispositivos.](iot-hub-auto-device-config.md) A solução deve incluir a definição de uma interface do operador para executar tarefas de gestão de dispositivos.

* O **operador de solução IoT** utiliza a solução IoT para executar tarefas de gestão de dispositivos, nomeadamente para agrupar dispositivos, iniciar alterações de configuração como atualizações de firmware, monitorizar o progresso e resolver problemas que surjam.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricante/integrador de hardware IoT

Seguem-se as melhores práticas para fabricantes de hardware e integradores que lidam com o desenvolvimento de software incorporado:

* **Implementar [gémeos dispositivo:](iot-hub-devguide-device-twins.md)** Os gémeos do dispositivo permitem sincronizar a configuração desejada a partir da nuvem e para reportar as propriedades atuais da configuração e do dispositivo. A melhor maneira de implementar gémeos dispositivos dentro de aplicações incorporadas é através dos [SDKs Azure IoT](https://github.com/Azure/azure-iot-sdks). Os gémeos dispositivosão mais adequados para a configuração porque:

    * Apoiar a comunicação bidirecional.
    * Deixe os estados do dispositivo ligados e desligados.
    * Siga o princípio da eventual consistência.
    * Estão completamente queriamble na nuvem.

* **Estruturar o dispositivo twin para a gestão do dispositivo:** O dispositivo twin deve ser estruturado de modo a que as propriedades de gestão do dispositivo sejam logicamente agruparadas em secções. Ao fazê-lo, permitirá que as alterações de configuração sejam isoladas sem afetar outras secções do gémeo. Por exemplo, criar uma secção dentro das propriedades desejadas para firmware, outra secção para software e uma terceira secção para configurações de rede. 

* **Atributos do dispositivo de relatório que são úteis para a gestão do dispositivo:** Atributos como a configuração e modelo de dispositivos físicos, firmware, sistema operativo, número de série e outros identificadores são úteis para reportar e como parâmetros para direcionar as alterações de configuração.

* **Definir os principais Estados para o estado de reporte e o progresso:** Os estados de alto nível devem ser enumerados para que possam ser comunicados ao operador. Por exemplo, uma atualização de firmware reportaria o estado como Current, Downloading, Applying, In Progress e Error. Defina campos adicionais para mais informações sobre cada estado.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções IoT

Seguem-se as melhores práticas para os desenvolvedores de soluções IoT que estão a construir sistemas baseados em Azure:

* **Implementar [gémeos dispositivo:](iot-hub-devguide-device-twins.md)** Os gémeos do dispositivo permitem sincronizar a configuração desejada a partir da nuvem e para reportar as propriedades atuais da configuração e do dispositivo. A melhor maneira de implementar gémeos de dispositivos dentro das aplicações de soluções cloud é através dos [SDKs Azure IoT](https://github.com/Azure/azure-iot-sdks). Os gémeos dispositivosão mais adequados para a configuração porque:

    * Apoiar a comunicação bidirecional.
    * Deixe os estados do dispositivo ligados e desligados.
    * Siga o princípio da eventual consistência.
    * Estão completamente queriamble na nuvem.

* **Organize dispositivos utilizando etiquetas duplas** do dispositivo: A solução deve permitir ao operador definir anéis de qualidade ou outros conjuntos de dispositivos baseados em várias estratégias de implementação, como canário. A organização do dispositivo pode ser implementada dentro da sua solução usando etiquetas e [consultas](iot-hub-devguide-query-language.md)de dispositivos. A organização do dispositivo é necessária para permitir a configuração de saídas com segurança e precisão.

* **Implementar [configurações automáticas do dispositivo:](iot-hub-auto-device-config.md)** As configurações automáticas do dispositivo implementam e monitorizam alterações de configuração para grandes conjuntos de dispositivos IoT através de gémeos dispositivos.

   Configurações automáticas de dispositivos conjuntos de gémeos de dispositivo através da **condição alvo,** que é uma consulta em tags duplas do dispositivo ou propriedades reportadas. O **conteúdo-alvo** é o conjunto de propriedades desejadas que serão definidas dentro dos gémeos do dispositivo direcionado. O conteúdo-alvo deve alinhar-se com a estrutura dupla do dispositivo definida pelo fabricante/integrador de hardware IoT. As **métricas** são consultas sobre propriedades reportadas twin do dispositivo e também devem alinhar-se com a estrutura dupla do dispositivo definida pelo fabricante/integrador de hardware IoT.

   As configurações automáticas do dispositivo funcionam pela primeira vez pouco depois da configuração ser criada e, em seguida, em intervalos de cinco minutos. Também beneficiam do dispositivo IoT Hub executando operações gémeas a um ritmo que nunca excederá os [limites](iot-hub-devguide-quotas-throttling.md) de estrangulamento para leituras e atualizações de gémeos dispositivos.

* **Utilizar o Serviço de [Provisionamento](../iot-dps/how-to-manage-enrollments.md)** de Dispositivos : Os desenvolvedores de soluções devem utilizar o Serviço de Fornecimento de Dispositivos para atribuir etiquetas duplas do dispositivo a novos dispositivos, de modo a que sejam automaticamente configurados por **configurações automáticas** de dispositivos que sejam direcionados para gémeos com essa etiqueta. 

## <a name="iot-solution-operator"></a>Operador de solução IoT

Seguem-se as melhores práticas para os operadores de soluções IoT que utilizam uma solução IoT construída em Azure:

* **Organize dispositivos para gestão:** A solução IoT deve definir ou permitir a criação de anéis de qualidade ou outros conjuntos de dispositivos baseados em várias estratégias de implementação, como canário. Os conjuntos de dispositivos serão utilizados para lançar alterações de configuração e para realizar outras operações de gestão de dispositivos em escala.

* **Execute alterações de configuração utilizando um lançamento faseado:**  Um lançamento faseado é um processo global pelo qual um operador implementa alterações para um conjunto alargado de dispositivos IoT. O objetivo é fazer mudanças gradualmente para reduzir o risco de fazer mudanças de rutura em larga escala.O operador deve utilizar a interface da solução para criar uma [configuração automática](iot-hub-auto-device-config.md) do dispositivo e a condição de segmentação deve visar um conjunto inicial de dispositivos (como um grupo canário). O operador deverá então validar a alteração de configuração no conjunto inicial de dispositivos.

   Uma vez concluída a validação, o operador atualizará a configuração do dispositivo automático para incluir um conjunto maior de dispositivos. O operador deve igualmente definir a prioridade para que a configuração seja superior à de outras configurações atualmente direcionadas para esses dispositivos. O lançamento pode ser monitorizado utilizando as métricas comunicadas pela configuração automática do dispositivo.

* **Efetuar retrocessos em caso de erros ou configurações erradas:**  Uma configuração automática do dispositivo que provoque erros ou configurações erradas pode ser revertida alterando a condição de **alvo para** que os dispositivos deixem de cumprir a condição de alvo. Certifique-se de que outra configuração automática de menor prioridade ainda é direcionada para esses dispositivos. Verifique se a reversão foi bem sucedida ao visualizar as métricas: A configuração de volta não deve mais mostrar o estado dos dispositivos não visados, e as métricas da segunda configuração devem agora incluir contagens para os dispositivos que ainda estão direcionados.

## <a name="next-steps"></a>Passos seguintes

* Aprenda sobre a implementação de gémeos de dispositivos em [Understand e use gémeos dispositivos no IoT Hub](iot-hub-devguide-device-twins.md).

* Caminhe pelos passos para criar, atualizar ou eliminar uma configuração automática do dispositivo em [Configurar e monitorar dispositivos IoT à escala](iot-hub-auto-device-config.md).

* Implemente um padrão de atualização de firmware utilizando gémeos dispositivos e configurações automáticas de dispositivos no [Tutorial: Implemente um processo](tutorial-firmware-update.md)de atualização de firmware de dispositivos .
