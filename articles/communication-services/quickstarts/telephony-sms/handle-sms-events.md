---
title: Quickstart - Lidar com eventos DE SMS
titleSuffix: An Azure Communication Services quickstart
description: Saiba como lidar com eventos DE SMS utilizando os Serviços de Comunicação Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: f47a8f1edb4afed41587eac3d539b5ad7cb8180f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488470"
---
# <a name="quickstart-handle-sms-events"></a>Quickstart: Lidar com eventos DE SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Inicie-se com os Serviços de Comunicação Azure utilizando a Azure Event Grid para lidar com eventos DE SMS dos Serviços de Comunicação.

## <a name="about-azure-event-grid"></a>Sobre a grelha de eventos de Azure

[A azure Event Grid](../../../event-grid/overview.md) é um serviço de eventos baseado na nuvem. Neste artigo, você vai aprender a subscrever eventos para [eventos de serviço de comunicação](../../concepts/event-handling.md), e desencadear um evento para ver o resultado. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. Neste artigo, enviaremos os eventos para uma aplicação web que recolhe e exibe as mensagens.

## <a name="prerequisites"></a>Pré-requisitos
- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Um recurso do Serviço de Comunicação Azure. Mais detalhes podem ser encontrados no [Quickstart Criar um Recurso de Comunicação Azure.](../create-communication-resource.md)
- Um número de telefone por SMS habilitado. [Obter um número de telefone.](./get-phone-number.md)

## <a name="setting-up"></a>Configuração

### <a name="enable-event-grid-resource-provider"></a>Ativar o fornecedor de recursos do Event Grid

Se não tiver usado previamente a Grade de Eventos na sua subscrição Azure, poderá ter de registar o fornecedor de recursos da Grelha de Eventos seguindo os passos abaixo:

No portal do Azure:

1. Selecione **Subscrições** no menu esquerdo.
2. Selecione a subscrição que pretende utilizar para Event Grid.
3. No menu esquerdo, em **Definições,** selecione **Fornecedores de Recursos**.
4. Localize **Microsoft.EventGrid**.
5. Se não estiver registado, selecione **Registar**.

Pode demorar algum tempo para o registo terminar. Selecione **Atualizar** para atualizar o estado. Quando **Estado** for **Registado**, está preparado para continuar.

### <a name="event-grid-viewer-deployment"></a>Implementação do espectador de grelha de evento

Para este arranque rápido, usaremos a amostra de [visualização da grelha de eventos Azure](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) para ver eventos em tempo quase real. Isto proporcionará ao utilizador a experiência de um feed em tempo real. Além disso, a carga útil de cada evento também deve estar disponível para inspeção.

## <a name="subscribe-to-the-sms-events-using-web-hooks"></a>Subscreva os eventos SMS utilizando ganchos web

No portal, navegue para o seu Recurso de Serviços de Comunicação Azure que criou. Dentro do recurso serviço de comunicação, selecione **Eventos** a partir do menu esquerdo da página **Serviços de Comunicação.**

:::image type="content" source="./media/handle-sms-events/select-events.png" alt-text="Screenshot mostrando a seleção do botão de subscrição do evento dentro da página de eventos de um recurso.":::

**Prima Adicionar Subscrição de Evento** para introduzir o assistente de criação.

Na página **'Criar Subscrição de** Eventos', insira um **nome** para a subscrição do evento.

Pode subscrever eventos específicos para dizer à Grade de Eventos quais dos eventos SMS que pretende rastrear e para onde enviar os eventos. Selecione os eventos que pretende subscrever no menu suspenso. Para SMS terá a opção de escolher `SMS Received` e `SMS Delivery Report Received` .

Se lhe for solicitado que forneça um **Nome Tópico do Sistema,** sinta-se à vontade para fornecer uma cadeia única. Este campo não tem impacto na sua experiência e é utilizado para fins de telemetria interna.

Confira a lista completa de [eventos suportados pelos Serviços de Comunicação Azure.](../../concepts/event-handling.md)

:::image type="content" source="./media/handle-sms-events/select-events-create-eventsub.png" alt-text="Screenshot mostrando o relatório de entrega de SMS Recebido e SMS Recebeu tipos de eventos sendo selecionados.":::

Selecione **Web Hook** para **o tipo Endpoint**.

:::image type="content" source="./media/handle-sms-events/select-events-create-linkwebhook.png" alt-text="Screenshot mostrando o campo Endpoint Type a ser definido para Web Hook.":::

Para **Endpoint,** clique em **Selecionar um ponto final** e insira o URL da sua aplicação web.

Neste caso, usaremos o URL da amostra de visualização da grelha de [eventos Azure](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) que configuramos mais cedo no arranque rápido. O URL da amostra estará no formato: `https://{{site-name}}.azurewebsites.net/api/updates`

Em seguida, **selecione Confirmar Seleção**.

:::image type="content" source="./media/handle-sms-events/select-events-create-selectwebhook-epadd.png" alt-text="Screenshot mostrando um ponto final do Web Hook.":::

## <a name="viewing-sms-events"></a>Visualização de eventos de SMS

### <a name="triggering-sms-events"></a>Desencadear eventos de SMS

Para ver os desencadeadores do evento, temos de gerar eventos em primeiro lugar.

- `SMS Received` os eventos são gerados quando o número de telefone dos Serviços de Comunicação recebe uma mensagem de texto. Para desencadear um evento, basta enviar uma mensagem do seu telefone para o número de telefone anexado ao seu recurso de Serviços de Comunicação.
- `SMS Delivery Report Received` os eventos são gerados quando envia um SMS a um utilizador utilizando um número de telefone dos Serviços de Comunicação. Para desencadear um evento, é-lhe exigido que ative `Delivery Report` nas opções do [SMS enviado.](../telephony-sms/send.md) Tente enviar uma mensagem para o seu telefone com `Delivery Report` . Completar esta ação incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

Confira a lista completa de [eventos suportados pelos Serviços de Comunicação Azure.](../../concepts/event-handling.md)

### <a name="receiving-sms-events"></a>Receber eventos de SMS

Assim que completares qualquer ação acima, vais reparar nisso `SMS Received` e `SMS Delivery Report Received` os eventos são enviados para o teu ponto final. Estes eventos vão aparecer na amostra de [espectadores de grelha de eventos Azure](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) que criamos no início. Pode premir o ícone dos olhos ao lado do evento para ver toda a carga útil. Os eventos serão assim:

:::image type="content" source="./media/handle-sms-events/sms-received.png" alt-text="Screenshot mostrando o Esquema de Grelha de Evento para um Evento RECEBIDO POR SMS.":::

:::image type="content" source="./media/handle-sms-events/sms-delivery-report-received.png" alt-text="Screenshot mostrando o esquema da grelha de evento para um evento de relatório de entrega de SMS.":::

Saiba mais sobre os esquemas de [eventos e outros conceitos de eventos.](../../concepts/event-handling.md)

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](../create-communication-resource.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a consumir eventos de SMS. Pode receber mensagens SMS criando uma subscrição de Grade de Eventos.

> [!div class="nextstepaction"]
> [Enviar SMS](../telephony-sms/send.md)

Também pode querer:

 - [Conheça os conceitos de manipulação de eventos](../../concepts/event-handling.md)
 - [Saiba mais sobre a Grelha de Eventos](../../../event-grid/overview.md)
