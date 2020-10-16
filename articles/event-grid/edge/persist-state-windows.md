---
title: Persistir estado no Windows - Azure Event Grid IoT Edge / Microsoft Docs
description: Persistir estado no Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: f38e23a3af1e2c81ee012a4f3c268cbff3fc1bee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171470"
---
# <a name="persist-state-in-windows"></a>Persistir estado no Windows

Os tópicos e subscrições criados no módulo 'Grade de Eventos' são armazenados por predefinição no sistema de ficheiros do contentor. Sem persistência, se o módulo for redistribuído, todos os metadados criados serão perdidos. Para preservar os dados através de implementações e reiniciões, é necessário persistir os dados fora do sistema de ficheiros do contentor. 

Por padrão, apenas os metadados são persistidos e os eventos ainda são armazenados na memória para um melhor desempenho. Siga a secção de eventos de persistência para permitir a persistência do evento também.

Este artigo fornece os passos necessários para implementar o módulo De Grelha de Eventos com persistência nas implementações do Windows.

> [!NOTE]
>O módulo De Grelha de Evento funciona como um **containeruser** de utilizador pouco privilegiado no Windows.

## <a name="persistence-via-volume-mount"></a>Persistência através do suporte de volume

[Os volumes de estivadores](https://docs.docker.com/storage/volumes/) são usados para preservar dados em todas as implementações. Para montar um volume, é necessário criá-lo utilizando comandos de estivadores, dar permissões para que o recipiente possa ler, escrever para ele e, em seguida, implantar o módulo.

1. Criar um volume executando o seguinte comando:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Por exemplo,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Obtenha o diretório de anfitriões para o que o volume mapeia através do comando abaixo

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Por exemplo,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Saída da amostra:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Adicione o grupo **de Utilizadores** ao valor apontado pelo **Mountpoint** da seguinte forma:
    1. Launch File Explorer.
    1. Navegue para a pasta apontada pelo **Mountpoint**.
    1. Clique com o botão direito e, em seguida, selecione **Propriedades**.
    1. Selecione **Segurança**.
    1. Em *Nomes de grupo ou de utilizador, **selecione Editar**.
    1. **Selecione Adicionar,** `Users` insira, **selecione 'Verificar nomes'** e selecione **Ok**.
    1. Sob *Permissões para Utilizadores*, selecione **Modificar**e selecione **Ok**.
1. Utilize **os Binds** para montar este volume e recolocar o módulo de Grelha de Eventos a partir do portal Azure

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >Não altere a segunda parte do valor do encaixe. Aponta para uma localização específica no módulo. Para o módulo de Grelha de Eventos nas janelas, tem de ser **C: \\ \\ metadados de aplicaçõesDb**.


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>Persistência através do suporte do diretório de anfitriões

Em vez de montar um volume, pode criar um diretório no sistema de anfitrião e montar esse diretório.

1. Crie um diretório no sistema de ficheiros anfitrião executando o seguinte comando.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Por exemplo,

   ```sh
   mkdir C:\myhostdir
   ```
1. Utilize **os Binds** para montar o seu diretório e recolocar o módulo 'Grade de Eventos' a partir do portal Azure.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Não altere a segunda parte do valor do encaixe. Aponta para uma localização específica no módulo. Para o módulo 'Grade' de Eventos nas janelas, tem de ser **C: \\ \\ metadados de aplicaçõesDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>Persistir eventos

Para ativar a persistência do evento, deve primeiro permitir a persistência de eventos, quer através do suporte de volume quer do suporte do diretório de anfitriões, utilizando as secções acima.

Coisas importantes a notar sobre eventos persistentes:

* Os eventos persistentes são ativados por subscrição de eventos e são opt-in uma vez que um volume ou diretório foi montado.
* A persistência do evento é configurada numa Subscrição de Eventos no momento da criação e não pode ser modificada uma vez que a Subscrição do Evento é criada. Para alternar a persistência do evento, tem de eliminar e recriar a Subscrição do Evento.
* Os eventos persistentes são quase sempre mais lentos do que nas operações de memória, no entanto a diferença de velocidade é altamente dependente das características da unidade. A troca entre velocidade e fiabilidade é inerente a todos os sistemas de mensagens, mas só se torna percetível em larga escala.

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