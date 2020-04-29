---
title: Utilize o portal Azure para criar um Hub IoT [ Hub] Microsoft Docs
description: Como criar, gerir e eliminar os hubs Azure IoT através do portal Azure. Inclui informações sobre níveis de preços, escalação, segurança e configuração de mensagens.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: c43c142b22709d42416b2dd14dfc78812970916a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79284736"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Crie um hub IoT usando o portal Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo descreve como criar e gerir centros IoT usando o [portal Azure](https://portal.azure.com).

Para utilizar os passos deste tutorial, precisa de uma subscrição Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Alterar as definições do hub IoT

Pode alterar as definições de um hub IoT existente depois de ser criado a partir do painel IoT Hub.

![Screenshot mostrando as definições para o hub IoT](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Aqui estão algumas das propriedades que você pode definir para um hub IoT:

**Preços e escala**: Pode utilizar esta propriedade para migrar para um nível diferente ou definir o número de unidades IoT Hub. 

**Monitorização de operações**: Ligue ou desligue as diferentes categorias de monitorização, tais como o registo de eventos relacionados com mensagens dispositivo-nuvem ou mensagens cloud-to-device.

**Filtro IP**: Especifique uma gama de endereços IP que serão aceites ou rejeitados pelo hub IoT.

**Propriedades**: Fornece a lista de propriedades que pode copiar e utilizar em outros lugares, tais como o ID de recursos, grupo de recursos, localização, e assim por diante.

### <a name="shared-access-policies"></a>Políticas de acesso partilhado

Também pode visualizar ou modificar a lista de políticas de acesso partilhado clicando em políticas de **acesso partilhado** na secção **Definições.** Estas políticas definem as permissões para que dispositivos e serviços se conectem ao IoT Hub. 

Clique em **Adicionar** para abrir a lâmina de política de **acesso partilhado.**  Pode introduzir o novo nome de política e as permissões que pretende associar a esta política, como mostra a seguinte figura:

![Screenshot mostrando adicionar uma política de acesso compartilhado](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* As políticas de leitura e **escrita** do **Registo** concedem direitos de leitura e de acesso ao registo de identidade. Estas permissões são usadas por serviços de nuvem de back-end para gerir identidades do dispositivo. Escolher a opção de escrita escolhe automaticamente a opção de leitura.

* A política **de ligação** do Serviço concede permissão para aceder a pontos finais do serviço. Esta permissão é utilizada pelos serviços de nuvem de back-end para enviar e receber mensagens de dispositivos, bem como para atualizar e ler dados gémeos e de dois módulos do dispositivo.

* A política de **ligação do Dispositivo** concede permissões para o envio e receção de mensagens utilizando os pontos finais do dispositivo IoT Hub. Esta permissão é utilizada por dispositivos para enviar e receber mensagens de um hub IoT, atualizar e ler dados gémeos e módulos do dispositivo e executar uploads de ficheiros.

Clique em **Criar** para adicionar esta política recém-criada à lista existente.

Para obter informações mais detalhadas sobre o acesso concedido por permissões específicas, consulte [as permissões do IoT Hub](./iot-hub-devguide-security.md#iot-hub-permissions).

## <a name="register-a-new-device-in-the-iot-hub"></a>Registe um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>Message Routing para um hub IoT

Clique em **Message Routing** em **Mensagens** para ver o painel de encaminhamento de mensagens, onde define rotas e pontos finais personalizados para o hub. [O encaminhamento de mensagens](iot-hub-devguide-messages-d2c.md) permite-lhe gerir a forma como os dados são enviados dos seus dispositivos para os seus pontos finais. O primeiro passo é adicionar uma nova rota. Em seguida, você pode adicionar um ponto final existente à rota, ou criar um novo dos tipos suportados, como o armazenamento de bolhas. 

![Painel de encaminhamento de mensagens](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Rotas

As rotas são o primeiro separador no painel de encaminhamento de mensagens. Para adicionar uma nova rota, clique +**Adicionar**. Vê o seguinte ecrã. 

![Screenshot mostrando adicionar uma nova rota](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Diga o nome do seu centro. O nome deve ser único dentro da lista de rotas para aquele centro. 

Para **Endpoint,** pode selecionar um da lista de dropdown, ou adicionar um novo. Neste exemplo, já está disponível uma conta de armazenamento e um contentor. Para adicioná-las como ponto final, clique +**Adicione** ao lado do dropdown endpoint e selecione **Blob Storage**. O ecrã seguinte mostra onde a conta de armazenamento e o recipiente são especificados.

![Screenshot mostrando adicionar um ponto final de armazenamento para a regra de encaminhamento](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Clique **em escolher um recipiente** para selecionar a conta de armazenamento e o recipiente. Quando selecionou esses campos, regressa ao painel Endpoint. Utilize os predefinições para o resto dos campos e **crie** para criar o ponto final para a conta de armazenamento e adicioná-lo às regras de encaminhamento.

Para **obter dados,** selecione Mensagens de Telemetria do Dispositivo. 

Em seguida, adicione uma consulta de encaminhamento. Neste exemplo, as mensagens que `level` têm um imóvel de aplicação chamado com um valor igual ao `critical` são encaminhados para a conta de armazenamento.

![Screenshot mostrando salvar uma nova regra de encaminhamento](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Clique em **Guardar** para salvar a regra de encaminhamento. Volta sê-lo ao painel de encaminhamento de mensagens e é apresentada a sua nova regra de encaminhamento.

### <a name="custom-endpoints"></a>Pontos finais personalizados

Clique no separador **de pontos finais Personalizado.** Você vê quaisquer pontos finais personalizados já criados. A partir daqui, pode adicionar novos pontos finais ou eliminar os pontos finais existentes. 

> [!NOTE]
> Se apagar uma rota, não apaga os pontos finais atribuídos a essa rota. Para eliminar um ponto final, clique no separador 'pontos finais' personalizado, selecione o ponto final que pretende eliminar e clique em Eliminar.
>

Pode ler mais sobre pontos finais personalizados em [Referência - Pontos finais do hub IoT](iot-hub-devguide-endpoints.md).

Pode definir até 10 pontos finais personalizados para um hub IoT. 

Para ver um exemplo completo de como usar pontos finais personalizados com encaminhamento, consulte [o encaminhamento de mensagens com o Hub IoT](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Encontre um hub ioT específico

Aqui estão duas maneiras de encontrar um hub ioT específico na sua subscrição:

1. Se conhecer o grupo de recursos a que pertence o hub IoT, clique em **grupos de Recursos**e, em seguida, selecione o grupo de recursos da lista. O ecrã do grupo de recursos mostra todos os recursos desse grupo, incluindo os hubs IoT. Clique no centro para o qual está procurando.

2. Clique em **Todos os recursos**. No painel **de todos os recursos,** há uma `All types`lista de abandono que não se aplica a . Clique na lista de dropdown, desfaça- se `Select all`. Encontre `IoT Hub` e verifique. Clique na caixa da lista de dropdown para fechá-la e as entradas serão filtradas, mostrando apenas os seus hubs IoT.

## <a name="delete-the-iot-hub"></a>Eliminar o hub IoT

Para eliminar um hub de iot, encontre o hub IoT que pretende eliminar e, em seguida, clique no botão **Eliminar** abaixo do nome do hub IoT.

## <a name="next-steps"></a>Passos seguintes

Siga estes links para saber mais sobre a gestão do Azure IoT Hub:

* [Encaminhamento de mensagem com Hub IoT](tutorial-routing.md)
* [Métricas do Hub IoT](iot-hub-metrics.md)
* [Monitorização de operações](iot-hub-operations-monitoring.md)