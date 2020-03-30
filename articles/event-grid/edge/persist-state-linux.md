---
title: Persistir estado em Linux - Azure Event Grid IoT Edge [ Microsoft Docs
description: Persistir metadados em Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 12655d2ceb4a1124376d9bddf82194472c98ebb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086653"
---
# <a name="persist-state-in-linux"></a>Persistir estado em Linux

Os tópicos e subscrições criados no módulo Event Grid são armazenados no sistema de ficheiros de contentores por padrão. Sem persistência, se o módulo for redistribuído, todos os metadados criados perder-se-ão. Para preservar os dados através de implementações e reinícios, é necessário persistir os dados fora do sistema de ficheiros de contentores.

Por padrão, apenas os metadados são persistidos e os eventos ainda são armazenados na memória para um melhor desempenho. Siga a secção de eventos persistais para permitir a persistência do evento também.

Este artigo fornece os passos para implementar o módulo De Rede de Eventos com persistência nas implementações do Linux.

> [!NOTE]
>O módulo Event Grid funciona como um utilizador `2000` de `eventgriduser`baixo privilégio com UID e nome .

## <a name="persistence-via-volume-mount"></a>Persistência através do volume de montagem

 [Os volumes](https://docs.docker.com/storage/volumes/) de docker são usados para preservar os dados através de implementações. Pode permitir que o Docker crie automaticamente um volume nomeado como parte da implementação do módulo DeRede de Eventos. Esta opção é a opção mais simples. Pode especificar o nome de volume a criar na secção **Binds** da seguinte forma:

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Não altere a segunda parte do valor da ligação. Aponta para uma localização específica dentro do módulo. Para o módulo De Rede de Eventos no Linux, tem de ser **/app/metadadosDb**.

Por exemplo, a seguinte configuração resultará na criação do volume **egmetadataDbVol** onde os metadados serão persistidos.

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

Em vez de acumular um volume, pode criar um diretório no sistema de anfitriões e montar esse diretório.

## <a name="persistence-via-host-directory-mount"></a>Persistência através do suporte de diretório anfitrião

Em vez de um volume de estivador, também tem a opção de montar uma pasta hospedeira.

1. Primeiro crie um utilizador com nome **eventgriduser** e ID **2000** na máquina anfitriã executando o seguinte comando:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Crie um diretório no sistema de ficheiros do anfitrião executando o seguinte comando.

   ```sh
   md <your-directory-name-here>
   ```

    Por exemplo, executar o seguinte comando criará um diretório chamado **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Em seguida, faça do proprietário do **eventgriduser** desta pasta executando o seguinte comando.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Por exemplo,

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Utilize **Binds** para montar o diretório e reimplantar o módulo Da Grelha de Eventos do portal Azure.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    Por exemplo,

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >Não altere a segunda parte do valor da ligação. Aponta para uma localização específica dentro do módulo. Para o módulo De Rede de Eventos no linux, tem de ser **/app/metadadosDb** e **/app/eventsDb**


## <a name="persist-events"></a>Persistir eventos

Para permitir a persistência do evento, primeiro deve ativar a persistência de metadados através do suporte de volume ou do suporte de diretório de hospedeiro utilizando as secções acima referidas.

Coisas importantes a notar sobre eventos persistentes:

* Os eventos persistentes são ativados por subscrição de eventos e é opt-in uma vez que um volume ou diretório foi montado.
* A persistência do evento é configurada numa Subscrição de Eventos no momento da criação e não pode ser modificada uma vez que a Subscrição do Evento é criada. Para alternar a persistência do evento, deve eliminar e recriar a Subscrição do Evento.
* Os eventos persistentes são quase sempre mais lentos do que nas operações de memória, no entanto a diferença de velocidade depende muito das características da unidade. A troca entre a velocidade e a fiabilidade é inerente a todos os sistemas de mensagens, mas geralmente só se torna percetível em larga escala.

Para permitir a persistência do `persistencePolicy` evento `true`numa Subscrição de Eventos, definida para:

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
