---
title: Visão geral de monitorização da saúde para o Gateway de Aplicação Azure
description: O Azure Application Gateway monitoriza a saúde de todos os recursos na sua piscina traseira e remove automaticamente qualquer recurso considerado insalubre da piscina.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2020
ms.author: victorh
ms.openlocfilehash: a4427c05d16a42879d37fdbd2e8b8be9095fcc9b
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505904"
---
# <a name="application-gateway-health-monitoring-overview"></a>Visão geral da monitorização da saúde gateway aplicação

O Portal de Aplicações Azure por padrão monitoriza a saúde de todos os recursos na sua piscina traseira e remove automaticamente qualquer recurso considerado insalubre da piscina. Application Gateway continua a monitorizar os casos pouco saudáveis e adiciona-os de volta ao saudável pool back-end assim que ficam disponíveis e respondem a sondas de saúde. O gateway de aplicação envia as sondas de saúde com a mesma porta que é definida nas definições http-end. Esta configuração garante que a sonda está a testar a mesma porta que os clientes estariam a usar para se ligarem ao backend. 

O endereço IP de origem Aplicação Gateway utiliza para sondas de saúde depende do pool de backend:
 
- Se o pool de backend é um ponto final público, então o endereço de origem é o endereço IP público de gateway de aplicação.
- Se o pool de backend for um ponto final privado, então o endereço IP de origem é do espaço de endereço ip privado da subnet de entrada de aplicação.


![exemplo de sonda de gateway de aplicação][1]

Além de utilizar a monitorização da sonda de saúde padrão, também pode personalizar a sonda de saúde de acordo com os requisitos da sua aplicação. Neste artigo, tanto as sondas de saúde padrão como as sondas de saúde personalizadas são cobertas.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Sonda de saúde padrão

Um gateway de aplicação configura automaticamente uma sonda de saúde padrão quando não configura qualquer configuração de sonda personalizada. O comportamento de monitorização funciona fazendo um pedido http para os endereços IP configurados para o pool back-end. Para as sondas predefinidas se as definições de backend http estiverem configuradas para HTTPS, a sonda utiliza HTTPS também para testar a saúde das costas.

