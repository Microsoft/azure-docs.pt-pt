---
title: Persistir estado no Windows - Azure Event Grid IoT Edge  Microsoft Docs
description: Persistir estado nas Janelas
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 42f7b5315cecd75e2aaf67145c57982872f43550
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844620"
---
# <a name="persist-state-in-windows"></a>Persistir estado nas Janelas

Os tópicos e subscrições criados no módulo Event Grid são armazenados no sistema de ficheiros de contentores por padrão. Sem persistência, se o módulo for redistribuído, todos os metadados criados perder-se-ão. Para preservar os dados através de implementações e reinícios, é necessário persistir os dados fora do sistema de ficheiros de contentores. 

Por padrão, apenas os metadados são persistidos e os eventos ainda são armazenados na memória para um melhor desempenho. Siga a secção de eventos persistais para permitir a persistência do evento também.

Este artigo fornece os passos necessários para implementar o módulo De Rede de Eventos com persistência nas implementações do Windows.

> [!NOTE]
>O módulo Event Grid funciona como um utilizador de baixo privilégio **ContainerUser** no Windows.

## <a name="persistence-via-volume-mount"></a>Persistência através do volume de montagem

[Os volumes](https://docs.docker.com/storage/volumes/) de docker são usados para preservar dados através de implementações. Para montar um volume, é necessário criá-lo utilizando comandos de estivador, dar permissões para que o recipiente possa ler, escrever e, em seguida, implantar o módulo.

1. Criar um volume executando o seguinte comando:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Por exemplo,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Obtenha o diretório anfitrião para que o volume mapeia para executando o comando abaixo

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
1. Adicione o grupo **Utilizadores** ao valor apontado pela **Mountpoint** da seguinte forma:
    1. Lançar o Explorador de Ficheiros.
    1. Navegue para a pasta apontada por **Mountpoint**.
    1. Clique à direita e, em seguida, selecione **Propriedades**.
    1. Selecione **Segurança**.
    1. Em *Nomes de grupo ou utilizador, **selecione Editar**.
    1. **Selecione Adicionar,** introduza `Users`, selecione **'Ver Nomes'** e selecione **Ok**.
    1. Sob *permissões para utilizadores,* selecione **Modificar**, e selecione **Ok**.
1. Use **Binds** para montar este volume e reimplantar módulo de Rede de Eventos do portal Azure

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
   >Não altere a segunda parte do valor da ligação. Aponta para uma localização específica no módulo. Para o módulo De Rede de Eventos nas janelas, tem de ser **C:\\app\\metadadosDb**.


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

## <a name="persistence-via-host-directory-mount"></a>Persistência através do suporte de diretório anfitrião

Em vez de acumular um volume, pode criar um diretório no sistema de anfitriões e montar esse diretório.

1. Crie um diretório no sistema de ficheiros do anfitrião executando o seguinte comando.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Por exemplo,

   ```sh
   mkdir C:\myhostdir
   ```
1. Utilize **Binds** para montar o seu diretório e recolocar o módulo Da Grelha de Eventos do portal Azure.

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
    >Não altere a segunda parte do valor da ligação. Aponta para uma localização específica no módulo. Para o módulo De Rede de Eventos nas janelas, tem de ser **C:\\app\\metadadosDb**.

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

Para permitir a persistência do evento, primeiro deve ativar a persistência de metadados através do suporte de volume ou do suporte de diretório de hospedeiro utilizando as secções acima referidas.

Coisas importantes a notar sobre eventos persistentes:

* Os eventos persistentes são ativados por subscrição de eventos e é opt-in uma vez que um volume ou diretório foi montado.
* A persistência do evento é configurada numa Subscrição de Eventos no momento da criação e não pode ser modificada uma vez que a Subscrição do Evento é criada. Para alternar a persistência do evento, deve eliminar e recriar a Subscrição do Evento.
* Os eventos persistentes são quase sempre mais lentos do que nas operações de memória, no entanto a diferença de velocidade depende muito das características da unidade. A troca entre a velocidade e a fiabilidade é inerente a todos os sistemas de mensagens, mas torna-se apenas um noticível em larga escala.

Para permitir a persistência do evento numa Subscrição de Eventos, detete `persistencePolicy` para `true`:

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