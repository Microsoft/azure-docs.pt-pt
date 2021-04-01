---
title: 'Quickstart: Route Azure Cache para eventos Redis para o ponto final da web com o portal Azure'
description: Use a Azure Event Grid para subscrever a Azure Cache para eventos Redis, enviar os eventos para um Webhook e lidar com os eventos numa aplicação web
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
author: curib
ms.author: cauribeg
ms.openlocfilehash: 5bdd6b0e6f97f7e5a738ab17d68282cf402004b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99055591"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Quickstart: Route Azure Cache para eventos Redis para o ponto final da web com o portal Azure

O Azure Event Grid é um serviço de eventos para a cloud. Neste arranque rápido, você usará o portal Azure para criar um Azure Cache para o caso Redis, subscrever eventos para esse caso, desencadear um evento e ver os resultados. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No entanto, para simplificar este arranque rápido, irá enviar eventos para uma aplicação web que irá recolher e exibir as mensagens. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Quando terminar, verá que os dados do evento foram enviados para a aplicação web.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid Viewer escalando em formato JSON.":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Crie uma cache Azure para a cache do Redis 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever os eventos para a instância cache, vamos criar o ponto final para a mensagem do evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Para simplificar este arranque rápido, irá implementar uma [aplicação web pré-construída](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens do evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar para Azure** no GitHub README para implementar a solução para a sua subscrição. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Desloque-se para o botão Azure.":::

2. Na página **de implantação personalizada,** faça os seguintes passos: 
    1. Para **o grupo de recursos,** selecione o grupo de recursos que criou ao criar a instância cache. Será mais fácil para si limpar depois de terminar o tutorial eliminando o grupo de recursos.  
    2. Para **Nome do Site,** insira um nome para a aplicação web.
    3. Para **hospedar o nome do plano,** insira um nome para o plano de Serviço de Aplicações a utilizar para hospedar a aplicação web.
    4. Selecione a caixa de verificação pois **concordo com os termos e condições acima indicados**. 
    5. Selecione **Comprar**. 
    
    | Definição      | Valor sugerido  | Descrição |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Subscrição** | Desça e selecione a sua subscrição. | A subscrição sob a qual criar esta aplicação web. | 
    | **Grupo de recursos** | Desça e selecione um grupo de recursos, ou **selecione Criar novo** e introduza um novo nome de grupo de recursos. | Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
    | **Nome do Site** | Insira um nome para a sua aplicação web. | Este valor não pode ser vazio. | 
    | **Nome do plano de hospedagem** | Introduza um nome para o plano de Serviço de Aplicações a utilizar para hospedar a aplicação web. | Este valor não pode ser vazio. | 

1. Selecione Alertas (ícone da campainha) no portal e, em seguida, selecione **Vá para o grupo de recursos**. 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Notificação de implantação do portal Azure.":::

4. Na página do **grupo Recursos,** na lista de recursos, selecione a aplicação web que criou. Você também verá o plano do Serviço de Aplicações e a instância cache nesta lista. 

5. Na página **de Serviço de Aplicações** para a sua aplicação web, selecione o URL para navegar no site. O URL deve estar neste formato: `https://<your-site-name>.azurewebsites.net` .

6. Confirme que vê o site, mas ainda não foram publicados quais foram os eventos.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Site vazio do espectador de grelha de evento.":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Subscreva a Cache Azure para a instância Redis

Neste passo, você subscreverá um tópico para dizer à Grade de Eventos quais eventos você quer rastrear, e para onde enviar os eventos.

1. No portal, navegue para o seu cache que criou anteriormente. 
2. Na página **Azure Cache for Redis,** selecione **Eventos** no menu esquerdo. 
3. Selecione **Web Hook**. Está a enviar eventos para a sua aplicação de espectadores usando um web hook para o ponto final. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Página de eventos do portal Azure.":::

4. Na página **'Criar Subscrição de** Eventos', insira o seguinte: 

    | Definição      | Valor sugerido  | Descrição |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Insira um nome para a subscrição do evento. | O valor deve ter entre 3 e 64 caracteres de comprimento. Só pode conter letras, números e traços. | 
    | **Tipos de eventos** | Desça e selecione que tipo de evento(s) você deseja ser empurrado para o seu destino. Para este arranque rápido, vamos escalar o nosso cache exemplo. | Remendar, escalonar, importar e exportar são as opções disponíveis. | 
    | **Tipo de ponto final** | Selecione **Web Hook**. | Manipulador de eventos para receber os seus eventos. | 
    | **Ponto final** | Clique **em Selecionar um ponto final** e insira o URL da sua aplicação web e adicione ao URL da página inicial `api/updates` (por exemplo: ), `https://cache.azurewebsites.net/api/updates` e, em seguida, selecione **Confirmar Seleção**. | Este é o URL da sua aplicação web que criou anteriormente. | 

5. Agora, na página **De Subscrição** de Eventos Criar, selecione **Criar** para criar a subscrição do evento. 

6. Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. Selecione o ícone do olho para expandir os dados do evento. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. A aplicação Web inclui código para validar a subscrição.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Espectador de grelha de eventos Azure.":::

## <a name="send-an-event-to-your-endpoint"></a>Enviar um evento para o seu ponto final

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final. Vamos escalar o teu Azure Cache para o caso Redis.

1. No portal Azure, navegue na cache Azure para a instância Redis e selecione **Scale** no menu esquerdo.

1. Selecione o nível de preços desejado na página **Escala** e clique em **Selecionar**. 

    Pode escalar para um nível de preços diferente com as seguintes restrições:
    
    * Não se pode escalar de um nível de preços mais elevado para um nível de preços mais baixo.
      * Não se pode escalar de uma cache **Premium** até um **Standard** ou uma cache **Básica.**
      * Não se pode escalar de uma cache **standard** até uma cache **básica.**
    * Pode escalar de uma cache **Basic** para uma cache **Standard,** mas não pode mudar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, pode fazer uma operação de escala subsequente ao tamanho pretendido.
    * Não se pode escalar de uma cache **básica** diretamente para uma cache **Premium.** Primeiro, escalone de **Base** para **Standard** numa operação de escala, e depois de **Standard** para **Premium** numa operação de escala subsequente.
    * Não é possível escalar de um tamanho maior até ao tamanho **C0 (250 MB).**
 
    Enquanto a cache está a escalonar para o novo nível de preços, é apresentado um estado **de escala** na Cache Azure para lâmina **Redis.** Quando o escalonamento está completo, o estado muda de **Escala para** **Running**.

1. Acionou o evento e o Event Grid enviou a mensagem para o ponto final que configurou ao subscrever. A mensagem está no formato JSON e contém uma matriz com um ou mais eventos. No exemplo seguinte, a mensagem JSON contém uma matriz com um evento. Veja a sua aplicação web e note que foi recebido um evento **ScalingCompleted.** 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid Viewer escalando em formato JSON.":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia continuar a trabalhar com este evento, não limpe os recursos criados neste quickstart. Caso contrário, elimine os recursos que criou neste arranque rápido.

Selecione o grupo de recursos e selecione **Eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como criar tópicos personalizados e subscrições de eventos, saiba mais sobre o que o Event Grid pode ajudá-lo a fazer:

- [Reagir a Azure Cache para eventos redis](cache-event-grid.md)
- [Sobre o Event Grid](../event-grid/overview.md)

