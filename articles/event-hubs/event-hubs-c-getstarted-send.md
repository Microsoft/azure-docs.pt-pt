---
title: Enviar eventos com C - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece um passo a passo para criar uma aplicação de C para enviar eventos para Hubs de eventos do Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6cb1f788f41fe07516d759b177e1d76405dd2bf8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57529732"
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Enviar eventos para Hubs de eventos do Azure com C

## <a name="introduction"></a>Introdução
Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como enviar eventos para um hub de eventos através de uma aplicação de consola em C. 

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa do seguinte:

* Um ambiente de desenvolvimento do C. Este tutorial pressupõe a pilha de gcc numa VM do Linux do Azure com o Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um espaço de nomes de Hubs de Eventos e um hub de eventos
O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nomes e um hub de eventos, siga o procedimento [este artigo](event-hubs-create.md).

Obter o valor da chave de acesso para o hub de eventos ao seguir as instruções do artigo: [Obter cadeia de ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Utilize a chave de acesso no código escrito mais tarde neste tutorial. O nome da chave predefinida é: **RootManageSharedAccessKey**.

Agora, continue com os seguintes passos neste tutorial.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Escrever código para enviar mensagens para os Hubs de eventos
Esta secção mostra como escrever um aplicativo de C para enviar eventos para o hub de eventos. O código utiliza a biblioteca de Proton AMQP dos [projeto Apache Qpid](https://qpid.apache.org/). Isso é análogo ao utilizar tópicos e filas do Service Bus com o AMQP do C conforme mostrado [neste exemplo](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Para obter mais informações, consulte a [documentação Qpid Proton](https://qpid.apache.org/proton/index.html).

1. Partir do [página Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html), siga as instruções para instalar Qpid Proton, dependendo do seu ambiente.
2. Para compilar a biblioteca de Proton, instale os seguintes pacotes:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Transfira o [biblioteca Qpid Proton](https://qpid.apache.org/proton/index.html)e extraí-lo, por exemplo:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Crie um diretório de compilação, a compilação e a instalação:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. No seu diretório de trabalho, crie um novo ficheiro chamado **sender.c** com o código a seguir. Lembre-se substituir os valores para o seu/nome da chave SAS, o nome do hub de eventos e o espaço de nomes. Também deve substituir uma versão com codificação URL da chave para o **SendRule** criado anteriormente. Pode codificar o URL ele [aqui](https://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Compilar o arquivo, partindo do princípio **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Esse código usa uma janela de saída de 1 para forçar as mensagens de saída logo que possível. Recomenda-se que seu aplicativo tente para agrupar mensagens para aumentar o débito. Consulte a [página Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html) para informações sobre como utilizar a biblioteca de Qpid Proton neste e noutros ambientes e a partir de plataformas para as quais são fornecidas ligações (atualmente Perl, PHP, Python e Ruby).

Execute a aplicação para enviar mensagens para o hub de eventos. 

Parabéns! Enviou agora mensagens para um hub de eventos.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre a receção de eventos do hub de eventos, clique no idioma de receção adequado sob **receber eventos de um hub de eventos** nó no índice.


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
