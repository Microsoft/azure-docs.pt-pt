---
title: Orientação para o desenvolvimento de Funções Azure
description: Aprenda os conceitos e técnicas de Funções Azure que precisa para desenvolver funções em Azure, em todas as linguagens e encadernações de programação.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: b6af3d7ab1fdd35391c9a189162c57dfb259f2d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405344"
---
# <a name="azure-functions-developer-guide"></a>Guia do programador das Funções do Azure
Nas Funções Azure, as funções específicas partilham alguns conceitos e componentes técnicos fundamentais, independentemente da linguagem ou da ligação que utilize. Antes de entrar em detalhes de aprendizagem específicos de uma determinada língua ou de ligação, não se esqueça de ler através desta visão geral que se aplica a todos eles.

Este artigo assume que já leu a visão geral das [Funções Azure.](functions-overview.md)

## <a name="function-code"></a>Código de função
Uma *função* é o conceito primário em Funções Azure. Uma função contém duas peças importantes - o seu código, que pode ser escrito em uma variedade de idiomas, e algum config, o arquivo fun.json. Para idiomas compilados, este ficheiro de config é gerado automaticamente a partir de anotações no seu código. Para scripts idiomas, você deve fornecer o arquivo de config você mesmo.

O ficheiro função.json define o gatilho, as ligações e outras definições de configuração da função. Cada função tem um, e apenas um, acionador. O tempo de execução utiliza este ficheiro de config para determinar os eventos para monitorizar e como transmitir dados e devolver dados de uma execução de função. Segue-se um ficheiro de função exemplo.json.

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

Para mais informações, consulte as [Funções Azure despoletou e encaderna conceitos.](functions-triggers-bindings.md)

A `bindings` propriedade é onde você configura tanto gatilhos como encadernações. Cada encadernação partilha algumas configurações comuns e algumas configurações específicas para um tipo particular de encadernação. Cada encadernação requer as seguintes definições:

| Propriedade | Valores/Tipos | Comentários |
| --- | --- | --- |
| `type` |string |Tipo de encadernação. Por exemplo, `queueTrigger`. |
| `direction` |'in', 'out' |Indica se a ligação é para receber dados na função ou para enviar dados da função. |
| `name` |string |O nome utilizado para os dados vinculados na função. Para C#, este é um nome de argumento; para javaScript, é a chave numa lista de chaves/valor. |

## <a name="function-app"></a>Function app
Uma aplicação de função fornece um contexto de execução em Azure no qual as suas funções funcionam. Como tal, é a unidade de implantação e gestão para as suas funções. Uma aplicação de função é composta por uma ou mais funções individuais que são geridas, implantadas e dimensionadas em conjunto. Todas as funções de uma aplicação de função partilham o mesmo plano de preços, método de implementação e versão de tempo de execução. Pense numa aplicação de função como uma forma de organizar e gerir coletivamente as suas funções. Para saber mais, consulte [como gerir uma aplicação de função](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Todas as funções numa aplicação de função devem ser da autoria da mesma língua. Em [versões anteriores](functions-versions.md) do tempo de funcionamento das Funções Azure, isso não era necessário.

## <a name="folder-structure"></a>Estrutura de pasta
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

A acima é a estrutura de pasta predefinida (e recomendada) para uma aplicação Função. Se desejar alterar a localização do ficheiro do código `scriptFile` de uma função, modifique a secção do ficheiro _função.json._ Recomendamos também a utilização da implementação de [pacotes](deployment-zip-push.md) para implementar o seu projeto na sua aplicação de funções no Azure. Também pode utilizar ferramentas existentes como [integração contínua e implantação](functions-continuous-deployment.md) e Azure DevOps.

> [!NOTE]
> Se implementar manualmente um pacote, certifique-se de que implementa o ficheiro `wwwroot` _host.json_ e funfun as pastas diretamente para a pasta. Não inclua `wwwroot` a pasta nas suas implementações. Caso contrário, acabas `wwwroot\wwwroot` com pastas.

#### <a name="use-local-tools-and-publishing"></a>Utilize ferramentas locais e publicações
As aplicações de função podem ser da autoria e publicadas usando uma variedade de ferramentas, incluindo [Visual Studio](./functions-develop-vs.md), Visual [Studio Code](functions-create-first-function-vs-code.md), [IntelliJ,](./functions-create-maven-intellij.md) [Eclipse,](./functions-create-maven-eclipse.md)e as [Ferramentas Core funções do Azure.](./functions-develop-local.md) Para mais informações, consulte [Código e teste funções Azure localmente](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a>Como editar funções no portal Azure
O editor de Funções incorporado no portal Azure permite-lhe atualizar o seu código e o seu ficheiro *function.json* diretamente inline. Isto é recomendado apenas para pequenas alterações ou provas de conceito - a melhor prática é usar uma ferramenta de desenvolvimento local como o Código VS.

## <a name="parallel-execution"></a>Execução paralela
Quando vários eventos de desencadeamento ocorrem mais rapidamente do que um tempo de funcionamento da função de um fio único pode processá-los, o tempo de execução pode invocar a função várias vezes em paralelo.  Se uma aplicação de função estiver a utilizar o plano de hospedagem do [Consumo,](functions-scale.md#how-the-consumption-and-premium-plans-work)a aplicação de função pode ser automaticamente ampliada.  Cada instância da aplicação de funções, quer a app seja executado no plano de hospedagem do Consumo ou num plano regular de hospedagem do Serviço de [Aplicações,](../app-service/overview-hosting-plans.md)pode processar invocações de funções simultâneas em paralelo utilizando vários fios.  O número máximo de invocações de funções simultâneas em cada instância de aplicação de função varia em função do tipo de gatilho utilizado, bem como dos recursos utilizados por outras funções dentro da aplicação de função.

## <a name="functions-runtime-versioning"></a>Funções versão de tempo de execução

Pode configurar a versão do tempo de `FUNCTIONS_EXTENSION_VERSION` funcionamento das Funções utilizando a definição da aplicação. Por exemplo, o valor "~3" indica que a sua App de Funções utilizará 3.x como versão principal. As Aplicações de Função são atualizadas para cada nova versão menor à medida que são lançadas. Para mais informações, incluindo como visualizar a versão exata da sua aplicação de funções, consulte as versões de execução do Funcionamento das [Funções Azure](set-runtime-version.md).

## <a name="repositories"></a>Repositórios
O código para funções Azure é de código aberto e armazenado em repositórios GitHub:

* [Funções do Azure](https://github.com/Azure/Azure-Functions)
* [Anfitrião de Funções Azure](https://github.com/Azure/azure-functions-host/)
* [Portal funções azure](https://github.com/azure/azure-functions-ux)
* [Modelos de funções azure](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/) (SDK de WebJobs do Azure)
* [Azure WebJobs SDK Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/) (Extensões do SDK de WebJobs do Azure)

## <a name="bindings"></a>Enlaces
Aqui está uma mesa de todas as encadernações apoiadas.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Ter problemas com erros vindos das encadernações? Reveja a documentação dos Códigos de Erro de Ligação das [Funções Azure.](functions-bindings-error-pages.md)

## <a name="reporting-issues"></a>Questões de reporte
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte os seguintes recursos:

* [Funções Azure desencadeiam e encadernam](functions-triggers-bindings.md)
* [Criar código e testar as Funções do Azure localmente](./functions-develop-local.md)
* [Boas Práticas para Funções Azure](functions-best-practices.md)
* [Referência do desenvolvedor funções Azure C#](functions-dotnet-class-library.md)
* [Referência do desenvolvedor de funções Azure Node.js](functions-reference-node.md)
