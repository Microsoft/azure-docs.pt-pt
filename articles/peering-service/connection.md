---
title: Conexão Azure Peering Service
description: Saiba mais sobre a ligação do Serviço de Peering microsoft Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: df337736a676c40f1345310afb9a2e685ddc1d5c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84872023"
---
# <a name="peering-service-connection"></a>Conexão de serviço de observação

Uma ligação normalmente refere-se a um conjunto de informações lógicas, identificando um Serviço de Observação. É definido especificando os seguintes atributos:

- Nome Lógico
- Parceiro de conectividade
- Localização física do cliente
- Prefixos IP

O cliente pode estabelecer uma única ligação ou múltiplas ligações de acordo com o requisito. Uma ligação também é usada como uma unidade de coleção de telemetria. Por exemplo, para optar por alertas de telemetria, o cliente deve definir a ligação que será monitorizada.

> [!Note]
> Quando se inscreve no Serviço de Peering, analisamos a sua telemetria Windows e Office 365 de forma a fornecer-lhe medições de latência para os seus prefixos selecionados.
>Para obter mais informações sobre a telemetria de ligação, consulte a [telemetria de ligação de serviço de pares](connection-telemetry.md).
>

>## <a name="how-to-register-a-connection"></a>Como registar uma ligação?

**Cenário** - Digamos que uma sucursal está espalhada por diferentes localizações geográficas, como mostra a figura abaixo. Aqui, o cliente é obrigado a fornecer um nome lógico, nome do Fornecedor de Serviços (SP), localização física do cliente e prefixos IP que são (pertencentes ao cliente ou atribuídos pelo Fornecedor de Serviços) associados a uma única ligação. Este processo deve ser repetido para registar o Serviço de Observação para ligações geo-redundantes separadas.

![Ligações Geo Redundantes](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> A filtragem de nível estatal é considerada para a localização física do cliente quando a ligação está geo-localizada nos Estados Unidos.
>

## <a name="next-steps"></a>Próximos passos

Para aprender passo a passo processo sobre como registar a ligação do Serviço de Peering, consulte [o Serviço de Permôns registador utilizando o portal Azure](azure-portal.md).

Para saber mais sobre a telemetria de conexão peering Service, consulte [a telemetria de conexão peering service](connection-telemetry.md).

Para medir a telemetria, consulte [a telemetria de ligação medida](measure-connection-telemetry.md).
