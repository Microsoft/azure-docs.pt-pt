---
author: ericmitt
ms.author: ericmitt
ms.service: iot-pnp
ms.topic: include
ms.date: 07/13/2020
ms.openlocfilehash: 3ab9161c006a88a254b247a921512698ed46fa95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87352814"
---
1. Abra o explorador de IoT Azure.

1. Na página de **hubs IoT,** se ainda não adicionou uma ligação ao seu hub IoT, selecione **+ Adicione a ligação**. Introduza a cadeia de ligação para o hub IoT que criou anteriormente e selecione **Save**.

1. Na página **IoT Plug and Play Settings,** **selecione + Adicione > pasta Local** e selecione a pasta de *modelos* locais onde guardou os ficheiros do seu modelo.

1. Na página de **hubs IoT,** clique no nome do hub com o que pretende trabalhar. Vê uma lista de dispositivos registados no centro de IoT.

1. Clique no **ID** do dispositivo que criou anteriormente.

1. O menu à esquerda mostra os diferentes tipos de informação disponíveis para o dispositivo.

1. Selecione **componentes IoT Plug e Play** para ver as informações do modelo para o seu dispositivo.

1. Pode ver os diferentes componentes do dispositivo. O componente predefinido e quaisquer outros. Selecione um componente para trabalhar.

1. Selecione a página **telemetria** e, em seguida, **selecione Iniciar** a visualização dos dados de telemetria que o dispositivo está a enviar para este componente.

1. Selecione a página **Propriedades (apenas para leitura)** para ver as propriedades apenas de leitura reportadas para este componente.

1. Selecione a página **Propriedades (writable)** para ver as propriedades que pode atualizar para este componente.

1. Selecione uma propriedade pelo seu **nome,** insira um novo valor para ele e selecione **Update desired value**.

1. Para ver o novo valor aparecer, selecione o botão **Refresh.**

1. Selecione a página **Comandos** para visualizar todos os comandos deste componente.

1. Selecione o comando que pretende testar, se houver. Selecione **Enviar o comando** para ligar para o comando do dispositivo. Pode ver o seu dispositivo responder ao comando na janela de aviso de comando onde o código de amostra está em funcionamento.
