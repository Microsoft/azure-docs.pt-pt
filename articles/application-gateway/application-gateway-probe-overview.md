---
title: Visão geral da monitorização da saúde para gateway de aplicação Azure
description: A Azure Application Gateway monitoriza a saúde de todos os recursos na sua piscina traseira e remove automaticamente qualquer recurso considerado insalubre da piscina.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: f0e5a153efe26640e54f386600f07c7b3d4711d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89649057"
---
# <a name="application-gateway-health-monitoring-overview"></a>Visão geral da monitorização da saúde do Gateway de aplicação

O Azure Application Gateway por predefinição monitoriza a saúde de todos os recursos na sua piscina traseira e remove automaticamente qualquer recurso considerado insalubre da piscina. O Gateway de Aplicação continua a monitorizar as instâncias em mau estado de funcionamento e adiciona-as novamente ao conjunto de back-end em bom estado de funcionamento quando estas ficam disponíveis e respondem às pesquisas de estado de funcionamento. Por predefinição, o Gateway de aplicação envia as sondas de saúde com a mesma porta que é definida nas definições HTTP de fundo. Uma porta de sonda personalizada pode ser configurada usando uma sonda de saúde personalizada.

O endereço IP de origem Application Gateway usa para sondas de saúde depende do pool backend:
 
- Se o endereço do servidor no pool de backend for um ponto final público, então o endereço de origem é o endereço IP público do gateway de aplicações.
- Se o endereço do servidor no pool de backend for um ponto final privado, então o endereço IP de origem é do espaço de endereço IP privado da sub-rede do gateway de aplicações.

![exemplo de sonda de gateway de aplicação][1]

Além de utilizar a monitorização da sonda de saúde padrão, também pode personalizar a sonda de saúde de acordo com os requisitos da sua aplicação. Neste artigo, tanto as sondas de saúde padrão como as personalizadas são cobertas.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Sonda de saúde padrão

Um gateway de aplicações configura automaticamente uma sonda de saúde padrão quando não configura qualquer configuração personalizada da sonda. O comportamento de monitorização funciona fazendo um pedido HTTP GET para os endereços IP ou FQDN configurado na piscina traseira. Para as sondas predefinidas se as definições de backend http forem configuradas para HTTPS, a sonda utiliza HTTPS para testar a saúde dos servidores backend.

