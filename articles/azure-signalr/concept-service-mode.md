---
title: Modo de serviço no Serviço Azure SignalR
description: Uma visão geral dos diferentes modos de serviço no Serviço Azure SignalR, explicar as suas diferenças e cenários de utilizador aplicáveis
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: kenchen
ms.openlocfilehash: 60f1ab0440120cb9a96e6c05a4fc1987ead29188
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92143265"
---
# <a name="service-mode-in-azure-signalr-service"></a>Modo de serviço no Serviço Azure SignalR

O modo de serviço é um conceito importante no Serviço Azure SignalR. Quando criar um novo recurso SignalR, será solicitado que especifique um modo de serviço:

:::image type="content" source="media/concept-service-mode/create.png" alt-text="Escolha o modo de serviço quando criar":::

Também pode alterá-lo mais tarde no menu de definições:

:::image type="content" source="media/concept-service-mode/update.png" alt-text="Modo de serviço de atualização":::

O Serviço Azure SignalR suporta atualmente três modos de serviço: **padrão,** **sem servidor** e **clássico**. O seu recurso SignalR comportar-se-á de forma diferente em diferentes modos. Neste artigo, você vai aprender as suas diferenças e como escolher o modo de serviço certo com base no seu cenário.

## <a name="default-mode"></a>Modo predefinido

O modo predefinido é o valor predefinido para o modo de serviço quando cria um novo recurso SignalR. Neste modo, a sua aplicação funciona como uma aplicação signalR ASP.NET típica ASP.NET (ou ASP.NET), onde tem um servidor web que acolhe um hub (chamado hub server a seguir) e os clientes podem ter comunicação em tempo real duplex com o servidor do hub. A única diferença é que, em vez de ligar diretamente o cliente e o servidor, o cliente e o servidor conectam-se ao serviço SignalR e utilizam o serviço como procuração. Abaixo está um diagrama que ilustra a estrutura típica da aplicação no modo predefinido:

:::image type="content" source="media/concept-service-mode/default.png" alt-text="Estrutura de aplicação no modo predefinido":::

Portanto, se tem uma aplicação SignalR e quer integrar-se com o serviço SignalR, o modo predefinido deve ser a escolha certa para a maioria dos casos.

### <a name="connection-routing-in-default-mode"></a>Encaminhamento de ligação no modo predefinido

No modo predefinido, haverá ligações websocket entre o servidor do hub e o serviço SignalR (chamadas ligações de servidor). Estas ligações são usadas para transferir mensagens entre servidor e cliente. Quando um novo cliente está ligado, o serviço SignalR irá encaminhar o cliente para um servidor de hub (assumir que tem mais de um servidor) através das ligações de servidor existentes. Em seguida, a ligação do cliente irá manter-se no mesmo servidor do hub durante a sua vida útil. Quando o cliente envia mensagens, vão sempre para o mesmo servidor do hub. Com este comportamento, pode manter com segurança alguns estados para ligações individuais no servidor do seu hub. Por exemplo, se pretender transmitir algo entre o servidor e o cliente, não precisa de considerar o caso de que os pacotes de dados vão para diferentes servidores.

> [!IMPORTANT]
> Isto também significa que no modo predefinido o cliente não pode ligar-se sem que o servidor seja ligado primeiro. Se todos os servidores do seu hub estiverem desligados devido a interrupção de rede ou reinicialização do servidor, o seu cliente liga-se a um erro que lhe diz que nenhum servidor está ligado. Por isso, é da sua responsabilidade certificar-se de que existe pelo menos um servidor hub ligado ao serviço SignalR (por exemplo, ter vários servidores de hub e certificar-se de que não vão ficar offline ao mesmo tempo para coisas como manutenção).

Este modelo de encaminhamento também significa que quando um servidor de hub fica offline, as ligações encaminhadas que o servidor será largada. Por isso, deverá esperar que a ligação caia quando o servidor do hub estiver offline para manutenção e manuseamento de religação adequada para que não tenha um impacto negativo na sua aplicação.

## <a name="serverless-mode"></a>Modo sem servidor

O modo serverless, como o seu nome indica, é um modo que não pode ter qualquer servidor de hub. Comparando com o modo predefinido, neste modo o cliente não necessita de um servidor de hub para se conectar. Todas as ligações estão ligadas ao serviço num modo "sem servidores" e o serviço é responsável por manter ligações ao cliente como manusear pings de clientes (no modo predefinido isto é tratado por servidores hub).

Também não existe nenhuma ligação ao servidor neste modo (se tentar utilizar o serviço SDK para estabelecer a ligação do servidor, obterá um erro). Portanto, também não existe encaminhamento de ligação e pegajosa servidor-cliente (conforme descrito na secção de modo predefinido). Mas ainda pode ter uma aplicação do lado do servidor para empurrar mensagens para os clientes. Isto pode ser feito de duas maneiras, use [APIs REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) para envio único, ou através de uma ligação websocket para que possa enviar várias mensagens de forma mais eficiente (note que esta ligação websocket é diferente da ligação do servidor).

