---
title: Solicitações de dados do cliente para dispositivos do Hub IoT do Azure
description: A maioria dos dispositivos gerenciados no Hub IoT do Azure não é pessoal, mas alguns são. Este artigo fala sobre os administradores que podem exportar ou excluir dados pessoais de um dispositivo.
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 29b3ed46ffe7f2236fc63d65ed49385b29b1a08a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889472"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo dos recursos de solicitação de dados do cliente

O Hub IoT do Azure é um serviço de nuvem baseado em API REST direcionado a clientes corporativos que habilitam a comunicação bidirecional segura entre milhões de dispositivos e um serviço do Azure particionado.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Os dispositivos individuais recebem um identificador de dispositivo (ID do dispositivo) por um administrador de locatários. Os dados do dispositivo são baseados na ID de dispositivo atribuída. A Microsoft não mantém nenhuma informação e não tem acesso a dados que permitiriam a identificação do dispositivo para a correlação do usuário.

Muitos dos dispositivos gerenciados no Hub IoT do Azure não são dispositivos pessoais, por exemplo um termostato do Office ou um robô de fábrica. No entanto, os clientes podem considerar que alguns dispositivos sejam identificáveis pessoalmente e, a seu critério, podem manter seus próprios métodos de ativos ou de controle de estoque que unem dispositivos a indivíduos. O Hub IoT do Azure gerencia e armazena todos os dados associados a dispositivos como se eles fossem dados pessoais.

Os administradores de locatários podem usar o portal do Azure ou as APIs REST do serviço para atender às solicitações de informações exportando ou excluindo dados associados a uma ID do dispositivo.

Se você usar o recurso de roteamento do serviço de Hub IoT do Azure para encaminhar mensagens de dispositivo a outros serviços, as solicitações de dados deverão ser executadas pelo administrador de locatários para cada ponto de extremidade de roteamento a fim de concluir uma solicitação completa para um determinado dispositivo. Para obter mais detalhes, consulte a documentação de referência para cada ponto de extremidade. Para obter mais informações sobre pontos de extremidade com suporte, consulte [referência-pontos de extremidade do Hub IOT](iot-hub-devguide-endpoints.md).

Se você usar o recurso integração da grade de eventos do Azure do serviço Hub IoT do Azure, as solicitações de dados deverão ser executadas pelo administrador de locatários para cada Assinante desses eventos. Para obter mais informações, consulte [reagir aos eventos do Hub IOT usando a grade de eventos](iot-hub-event-grid.md).

Se você usar o recurso de integração de Azure Monitor do serviço de Hub IoT do Azure para criar logs de diagnóstico, as solicitações de dados deverão ser executadas pelo administrador de locatários nos logs armazenados. Para obter mais informações, consulte [monitorar a integridade do Hub IOT do Azure](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Excluindo dados do cliente

Os administradores de locatários podem usar a folha dispositivos IoT da extensão do Hub IoT do Azure no portal do Azure para excluir um dispositivo, o que exclui os dados associados a esse dispositivo.

Também é possível executar operações de exclusão para dispositivos que usam APIs REST. Para obter mais informações, consulte [serviço-excluir dispositivo](/rest/api/iothub/service/deletedevice).

## <a name="exporting-customer-data"></a>Exportando dados do cliente

Os administradores de locatários podem utilizar copiar e colar no painel dispositivos IoT da extensão do Hub IoT do Azure no portal do Azure para exportar dados associados a um dispositivo.

Também é possível executar operações de exportação para dispositivos usando APIs REST. Para obter mais informações, consulte [serviço-obter dispositivo](/rest/api/iothub/service/getdevice).

> [!NOTE]
> Quando você usa os serviços corporativos da Microsoft, a Microsoft gera algumas informações, conhecidas como logs gerados pelo sistema. Alguns logs gerados pelo sistema do Hub IoT do Azure não estão acessíveis ou exportáveis por administradores de locatários. Esses logs constituem ações reais executadas no serviço e nos dados de diagnóstico relacionados a dispositivos individuais.

## <a name="links-to-additional-documentation"></a>Links para documentação adicional

A documentação completa para as APIs de serviço do Hub IoT do Azure está localizada em [APIs de serviço do Hub IOT](https://docs.microsoft.com/rest/api/iothub/service).
