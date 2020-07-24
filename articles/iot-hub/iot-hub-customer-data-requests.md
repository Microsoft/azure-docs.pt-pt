---
title: Pedidos de dados do cliente para dispositivos Azure IoT Hub
description: A maioria dos dispositivos geridos no Azure IoT Hub não são pessoais, mas alguns são. Este artigo fala sobre a formação de administradores poderem exportar ou eliminar dados pessoais de um dispositivo.
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 0bb7b91cd31761b801ad469b8ce556b29e09fd3a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074706"
---
# <a name="customer-data-request-features-for-azure-iot-hub-devices"></a>Recursos de pedido de dados do cliente para dispositivos Azure IoT Hub

O Azure IoT Hub é um serviço de nuvem baseado em API com base em REST direcionado para clientes empresariais que permite uma comunicação segura e bidcional entre milhões de dispositivos e um serviço Azure dividido.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Os dispositivos individuais são atribuídos a um identificador de dispositivo (ID do dispositivo) por um administrador do inquilino. Os dados do dispositivo baseiam-se na identificação do dispositivo atribuído. A Microsoft não mantém nenhuma informação e não tem acesso a dados que permitam a correlação do dispositivo com a correlação do utilizador.

Muitos dos dispositivos geridos no Azure IoT Hub não são dispositivos pessoais, por exemplo um termóstato de escritório ou robô de fábrica. Os clientes podem, no entanto, considerar que alguns dispositivos são pessoalmente identificáveis e, a seu critério, podem manter o seu próprio ativo ou métodos de rastreio de inventário que liguem os dispositivos a indivíduos. O Azure IoT Hub gere e armazena todos os dados associados aos dispositivos como se fossem dados pessoais.

Os administradores de inquilinos podem usar o portal Azure ou as APIs REST do serviço para satisfazer pedidos de informação exportando ou eliminando dados associados a um ID do dispositivo.

Se utilizar a função de encaminhamento do serviço Azure IoT Hub para encaminhar mensagens do dispositivo para outros serviços, então os pedidos de dados devem ser realizados pelo administrador do arrendatário para cada ponto final de encaminhamento, a fim de completar um pedido completo de um determinado dispositivo. Para mais detalhes, consulte a documentação de referência para cada ponto final. Para obter mais informações sobre os pontos finais suportados, consulte [pontos finais Reference - IoT Hub](iot-hub-devguide-endpoints.md).

Se utilizar a funcionalidade de integração da Azure Event Grid do serviço Azure IoT Hub, então os pedidos de dados devem ser realizados pelo administrador do arrendatário para cada subscritor destes eventos. Para obter mais informações, consulte [reagir aos eventos do IoT Hub utilizando a Grade de Eventos.](iot-hub-event-grid.md)

Se utilizar a função de integração do Azure Monitor do serviço Azure IoT Hub para criar registos de diagnóstico, então os pedidos de dados devem ser realizados pelo administrador do arrendatário contra os registos armazenados. Para mais informações, consulte [Monitorar a saúde do Azure IoT Hub](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Eliminação dos dados dos clientes

Os administradores de inquilinos podem usar a lâmina de dispositivos IoT da extensão Azure IoT Hub no portal Azure para eliminar um dispositivo, que elimina os dados associados a esse dispositivo.

Também é possível executar operações de eliminação para dispositivos que utilizem APIs REST. Para obter mais informações, consulte [Serviço - Eliminar Dispositivo](/rest/api/iothub/service/registrymanager/deletedevice).

## <a name="exporting-customer-data"></a>Exportação de dados dos clientes

Os administradores de inquilinos podem utilizar cópias e pastas dentro do painel de dispositivos IoT da extensão Azure IoT Hub no portal Azure para exportar dados associados a um dispositivo.

É igualmente possível realizar operações de exportação para dispositivos que utilizem APIs REST. Para mais informações, consulte [Serviço - Obter Dispositivo.](/rest/api/iothub/service/registrymanager/getdevice)

> [!NOTE]
> Quando utiliza os serviços empresariais da Microsoft, a Microsoft gera algumas informações, conhecidas como registos gerados pelo sistema. Alguns registos gerados pelo sistema Azure IoT Hub não são acessíveis ou exportáveis pelos administradores de inquilinos. Estes registos constituem ações factuais realizadas no âmbito do serviço e dados de diagnóstico relacionados com dispositivos individuais.

## <a name="links-to-additional-documentation"></a>Links para documentação adicional

A documentação completa para apis do Serviço Azure IoT Hub está localizada nas [APIs do IoT Hub Service](https://docs.microsoft.com/rest/api/iothub/service/configuration).
