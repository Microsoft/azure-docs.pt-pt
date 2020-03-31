---
title: Troubleshoot - Azure Event Grid IoT Edge [ Microsoft Docs
description: Resolução de problemas em Event Grid em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/24/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 95181d0eb23d5956b2c6af52c77f85714b107345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100148"
---
# <a name="common-issues"></a>Problemas Comuns

Se tiver problemas com a Grelha de Eventos Azure no IoT Edge no seu ambiente, use este artigo como guia para resolução e resolução de problemas.

## <a name="view-event-grid-module-logs"></a>Ver registos de módulos da Grelha de Eventos

Para resolver problemas, poderá ter de aceder aos registos de módulos da Rede de Eventos. Para tal, no VM onde o módulo é implantado executa o seguinte comando:

Nas janelas,

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

Em Linux,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Incapaz de fazer pedidos HTTPS

* Primeiro certifique-se de que o módulo De Rede de Eventos tem **entrada:serverAuth:tlsPolítica** definida para **rígida** ou **ativada**.

* Se as suas comunicações módulo-módulo, certifique-se de que está a fazer a chamada na porta **4438** e o nome do módulo corresponde ao que está implantado. 

  Para, por exemplo, se o módulo Event Grid foi implantado com **https://eventgridmodule:4438**o **nome eventgridmodule** então o seu URL deve ser . Certifique-se de que o invólucro e o número da porta estão corretos.
    
* Se for do módulo não IoT, certifique-se de que a porta da Grelha de Eventos está mapeada na máquina anfitriã durante a implantação, por exemplo,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>Incapaz de fazer pedidos http

* Primeiro certifique-se de que o módulo De Rede de Eventos tem **entrada:serverAuth:tlsDefinição de política** **ativada** ou **desativada**.

* Se as suas comunicações módulo-módulo, certifique-se de que está a fazer a chamada na porta **5888** e o nome do módulo corresponde ao que está implantado. 

  Para, por exemplo, se o módulo Event Grid foi implantado com **http://eventgridmodule:5888**o **nome eventgridmodule** então o seu URL deve ser . Certifique-se de que o invólucro e o número da porta estão corretos.
    
* Se for do módulo não IoT, certifique-se de que a porta da Grelha de Eventos está mapeada na máquina anfitriã durante a implantação, por exemplo,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>A cadeia de certificados foi emitida por uma autoridade que não é de confiança.

Por padrão, o módulo Event Grid está configurado para autenticar clientes com certificado emitido pelo daemon de segurança IoT Edge. Certifique-se de que o cliente está a apresentar um certificado que está enraizado nesta cadeia.

A classe **IoTSecurity** mostra [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) como recuperar certificados do daemon IoT Edge Security e usá-lo para configurar chamadas de saída.

Se for um ambiente de não produção, tem a opção de desativar a autenticação do cliente. Consulte a [Segurança e Autenticação](security-authentication.md) para obter mais informações sobre como fazê-lo.

## <a name="debug-events-not-received-by-subscriber"></a>Debug Eventos não recebidos por assinante

As razões típicas para isto são:

* O evento nunca foi publicado com sucesso. Um Código de Estado HTTP de 200 (OK) deve ser recebido ao publicar um evento no módulo Event Grid.

* Verifique a subscrição do evento para verificar:
    * URL endpoint é válido
    * Quaisquer filtros na subscrição não estão a fazer com que o evento seja "largado".

* Verifique se o módulo de subscritor está em execução

* Inicie sessão no VM onde o módulo De Rede de Eventos é implantado e veja os seus registos.

* Ligue o registo por entrega definindo **o corretor:logDeliverySuccess=verdadeiro** e reimplantando o módulo De rede de eventos e reexperimentando o pedido. Ligar o registo por entrega pode ter impacto na entrada e latência, por isso, uma vez completada a depuração, a nossa recomendação é voltar a trás para **corretor:logDeliverySuccess=falso** e reimplantar módulo de Rede de Eventos.

* Ligue as métricas definindo **métricas:repórtertype=consola** e reimplante o módulo De Rede de Eventos. Quaisquer operações posteriores resultarão na registo de métricas na consola do módulo Event Grid, que pode ser usada para depurar ainda mais. A nossa recomendação é ligar as métricas apenas para depurar e uma vez completa para desligá-la definindo **métricas:repórtertype=nenhuma** e reimplantando o módulo De Rede de Eventos.

## <a name="next-steps"></a>Passos seguintes

Relatar quaisquer problemas, sugestões com a utilização da Grelha de Eventos no IoT Edge em [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues).