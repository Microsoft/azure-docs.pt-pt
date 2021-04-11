---
title: Orientação para o desenvolvimento de funções Azure
description: Aprenda os conceitos e técnicas das Funções Azure que precisa para desenvolver funções em Azure, em todas as linguagens de programação e encadernações.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 7030ca1c1950f7c06580ce7417a4429fbe330c4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614824"
---
# <a name="azure-functions-developer-guide"></a>Guia do programador das Funções do Azure
Nas Funções Azure, funções específicas partilham alguns conceitos e componentes técnicos fundamentais, independentemente do idioma ou ligação que utilizar. Antes de entrar em detalhes de aprendizagem específicos de uma determinada língua ou ligação, não se esqueça de ler este resumo que se aplica a todos eles.

Este artigo pressupõe que já leu a visão geral das [Funções Azure](functions-overview.md).

## <a name="function-code"></a>Código de função
Uma *função* é o conceito primário em Funções Azure. Uma função contém duas peças importantes - o seu código, que pode ser escrito em várias línguas, e alguns config, o function.jsem arquivo. Para idiomas compilados, este ficheiro config é gerado automaticamente a partir de anotações no seu código. Para scripts linguísticos, você deve fornecer o arquivo config si mesmo.

O function.jsno ficheiro define o gatilho da função, as encadernações e outras definições de configuração. Cada função tem um, e apenas um, acionador. O tempo de execução utiliza este ficheiro config para determinar os eventos para monitorizar e como transmitir dados e devolver dados de uma execução de função. Segue-se um exemplo function.jsarquivado.

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

Para obter mais informações, consulte [os conceitos de gatilhos e encadernações do Azure Functions](functions-triggers-bindings.md).

A `bindings` propriedade é onde você configura tanto gatilhos como encadernações. Cada ligação partilha algumas configurações comuns e algumas configurações específicas de um determinado tipo de encadernação. Cada encadernação requer as seguintes definições:

| Propriedade    | Valores | Tipo | Comentários|
|---|---|---|---|
| tipo  | Nome da ligação.<br><br>Por exemplo, `queueTrigger`. | string | |
| direção | `in`, `out`  | string | Indica se a ligação é para receber dados na função ou para enviar dados da função. |
| name | Identificador de função.<br><br>Por exemplo, `myQueue`. | string | O nome que é usado para os dados vinculados na função. Para C#, este é um nome de argumento; para o JavaScript, é a chave de uma lista de chaves/valores. |

## <a name="function-app"></a>Aplicação de funções
Uma aplicação de função fornece um contexto de execução em Azure no qual as suas funções são executadas. Como tal, é a unidade de implantação e gestão para as suas funções. Uma aplicação de função é composta por uma ou mais funções individuais que são geridas, implementadas e dimensionadas em conjunto. Todas as funções de uma aplicação de função partilham o mesmo plano de preços, método de implementação e versão de tempo de execução. Pense numa aplicação de função como uma forma de organizar e gerir coletivamente as suas funções. Para saber mais, consulte [Como gerir uma aplicação de função.](functions-how-to-use-azure-function-app-settings.md) 

> [!NOTE]
> Todas as funções de uma aplicação de funções devem ser da autoria no mesmo idioma. Em [versões anteriores](functions-versions.md) do tempo de funcionamento das Funções Azure, isso não era necessário.

## <a name="folder-structure"></a>Estrutura de pasta
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

O acima é a estrutura de pasta padrão (e recomendada) para uma aplicação De função. Se desejar alterar a localização do ficheiro do código de uma função, modifique a `scriptFile` secção dofunction.js _no_ ficheiro. Recomendamos também a [utilização da implementação do pacote](deployment-zip-push.md) para implementar o seu projeto na sua aplicação de função em Azure. Também pode utilizar ferramentas existentes como [integração contínua e implantação](functions-continuous-deployment.md) e Azure DevOps.

> [!NOTE]
> Se utilizar uma embalagem manualmente, certifique-se de que implanta o seu _host.jsnas_ pastas de ficheiro e função diretamente na `wwwroot` pasta. Não inclua a `wwwroot` pasta nas suas implementações. Caso contrário, acabas com `wwwroot\wwwroot` pastas.

