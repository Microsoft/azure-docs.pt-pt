---
title: Transmita dados da Monitorização Remota para data lake store - Azure [ Microsoft Docs
description: Saiba como integrar a solução de Monitorização Remota com a Azure Data Lake Store utilizando um trabalho de Azure Stream Analytics.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 0a684151e01b298c60ff17ef1470e0648a425850
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73889243"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integrar a solução de monitorização remota com a Azure Data Lake Store

Pode ter requisitos de análise avançados para além do que é oferecido na solução de Monitorização Remota. A Azure Data Lake Store é ideal para esta aplicação porque pode armazenar dados de conjuntos de dados massivos e diversos, bem como integrar-se com o Azure Data Lake Analytics para fornecer análises a pedido.

Neste como-fazer, você usará um trabalho de Azure Stream Analytics para transmitir dados do hub IoT na sua solução de Monitorização Remota para uma Loja de Lagos De Dados Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este how-to, você precisará do seguinte:

* [Implante o acelerador de solução](quickstart-remote-monitoring-deploy.md)de monitorização remota .
  * A solução de Monitorização Remota implementará o hub IoT e o trabalho azure Stream Analytics utilizados neste artigo na sua subscrição Azure.
* [Implementar uma Loja de Lagos de Dados Azure](../data-lake-store/data-lake-store-get-started-portal.md)
  * A sua Loja data Lake deve ser implantada na mesma região que a sua solução de Monitorização Remota.
  * [Crie uma pasta](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) chamada "streaming" na sua conta.

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Crie um grupo de consumidores dedicado no centro IoT da sua solução de Monitorização Remota. Isto será utilizado pelo trabalho do Stream Analytics para o streaming de dados para a sua Data Lake Store.

> [!NOTE]
> Os grupos de consumidores são utilizados por aplicações para retirar dados do Azure IoT Hub. Deve criar um novo grupo de consumidores para cada cinco consumidores de produção. Pode criar até 32 grupos de consumidores.

1. Inicie sessão no Portal do Azure.

1. No portal Azure, clique no botão **Cloud Shell.**

    ![Ícone de lançamento do portal](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Execute este comando para criar um novo grupo de consumidores:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Utilize os nomes do grupo de recursos e do hub IoT da sua solução de Monitorização Remota.

## <a name="create-stream-analytics-job"></a>Criar trabalho de análise de fluxo

Crie um trabalho de Análise de Fluxo Saque para transmitir os dados do seu hub IoT para a sua loja Azure Data Lake.

1. Clique em **Criar um recurso,** selecione Internet das Coisas do Mercado e clique em **trabalho de Stream Analytics**.

    ![Novo trabalho de análise de fluxo](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Insira um nome de trabalho e selecione o grupo de Subscrição e Recursos apropriado.

1. Selecione um Local na área próxima ou na mesma região que a sua Data Lake Store. Aqui estamos a usar os EUA Orientais.

1. Certifique-se de deixar o ambiente de hospedagem como a **nuvem**padrão .

1. Clique em **Criar**.

    ![Criar trabalho de análise de fluxo](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Configure o trabalho de Stream Analytics

1. Vá ao **trabalho de Stream Analytics** no seu grupo de recursos de solução de monitorização remota.

1. Na página 'Visão Geral', clique em **Inputs**.

    ![Página de visão geral](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Clique em **Adicionar entrada** de fluxo e selecione **IoT Hub** a partir da queda para baixo.

    ![Adicionar Entrada](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. No separador de entrada Novo, introduza um pseudónimo de Entrada do **IoTHub**.

1. A partir do grupo consumidor drop-down, selecione o grupo de consumidores que criou anteriormente. Aqui estamos a usar o **streamanalyticsjob**.

    ![Selecione Entrada](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Clique em **Guardar**.

1. Na página 'Visão Geral', clique em **Saídas**.

    ![Adicionar Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Clique em **Adicionar** e selecione **Data Lake Store** a partir da queda.

    ![Adicionar Saída](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. No separador de saída New, introduza um pseudónimo de Saída de **DataLakeStore**.

1. Selecione a conta Data Lake Store que criou em passos anteriores e forneça a estrutura de pastas para transmitir dados para a loja.

1. No campo formato Data, **introduza /streaming/{date}/{time}**. Deixe o formato de data padrão do formato YYYY/MM/DD e do formato de tempo de HH.

    ![Fornecer estrutura de pasta](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Clique **em Autorizar**.

    Terá de autorizar com a Data Lake Store para dar ao stream analytics o acesso ao sistema de ficheiros.

    ![Autorizar o Stream Analytics à Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Você verá um popup e assim que o popup fechar o botão Autorizar será acinzentado após a autorização estar completa.

    > [!NOTE]
    > Se vir um erro na janela popup, abra uma nova janela do navegador no Modo Incógnito e tente novamente.

1. Clique em **Guardar**.

## <a name="edit-the-stream-analytics-query"></a>Editar a consulta Stream Analytics

O Azure Stream Analytics utiliza um idioma de consulta semelhante ao SQL para especificar uma fonte de entrada que transmite dados, transforma esses dados conforme desejado, e produção para uma variedade de destinos de armazenamento ou processamento.

1. No separador Overview, clique em **Editar consulta**.

    ![Editar Consulta](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. No editor da Query, substitua os espaços reservados [YourOutputAlias] e [YourInputAlias] com os valores que definiu anteriormente.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Consulta de análise de fluxo](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Clique em **Guardar**.
1. Clique **sim** para aceitar as alterações.

## <a name="start-the-stream-analytics-job"></a>Inicie o trabalho de Stream Analytics

1. No separador 'Visão Geral', clique em **Iniciar**.

    ![Iniciar trabalho de análise de fluxo](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. No separador Iniciar o trabalho, clique em **Custom**.

1. Detete o tempo personalizado para voltar algumas horas para recolher dados a partir do momento em que o seu dispositivo começou a ser transmitido.

1. Clique em **Iniciar**.

    ![Escolha a data personalizada](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Espere até que o trabalho entre em estado de funcionamento, se vir erros pode ser da sua consulta, certifique-se de verificar se a sintaxe está correta.

    ![Funcionamento do emprego](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    O trabalho de streaming começará a ler dados do seu IoT Hub e armazenar os dados na sua Data Lake Store. Pode levar alguns minutos para que os dados comecem a aparecer na sua Loja data Lake.

## <a name="explore-the-streaming-data"></a>Explore os dados de streaming

1. Vá à sua Loja data lake.

1. No separador Overview, clique no explorador de **dados**.

1. No explorador de dados, faça uma broca até à pasta **/streaming.** Verá pastas criadas com formato YYYY/MM/DD/HH.

    ![Explore dados de streaming](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Verá ficheiros JSON com um ficheiro por hora.

    ![Explore dados de streaming](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Passos Seguintes

O Azure Data Lake Analytics pode ser usado para realizar uma análise de big data nos seus conjuntos de dados data Lake Store. Saiba mais sobre a Documentação análise do [Lago de Dados.](https://docs.microsoft.com/azure/data-lake-analytics)
