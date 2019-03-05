---
title: Exportar os dados no Azure IoT Central | Documentos da Microsoft
description: Como exportar dados a partir da sua aplicação do Azure IoT Central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: ecfd9671587c0544cf82aa7ddbccef975e1fe5c6
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310425"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportar os dados no Azure IoT Central

*Este tópico aplica-se aos administradores.*

Este artigo descreve como utilizar a funcionalidade de exportação contínua de dados no Azure IoT Central para exportar os dados para os seus próprios **armazenamento de Blobs do Azure**, **Event Hubs do Azure**, e **doAzureServiceBus** instâncias. Pode exportar **medidas**, **dispositivos**, e **modelos de dispositivos** para o seu destino para caminho de acesso pouco frequente e análise de caminho típico. Pode exportar dados para o armazenamento de BLOBs para executar a análise de tendências de longo prazo no Microsoft Power BI ou exportar dados para os Hubs de eventos e o Service Bus para transformar e aumentar os seus dados em quase em tempo real com o Azure Logic Apps ou as funções do Azure.

> [!Note]
> Quando ativar a exportação contínua de dados, obtém apenas os dados a partir desse momento ou superior. Atualmente, não poderá obter dados para um tempo quando a exportação de dados contínua foi desativada. Para manter mais dados históricos, ative a exportação contínua de dados desde o início.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ser um administrador na sua aplicação do Centro de IoT

## <a name="export-to-blob-storage"></a>Exportar para o Armazenamento de Blobs

