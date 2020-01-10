---
title: Diretrizes para o desenvolvimento de Azure Functions
description: Conheça os conceitos e as técnicas de Azure Functions de que você precisa para desenvolver funções no Azure, em todas as linguagens de programação e associações.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 7dd7ef3c4833fb9ffa3781f06faba4f40cd40cfb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769018"
---
# <a name="azure-functions-developers-guide"></a>Guia de desenvolvedores do Azure Functions
Em Azure Functions, as funções específicas compartilham alguns conceitos técnicos e componentes essenciais, independentemente da linguagem ou da associação usada. Antes de ir para os detalhes de aprendizagem específicos de um determinado idioma ou associação, leia esta visão geral que se aplica a todos eles.

Este artigo pressupõe que você já tenha lido o [Azure Functions visão geral](functions-overview.md).

## <a name="function-code"></a>Código de função
Uma *função* é o conceito principal no Azure functions. Uma função contém duas partes importantes: seu código, que pode ser escrito em uma variedade de linguagens, e algumas configurações, o arquivo function. JSON. Para idiomas compilados, esse arquivo de configuração é gerado automaticamente de anotações em seu código. Para linguagens de script, você mesmo deve fornecer o arquivo de configuração.

O arquivo function. JSON define o gatilho, as associações e outras definições de configuração da função. Cada função tem um, e apenas um, acionador. O tempo de execução usa esse arquivo de configuração para determinar os eventos a serem monitorados e como passar dados para e retornar dados de uma execução de função. Este é um arquivo function. JSON de exemplo.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Para obter mais informações, consulte [Azure Functions os conceitos de gatilhos e associações](functions-triggers-bindings.md).

A propriedade `bindings` é onde você configura gatilhos e associações. Cada associação compartilha algumas configurações comuns e algumas configurações que são específicas a um tipo específico de associação. Cada associação requer as seguintes configurações:

| Propriedade | Valores/tipos | Comentários |
| --- | --- | --- |
| `type` |string |Tipo de associação. Por exemplo, `queueTrigger`. |
| `direction` |' in ', ' out ' |Indica se a associação é para receber dados na função ou enviar dados da função. |
| `name` |string |O nome que é usado para os dados vinculados na função. Para C#, este é um nome de argumento; para JavaScript, é a chave em uma lista de chave/valor. |

## <a name="function-app"></a>Function app
Um aplicativo de funções fornece um contexto de execução no Azure no qual suas funções são executadas. Como tal, é a unidade de implantação e gerenciamento para suas funções. Um aplicativo de funções é composto de uma ou mais funções individuais que são gerenciadas, implantadas e dimensionadas juntas. Todas as funções em um aplicativo de funções compartilham o mesmo plano de preços, método de implantação e versão de tempo de execução. Imagine um aplicativo de funções como uma maneira de organizar e gerenciar coletivamente suas funções. Para saber mais, consulte [como gerenciar um aplicativo de funções](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Todas as funções em um aplicativo de funções devem ser criadas no mesmo idioma. Em [versões anteriores](functions-versions.md) do tempo de execução de Azure functions, isso não era necessário.

## <a name="folder-structure"></a>Estrutura de pastas
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

O acima é a estrutura de pastas padrão (e recomendada) para um aplicativo de funções. Se você quiser alterar o local do arquivo do código de uma função, modifique a seção `scriptFile` do arquivo _Function. JSON_ . Também é recomendável usar a [implantação de pacote](deployment-zip-push.md) para implantar seu projeto em seu aplicativo de funções no Azure. Você também pode usar ferramentas existentes como [integração e implantação contínua](functions-continuous-deployment.md) e DevOps do Azure.

> [!NOTE]
> Se estiver implantando um pacote manualmente, certifique-se de implantar o arquivo _host. JSON_ e as pastas de função diretamente na pasta `wwwroot`. Não inclua a pasta `wwwroot` em suas implantações. Caso contrário, você acabará com `wwwroot\wwwroot` pastas.

#### <a name="use-local-tools-and-publishing"></a>Usar ferramentas locais e publicação
Os aplicativos de funções podem ser criados e publicados usando uma variedade de ferramentas, incluindo o [Visual Studio](./functions-develop-vs.md), o [Visual Studio Code](functions-create-first-function-vs-code.md), o [IntelliJ](./functions-create-maven-intellij.md), o [Eclipse](./functions-create-maven-eclipse.md)e o [Azure Functions Core Tools](./functions-develop-local.md). Para obter mais informações, consulte [código e teste Azure Functions localmente](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a>Como editar funções no portal do Azure
O editor de funções interno do portal do Azure permite que você atualize seu código e o arquivo *Function. JSON* diretamente embutido. Isso é recomendado apenas para pequenas alterações ou provas de conceito – a prática recomendada é usar uma ferramenta de desenvolvimento local como VS Code.

## <a name="parallel-execution"></a>Execução paralela
Quando vários eventos de disparo ocorrem mais rápido do que um tempo de execução de função de thread único pode processá-los, o tempo de execução pode invocar a função várias vezes em paralelo.  Se um aplicativo de funções estiver usando o [plano de Hospedagem de consumo](functions-scale.md#how-the-consumption-and-premium-plans-work), o aplicativo de funções poderá ser dimensionado automaticamente.  Cada instância do aplicativo de funções, se o aplicativo for executado no plano de Hospedagem de consumo ou em um [plano de hospedagem do serviço de aplicativo](../app-service/overview-hosting-plans.md)regular, poderá processar invocações de função simultâneas em paralelo usando vários threads.  O número máximo de invocações de função simultâneas em cada instância de aplicativo de função varia de acordo com o tipo de gatilho que está sendo usado, bem como os recursos usados por outras funções no aplicativo de funções.

## <a name="functions-runtime-versioning"></a>Controle de versão do Functions Runtime

Você pode configurar a versão do tempo de execução do Functions usando a configuração do aplicativo `FUNCTIONS_EXTENSION_VERSION`. Por exemplo, o valor "~ 3" indica que seu Aplicativo de funções usará 3. x como sua versão principal. Os aplicativos de funções são atualizados para cada nova versão secundária à medida que são liberados. Para obter mais informações, incluindo como exibir a versão exata do seu aplicativo de funções, consulte [como direcionar Azure Functions versões de tempo de execução](set-runtime-version.md).

## <a name="repositories"></a>Repositórios
O código para Azure Functions é de software livre e armazenado em repositórios GitHub:

* [Funções do Azure](https://github.com/Azure/Azure-Functions)
* [Host Azure Functions](https://github.com/Azure/azure-functions-host/)
* [Portal de Azure Functions](https://github.com/azure/azure-functions-ux)
* [Modelos de Azure Functions](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/) (SDK de WebJobs do Azure)
* [Azure WebJobs SDK Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/) (Extensões do SDK de WebJobs do Azure)

## <a name="bindings"></a>Enlaces
Aqui está uma tabela de todas as associações com suporte.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Está tendo problemas com erros provenientes das associações? Examine a documentação [Azure Functions códigos de erro de associação](functions-bindings-error-pages.md) .

## <a name="reporting-issues"></a>Problemas de relatório
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte os seguintes recursos:

* [Azure Functions gatilhos e associações](functions-triggers-bindings.md)
* [Criar código e testar as Funções do Azure localmente](./functions-develop-local.md)
* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência C# do Azure Functions Developer](functions-dotnet-class-library.md)
* [Azure Functions referência do desenvolvedor node. js](functions-reference-node.md)
