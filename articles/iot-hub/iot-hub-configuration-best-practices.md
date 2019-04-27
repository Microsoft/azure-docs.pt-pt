---
title: Práticas recomendadas com uma configuração de dispositivo para o IoT Hub do Azure | Documentos da Microsoft
description: Saiba mais sobre as melhores práticas para a configuração de dispositivos IoT à escala
author: chrisgre
ms.author: chrisgre
ms.date: 06/24/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: c97395981ea3af90c7b0c590cb049fccc7392304
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734835"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Melhores práticas para configuração do dispositivo dentro de uma solução de IoT

Gerenciamento automático de dispositivos no IoT Hub do Azure automatiza muitas tarefas repetitivas e complexas de gerenciamento de frotas de dispositivo grandes sobre a totalidade dos respetivos ciclos de vida. Este artigo define muitas das práticas recomendadas para as várias funções envolvidas no desenvolvimento e operar uma solução de IoT.

* **Integrador/fabricante de hardware de IoT:** Os fabricantes de hardware de IoT, integradores de montagem de hardware de vários fabricantes, ou os fornecedores fornecendo o hardware para uma implementação de IoT fabricados ou integrados por outros fornecedores. Envolvidos no desenvolvimento e integração de firmware, sistemas de operativos incorporados e software incorporados.

* **Desenvolvedor de soluções de IoT:** O desenvolvimento de uma solução de IoT é geralmente feito por um desenvolvedor de soluções. Este developer pode ser parte de uma equipa interna ou um integrador de sistemas especializado nesta atividade. O desenvolvedor de soluções de IoT pode desenvolver vários componentes da solução IoT partir do zero, integrar vários componentes de código-fonte aberto ou padrão ou personalizar uma [acelerador de solução de IoT](/azure/iot-accelerators/).

* **Operador de solução de IoT:** Depois de implementar a solução de IoT, necessita de operações de longa duração, monitorização, atualizações e manutenção. Estas tarefas podem ser feitas por uma equipe interna que é composta por especialistas em tecnologia de informação, as operações de hardware e as equipes de manutenção e especialistas de domínio que monitorar o comportamento correto da infraestrutura global do IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Compreender a gestão de dispositivos automático para a configuração de dispositivos IoT à escala

Gestão de dispositivos automático inclui os vários benefícios de [dispositivos duplos](iot-hub-devguide-device-twins.md) e [duplos de módulo](iot-hub-devguide-module-twins.md) para sincronizar os Estados desejados e comunicados entre a cloud e dispositivos. [Configurações de dispositivos automático](iot-hub-auto-device-config.md) automaticamente atualizar conjuntos grandes de duplos e resumir o progresso e conformidade. Os passos de alto nível seguintes descrevem como automática gestão de dispositivos é desenvolvido e usado:

* O **fabricante de hardware de IoT/integrador** implementa as funcionalidades de gestão de dispositivos dentro de uma aplicação incorporada com [dispositivos duplos](iot-hub-devguide-device-twins.md). Esses recursos podem incluir atualizações de firmware, instalação de software e atualizações e gerenciamento de configurações.

* O **desenvolvedor de soluções de IoT** implementa a camada de gerenciamento de operações de gestão de dispositivos usando [dispositivos duplos](iot-hub-devguide-device-twins.md) e [configurações de dispositivos automático](iot-hub-auto-device-config.md). A solução deve incluir a definir uma interface de operador para executar tarefas de gestão do dispositivo.

* O **operador de solução de IoT** utiliza a solução de IoT para efetuar tarefas de gestão de dispositivos, especialmente para agrupar dispositivos em conjunto, iniciar alterações de configuração, como atualizações de firmware, monitorizar o progresso e resolver problemas que surgem.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricante/integrador de hardware de IoT

Seguem-se as melhores práticas para fabricantes de hardware e integradores de lidar com o desenvolvimento de software incorporados:

* **Implemente [dispositivos duplos](iot-hub-devguide-device-twins.md):** Dispositivos duplos ativar a sincronização de configuração pretendida da cloud e para o relatório de configuração atual e propriedades do dispositivo. A melhor maneira de implementar dispositivos duplos dentro de aplicativos incorporados é através da [do Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks). Os dispositivos duplos são mais adequados para a configuração porque eles:

    * Suporta a comunicação bidirecional.
    * Permitir que os dois Estados de dispositivo conectado ou desconectado.
    * Acompanhar o princípio da consistência eventual.
    * São totalmente consultável na cloud.

* **Estruture o twin do dispositivo para gestão de dispositivos:** O dispositivo duplo deve ser estruturado, de modo a que as propriedades de gestão do dispositivo estão logicamente agrupadas em conjunto em seções. Se o fizer, irá permitir alterações de configuração para ser isolados sem afetar outras secções do duplo. Por exemplo, crie uma secção de propriedades pretendidas firmware, outra seção de software e uma terceira secção para definições de rede. 

* **Atributos de dispositivo do relatório que são úteis para gestão de dispositivos:** Atributos, como o dispositivo físico marca e modelam, firmware, o sistema operativo, o número de série, e outros identificadores são úteis para relatórios e como parâmetros para o direcionamento de alterações de configuração.

* **Definem os Estados de principais para os relatórios de estado e o progresso:** Estados de nível superior devem ser enumerados para que eles podem ser comunicados para o operador. Por exemplo, uma atualização de firmware poderia informar o estado como atual, transferir, aplicar, em curso e erro. Defina campos adicionais para obter mais informações em cada Estado.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções de IoT

