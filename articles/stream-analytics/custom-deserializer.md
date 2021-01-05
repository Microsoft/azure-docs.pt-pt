---
title: Tutorial - Deserializadores personalizados .NET para trabalhos em nuvem Azure Stream Analytics
description: Este tutorial demonstra como criar um deserializador personalizado .NET para um trabalho em nuvem Azure Stream Analytics usando o Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/17/2020
ms.openlocfilehash: 2353d15707fe215bfcab7912f2a9c598c4af7e49
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822017"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Tutorial: Deserializadores personalizados .NET para Azure Stream Analytics

A Azure Stream Analytics tem [suporte incorporado para três formatos de dados](stream-analytics-parsing-json.md): JSON, CSV e Avro. Com deserializadores .NET personalizados, pode ler dados de outros formatos, tais como [Protocol Buffer,](https://developers.google.com/protocol-buffers/) [Bond](https://github.com/Microsoft/bond) e outros formatos definidos pelo utilizador para trabalhos de nuvem e borda.

Este tutorial demonstra como criar um deserializador personalizado .NET para um trabalho em nuvem Azure Stream Analytics usando o Visual Studio. Para aprender a criar deserializadores .NET no Código do Estúdio Visual, consulte [Criar deserializadores .NET para trabalhos Azure Stream Analytics em Código de Estúdio Visual](visual-studio-code-custom-deserializer.md).

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Crie um deserializador personalizado para tampão de protocolo.
> * Crie um trabalho Azure Stream Analytics no Visual Studio.
> * Configure o seu trabalho stream Analytics para usar o deserializador personalizado.
> * Execute o seu trabalho stream Analytics localmente para testar e depurar o deserializador personalizado.


## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Instalar [Visual Studio 2019 (recomendado)](https://www.visualstudio.com/downloads/) ou [Visual Studio 2017](https://www.visualstudio.com/vs/older-downloads/). As edições Enterprise (Ultimate/Premium), Professional e Community são suportadas. A edição expressa não é apoiada. 

* [Instale as ferramentas Stream Analytics para Visual Studio](stream-analytics-tools-for-visual-studio-install.md) ou atualize para a versão mais recente. 

* Abra **o Cloud Explorer** no Estúdio Visual e inscreva-se na sua subscrição do Azure.

* Crie um recipiente na sua Conta de Armazenamento Azure.
O contentor que criar será utilizado para armazenar ativos relacionados com o seu trabalho stream Analytics. Se já tiver uma conta de armazenamento com contentores existentes, poderá utilizá-los. Caso contrário, precisará de [criar um novo contentor](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Criar um deserializador personalizado

1. Abra o Estúdio Visual e selecione **File > New > Project**. Procure por **Stream Analytics** e selecione **Azure Stream Analytics Custom Deserializer Project (.NET)**. Dê ao projeto um nome, como **Protobuf Deserializer.**

   ![Criar projeto de biblioteca de classe padrão do estúdio visual](./media/custom-deserializer/create-dotnet-library-project.png)

2. No Solution Explorer, clique com o botão direito do seu projeto **Protobuf Deserializer** e selecione **Gerir pacotes NuGet** no menu. Em seguida, instale os **pacotes Microsoft.Azure.StreamAnalytics** e **Google.Protobuf** NuGet.

3. Adicione a [classe MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) e a [classe MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) ao seu projeto.

4. Construa o projeto **Protobuf Deserializer.**

## <a name="add-an-azure-stream-analytics-project"></a>Adicione um projeto Azure Stream Analytics

1. No Solution Explorer, clique com o botão direito na solução **Protobuf Deserializer** e **selecione Add > New Project**. Em **Azure Stream Analytics > Stream Analytics,** escolha **a aplicação Azure Stream Analytics**. Nomeie-o **ProtobufCloudDeserializer** e selecione **OK**. 

2. Clique à direita **Referências** no âmbito do projeto **ProtobufCloudDeserializer** Azure Stream Analytics. Em **Projetos**, adicione **Protobuf Deserializer**. Deve ser automaticamente povoado para si.

## <a name="configure-a-stream-analytics-job"></a>Configure um trabalho stream analytics

1. Clique duas **vezesJobConfig.jsem**. Utilize as configurações predefinidos, exceto as seguintes definições:

   |Definição|Valor sugerido|
   |-------|---------------|
   |Recurso global de definições de armazenamento|Escolha a origem de dados na conta atual|
   |Subscrição global de configurações de armazenamento| < o seu > de subscrição|
   |Conta global de armazenamento de configurações de armazenamento| < a sua conta de armazenamento >|
   |Recurso de definições de armazenamento de código personalizado|Escolha a origem de dados na conta atual|
   |Conta de armazenamento de definições de código personalizada|< a sua conta de armazenamento >|
   |Recipiente de definições de armazenamento de código personalizado|< o seu recipiente de armazenamento >|

2. Em Entradas , clique duas **vezes** **Input.jsem**. Utilize as configurações predefinidos, exceto as seguintes definições:

   |Definição|Valor sugerido|
   |-------|---------------|
   |Origem|Armazenamento de Blobs|
   |Recurso|Escolha a origem de dados na conta atual|
   |Subscrição|< o seu > de subscrição|
   |Conta de Armazenamento|< a sua conta de armazenamento >|
   |Contentor|< o seu recipiente de armazenamento >|
   |Formato de Serialização de Eventos|Outros (Protobuf, XML, proprietário...)|
   |Recurso|Carga a partir de Referência do Projeto ASA ou CodeBehind|
   |Nome da montagem do CSharp|ProtobufDeserializer.dll|
   |Nome da classe|MessageBodyProto.messageBodyDeserializer|
   |Tipo de Compressão de Eventos|Nenhum|

3. Adicione a seguinte consulta ao ficheiro **Script.asaql.**

   ```sql
   SELECT * FROM Input
   ```

4. Descarregue o [ficheiro de entrada protobuf de amostra](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Na pasta **Entradas,** **clique** Input.jsà direita e selecione Adicionar **Entrada Local**. Em seguida, clique duas vezes **local_Input.js** e configufique as seguintes definições:

   |Definição|Valor sugerido|
   |-------|---------------|
   |Alias de Entrada|Entrada|
   |Tipo de Fonte|Fluxo de Dados|
   |Formato de Serialização de Eventos|Outros (Protobuf, XML, proprietário...)|
   |Nome da montagem do CSharp|ProtobufDeserializer.dll|
   |Nome da classe|MessageBodyProto.messageBodyDeserializer|
   |Caminho do arquivo de entrada local|< o caminho do ficheiro para o ficheiro de entrada de protobuf de amostra descarregado>|

## <a name="execute-the-stream-analytics-job"></a>Execute o trabalho stream analytics

1. Abra **script.asaql** e selecione **Executar Localmente.**

2. Observe os resultados em **Stream Analytics Resultados locais de execução**.

Implementou com sucesso um deserializador personalizado para o seu trabalho stream Analytics! Neste tutorial, você testou o desercializador personalizado localmente. Para os seus dados reais, configuraria adequadamente a entrada e a saída. Em seguida, submeta o trabalho ao Azure do Visual Studio para executar o seu trabalho na nuvem usando o deserializador personalizado que acabou de implementar.

## <a name="debug-your-deserializer"></a>Depurar o seu deserializador

Pode depurar o seu deserializador .NET localmente da mesma forma que depurar código .NET standard.

1. Clique em direito no nome do projeto **ProtobufCloudDeserializer** e desaconte-o como projeto de arranque.

2. Adicione pontos de rutura na sua função.

3. Prima **F5** para iniciar a depuração. O programa irá parar nos seus pontos de interrupção, conforme esperado.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este tutorial ao utilizar os seguintes passos:

1. No menu do lado esquerdo no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione o nome do recurso que criou.  

2. Na página do grupo de recursos, selecione **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a implementar um deserializador personalizado .NET para a serialização da entrada de tampão protocolar. Para saber mais sobre a criação de deserializadores personalizados, continue ao seguinte artigo:

> [!div class="nextstepaction"]
> [Criar diferentes desserializadores .NET para empregos Azure Stream Analytics](custom-deserializer-examples.md)