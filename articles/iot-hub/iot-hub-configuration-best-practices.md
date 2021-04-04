---
title: Configuração do dispositivo as melhores práticas para Azure IoT Hub | Microsoft Docs
description: Saiba mais sobre as melhores práticas de utilização automática de dispositivos para minimizar tarefas repetitivas e complexas envolvidas na gestão de dispositivos IoT em escala.
author: robinsh
ms.author: robinsh
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 8a39c2b06ca8a0f852891acb60ba199fc2c6db5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96024134"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Melhores práticas para a configuração do dispositivo dentro de uma solução IoT

A gestão automática de dispositivos no Azure IoT Hub automatiza muitas tarefas repetitivas e complexas de gestão de grandes frotas de dispositivos ao longo de todo o seu ciclo de vida. Este artigo define muitas das melhores práticas para as várias funções envolvidas no desenvolvimento e exploração de uma solução IoT.

* **Fabricante/integrador de hardware IoT:** Fabricantes de hardware IoT, integradores que montam hardware de vários fabricantes, ou fornecedores que fornecem hardware para uma implementação IoT fabricada ou integrada por outros fornecedores. Envolvido no desenvolvimento e integração de firmware, sistemas operativos incorporados e software incorporado.

* **Desenvolvedor de soluções IoT:** O desenvolvimento de uma solução IoT é normalmente feito por um desenvolvedor de soluções. Este desenvolvedor pode fazer parte de uma equipa interna ou de um integrador de sistemas especializado nesta atividade. O desenvolvedor de soluções IoT pode desenvolver vários componentes da solução IoT de raiz, integrar vários componentes standard ou open-source, ou personalizar um acelerador de [solução IoT.](../iot-accelerators/index.yml)

* **Operador de solução IoT:** Após a implementação da solução IoT, requer operações a longo prazo, monitorização, upgrades e manutenção. Estas tarefas podem ser feitas por uma equipa interna que é constituída por especialistas em tecnologias da informação, equipas de operações de hardware e manutenção, e especialistas em domínios que monitorizam o comportamento correto da infraestrutura geral de IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Compreender a gestão automática de dispositivos para configurar dispositivos IoT à escala

A gestão automática de dispositivos inclui os muitos benefícios dos [gémeos](iot-hub-devguide-device-twins.md) do dispositivo e [dos gémeos módulos](iot-hub-devguide-module-twins.md) para sincronizar estados desejados e relatados entre a nuvem e os dispositivos. [As configurações automáticas](./iot-hub-automatic-device-management.md) do dispositivo atualizam automaticamente grandes conjuntos de gémeos e resumem o progresso e a conformidade. Os seguintes passos de alto nível descrevem como a gestão automática do dispositivo é desenvolvida e utilizada:

* O **fabricante/integrador de hardware IoT** implementa funcionalidades de gestão de dispositivos dentro de uma aplicação incorporada utilizando [gémeos dispositivos](iot-hub-devguide-device-twins.md). Estas funcionalidades podem incluir atualizações de firmware, instalação e atualização de software e gestão de definições.

* O **desenvolvedor de soluções IoT** implementa a camada de gestão das operações de gestão de dispositivos utilizando [gémeos de dispositivos](iot-hub-devguide-device-twins.md) e [configurações automáticas de dispositivos.](./iot-hub-automatic-device-management.md) A solução deve incluir a definição de uma interface do operador para executar tarefas de gestão de dispositivos.

* O **operador de solução IoT** utiliza a solução IoT para executar tarefas de gestão de dispositivos, nomeadamente para agrupar dispositivos em conjunto, iniciar alterações de configuração como atualizações de firmware, monitorizar o progresso e resolver problemas que surgem.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricante/integrador de hardware IoT

Seguem-se as melhores práticas para fabricantes de hardware e integradores que lidam com o desenvolvimento de software incorporado:

