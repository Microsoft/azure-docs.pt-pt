---
title: Integrar a solução de monitorização remota com o Data Lake Store - Azure | Documentos da Microsoft
description: Saiba como integrar a solução de monitorização remota com o Azure Data Lake Store através de uma tarefa do Azure Stream Analytics.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 021f18f588613817110539d408f9260fb9247895
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61449501"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integrar a solução de monitorização remota com o Azure Data Lake Store

Poderá ter avançada requisitos de análise para além de que é oferecido na solução de monitorização remota. Azure Data Lake Store é ideal para esta aplicação porque ele pode armazenar dados de conjuntos de dados em massa e diversos, bem como integrar com o Azure Data Lake Analytics para fornecer análise no local.

Nesta explicação de procedimento, irá utilizar uma tarefa do Azure Stream Analytics para transmitir dados a partir do IoT hub na sua solução de monitorização remota para um Store do Azure Data Lake.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este procedimentos, terá o seguinte:

* [Implementar o acelerador de solução de monitorização remota](quickstart-remote-monitoring-deploy.md).
  * A solução de monitorização remota irá implementar o IoT hub e o trabalho do Azure Stream Analytics utilizadas neste artigo para a sua subscrição do Azure.
* [Implementar um Store do Azure Data Lake](../data-lake-store/data-lake-store-get-started-portal.md)
  * Store de Lake seus dados devem ser implementado para a mesma região que a sua solução de monitorização remota.
  * [Crie uma pasta](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) com o nome "transmissão em fluxo" na sua conta.

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Crie um grupo de consumidores dedicado no hub IoT da sua solução de monitorização remota. Isso será usado por tarefa do Stream Analytics para dados de transmissão em fluxo para Store de Lake seus dados.

> [!NOTE]
> Grupos de consumidores são utilizados por aplicações para obter dados do IoT Hub do Azure. Deve criar um novo grupo de consumidores para todos os consumidores de saída de cinco. Pode criar até 32 grupos de consumidores.

1. Inicie sessão no Portal do Azure.

1. No portal do Azure, clique nas **Cloud Shell** botão.

    ![Ícone do portal de lançamento](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Execute este comando para criar um novo grupo de consumidores:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Utilize o grupo de recursos e os nomes do hub IoT partir da sua solução de monitorização remota.

## <a name="create-stream-analytics-job"></a>Criar tarefa do Stream Analytics

Crie uma tarefa Azure Stream Analytics para transmitir os dados do seu hub IoT para o Azure Data Lake store.

1. Clique em **criar um recurso**, selecione a Internet das coisas do Marketplace e clique em **tarefa do Stream Analytics**.

    ![Nova tarefa do Stream Analytics](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Introduza um nome de tarefa e selecione o grupo de recursos e subscrição adequado.

1. Selecione uma localização na próxima ou na mesma região que o Store de Lake de dados. Aqui estamos a utilizar E.U.A. Leste.

1. Certifique-se para deixar o ambiente de alojamento como predefinição **Cloud**.

1. Clique em **Criar**.

    ![Criar tarefa do Stream Analytics](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Configurar a tarefa do Stream Analytics

1. Vá para o **tarefa do Stream Analytics** no seu grupo de recursos de solução de monitorização remota.

1. Na página Descrição geral, clique em **entradas**.

    ![Página de descrição geral](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Clique em **Adicionar entrada de fluxo** e selecione **IoT Hub** na lista suspensa.

    ![Adicionar entrada](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. No separador de entrada, introduza um alias de entrada da **IoTHub**.

1. O consumidor grupo lista pendente, selecione o grupo de consumidores que criou anteriormente. Aqui estamos usando **streamanalyticsjob**.

    ![Selecione a entrada](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Clique em **Guardar**.

1. Na página Descrição geral, clique em **saídas**.

    ![Adicionar o Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Clique em **Add** e selecione **Data Lake Store** na lista suspensa.

    ![Adicionar a saída](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. No novo separador de saída, introduza um alias de saída da **DataLakeStore**.

1. Selecione a conta de Data Lake Store que criou nos passos anteriores e forneça a estrutura de pastas para transmitir dados para o arquivo.

1. No campo de formato de data, digite **/streaming/ {date} / {hora}** . Deixe o formato de data padrão de DD/MM/AAAA e formato de hora de HH.

    ![Forneça a estrutura de pastas](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Clique em **autorizar**.

    Terá de conceder autorização com o Data Lake Store para dar o acesso de escrita de tarefa do Stream analytics para o sistema de ficheiros.

    ![Autorizar o Stream Analytics para o Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Verá um pop-up e depois fecha a janela de pop-up autorizar botão estarão esbatida após a conclusão da autorização.

    > [!NOTE]
    > Se vir um erro na janela de pop-up, abra uma nova janela do browser no modo de Incognito e tente novamente.

1. Clique em **Guardar**.

## <a name="edit-the-stream-analytics-query"></a>Editar a consulta do Stream Analytics

O Azure Stream Analytics utiliza uma linguagem de consulta do tipo SQL para especificar uma origem de entrada que transmite os dados, transformar esses dados como desejado e de saída a uma variedade de destinos de armazenamento ou processamento.

1. Na guia visão geral, clique em **Editar consulta**.

    ![Editar consulta](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. No editor de consultas, substitua [YourOutputAlias] e [YourInputAlias] marcadores de posição pelos valores que definiu anteriormente.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Consulta do Stream Analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Clique em **Guardar**.
1. Clique em **Sim** para aceitar as alterações.

## <a name="start-the-stream-analytics-job"></a>Iniciar a tarefa de Stream Analytics

1. Na guia visão geral, clique em **iniciar**.

    ![Iniciar a tarefa do Stream Analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. No separador de tarefa de início, clique em **personalizado**.

1. Hora personalizada definida para voltar atrás algumas horas para recolher dados a partir de quando o seu dispositivo foi iniciada a transmissão em fluxo.

1. Clique em **Iniciar**.

    ![Escolher data personalizada](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Aguarde até que a tarefa entra em estado, de execução se observar erros poderia ser da sua consulta, certifique-se verificar se a sintaxe está correta.

    ![Tarefa em execução](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    A tarefa de transmissão em fluxo irá começar a ler dados a partir do seu IoT Hub e armazenar os dados no seu Store de Lake de dados. Pode demorar alguns minutos para que os dados comecem a aparecer na sua Store de Lake de dados.

## <a name="explore-the-streaming-data"></a>Explorar os dados de transmissão em fluxo

1. Aceda ao seu Store de Lake de dados.

1. Na guia visão geral, clique em **Explorador de dados**.

1. No Explorador de dados, desagregar para o **/ transmissão em fluxo** pasta. Verá as pastas criadas com o formato de aaaa/MM/DD/HH.

    ![Explorar os dados de transmissão em fluxo](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Irá ver ficheiros json com um ficheiro por hora.

    ![Explorar os dados de transmissão em fluxo](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Próximos Passos

O Azure Data Lake Analytics pode ser utilizado para efetuar análises de grandes volumes de dados nos seus conjuntos de dados do Data Lake Store. Obter mais informações sobre o [documentação do Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics).
