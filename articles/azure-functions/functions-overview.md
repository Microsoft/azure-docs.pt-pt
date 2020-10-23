---
title: Descrição geral das Funções do Azure
description: Compreenda como utilizar as Funções do Azure para otimizar cargas de trabalho assíncronas em minutos.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 0583b68df603b04d47ac6104f0cf127b3c4bedd0
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173726"
---
# <a name="an-introduction-to-azure-functions"></a>Uma introdução às Funções do Azure

As Funções Azure permitem executar pequenas peças de código (chamadas "funções") sem se preocupar com a infraestrutura de aplicações. Com as Funções Azure, a infraestrutura de nuvem fornece todos os servidores atualizados necessários para manter a sua aplicação em funcionamento à escala.

Uma função é "desencadeada" por um tipo específico de evento. [Os gatilhos suportados](./functions-triggers-bindings.md) incluem responder a alterações nos dados, responder a mensagens, correr num horário ou como resultado de um pedido HTTP.

Embora possa sempre codificar diretamente contra vários serviços, a integração com outros serviços é simplificada através de encadernações. As obrigações dão-lhe [acesso declarativo a uma grande variedade de serviços Azure e terceiros.](./functions-triggers-bindings.md)

## <a name="features"></a>Funcionalidades

Algumas características-chave das Funções Azure incluem:

- **Aplicações sem servidor**: As funções permitem desenvolver aplicações [sem servidor](https://azure.microsoft.com/solutions/serverless/) no Microsoft Azure.

- **Escolha do idioma**: Escreva funções utilizando a sua escolha de [C#, Java, JavaScript, Python e PowerShell](supported-languages.md).

- **Modelo de preços pay-per-use**: Pague apenas pelo tempo gasto a executar o seu código. Veja a opção do plano de alojamento de Consumo na [secção preços](#pricing).  

- **Traga as suas próprias dependências**: As funções suportam o NuGet e o NPM, dando-lhe acesso às suas bibliotecas favoritas.

- **Segurança integrada**: Proteja funções desencadeadas por HTTP com fornecedores OAuth tais como Azure Ative Directory, Facebook, Google, Twitter e Microsoft Account.

- **Integração simplificada**: Integrar-se facilmente com serviços Azure e ofertas de software-as-a-service (SaaS).

- **Desenvolvimento flexível**: Confiem a integração contínua e implementem o seu código através do [GitHub,](../app-service/scripts/cli-continuous-deployment-github.md) [Azure DevOps Services,](../app-service/scripts/cli-continuous-deployment-vsts.md)e outras [ferramentas de desenvolvimento apoiadas.](../app-service/deploy-local-git.md)

- **Arquitetura apátrida sem servidor**: Orquestrar aplicações sem servidor com [funções duráveis](durable/durable-functions-overview.md).

- **Fonte aberta**: O tempo de funcionamento das funções é de código aberto e [está disponível no GitHub](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>O que posso fazer com as Funções?

As funções são uma excelente solução para o processamento de dados a granel, integração de sistemas, trabalho com a internet das coisas (IoT), e construção de APIs simples e micro-serviços.

Uma série de modelos está disponível para começar com cenários-chave, incluindo:

- **HTTP**: Executar código com base em [pedidos HTTP](functions-create-first-azure-function.md)

- **Temporizador**: Programar código para [executar em tempos predefinidos](./functions-create-scheduled-function.md)

- **Azure Cosmos DB**: Processar [documentos DB novos e modificados da Azure Cosmos](./functions-create-cosmos-db-triggered-function.md)

- **Armazenamento de bolhas**: Processe [bolhas de armazenamento Azure novas e modificadas](./functions-create-storage-blob-triggered-function.md)

- **Armazenamento de fila**: Responda às [mensagens de fila do Azure Storage](./functions-create-storage-queue-triggered-function.md)

- **Grelha de eventos**: Responder aos [eventos da Azure Event Grid através de subscrições e filtros](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Centro de Eventos**: Responda a [grandes volumes de eventos do Azure Event Hub](./functions-bindings-event-hubs.md)

- **Fila de autocarros de**serviço : Conecte-se a outros serviços Azure ou no local [respondendo mensagens de fila de autocarros de serviço](./functions-bindings-service-bus.md)

- **Tópico de ônibus de serviço**: Conecte outros serviços Azure ou serviços no [local, respondendo a mensagens de tópicos de Service Bus](./functions-bindings-service-bus.md)

## <a name="how-much-does-functions-cost"></a><a name="pricing"></a>Quanto custam as Funções?

A Azure Functions tem três tipos de planos de preços. Escolha o que melhor se adequa às suas necessidades:

- **Plano de consumo**: O Azul fornece todos os recursos computacionais necessários. Não tens de te preocupar com a gestão de recursos, e só pagas o tempo que o teu código for executado.

- **Plano premium**: Especifica uma série de casos pré-aquecidos que estão sempre online e prontos para responder imediatamente. Quando a sua função é executada, o Azure fornece quaisquer recursos computacionais adicionais que sejam necessários. Você paga as instâncias pré-aquecidas que estão a decorrer continuamente e quaisquer instâncias adicionais que usa como Azure escala a sua app dentro e fora.

- **Plano de Serviço de Aplicações**: Executar as suas funções tal como as suas aplicações web. Se utilizar o Serviço de Aplicações para as suas outras aplicações, as suas funções podem funcionar no mesmo plano sem custos adicionais.

Para obter mais informações sobre os planos de alojamento, veja [Azure Functions hosting plan comparison (Comparação do plano de alojamento das Funções do Azure)](functions-scale.md). Os detalhes completos sobre os preços estão disponíveis na [Preços das Funções](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Passos Seguintes

- [Criar a sua primeira Função do Azure](functions-create-first-function-vs-code.md)  
  Começa com o [Código do Estúdio Visual,](functions-create-first-function-vs-code.md)a [linha de comando,](functions-create-first-azure-function-azure-cli.md)ou usa o [portal Azure](functions-create-first-azure-function.md).

- [Referência para programadores das Funções do Azure](functions-reference.md)  
  Fornece mais informações técnicas sobre o tempo de execução das Funções do Azure e uma referência para as funções de codificação e definição de acionadores e enlaces.

- [Como dimensionar as Funções do Azure](functions-scale.md)  
  Aborda os planos de serviço disponíveis com as Funções do Azure, incluindo o plano de alojamento de Consumo e como escolher o plano adequado.

- [Saiba mais sobre o Azure App Service](../app-service/overview.md)  
  Funções do Azure melhora o Serviço de Aplicações do Azure para funcionalidades essenciais como implementações, variáveis de ambiente e diagnósticos.
