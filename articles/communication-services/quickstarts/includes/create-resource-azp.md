---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 051f903c0b203045886b80f72d868adc1fa85f73
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886119"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/dotnet/)

## <a name="create-azure-communication-resource"></a>Criar recurso de comunicação Azure

Para criar um recurso Azure Communication Services, inscreva-se primeiro no [portal Azure](https://portal.azure.com). No canto superior esquerdo da página, selecione **+ Criar um recurso**. 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Screenshot realçando a criação de um botão de recurso no portal Azure.":::

Introduza **a Comunicação** na **entrada 'Pesquisar o Mercado'** ou na barra de pesquisa no topo do portal.

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="Screenshot mostrando uma busca por serviços de comunicação na barra de pesquisa.":::

Selecione **serviços de comunicação** nos resultados e, em seguida, selecione **Adicionar**.

:::image type="content" source="../media/create-a-communication-resource/add-communication-portal.png" alt-text="Screenshot mostrando o painel Azure, realçando o botão adicionar.":::

Pode agora configurar o seu recurso de Serviços de Comunicação. Na primeira página do processo de criação, será solicitado que especifique:

* A subscrição
* O grupo de recursos (pode criar um novo ou escolher um grupo de recursos existente)
* O nome do recurso serviços de comunicação
* A geografia com que o recurso será associado

No passo seguinte, pode atribuir etiquetas ao recurso. As etiquetas podem ser usadas para organizar os seus recursos Azure. Consulte a [documentação de marcação de recursos](../../../azure-resource-manager/management/tag-resources.md) para obter mais informações sobre etiquetas.

Finalmente, pode rever a sua configuração e **criar** o recurso. Note que a colocação levará alguns minutos para ser concluída.

## <a name="manage-your-communication-services-resource"></a>Gerir o seu recurso de Serviços de Comunicação

Para gerir o seu recurso de Serviços de Comunicação, vá ao [portal Azure](https://portal.azure.com)e procure e selecione **Serviços de Comunicação Azure.**

Na página **serviços de comunicação,** selecione o nome do seu recurso.

A página **'Vista Geral'** para o seu recurso contém opções para gestão básica como navegar, parar, iniciar, reiniciar e eliminar. Pode encontrar mais opções de configuração no menu esquerdo da sua página de recursos.