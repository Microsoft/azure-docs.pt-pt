---
title: Orientações para desenvolver as funções do Azure | Documentos da Microsoft
description: Aprenda os conceitos de funções do Azure e técnicas que precisa para desenvolver as funções no Azure, em todos os enlaces e linguagens de programação.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funções de guia, do azure programador, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 01fce9813299465767bdaed7f3c2939813cfab87
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "56339498"
---
# <a name="azure-functions-developers-guide"></a>Guia para programadores do funções do Azure
Nas funções do Azure, funções específicas partilham alguns conceitos técnicos de núcleos e componentes, independentemente do idioma ou associação usada. Antes de conhecer em detalhes específicos para um determinado idioma ou enlace de aprendizagem, certifique-se de que leia esta visão geral que se aplica a todos eles.

Este artigo pressupõe que já leu a [descrição geral das funções do Azure](functions-overview.md).

## <a name="function-code"></a>Código de função
R *função* é o principal conceito nas funções do Azure. Uma função contém duas partes importantes - seu código, que podem ser escritos numa variedade de linguagens e alguma configuração, o ficheiro de Function. Para linguagens compiladas, este ficheiro de configuração é gerado automaticamente de anotações no seu código. Em linguagens de script, tem de fornecer o ficheiro de configuração por conta própria.

O arquivo de Function define o acionador da função, associações e outras definições de configuração. Cada função tem apenas um acionador. O tempo de execução utiliza este ficheiro de configuração para determinar os eventos a monitorizar e como passar dados para e devolver dados de uma execução de função. Segue-se um exemplo de arquivo Function.

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

O `bindings` propriedade é onde configura acionadores e enlaces. Cada ligação compartilha algumas configurações comuns e algumas definições que são específicas para um determinado tipo de enlace. Cada ligação requer as seguintes definições:

| Propriedade | Tipos de valores / | Comentários |
| --- | --- | --- |
| `type` |string |Tipo de enlace. Por exemplo, `queueTrigger`. |
| `direction` |'em', 'out' |Indica se a ligação é para receber dados para a função ou enviar dados a partir da função. |
| `name` |string |O nome que é utilizado para dados vinculados na função. No c#, isso é um nome de argumento; para JavaScript, é a chave numa lista de chave/valor. |

## <a name="function-app"></a>Function app
Uma aplicação de funções fornece um contexto de execução no Azure em que executar as suas funções. Uma aplicação de funções é composta por um ou mais funções individuais que são geridas, implementadas e dimensionadas em conjunto. Todas as funções numa aplicação de funções de partilham o mesmo plano de preços, a implementação contínua e a versão de runtime. Considere uma aplicação de funções como uma forma de organizar e gerir coletivamente as suas funções. 

> [!NOTE]
> Todas as funções na aplicação de função tem de ser criadas no mesmo idioma. Na [versões anteriores](functions-versions.md) do runtime das funções do Azure, essa não era necessária.

## <a name="folder-structure"></a>estrutura de pastas
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

O procedimento acima é o padrão (e recomendado) estrutura de pastas para uma aplicação de funções. Se desejar alterar a localização do ficheiro de código de uma função, modificar os `scriptFile` secção do _Function_ ficheiro. Também recomendamos que utilize [implementação do pacote](deployment-zip-push.md) para implementar o projeto para a aplicação de funções no Azure. Também pode utilizar as ferramentas existentes, como [integração e implementação contínuas](functions-continuous-deployment.md) e DevOps do Azure.

> [!NOTE]
> Se implementar um pacote manualmente, certifique-se implementar o seu _Host. JSON_ de ficheiros e pastas de função diretamente para o `wwwroot` pasta. Não inclua o `wwwroot` pasta das implementações. Caso contrário, acaba tendo `wwwroot\wwwroot` pastas.

#### <a name="use-local-tools-and-publishing"></a>Utilize as ferramentas locais e de publicação
Aplicações de funções podem ser criadas e publicadas utilizando uma variedade de ferramentas, incluindo [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md)e o [ferramentas de núcleo das funções do Azure](./functions-develop-local.md). Para obter mais informações, consulte [codificar e testar as funções do Azure localmente](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a> Como editar funções no portal do Azure
O editor de funções incorporado no portal do Azure permite-lhe atualizar seu código e a sua *Function* diretamente de ficheiros inline. Isto é recomendado apenas para pequenas alterações ou provas de conceito - prática recomendada é usar uma ferramenta de desenvolvimento local como VS Code.

## <a name="parallel-execution"></a>Execução paralela
Quando vários eventos acionadora ocorrem mais rapidamente do que um tempo de execução de função de thread único pode processá-los, o tempo de execução poderá invocar a função várias vezes em paralelo.  Se estiver a utilizar uma aplicação de funções a [plano de alojamento de consumo](functions-scale.md#how-the-consumption-plan-works), a aplicação de função pode aumentar horizontalmente a automaticamente.  Cada instância da aplicação de função, se a aplicação for executada no consumo ou um regulares do plano de alojamento [plano de alojamento do App Service](../app-service/overview-hosting-plans.md), pode processar chamadas simultâneas de função em paralelo com vários threads.  O número máximo de chamadas de função em simultâneo em cada instância de aplicação de função varia consoante o tipo de Acionador a ser utilizado, bem como os recursos utilizados por outras funções na aplicação de função.

## <a name="functions-runtime-versioning"></a>Controlo de versões de tempo de execução de funções

Pode configurar a versão do uso de tempo de execução de funções a `FUNCTIONS_EXTENSION_VERSION` definição de aplicação. Por exemplo, o valor "~ 2" indica que a aplicação de funções irá utilizar 2.x como sua versão principal. Aplicações de função são atualizadas para cada nova versão secundária, à medida que são lançadas. Para obter mais informações, incluindo como ver a versão exata da sua aplicação de função, veja [como versões de tempo de execução de funções do Azure de destino](set-runtime-version.md).

## <a name="repositories"></a>Repositórios
O código das funções do Azure é open source e armazenados nos repositórios do GitHub:

* [Funções do Azure](https://github.com/Azure/Azure-Functions)
* [Anfitrião de funções do Azure](https://github.com/Azure/azure-functions-host/)
* [Portal de funções do Azure](https://github.com/azure/azure-functions-ux)
* [Modelos do funções do Azure](https://github.com/azure/azure-functions-templates)
* [SDK de WebJobs do Azure](https://github.com/Azure/azure-webjobs-sdk/)
* [Extensões do SDK de WebJobs do Azure](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Enlaces
Aqui está uma tabela de todos os enlaces suportados.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Está a ter problemas com erros proveniente de associações? Reveja os [códigos de erro de ligação do Azure funções](functions-bindings-error-pages.md) documentação.

## <a name="reporting-issues"></a>Problemas de relatórios
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte os seguintes recursos:

* [Acionadores de funções do Azure e enlaces](functions-triggers-bindings.md)
* [Codificar e testar as funções do Azure localmente](./functions-develop-local.md)
* [Melhores práticas para as funções do Azure](functions-best-practices.md)
* [Referência do Azure funções c# para programadores](functions-reference-csharp.md)
* [Referência do desenvolvedor do NodeJS de funções do Azure](functions-reference-node.md)
