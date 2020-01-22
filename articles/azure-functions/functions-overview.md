---
title: Descrição geral das Funções do Azure
description: Compreenda como utilizar as Funções do Azure para otimizar cargas de trabalho assíncronas em minutos.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 1cd7b3db92de339b7614a4271f9e6793cdf8f2ad
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292914"
---
# <a name="an-introduction-to-azure-functions"></a>Uma introdução às Funções do Azure

Azure Functions permite que você execute pequenas partes de código (chamadas de "funções") sem se preocupar com a infraestrutura do aplicativo. Com o Azure Functions, a infraestrutura de nuvem fornece todos os servidores atualizados que você precisa para manter seu aplicativo em execução em escala.

Uma função é "disparada" por um tipo específico de evento. Os [gatilhos com suporte](./functions-triggers-bindings.md) incluem a resposta a alterações nos dados, a resposta a mensagens, a execução em um agendamento ou como resultado de uma solicitação HTTP.

Embora você sempre possa codificar diretamente em uma infinidade de serviços, a integração com outros serviços é simplificada com o uso de associações. As associações oferecem [acesso declarativo a uma ampla variedade de serviços do Azure e de](./functions-triggers-bindings.md)terceiros.

## <a name="features"></a>Funcionalidades

Alguns dos principais recursos do Azure Functions incluem:

- **Aplicativos sem servidor**: o Functions permite que você desenvolva aplicativos sem [servidor](https://azure.microsoft.com/solutions/serverless/) no Microsoft Azure.

- **Opção de linguagem**: escreva funções usando sua escolha [ C#, Java, JavaScript, Python e PowerShell](supported-languages.md).

- **Modelo de preços de pagamento por uso**: pague apenas pelo tempo gasto na execução do código. Veja a opção do plano de alojamento de Consumo na [secção preços](#pricing).  

- **Traga suas próprias dependências**: o Functions oferece suporte a NUGET e NPM, dando a você acesso às suas bibliotecas favoritas.

- **Segurança integrada**: proteger funções disparadas por http com provedores OAuth, como Azure Active Directory, Facebook, Google, Twitter e conta da Microsoft.

- **Integração simplificada**: integre-se facilmente a serviços do Azure e ofertas de software como serviço (SaaS).

- **Desenvolvimento flexível**: Configure a integração contínua e implante seu código por meio do [GitHub](../app-service/scripts/cli-continuous-deployment-github.md), [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md)e outras [ferramentas de desenvolvimento com suporte](../app-service/deploy-local-git.md).

- **Arquitetura sem monitoração de estado**: orquestrar aplicativos de servidor com [Durable Functions](durable/durable-functions-overview.md).

- **Código-fonte aberto**: o tempo de execução do Functions é de código-fonte aberto e está [disponível no GitHub](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>O que posso fazer com as Funções?

O Functions é uma ótima solução para o processamento de dados em massa, a integração de sistemas, o trabalho com a IoT (Internet das coisas) e a criação de APIs e micro-serviços simples.

Uma série de modelos está disponível para você começar a usar os principais cenários, incluindo:

- **Http**: executar código com base em [solicitações HTTP](functions-create-first-azure-function.md)

- **Temporizador**: agendar código para ser [executado em horários predefinidos](./functions-create-scheduled-function.md)

- **Azure Cosmos DB**: processar [documentos de Azure Cosmos DB novos e modificados](./functions-create-cosmos-db-triggered-function.md)

- **Armazenamento de blob**: processar [blobs de armazenamento do Azure novos e modificados](./functions-create-storage-blob-triggered-function.md)

- **Armazenamento de filas**: responder a [mensagens da fila de armazenamento do Azure](./functions-create-storage-queue-triggered-function.md)

- **Grade de eventos**: responder a [eventos da grade de eventos do Azure por meio de assinaturas e filtros](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Hub de eventos**: responder a [altos volumes de eventos do hub de eventos do Azure](./functions-bindings-event-hubs.md)

- **Fila do barramento de serviço**: Conecte-se a outros serviços do Azure ou locais [respondendo mensagens de fila do barramento de serviço](./functions-bindings-service-bus.md)

- **Tópico do barramento de serviço**: conectar outros serviços do Azure ou serviços locais [respondendo a mensagens de tópico do barramento de serviço](./functions-bindings-service-bus.md)

## <a name="pricing"></a>Quanto custam as Funções?

Azure Functions tem três tipos de planos de preços. Escolha o que melhor se adequa às suas necessidades:

- **Plano de consumo**: o Azure fornece todos os recursos computacionais necessários. Você não precisa se preocupar com o gerenciamento de recursos e pagar apenas pelo tempo de execução do código.

- **Plano Premium**: você especifica um número de instâncias pré-configuradas que estão sempre online e prontas para responder imediatamente. Quando sua função é executada, o Azure fornece quaisquer recursos computacionais adicionais necessários. Você paga pelas instâncias pré-configuradas executadas continuamente e quaisquer instâncias adicionais que você usa como o Azure dimensiona seu aplicativo para dentro e para fora.

- **Plano do serviço de aplicativo**: execute suas funções da mesma forma que seus aplicativos Web. Se você usar o serviço de aplicativo para seus outros aplicativos, suas funções poderão ser executadas no mesmo plano sem custo adicional.

Para obter mais informações sobre os planos de alojamento, veja [Azure Functions hosting plan comparison (Comparação do plano de alojamento das Funções do Azure)](functions-scale.md). Os detalhes completos sobre os preços estão disponíveis na [Preços das Funções](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Próximos Passos

- [Criar a sua primeira Função do Azure](functions-create-first-function-vs-code.md)  
  Comece a usar o [Visual Studio Code](functions-create-first-function-vs-code.md), a [linha de comando](functions-create-first-azure-function-azure-cli.md)ou use o [portal do Azure](functions-create-first-azure-function.md).

- [Referência para programadores das Funções do Azure](functions-reference.md)  
  Fornece mais informações técnicas sobre o tempo de execução das Funções do Azure e uma referência para as funções de codificação e definição de acionadores e enlaces.

- [Como dimensionar as Funções do Azure](functions-scale.md)  
  Aborda os planos de serviço disponíveis com as Funções do Azure, incluindo o plano de alojamento de Consumo e como escolher o plano adequado.

- [Saiba mais sobre o Azure App Service](../app-service/overview.md)  
  Funções do Azure melhora o Serviço de Aplicações do Azure para funcionalidades essenciais como implementações, variáveis de ambiente e diagnósticos.