Seguem-se as práticas recomendadas para os desenvolvedores de solução de IoT que estão a criar sistemas com base no Azure:

* **Implemente [dispositivos duplos](iot-hub-devguide-device-twins.md):** Dispositivos duplos ativar a sincronização de configuração pretendida da cloud e para o relatório de configuração atual e propriedades do dispositivo. A melhor maneira de implementar dispositivos duplos dentro de aplicações de soluções na cloud é através da [do Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks). Os dispositivos duplos são mais adequados para a configuração porque eles:

    * Suporta a comunicação bidirecional.
    * Permitir que os dois Estados de dispositivo conectado ou desconectado.
    * Acompanhar o princípio da consistência eventual.
    * São totalmente consultável na cloud.

* **Organize dispositivos utilizando etiquetas do dispositivo duplo:** A solução deve permitir que o operador definir os anéis de qualidade ou outros conjuntos de dispositivos com base em várias estratégias de implantação, como Canárias. Organização de dispositivo pode ser implementada dentro da sua solução utilizando as etiquetas do dispositivo duplo e [consultas](iot-hub-devguide-query-language.md). Organização de dispositivo é necessária para permitir a Vossa configuração com precisão e em segurança.

* **Implemente [configurações de dispositivos automático](iot-hub-auto-device-config.md):** Implantar configurações de dispositivos automática e configuração do monitor é alterado para grandes conjuntos de dispositivos de IoT através de dispositivos duplos. Configurações de automático de dispositivos de destino conjuntos de dispositivos duplos através do **condição, de destino** que é uma consulta no dispositivo etiquetas duplo ou propriedades comunicadas. O **conteúdo de destino** é o conjunto de propriedades pretendidas que serão definidas dentro os twins do dispositivo de destino. O conteúdo de destino, deve alinhar com a estrutura de gémeos de dispositivo definida pelo fabricante/integrador do hardware IoT.

   O **métricas** são consultas no dispositivo duplo propriedades comunicadas e também devem alinhar com a estrutura de gémeos de dispositivo definida pelo fabricante/integrador do hardware IoT. Configurações de dispositivos automática também têm a vantagem de efetuar operações de twin do dispositivo a uma velocidade que nunca vai exceder o IoT Hub a [limites de conjunto](iot-hub-devguide-quotas-throttling.md) para leituras de twin do dispositivo e atualizações.

* **Utilize o [serviço aprovisionamento de dispositivos](../iot-dps/how-to-manage-enrollments.md):** Os desenvolvedores de solução devem utilizar o serviço de aprovisionamento de dispositivos para atribuir etiquetas do dispositivo duplo para novos dispositivos, que irá ser configurados automaticamente pelo **configurações de dispositivos automático** que se destinam a ser duplos com a mesma. 

## <a name="iot-solution-operator"></a>Operador de solução de IoT

Seguem-se as práticas recomendadas para operadores de solução de IoT que utilizar uma solução de IoT criada no Azure:

* **Organize dispositivos para gestão:** A solução de IoT deve definir ou permitir a criação de anéis de qualidade ou outros conjuntos de dispositivos com base em várias estratégias de implantação, como Canárias. Os conjuntos de dispositivos serão utilizados para distribuir as alterações de configuração e executar outras operações de gestão de dispositivos à escala.

* **Efetue alterações à configuração utilizando uma implementação faseada:**  Implementação faseada é um processo geral no qual um operador implementa as alterações a um conjunto essa de dispositivos IoT. O objetivo é fazer alterações gradualmente para reduzir o risco de efetuar alterações significativas de grande escala.  O operador deve usar a interface da solução para criar uma [configuração do dispositivo automática](iot-hub-auto-device-config.md) e a condição de destino deve visar um conjunto inicial de dispositivos (por exemplo, um grupo de proteção). O operador deve, em seguida, validar a alteração da configuração no conjunto inicial de dispositivos.

   Depois de concluída a validação, o operador irá atualizar a configuração do dispositivo automática para incluir um conjunto maior de dispositivos. O operador também deve definir a prioridade para a configuração seja maior do que outras configurações atualmente visadas para esses dispositivos. A implementação pode ser monitorizada com as métricas informadas pela configuração do dispositivo automática.

* **Execute reversões em caso de erros ou erros de configuração:**  Uma configuração do dispositivo automática que causa erros ou configurações incorretas pode ser revertida, alterando a **direcionamento condição** para que os dispositivos já não cumprem a condição de destino. Certifique-se de que outra configuração do dispositivo automática de menor prioridade ainda está direcionada para esses dispositivos. Certifique-se de que a reversão foi concluída com êxito ao visualizar as métricas: A configuração de revertidas deve deixar de Mostrar estado dos dispositivos untargeted e métricas da configuração segundo devem incluir agora contagens para os dispositivos que ainda são visados.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre a implementação de dispositivos duplos no [compreender e utilizar dispositivos duplos no IoT Hub](iot-hub-devguide-device-twins.md).

* Siga os passos para criar, atualizar ou eliminar uma configuração de automático de dispositivos na [configurar e monitorizar dispositivos de IoT em escala](iot-hub-auto-device-config.md).

* Implementar um padrão de atualização de firmware a utilizar dispositivos duplos e configurações de automático de dispositivos no [Tutorial: Implementar um processo de atualização de firmware do dispositivo](tutorial-firmware-update.md).
