---
title: Descrição geral das Funções do Azure
description: Compreenda como utilizar as Funções do Azure para otimizar cargas de trabalho assíncronas em minutos.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621011"
---
# <a name="an-introduction-to-azure-functions"></a>Uma introdução às Funções do Azure

As Funções Azure permitem-lhe executar pequenos pedaços de código (chamados "funções") sem se preocupar com a infraestrutura de aplicação. Com as Funções Azure, a infraestrutura em nuvem fornece todos os servidores atualizados que precisa para manter a sua aplicação em escala.

Uma função é "desencadeada" por um tipo específico de evento. [Os gatilhos suportados](./functions-triggers-bindings.md) incluem responder a alterações de dados, responder a mensagens, executar um horário ou como resultado de um pedido http.

Embora possa sempre codificar diretamente contra uma miríade de serviços, a integração com outros serviços é simplificada através da utilização de encadernações. As encadernações dão-lhe [acesso declarativo a uma grande variedade de serviços Azure e de terceiros.](./functions-triggers-bindings.md)

## <a name="features"></a>Funcionalidades

Algumas características-chave das Funções Azure incluem:

- **Aplicações sem servidores**: As funções permitem desenvolver aplicações [sem servidor estoirar](https://azure.microsoft.com/solutions/serverless/) no Microsoft Azure.

- **Escolha da linguagem**: Escreva funções utilizando a sua escolha de [ C#Java, JavaScript, Python e PowerShell](supported-languages.md).

- **Modelo de preços pay-per-use**: Pague apenas pelo tempo gasto a executar o seu código. Veja a opção do plano de alojamento de Consumo na [secção preços](#pricing).  

- **Traga as suas próprias dependências**: As funções suportam nuGet e NPM, dando-lhe acesso às suas bibliotecas favoritas.

- **Segurança integrada**: Proteja funções desencadeadas por HTTP com fornecedores da OAuth, tais como ODirecty Ativo, Facebook, Google, Twitter e Microsoft Account.

- **Integração simplificada**: Facilmente integrar-se com serviços Azure e ofertas de software como serviço (SaaS).

- **Desenvolvimento flexível**: Instale uma integração contínua e implemente o seu código através do [GitHub,](../app-service/scripts/cli-continuous-deployment-github.md) [Azure DevOps Services, e outras ferramentas](../app-service/scripts/cli-continuous-deployment-vsts.md)de [desenvolvimento suportadas.](../app-service/deploy-local-git.md)

- **Arquitetura servidora imponente**: Orquequete aplicações sem servidor com [Funções Duráveis](durable/durable-functions-overview.md).

- **Fonte aberta**: O tempo de funcionamento das funções é de código aberto e [está disponível no GitHub](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>O que posso fazer com as Funções?

As funções são uma ótima solução para o processamento de dados a granel, integrando sistemas, trabalhando com a internet-das-coisas (IoT), e construindo apis simples e micro-serviços.

Uma série de modelos está disponível para começar com cenários-chave, incluindo:

- **HTTP**: Executar código com base nos pedidos do [HTTP](functions-create-first-azure-function.md)

- **Temporizador**: Agendar código para [executar em horários predefinidos](./functions-create-scheduled-function.md)

- **Azure Cosmos DB**: Processa [novos e modificados documentos da Azure Cosmos DB](./functions-create-cosmos-db-triggered-function.md)

- **Armazenamento blob**: Processe [novas e modificadas bolhas](./functions-create-storage-blob-triggered-function.md) de armazenamento azure

- **Armazenamento de fila**: Responda às mensagens de fila de [armazenamento azure](./functions-create-storage-queue-triggered-function.md)

- **Grelha de Eventos**: Responda aos eventos da Grelha de [Eventos Azure através de subscrições e filtros](../event-grid/resize-images-on-storage-blob-upload-event.md)

- Hub de **eventos**: Responda a [grandes volumes de eventos do Azure Event Hub](./functions-bindings-event-hubs.md)

- **Fila de ônibus de serviço**: Ligue-se a outros serviços Azure ou no local [respondendo mensagens](./functions-bindings-service-bus.md) de fila de ônibus de serviço

- Tópico de **ônibus de serviço**: Ligue outros serviços Azure ou no local [respondendo a mensagens de tópico](./functions-bindings-service-bus.md) de ônibus de serviço

## <a name="pricing"></a>Quanto custam as Funções?

A Azure Functions tem três tipos de planos de preços. Escolha o que melhor se adequa às suas necessidades:

- **Plano**de consumo : O Azure fornece todos os recursos computacionais necessários. Não tens de te preocupar com a gestão de recursos, e só pagas pelo tempo que o teu código funciona.

- **Plano premium**: Especifica uma série de casos pré-aquecidos que estão sempre online e prontos para responder imediatamente. Quando a sua função funciona, o Azure fornece quaisquer recursos computacionais adicionais que sejam necessários. Você paga pelas instâncias pré-aquecidas que correm continuamente e quaisquer instâncias adicionais que você usa como Azure escala a sua app dentro e fora.

- Plano de Serviço de **Aplicações**: Execute as suas funções tal como as suas aplicações web. Se utilizar o Serviço de Aplicações para as suas outras aplicações, as suas funções podem executar o mesmo plano sem custos adicionais.

Para obter mais informações sobre os planos de alojamento, veja [Azure Functions hosting plan comparison (Comparação do plano de alojamento das Funções do Azure)](functions-scale.md). Os detalhes completos sobre os preços estão disponíveis na [Preços das Funções](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Passos Seguintes

- [Criar a sua primeira Função do Azure](functions-create-first-function-vs-code.md)  
  Inicie-se com o [Visual Studio Code,](functions-create-first-function-vs-code.md)a [linha de comando,](functions-create-first-azure-function-azure-cli.md)ou utilize o [portal Azure.](functions-create-first-azure-function.md)

- [Referência para programadores das Funções do Azure](functions-reference.md)  
  Fornece mais informações técnicas sobre o tempo de execução das Funções do Azure e uma referência para as funções de codificação e definição de acionadores e enlaces.

- [Como dimensionar as Funções do Azure](functions-scale.md)  
  Aborda os planos de serviço disponíveis com as Funções do Azure, incluindo o plano de alojamento de Consumo e como escolher o plano adequado.

- [Saiba mais sobre o Azure App Service](../app-service/overview.md)  
  Funções do Azure melhora o Serviço de Aplicações do Azure para funcionalidades essenciais como implementações, variáveis de ambiente e diagnósticos.