Medidas, dispositivos e dados de modelos de dispositivos são exportados para a sua conta de armazenamento, uma vez por minuto, com cada ficheiro que contém o lote de alterações, uma vez que o último exportou o ficheiro. Os dados exportados estão sendo [Apache AVRO](https://avro.apache.org/docs/current/index.html) formato.

Saiba mais sobre [exportar para o armazenamento de BLOBs](howto-export-data-blob-storage.md).

## <a name="export-to-event-hubs-and-service-bus"></a>Exportar para os Hubs de Eventos e o Service Bus

Medidas, dispositivos e dados de modelos de dispositivos são exportados para o seu hub de eventos ou fila do Service Bus ou tópico. Medidas exportado dados chegam em tempo quase real e contém a totalidade da mensagem de seus dispositivos enviados ao IoT Central, não apenas os valores de medidas de si. Dispositivos exportados dados chegam em lotes, uma vez por minuto e contém alterações para propriedades e configurações de todos os dispositivos e modelos de dispositivos exportado contém alterações para todos os modelos de dispositivos.


Saiba mais sobre [exportar para os Hubs de eventos e o Service Bus](howto-export-data-event-hubs-service-bus.md).

## <a name="set-up-export-destination"></a>Configurar o destino de exportação

Se não tiver um barramento / serviço de Hubs de eventos/armazenamento existente para exportar para, siga estes passos:

### <a name="create-storage-account"></a>Criar conta de armazenamento

1. Criar uma [nova conta de armazenamento no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Pode saber mais na [docs do armazenamento do Azure](https://aka.ms/blobdocscreatestorageaccount).
2. Para o tipo de conta, escolha **fins gerais** ou **armazenamento de BLOBs**.
3. Escolha uma subscrição. 

    > [!Note] 
    > Agora, pode exportar dados para outras subscrições que estão **não são as mesmas** que aquela para a sua aplicação pay as you go IoT Central. Irá ligar através de uma cadeia de ligação em vez disso.

4. Crie um contentor na conta de armazenamento. Aceda à sua conta de armazenamento. Sob **serviço Blob**, selecione **procurar Blobs**. Selecione **+ contentor** na parte superior para criar um novo contentor.

### <a name="create-event-hubs-namespace"></a>Criar espaço de nomes de Hubs de eventos

1. Criar uma [novo espaço de nomes de Hubs de eventos no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Pode saber mais na [docs de Event Hubs do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Escolha uma subscrição. 

    > [!Note] 
    > Agora, pode exportar dados para outras subscrições que estão **não são as mesmas** que aquela para a sua aplicação pay as you go IoT Central. Irá ligar através de uma cadeia de ligação em vez disso.
3. Crie um hub de eventos no seu espaço de nomes de Hubs de eventos. Aceda ao seu espaço de nomes e selecione **+ Hub de eventos** na parte superior para criar uma instância do hub de eventos.

### <a name="create-service-bus-namespace"></a>Criar espaço de nomes do Service Bus

1. Criar uma [novo espaço de nomes do Service Bus no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Pode saber mais na [docs do Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Escolha uma subscrição. 

    > [!Note] 
    > Agora, pode exportar dados para outras subscrições que estão **não são as mesmas** que aquela para a sua aplicação pay as you go IoT Central. Irá ligar através de uma cadeia de ligação em vez disso.

3. Aceda ao seu espaço de nomes do Service Bus e selecione **+ fila** ou **+ tópico** na parte superior para criar uma fila ou tópico para exportar para.

## <a name="set-up-continuous-data-export"></a>Configurar a exportação contínua de dados

Agora que tem um destino de armazenamento/Event Hubs/Service Bus para exportar dados, siga estes passos para configurar a exportação contínua de dados. 

1. Inicie sessão na sua aplicação IoT Central.

2. No menu da esquerda, selecione **a exportação contínua de dados**.

    > [!Note]
    > Se não vir a exportação contínua de dados no menu à esquerda, não for um administrador na sua aplicação. Fale com o administrador para configurar a exportação de dados.

    ![Criar novo cde Hub de eventos](media/howto-export-data/export_menu.PNG)

3. Selecione o **+ novo** botão no canto superior direito. Escolha uma das **armazenamento de Blobs do Azure**, **Event Hubs do Azure**, ou **Azure Service Bus** como o destino da exportação. 

    > [!NOTE] 
    > O número máximo de exportações por aplicação é cinco. 

    ![Criar nova exportação contínua de dados](media/howto-export-data/export_new.PNG)

4. Na caixa de lista pendente, selecione seu **espaço de nomes do Hubs de eventos/conta de armazenamento espaço de nomes/Service Bus**. Também pode escolher a última opção na lista que é **introduza uma cadeia de ligação**. 

    > [!NOTE] 
    > Apenas verá espaços de nomes do Hubs de eventos/contas de armazenamento espaços de nomes/Service Bus no **mesma subscrição que a sua aplicação do Centro de IoT**. Se pretender exportar para um destino fora desta subscrição, escolha **introduza uma cadeia de ligação** e veja o passo 5.

    > [!NOTE] 
    > Para exportar aplicações de avaliação, a única forma de configurar contínua de dados de 7 dias é por meio de uma cadeia de ligação. Isto acontece porque a 7 dias aplicações avaliação não tem uma subscrição do Azure associada.

    ![Criar novo cde Hub de eventos](media/howto-export-data/export_create.PNG)

5. (Opcional) Se escolheu **introduza uma cadeia de ligação**, aparece uma caixa de novo para que cole a cadeia de ligação. Para obter a cadeia de ligação para a sua:
    - Conta de armazenamento, aceda à conta de armazenamento no Portal do Azure.
        - Sob **configurações**, selecione **chaves de acesso**
        - Copiar a cadeia de ligação chave1 ou a cadeia de ligação chave2
    - Os Hubs de eventos ou do Service Bus, avance para o espaço de nomes no Portal do Azure.
        - Sob **configurações**, selecione **políticas de acesso partilhado**
        - Escolher a predefinição **RootManageSharedAccessKey** ou criar um novo
        - Copie a cadeia de ligação primária ou secundária
 
6. Na caixa de lista pendente, escolha um hub de eventos/contentores/fila ou tópico.

7. Sob **dados para exportar**, especifique cada tipo de dados para exportar, definindo o tipo como **no**.

6. Para ativar a exportação contínua de dados, certifique-se **exportar dados** é **no**. Selecione **Guardar**.

  ![Configurar a exportação contínua de dados](media/howto-export-data/export_list.PNG)

7. Após alguns minutos, os dados serão apresentados no seu destino escolhido.

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como exportar os seus dados, avance para o passo seguinte:

> [!div class="nextstepaction"]
> [Exportar dados para o armazenamento de Blobs do Azure](howto-export-data-blob-storage.md)

> [!div class="nextstepaction"]
> [Exportar dados para os Hubs de eventos do Azure e o Azure Service Bus](howto-export-data-event-hubs-service-bus.md)

> [!div class="nextstepaction"]
> [Como visualizar os seus dados no Power BI](howto-connect-powerbi.md)
