---
title: Recursos de pedido de dados do cliente para dispositivos Azure DPS
description: Para dispositivos geridos no Serviço de Provisionamento de Dispositivos Azure (DPS) que são pessoais, este artigo mostra administradores como exportar ou apagar dados pessoais.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 75a2762a0937efbceaa168f8a2d6409e2e3a7ae4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967232"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo das funcionalidades de pedido de dados de clientes

O Azure IoT Hub Device Provisioning Service é um serviço de nuvem baseado em REST API direcionado para clientes empresariais que permite o fornecimento de dispositivos sem emenda e zero-touch para o Azure IoT Hub com segurança que começa no dispositivo e termina com a nuvem.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Os dispositivos individuais são atribuídos a um ID de registo e identificação do dispositivo por um administrador do inquilino. Os dados destes dispositivos são baseados nestes IDs. A Microsoft não mantém nenhuma informação e não tem acesso a dados que permitam a correlação destes dispositivos com um indivíduo.

Muitos dos dispositivos geridos no Serviço de Provisionamento de Dispositivos não são dispositivos pessoais, por exemplo um termóstato de escritório ou robô de fábrica. Os clientes podem, no entanto, considerar que alguns dispositivos são pessoalmente identificáveis e, a seu critério, podem manter o seu próprio ativo ou métodos de rastreio de inventário que liguem os dispositivos a indivíduos. O Serviço de Fornecimento de Dispositivos gere e armazena todos os dados associados aos dispositivos como se fossem dados pessoais.

Os administradores de inquilinos podem usar o portal Azure ou as APIs REST do serviço para satisfazer pedidos de informação exportando ou eliminando dados associados a um ID do dispositivo ou ID de registo.

> [!NOTE]
> Os dispositivos que foram ateados no Azure IoT Hub através do Serviço de Provisionamento de Dispositivos têm dados adicionais armazenados no serviço Azure IoT Hub. Consulte a documentação de referência do [Azure IoT Hub](../iot-hub/iot-hub-customer-data-requests.md) para completar um pedido completo de um determinado dispositivo.

## <a name="deleting-customer-data"></a>Eliminação dos dados dos clientes

Serviço de Fornecimento de Dispositivos armazena inscrições e registos de registo. As inscrições contêm informações sobre dispositivos que podem ser a provisionados, e os registos de registo mostram quais os dispositivos que já passaram pelo processo de provisionamento.

Os administradores de inquilinos podem remover as inscrições do portal Azure, o que também remove quaisquer registos associados.

Para obter mais informações, consulte [Como gerir as inscrições dos dispositivos.](how-to-manage-enrollments.md)

Também é possível efetuar operações de eliminação de matrículas e registos de registos utilizando APIs REST:

* Para eliminar as informações de inscrição de um único dispositivo, pode utilizar [a Inscrição do Dispositivo - Eliminar](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Para eliminar as informações de inscrição de um grupo de dispositivos, pode utilizar [o Grupo de Inscrição do Dispositivo - Eliminar](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Para eliminar informações sobre dispositivos que tenham sido a provisionados, pode utilizar [o Estado de Registo - Apagar o Estado de Registo](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate).

## <a name="exporting-customer-data"></a>Exportação de dados dos clientes

Serviço de Fornecimento de Dispositivos armazena inscrições e registos de registo. As inscrições contêm informações sobre dispositivos que podem ser a provisionados, e os registos de registo mostram quais os dispositivos que já passaram pelo processo de provisionamento.

Os administradores de inquilinos podem ver as matrículas e registos através do portal Azure e exportá-los usando cópia e pasta.

Para obter mais informações sobre como gerir as matrículas, consulte [Como gerir as matrículas de dispositivos.](how-to-manage-enrollments.md)

É igualmente possível efetuar operações de exportação para matrículas e registos de registos utilizando APIs REST:

* Para exportar informações de inscrição para um único dispositivo, pode utilizar [a Inscrição do Dispositivo - Obter](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment).
* Para exportar informações de inscrição para um grupo de dispositivos, pode utilizar [o Grupo de Inscrição de Dispositivos - Get](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* Para exportar informações sobre dispositivos que já foram a provisionados, pode utilizar [o Estado de Registo - Estado de Registo.](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate)

> [!NOTE]
> Quando utiliza os serviços empresariais da Microsoft, a Microsoft gera algumas informações, conhecidas como registos gerados pelo sistema. Alguns registos gerados pelo sistema de fornecimento de dispositivos não são acessíveis nem exportáveis pelos administradores dos inquilinos. Estes registos constituem ações factuais realizadas no âmbito do serviço e dados de diagnóstico relacionados com dispositivos individuais.

## <a name="links-to-additional-documentation"></a>Links para documentação adicional

A documentação completa para o Serviço de Provisionamento de Dispositivos APIs está localizada em [https://docs.microsoft.com/rest/api/iot-dps](/rest/api/iot-dps) .

Funcionalidades de pedido [de dados do cliente](../iot-hub/iot-hub-customer-data-requests.md)Azure IoT Hub .