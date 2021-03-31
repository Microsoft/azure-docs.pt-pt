---
title: 'Quickstart: Enviar eventos usando C - Azure Event Hubs'
description: 'Quickstart: Este artigo fornece uma passagem para a criação de uma aplicação C que envia eventos para Azure Event Hubs.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: bfe1ca1a45f7b33d7431aed13446d8d72f79fb90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85315672"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Quickstart: Enviar eventos para Azure Event Hubs usando C

## <a name="introduction"></a>Introdução
Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como enviar eventos para um centro de eventos usando uma aplicação de consola em C. 

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa do seguinte:

* Um ambiente de desenvolvimento C. Este tutorial assume a pilha gcc em um Azure Linux VM com Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* **Crie um espaço de nomes de Centros de Eventos e um centro de eventos.** Utilize o [portal Azure](https://portal.azure.com) para criar um espaço de nome de centros de eventos tipo, e obtenha as credenciais de gestão que a sua aplicação necessita para comunicar com o centro de eventos. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo](event-hubs-create.md). Obtenha o valor da chave de acesso para o centro do evento seguindo as instruções do artigo: Obtenha a cadeia de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Utilize a chave de acesso no código que escreve mais tarde neste tutorial. O nome-chave predefinido é: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Escreva código para enviar mensagens para Os Centros de Eventos
Nesta secção mostra como escrever uma aplicação C para enviar eventos para o seu centro de eventos. O código utiliza a biblioteca Proton AMQP do [projeto Apache Qpid.](https://qpid.apache.org/) Isto é análogo à utilização de filas e tópicos de Service Bus com AMQP de C, como mostrado [nesta amostra.](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504) Para mais informações, consulte a [documentação do Qpid Proton.](https://qpid.apache.org/proton/index.html)

1. A partir da [página Qpid AMQP Messenger,](https://qpid.apache.org/proton/messenger.html)siga as instruções para instalar o Qpid Protão, dependendo do seu ambiente.
2. Para compilar a biblioteca Proton, instale os seguintes pacotes:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Descarregue a [biblioteca Qpid Proton](https://qpid.apache.org/proton/index.html)e extraia-a, por exemplo:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Criar um diretório de construção, compilar e instalar:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. No seu diretório de trabalho, crie um novo ficheiro chamado **remetente.c** com o seguinte código. Lembre-se de substituir os valores da sua chave/nome SAS, nome do hub do evento e espaço de nome. Também deve substituir uma versão codificada por URL da chave para o **SendRule** criado anteriormente. Pode codificar o URL [aqui.](https://www.w3schools.com/tags/ref_urlencode.asp)
   
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
6. Compilar o ficheiro, assumindo **o cc:**
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Este código utiliza uma janela de saída de 1 para forçar a saída das mensagens o mais rapidamente possível. Recomenda-se que a sua aplicação tente emaçar mensagens para aumentar a produção. Consulte a [página Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html) para obter informações sobre como usar a biblioteca Qpid Proton neste e noutros ambientes, e a partir de plataformas para as quais são fornecidas ligações (atualmente Perl, PHP, Python e Ruby).

Execute a aplicação para enviar mensagens para o centro de eventos. 

Parabéns! Enviou agora mensagens para um hub de eventos.

## <a name="next-steps"></a>Passos seguintes
Leia os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Características e terminologia nos Hubs de Eventos Azure.](event-hubs-features.md)


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
