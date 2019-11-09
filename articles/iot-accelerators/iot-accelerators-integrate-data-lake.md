---
title: Transmitir dados do monitoramento remoto para o Data Lake Store-Azure | Microsoft Docs
description: Saiba como integrar a solução de monitoramento remoto com Azure Data Lake Store usando um trabalho de Azure Stream Analytics.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 0a684151e01b298c60ff17ef1470e0648a425850
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889243"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integre a solução de monitoramento remoto com Azure Data Lake Store

Você pode ter requisitos de análise avançada além do que é oferecido na solução de monitoramento remoto. Azure Data Lake Store é ideal para esse aplicativo, pois ele pode armazenar dados de conjuntos grandes e diversificados, bem como integrar com Azure Data Lake Analytics para fornecer análise sob demanda.

Nestas instruções, você usará um trabalho de Azure Stream Analytics para transmitir dados do Hub IoT em sua solução de monitoramento remoto para um Azure Data Lake Store.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este procedimento, você precisará do seguinte:

* [Implante o acelerador de solução de monitoramento remoto](quickstart-remote-monitoring-deploy.md).
  * A solução de monitoramento remoto implantará o Hub IoT e Azure Stream Analytics trabalho usado neste artigo em sua assinatura do Azure.
* [Implantar um Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * Seu Data Lake Store deve ser implantado na mesma região que sua solução de monitoramento remoto.
  * [Crie uma pasta](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) chamada "streaming" em sua conta.

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Crie um grupo de consumidores dedicado no Hub IoT de sua solução de monitoramento remoto. Isso será usado pelo trabalho de Stream Analytics para transmitir dados para o Data Lake Store.

> [!NOTE]
> Os grupos de consumidores são usados por aplicativos para efetuar pull de dados do Hub IoT do Azure. Você deve criar um novo grupo de consumidores para cada cinco consumidores de saída. Você pode criar até 32 grupos de consumidores.

1. Inicie sessão no Portal do Azure.

1. No portal do Azure, clique no botão **Cloud Shell** .

    ![Ícone de inicialização do portal](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Execute este comando para criar um novo grupo de consumidores:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Use o grupo de recursos e os nomes do Hub IoT de sua solução de monitoramento remoto.

## <a name="create-stream-analytics-job"></a>Criar Stream Analytics trabalho

Crie um trabalho de Azure Stream Analytics para transmitir os dados do Hub IoT para o armazenamento de Azure Data Lake.

1. Clique em **criar um recurso**, selecione Internet das coisas no Marketplace e clique em **Stream Analytics trabalho**.

    ![Novo trabalho de Stream Analytics](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Insira um nome de trabalho e selecione a assinatura e o grupo de recursos apropriados.

1. Selecione um local no próximo ou na mesma região que seu Data Lake Store. Aqui estamos usando o leste dos EUA.

1. Certifique-se de deixar o ambiente de hospedagem como a **nuvem**padrão.

1. Clique em **Criar**.

    ![Criar Stream Analytics trabalho](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Configurar o trabalho de Stream Analytics

1. Vá para o **trabalho de Stream Analytics** em seu grupo de recursos de solução de monitoramento remoto.

1. Na página Visão geral, clique em **entradas**.

    ![Página de visão geral](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Clique em **Adicionar entrada de fluxo** e selecione **Hub IOT** na lista suspensa.

    ![Adicionar entrada](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Na guia nova entrada, insira um alias de entrada de **IoTHub**.

1. Na lista suspensa grupo de consumidores, selecione o grupo de consumidores que você criou anteriormente. Aqui estamos usando **streamanalyticsjob**.

    ![Selecionar entrada](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Clique em **Guardar**.

1. Na página Visão geral, clique em **saídas**.

    ![Adicionar Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Clique em **Adicionar** e selecione **Data Lake Store** na lista suspensa.

    ![Adicionar saída](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Na nova guia saída, insira um alias de saída de **DataLakeStore**.

1. Selecione a conta de Data Lake Store que você criou nas etapas anteriores e forneça a estrutura de pastas para transmitir dados para o repositório.

1. No campo formato de data, insira **/streaming/{Date}/{Time}** . Deixe o formato de data padrão de aaaa/MM/DD e o formato de hora de HH.

    ![Fornecer estrutura de pastas](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Clique em **autorizar**.

    Você precisará autorizar com Data Lake Store para fornecer acesso de gravação ao trabalho do Stream Analytics para o sistema de arquivos.

    ![Autorizar Stream Analytics a Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Você verá um pop-up e, quando o pop-up fechar, o botão autorizar ficará esmaecido após a autorização ser concluída.

    > [!NOTE]
    > Se você vir um erro na janela pop-up, abra uma nova janela do navegador no modo Incognito e tente novamente.

1. Clique em **Guardar**.

## <a name="edit-the-stream-analytics-query"></a>Editar a consulta de Stream Analytics

Azure Stream Analytics usa uma linguagem de consulta do tipo SQL para especificar uma fonte de entrada que transmite dados, transforma esses dados conforme desejado e saída para uma variedade de destinos de armazenamento ou processamento.

1. Na guia Visão geral, clique em **Editar consulta**.

    ![Editar consulta](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. No editor de consultas, substitua os espaços reservados [YourOutputAlias] e [YourInputAlias] pelos valores definidos anteriormente.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Consulta de Stream Analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Clique em **Guardar**.
1. Clique em **Sim** para aceitar as alterações.

## <a name="start-the-stream-analytics-job"></a>Iniciar o trabalho de Stream Analytics

1. Na guia Visão geral, clique em **Iniciar**.

    ![Iniciar Stream Analytics trabalho](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Na guia Iniciar trabalho, clique em **personalizado**.

1. Defina o tempo personalizado para voltar algumas horas para a coleta de dados de quando o dispositivo iniciou o streaming.

1. Clique em **Iniciar**.

    ![Escolher Data personalizada](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Aguarde até que o trabalho entre no estado de execução, se você vir erros de sua consulta, certifique-se de verificar se a sintaxe está correta.

    ![Trabalho em execução](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    O trabalho de streaming começará a ler os dados do Hub IoT e armazenará os dados em seu Data Lake Store. Pode levar alguns minutos para que os dados comecem a aparecer em seu Data Lake Store.

## <a name="explore-the-streaming-data"></a>Explorar os dados de streaming

1. Vá para o Data Lake Store.

1. Na guia Visão geral, clique em **Data Explorer**.

1. No data Explorer, faça uma busca detalhada na pasta **/streaming** Você verá as pastas criadas com o formato AAAA/MM/DD/HH.

    ![Explorar dados de streaming](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Você verá arquivos JSON com um arquivo por hora.

    ![Explorar dados de streaming](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Passos Seguintes

Azure Data Lake Analytics pode ser usado para executar a análise de Big Data em seus conjuntos de dados do Data Lake Store. Saiba mais na [documentação do data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics).
