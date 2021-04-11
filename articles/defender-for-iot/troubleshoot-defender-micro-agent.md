---
title: Resolução de problemas do micro agente do Defender IoT (Pré-visualização)
description: Aprenda a lidar com erros inesperados ou inexplicáveis.
ms.date: 4/5/2021
ms.topic: reference
ms.openlocfilehash: 3d52c4093c01d7e449c68b1c8143249b51f7061a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011424"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Resolução de problemas do micro agente do Defender IoT (Pré-visualização)

Se ocorrer um erro inesperado, pode utilizar estes métodos de resolução de problemas para tentar resolver o problema. Você também pode contactar o Azure Defender para a equipe de produtos IoT para assistência, se necessário.   

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

Saberá que o serviço está em paragem se o tempo de paragem do processo for inferior a 2 minutos. Para resolver este problema, tem de [rever os registos](#review-the-logs).

## <a name="validate-micro-agent-root-privileges"></a>Validar privilégios de raiz de micro-agente

Utilize o seguinte comando para verificar se o serviço de micro-agente Defender IoT está a funcionar com privilégios de raiz.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Verifique se o serviço de micro-agente IoT está a funcionar com privilégios de raiz.":::
## <a name="review-the-logs"></a>Rever os registos 

Para rever os registos, utilize o seguinte comando:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

### <a name="quick-log-review"></a>Revisão rápida do registo

Se ocorrer um problema quando o micro-agente é executado, pode executar o micro-agente num estado temporário, o que lhe permitirá visualizar os registos utilizando o seguinte comando:

```azurecli
sudo systectl stop defender-iot-micro-agent
cd /var/defender_iot_micro_agent/
sudo ./defender_iot_micro_agent
```

## <a name="restart-the-service"></a>Reinicie o serviço

Para reiniciar o serviço, utilize o seguinte comando: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Passos seguintes

Confira o [suporte de Recurso e a reforma.](edge-security-module-deprecation.md)
