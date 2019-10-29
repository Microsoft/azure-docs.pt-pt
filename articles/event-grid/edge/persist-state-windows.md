---
title: Persistir o estado no Windows – grade de eventos do Azure IoT Edge | Microsoft Docs
description: Persistir estado no Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 485c6d4a92539a2ba67aece319c68d31649e8045
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992265"
---
# <a name="persist-state-in-windows"></a>Persistir estado no Windows

Os tópicos e as assinaturas criados no módulo grade de eventos são armazenados por padrão no sistema de arquivos do contêiner. Sem persistência, se o módulo for reimplantado, todos os metadados criados serão perdidos. Para preservar os dados entre as implantações, você precisará manter os dados fora do sistema de arquivos do contêiner. Atualmente, somente os metadados são persistidos. Os eventos são armazenados na memória. Se o módulo de grade de eventos for reimplantado ou reiniciado, todos os eventos não entregues serão perdidos.

Este artigo fornece as etapas necessárias para implantar o módulo de grade de eventos com persistência em implantações do Windows.

> [!NOTE]
>O módulo de grade de eventos é executado como um **ContainerUser** de usuário com privilégios Baixos no Windows.

## <a name="persistence-via-volume-mount"></a>Persistência por meio de montagem de volume

Os [volumes do Docker](https://docs.docker.com/storage/volumes/) são usados para preservar dados entre implantações. Para montar um volume, você precisa criá-lo usando comandos do Docker, conceder permissões para que o contêiner possa ler, gravar nele e, em seguida, implantar o módulo. Não há nenhuma provisão para criar automaticamente um volume com as permissões necessárias no Windows. Ele precisa ser criado antes da implantação.

1. Crie um volume executando o seguinte comando:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Por exemplo,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Obtenha o diretório de host para o qual o volume é mapeado executando o comando abaixo

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Por exemplo,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Saída de exemplo:-

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
1. Adicione o grupo **usuários** ao valor apontado pelo **mountpoint** da seguinte maneira:
    1. Inicie o explorador de arquivos.
    1. Navegue até a pasta apontada por **mountpoint**.
    1. Clique com o botão direito do mouse em e selecione **Propriedades**.
    1. Selecione **segurança**.
    1. Em * nomes de grupo ou de usuário, selecione **Editar**.
    1. Selecione **Adicionar**, insira `Users`, selecione **verificar nomes**e selecione **OK**.
    1. Em *permissões para usuários*, selecione **Modificar**e selecione **OK**.
1. Usar **associações** para montar este volume e reimplantar o módulo de grade de eventos de portal do Azure

   Por exemplo,

    ```json
        {
              "Env": [
                "inbound:serverAuth:tlsPolicy=strict",
                "inbound:serverAuth:serverCert:source=IoTEdge",
                "inbound:clientAuth:sasKeys:enabled=false",
                "inbound:clientAuth:clientCert:enabled=true",
                "inbound:clientAuth:clientCert:source=IoTEdge",
                "inbound:clientAuth:clientCert:allowUnknownCA=true",
                "outbound:clientAuth:clientCert:enabled=true",
                "outbound:clientAuth:clientCert:source=IoTEdge",
                "outbound:webhook:httpsOnly=true",
                "outbound:webhook:skipServerCertValidation=false",
                "outbound:webhook:allowUnknownCA=true"
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
   >Não altere a segunda parte do valor de ligação. Ele aponta para um local específico no módulo. Para o módulo de grade de eventos no Windows, ele deve ser **C:\\aplicativo\\metadataDb**.


    Por exemplo,

    ```json
    {
        "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "myeventgridvol:C:\\app\\metadataDb"
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

## <a name="persistence-via-host-directory-mount"></a>Persistência via montagem de diretório de host

Como alternativa, você pode optar por criar um diretório no sistema host e montar esse diretório.

1. Crie um diretório no sistema de arquivos de host executando o comando a seguir.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Por exemplo,

   ```sh
   mkdir C:\myhostdir
   ```
1. Use o **bindings** para montar seu diretório e reimplantar o módulo de grade de eventos do portal do Azure.

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
    >Não altere a segunda parte do valor de ligação. Ele aponta para um local específico no módulo. Para o módulo de grade de eventos no Windows, ele deve ser **C:\\aplicativo\\metadataDb**.

    Por exemplo,

    ```json
    {
        "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "C:\\myhostdir:C:\\app\\metadataDb"
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
