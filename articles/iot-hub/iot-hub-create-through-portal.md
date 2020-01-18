---
title: Usar o portal do Azure para criar um hub IoT | Microsoft Docs
description: Como criar, gerenciar e excluir os hubs IoT do Azure por meio do portal do Azure. Inclui informações sobre tipos de preço, escala, segurança e configuração de mensagens.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: c43c142b22709d42416b2dd14dfc78812970916a
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169612"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Criar um hub IoT usando o portal do Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo descreve como criar e gerenciar hubs IoT usando o [portal do Azure](https://portal.azure.com).

Para usar as etapas deste tutorial, você precisa de uma assinatura do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Alterar as configurações do Hub IoT

Você pode alterar as configurações de um hub IoT existente após sua criação no painel do Hub IoT.

![Captura de tela mostrando as configurações do Hub IoT](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Aqui estão algumas das propriedades que você pode definir para um hub IoT:

**Preço e escala**: você pode usar essa propriedade para migrar para uma camada diferente ou definir o número de unidades do Hub IOT. 

**Monitoramento de operações**: ativar ou desativar diferentes categorias de monitoramento, como registro em log de eventos relacionados a mensagens do dispositivo para a nuvem ou a mensagens da nuvem para o dispositivo.

**Filtro IP**: especifique um intervalo de endereços IP que serão aceitos ou rejeitados pelo Hub IOT.

**Propriedades**: fornece a lista de propriedades que você pode copiar e usar em outro lugar, como a ID do recurso, o grupo de recursos, o local e assim por diante.

### <a name="shared-access-policies"></a>Políticas de acesso partilhado

Você também pode exibir ou modificar a lista de políticas de acesso compartilhado clicando em **políticas de acesso compartilhado** na seção **configurações** . Essas políticas definem as permissões para que dispositivos e serviços se conectem ao Hub IoT. 

Clique em **Adicionar** para abrir a folha **Adicionar uma política de acesso compartilhado** .  Você pode inserir o nome da nova política e as permissões que deseja associar a essa política, conforme mostrado na figura a seguir:

![Captura de tela mostrando a adição de uma política de acesso compartilhado](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* As políticas de **leitura** e **gravação** do registro concedem direitos de acesso de leitura e gravação ao registro de identidade. Essas permissões são usadas pelos serviços de nuvem de back-end para gerenciar identidades de dispositivo. A escolha da opção de gravação escolhe automaticamente a opção de leitura.

* A política de **conexão de serviço** concede permissão para acessar pontos de extremidade de serviço. Essa permissão é usada pelos serviços de nuvem de back-end para enviar e receber mensagens de dispositivos, bem como para atualizar e ler dados de dispositivo e do módulo.

* A política de **conexão de dispositivo** concede permissões para enviar e receber mensagens usando os pontos de extremidade do lado do dispositivo do Hub IOT. Essa permissão é usada pelos dispositivos para enviar e receber mensagens de um hub IoT, atualizar e ler os dados do dispositivo e do módulo e executar carregamentos de arquivos.

Clique em **criar** para adicionar essa política recém-criada à lista existente.

Para obter informações mais detalhadas sobre o acesso concedido por permissões específicas, consulte [permissões do Hub IOT](./iot-hub-devguide-security.md#iot-hub-permissions).

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no Hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>Roteamento de mensagens para um hub IoT

Clique em **Roteamento de mensagens** em **mensagens** para ver o painel roteamento de mensagens, onde você define rotas e pontos de extremidade personalizados para o Hub. O [Roteamento de mensagens](iot-hub-devguide-messages-d2c.md) permite que você gerencie como os dados são enviados de seus dispositivos para seus pontos de extremidade. A primeira etapa é adicionar uma nova rota. Em seguida, você pode adicionar um ponto de extremidade existente à rota ou criar um novo dos tipos com suporte, como o armazenamento de BLOBs. 

![Painel de roteamento de mensagens](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Rotas

Rotas é a primeira guia no painel roteamento de mensagens. Para adicionar uma nova rota, clique em +**Adicionar**. Você verá a tela a seguir. 

![Captura de tela mostrando a adição de uma nova rota](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Nomeie o Hub. O nome deve ser exclusivo na lista de rotas para esse Hub. 

Para **ponto de extremidade**, você pode selecionar um na lista suspensa ou adicionar um novo. Neste exemplo, uma conta de armazenamento e um contêiner já estão disponíveis. Para adicioná-los como um ponto de extremidade, clique em +**Adicionar** ao lado da lista suspensa ponto de extremidade e selecione **armazenamento de BLOBs**. A tela a seguir mostra onde a conta de armazenamento e o contêiner são especificados.

![Captura de tela mostrando a adição de um ponto de extremidade de armazenamento para a regra de roteamento](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Clique em **escolher um contêiner** para selecionar a conta de armazenamento e o contêiner. Quando você seleciona esses campos, ele retorna para o painel ponto de extremidade. Use os padrões para o restante dos campos e **crie** para criar o ponto de extremidade para a conta de armazenamento e adicioná-lo às regras de roteamento.

Para **fonte de dados**, selecione mensagens de telemetria do dispositivo. 

Em seguida, adicione uma consulta de roteamento. Neste exemplo, as mensagens que têm uma propriedade de aplicativo chamada `level` com um valor igual a `critical` são roteadas para a conta de armazenamento.

![Captura de tela mostrando salvar uma nova regra de roteamento](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Clique em **salvar** para salvar a regra de roteamento. Você retorna ao painel roteamento de mensagens e a nova regra de roteamento é exibida.

### <a name="custom-endpoints"></a>Pontos finais personalizados

Clique na guia **pontos de extremidade personalizados** . Você verá todos os pontos de extremidade personalizados já criados. A partir daqui, você pode adicionar novos pontos de extremidade ou excluir pontos de extremidade existentes. 

> [!NOTE]
> Se você excluir uma rota, ela não excluirá os pontos de extremidade atribuídos a essa rota. Para excluir um ponto de extremidade, clique na guia pontos de extremidade personalizados, selecione o ponto de extremidades que você deseja excluir e clique em excluir.
>

Você pode ler mais sobre pontos de extremidade personalizados em [referência – pontos de extremidade do Hub IOT](iot-hub-devguide-endpoints.md).

Você pode definir até 10 pontos de extremidade personalizados para um hub IoT. 

Para ver um exemplo completo de como usar pontos de extremidade personalizados com o roteamento, consulte [Roteamento de mensagens com o Hub IOT](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Localizar um hub IoT específico

Aqui estão duas maneiras de encontrar um hub IoT específico em sua assinatura:

1. Se você souber o grupo de recursos ao qual o Hub IoT pertence, clique em **grupos de recursos**e selecione o grupo de recursos na lista. A tela grupo de recursos mostra todos os recursos nesse grupo, incluindo os hubs IoT. Clique no Hub para o qual você está olhando.

2. Clique em **Todos os recursos**. No painel **todos os recursos** , há uma lista suspensa que usa como padrão `All types`. Clique na lista suspensa, desmarque `Select all`. Localize `IoT Hub` e marque-o. Clique na caixa de listagem suspensa para fechá-la e as entradas serão filtradas, mostrando apenas os hubs IoT.

## <a name="delete-the-iot-hub"></a>Excluir o Hub IoT

Para excluir um hub IOT, localize o Hub IoT que você deseja excluir e, em seguida, clique no botão **excluir** abaixo do nome do Hub IOT.

## <a name="next-steps"></a>Passos seguintes

Siga estes links para saber mais sobre como gerenciar o Hub IoT do Azure:

* [Roteamento de mensagens com o Hub IoT](tutorial-routing.md)
* [Métricas do Hub IoT](iot-hub-metrics.md)
* [Monitorização de operações](iot-hub-operations-monitoring.md)