> [!NOTE]
> Tanto a API REST como a websocket são suportadas na gestão de serviços [signalR SDK](https://github.com/Azure/azure-signalr/blob/dev/docs/management-sdk-guide.md). Se estiver a utilizar um idioma diferente de .NET, também pode invocar manualmente as APIs REST seguindo esta [especificação.](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)
>
> Se estiver a utilizar funções Azure, pode utilizar [as ligações de serviço SignalR para funções Azure](../azure-functions/functions-bindings-signalr-service.md) (doravante chamadas de ligação de funções) para enviar mensagens como uma ligação de saída.

Também é possível que a sua aplicação de servidor receba mensagens e eventos de conexão dos clientes. O serviço entregará mensagens e eventos de conexão a pontos finais pré-configurados (chamados Upstream) utilizando webhooks. Comparando com o modo predefinido, não existe garantia de pegajosa e os pedidos HTTP podem ser menos eficientes do que as ligações websocket.

Para obter mais informações sobre como configurar a montante, consulte este [doc](./concept-upstream.md).

Abaixo está um diagrama que ilustra como funciona o modo sem servidor:

:::image type="content" source="media/concept-service-mode/serverless.png" alt-text="Estrutura de aplicação no modo sem servidor":::

> [!NOTE]
> Tenha em atenção no modo predefinido que também pode utilizar a ligação REST API/management SDK/função para enviar mensagens diretamente ao cliente se não quiser passar pelo servidor do hub. Mas no modo predefinido as ligações do cliente ainda são manuseadas por servidores hub e a montante não funcionará nesse modo.

## <a name="classic-mode"></a>Modo clássico

Clássico é um modo misto de modo padrão e sem servidor. Neste modo, o modo de ligação é decidido se existe um servidor de hub ligado quando a ligação do cliente é estabelecida. Se houver servidor de hub, a ligação do cliente será encaminhada para um servidor de hub. Caso contrário, entrará num modo sem servidor onde o cliente para a mensagem do servidor não pode ser entregue no servidor hub. Isto irá causar algumas discrepâncias, por exemplo, se todos os servidores do hub estiverem indisponíveis por um curto período de tempo, todas as ligações do cliente criadas durante esse tempo estarão em modo sem servidor e não podem enviar mensagens para o servidor hub.

> [!NOTE]
> O modo clássico é principalmente para retrocompatibilidade para as aplicações criadas antes de existir o modo padrão e sem servidor. É fortemente recomendado não usar mais este modo. Para novas aplicações, por favor escolha padrão ou sem servidor com base no seu cenário. Para aplicações existentes, também é recomendado rever os seus casos de uso e escolher um modo de serviço adequado.

O modo clássico também não suporta algumas novas funcionalidades, como a montante no modo sem servidor.

## <a name="choose-the-right-service-mode"></a>Escolha o modo de serviço certo

Agora deve entender as diferenças entre os modos de serviço e saber escolher entre eles. Como já aprendeu na secção anterior, o modo clássico não é encorajado e só deve escolher entre o padrão e o sem servidor. Aqui ficam mais algumas dicas que podem ajudá-lo a fazer a escolha certa para novas aplicações e retirar o modo clássico para as aplicações existentes.

* Se já está familiarizado com o funcionamento da biblioteca SignalR e se pretende mover-se de um SignalR auto-hospedado para utilizar o Serviço Azure SignalR, escolha o modo predefinido. O modo predefinido funciona exatamente da mesma forma que o SignalR auto-hospedado (e pode utilizar o mesmo modelo de programação na biblioteca SignalR), o serviço SignalR funciona apenas como um proxy entre clientes e servidores hub.

* Se estiver a criar uma nova aplicação e não quiser manter as ligações do servidor do hub e do servidor, escolha o modo sem servidor. Este modo funciona normalmente em conjunto com as Funções Azure, pelo que não necessita de manter nenhum servidor. Ainda pode ter comunicações duplex (com REST API/management SDK/fun binding + upstream), mas o modelo de programação será diferente da biblioteca SignalR.

* Se tiver ambos os servidores do hub para servir as ligações do cliente e a aplicação backend para empurrar diretamente mensagens para os clientes (por exemplo, através da REST API), deverá ainda escolher o modo predefinido. Tenha em mente que a diferença chave entre o modo padrão e o sem servidor é se tem servidores hub e como as ligações do cliente são encaminhadas. REST API/management SDK/função pode ser utilizado em ambos os modos.

* Se realmente tem um cenário misto, por exemplo, tem dois hubs diferentes no mesmo recurso SignalR, um usado como um hub signalr tradicional e o outro usado com Funções Azure e não tem servidor hub, você realmente deve considerar separá-los em dois recursos SignalR, um em modo padrão e outro em modo sem servidor.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o modo padrão e sem servidor, leia os seguintes artigos:

* [Detalhes internos do Serviço SignalR do Azure](signalr-concept-internals.md)

* [Desenvolvimento das Funções do Azure e configuração com o Azure SignalR Service](signalr-concept-serverless-development-config.md)