---
title: Resolução de problemas - Azure Event Grid IoT Edge | Microsoft Docs
description: Resolução de problemas em Grade de Eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0196522618d4b61f615f7cc6faeacbe9a8c7c5b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171351"
---
# <a name="common-issues"></a>Problemas Comuns

Se sentir problemas usando a Azure Event Grid no IoT Edge no seu ambiente, use este artigo como um guia para resolução e resolução de problemas.

## <a name="view-event-grid-module-logs"></a>Ver registos de módulos de grelha de evento

Para resolver problemas, poderá ter de aceder aos registos de módulos da Grelha de Eventos. Para isso, no VM onde o módulo é implantado, executar o seguinte comando:

Nas janelas,

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

Em Linux,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Não é possível fazer pedidos HTTPS

* Primeiro certifique-se de que o módulo De Grelha de Eventos tem **entrada:serverAuth:tlsPolicy** definido para **rigoroso** ou **ativado**.

* Se as comunicações módulo-módulo, certifique-se de que está a fazer a chamada na porta **4438** e o nome do módulo corresponde ao que está implantado. 

  Por exemplo, se o módulo 'Grade de Eventos' foi implantado com o nome **eventgridmodule,** então o seu URL deve ser **https://eventgridmodule:4438** . Certifique-se de que o invólucro e o número da porta estão corretos.
    
* Se for de um módulo não IoT, certifique-se de que a porta de grelha de eventos está mapeada na máquina do anfitrião durante a implementação, por exemplo,

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

## <a name="unable-to-make-http-requests"></a>Não é possível fazer pedidos HTTP

* Primeiro certifique-se de que o módulo De Grelha de Eventos tem **entrada:serverAuth:tlsPolicy** definido para **ativar** ou **desativar**.

* Se as comunicações módulo-módulo, certifique-se de que está a fazer a chamada na porta **5888** e o nome do módulo corresponde ao que está implantado. 

  Por exemplo, se o módulo 'Grade de Eventos' foi implantado com o nome **eventgridmodule,** então o seu URL deve ser **http://eventgridmodule:5888** . Certifique-se de que o invólucro e o número da porta estão corretos.
    
* Se for de um módulo não IoT, certifique-se de que a porta de grelha de eventos está mapeada na máquina do anfitrião durante a implementação, por exemplo,

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

Por predefinição, o módulo Event Grid está configurado para autenticar clientes com certificado emitido pelo daemon de segurança IoT Edge. Certifique-se de que o cliente está a apresentar um certificado que está enraizado nesta cadeia.

A classe **IoTSecurity** [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) mostra como recuperar certificados da IoT Edge Security e usá-los para configurar chamadas de saída.

Se não for um ambiente de não produção, tem a opção de desligar a autenticação do cliente. Consulte a [Segurança e Autenticação](security-authentication.md) para obter mais informações sobre como fazê-lo.

## <a name="debug-events-not-received-by-subscriber"></a>Eventos Debug não recebidos por assinante

As razões típicas para isto são:

* O evento nunca foi publicado com sucesso. Um Código de Estado HTTP de 200(OK) deve ser recebido ao publicar um evento no módulo De Grelha de Eventos.

* Verifique a subscrição do evento para verificar:
    * URL de ponto final é válido
    * Quaisquer filtros na subscrição não estão a fazer com que o evento seja "largado".

* Verifique se o módulo de assinante está em execução

* Inicie sessão no VM onde o módulo 'Grade de Eventos' está implantado e veja os seus registos.

* Ligue por registo de entrega definindo **o corretor:logDeliverySuccess=verdadeiro** e reimplantando o módulo de Grelha de Evento e retorcendo o pedido. Ligar o registo por entrega pode ter impacto na produção e latência, pelo que uma vez concluída a depuração, a nossa recomendação é voltar a **ligá-lo ao módulo corretor:logDeliverySuccess=falso**  e reafectando o módulo de Grade de Eventos.

* Ligue as métricas definindo **métricas:reportertype=consola** e reposicione o módulo de grelha de evento. Qualquer operação depois disso resultará em métricas registadas na consola do módulo Event Grid, que pode ser usado para depurar ainda mais. A nossa recomendação é ligar as métricas apenas para depuração e uma vez completa para desligá-la definindo **métricas:reportertype=nenhum** e reafectando o módulo de Grade de Eventos.

## <a name="next-steps"></a>Passos seguintes

Informe quaisquer problemas, sugestões com a utilização da Grade de Eventos no IoT Edge em [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) .