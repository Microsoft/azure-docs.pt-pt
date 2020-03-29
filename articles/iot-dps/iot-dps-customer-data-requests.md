---
title: Funcionalidades de pedido de dados do cliente para dispositivos DPS Azure
description: Para dispositivos geridos no Serviço de Provisionamento de Dispositivos Azure (DPS) que sejam pessoais, este artigo mostra aos administradores como exportar ou eliminar dados pessoais.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 1dcf1b9f62f94b8f75ef2fe77f3e237a387c53eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890658"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo das funcionalidades de pedido de dados dos clientes

O Serviço de Provisionamento de Dispositivos Hub Azure IoT é um serviço de cloud baseado em Rest API direcionado para clientes empresariais que permite um fornecimento de dispositivos sem emenda e automatizado de dispositivos para o Hub Azure IoT com segurança que começa no dispositivo e termina com a nuvem.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Os dispositivos individuais são atribuídos a um ID de registo e identificação do dispositivo por um administrador de inquilino. Os dados de e sobre estes dispositivos baseiam-se nestes IDs. A Microsoft não mantém nenhuma informação e não tem acesso a dados que permitam a correlação destes dispositivos a um indivíduo.

Muitos dos dispositivos geridos no Serviço de Provisionamento de Dispositivos não são dispositivos pessoais, por exemplo um termóstato de escritório ou robô de fábrica. Os clientes podem, no entanto, considerar alguns dispositivos pessoalmente identificáveis e, a seu critério, podem manter os seus próprios métodos de rastreio de ativos ou inventários que liguem dispositivos a indivíduos. O Serviço de Provisionamento de Dispositivos gere e armazena todos os dados associados aos dispositivos como se fossem dados pessoais.

Os administradores de inquilinos podem utilizar o portal Azure ou as APIs rest do serviço para satisfazer pedidos de informação exportando ou apagando dados associados a um ID de dispositivo ou identificação de registo.

> [!NOTE]
> Os dispositivos que foram aprovisionados no Hub Azure IoT através do Serviço de Provisionamento de Dispositivos têm dados adicionais armazenados no serviço Azure IoT Hub. Consulte a documentação de referência do [Hub Azure IoT](../iot-hub/iot-hub-customer-data-requests.md) para completar um pedido completo de um determinado dispositivo.

## <a name="deleting-customer-data"></a>Apagar dados dos clientes

O Serviço de Provisionamento de Dispositivos armazena inscrições e registos de registo. As inscrições contêm informações sobre dispositivos que podem ser aprovisionados, e os registos de registo mostram quais os dispositivos que já passaram pelo processo de provisionamento.

Os administradores de inquilinos podem remover as matrículas do portal Azure, o que também remove quaisquer registos de registo associados.

Para mais informações, consulte [Como gerir as matrículas do dispositivo](how-to-manage-enrollments.md).

Também é possível efetuar operações de exclusão de inscrições e registos utilizando APIs REST:

* Para eliminar as informações de inscrição para um único dispositivo, pode utilizar a [Inscrição do Dispositivo - Eliminar](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Para eliminar as informações de inscrição de um grupo de dispositivos, pode utilizar o Grupo de [Inscrição do Dispositivo - Eliminar](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Para eliminar informações sobre dispositivos que tenham sido provisionados, pode utilizar o [Estado de Registo - Excluir](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate)o Estado de Registo .

## <a name="exporting-customer-data"></a>Exportação de dados dos clientes

O Serviço de Provisionamento de Dispositivos armazena inscrições e registos de registo. As inscrições contêm informações sobre dispositivos que podem ser aprovisionados, e os registos de registo mostram quais os dispositivos que já passaram pelo processo de provisionamento.

Os administradores de inquilinos podem ver as matrículas e registos através do portal Azure e exportá-los com cópia e pasta.

Para obter mais informações sobre como gerir as matrículas, consulte [Como gerir as matrículas](how-to-manage-enrollments.md)do dispositivo.

Também é possível realizar operações de exportação para inscrições e registos utilizando APIs REST:

* Para exportar informações de inscrição para um único dispositivo, pode utilizar a [Inscrição do Dispositivo - Get](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment).
* Para exportar informações de inscrição para um grupo de dispositivos, pode utilizar o Grupo de Inscrição de [Dispositivos - Get](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* Para exportar informações sobre dispositivos que já foram provisionados, pode utilizar o Estado de [Registo - Obter Estado de Registo](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> Quando utiliza os serviços empresariais da Microsoft, a Microsoft gera algumas informações, conhecidas como registos gerados pelo sistema. Alguns registos gerados pelo sistema de provisionamento de dispositivos não são acessíveis ou exportáveis pelos administradores de inquilinos. Estes registos constituem ações factuais realizadas no âmbito do serviço e dados de diagnóstico relacionados com dispositivos individuais.

## <a name="links-to-additional-documentation"></a>Links para documentação adicional

Documentação completa para APIs do [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps)Serviço de Provisionamento de Dispositivos está localizada em .

[Funcionalidades](../iot-hub/iot-hub-customer-data-requests.md)de pedido de dados de clientes do Azure IoT Hub.