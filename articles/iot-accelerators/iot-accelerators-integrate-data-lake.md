---
title: Dados de fluxo de Monitorização Remota para Data Lake Store - Azure Microsoft Docs
description: Saiba como integrar a solução de Monitorização Remota com a Azure Data Lake Store utilizando um trabalho de Azure Stream Analytics.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: bbd895686b52ae7d1ced00b635d4d33e93c970a7
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92069850"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integre a solução de monitorização remota com a Azure Data Lake Store

Pode ter requisitos de análise avançados para além do que é oferecido na solução de Monitorização Remota. A Azure Data Lake Store é ideal para esta aplicação, pois pode armazenar dados de conjuntos de dados massivos e diversos, bem como integrar-se com o Azure Data Lake Analytics para fornecer análises a pedido.

Neste como fazer, utilizará um trabalho do Azure Stream Analytics para transmitir dados do hub IoT na sua solução de Monitorização Remota para uma Loja de Lagos de Dados Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este como fazer, precisará do seguinte:

* [Implementar o acelerador de solução de monitorização remota](quickstart-remote-monitoring-deploy.md).
  * A solução de Monitorização Remota irá implantar o hub IoT e o trabalho Azure Stream Analytics utilizados neste artigo na sua assinatura Azure.
* [Implementar uma loja Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * A sua Data Lake Store deve ser implantada na mesma região que a sua solução de Monitorização Remota.
  * [Crie uma pasta](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) chamada "streaming" na sua conta.

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Crie um grupo de consumidores dedicado no centro IoT da sua solução de Monitorização Remota. Isto será utilizado pelo trabalho stream Analytics para transmitir dados para a sua Data Lake Store.

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

Crie um trabalho Azure Stream Analytics para transmitir os dados do seu hub IoT para a sua loja Azure Data Lake.

1. Clique **em Criar um recurso,** selecione Internet of Things a partir do Marketplace e clique em Stream Analytics **job**.

    ![Novo trabalho de análise de fluxo](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Insira um nome de trabalho e selecione o grupo de subscrição e recursos apropriados.

1. Selecione uma localização na região próxima ou na mesma região que a sua Data Lake Store. Aqui estamos a usar os Eua Orientais.

1. Certifique-se de deixar o ambiente de hospedagem como a **Nuvem**padrão .

1. Clique em **Criar**.

    ![Criar trabalho de análise de fluxo](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Configure o trabalho stream analytics

1. Vá ao **trabalho stream Analytics** no seu grupo de recursos de monitorização remota.

1. Na página 'Vista Geral', clique **em Entradas**.

    ![Página geral](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Clique **em Adicionar entrada de fluxo** e selecione **IoT Hub** a partir do drop-down.

    ![Adicionar entrada](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. No novo separador de entrada, insira um pseudónimo de Entrada do **IoTHub**.

1. A partir do grupo de consumidores, selecione o grupo de consumidores que criou anteriormente. Aqui estamos a usar **o trabalho de streamanalytics.**

    ![Selecione Entrada](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Clique em **Guardar**.

1. Na página 'Vista Geral', clique em **Saídas**.

    ![Adicionar data lake store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Clique **em Adicionar** e selecione Data Lake **Store** a partir do drop-down.

    ![Adicionar saída](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. No novo separador de saída, insira um pseudónimo de Saída da **DataLakeStore.**

1. Selecione a conta Data Lake Store que criou em etapas anteriores e forneça a estrutura da pasta para transmitir dados para a loja.

1. No campo do formato Data, introduza **/streaming/{date}/{time}**. Deixe o formato de data padrão de YYYY/MM/DD e formato tempo de HH.

    ![Fornecer estrutura de pasta](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Clique **em Autorizor**.

    Terá de autorizar com a Data Lake Store que dê acesso ao stream analytics para o sistema de ficheiros.

    ![Autorizar stream analytics para data lake store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Verá um pop-up e assim que o botão de pop-up fechar, o botão Authorize será acinzentado após a conclusão da autorização.

    > [!NOTE]
    > Se vir um erro na janela popup, abra uma nova janela do navegador no Modo Incógnito e tente novamente.

1. Clique em **Guardar**.

## <a name="edit-the-stream-analytics-query"></a>Editar a consulta Stream Analytics

O Azure Stream Analytics utiliza uma linguagem de consulta semelhante ao SQL para especificar uma fonte de entrada que transmite dados, transforma esses dados conforme desejado, e a saída para uma variedade de destinos de armazenamento ou processamento.

1. No separador Vista Geral, clique em **Editar consulta**.

    ![Editar Consulta](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. No editor de Consulta, substitua os espaços reservados [YourOutputAlias] e [YourInputAlias] pelos valores que definiu anteriormente.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Consulta de Análise de Fluxo](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Clique em **Guardar**.
1. Clique **em Sim** para aceitar as alterações.

## <a name="start-the-stream-analytics-job"></a>Inicie o trabalho stream analytics

1. No separador Visão Geral, clique em **Iniciar**.

    ![Iniciar trabalho de analítica de fluxo](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. No separador iniciar o trabalho, clique **em 'Personalizado'.**

1. Desaça o tempo personalizado para voltar algumas horas para recolher dados a partir de quando o seu dispositivo começou a ser transmitido.

1. Clique em **Iniciar**.

    ![Escolha data personalizada](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Aguarde até que o trabalho entre em funcionamento, se vir erros que podem ser da sua consulta, certifique-se de verificar se a sintaxe está correta.

    ![Trabalho correndo](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    O trabalho de streaming começará a ler dados do seu IoT Hub e armazenará os dados na sua Data Lake Store. Pode levar alguns minutos para que os dados comecem a aparecer na sua Data Lake Store.

## <a name="explore-the-streaming-data"></a>Explore os dados de streaming

1. Vá à sua Loja data lake.

1. No separador 'Vista Geral', clique no **explorador de dados.**

1. No explorador de dados, desaca a pasta **/streaming.** Verá pastas criadas com formato YYYY/MM/DD/HH.

    ![Explore dados de streaming](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Verá ficheiros json com um ficheiro por hora.

    ![Explore dados de streaming](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Passos Seguintes

O Azure Data Lake Analytics pode ser usado para realizar uma grande análise de dados nos seus conjuntos de dados da Data Lake Store. Saiba mais sobre a Documentação de Análise do [Lago de Dados.](../data-lake-analytics/index.yml)