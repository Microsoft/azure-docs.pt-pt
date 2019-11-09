---
title: Recursos de solicitação de dados do cliente para dispositivos do Azure DPS
description: Para dispositivos gerenciados no DPS (serviço de provisionamento de dispositivos) do Azure que são pessoais, este artigo mostra aos administradores como exportar ou excluir dados pessoais.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 1dcf1b9f62f94b8f75ef2fe77f3e237a387c53eb
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890658"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo dos recursos de solicitação de dados do cliente

O serviço de provisionamento de dispositivos no Hub IoT do Azure é um serviço de nuvem baseado em API REST direcionado a clientes corporativos que permite o provisionamento sem toque automático e automatizado de dispositivos no Hub IoT do Azure com segurança que começa no dispositivo e termina com a nuvem.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Os dispositivos individuais recebem uma ID de registro e uma ID de dispositivo por um administrador de locatários. Os dados de e sobre esses dispositivos baseiam-se nessas IDs. A Microsoft não mantém nenhuma informação e não tem acesso aos dados que permitiriam a correlação desses dispositivos em um indivíduo.

Muitos dos dispositivos gerenciados no serviço de provisionamento de dispositivos não são dispositivos pessoais, por exemplo um termostato do Office ou um robô de fábrica. No entanto, os clientes podem considerar que alguns dispositivos sejam identificáveis pessoalmente e, a seu critério, podem manter seus próprios métodos de ativos ou de controle de estoque que unem dispositivos a indivíduos. O serviço de provisionamento de dispositivos gerencia e armazena todos os dados associados a dispositivos como se eles fossem dados pessoais.

Os administradores de locatários podem usar o portal do Azure ou as APIs REST do serviço para atender às solicitações de informações exportando ou excluindo dados associados a uma ID de dispositivo ou ID de registro.

> [!NOTE]
> Os dispositivos que foram provisionados no Hub IoT do Azure por meio do serviço de provisionamento de dispositivos têm dados adicionais armazenados no serviço do Hub IoT do Azure. Consulte a [documentação de referência do Hub IOT do Azure](../iot-hub/iot-hub-customer-data-requests.md) para concluir uma solicitação completa para um determinado dispositivo.

## <a name="deleting-customer-data"></a>Excluindo dados do cliente

O serviço de provisionamento de dispositivos armazena registros de registro e registrar. Os registros contêm informações sobre os dispositivos que têm permissão para serem provisionados e o registro mostra quais dispositivos já passaram pelo processo de provisionamento.

Os administradores de locatários podem remover registros da portal do Azure, e isso também removerá qualquer registro associado.

Para obter mais informações, consulte [como gerenciar registros de dispositivo](how-to-manage-enrollments.md).

Também é possível executar operações de exclusão para registros de registro e registrar usando as APIs REST:

* Para excluir informações de registro de um único dispositivo, você pode usar o [registro de dispositivo-excluir](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Para excluir informações de registro de um grupo de dispositivos, você pode usar o [grupo de registro de dispositivo-excluir](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Para excluir informações sobre dispositivos que foram provisionados, você pode usar o estado [de registro-excluir estado de registro](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate).

## <a name="exporting-customer-data"></a>Exportando dados do cliente

O serviço de provisionamento de dispositivos armazena registros de registro e registrar. Os registros contêm informações sobre os dispositivos que têm permissão para serem provisionados e o registro mostra quais dispositivos já passaram pelo processo de provisionamento.

Os administradores de locatários podem exibir registros de registro e registrados por meio do portal do Azure e exportá-los usando copiar e colar.

Para obter mais informações sobre como gerenciar registros, consulte [como gerenciar registros de dispositivo](how-to-manage-enrollments.md).

Também é possível executar operações de exportação para registros de registro e registrar usando as APIs REST:

* Para exportar informações de registro para um único dispositivo, você pode usar o [registro de dispositivo-Get](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment).
* Para exportar informações de registro para um grupo de dispositivos, você pode usar o [grupo de registro de dispositivos-Get](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* Para exportar informações sobre dispositivos que já foram provisionados, você pode usar o estado [de registro-obter estado de registro](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> Quando você usa os serviços corporativos da Microsoft, a Microsoft gera algumas informações, conhecidas como logs gerados pelo sistema. Alguns logs gerados pelo sistema de serviço de provisionamento de dispositivos não estão acessíveis ou exportáveis por administradores de locatários. Esses logs constituem ações reais executadas no serviço e nos dados de diagnóstico relacionados a dispositivos individuais.

## <a name="links-to-additional-documentation"></a>Links para documentação adicional

A documentação completa para APIs do serviço de provisionamento de dispositivos está localizada em [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps).

[Recursos de solicitação de dados do cliente](../iot-hub/iot-hub-customer-data-requests.md)do Hub IOT do Azure.