Por exemplo: Configura o seu gateway de aplicações para utilizar os servidores de back-end A, B e C para receber o tráfego da rede HTTP na porta 80. A monitorização de saúde predefinido testa os três servidores a cada 30 segundos para uma resposta HTTP saudável com um intervalo de 30 segundos para cada pedido. Uma resposta HTTP saudável tem um [código de estado](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 e 399. Neste caso, o pedido HTTP GET para a sonda de saúde será http://127.0.0.1/ semelhante.

Se a verificação da sonda predefinida falhar para o servidor A, o gateway de aplicação para de encaminhar os pedidos para este servidor. A sonda predefinida continua a verificar se o servidor A a cada 30 segundos. Quando o servidor A responde com sucesso a um pedido de uma sonda de saúde predefinido, o gateway de aplicação começa a reencaminhar os pedidos para o servidor novamente.

### <a name="default-health-probe-settings"></a>Definições de sonda de saúde predefinidos

| Propriedade sonda | Valor | Descrição |
| --- | --- | --- |
| URL da sonda |\<protocol\>://127.0.0.1:\<port\>/ |O protocolo e a porta são herdados a partir das definições HTTP de backend às quais a sonda está associada |
| Intervalo |30 |A quantidade de tempo em segundos para esperar antes da próxima sonda de saúde ser enviada.|
| Limite de Tempo Excedido |30 |A quantidade de tempo em segundos que o gateway da aplicação aguarda uma resposta da sonda antes de marcar a sonda como pouco saudável. Se uma sonda voltar tão saudável, o backend correspondente é imediatamente marcado como saudável.|
| Limiar com funcionamento incorreto |3 |Governa quantas sondas enviar para o caso de haver uma falha da sonda de saúde regular. No V1 SKU, estas sondas de saúde adicionais são enviadas numa rápida sucessão para determinar a saúde do backend rapidamente e não esperar pelo intervalo da sonda. No caso do V2 SKU, as sondas de saúde aguardam o intervalo. O servidor back-end é marcado para baixo depois que a contagem de falha da sonda consecutiva atinge o limiar insalubre. |

A sonda predefinida analisa \<protocol\> apenas: \/ /127.0.0.1: \<port\> para determinar o estado de saúde. Se precisar de configurar a sonda de saúde para ir a um URL personalizado ou modificar quaisquer outras definições, deve utilizar sondas personalizadas. Para obter mais informações sobre as sondas HTTPS, consulte [a visão geral da rescisão do TLS e acabe com o TLS com o Gateway de aplicações](ssl-overview.md#for-probe-traffic).

### <a name="probe-intervals"></a>Intervalos de sonda

Todas as instâncias de Application Gateway sondam o backend independente uns dos outros. A mesma configuração da sonda aplica-se a cada instância do Gateway de Aplicação. Por exemplo, se a configuração da sonda for para enviar sondas de saúde a cada 30 segundos e o gateway de aplicação tiver duas instâncias, então ambas as instâncias enviam a sonda de saúde a cada 30 segundos.

Além disso, se houver vários ouvintes, cada ouvinte sonda o backend independente um do outro. Por exemplo, se houver dois ouvintes a apontar para a mesma piscina de backend em duas portas diferentes (configuradas por duas definições http backend) então cada ouvinte sonda o mesmo backend de forma independente. Neste caso, existem duas sondas de cada instância de gateway de aplicação para os dois ouvintes. Se houver duas instâncias do gateway de aplicação neste cenário, a máquina virtual backend veria quatro sondas por cada intervalo de sonda configurada.

## <a name="custom-health-probe"></a>Sonda de saúde personalizada

As sondas personalizadas permitem-lhe ter mais controlo granular sobre a monitorização da saúde. Ao utilizar sondas personalizadas, pode configurar um nome de anfitrião personalizado, caminho URL, intervalo de sonda, e quantas respostas falhadas a aceitar antes de marcar a instância da piscina de back-end como insalubre, etc.

### <a name="custom-health-probe-settings"></a>Configurações personalizadas da sonda de saúde

A tabela seguinte fornece definições para as propriedades de uma sonda de saúde personalizada.

| Propriedade sonda | Descrição |
| --- | --- |
| Nome |O nome da sonda. Este nome é usado para identificar e consultar a sonda nas definições HTTP de fundo. |
| Protocolo |Protocolo usado para enviar a sonda. Isto tem de coincidir com o protocolo definido nas definições HTTP de back-end a que está associado|
| Anfitrião |Nome do anfitrião para enviar a sonda com. No v1 SKU, este valor será utilizado apenas para o cabeçalho anfitrião do pedido da sonda. Em v2 SKU, será usado tanto como cabeçalho anfitrião, como SNI |
| Caminho |Caminho relativo da sonda. Um caminho válido começa com '/' |
| Porta |Se definido, este é usado como a porta de destino. Caso contrário, utiliza a mesma porta que as definições HTTP a que está associada. Esta propriedade só está disponível no V2 SKU
| Intervalo |Intervalo da sonda em segundos. Este valor é o intervalo de tempo entre duas sondas consecutivas |
| Limite de Tempo Excedido |Tempo de tempo de sonda em segundos. Se uma resposta válida não for recebida dentro deste período de tempo, a sonda é marcada como falhada  |
| Limiar com funcionamento incorreto |Contagem de sondagens. O servidor back-end é marcado para baixo depois que a contagem de falhas de sonda consecutiva atinge o limiar insalubre |

### <a name="probe-matching"></a>Correspondência de sonda

Por predefinição, uma resposta HTTP(S) com código de estado entre 200 e 399 é considerada saudável. As sondas de saúde personalizadas suportam adicionalmente dois critérios de correspondência. Os critérios de correspondência podem ser usados para modificar opcionalmente a interpretação padrão do que faz uma resposta saudável.

Seguem-se critérios correspondentes: 

- **HTTP Response status code match** - Probe matching criterion for accepting user specific http response code or response code ranges. São suportados códigos de estado de resposta separados por vírgula ou uma gama de códigos de estado.
- **HTTP response body match** - Probe matching criterion que olha para o corpo de resposta HTTP e corresponde a uma cadeia especificada pelo utilizador. A partida apenas procura a presença de uma corda especificada pelo utilizador no corpo de resposta e não é uma combinação de expressão regular completa.

Os critérios de correspondência podem ser especificados utilizando o `New-AzApplicationGatewayProbeHealthResponseMatch` cmdlet.

Por exemplo:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Uma vez especificados os critérios de correspondência, pode ser ligado à configuração da sonda utilizando um `-Match` parâmetro em PowerShell.

## <a name="nsg-considerations"></a>Considerações do NSG

Deve permitir a entrada de tráfego de Internet nas portas TCP 65503-65534 para o Gateway de Aplicação v1 SKU e portas TCP 65200-65535 para o V2 SKU com a sub-rede de destino como **Qualquer** e fonte como etiqueta de serviço **GatewayManager.** Esta gama portuária é necessária para a comunicação da infraestrutura Azure.

Além disso, a conectividade de saída da Internet não pode ser bloqueada, e o tráfego de entrada proveniente da etiqueta **AzureLoadBalancer** deve ser permitido.

Para obter mais informações, consulte a [visão geral da configuração do Gateway de Aplicação](configuration-infrastructure.md#network-security-groups).

## <a name="next-steps"></a>Passos seguintes
Depois de aprender sobre a monitorização de saúde do Application Gateway, pode configurar uma [sonda de saúde personalizada](application-gateway-create-probe-portal.md) no portal Azure ou uma sonda de [saúde personalizada](application-gateway-create-probe-ps.md) utilizando o PowerShell e o modelo de implementação do Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
