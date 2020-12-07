---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 472c1770e2793d8da4e8fc76fafbf3b9073b746d
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763431"
---
## <a name="deploy-and-configure-azure-media-services"></a>Implementar e configurar serviços de mídia Azure

A solução utiliza uma conta Azure Media Services para armazenar os videoclips de deteção de objetos feitos pelo dispositivo IoT Edge gateway.

Quando cria a conta serviços de comunicação:

- Você precisa fornecer um nome de conta, uma subscrição Azure, uma localização, um grupo de recursos e uma conta de armazenamento. Crie uma nova conta de armazenamento utilizando as definições predefinidos enquanto está a criar a conta De Serviços de Mídia.

- Escolha a região **leste dos EUA** para a localização.

- Criar um novo grupo de recursos chamado *lva-rg* na região **leste dos EUA** para os Serviços de Media e contas de armazenamento. Quando termina os tutoriais, é fácil remover todos os recursos eliminando o grupo de recursos *Lva-rg.*

Criar a [conta dos Serviços de Comunicação social no portal Azure.](https://portal.azure.com/?r=1#create/Microsoft.MediaService)

> [!TIP]
> Estes tutoriais usam a região **leste dos EUA** em todos os exemplos. Pode usar a região mais próxima, se preferir.

Tome nota do nome da sua conta **media** no ficheiro *scratchpad.txt.*

Quando a implementação estiver concluída, abra uma Cloud Shell e execute o seguinte comando para recuperar o **ID de recursos** para a sua conta de serviço de mídia:

```azurecli
az resource list --resource-group lva-rg --resource-type microsoft.media/mediaservices --output table --query "[].{ResourceID:id}"
```

:::image type="content" source="media/iot-central-video-analytics-part2/get-resource-id.png" alt-text="Use a Cloud Shell para obter o ID do recurso":::

Tome nota do **ID** de recurso no ficheiro *scratchpad.txt,* utilize este valor mais tarde quando configurar o módulo IoT Edge.

Em seguida, configurar um diretor de serviço Azure Ative Para o seu recurso De Serviços de Mídia. Selecione **o acesso a API** e, em **seguida, a autenticação principal do serviço.** Crie uma nova aplicação Azure Ative Directory com o mesmo nome que o seu recurso Media Services e crie um segredo com uma descrição *IoT Edge Access*.

:::image type="content" source="./media/iot-central-video-analytics-part2/media-service-authentication.png" alt-text="Configure Azure A D app para Azure Media Services":::

Quando o segredo for gerado, desloque-se até à **Cópia das suas credenciais para ligar a secção de aplicação principal do seu serviço.** Em seguida, selecione **JSON**. Pode copiar todas as informações credenciais daqui de uma vez. Tome nota destas informações no ficheiro *scratchpad.txt,* utilize-as mais tarde quando configurar o dispositivo IoT Edge.

> [!WARNING]
> Esta é a sua única oportunidade de ver e salvar o segredo. Se o perderes, tens de gerar outro segredo.

## <a name="create-the-azure-iot-central-application"></a>Criar a aplicação Azure IoT Central

Nesta secção, você cria uma nova aplicação Azure IoT Central a partir de um modelo. Você usa esta aplicação ao longo da série tutorial para construir uma solução completa.

Para criar uma nova aplicação do Azure IoT Central:

1. Navegue para o site do [gestor de aplicações Azure IoT Central.](https://aka.ms/iotcentral)

1. Faça o sômis com as credenciais que utiliza para aceder à sua assinatura Azure.

1. Para começar a criar uma nova aplicação Azure IoT Central, selecione **Nova Aplicação** na página **Build.**

1. Selecione **Varejo**. A página de varejo apresenta vários modelos de aplicações de varejo.

Para criar uma nova aplicação de análise de vídeo:

1. Selecione o **modelo de aplicação de deteção de objetos e movimentos** de vídeo. Este modelo inclui modelos de dispositivo para os dispositivos utilizados no tutorial. O modelo inclui painéis de amostra que os operadores podem usar para executar tarefas como monitorizar e gerir câmaras.

1. Opcionalmente, escolha um nome de **aplicação** amigável. Esta aplicação baseia-se numa loja fictícia chamada Northwind Traders. O tutorial usa o **nome de aplicação** *Northwind Traders video analytics*.

    > [!NOTE]
    > Se utilizar um nome de **aplicação** amigável, ainda deve utilizar um valor único para o **URL** da aplicação.

1. Se tiver uma subscrição Azure, selecione o seu **Diretório,** **subscrição Azure** e **Estados Unidos** como **Localização**. Se não tiver uma subscrição, pode ativar um **teste gratuito de 7 dias** e completar as informações de contacto necessárias. Este tutorial utiliza três dispositivos - duas câmaras e um dispositivo IoT Edge - por isso, se não utilizar o teste gratuito, será cobrado para uso.

    Para obter mais informações sobre diretórios, subscrições e locais, consulte a [criação de um quickstart de aplicação](../articles/iot-central/core/quick-deploy-iot-central.md).

1. Selecione **Criar**.

    :::image type="content" source="./media/iot-central-video-analytics-part2/new-application.png" alt-text="Página da Aplicação do Azure IoT Central":::

### <a name="retrieve-the-configuration-data"></a>Recuperar os dados de configuração

Mais tarde neste tutorial, quando configurar o gateway IoT Edge, precisa de alguns dados de configuração da aplicação IoT Central. O dispositivo IoT Edge necessita desta informação para registar e ligar à aplicação.

Na secção **Administração,** selecione **a sua aplicação** e tome nota do URL de **aplicação** e do ID de **aplicação** no ficheiro *scratchpad.txt:*

:::image type="content" source="./media/iot-central-video-analytics-part2/administration.png" alt-text="O Screenshot mostra o painel de administração de uma página de análise de vídeo com a aplicação U R L e a aplicação I D em destaque.":::

Selecione **Tokens API** e gere um novo token chamado **LVAEdgeToken** para o papel **de Operador:**

:::image type="content" source="./media/iot-central-video-analytics-part2/token.png" alt-text="Gerar Token":::

Tome nota do token no ficheiro *scratchpad.txt* para mais tarde. Depois do diálogo fechar, não pode voltar a ver o símbolo.

Na secção **Administração,** selecione **a ligação do dispositivo** e, em seguida, selecione **dispositivos SAS-IoT**.

Tome nota da **chave primária** para dispositivos no ficheiro *scratchpad.txt.* Você usa este *símbolo de assinatura de acesso compartilhado* do grupo primário mais tarde quando configurar o dispositivo IoT Edge.
