---
title: Utilize o portal Azure para criar um hub IoT | Microsoft Docs
description: Como criar, gerir e eliminar os hubs Azure IoT através do portal Azure. Inclui informações sobre os níveis de preços, escala, segurança e configuração de mensagens.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 0a54d0c85902915d2ee62acd8a1d38b8db8b221c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92536050"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Criar um hub IoT utilizando o portal Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo descreve como criar e gerir centros IoT utilizando o [portal Azure](https://portal.azure.com).

Para utilizar os passos neste tutorial, precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Alterar as definições do hub IoT

Pode alterar as definições de um hub IoT existente depois de ser criado a partir do painel IoT Hub.

![Screenshot mostrando as definições para o hub IoT](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Aqui estão algumas das propriedades que pode definir para um hub IoT:

**Preços e escala**: Pode utilizar esta propriedade para migrar para um nível diferente ou definir o número de unidades IoT Hub. 

**Monitorização de operações**: Ligue ou desligue as diferentes categorias de monitorização, tais como registo de eventos relacionados com mensagens de dispositivo para nuvem ou mensagens nuvem-para-dispositivo.

**Filtro IP**: Especifique uma gama de endereços IP que serão aceites ou rejeitados pelo hub IoT.

**Propriedades**: Fornece a lista de propriedades que pode copiar e usar em outros lugares, tais como o ID de recursos, grupo de recursos, localização, e assim por diante.

### <a name="shared-access-policies"></a>Políticas de acesso partilhado

Também pode visualizar ou modificar a lista de políticas de acesso partilhado clicando em **políticas de acesso partilhado** na secção **Definições.** Estas políticas definem as permissões para dispositivos e serviços para se conectarem ao IoT Hub. 

Clique **em Adicionar** para abrir a lâmina de política de acesso **partilhado.**  Pode introduzir o novo nome da apólice e as permissões que pretende associar a esta política, como mostra o seguinte número:

![Screenshot mostrando a adição de uma política de acesso compartilhado](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* A **leitura do registo** e as políticas de escrita do **Registo** concedem a leitura e a escrita dos direitos de acesso ao registo de identidade. Estas permissões são utilizadas por serviços de nuvem de back-end para gerir identidades do dispositivo. Escolher a opção de escrita escolhe automaticamente a opção de leitura.

* A política **de ligação do Serviço** concede permissão para aceder aos pontos finais do serviço. Esta permissão é utilizada pelos serviços de cloud back-end para enviar e receber mensagens dos dispositivos, bem como para atualizar e ler dados gémeos e módulos do dispositivo.

* A política **de ligação do dispositivo** concede permissões para o envio e receção de mensagens utilizando os pontos finais do lado do dispositivo IoT Hub. Esta permissão é utilizada por dispositivos para enviar e receber mensagens de um hub IoT, atualizar e ler dados gémeos e módulos do dispositivo e realizar uploads de ficheiros.

Clique em **Criar** para adicionar esta política recém-criada à lista existente.

Para obter informações mais detalhadas sobre o acesso concedido por permissões específicas, consulte [as permissões do IoT Hub](./iot-hub-devguide-security.md#iot-hub-permissions).

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>Encaminhamento de mensagens para um hub IoT

Clique em **Roteamento de Mensagens** em **Mensagens** para ver o painel de encaminhamento de mensagens, onde define rotas e pontos finais personalizados para o hub. [O encaminhamento de mensagens](iot-hub-devguide-messages-d2c.md) permite-lhe gerir a forma como os dados são enviados dos seus dispositivos para os seus pontos finais. O primeiro passo é adicionar uma nova rota. Em seguida, pode adicionar um ponto final existente à rota, ou criar um novo dos tipos suportados, como o armazenamento de bolhas. 

![Painel de encaminhamento de mensagens](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Rotas

As rotas são o primeiro separador no painel de encaminhamento de mensagens. Para adicionar uma nova rota, clique em +**Adicionar**. Vê o seguinte ecrã. 

![Screenshot mostrando a adição de uma nova rota](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Diga a sua rota. O nome da rota deve ser único dentro da lista de rotas para aquele centro. 

Para **Endpoint,** pode selecionar um da lista de dropdown ou adicionar um novo. Neste exemplo, já estão disponíveis uma conta de armazenamento e um contentor. Para adicioná-los como ponto final, clique em +**Adicione** ao lado do dropdown endpoint e selecione **Blob Storage**. O ecrã a seguir mostra onde a conta de armazenamento e o recipiente são especificados.

![Screenshot mostrando a adição de um ponto final de armazenamento para a regra de encaminhamento](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Clique **em Escolher um recipiente** para selecionar a conta de armazenamento e o recipiente. Quando tiver selecionado esses campos, regressa ao painel endpoint. Utilize os predefinidos para o resto dos campos e **crie** para criar o ponto final para a conta de armazenamento e adicioná-lo às regras de encaminhamento.

Para **obter informações,** selecione Mensagens de Telemetria do Dispositivo. 

Em seguida, adicione uma consulta de encaminhamento. Neste exemplo, as mensagens que têm um imóvel de aplicação chamado `level` com um valor igual são `critical` encaminhada para a conta de armazenamento.

![Screenshot mostrando salvar uma nova regra de encaminhamento](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Clique **em Guardar** para salvar a regra de encaminhamento. Volte ao painel de encaminhamento de mensagens e a sua nova regra de encaminhamento é apresentada.

### <a name="custom-endpoints"></a>Pontos finais personalizados

Clique no **separador pontos finais personalizados.** Vê quaisquer pontos finais personalizados já criados. A partir daqui, pode adicionar novos pontos finais ou eliminar os pontos finais existentes. 

> [!NOTE]
> Se eliminar uma rota, não elimina os pontos finais atribuídos a essa rota. Para eliminar um ponto final, clique no separador pontos finais personalizados, selecione o ponto final que pretende eliminar e clique em Eliminar.
>

Pode ler mais sobre pontos finais personalizados em [Referência - Pontos finais do hub IoT](iot-hub-devguide-endpoints.md).

Pode definir até 10 pontos finais personalizados para um hub IoT. 

Para ver um exemplo completo de como usar pontos finais personalizados com encaminhamento, consulte [o encaminhamento de mensagens com ioT Hub](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Encontre um hub de IoT específico

Aqui estão duas formas de encontrar um hub IoT específico na sua subscrição:

1. Se conhecer o grupo de recursos a que pertence o hub IoT, clique em **grupos de recursos** e, em seguida, selecione o grupo de recursos da lista. O ecrã do grupo de recursos mostra todos os recursos desse grupo, incluindo os hubs IoT. Clique no centro para o qual está procurando.

2. Clique em **Todos os recursos**. No painel **de todos os recursos,** há uma lista de abandono que não tem padrão para `All types` . Clique na lista de dropdown, desmarcada `Select all` . Encontre `IoT Hub` e verifique. Clique na caixa de lista suspensa para fechá-la e as entradas serão filtradas, mostrando apenas os seus hubs IoT.

## <a name="delete-the-iot-hub"></a>Apagar o hub IoT

Para eliminar um hub Iot, encontre o hub IoT que pretende eliminar e, em seguida, clique no botão **Delete** abaixo do nome do hub IoT.

## <a name="next-steps"></a>Passos seguintes

Siga estes links para saber mais sobre a gestão do Azure IoT Hub:

* [Encaminhamento de mensagens com IoT Hub](tutorial-routing.md)
* [Monitorize o seu hub IoT](monitor-iot-hub.md)
