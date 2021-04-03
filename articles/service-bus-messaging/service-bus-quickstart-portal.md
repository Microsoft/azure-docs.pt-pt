---
title: Use o portal Azure para criar uma fila de ônibus de serviço
description: Neste arranque rápido, você aprende a criar um espaço de nomes de Service Bus e uma fila no espaço de nomes usando o portal Azure.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 79dd751c43443790aafc494d89ad45e3b6705a64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95799217"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>Use o portal Azure para criar um espaço de nome de service bus e uma fila
Este quickstart mostra-lhe como criar um espaço de nomes de Service Bus e uma fila utilizando o [portal Azure.][Azure portal] Também mostra como obter credenciais de autorização que uma aplicação do cliente pode usar para enviar/receber mensagens de/para a fila. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, certifique-se de que tem uma subscrição Azure. Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita][] antes de começar.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou um espaço de nomes de Service Bus e uma fila no espaço de nomes. Para saber como enviar/receber mensagens de/para a fila, consulte um dos seguintes quickstarts na secção **Enviar e receber mensagens.** 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[conta livre]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
