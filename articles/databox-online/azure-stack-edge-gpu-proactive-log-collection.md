---
title: Compreenda a recolha proativa de registo no dispositivo Azure Stack Edge Pro
description: Descreve como a recolha de registos proactiva é feita num dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: alkohli
ms.openlocfilehash: f79de47ec0ffad11f650054b581dbbaae030edbf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466961"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Coleção de registos proactivos no seu dispositivo Azure Stack Edge

Pode ativar a recolha proativa de registos no seu dispositivo Azure Stack Edge com base nos indicadores de saúde do sistema para ajudar a resolver eficazmente quaisquer problemas do dispositivo. Este artigo descreve o que é a recolha proativa de registos, como capacitá-lo e como os dados são tratados quando a recolha proativa de registos está ativada.
   
As informações deste artigo aplicam-se aos dispositivos Azure Stack Edge Pro GPU, Azure Stack Edge Pro R e Azure Stack Edge Mini R.

## <a name="about-proactive-log-collection"></a>Sobre a coleção de registos pró-ativos

As equipas de Suporte ao Cliente e Engenharia da Microsoft utilizam registos de sistemas a partir do seu dispositivo Azure Stack Edge para identificar e corrigir eficazmente problemas que possam surgir durante o funcionamento. A recolha proactiva de registos é um método que alerta a Microsoft de que foi detetado um problema/evento (ver secção de indicadores de recolha de registos proactivos para eventos a serem rastreados) pelo aparelho Azure Stack Edge do cliente. Os registos de suporte relativos ao problema são automaticamente enviados para uma conta de Armazenamento Azure gerida e controlada pela Microsoft. Os engenheiros do Microsoft Support e da Microsoft analisam estes registos de suporte para determinar o melhor curso de ação para resolver o problema com o cliente.    

> [!NOTE]
> Estes registos são utilizados apenas para depurar e prestar apoio aos clientes em caso de problemas. 


## <a name="enabling-proactive-log-collection"></a>Habilitação de recolha de registos proativos

Pode ativar a recolha de registos proactivos ao tentar ativar o dispositivo através da UI local. 

1. Na uI web local do dispositivo, vá para **começar** a página.
2. No azulejo de **ativação,** **selecione Ativar**. 

    ![Web local UI "Cloud details" página 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. No painel **Ativar:**
    1. Introduza a **tecla de ativação** que obteve em [Obter a chave de ativação para Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. Pode permitir que a recolha proativa de registos permita à Microsoft recolher registos com base no estado de saúde do dispositivo. Os registos recolhidos desta forma são enviados para uma conta de Armazenamento Azure.
    
    1. Selecione **Aplicar**.

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

Além da recolha proativa de registos, que recolhe registos específicos relativos a um problema específico detetado, existem outras coleções de registos que podem fornecer uma compreensão muito mais profunda da saúde e comportamento do sistema. Normalmente, estas outras recolhas de registos podem ser executadas durante um pedido de suporte ou desencadeadas pela Microsoft com base em dados de telemetria que o dispositivo fornece.  

## <a name="handling-data"></a>Tratamento de dados

Se um cliente ativar a recolha de registos proactivos, concorda com a Microsoft a recolher registos do dispositivo Azure Stack Edge, tal como descrito aqui. O cliente também reconhece e consente no upload e retenção desses registos numa conta de Armazenamento Azure gerida e controlada pela Microsoft.

A Microsoft utiliza os dados para resolver problemas de saúde do sistema e apenas problemas. Os dados não são utilizados para marketing, publicidade ou quaisquer outros fins comerciais sem o consentimento do cliente. 

Os dados que a Microsoft recolhe são tratados de acordo com as nossas práticas de privacidade padrão. Caso um cliente decida revogar o seu consentimento para a recolha proativa de registos, quaisquer dados recolhidos com consentimento antes da revogação não serão afetados.

## <a name="next-steps"></a>Passos seguintes

Saiba como [recolher um pacote de suporte.](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)