#### <a name="use-local-tools-and-publishing"></a>Use ferramentas locais e publicação
As aplicações de função podem ser da autoria e publicadas utilizando uma variedade de ferramentas, incluindo [Visual Studio,](./functions-develop-vs.md) [Visual Studio Code,](./create-first-function-vs-code-csharp.md) [IntelliJ,](./functions-create-maven-intellij.md) [Eclipse,](./functions-create-maven-eclipse.md)e as [Ferramentas Core de Funções Azure.](./functions-develop-local.md) Para obter mais informações, consulte [Código e teste as Funções Azure localmente](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Como editar funções no portal Azure
O editor de Funções incorporado no portal Azure permite-lhe atualizar o seu código e a sua *function.jsno* ficheiro diretamente inline. Isto é recomendado apenas para pequenas alterações ou provas de conceito - a melhor prática é usar uma ferramenta de desenvolvimento local como o Código VS.

## <a name="parallel-execution"></a>Execução paralela
Quando vários eventos de desencadeamento ocorrem mais rapidamente do que um tempo de funcionamento de uma única função pode processá-los, o tempo de execução pode invocar a função várias vezes em paralelo.  Se uma aplicação de função estiver a utilizar o [plano de hospedagem Consumption,](event-driven-scaling.md)a aplicação de função poderá ser reduzida automaticamente.  Cada instância da aplicação de função, quer a aplicação seja executada no plano de hospedagem de Consumo ou num plano regular [de hospedagem do Serviço de Aplicações,](../app-service/overview-hosting-plans.md)pode processar invocações de funções simultâneas em paralelo utilizando vários fios.  O número máximo de invocações de funções simultâneas em cada instância de aplicação de função varia em função com base no tipo de gatilho utilizado, bem como nos recursos utilizados por outras funções dentro da aplicação de função.

## <a name="functions-runtime-versioning"></a>Versão de tempo de execução de funções

Pode configurar a versão do tempo de execução de Funções utilizando a definição da `FUNCTIONS_EXTENSION_VERSION` aplicação. Por exemplo, o valor "~3" indica que a sua aplicação de função utilizará 3.x como a sua versão principal. As aplicações de função são atualizadas para cada nova versão menor à medida que são lançadas. Para obter mais informações, incluindo como visualizar a versão exata da sua aplicação de função, consulte [como direcionar as versões de tempo de execução do Azure Functions](set-runtime-version.md).

## <a name="repositories"></a>Repositórios
O código para funções Azure é de código aberto e armazenado nos repositórios gitHub:

* [Funções do Azure](https://github.com/Azure/Azure-Functions)
* [Anfitrião das Funções Azure](https://github.com/Azure/azure-functions-host/)
* [Portal de Funções Azure](https://github.com/azure/azure-functions-ux)
* [Modelos de funções Azure](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/) (SDK de WebJobs do Azure)
* [Azure WebJobs SDK Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/) (Extensões do SDK de WebJobs do Azure)

## <a name="bindings"></a>Enlaces
Aqui está uma tabela de todas as encadernações apoiadas.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Ter problemas com erros vindos das ligações? Reveja a documentação [dos Códigos de Erro de Ligação de Funções Azure.](functions-bindings-error-pages.md)


## <a name="connections"></a>Ligações

O seu projeto de função refere informações de ligação pelo nome do seu fornecedor de configuração. Não aceita diretamente os detalhes da ligação, permitindo que sejam alterados em ambientes. Por exemplo, uma definição de gatilho pode incluir uma `connection` propriedade. Isto pode referir-se a uma cadeia de ligação, mas não é possível definir a cadeia de ligação diretamente num `function.json` . Em vez disso, definiria `connection` o nome de uma variável ambiental que contém a cadeia de ligação.

O fornecedor de configuração padrão utiliza variáveis ambientais. Estas podem ser definidas pelas [Definições de Aplicação](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) quando estão a ser executadas no serviço Azure Functions ou no [ficheiro de definições locais](functions-run-local.md#local-settings-file) quando se desenvolve localmente.

### <a name="connection-values"></a>Valores de ligação

Quando o nome de ligação se resolve com um único valor exato, o tempo de execução identifica o valor como uma _cadeia de ligação_, que normalmente inclui um segredo. Os detalhes de uma cadeia de ligação são definidos pelo serviço ao qual deseja ligar.

No entanto, um nome de ligação também pode referir-se a uma coleção de vários itens de configuração. As variáveis ambientais podem ser tratadas como uma coleção utilizando um prefixo partilhado que termina em duplos `__` sublinhados. O grupo pode então ser referenciado definindo o nome de ligação a este prefixo.

Por exemplo, a `connection` propriedade para uma definição de gatilho Azure Blob pode ser `Storage1` . Desde que não exista um único valor de corda configurado com `Storage1` o seu nome, `Storage1__serviceUri` seria usado para a propriedade da `serviceUri` ligação. As propriedades de ligação são diferentes para cada serviço. Consulte a documentação para a extensão que utiliza a ligação.

### <a name="configure-an-identity-based-connection"></a>Configure uma ligação baseada na identidade

Algumas ligações em Funções Azure são configuradas para usar uma identidade em vez de um segredo. O suporte depende da extensão utilizando a ligação. Em alguns casos, um fio de ligação ainda pode ser necessário em Funções, mesmo que o serviço ao qual está a ligar suporte ligações baseadas na identidade.

> [!IMPORTANT]
> Mesmo que uma extensão vinculativa suporte ligações baseadas na identidade, essa configuração pode ainda não ser suportada no plano de Consumo. Consulte a tabela de apoio abaixo.

As ligações baseadas na identidade são suportadas pelas seguintes extensões de gatilho e ligação:

| Nome de extensão | Versão da extensão                                                                                     | Apoiado no Plano de Consumo |
|----------------|-------------------------------------------------------------------------------------------------------|---------------------------------------|
| Blob do Azure     | [Versão 5.0.0-beta1 ou posterior](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)  | No                                    |
| Filas do Azure    | [Versão 5.0.0-beta1 ou posterior](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) | No                                    |
| Azure Event Hubs    | [Versão 5.0.0-beta1 ou posterior](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher) | No                                    |

> [!NOTE]
> O suporte para ligações baseadas na identidade ainda não está disponível para ligações de armazenamento utilizadas pelo tempo de execução de Funções para comportamentos fundamentais. Isto significa que a `AzureWebJobsStorage` regulação deve ser uma cadeia de ligação.

#### <a name="connection-properties"></a>Propriedades de ligação

Uma ligação baseada na identidade de um serviço Azure aceita as seguintes propriedades:

| Propriedade    | Necessário para extensões | Variável de ambiente | Description |
|---|---|---|---|
| Serviço URI | Azure Blob, Fila Azure | `<CONNECTION_NAME_PREFIX>__serviceUri` |  O plano de dados URI do serviço ao qual está a ligar. |
| Espaço de nome totalmente qualificado | Hubs de Eventos | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | O espaço de nomes do Event Hub totalmente qualificado. |

Podem ser suportadas opções adicionais para um determinado tipo de ligação. Consulte a documentação do componente que estica a ligação.

Quando hospedados no serviço Azure Functions, as ligações baseadas na identidade utilizam uma [identidade gerida](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json). A identidade atribuída ao sistema é utilizada por defeito. Quando executado em outros contextos, como o desenvolvimento local, a sua identidade de desenvolvedor é usada em vez disso, embora isso possa ser personalizado usando parâmetros de conexão alternativos.

##### <a name="local-development"></a>Desenvolvimento local

Ao correr localmente, a configuração acima indica o tempo de execução para utilizar a identidade do desenvolvedor local. A ligação tentará obter um sinal dos seguintes locais, por ordem:

- Uma cache local partilhada entre aplicações da Microsoft
- O contexto atual do utilizador no Estúdio Visual
- O contexto atual do utilizador no Código do Estúdio Visual
- O contexto atual do utilizador no CLI Azure

Se nenhuma destas opções for bem sucedida, ocorrerá um erro.

Em alguns casos, pode desejar especificar o uso de uma identidade diferente. Pode adicionar propriedades de configuração para a ligação que aponta para a identidade alternativa.

> [!NOTE]
> As seguintes opções de configuração não são suportadas quando hospedadas no serviço Azure Functions.

Para ligar utilizando um diretor de serviço Azure Ative Com um ID do cliente e secreto, defina a ligação com as seguintes propriedades necessárias para além das [propriedades de Conexão](#connection-properties) acima:

| Propriedade    | Variável de ambiente | Description |
|---|---|---|
| ID do inquilino | `<CONNECTION_NAME_PREFIX>__tenantId` | Identificação do inquilino do Diretório Ativo Azure (diretório). |
| ID de Cliente | `<CONNECTION_NAME_PREFIX>__clientId` |  O cliente (aplicação) ID de um registo de aplicação no inquilino. |
| Segredo do cliente | `<CONNECTION_NAME_PREFIX>__clientSecret` | Um segredo de cliente que foi gerado para o registo da aplicação. |

Exemplo das `local.settings.json` propriedades necessárias para a ligação baseada na identidade com a Azure Blob: 
```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__serviceUri": "<serviceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="grant-permission-to-the-identity"></a>Conceder permissão à identidade

Qualquer identidade que esteja a ser usada deve ter permissões para executar as ações pretendidas. Isto é normalmente feito atribuindo um papel no Azure RBAC ou especificando a identidade numa política de acesso, dependendo do serviço a que está a ligar. Consulte a documentação de cada serviço sobre as permissões necessárias e como podem ser definidas.

> [!IMPORTANT]
> Algumas permissões podem ser expostas pelo serviço que não é necessário para todos os contextos. Sempre que possível, aderir ao **princípio do menor privilégio,** concedendo a identidade apenas os privilégios necessários. Por exemplo, se a aplicação apenas precisar de ler a partir de uma bolha, use a função [de Leitor de Dados blob de armazenamento,](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) uma vez que o [Proprietário de Dados de Armazenamento blob](../role-based-access-control/built-in-roles.md#storage-blob-data-owner) inclui permissões excessivas para uma operação de leitura.


## <a name="reporting-issues"></a>Questões de Reporte
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, veja os seguintes recursos:

* [Funções Azure dispara e encaderna](functions-triggers-bindings.md)
* [Criar código e testar as Funções do Azure localmente](./functions-develop-local.md)
* [Boas Práticas para Funções Azure](functions-best-practices.md)
* [Referência do programador Azure Functions C#](functions-dotnet-class-library.md)
* [Funções Azure Node.js referência do desenvolvedor](functions-reference-node.md)
