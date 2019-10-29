---
title: Persistir o estado no Linux-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Manter metadados no Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 6639b8f4d947720db38a6b366c47700a56154af3
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992278"
---
# <a name="persist-state-in-linux"></a>Persistir o estado no Linux

Os tópicos e as assinaturas criados no módulo grade de eventos são armazenados por padrão no sistema de arquivos do contêiner. Sem persistência, se o módulo for reimplantado, todos os metadados criados serão perdidos. No momento, somente os metadados são persistidos. Os eventos são armazenados na memória. Se o módulo de grade de eventos for reimplantado ou reiniciado, todos os eventos não entregues serão perdidos.

Este artigo fornece as etapas para implantar o módulo de grade de eventos com persistência em implantações do Linux.

> [!NOTE]
>O módulo de grade de eventos é executado como um usuário com poucos privilégios com UID `2000` e nome `eventgriduser`.

## <a name="persistence-via-volume-mount"></a>Persistência por meio de montagem de volume

 Os [volumes do Docker](https://docs.docker.com/storage/volumes/) são usados para preservar os dados entre implantações. Você pode permitir que o Docker crie automaticamente um volume nomeado como parte da implantação do módulo de grade de eventos. Essa opção é a opção mais simples. Você pode especificar o nome do volume a ser criado na seção de **associações** da seguinte maneira:

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
>Não altere a segunda parte do valor de ligação. Ele aponta para um local específico dentro do módulo. Para o módulo de grade de eventos no Linux, ele deve ser **/app/Metadata**.

Por exemplo, a configuração a seguir resultará na criação do volume **egmetadataDbVol** em que os metadados serão persistidos.

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
      "egmetadataDbVol:/app/metadataDb"
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

Como alternativa, você pode criar um volume do Docker usando comandos de cliente do Docker. 

## <a name="persistence-via-host-directory-mount"></a>Persistência via montagem de diretório de host

Em vez de um volume do Docker, você também tem a opção de montar uma pasta de host.

1. Primeiro, crie um usuário com o nome **eventgriduser** e a ID **2000** no computador host executando o seguinte comando:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Crie um diretório no sistema de arquivos do host executando o comando a seguir.

   ```sh
   md <your-directory-name-here>
   ```

    Por exemplo, a execução do comando a seguir criará um diretório chamado **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Em seguida, torne o proprietário do **eventgriduser** desta pasta executando o comando a seguir.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Por exemplo,

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Use **bindings** para montar o diretório e reimplantar o módulo de grade de eventos do portal do Azure.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

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
                  "/myhostdir:/app/metadataDb"
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
    >Não altere a segunda parte do valor de ligação. Ele aponta para um local específico dentro do módulo. Para o módulo de grade de eventos no Linux, ele deve ser **/app/Metadata**.
