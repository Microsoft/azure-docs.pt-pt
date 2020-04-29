---
title: Tutorial - Desserializers personalizados .NET para trabalhos em nuvem Azure Stream Analytics
description: Este tutorial demonstra como criar um desserializer personalizado .NET para um trabalho de nuvem Azure Stream Analytics usando o Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 1fffeec1434cb066487bf383589554edec2e6a86
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75443698"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Tutorial: Desserializers personalizados .NET para Azure Stream Analytics

O Azure Stream Analytics tem [suporte integrado para três formatos](stream-analytics-parsing-json.md)de dados : JSON, CSV e Avro. Com desserializers personalizados .NET, pode ler dados de outros formatos, tais como [Buffer protocol,](https://developers.google.com/protocol-buffers/) [Bond](https://github.com/Microsoft/bond) e outros formatos definidos pelo utilizador para trabalhos de nuvem e borda.

Este tutorial demonstra como criar um desserializer personalizado .NET para um trabalho de nuvem Azure Stream Analytics usando o Visual Studio. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um desserializador personalizado para tampão de protocolo.
> * Crie um trabalho azure Stream Analytics no Estúdio Visual.
> * Configure o seu trabalho de Stream Analytics para utilizar o desserializer personalizado.
> * Execute o seu trabalho de Stream Analytics localmente para testar o desserializer personalizado.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Instale o [Visual Studio 2017](https://www.visualstudio.com/downloads/) ou [o Visual Studio 2015.](https://www.visualstudio.com/vs/older-downloads/) As edições Enterprise (Ultimate/Premium), Professional e Community são suportadas. A edição expressa não é apoiada.

* [Instale as ferramentas Stream Analytics para Estúdio Visual](stream-analytics-tools-for-visual-studio-install.md) ou atualize para a versão mais recente. As seguintes versões do Estúdio Visual são suportadas:
   * Visual Studio 2015
   * Visual Studio 2017

* Abra o **Cloud Explorer** no Estúdio Visual e inscreva-se na subscrição do Azure.

* Crie um recipiente na sua Conta de Armazenamento Azure.
O recipiente que criar será utilizado para armazenar ativos relacionados com o seu trabalho em Stream Analytics. Se já tiver uma conta de armazenamento com contentores existentes, poderá utilizá-los. Caso contrário, precisará de [criar um novo contentor](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Criar um desserializer personalizado

1. Open Visual Studio e selecione **File > New > Project**. Procure o **Stream Analytics** e selecione **Azure Stream Analytics Custom Deserializer Project (.NET)**. Dê ao projeto um nome, como **Protobuf Deserializer.**

   ![Criar projeto de biblioteca de classe padrão do estúdio visual dotnet](./media/custom-deserializer/create-dotnet-library-project.png)

2. No Solution Explorer, clique no seu projeto **Protobuf Deserializer** e selecione **Gerir pacotes NuGet** a partir do menu. Em seguida, instale os pacotes **Microsoft.Azure.StreamAnalytics** e **Google.Protobuf** NuGet.

3. Adicione a [classe MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) e a [classe MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) ao seu projeto.

4. Construa o projeto **Protobuf Deserializer.**

## <a name="add-an-azure-stream-analytics-project"></a>Adicione um projeto Azure Stream Analytics

1. No Solution Explorer, clique à direita na solução **Protobuf Deserializer** e **selecione Adicionar > Novo Projeto**. Em **Azure Stream Analytics > Stream Analytics,** escolha a **aplicação Azure Stream Analytics**. Nomeie-o **ProtobufCloudDeserializer** e selecione **OK**. 

2. Clique à direita **Referências** no âmbito do projeto **ProtobufCloudDeserializer** Azure Stream Analytics. Em **Projetos,** adicione **Protobuf Deserializer.** Deve ser automaticamente povoado para si.

## <a name="configure-a-stream-analytics-job"></a>Configure um trabalho de Stream Analytics

1. Clique duas vezes **jobConfig.json**. Utilize as configurações predefinidas, exceto as seguintes definições:

   |Definição|Valor sugerido|
   |-------|---------------|
   |Recurso de definições de armazenamento global|Escolha a origem de dados na conta atual|
   |Subscrição de configurações globais de armazenamento| < o seu > de subscrição|
   |Conta de armazenamento de configurações globais| < a sua conta de armazenamento >|
   |Recurso de definições de armazenamento de código personalizado|Escolha a origem de dados na conta atual|
   |Conta de armazenamento de código personalizado|< a sua conta de armazenamento >|
   |Recipiente de definições de armazenamento de código personalizado|< o seu > de recipiente de armazenamento|

2. Em **Inputs,** clique duas vezes **Em Entrada.json**. Utilize as configurações predefinidas, exceto as seguintes definições:

   |Definição|Valor sugerido|
   |-------|---------------|
   |Origem|Blob Storage|
   |Recurso|Escolha a origem de dados na conta atual|
   |Subscrição|< o seu > de subscrição|
   |Conta de Armazenamento|< a sua conta de armazenamento >|
   |Contentor|< o seu > de recipiente de armazenamento|
   |Formato de Serialização de Eventos|Outros (Protobuf, XML, proprietário...)|
   |Recurso|Carga a partir de Referência do Projeto ASA ou Código Por trás|
   |Nome da Assembléia CSharp|ProtobufDeserializer.dll|
   |Nome da classe|MessageBodyProto.MessageBodyDeserializer|
   |Tipo de compressão de eventos|Nenhuma|

3. Adicione a seguinte consulta ao ficheiro **Script.asaql.**

   ```sql
   SELECT * FROM Input
   ```

4. Descarregue o ficheiro de [entrada protobuf da amostra](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Na pasta **Inputs,** clique no botão direito **Input.json** e selecione **Adicionar Entrada Local**. Em seguida, clique duas vezes **local_Input.json** e configure as seguintes definições:

   |Definição|Valor sugerido|
   |-------|---------------|
   |Alias de Entrada|Input|
   |Tipo de Fonte|Fluxo de Dados|
   |Formato de Serialização de Eventos|Outros (Protobuf, XML, proprietário...)|
   |Nome da Assembléia CSharp|ProtobufDeserializer.dll|
   |Nome da classe|MessageBodyProto.MessageBodyDeserializer|
   |Caminho de arquivo de entrada local|< o caminho do ficheiro de ficheiros para o ficheiro de entrada protobuf da amostra descarregado>|

## <a name="execute-the-stream-analytics-job"></a>Executar o trabalho de Stream Analytics

1. Abra o **Script.asaql** e selecione **Executar Localmente**.

2. Observe os resultados nos resultados de **execução local do Stream Analytics**.

Implementou com sucesso um desserializer personalizado para o seu trabalho em Stream Analytics! Neste tutorial, testou o desserializador personalizado localmente. Para os seus dados reais, configuraria adequadamente a entrada e a saída. Em seguida, submeta o trabalho ao Azure do Visual Studio para executar o seu trabalho na nuvem usando o desserializer personalizado que acabou de implementar.

## <a name="debug-your-deserializer"></a>Desinem o seu desserializer

Pode desmarcar o seu desserializador .NET localmente da mesma forma que depura o código padrão .NET. 

1. Adicione pontos de rutura na sua função.

2. Prima **F5** para iniciar a depuração. O programa irá parar nos seus pontos de interrupção, conforme esperado.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este tutorial ao utilizar os seguintes passos:

1. No menu do lado esquerdo no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione o nome do recurso que criou.  

2. Na página do grupo de recursos, selecione **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a implementar um desserializador personalizado .NET para a serialização da entrada do tampão do protocolo. Para saber mais sobre a criação de desserializers personalizados, continue para o seguinte artigo:

> [!div class="nextstepaction"]
> [Criar diferentes desserializers .NET para trabalhos de Azure Stream Analytics](custom-deserializer-examples.md)
