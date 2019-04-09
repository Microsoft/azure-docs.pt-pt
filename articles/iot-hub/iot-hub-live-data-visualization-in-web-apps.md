---
title: Visualização de dados em tempo real dos dados de sensor do seu hub IoT do Azure – aplicações Web | Documentos da Microsoft
description: Utilize a funcionalidade de aplicações Web do serviço de aplicações do Microsoft Azure para visualizar dados de temperatura e humidade recolhidos do sensor e enviados ao seu hub Iot.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: b870c432ea8021d9eeb14eec85ff9912b96ddc96
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270885"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Visualizar dados de sensores em tempo real do seu hub IoT do Azure ao utilizar a funcionalidade de aplicações Web do serviço de aplicações do Azure

![Diagrama de ponto a ponto](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que irá aprender

Neste tutorial, saiba como visualizar dados de sensores em tempo real que recebe o seu hub IoT ao executar uma aplicação web que está alojada numa aplicação web. Se quiser tentar visualizar os dados do seu hub IoT através do Power BI, veja [utilize o Power BI para visualizar dados de sensores em tempo real do IoT Hub do Azure](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>O que fazer

* Crie uma aplicação web no portal do Azure.
* Prepare seu IoT hub para acesso a dados ao adicionar um grupo de consumidores.
* Configure a aplicação web para ler os dados de sensor do seu hub IoT.
* Carregar uma aplicação web para ser alojada pela aplicação web.
* Abra a aplicação web para ver dados de temperatura e humidade em tempo real do seu hub IoT.

## <a name="what-you-need"></a>Do que precisa

* [Configurar o dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md), que abrange os seguintes requisitos:

  * Uma subscrição do Azure Active Directory
  * Um hub Iot com a sua subscrição
  * Uma aplicação de cliente que envia mensagens para o seu hub Iot

* [Baixe o Git](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>Criar uma aplicação Web

1. Na [portal do Azure](https://portal.azure.com/), clique em **criar um recurso** > **Web + móvel** > **aplicação Web**.

2. Introduza um nome exclusivo de tarefa, verifique se a subscrição, especifique um grupo de recursos e um local, marque **afixar ao dashboard**e, em seguida, clique em **criar**.

   Recomendamos que selecione a mesma localização que o grupo de recursos. 
   
   ![Criar uma aplicação Web](./media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Configurar a aplicação web para ler dados a partir do seu hub IoT

1. Abra a aplicação web, que basta ter aprovisionado.

2. Clique em **as configurações do aplicativo**e, em **as definições da aplicação**, adicione os seguintes pares de chave/valor:

   | Chave                                   | Valor                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Obtidos a partir da CLI do Azure                                      |
   | Azure.IoT.IoTHub.ConsumerGroup        | O nome do grupo de consumidores que adicionar ao seu hub IoT  |
   | WEBSITE_NODE_DEFAULT_VERSION          | 8.9.4                                                        |

   ![Adicionar definições para a sua aplicação web com pares chave/valor](./media/iot-hub-live-data-visualization-in-web-apps/3_web-app-settings-key-value-azure.png)

3. Clique em **as configurações do aplicativo**, em **definições gerais**, ativar/desativar o **Web sockets** opção e, em seguida, clique em **guardar**.

   ![Ativar/desativar a opção de sockets Web](./media/iot-hub-live-data-visualization-in-web-apps/4_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Carregar uma aplicação web para ser alojada pela aplicação web

No GitHub, fizemos disponível uma aplicação web que apresenta dados de sensores em tempo real do seu hub IoT. Tudo o que precisa fazer é configurar a aplicação web para trabalhar com um repositório de Git, transferir a aplicação web a partir do GitHub e, em seguida, carregue-o para o Azure para a aplicação web para o anfitrião.

1. Na aplicação web, clique em **opções de implementação** > **Escolher origem** > **repositório de Git Local**e, em seguida, clique em **OK**.

   ![Configurar a implementação da sua aplicação web para usar o repositório de Git local](./media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. Clique em **credenciais de implementação**, crie um nome de utilizador e palavra-passe para utilizar para ligar para o repositório de Git no Azure e, em seguida, clique em **guardar**.

3. Clique em **descrição geral**e anote o valor de **url de clone de Git**.

   ![Obter o URL de clone de Git da sua aplicação web](./media/iot-hub-live-data-visualization-in-web-apps/6_web-app-git-clone-url-azure.png)

4. Abra um comando ou a janela de terminal no seu computador local.

5. Transferir a aplicação web a partir do GitHub e carregue-o para o Azure para a aplicação web para o anfitrião. Para tal, execute os seguintes comandos:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > \<URL de clone de Git\> é o URL do repositório de Git no **descrição geral** página da aplicação web.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Abra a aplicação web para ver dados de temperatura e humidade em tempo real do seu hub IoT

Sobre o **descrição geral** página da sua aplicação web, clique no URL para abrir a aplicação web.

![Obter o URL da sua aplicação web](./media/iot-hub-live-data-visualization-in-web-apps/7_web-app-url-azure.png)

Deverá ver os dados de temperatura e humidade em tempo real do seu hub IoT.

![Página da aplicação Web que mostra em tempo real temperatura e humidade](./media/iot-hub-live-data-visualization-in-web-apps/8_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> Certifique-se de que o aplicativo de exemplo está em execução no seu dispositivo. Se não, obterá um gráfico em branco, pode consultar os tutoriais em [configurar o seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="next-steps"></a>Passos Seguintes

Utilizou com êxito a aplicação web para visualizar dados de sensores em tempo real do seu hub IoT.

Para uma forma alternativa visualizar dados do IoT Hub do Azure, consulte [utilize o Power BI para visualizar dados de sensores em tempo real do seu hub IoT](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]