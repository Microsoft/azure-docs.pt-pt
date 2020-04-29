---
title: Pedidos de dados dos clientes para dispositivos Azure IoT Hub
description: A maioria dos dispositivos geridos no Azure IoT Hub não são pessoais, mas alguns são. Este artigo fala sobre a aptidão para exportar ou apagar dados pessoais de um dispositivo.
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: a05fbf6e1908f88014cd8da99fafb875de033f45
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499326"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo das funcionalidades de pedido de dados dos clientes

O Azure IoT Hub é um serviço de nuvem baseado em Rest API direcionado para clientes empresariais que permite uma comunicação segura e bidirecional entre milhões de dispositivos e um serviço Azure dividido.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Os dispositivos individuais são atribuídos a um identificador de dispositivo (ID do dispositivo) por um administrador de inquilino. Os dados do dispositivo baseiam-se no ID do dispositivo atribuído. A Microsoft não mantém nenhuma informação e não tem acesso a dados que permitam identificar o dispositivo com a correlação do utilizador.

Muitos dos dispositivos geridos no Azure IoT Hub não são dispositivos pessoais, por exemplo um termóstato de escritório ou robô de fábrica. Os clientes podem, no entanto, considerar alguns dispositivos pessoalmente identificáveis e, a seu critério, podem manter os seus próprios métodos de rastreio de ativos ou inventários que liguem dispositivos a indivíduos. O Azure IoT Hub gere e armazena todos os dados associados aos dispositivos como se fossem dados pessoais.

Os administradores de inquilinos podem utilizar o portal Azure ou as APIs rest do serviço para satisfazer pedidos de informação exportando ou apagando dados associados a um ID do dispositivo.

Se utilizar a função de encaminhamento do serviço Azure IoT Hub para encaminhar mensagens de dispositivo para outros serviços, os pedidos de dados devem ser realizados pelo administrador do arrendatário para cada ponto final de encaminhamento, a fim de completar um pedido completo de um determinado dispositivo. Para mais detalhes, consulte a documentação de referência para cada ponto final. Para obter mais informações sobre pontos finais suportados, consulte [Referência - Pontos finais do Hub IoT](iot-hub-devguide-endpoints.md).

Se utilizar a funcionalidade de integração da Rede de Eventos Azure do serviço Azure IoT Hub, os pedidos de dados devem ser realizados pelo administrador do inquilino para cada subscritor destes eventos. Para mais informações, consulte [Reagir aos eventos do IoT Hub utilizando](iot-hub-event-grid.md)a Grelha de Eventos .

Se utilizar a funcionalidade de integração do Azure Monitor do serviço Azure IoT Hub para criar registos de diagnóstico, os pedidos de dados devem ser realizados pelo administrador do inquilino contra os registos armazenados. Para mais informações, consulte [Monitorize a saúde do Hub Azure IoT](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Apagar dados dos clientes

Os administradores de inquilinos podem utilizar a lâmina de dispositivos IoT da extensão Do Hub Azure IoT no portal Azure para eliminar um dispositivo, que elimina os dados associados a esse dispositivo.

Também é possível executar operações de eliminação de dispositivos que utilizem APIs REST. Para mais informações, consulte [Serviço - Eliminar Dispositivo](/rest/api/iothub/service/registrymanager/deletedevice).

## <a name="exporting-customer-data"></a>Exportação de dados dos clientes

Os administradores de inquilinos podem utilizar cópia e pasta dentro do painel de dispositivos IoT da extensão Do Hub Azure IoT no portal Azure para exportar dados associados a um dispositivo.

Também é possível realizar operações de exportação para dispositivos que utilizem APIs REST. Para mais informações, consulte [Serviço - Obter Dispositivo](/rest/api/iothub/service/registrymanager/getdevice).

> [!NOTE]
> Quando utiliza os serviços empresariais da Microsoft, a Microsoft gera algumas informações, conhecidas como registos gerados pelo sistema. Alguns registos gerados pelo sistema Azure IoT Hub não são acessíveis ou exportáveis por administradores de inquilinos. Estes registos constituem ações factuais realizadas no âmbito do serviço e dados de diagnóstico relacionados com dispositivos individuais.

## <a name="links-to-additional-documentation"></a>Links para documentação adicional

Documentação completa para APIs do Serviço De Hub Azure IoT está localizada em APIs do [Serviço IoT Hub](https://docs.microsoft.com/rest/api/iothub/service/configuration).
