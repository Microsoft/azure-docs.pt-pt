---
title: Experimente e executar uma solução de simulação de dispositivo - Azure | Documentos da Microsoft
description: Neste início rápido, vai implementar a Simulação de Dispositivo de IoT do Azure e executar uma simulação
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: 0fcd688c9e5164522a482a4375c524a97a48d400
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175629"
---
# <a name="quickstart-deploy-and-run-an-iot-device-simulation-in-azure"></a>Início rápido: Implementar e executar uma simulação de dispositivo do IoT no Azure

Este início rápido mostra como implementar a Simulação de Dispositivo de IoT do Azure para testar a sua solução de IoT. Depois de implementar o acelerador de soluções, execute uma simulação de exemplo para começar.

Para concluir este início rápido, precisa de uma subscrição ativa do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="deploy-device-simulation"></a>Implementar a Simulação do Dispositivo

Ao implementar a Simulação do Dispositivo na sua subscrição do Azure, tem de definir algumas opções de configuração.

Inicie sessão em [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) com as credenciais da conta do Azure.

Clique no mosaico **Simulação de Dispositivo**:

![Escolher a Simulação de Dispositivo](./media/quickstart-device-simulation-deploy/devicesimulation.png)

Clique em **Experimente agora** na página de descrição Simulação de Dispositivo:

![Clique em Experimente Agora](./media/quickstart-device-simulation-deploy/devicesimulationPDP.png)

Na página **Criar solução de Simulação de Dispositivo**, introduza um **Nome de solução** exclusivo.

Selecione a **Subscrição** e a **Região** que quer utilizar para implementar o acelerador de soluções. Normalmente, pode escolher a região mais próxima para si. Tem de ser um [administrador global ou de utilizador](iot-accelerators-permissions.md) na subscrição.

Selecione a caixa para implementar um hub IoT para utilizar com a sua solução de simulação de dispositivo. Pode sempre alterar o hub IoT que a simulação utilizará mais tarde.

Clique em **criar** para começar a aprovisionar a solução. Este processo demora, pelo menos, cinco minutos a ser executado:

![Detalhes da solução de Simulação de Dispositivos](./media/quickstart-device-simulation-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Iniciar sessão na solução

Quando o processo de aprovisionamento estiver concluído, pode iniciar sessão na instância da Simulação de Dispositivo ao clicar no botão **Iniciar**:

![Abrir a Simulação de Dispositivo](./media/quickstart-device-simulation-deploy/choosenew.png)

Clique em **Aceitar** para aceitar o pedido de permissões. O dashboard da solução de Simulação de Dispositivo é apresentado no browser.

Quando aberto pela primeira vez, verá o dashboard Simulação de Dispositivo com um guia **Introdução**. Clique no primeiro mosaico para abrir uma simulação de exemplo. Se fechar o guia **Introdução**, pode abrir a **Simulação Simples de Exemplo** a partir do dashboard ao clicar no respetivo bloco:

![Dashboard de soluções](./media/quickstart-device-simulation-deploy/GettingStarted.png)

## <a name="sample-simulation"></a>Simulação de Exemplo

Não pode ser editada enquanto simulação de exemplo. A simulação é configurada com as seguintes definições:

| Definição             | Value                       |
| ------------------- | --------------------------- |
| Hub IoT de destino      | Utilizar Hub IoT pré-aprovisionado |
| Modelo do dispositivo        | Camião                       |
| Número de dispositivos   | 10                          |
| Frequência da telemetria | 10 segundos                  |
| Duração da simulação | Executar indefinidamente            |

![Configuração de simulação](./media/quickstart-device-simulation-deploy/SampleSimulation.png)

## <a name="run-the-simulation"></a>Executar a simulação

Clique em **Iniciar Simulação**. A simulação será executada indefinidamente, conforme configurado. Pode parar a simulação em qualquer altura ao clicar em **Parar Simulação**. A simulação mostra as estatísticas da execução atual.

![Execução de simulação](./media/quickstart-device-simulation-deploy/runningsimulation.png)

Só pode executar uma simulação de cada vez a partir de uma instância de Simulação do Dispositivo.

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser explorar ainda mais, deixe a Simulação de Dispositivo implementada.

Se já não precisar da Simulação de Dispositivo, elimine-a na página [Soluções aprovisionadas](https://www.azureiotsolutions.com/Accelerators#dashboard) ao clicar no respetivo mosaico e, em seguida, clique em **Eliminar Solução**:

![Eliminar solução](media/quickstart-device-simulation-deploy/deletesolution.png)

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, vai implementar a Simulação de Dispositivo e executar uma simulação de dispositivo IoT.

> [!div class="nextstepaction"]
> [Criar uma simulação com um ou mais tipos de dispositivo](iot-accelerators-device-simulation-create-simulation.md)