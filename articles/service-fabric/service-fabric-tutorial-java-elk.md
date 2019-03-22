---
title: Monitorizar as aplicações no Service Fabric com o ELK no Azure | Microsoft Docs
description: Neste tutorial, saiba como configurar o ELK e monitorizar as suas aplicações do Service Fabric.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 689207339db0250d42fc64c33f43c42c18317d41
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313010"
---
# <a name="tutorial-monitor-your-service-fabric-applications-using-elk"></a>Tutorial: Monitorizar as aplicações do Service Fabric com o ELK

Este tutorial é a quarta parte de uma série. Mostra como utilizar o ELK (Elasticsearch, Logstash e Kibana) para monitorizar as aplicações do Service Fabric em execução no Azure.

Na quarta parte da série, ficará a saber como:
> [!div class="checklist"]
> * Configurar o servidor do ELK no Azure
> * Configurar o Logstash para receber registos dos Hubs de Eventos
> * Visualizar os registos da plataforma e da aplicação no Kibana

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação Java Service Fabric Reliable Services](service-fabric-tutorial-create-java-app.md)
> * [Implementar e depurar a aplicação num cluster local](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Implementar a aplicação num cluster do Azure](service-fabric-tutorial-java-deploy-azure.md)
> * Configurar a monitorização e os diagnósticos da aplicação
> * [Configurar CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Configure a sua aplicação para emitir os registos para a localização especificada na [segunda parte](service-fabric-tutorial-debug-log-local-cluster.md).
* Conclua a [terceira parte](service-fabric-tutorial-java-deploy-azure.md) e tenha um cluster do Service Fabric em execução configurado para enviar registos para os Hubs de Eventos.
* A política nos Hubs de Eventos com a permissão "Escutar" e a chave primária associada da terceira série.

## <a name="download-the-voting-sample-application"></a>Transferir a aplicação de votação de exemplo

Se não conseguiu criar a aplicação de votação de exemplo na [primeira parte desta série de tutoriais](service-fabric-tutorial-create-java-app.md), pode transferi-la. Numa janela do comando, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

## <a name="create-an-elk-server-in-azure"></a>Criar um servidor do ELK no Azure

Pode utilizar um ambiente ELK pré-configurado para este tutorial e, se já tiver um, avance para a secção **Configurar o Logstash**. No entanto, se não tiver um, os passos seguintes criam um no Azure.

1. Criar um ELK Certificada pelo [Bitnami](https://ms.portal.azure.com/#create/bitnami.elk4-6) no Azure. Para a finalidade do tutorial, não existem quaisquer especificações a seguir para a criação deste servidor.

2. Aceda ao seu recurso no portal do Azure e introduza o separador **Diagnósticos de Arranque** na secção **Suporte + Resolução de Problemas**. Em seguida, clique no separador **Registo Série**.

    ![Diagnósticos de Arranque](./media/service-fabric-tutorial-java-elk/bootdiagnostics.png)
3. É necessário pesquisar a palavra-passe nos registos para aceder à instância do Kibana. Assemelha-se ao seguinte fragmento:

    ```bash
    [   25.932766] bitnami[1496]: #########################################################################
    [   25.948656] bitnami[1496]: #                                                                       #
    [   25.962448] bitnami[1496]: #        Setting Bitnami application password to '[PASSWORD]'           #
    [   25.978137] bitnami[1496]: #        (the default application username is 'user')                   #
    [   26.004770] bitnami[1496]: #                                                                       #
    [   26.029413] bitnami[1496]: #########################################################################
    ```

4. Prima o botão Ligar na página Descrição Geral do servidor no portal do Azure para obter os detalhes de início de sessão.

    ![Ligação à VM](./media/service-fabric-tutorial-java-elk/vmconnection.png)

5. SSH no servidor que aloja a imagem do ELK com o seguinte comando

    ```bash
    ssh [USERNAME]@[CONNECTION-IP-OF-SERVER]

    Example: ssh testaccount@104.40.63.157
    ```

## <a name="set-up-elk"></a>Configurar o ELK

1. O primeiro passo é carregar o ambiente de ELK

    ```bash
    sudo /opt/bitnami/use_elk
    ```

2. Se estiver a utilizar um ambiente existente, tem de executar o seguinte comando para parar o serviço Logstash

    ```bash
    sudo /opt/bitnami/ctlscript.sh stop logstash
    ```

3. Execute o seguinte comando para instalar o plug-in do Logstash nos Hubs de Eventos.

    ```bash
    logstash-plugin install logstash-input-azureeventhub
    ```

4. Criar ou modificar o ficheiro de configuração do Logstash existente com o seguinte conteúdo: Se estiver a criar o ficheiro, tem de ser criado em ```/opt/bitnami/logstash/conf/access-log.conf``` se utilizar a imagem do ELK Bitnami no Azure.

    ```json
    input
    {
        azureeventhub
        {
            key => "[RECEIVER-POLICY-KEY-FOR-EVENT-HUB]"
            username => "[RECEIVER-POLICY-NAME]"
            namespace => "[EVENTHUB-NAMESPACENAME]"
            eventhub => "[EVENTHUB-NAME]"
            partitions => 4
        }
    }

    output {
         elasticsearch {
             hosts => [ "127.0.0.1:9200" ]
         }
     }
    ```

5. Para verificar a configuração, execute o seguinte comando:

    ```bash
    /opt/bitnami/logstash/bin/logstash -f /opt/bitnami/logstash/conf/ --config.test_and_exit
    ```

6. Inicie o serviço Logstash

    ```bash
    sudo /opt/bitnami/ctlscript.sh start logstash
    ```

7. Verifique o Elasticsearch para assegurar que está a receber dados

    ```bash
    curl 'localhost:9200/_cat/indices?v'
    ```

8. Aceda ao seu dashboard do Kibana em **http:\//SERVER-IP** e introduza o nome de utilizador e a palavra-passe do Kibana. Se utilizou a imagem do ELK no Azure, o nome de utilizador predefinido é "user" e a palavra-passe é a obtida nos **Diagnósticos de Arranque**.

    ![Kibana](./media/service-fabric-tutorial-java-elk/kibana.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ter um servidor do ELK operacional no Azure
> * Configurar o servidor para receber as informações de diagnóstico do cluster do Service Fabric

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Configurar CI/CD com o Jenkins](service-fabric-tutorial-java-jenkins.md)