* **Implementar [gémeos dispositivo:](iot-hub-devguide-device-twins.md)** Os gémeos do dispositivo permitem sincronizar a configuração desejada a partir da nuvem e para reportar as propriedades da configuração e do dispositivo atuais. A melhor forma de implementar gémeos de dispositivos dentro de aplicações incorporadas é através dos [SDKs Azure IoT](https://github.com/Azure/azure-iot-sdks). Os gémeos do dispositivo são mais adequados para a configuração porque:

    * Apoie a comunicação bidis.
    * Deixe os estados do dispositivo ligados e desligados.
    * Siga o princípio da eventual consistência.
    * São totalmente desapersiáveis na nuvem.

* **Estruturar o dispositivo gémeo para a gestão do dispositivo:** O twin do dispositivo deve ser estruturado de modo a que as propriedades de gestão do dispositivo sejam logicamente agrupadas em secções. Ao fazê-lo, as alterações de configuração serão isoladas sem afetar outras secções do gémeo. Por exemplo, crie uma secção dentro das propriedades desejadas para firmware, outra secção para software e uma terceira secção para configurações de rede. 

* **Reportar atributos de dispositivos que são úteis para a gestão do dispositivo:** Atributos como a operação e modelo de dispositivos físicos, firmware, sistema operativo, número de série e outros identificadores são úteis para reportar e como parâmetros para direcionar as alterações de configuração.

* **Definir os principais Estados para o estado de reporte e o progresso:** Os Estados de alto nível devem ser enumerados para que possam ser comunicados ao operador. Por exemplo, uma atualização de firmware reportaria o estado como Current, Downloading, Applying, In Progress e Error. Defina campos adicionais para mais informações sobre cada estado.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções IoT

Seguem-se as melhores práticas para os desenvolvedores de soluções IoT que estão a construir sistemas baseados em Azure:

* **Implementar [gémeos dispositivo:](iot-hub-devguide-device-twins.md)** Os gémeos do dispositivo permitem sincronizar a configuração desejada a partir da nuvem e para reportar as propriedades da configuração e do dispositivo atuais. A melhor forma de implementar gémeos de dispositivos dentro de aplicações de soluções em nuvem é através dos [SDKs Azure IoT](https://github.com/Azure/azure-iot-sdks). Os gémeos do dispositivo são mais adequados para a configuração porque:

    * Apoie a comunicação bidis.
    * Deixe os estados do dispositivo ligados e desligados.
    * Siga o princípio da eventual consistência.
    * São totalmente desapersiáveis na nuvem.

* **Organizar dispositivos utilizando etiquetas gémeas do dispositivo:** A solução deve permitir ao operador definir anéis de qualidade ou outros conjuntos de dispositivos baseados em várias estratégias de implantação, como o canário. A organização do dispositivo pode ser implementada dentro da sua solução utilizando etiquetas e consultas gémeas [do](iot-hub-devguide-query-language.md)dispositivo. A organização do dispositivo é necessária para permitir que os lançamentos de configuração se desenrolam de forma segura e precisa.

* **Implementar [configurações automáticas de dispositivos:](./iot-hub-automatic-device-management.md)** As configurações automáticas do dispositivo implementam e monitorizam as alterações na configuração dos grandes conjuntos de dispositivos IoT através de gémeos dispositivos.

   Configurações automáticas do dispositivo alvo conjuntos de gémeos do dispositivo através da **condição alvo,** que é uma consulta sobre etiquetas gémeas do dispositivo ou propriedades relatadas. O **conteúdo-alvo** é o conjunto de propriedades desejadas que serão definidas dentro dos gémeos do dispositivo alvo. O conteúdo-alvo deve alinhar-se com a estrutura dupla do dispositivo definida pelo fabricante/integrador de hardware IoT. As **métricas** são consultas sobre as propriedades reportadas por twin do dispositivo e devem também alinhar-se com a estrutura dupla do dispositivo definida pelo fabricante/integrador de hardware IoT.

   As configurações automáticas do dispositivo funcionam pela primeira vez pouco depois da configuração ser criada e, em seguida, em intervalos de cinco minutos. Beneficiam também do IoT Hub que realiza operações duplas de dispositivos a uma taxa que nunca excederá os [limites de estrangulamento](iot-hub-devguide-quotas-throttling.md) para leituras e atualizações de twin do dispositivo.

* **Utilizar o [Serviço de Provisionamento de Dispositivos](../iot-dps/how-to-manage-enrollments.md):** Os desenvolvedores de soluções devem utilizar o Serviço de Provisionamento de Dispositivos para atribuir etiquetas gémeas do dispositivo a novos dispositivos, de modo a que sejam configuradas automaticamente por **configurações automáticas** de dispositivos que são direcionadas a gémeos com essa etiqueta. 

## <a name="iot-solution-operator"></a>Operador de solução IoT

Seguem-se as melhores práticas para os operadores de soluções IoT que utilizam uma solução IoT construída em Azure:

* **Organizar dispositivos para gestão:** A solução IoT deve definir ou permitir a criação de anéis de qualidade ou outros conjuntos de dispositivos baseados em várias estratégias de implantação, como o canário. Os conjuntos de dispositivos serão utilizados para lançar alterações de configuração e para executar outras operações de gestão de dispositivos à escala.

* **Executar alterações de configuração utilizando um roll out faseado:**  Um roll-out faseado é um processo global pelo qual um operador implementa alterações para um conjunto alargado de dispositivos IoT. O objetivo é fazer mudanças gradualmente para reduzir o risco de fazer mudanças de quebra em larga escala.  O operador deve utilizar a interface da solução para criar uma [configuração automática](./iot-hub-automatic-device-management.md) do dispositivo e a condição de destino deve visar um conjunto inicial de dispositivos (como um grupo canário). O operador deve então validar a alteração de configuração no conjunto inicial de dispositivos.

   Uma vez concluída a validação, o operador atualizará a configuração automática do dispositivo para incluir um conjunto maior de dispositivos. O operador deve também definir a prioridade para que a configuração seja superior às outras configurações atualmente direcionadas para esses dispositivos. O roll out pode ser monitorizado utilizando as métricas comunicadas pela configuração automática do dispositivo.

* **Efetuar revés em caso de erros ou configurações erradas:**  Uma configuração automática do dispositivo que cause erros ou configurações erradas pode ser revertida alterando a **condição de alvo** para que os dispositivos não cumpram mais a condição de alvo. Certifique-se de que outra configuração automática de dispositivo de menor prioridade ainda é direcionada para esses dispositivos. Verifique se o recuo foi bem sucedido ao visualizar as métricas: A configuração de reversão revirada não deve mais apresentar o estado dos dispositivos não visados, e as métricas da segunda configuração devem agora incluir contagens para os dispositivos que ainda estão visados.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a implementação de gémeos dispositivos em [Compreender e usar gémeos dispositivos no IoT Hub](iot-hub-devguide-device-twins.md).

* Caminhe pelos passos para criar, atualizar ou eliminar uma configuração automática do dispositivo em [configurar e monitorizar os dispositivos IoT à escala](./iot-hub-automatic-device-management.md).

* Implementar um padrão de atualização de firmware utilizando gémeos de dispositivo e configurações automáticas de dispositivos em [Tutorial: Implementar um processo de atualização do firmware do dispositivo](tutorial-firmware-update.md).