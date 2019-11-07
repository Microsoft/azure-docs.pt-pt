---
title: 'Início rápido: enviar eventos usando o C-hubs de eventos do Azure'
description: 'Início rápido: Este artigo fornece uma explicação para a criação de um aplicativo C que envia eventos para os hubs de eventos do Azure.'
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
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 5bd4bb66b7e3c3ec37724f8684105befbc9132ff
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720674"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Início rápido: enviar eventos para os hubs de eventos do Azure usando C

## <a name="introduction"></a>Introdução
Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como enviar eventos para um hub de eventos usando um aplicativo de console em C. 

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa do seguinte:

* Um ambiente de desenvolvimento do C. Este tutorial pressupõe a pilha gcc em uma VM Linux do Azure com o Ubuntu 14, 4.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* **Crie um namespace de hubs de eventos e um hub de eventos**. Use o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo hubs de eventos e obter as credenciais de gerenciamento que seu aplicativo precisa para se comunicar com o Hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento neste [artigo](event-hubs-create.md). Obtenha o valor da chave de acesso para o Hub de eventos seguindo as instruções do artigo: [obter cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Você usa a chave de acesso no código que escreve posteriormente neste tutorial. O nome da chave padrão é: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Escrever código para enviar mensagens para os hubs de eventos
Nesta seção, mostra como gravar um aplicativo C para enviar eventos para o Hub de eventos. O código usa a biblioteca Proton AMQP do [projeto Apache QPID](https://qpid.apache.org/). Isso é análogo ao uso de filas e tópicos do barramento de serviço com AMQP de C, conforme mostrado neste [exemplo](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Para obter mais informações, consulte a [documentação do QPID Proton](https://qpid.apache.org/proton/index.html).

1. Na [página do QPID AMQP Messenger](https://qpid.apache.org/proton/messenger.html), siga as instruções para instalar o QPID Proton, dependendo do seu ambiente.
2. Para compilar a biblioteca Proton, instale os seguintes pacotes:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Baixe a [biblioteca QPID Proton](https://qpid.apache.org/proton/index.html)e extraia-a, por exemplo:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Crie um diretório de compilação, compile e instale:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. Em seu diretório de trabalho, crie um novo arquivo chamado **Sender. c** com o código a seguir. Lembre-se de substituir os valores de sua chave/nome de SAS, o nome do hub de eventos e o namespace. Você também deve substituir uma versão codificada por URL da chave para o **SendRule** criado anteriormente. Você pode codificá-lo por URL [aqui](https://www.w3schools.com/tags/ref_urlencode.asp).
   
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
6. Compile o arquivo, supondo que **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Esse código usa uma janela de saída de 1 para forçar as mensagens assim que possível. É recomendável que seu aplicativo tente executar mensagens em lote para aumentar a taxa de transferência. Consulte a [página do QPID AMQP Messenger](https://qpid.apache.org/proton/messenger.html) para obter informações sobre como usar a biblioteca QPID Proton neste e em outros ambientes e de plataformas para as quais as associações são fornecidas (atualmente, Perl, PHP, Python e Ruby).

Execute o aplicativo para enviar mensagens para o Hub de eventos. 

Parabéns! Enviou agora mensagens para um hub de eventos.

## <a name="next-steps"></a>Passos seguintes
Leia os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Recursos e terminologia nos hubs de eventos do Azure](event-hubs-features.md).


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
