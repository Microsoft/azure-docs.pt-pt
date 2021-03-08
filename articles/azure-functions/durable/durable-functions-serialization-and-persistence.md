---
title: Persistência de dados e serialização em Funções Duráveis - Azure
description: Saiba como a extensão de Funções Duradouras para Funções Azure persiste em dados
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: ea4aaa1cdbe10e2db9cf619452558d104a2293ab
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449378"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Persistência de dados e serialização em Funções Duradouras (Funções Azure)

Funções duradouras persistem automaticamente parâmetros de função, valores de retorno e outro estado a um backend durável, a fim de proporcionar uma execução fiável. No entanto, a quantidade e a frequência dos dados persistiram no armazenamento duradouro pode ter impacto nos custos de desempenho e transação de armazenamento da aplicação. Dependendo do tipo de dados que as suas lojas de aplicações, a retenção de dados e as políticas de privacidade também podem ser consideradas.

## <a name="azure-storage"></a>Storage do Azure

Por predefinição, as Funções Duradouras persistem em filas, tabelas e bolhas numa conta [de Armazenamento Azure](https://azure.microsoft.com/services/storage/) que especifique.

### <a name="queues"></a>Filas

Funções Duradouras usam filas de armazenamento Azure para agendar de forma fiável todas as execuções de funções. Estas mensagens de fila contêm entradas ou saídas de função, dependendo se a mensagem está a ser usada para agendar uma execução ou devolver um valor a uma função de chamada. Estas mensagens de fila também incluem metadados adicionais que as Funções Duráveis usam para fins internos, como encaminhamento e correlação de ponta a ponta. Depois de uma função ter terminado a execução em resposta a uma mensagem recebida, essa mensagem é eliminada e o resultado da execução também pode ser persistido em tabelas de armazenamento Azure ou Blobs de Armazenamento Azure.

Dentro de um único centro de [tarefas,](durable-functions-task-hubs.md)as Funções Duráveis criam e adicionam mensagens a uma fila *de artigos de trabalho* nomeada para `<taskhub>-workitem` agendar funções de atividade e uma ou mais filas de *controlo nomeadas* `<taskhub>-control-##` para agendar ou retomar funções de orquestrador e entidade. O número de filas de controlo é igual ao número de divisórias configuradas para a sua aplicação. Para obter mais informações sobre filas e divisórias, consulte a [documentação performance e escalabilidade.](durable-functions-perf-and-scale.md)

### <a name="tables"></a>Tabelas

Uma vez que as orquestrações processam mensagens com sucesso, os registos das suas ações resultantes são percritos na tabela *História* denominada `<taskhub>History` . As entradas de orquestração, as saídas e os dados de estado personalizado também são persistidos na tabela *Instâncias* denominada `<taskhub>Instances` .

### <a name="blobs"></a>Blobs

Na maioria dos casos, as Funções Duradouras não usam blobs de armazenamento Azure para persistir em dados. No entanto, as filas e as tabelas têm [limites](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits) de tamanho que podem impedir que as Funções Duráveis persistam todos os dados necessários numa linha de armazenamento ou mensagem de fila. Por exemplo, quando um pedaço de dados que precisa de ser persistido a uma fila é superior a 45 KB quando serializado, as Funções Duráveis comprimem os dados e armazenam-nos numa bolha. Ao persistir dados para o armazenamento de bolhas desta forma, a Função Durable armazena uma referência a essa bolha na fila da mesa ou na mensagem de fila. Quando as Funções Duradouras precisam de recuperar os dados, irá buscá-los automaticamente a partir da bolha. Estas bolhas estão guardadas no recipiente da bolha `<taskhub>-largemessages` .

> [!NOTE]
> Os passos de operação de compressão e de operação de bolhas extra para mensagens grandes podem ser dispendiosos em termos de custos de cpu e de latência de I/O. Além disso, as Funções Duradouras precisam de carregar dados persistidos na memória, e podem fazê-lo para muitas execuções de funções diferentes ao mesmo tempo. Como resultado, a persistência de grandes cargas de dados pode causar alta utilização da memória também. Para minimizar a sobrecarga da memória, considere a persistência de grandes cargas de dados manualmente (por exemplo, no armazenamento de bolhas) e, em vez disso, transmita referências a estes dados. Desta forma, o seu código só pode carregar os dados quando necessário para evitar cargas redundantes durante [as repetições da função do orquestrador](durable-functions-orchestrations.md#reliability). No entanto, *não* é recomendado o armazenamento de cargas útil no disco, uma vez que o estado do disco não está garantido para estar disponível, uma vez que as funções podem ser executadas em diferentes VM ao longo da sua vida útil.

### <a name="types-of-data-that-is-serialized-and-persisted"></a>Tipos de dados que são serializados e persistidos
Segue-se uma lista dos diferentes tipos de dados que serão serializados e persistidos ao utilizarem funcionalidades de Funções Duradouras:

- Todas as entradas e saídas de funções de orquestrador, atividade e entidade, incluindo quaisquer IDs e exceções não manipuladas
- Nomes de funções de orquestrador, atividade e entidade
- Nomes e cargas de eventos externos
- Cargas personalizadas de estado de orquestração
- Mensagens de rescisão de orquestração
- Cargas de temporizador duráveis
- URLs, cabeçalhos e cargas de resposta de HTTP duráveis
- Cargas de chamada e sinal de entidade
- Cargas do estado da entidade

### <a name="working-with-sensitive-data"></a>Trabalhar com dados sensíveis
Ao utilizar o Azure Storage, todos os dados são automaticamente encriptados em repouso. No entanto, qualquer pessoa que tenha acesso à conta de armazenamento pode ler os dados na sua forma não encriptada. Se necessitar de uma proteção mais forte para dados sensíveis, considere primeiro encriptar os dados utilizando as suas próprias chaves de encriptação para que as Funções Duráveis persistam nos dados de forma pré-encriptada.

Em alternativa, os utilizadores .NET têm a opção de implementar fornecedores de serialização personalizados que fornecem encriptação automática. Um exemplo de serialização personalizada com encriptação pode ser encontrado [nesta amostra do GitHub.](https://github.com/charleszipp/azure-durable-entities-encryption)

> [!NOTE]
> Se decidir implementar encriptação ao nível da aplicação, esteja ciente de que as orquestrações e entidades podem existir por tempo indeterminado. Isto importa quando chega a hora de rodar as suas chaves de encriptação porque uma orquestração ou entidades podem correr mais tempo do que a sua política de rotação chave. Se uma rotação de chave acontecer, a chave usada para encriptar os seus dados pode deixar de estar disponível para desencriptar na próxima vez que a sua orquestração ou entidade executar. A encriptação do cliente só é recomendada quando se espera que as orquestrações e entidades sejam executadas por períodos de tempo relativamente curtos.

## <a name="customizing-serialization-and-deserialization"></a>Personalização da serialização e da deserialização

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>Lógica de serialização padrão

Funções Duradouras usam internamente [Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar a orquestração e os dados da entidade para json. As definições predefiniíveis que as funções duradouras utilizam para Json.NET são:

**Entradas, saídas e Estado:**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**Exceções:**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

Leia mais documentação detalhada `JsonSerializerSettings` [sobre aqui.](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm)

## <a name="customizing-serialization-with-net-attributes"></a>Personalizar serialização com atributos .NET

Ao serializar dados, Json.NET procura [vários atributos](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm) sobre classes e propriedades que controlam como os dados são serializados e dessesalizados a partir de JSON. Se possuir o código fonte para o tipo de dados passado para APIs funções duráveis, considere adicionar estes atributos ao tipo de personalização e deserialização.

## <a name="customizing-serialization-with-dependency-injection"></a>Personalização da serialização com Injeção de Dependência

As aplicações de função que visam .NET e funcionam no tempo de execução funções V3 podem usar [a Injeção de Dependência (DI)](../functions-dotnet-dependency-injection.md) para personalizar como os dados e exceções são serializados. O código de amostra abaixo demonstra como usar o DI para anular as definições de serialização de Json.NET padrão utilizando implementações personalizadas das `IMessageSerializerSettingsFactory` interfaces e `IErrorSerializerSettingsFactory` de serviço.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Extensions.DependencyInjection;
using Newtonsoft.Json;
using System.Collections.Generic;

[assembly: FunctionsStartup(typeof(MyApplication.Startup))]
namespace MyApplication
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddSingleton<IMessageSerializerSettingsFactory, CustomMessageSerializerSettingsFactory>();
            builder.Services.AddSingleton<IErrorSerializerSettingsFactory, CustomErrorSerializerSettingsFactory>();
        }

        /// <summary>
        /// A factory that provides the serialization for all inputs and outputs for activities and
        /// orchestrations, as well as entity state.
        /// </summary>
        internal class CustomMessageSerializerSettingsFactory : IMessageSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }

        /// <summary>
        /// A factory that provides the serialization for all exceptions thrown by activities
        /// and orchestrations
        /// </summary>
        internal class CustomErrorSerializerSettingsFactory : IErrorSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="serialization-and-deserialization-logic"></a>Lógica de serialização e deserialização

Aplicações de nó de funções Azure Funcionam [ `JSON.stringify()` para serialização](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) e [ `JSON.Parse()` para a deserialização](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse). A maioria dos tipos deve serializar e deserizar perfeitamente. Nos casos em que a lógica padrão é insuficiente, definir um `toJSON()` método no objeto irá sequestrar a lógica de serialização. No entanto, não existe analogia para a deserialização de objetos.

Para personalizar completamente o pipeline de serialização/desserialização, considere lidar com a serialização e deserialização com o seu próprio código e passar dados como cordas.


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>Lógica de serialização e deserialização

Recomenda-se vivamente a utilização de anotações de tipo para garantir que as Funções Duráveis serializam e deserizam corretamente os seus dados. Enquanto muitos tipos incorporados são manuseados automaticamente, alguns tipos de dados incorporados requerem anotações de tipo para preservar o tipo durante a deserialização.

Para tipos de dados personalizados, deve definir a serialização e desseialização JSON de um tipo de dados exportando um `to_json` estático e `from_json` método da sua classe.

---
