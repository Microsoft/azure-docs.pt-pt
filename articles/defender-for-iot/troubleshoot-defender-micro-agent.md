---
title: Resolução de problemas do micro agente defender IoT
titleSuffix: Azure Defender for IoT
description: Aprenda a lidar com erros inesperados ou inexplicáveis.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/24/2021
ms.topic: reference
ms.service: azure
ms.openlocfilehash: dade0d0d5dc4d690ea94f20deaf956b1e079bad7
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124181"
---
# <a name="defender-iot-micro-agent-troubleshooting"></a>Resolução de problemas do micro agente defender IoT 

No caso de ter erros inesperados ou inexplicáveis, utilize os seguintes métodos de resolução de problemas para tentar resolver os seus problemas. Você também pode contactar o Azure Defender para a equipe de produtos IoT para assistência, se necessário.   

## <a name="service-status"></a>Estado do serviço 

Para visualizar o estado do serviço: 

1. Execute o seguinte comando

    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```

1. Verifique se o serviço está estável certificando-se de que é `active` , e que o tempo de funcionamento no processo é apropriado.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="Certifique-se de que o seu serviço está estável, verificando se está ativo e que o tempo de funcionamento é adequado.":::

Se o serviço estiver listado como `inactive` , utilize o seguinte comando para iniciar o serviço:

```azurecli
systemctl start defender-iot-micro-agent.service 
```

Saberá que o serviço está a falhar se o tempo de intemí desa metragem for demasiado curto. Para resolver este problema, tem de rever os registos.

## <a name="review-logs"></a>Subscrever registos 

Utilize o seguinte comando para verificar se o serviço de micro-agente Defender IoT está a funcionar com privilégios de raiz.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Verifique se o serviço de micro-agente IoT está a funcionar com privilégios de raiz.":::

Para visualizar os registos, utilize o seguinte comando:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

Para reiniciar o serviço, utilize o seguinte comando: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Passos seguintes

Confira o [suporte de Recurso e a reforma.](edge-security-module-deprecation.md)
