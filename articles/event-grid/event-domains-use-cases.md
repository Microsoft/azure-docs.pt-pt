---
title: Utilize casos para domínios de eventos na grelha de eventos Azure
description: Este artigo descreve alguns casos de utilização para a utilização de domínios de eventos na Grelha de Eventos Azure.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 00318fc78053ed55e3599c329746d89d2eee4f99
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204410"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Utilize casos para domínios de eventos na Grelha de Eventos Azure
Este artigo descreve alguns casos de utilização para a utilização de domínios de eventos na Grelha de Eventos Azure. 

## <a name="use-case-1"></a>Use o caso 1 
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>Use o caso 2
Existe um limite de 500 subscrições de eventos ao utilizar tópicos do sistema. Se precisar de mais de 500 subscrições de eventos para um tópico do sistema, poderá utilizar domínios. 

Suponha que criou um tópico de sistema para um Azure Blob Storage e precisa de criar mais de 500 subscrições para o tema, mas não é possível devido à limitação (500 subscrições por tópico). Neste caso, pode utilizar a seguinte solução que utiliza um domínio de evento. 

1. Crie um domínio que possa suportar até 100.000 tópicos e cada tópico de domínio pode ter 500 subscrições de eventos. Este modelo dar-lhe-ia 500 * 100.000 subscrições de eventos. 
1. Crie uma subscrição de função Azure para o tópico do sistema de armazenamento Azure. Quando a função recebe eventos do armazenamento Azure, pode enriquecer e publicar eventos para um tópico de domínio apropriado. Por exemplo, a função poderia analisar o nome do ficheiro blob para determinar o tópico de domínio alvo e publicar o evento para o tópico de domínio. 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="Domínios AzureEvent Grid - use o caso 2":::


## <a name="next-steps"></a>Passos seguintes
Para aprender a configurar domínios de eventos, criar tópicos, criar subscrições de eventos e publicar eventos, consulte [gerir os domínios de eventos.](./how-to-event-domains.md)
