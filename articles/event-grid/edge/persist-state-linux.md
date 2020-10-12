---
title: Persistir estado em Linux - Azure Event Grid IoT Edge / Microsoft Docs
description: Persistir metadados em Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 92333c2528303a6fa53fa30f47def33c33235d39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171487"
---
# <a name="persist-state-in-linux"></a>Persistir estado em Linux

Os tópicos e subscrições criados no módulo 'Grade de Eventos' são armazenados por predefinição no sistema de ficheiros do contentor. Sem persistência, se o módulo for redistribuído, todos os metadados criados serão perdidos. Para preservar os dados através de implementações e reiniciões, é necessário persistir os dados fora do sistema de ficheiros do contentor.

Por padrão, apenas os metadados são persistidos e os eventos ainda são armazenados na memória para um melhor desempenho. Siga a secção de eventos de persistência para permitir a persistência do evento também.

Este artigo fornece os passos para implantar o módulo De Grelha de Eventos com persistência nas implementações do Linux.

> [!NOTE]
>O módulo De Grelha de Evento funciona como um utilizador privilegiado com UID `2000` e nome `eventgriduser` .

## <a name="persistence-via-volume-mount"></a>Persistência através do suporte de volume

 [Os volumes de estivadores](https://docs.docker.com/storage/volumes/) são usados para preservar os dados através das implementações. Pode permitir que o Docker crie automaticamente um volume nomeado como parte da implementação do módulo 'Grade de Eventos'. Esta opção é a opção mais simples. Pode especificar o nome de volume a ser criado na secção **Binds** da seguinte forma:

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
>Não altere a segunda parte do valor do encaixe. Aponta para uma localização específica dentro do módulo. Para o módulo de Grelha de Eventos no Linux, tem de ser **/app/metadadosDb**.

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

Em vez de montar um volume, pode criar um diretório no sistema de anfitrião e montar esse diretório.

## <a name="persistence-via-host-directory-mount"></a>Persistência através do suporte do diretório de anfitriões

Em vez de um volume de estivador, também tem a opção de montar uma pasta de anfitrião.

1. Primeiro crie um utilizador com o nome **eventgriduser** e iD **2000** na máquina hospedeira executando o seguinte comando:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Crie um diretório no sistema de ficheiros hospedeiros executando o seguinte comando.

   ```sh
   md <your-directory-name-here>
   ```

    Por exemplo, executar o seguinte comando criará um diretório chamado **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Em seguida, torne **o eventgriduser** proprietário desta pasta executando o seguinte comando.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Por exemplo,

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Utilize **os Binds** para montar o diretório e recolocar o módulo de Grelha de Eventos a partir do portal Azure.

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
    >Não altere a segunda parte do valor do encaixe. Aponta para uma localização específica dentro do módulo. Para o módulo de Grelha de Eventos no linux, tem de ser **/app/metadadosDb** e **/app/eventsDb**


## <a name="persist-events"></a>Persistir eventos

Para ativar a persistência do evento, deve primeiro ativar a persistência de metadados através de montagem de volume ou de suporte de diretório de anfitriões utilizando as secções acima.

Coisas importantes a notar sobre eventos persistentes:

* Os eventos persistentes são ativados por subscrição de eventos e são opt-in uma vez que um volume ou diretório foi montado.
* A persistência do evento é configurada numa Subscrição de Eventos no momento da criação e não pode ser modificada uma vez que a Subscrição do Evento é criada. Para alternar a persistência do evento, tem de eliminar e recriar a Subscrição do Evento.
* Os eventos persistentes são quase sempre mais lentos do que nas operações de memória, no entanto a diferença de velocidade é altamente dependente das características da unidade. A troca entre velocidade e fiabilidade é inerente a todos os sistemas de mensagens, mas geralmente só se torna percetível em larga escala.

Para permitir a persistência do evento numa Subscrição de Eventos, definido `persistencePolicy` `true` para:

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
