---
title: Executar detetor de anomalias na borda IoT
titleSuffix: Azure Cognitive Services
description: Insi reloque o módulo de detetor de anomalias para ioT Edge.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: 370883b90902dad5a6e222897e68e1d3e9dd2acf
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98738020"
---
# <a name="deploy-an-anomaly-detector-module-to-iot-edge"></a>Implementar um módulo de detetor de anomalias para ioT Edge

Saiba como implantar o módulo [de detetor de anomalias de](../anomaly-detector-container-howto.md) serviços cognitivos num dispositivo IoT Edge. Uma vez implantado no IoT Edge, o módulo funciona em IoT Edge juntamente com outros módulos como instâncias de contentores. Expõe exatamente as mesmas APIs que um contentor de detetor de anomalias que funciona num ambiente normal de contentores de estivadores. 

## <a name="prerequisites"></a>Pré-requisitos

* Utilize uma assinatura Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.
* Instale o [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Um [Hub IoT](../../../iot-hub/iot-hub-create-through-portal.md) e um dispositivo [IoT Edge.](../../../iot-edge/quickstart-linux.md)

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>Implementar o módulo de deteção de anomalias na borda

1. No portal Azure, introduza o **Detetor de Anomalias no IoT Edge** na pesquisa e abra o resultado do Azure Marketplace.
2. Irá levá-lo aos [dispositivos-alvo](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector)do portal Azure para a página do Módulo de Borda IoT . Forneça as seguintes informações obrigatórias.

    1. Selecione a sua subscrição.

    1. Selecione o seu Hub IoT.

    1. **Selecione Localizar o dispositivo** e encontrar um dispositivo IoT Edge.

3. Selecione o botão **Criar**.

4. Selecione o módulo **AnomalyDetectoronIoTEdge.**

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="Imagem da interface de utilizador dos Módulos IoT Edge com ligação AnomalyDetectoronIoTEdge realçada com uma caixa vermelha para indicar que este é o item a selecionar.":::

5. Navegue até **Variáveis de Ambiente** e forneça as seguintes informações.

    1.  Mantenha o valor para aceitar o **Eula**.

    1. Preencha a **Faturação** com o seu ponto final dos Serviços Cognitivos.

    1. Preencha a **APIKey** com a sua chave de API dos Serviços Cognitivos.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="Variáveis ambientais com caixas vermelhas em torno das áreas que precisam de valores para ser preenchidos para a chave endpoint e API":::

6. Selecione **Atualizar**

7. Selecione **Seguinte: Rotas** para definir o seu percurso. Tem de definir todas as mensagens de todos os módulos para serem enviadas para o Hub IoT do Azure.

8. Selecione **Seguinte: Rever + criar**. Pode pré-visualizar o ficheiro JSON que define todos os módulos que são implementados no seu dispositivo IoT Edge.
    
9. Selecione **Criar** para começar a implementação do módulo.

10. Após concluir a implementação de módulos, volte à página IoT Edge do seu hub IoT. Selecione o seu dispositivo da lista de dispositivos de IoT Edge para ver os respetivos detalhes.

11. Desloque-se para baixo e veja os módulos listados. Verifique se o estado de funcionamento está em funcionamento para o seu novo módulo. 

Para resolver problemas com o estado de funcionamento do seu dispositivo IoT Edge, consulte o [guia de resolução de problemas](../../../iot-edge/troubleshoot.md)

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>Detetor de anomalia de teste num dispositivo IoT Edge

Irá fazer uma chamada HTTP para o dispositivo Azure IoT Edge que tem o contentor dos Serviços Cognitivos em execução. O recipiente fornece APIs de ponto final baseado em REST. Utilize o hospedeiro, `http://<your-edge-device-ipaddress>:5000` para apis módulos.

Se o seu dispositivo de borda já não permitir a comunicação de entrada na porta 5000, terá de criar uma nova **regra de entrada na porta**. 

Para um Azure VM, isto pode ser definido sob a regra **de** entrada de entrada de  >  **máquinas** virtuais Adicionar  >    >    >  **regra de porta de entrada**.

Existem várias formas de validar que o módulo está em funcionamento. Localize o endereço *IP externo* e a porta exposta do dispositivo de borda em questão e abra o seu navegador web favorito. Utilize os urls de vários pedidos abaixo para validar o recipiente em funcionamento. O exemplo de pedido URLs listados abaixo são `http://<your-edge-device-ipaddress:5000` , mas o seu recipiente específico pode variar. Tenha em mente que precisa de utilizar o endereço *IP externo* do seu dispositivo de borda.

| URL do Pedido | Objetivo |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | O contentor fornece uma home page. |
| `http://<your-edge-device-ipaddress>:5000/status` | Também solicitado com GET, isto verifica se a chave api utilizada para iniciar o contentor é válida sem causar uma consulta de ponto final. Este pedido pode ser utilizado para sondas kubernetes [de prontidão e prontidão.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) |
| `http://<your-edge-device-ipaddress>:5000/swagger` | O contentor fornece um conjunto completo de documentação para os pontos finais e uma funcionalidade **Experimentar**. Com esta funcionalidade, pode introduzir as suas definições num formulário HTML baseado na Web e fazer a consulta sem ter de escrever qualquer código. Após o retorno da consulta, é fornecido um comando CURL de exemplo para demonstrar os cabeçalhos HTTP e o formato corporal que é necessário. |

![Página inicial do contentor](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>Próximos passos

* Rever [Instalar e executar recipientes](../anomaly-detector-container-configuration.md) para puxar a imagem do contentor e executar o recipiente
* [Rever recipientes de configuração](../anomaly-detector-container-configuration.md) para configurações de configuração
* [Saiba mais sobre o serviço API do Detetor de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)