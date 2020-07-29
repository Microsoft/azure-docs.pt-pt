---
title: Desenvolver funções padrão .NET para trabalhos Azure Stream Analytics (Pré-visualização)
description: Saiba como escrever funções definidas pelo utilizador para trabalhos stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: 4f9d117ccc763744411bfe24163ed955532e8e56
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921852"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Desenvolver funções padrão .NET para trabalhos Azure Stream Analytics (Pré-visualização)

O Azure Stream Analytics oferece uma linguagem de consulta semelhante ao SQL para realizar transformações e computações sobre fluxos de dados de eventos. Existem muitas funções incorporadas, mas alguns cenários complexos requerem flexibilidade adicional. Com funções padrão .NET (UDF), pode invocar as suas próprias funções escritas em qualquer idioma padrão .NET (C#, F#, etc.) para estender a linguagem de consulta Stream Analytics. As UDFs permitem-lhe realizar computações matemáticas complexas, importar modelos ML personalizados usando ML.NET e usar lógica de imputação personalizada para dados em falta. A funcionalidade UDF para trabalhos stream Analytics está atualmente em pré-visualização e não deve ser usada em cargas de trabalho de produção.

.NET a função definida pelo utilizador para trabalhos em nuvem está disponível em:
* E.U.A. Centro-Oeste
* Europa do Norte
* E.U.A. Leste
* E.U.A. Oeste
* E.U.A. Leste 2
* Europa Ocidental

Se estiver interessado em utilizar esta funcionalidade em qualquer outra região, pode [solicitar acesso.](https://aka.ms/ccodereqregion)

## <a name="overview"></a>Descrição geral
As ferramentas do Visual Studio para o Azure Stream Analytics facilitam-lhe a escrita de UDFs, teste os seus trabalhos localmente (mesmo offline) e publique o seu trabalho stream Analytics para a Azure. Uma vez publicado no Azure, pode implementar o seu trabalho em dispositivos IoT utilizando o IoT Hub.

Existem três formas de implementar os UDFs:

* CodeBehind arquivos em um projeto ASA
* UDF de um projeto local
* Um pacote existente a partir de uma conta de armazenamento Azure

## <a name="package-path"></a>Caminho do pacote

O formato de qualquer pacote UDF tem o `/UserCustomCode/CLR/*` caminho. As Bibliotecas de Ligação Dinâmica (DLLs) e os recursos são copiados sob a pasta, o `/UserCustomCode/CLR/*` que ajuda a isolar os DLLs do utilizador do sistema e dos DLLs Azure Stream Analytics. Este caminho de embalagem é utilizado para todas as funções, independentemente do método utilizado para as empregar.

## <a name="supported-types-and-mapping"></a>Tipos e mapeamento suportados
Para que os valores do Azure Stream Analytics sejam utilizados em C#, eles precisam de ser marechais de um ambiente para o outro. A marechala acontece para todos os parâmetros de entrada de um UDF. Cada tipo Azure Stream Analytics tem um tipo correspondente em C# mostrado na tabela abaixo:

|**Tipo Azure Stream Analytics** |**Tipo C#** |
|---------|---------|
|bigint | long |
|float | double |
|nvarchar(máx) | string |
|datetime | DateTime |
|Registo | Dicionário\<string, object> |
|Matriz | Objeto[] |

O mesmo acontece quando os dados precisam de ser recedidos de C# para Azure Stream Analytics, o que acontece no valor de saída de um UDF. A tabela abaixo mostra que tipos são suportados:

|**Tipo C#**  |**Tipo Azure Stream Analytics**  |
|---------|---------|
|long  |  bigint   |
|double  |  float   |
|string  |  nvarchar(máx)   |
|DateTime  |  dataTime   |
|estrutura  |  Registo   |
|objeto  |  Registo   |
|Objeto[]  |  Matriz   |
|Dicionário\<string, object>  |  Registo   |

## <a name="codebehind"></a>CodeBehind
Pode escrever funções definidas pelo utilizador no **Script.asql** CodeBehind. As ferramentas do Estúdio Visual compilarão automaticamente o ficheiro CodeBehind num ficheiro de montagem. As assembléias são embaladas como um ficheiro zip e enviadas para a sua conta de armazenamento quando submete o seu trabalho ao Azure. Você pode aprender a escrever um C# UDF usando CodeBehind seguindo o tutorial [de empregos UDF para stream analytics edge.](stream-analytics-edge-csharp-udf.md) 

## <a name="local-project"></a>Projeto local
As funções definidas pelo utilizador podem ser escritas num conjunto que é posteriormente referenciado numa consulta Azure Stream Analytics. Esta é a opção recomendada para funções complexas que requerem a potência total de uma língua standard .NET para além da sua linguagem de expressão, como lógica processual ou recursão. Os UDFs de um projeto local também podem ser usados quando você precisa compartilhar a lógica de função através de várias consultas Azure Stream Analytics. Adicionar UDFs ao seu projeto local dá-lhe a capacidade de depurar e testar as suas funções localmente a partir do Visual Studio.

Para fazer referência a um projeto local:

1. Crie uma nova biblioteca de classes na sua solução.
2. Escreva o código na sua turma. Lembre-se que as classes devem ser definidas como *públicas* e os objetos devem ser definidos como *públicos estáticos.* 
3. Crie o projeto. As ferramentas irão embalar todos os artefactos da pasta do caixote do lixo para um ficheiro zip e carregar o ficheiro zip para a conta de armazenamento. Para referências externas, utilize referência de montagem em vez da embalagem NuGet.
4. Refira-se à nova classe no seu projeto Azure Stream Analytics.
5. Adicione uma nova função no seu projeto Azure Stream Analytics.
6. Configure o caminho de montagem no ficheiro de configuração do trabalho, `JobConfig.json` . Definir o Caminho de Montagem para **referência de projeto local ou codeBehind**.
7. Reconstruir tanto o projeto de função como o projeto Azure Stream Analytics.  

### <a name="example"></a>Exemplo

Neste exemplo, **o UDFTest** é um projeto de biblioteca de classes C# e **o ASAUDFDemo** é o projeto Azure Stream Analytics, que fará referência **à UDFTest**.

![Projeto Azure Stream Analytics IoT Edge em Estúdio Visual](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Construa o seu projeto C# que lhe permitirá adicionar uma referência ao seu C# UDF a partir da consulta Azure Stream Analytics.
    
   ![Construir um projeto Azure Stream Analytics IoT Edge em Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Adicione a referência ao projeto C# no projeto ASA. Clique com o botão direito no nó referências e escolha Adicionar Referência.

   ![Adicione uma referência a um projeto C# no Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Escolha o nome do projeto C# da lista. 
    
   ![Escolha o nome do seu projeto C# na lista de referência](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Deverá ver o **UDFTest** listado em **Referências** no **Solution Explorer**.

   ![Ver a referência de função definida pelo utilizador no explorador de soluções](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Clique no botão direito na pasta **Funções** e escolha **Novo Item**.

   ![Adicione novo item às funções na solução Azure Stream Analytics Edge](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Adicione uma função C# **SquareFunction.jsao** seu projeto Azure Stream Analytics.

   ![Selecione a função CSharp a partir de itens stream Analytics Edge em Estúdio Visual](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Clique duas vezes na função no **Solution Explorer** para abrir o diálogo de configuração.

   ![C configuração de função afiada no Estúdio Visual](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. Na configuração da função C#, escolha **a carga do AsA Project Reference** e os nomes de montagem, classe e métodos relacionados da lista de dropdown. Para se referir aos métodos, tipos e funções na consulta Stream Analytics, as classes devem ser definidas como *públicas* e os objetos devem ser definidos como *público estático*.

   ![Configuração de função afiada stream Analytics C](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Pacotes existentes

Pode autorizar UDFs standard .NET em qualquer IDE à sua escolha e invocá-los a partir da sua consulta Azure Stream Analytics. Primeiro, compile o seu código e embale todos os DLLs. O formato do pacote tem o `/UserCustomCode/CLR/*` caminho. Em seguida, faça o upload `UserCustomCode.zip` para a raiz do recipiente na sua conta de armazenamento Azure.

Uma vez que os pacotes zip de montagem tenham sido enviados para a sua conta de armazenamento Azure, pode utilizar as funções nas consultas Azure Stream Analytics. Tudo o que precisa de fazer é incluir as informações de armazenamento na configuração de trabalho do Stream Analytics. Não é possível testar a função localmente com esta opção porque as ferramentas do Visual Studio não descarregam o seu pacote. O caminho do pacote é analisado diretamente para o serviço. 

Para configurar o caminho de montagem no ficheiro de configuração de trabalho, `JobConfig.json` :

Expanda a secção **Configuração do Código Definido Pelo Utilizador** e preencha a configuração com os seguintes valores sugeridos:

   |**Definição**|**Valor Sugerido**|
   |-------|---------------|
   |Recurso global de definições de armazenamento|Escolha a origem de dados na conta atual|
   |Subscrição global de configurações de armazenamento| < o seu > de subscrição|
   |Conta global de armazenamento de configurações de armazenamento| < a sua conta de armazenamento >|
   |Recurso de definições de armazenamento de código personalizado|Escolha a origem de dados na conta atual|
   |Conta de armazenamento de definições de código personalizada|< a sua conta de armazenamento >|
   |Recipiente de definições de armazenamento de código personalizado|< o seu recipiente de armazenamento >|
   |Fonte de montagem de código personalizado|Pacotes de montagem existentes da nuvem|
   |Fonte de montagem de código personalizado|UserCustomCode.zip|

## <a name="user-logging"></a>Registo de utilizadores
O mecanismo de registo permite-lhe capturar informações personalizadas enquanto um trabalho está em execução. Pode utilizar dados de registo para depurar ou avaliar a correção do código personalizado em tempo real.

A `StreamingContext` classe permite-lhe publicar informações de diagnóstico utilizando a `StreamingDiagnostics.WriteError` função. O código abaixo mostra a interface exposta pelo Azure Stream Analytics.

```csharp
public abstract class StreamingContext
{
    public abstract StreamingDiagnostics Diagnostics { get; }
}

public abstract class StreamingDiagnostics
{
    public abstract void WriteError(string briefMessage, string detailedMessage);
}
```

`StreamingContext`é passado como um parâmetro de entrada para o método UDF e pode ser usado dentro do UDF para publicar informações de registo personalizado. No exemplo abaixo, `MyUdfMethod` define uma entrada de **dados,** que é fornecida pela consulta, e uma entrada de **contexto** como o , fornecido pelo motor de tempo `StreamingContext` de execução. 

```csharp
public static long MyUdfMethod(long data, StreamingContext context)
{
    // write log
    context.Diagnostics.WriteError("User Log", "This is a log message");
    
    return data;
}
```

O `StreamingContext` valor não precisa de ser passado pela consulta SQL. O Azure Stream Analytics fornece um objeto de contexto automaticamente se estiver presente um parâmetro de entrada. A utilização do `MyUdfMethod` não se altera, como mostra a seguinte consulta:

```sql
SELECT udf.MyUdfMethod(input.value) as udfValue FROM input
```

Pode aceder às mensagens de registo através dos [registos de diagnóstico](data-errors.md).

## <a name="limitations"></a>Limitações
A pré-visualização da UDF tem atualmente as seguintes limitações:

* .NET Standard UDFs só pode ser da autoria em Visual Studio e publicado no Azure. As versões apenas de leitura de UDFs standard .NET podem ser vistas em **Funções** no portal Azure. A autoria de funções standard .NET não é suportada no portal Azure.

* O editor de consulta do portal Azure mostra um erro ao utilizar o UDF standard .NET no portal. 

* Como o código personalizado partilha o contexto com o motor Azure Stream Analytics, o código personalizado não pode referenciar nada que tenha um espaço/dll_name de nomes conflituoso com o código Azure Stream Analytics. Por exemplo, não se pode fazer referência *a Newtonsoft Json.*

* Os ficheiros de suporte incluídos no projeto são copiados para o ficheiro postal do Código Personalizado do Utilizador que é utilizado quando publica o trabalho na nuvem. Todos os ficheiros nas sub-dobradizações são copiados diretamente para a raiz da pasta Código Personalizado do Utilizador na nuvem quando desapertados. O fecho é "achatado" quando descomprimido.

* O Código Personalizado do Utilizador não suporta pastas vazias. Não adicione pastas vazias aos ficheiros de suporte do projeto.

## <a name="next-steps"></a>Próximos passos

* [Tutorial: Escreva uma função definida pelo utilizador C# para um trabalho Azure Stream Analytics (Pré-visualização)](stream-analytics-edge-csharp-udf.md)
* [Tutorial: funções definidas pelo utilizador do JavaScript do Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Use o Estúdio Visual para ver os trabalhos do Azure Stream Analytics](stream-analytics-vs-tools.md)
