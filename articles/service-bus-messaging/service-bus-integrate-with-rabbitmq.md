---
title: Como integrar o RabbitMQ com o Azure Service Bus
description: Guia passo a passo sobre como integrar o RabbitMQ com o Azure Service Bus
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.service: service-bus
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: 6366824b8dc7f63f99ebda2a542d95d3eb1c6146
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91301206"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>Como integrar o RabbitMQ com o Azure Service Bus

Neste guia, vamos aprender a enviar mensagens da RabbitMQ para a Azure Service Bus.

Aqui estão alguns cenários em que podemos fazer uso destas capacidades:

- **Configurações de bordas**: Temos uma configuração de borda onde estamos a enviar mensagens para o RabbitMQ, mas queremos encaminhar essas mensagens para [a Azure Service Bus](./service-bus-messaging-overview.md) para posterior processamento, para que possamos usar muitas das capacidades do [Azure Big Data.](/azure/architecture/guide/architecture-styles/big-data)
- **Hybrid Cloud**: A sua empresa acaba de adquirir um terceiro que usa o RabbitMQ para as suas necessidades de mensagens. Estão numa nuvem diferente. Enquanto eles fazem a transição para a Azure já pode começar a partilhar dados fazendo a ponte rabbitMQ com a Azure Service Bus.
- **Integração de terceiros**: Um terceiro usa o RabbitMQ como corretor, e quer enviar os seus dados para nós, mas eles estão fora da nossa organização. Podemos fornecer-lhes a Sas Key dando-lhes acesso a um conjunto limitado de filas de autocarros da Azure Service para onde podem reencaminar as suas mensagens.

A lista continua, mas podemos resolver a maioria destes casos de uso, fazendo a ponte rabbitmq para Azure.

