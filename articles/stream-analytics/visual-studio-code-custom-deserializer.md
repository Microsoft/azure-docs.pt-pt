---
title: Crie desserializantes personalizados .NET para trabalhos em nuvem Azure Stream Analytics usando Código de Estúdio Visual
description: Este tutorial demonstra como criar um deserializador personalizado .NET para um trabalho em nuvem Azure Stream Analytics usando o Código do Estúdio Visual.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 0f041910d1b02cc6d1fd48bfd773711c3361c0f5
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822508"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>Crie desserializadores personalizados .NET para Azure Stream Analytics em Código de Estúdio Visual

A Azure Stream Analytics tem [suporte incorporado para três formatos de dados](stream-analytics-parsing-json.md): JSON, CSV e Avro. Com deserializadores .NET personalizados, pode ler dados de outros formatos, tais como [Protocol Buffer,](https://developers.google.com/protocol-buffers/) [Bond](https://github.com/Microsoft/bond) e outros formatos definidos pelo utilizador para trabalhos na nuvem.

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Deserializadores personalizados .NET em Código de Estúdio Visual

Pode criar, testar e depurar um deserializador personalizado .NET para um trabalho na nuvem Azure Stream Analytics utilizando o Código do Estúdio Visual.

### <a name="prerequisites"></a>Pré-requisitos

* Instale [.NET core SDK](https://dotnet.microsoft.com/download) e reinicie o Código do Estúdio Visual.

* Use este [quickstart](quick-create-visual-studio-code.md) para aprender a criar um trabalho stream Analytics usando o Código de Estúdio Visual.

### <a name="create-a-custom-deserializer"></a>Criar um deserializador personalizado

1. Abra um terminal e corra seguindo o comando para criar uma biblioteca de classe .NET no Código do Estúdio Visual para o seu deserializador personalizado chamado **ProtobufDeserializer**.

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. Vá ao diretório do projeto **ProtobufDeserializer** e instale os [pacotes Microsoft.Azure.StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) e [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) NuGet.

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. Adicione a [classe MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) e a [classe MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) ao seu projeto.

4. Construa o projeto **ProtobufDeserializer.**

### <a name="add-an-azure-stream-analytics-project"></a>Adicione um projeto Azure Stream Analytics

1. Abra o Código do Estúdio Visual e selecione **Ctrl+Shift+P** para abrir a paleta de comando. Em seguida, insira as ASA e selecione **ASA: Criar novo projeto**. Nomeie-o **ProtobufCloudDeserializer**.

### <a name="configure-a-stream-analytics-job"></a>Configure um trabalho stream analytics

1. Clique duas **vezesJobConfig.jsem**. Utilize as configurações predefinidos, exceto as seguintes definições:

   |Definição|Valor sugerido|
   |-------|---------------|
   |Recurso global de definições de armazenamento|Escolha a origem de dados na conta atual|
   |Subscrição global de configurações de armazenamento| < o seu > de subscrição|
   |Conta global de armazenamento de configurações de armazenamento| < a sua conta de armazenamento >|
   |Conta de armazenamento de configurações de códigos personalizados|< a sua conta de armazenamento >|
   |Recipiente de definições de descodificadores personalizados|< o seu recipiente de armazenamento >|

2. No interior, a pasta **"Inserir"** input.js **aberta**. **Selecione Adicionar entrada ao vivo** e adicionar uma entrada do armazenamento Azure Data Lake Gen2/Blob, escolha **Selecione da sua assinatura Azure**. Utilize as configurações predefinidos, exceto as seguintes definições:

   |Definição|Valor sugerido|
   |-------|---------------|
   |Nome|Entrada|
   |Subscrição|< o seu > de subscrição|
   |Conta de Armazenamento|< a sua conta de armazenamento >|
   |Contentor|< o seu recipiente de armazenamento >|
   |Tipo de Serialização|Escolha **o costume**|
   |SerializationProjectPath|**Selecione Escolha o caminho** do projeto da biblioteca a partir do CodeLens e selecione o projeto da biblioteca **ProtobufDeserializer** criado na secção anterior. Selecione **projeto de construção** para construir o projeto|
   |SerializationClassName|Selecione **a classe de desserialização** de CodeLens para preencher automaticamente o nome de classe e o caminho DLL|
   |Nome da classe|MessageBodyProto.messageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="Adicione entrada de deserializador personalizado.":::

3. Adicione a seguinte consulta ao ficheiro **ProtobufCloudDeserializer.asaql.**

   ```sql
   SELECT * FROM Input
   ```

4. Descarregue o [ficheiro de entrada protobuf de amostra](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Na pasta **Entradas,** **clique** input.jsà direita e selecione Adicionar **entrada local**. Em seguida, clique duas vezes **local_input1.js** e utilize as configurações predefinidos, exceto para as seguintes definições.

   |Definição|Valor sugerido|
   |-------|---------------|
   |Selecione o caminho do arquivo local|Clique em CodeLens para selecionar < O caminho do ficheiro para o ficheiro de entrada de protobuf de amostra descarregado>|

### <a name="execute-the-stream-analytics-job"></a>Execute o trabalho stream analytics

1. Abra **ProtobufCloudDeserializer.asaql** e selecione **Executar Localmente** a partir de CodeLens e, em seguida, escolha Utilizar a **entrada local** da lista de dropdown.

2. Observe os resultados no separador **Resultados** na vista do diagrama de trabalho à direita. Também pode clicar nos passos do diagrama de trabalho para ver o resultado intermédio. Mais detalhes por favor consulte [Debug localmente usando o diagrama de trabalho.](debug-locally-using-job-diagram-vs-code.md)

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="Verifique o resultado da execução local.":::

Implementou com sucesso um deserializador personalizado para o seu trabalho stream Analytics! Neste tutorial, você testou o deserializador personalizado localmente com dados de entrada local. Também pode testá-lo [usando a entrada de dados ao vivo na nuvem.](visual-studio-code-local-run-live-input.md) Para executar o trabalho na nuvem, você configuraria corretamente a entrada e a saída. Em seguida, submeta o trabalho ao Azure do Visual Studio Code para executar o seu trabalho na nuvem usando o deserializador personalizado que acabou de implementar.

### <a name="debug-your-deserializer"></a>Depurar o seu deserializador

Pode depurar o seu deserializador .NET localmente da mesma forma que depurar código .NET standard. 

1. Adicione pontos de rutura na sua função .NET.

2. Clique **em Executar a** partir da barra de atividade do código do estúdio visual e selecione criar uma launch.jsno **ficheiro**.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="Crie o ficheiro de lançamento.":::

   Escolha **ProtobufCloudDeserializer** e, em seguida, **Azure Stream Analytics** da lista de dropdown.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="Criar o ficheiro de lançamento 2.":::

   Edite a **launch.jsno** ficheiro para substituir <ASAScript> .asaql por ProtobufCloudDeserializer.asaql.
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="Configure o ficheiro de lançamento.":::

3. Prima **F5** para iniciar a depuração. O programa irá parar nos seus pontos de interrupção, conforme esperado. Isto funciona tanto para os dados de entrada local como para os dados de entrada ao vivo.

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="Depurador personalizado.":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este tutorial ao utilizar os seguintes passos:

1. No menu do lado esquerdo no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione o nome do recurso que criou.  

2. Na página do grupo de recursos, selecione **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a implementar um deserializador personalizado .NET para a serialização da entrada de tampão protocolar. Para saber mais sobre a criação de deserializadores personalizados, continue ao seguinte artigo:

> [!div class="nextstepaction"]
> * [Criar diferentes desserializadores .NET para empregos Azure Stream Analytics](custom-deserializer-examples.md)
> * [Teste Azure Stream Analytics empregos localmente com entrada ao vivo usando Código de Estúdio Visual](visual-studio-code-local-run-live-input.md)