Por exemplo: Configura o portal de aplicações para utilizar os servidores de back-end A, B e C para receber o tráfego da rede HTTP na porta 80. A monitorização de saúde predefinida testa os três servidores a cada 30 segundos para uma resposta HTTP saudável. Uma resposta http saudável tem um código de [estado](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 e 399.

Se a verificação de sonda predefinida falhar no servidor A, o gateway da aplicação remove-o do seu pool de back-end e o tráfego de rede para de fluir para este servidor. A sonda predefinida continua a verificar se o servidor A está a cada 30 segundos. Quando o servidor A responde com sucesso a um pedido de uma sonda de saúde padrão, é adicionado de volta como saudável para a piscina traseira, e o tráfego começa a fluir para o servidor novamente.

### <a name="probe-matching"></a>Correspondência de sonda

Por predefinição, uma resposta HTTP(S) com código de estado entre 200 e 399 é considerada saudável. As sondas de saúde personalizadas suportam ainda dois critérios correspondentes. Os critérios de correspondência podem ser utilizados para modificar opcionalmente a interpretação padrão do que faz uma resposta saudável.

Seguem-se critérios correspondentes: 

- **Correspondência** do código de resposta HTTP - Critério de correspondência da sonda para aceitar código de resposta de http especificado pelo utilizador ou intervalos de código de resposta. São apoiados códigos de estado de resposta separados por vírpostas individuais ou uma série de códigos de estado.
- **CORRESPONDÊNCIA DO corpo** de resposta HTTP - Critério de correspondência da sonda que olha para o corpo de resposta HTTP e combina com uma corda especificada pelo utilizador. A partida apenas procura a presença de cordas especificadas pelo utilizador no corpo de resposta e não é uma correspondência de expressão regular completa.

Os critérios de correspondência podem ser especificados utilizando o `New-AzApplicationGatewayProbeHealthResponseMatch` cmdlet.

Por exemplo:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Uma vez especificados os critérios de correspondência, pode ser anexado à configuração da sonda utilizando um parâmetro `-Match` no PowerShell.

### <a name="default-health-probe-settings"></a>Definições de sonda de saúde padrão

| Propriedade da sonda | Valor | Descrição |
| --- | --- | --- |
| URL da sonda |http://127.0.0.1:\<port\>/ |Caminho da URL |
| Interval |30 |O tempo em segundos para esperar antes da próxima sonda de saúde ser enviada.|
| Intervalo |30 |A quantidade de tempo em segundos o gateway da aplicação aguarda uma resposta da sonda antes de marcar a sonda como insalubre. Se uma sonda voltar como saudável, o backend correspondente é imediatamente marcado como saudável.|
| Limiar insalubre |3 |Governa quantas sondas enviar para o caso de haver uma falha da sonda de saúde normal. Estas sondas de saúde adicionais são enviadas em rápida sucessão para determinar a saúde do backend rapidamente e não esperar pelo intervalo da sonda. O servidor de back-end é marcado para baixo após a contagem de falhas de sonda consecutiva atingir o limiar insalubre. |

> [!NOTE]
> A porta é a mesma porta que as definições http-end.

A sonda padrão olha apenas para http:\//127.0.0.1:\<\> portuário para determinar o estado de saúde. Se precisar de configurar a sonda de saúde para ir a um URL personalizado ou modificar quaisquer outras definições, deve utilizar sondas personalizadas.

### <a name="probe-intervals"></a>Intervalos de sonda

Todos os casos de Application Gateway sondam o backend independentemente uns dos outros. A mesma configuração da sonda aplica-se a cada instância de Gateway de aplicação. Por exemplo, se a configuração da sonda for para enviar sondas de saúde a cada 30 segundos e a porta de aplicação tiver duas instâncias, então ambos os casos enviam a sonda de saúde a cada 30 segundos.

Também se houver vários ouvintes, então cada ouvinte sonda o backend independente mente uns dos outros. Por exemplo, se houver dois ouvintes apontando para a mesma piscina de backend em duas portas diferentes (configuradas por duas definições de http backend) então cada ouvinte sonda o mesmo backend de forma independente. Neste caso, existem duas sondas de cada porta de entrada de aplicações, por exemplo, para os dois ouvintes. Se houver dois casos da porta de aplicação neste cenário, a máquina virtual de backend veria quatro sondas por intervalo de sonda configurada.

## <a name="custom-health-probe"></a>Sonda de saúde personalizada

As sondas personalizadas permitem-lhe ter um controlo mais granular sobre a monitorização da saúde. Ao utilizar sondas personalizadas, pode configurar o intervalo da sonda, o URL e o caminho para testar, e quantas respostas falhadas aceitar antes de marcar a instância de piscina de back-end como insalubre.

### <a name="custom-health-probe-settings"></a>Configurações personalizadas da sonda de saúde

A tabela seguinte fornece definições para as propriedades de uma sonda de saúde personalizada.

| Propriedade da sonda | Descrição |
| --- | --- |
| Nome |Nome da sonda. Este nome é utilizado para se referir à sonda nas definições http back-end. |
| Protocol |O protocolo costumava enviar a sonda. A sonda utiliza o protocolo definido nas definições de HTTP de back-end |
| Host |Nome anfitrião para enviar a sonda. Aplicável apenas quando vários sites estiverem configurados no Gateway da Aplicação, utilize de outra forma '127.0.0.1'. Este valor é diferente do nome de anfitrião vm. |
| Caminho |Caminho relativo da sonda. O caminho válido começa a partir de '/'. |
| Interval |Intervalo da sonda em segundos. Este valor é o intervalo de tempo entre duas sondas consecutivas. |
| Intervalo |Intervalo da sonda em segundos. Se uma resposta válida não for recebida dentro deste período de tempo, a sonda está marcada como falhada.  |
| Limiar insalubre |Contagem de tentativas de nova contagem. O servidor de back-end é marcado para baixo após a contagem de falhas de sonda consecutiva atingir o limiar insalubre. |

> [!IMPORTANT]
> Se o Gateway da Aplicação estiver configurado para um único site, por padrão, o nome anfitrião deve ser especificado como '127.0.0.1', salvo configuração em contrário em sonda personalizada.
> Para referência, uma sonda personalizada é enviada para \<protocolo\>://\<anfitrião\>:\<porta\>\<caminho\>. A porta utilizada será a mesma porta definida nas definições http-end.

## <a name="nsg-considerations"></a>Considerações de NSG

Deve permitir a entrada de tráfego na Internet nas portas TCP 65503-65534 para o Gateway de aplicação v1 SKU, e as portas TCP 65200-65535 para o V2 SKU com a subnet de destino como **Qualquer** e fonte como etiqueta de serviço **GatewayManager.** Esta gama portuária é necessária para a comunicação da infraestrutura Azure.

Além disso, a conectividade de saída da Internet não pode ser bloqueada, e o tráfego de entrada proveniente da etiqueta **AzureLoadBalancer** deve ser permitido.

Para mais informações, consulte a visão geral da [configuração do Gateway](configuration-overview.md#network-security-groups-on-the-application-gateway-subnet)de aplicação .

## <a name="next-steps"></a>Passos Seguintes
Depois de aprender sobre a monitorização da saúde application Gateway, pode configurar uma sonda de [saúde personalizada](application-gateway-create-probe-portal.md) no portal Azure ou uma [sonda de saúde personalizada](application-gateway-create-probe-ps.md) utilizando o PowerShell e o modelo de implementação do Gestor de Recursos Azure.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
