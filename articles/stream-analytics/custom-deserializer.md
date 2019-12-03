---
title: Tutorial-desserializadores personalizados do .NET para Azure Stream Analytics trabalhos de nuvem
description: Este tutorial demonstra como criar um desserializador do .NET personalizado para um Azure Stream Analytics trabalho de nuvem usando o Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: f5fa0a4398c904113dbce5d80844b42b6e775df0
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702444"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Tutorial: desserializadores .NET personalizados para Azure Stream Analytics

Azure Stream Analytics tem [suporte interno para três formatos de dados](stream-analytics-parsing-json.md): JSON, CSV e Avro. Com os desserializadores do .NET personalizados, você pode ler dados de outros formatos, como [buffer de protocolo](https://developers.google.com/protocol-buffers/), [títulos](https://github.com/Microsoft/bond) e outros formatos definidos pelo usuário para trabalhos de nuvem e de borda.

Este tutorial demonstra como criar um desserializador do .NET personalizado para um Azure Stream Analytics trabalho de nuvem usando o Visual Studio. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um desserializador personalizado para o buffer de protocolo.
> * Crie um trabalho de Azure Stream Analytics no Visual Studio.
> * Configure seu trabalho de Stream Analytics para usar o desserializador personalizado.
> * Execute o trabalho de Stream Analytics localmente para testar o desserializador personalizado.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Instale o [visual studio 2017](https://www.visualstudio.com/downloads/) ou o [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/). As edições Enterprise (Ultimate/Premium), Professional e Community são suportadas. Não há suporte para a Express Edition.

* [Instale o Stream Analytics Tools para Visual Studio](stream-analytics-tools-for-visual-studio-install.md) ou atualize para a versão mais recente. Há suporte para as seguintes versões do Visual Studio:
   * Visual Studio 2015
   * Visual Studio 2017

* Abra o **Cloud Explorer** no Visual Studio e entre em sua assinatura do Azure.

* Crie um contêiner em sua conta de armazenamento do Azure.
O contêiner que você criar será usado para armazenar os ativos relacionados ao seu trabalho de Stream Analytics. Se já tiver uma conta de armazenamento com contentores existentes, poderá utilizá-los. Caso contrário, precisará de [criar um novo contentor](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Criar um desserializador personalizado

1. Abra o Visual Studio e selecione **arquivo > novo projeto de >** . Pesquise **Stream Analytics** e selecione **Azure Stream Analytics projeto de desserializador personalizado (.net)** . Dê um nome ao projeto, como o **desserializador Protobuf**.

   ![Criar projeto de biblioteca de classes padrão do Visual Studio dotnet](./media/custom-deserializer/create-dotnet-library-project.png)

2. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto de **desserializador Protobuf** e selecione **gerenciar pacotes NuGet** no menu. Em seguida, instale os pacotes NuGet **Microsoft. Azure. StreamAnalytics** e **Google. Protobuf** .

3. Adicione a [classe MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) e a [classe MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) ao seu projeto.

4. Crie o projeto de **desserializador Protobuf** .

## <a name="add-an-azure-stream-analytics-project"></a>Adicionar um projeto de Azure Stream Analytics

1. Em Gerenciador de Soluções, clique com o botão direito do mouse na solução **desserializador Protobuf** e selecione **Adicionar > novo projeto**. Em **Azure Stream Analytics > Stream Analytics**, escolha **Azure Stream Analytics aplicativo**. Nomeie-o **ProtobufCloudDeserializer** e selecione **OK**. 

2. Clique com o botão direito do mouse em **referências** no projeto **ProtobufCloudDeserializer** Azure Stream Analytics. Em **projetos**, adicione **desserializador Protobuf**. Ele deve ser preenchido automaticamente para você.

## <a name="configure-a-stream-analytics-job"></a>Configurar um trabalho de Stream Analytics

1. Clique duas vezes em **JobConfig. JSON**. Use as configurações padrão, exceto as seguintes:

   |Definição|Valor sugerido|
   |-------|---------------|
   |Recurso de configurações de armazenamento global|Escolha a origem de dados na conta atual|
   |Assinatura de configurações de armazenamento global| < sua assinatura >|
   |Conta de armazenamento de configurações de armazenamento global| < sua conta de armazenamento >|
   |Recurso de configurações de armazenamento de código personalizado|Escolha a origem de dados na conta atual|
   |Conta de armazenamento de configurações de armazenamento de código personalizado|< sua conta de armazenamento >|
   |Contêiner de configurações de armazenamento de código personalizado|< seu contêiner de armazenamento >|

2. Em **entradas**, clique duas vezes em **Input. JSON**. Use as configurações padrão, exceto as seguintes:

   |Definição|Valor sugerido|
   |-------|---------------|
   |Origem|Armazenamento de Blobs|
   |Recurso|Escolha a origem de dados na conta atual|
   |Subscrição|< sua assinatura >|
   |Conta de Armazenamento|< sua conta de armazenamento >|
   |Contentor|< seu contêiner de armazenamento >|
   |Formato de serialização de evento|Outro (Protobuf, XML, proprietário...)|
   |Recurso|Carregar da referência de projeto ASA ou codebehind|
   |Nome do assembly CSharp|ProtobufDeserializer. dll|
   |Nome da classe|MessageBodyProto.MessageBodyDeserializer|
   |Tipo de compactação de evento|Nenhuma|

3. Adicione a consulta a seguir ao arquivo **script. asaql** .

   ```sql
   SELECT * FROM Input
   ```

4. Baixe o [arquivo de entrada protobuf de exemplo](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Na pasta **entradas** , clique com o botão direito do mouse em **Input. JSON** e selecione **Adicionar entrada local**. Em seguida, clique duas vezes em **local_Input. JSON** e defina as seguintes configurações:

   |Definição|Valor sugerido|
   |-------|---------------|
   |Alias de Entrada|Input|
   |Tipo de Fonte|Fluxo de Dados|
   |Formato de serialização de evento|Outro (Protobuf, XML, proprietário...)|
   |Nome do assembly CSharp|ProtobufDeserializer. dll|
   |Nome da classe|MessageBodyProto.MessageBodyDeserializer|
   |Caminho do arquivo de entrada local|< o caminho do arquivo para o arquivo de entrada protobuf de exemplo baixado >|

## <a name="execute-the-stream-analytics-job"></a>Executar o trabalho de Stream Analytics

1. Abra **script. asaql** e selecione **executar localmente**.

2. Observe os resultados em **Stream Analytics resultados da execução local**.

Você implementou com êxito um desserializador personalizado para seu trabalho de Stream Analytics! Neste tutorial, você testou o desserializador personalizado localmente. Para seus dados reais, você configuraria corretamente a entrada e a saída. Em seguida, envie o trabalho para o Azure do Visual Studio para executar seu trabalho na nuvem usando o desserializador personalizado que você acabou de implementar.

## <a name="debug-your-deserializer"></a>Depurar seu desserializador

Você pode depurar o desserializador do .NET localmente da mesma maneira que depura o código .NET padrão. 

1. Adicione pontos de interrupção em sua função.

2. Prima **F5** para iniciar a depuração. O programa irá parar nos seus pontos de interrupção, conforme esperado.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este tutorial ao utilizar os seguintes passos:

1. No menu do lado esquerdo no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione o nome do recurso que criou.  

2. Na página do grupo de recursos, selecione **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a implementar um desserializador .NET personalizado para a serialização de entrada do buffer de protocolo. Para saber mais sobre como criar desserializadores personalizados, vá para o seguinte artigo:

> [!div class="nextstepaction"]
> [Criar desserializadores .NET diferentes para trabalhos de Azure Stream Analytics](custom-deserializer-examples.md)
