---
title: Compreenda a recolha proativa de registo no dispositivo Azure Stack Edge Pro
description: Descreve como a recolha de registos proactiva é feita num dispositivo Azure Stack Edge Pro e como desativá-la.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: alkohli
ms.openlocfilehash: bdd44bf81e34b60ee648b71c6dc3bde5a96d3deb
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443017"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Coleção de registos proactivos no seu dispositivo Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A recolha proativa de registos recolhe indicadores de saúde do sistema no seu dispositivo Azure Stack Edge para ajudá-lo a resolver eficazmente qualquer problema do dispositivo. A recolha proativa de registos é ativada por padrão. Este artigo descreve o que é registado, como a Microsoft lida com os dados e como desativar ou permitir a recolha de registos proativa. 

As informações deste artigo aplicam-se aos dispositivos Azure Stack Edge Pro GPU, Azure Stack Edge Pro R e Azure Stack Edge Mini R.

## <a name="about-proactive-log-collection"></a>Sobre a coleção de registos pró-ativos

As equipas de Suporte ao Cliente e Engenharia da Microsoft utilizam registos de sistemas a partir do seu dispositivo Azure Stack Edge para identificar e corrigir eficazmente problemas que possam surgir durante o funcionamento. A recolha de registos proactivos é um método que alerta a Microsoft de que um problema/evento foi detetado pelo aparelho Azure Stack Edge do cliente. Consulte [indicadores de recolha de registos proactivos](#proactive-log-collection-indicators) para eventos que sejam rastreados. Os registos de suporte relativos ao problema são automaticamente enviados para uma conta de Armazenamento Azure que a Microsoft gere e controla. Os engenheiros do Microsoft Support e da Microsoft analisam estes registos de suporte para determinar o melhor curso de ação para resolver o problema com o cliente.

> [!NOTE]
> Estes registos são utilizados apenas para depurar e para prestar apoio aos clientes em caso de problemas.


## <a name="enabling-proactive-log-collection"></a>Habilitação de recolha de registos proativos

A recolha proativa de registos é ativada por padrão. Pode desativar a recolha de registos proactivos ao tentar ativar o dispositivo através da UI local. 

1. Na uI web local do dispositivo, vá para a página **Get start.**

2. No azulejo de **ativação,** **selecione Ativar**. 

    ![Web local UI "Cloud details" página 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)

3. No painel **Ativar:**

   1. Introduza a **tecla de ativação** que obteve em [Obter a chave de ativação para Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

      Uma vez ativada, a recolha de registos proactivos é ativada por padrão, permitindo à Microsoft recolher registos com base no estado de saúde do dispositivo. Estes registos são enviados para uma conta de Armazenamento Azure. 

      Pode desativar a recolha de registos proactivos para impedir que a Microsoft recolha registos.

   1. Se pretender desativar a recolha de registos proactivos no dispositivo, **selecione Desativar**.

   1. **Selecione Ativar**.

   ![Web local UI "Cloud details" página 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)

## <a name="proactive-log-collection-indicators"></a>Indicadores proactivos de recolha de registos

Após a ativação da recolha proativa do registo, os registos são carregados automaticamente quando um dos seguintes eventos é detetado no dispositivo:  


|Alerta/Erro/Condição  |Descrição  |
|---------|---------|
|AcsUnhealthyCondition     |Os Serviços Consistentes Azure não são saudáveis.         |
|IOTEdgeAgentNotRunningCondição      |O Agente IoT Edge não está a funcionar.         |
|UpdateInstallFailedEvent | Não foi possível instalar a atualização.        |

 
A Microsoft continuará a adicionar novos eventos à lista anterior. Não é necessário nenhum consentimento adicional para novos eventos. Consulte esta página para saber mais sobre os indicadores de recolha de registos proativos mais atualizados.    
 

## <a name="other-log-collection-methods"></a>Outros métodos de recolha de registos

Além da recolha proativa de registos, que recolhe registos específicos relativos a um problema específico detetado, outras coleções de registos podem dar uma compreensão muito mais profunda da saúde e comportamento do sistema. Normalmente, estes outros registos podem ser recolhidos durante um pedido de suporte ou desencadeados pela Microsoft com base em dados de telemetria do dispositivo.

## <a name="handling-data"></a>Tratamento de dados

Quando a recolha de registos proactivos está ativada, o cliente concorda com a Microsoft a recolher registos do dispositivo Azure Stack Edge, tal como descrito aqui. O cliente também reconhece e consente no upload e retenção desses registos numa conta de Armazenamento Azure gerida e controlada pela Microsoft.

A Microsoft utiliza os dados para resolver problemas de saúde do sistema e apenas problemas. Os dados não são utilizados para marketing, publicidade ou quaisquer outros fins comerciais sem o consentimento do cliente. 

Os dados que a Microsoft recolhe são tratados de acordo com as nossas práticas de privacidade padrão. Caso um cliente decida revogar o seu consentimento para a recolha proativa de registos, quaisquer dados recolhidos com consentimento antes da revogação não serão afetados.

## <a name="next-steps"></a>Passos seguintes

Saiba como [recolher um pacote de suporte.](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)