Primeiro precisa criar uma conta Azure gratuita ao inscrever-se [aqui](https://azure.microsoft.com/free/)

Assim que fizer o seu contrato de sessão, vá ao [portal Azure](https://portal.azure.com/) e crie um novo [espaço de nomes](./service-bus-create-namespace-portal.md)para o Azure Service Bus . Os espaços de nome são os recipientes de deteção onde os nossos componentes de mensagens vão viver, como filas e tópicos.

## <a name="adding-a-new-azure-service-bus-namespace"></a>Adicionar um novo espaço de nomes de autocarros da Azure Service

No portal Azure, clique no botão grande mais para adicionar um novo recurso

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="Criar um recurso":::

Em seguida, selecione Integração e clique no Azure Service Bus para criar um espaço de nome de mensagens:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="Selecione ônibus da Azure Service":::

Serão solicitados a introduzir a informação do espaço de nome. Selecione a subscrição do Azure que pretende utilizar. Se não tiver um [grupo de recursos,](../azure-resource-manager/management/manage-resource-groups-portal.md)pode criar um novo.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="Create namespace":::

Use `rabbitmq` `Namespace name` para, mas pode ser o que quiser. Em seguida, definir `East US` para o local. Escolha `Basic` como o preço.

Se tudo correr bem, deve ver o seguinte ecrã de confirmação:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="Criar confirmação de espaço de nome":::

Em seguida, no portal Azure, verá o seu novo `rabbitmq` espaço de nome listado lá. Clique nele para aceder ao recurso para que possa adicionar uma fila ao mesmo.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="Lista de recursos com novo espaço de nome":::

## <a name="creating-our-azure-service-bus-queue"></a>Criar a nossa fila de autocarros Azure Service

Agora que tem o seu espaço de nomes Azure Service Bus, clique `Queues` no botão à esquerda, para `Entities` que possa adicionar uma nova fila:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="Criar fila":::

O nome da fila será `from-rabbitmq` apenas como um lembrete de onde vêm as mensagens. Pode deixar todas as outras opções como padrão, mas pode alterá-las de acordo com as necessidades da sua app.

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>Habilitando o RabbitMQ Shovel Plugin

Para enviar mensagens da RabbitMQ para a Azure Service Bus, vamos usar o [Shovel Plugin](https://www.rabbitmq.com/shovel.html) que vem embalado com o RabbitMQ. Pode ativar o plugin e a sua interface visual com este comando:

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>Talvez precises de executar esse comando como raiz.

Agora é hora de obter as credenciais necessárias para ligar RabbitMQ a Azure.

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>Ligando o RabbitMQ ao ônibus de serviço Azure

Terá de criar uma Política de [Acesso Partilhado](../storage/common/storage-sas-overview.md) (SAS) para a sua fila, para que o RabbitMQ possa publicar mensagens para a sua fila. Uma Política SAS permite-lhe especificar o que é permitido a parte externa fazer com o seu recurso. A ideia é que o RabbitMQ seja capaz de enviar mensagens, mas não ouvir ou gerir a fila.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="Adicionar política SAS":::

Marque a `Send` caixa e, em seguida, clique `Create` para ter a nossa Política SAS no lugar.

Uma vez criada a política, clique nele para ver a **Cadeia de Ligação Primária**. Vamos usá-lo para deixar o RabbitMQ falar com a Azure Service Bus:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="Obter política SAS":::

Antes de poder utilizar essa cadeia de ligação, terá de convertê-la no formato de ligação AMQP da RabbitMQ. Então vá para a [ferramenta de conversor de cordas de ligação](https://red-mushroom-0f7446a0f.azurestaticapps.net/) e cole a sua cadeia de ligação no formulário, clique em converter. Terá uma ligação que está pronta para o RabbitMQ. (Esse site executa tudo o que é local no seu navegador para que os seus dados não são enviados através do fio). Pode aceder ao seu código fonte no [GitHub.](https://github.com/videlalvaro/connstring_to_amqp)

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="Converter cadeia de conexão":::

Agora abra o plugin de gestão RabbitMQ nos nossos navegadores `http://localhost:15672/#/dynamic-shovels` e vá `Admin -> Shovel Management` para, onde você pode adicionar a sua nova pá que cuidará de enviar mensagens de uma fila RabbitMQ para a sua fila de ônibus de serviço Azure.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="Adicionar pá de coelhinhomq":::

Ligue para a sua Pá `azure` e escolha como protocolo de `AMQP 0.9.1` origem. Na imagem, temos `amqp://` , que é o URI padrão que nos liga a um servidor RabbitMQ local. Certifique-se de que o adapta à sua atual implantação.

No lado da fila, podes usar `azure` como nome da tua fila. Se essa fila não existir, o RabbitMQ vai criá-la para si. Também pode escolher o nome de uma fila que já existe. Pode deixar as outras opções como padrão.

Então, do `destination` lado das coisas, escolha `AMQP 1.0` como protocolo. No `URI` campo, introduza a corda de ligação que obteve do passo anterior, caso tenha convertido a sua cadeia de ligação Azure para o formato RabbitMQ. Deverá ter o seguinte aspeto:

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

No `Address` campo vamos inserir o nome da sua fila de **autocarros Azure Service,** neste caso, foi `from-rabbitmq` chamada. Clique `Add Shovel` em , e a sua configuração deve estar pronta para começar a receber mensagens.

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>Publicando mensagens de RabbitMQ para Azure Service Bus

Na interface De Gestão RabbitMQ podemos `Queues` ir, selecionar a `azure` fila e procurar o `Publish message` painel. Aparecerá um formulário que lhe permitirá publicar mensagens diretamente na sua fila. Para o nosso exemplo, vamos apenas adicionar `fist message` como o e bater `Payload` `Publish Message` :

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="Publicar primeira mensagem":::

Volte para Azure e inspecione a sua fila. Clique `Service Bus Explorer` no painel esquerdo e, em seguida, clique no botão _Peek._ Se tudo correr bem, verá que a sua fila agora tem uma mensagem. Parabéns!

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="Fila de ônibus de serviço Azure":::

Mas vamos certificar-nos de que a mensagem é a que enviaste do RabbitMQ. Selecione o `Peek` separador e clique no `Peek` botão para recuperar as últimas mensagens na sua fila. Clique na mensagem para inspecionar o seu conteúdo. Devia ver algo como a imagem abaixo onde está `first message` listado.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="Espreitar a fila":::

## <a name="lets-recap"></a>Vamos Recapitular

Parabéns! Conseguiu muito! Conseguiu levar as suas mensagens da RabbitMQ para a Azure Service Bus, vamos recapitular os passos:

1. Criar um espaço de nomeação de ônibus de serviço Azure
2. Adicione uma fila ao espaço de nomes
3. Adicione uma Política SAS à sua fila
4. Obtenha a cadeia de ligação de fila
5. Ativar o plugin da pá RabbitMQ & a interface de gestão
6. Converta a cadeia de ligação Azure Service Bus ao formato AMQP da RabbitMQ
7. Adicione uma nova pá ao RabbitMQ & conectá-la ao Azure Service Bus
8. Publicar mensagens

Seguindo os passos anteriores, integrou áreas do seu org que estavam fora de Azure. O plugin da Pá permitiu-lhe enviar mensagens da RabbitMQ para a Azure Service Bus. Isto tem enormes vantagens, uma vez que agora pode permitir que terceiros de confiança conectem as suas apps com a sua implementação Azure.

No final, as mensagens são sobre permitir ligações, e com esta técnica acabamos de abrir uma nova.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Azure Service Bus](./service-bus-messaging-overview.md)
- Saiba mais sobre [o suporte amQP 1.0 em Service Bus](./service-bus-amqp-overview.md)
