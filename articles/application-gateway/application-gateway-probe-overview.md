---
title: Estado de funcionamento da descrição geral da monitorização para o Gateway de aplicação do Azure
description: Saiba mais sobre as capacidades de monitorização no Gateway de aplicação do Azure
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 8/6/2018
ms.author: victorh
ms.openlocfilehash: d0c425bcb9961fde9fb319991148c18c6a9ff57b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120555"
---
# <a name="application-gateway-health-monitoring-overview"></a>Descrição geral do Application Gateway health monitorização

O Gateway de aplicação do Azure por predefinição monitoriza o estado de funcionamento de todos os recursos no seu conjunto de back-end e automaticamente remove qualquer recurso considerado em mau estado de funcionamento do conjunto. Gateway de aplicação continua a monitorizar as instâncias em mau estado de funcionamento e adiciona-os novamente para o conjunto de back-end em bom estado, uma vez que eles se tornarem disponíveis e respondem às sondas de estado de funcionamento. Gateway de aplicação envia que o estado de funcionamento sondas com a mesma porta que é definida nas definições de HTTP de back-end. Esta configuração garante que a sonda está a testar a mesma porta que os clientes estariam a utilizar para ligar ao back-end.

![exemplo de sonda de gateway de aplicação][1]

Além de utilizar a monitorização de sonda de estado de funcionamento do predefinido, também pode personalizar a sonda de estado de funcionamento para atender às necessidades da sua aplicação. Neste artigo, são abordadas predefinido e sondas de estado de funcionamento personalizados.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Sonda de estado de funcionamento predefinida

Um gateway de aplicação configura automaticamente uma sonda de estado de funcionamento predefinida quando não configurada qualquer configuração de sonda personalizada. O comportamento de monitorização funciona fazendo uma solicitação HTTP para os endereços IP configurados para o conjunto de back-end. Para sondas padrão se as definições de http de back-end estão configuradas para HTTPS, a sonda utiliza HTTPS também para testar o estado de funcionamento dos back-ends.

Por exemplo: Configurar o gateway de aplicação para utilizar servidores de back-end A, B e C para receber o tráfego de rede HTTP na porta 80. A monitorização de estado de funcionamento de predefinição testa os três servidores a cada 30 segundos para uma resposta HTTP em bom estado. Uma bom estado de funcionamento resposta HTTP tem um [código de estado](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 e 399.

Se falhar a verificação de sonda de padrão para o servidor A, o gateway de aplicação remove-o do seu conjunto de back-end e deixa de tráfego de rede que fluem para este servidor. A sonda predefinida ainda continua a verificar a existência de servidor um cada 30 segundos. Quando o servidor A responde com êxito a uma solicitação de uma sonda de estado de funcionamento predefinida, é adicionado novamente como bom estado de funcionamento para o conjunto de back-end e o tráfego começar a fluir para o servidor novamente.

### <a name="probe-matching"></a>Correspondência de sonda

Por predefinição, uma resposta de HTTP (S) com o código de estado entre 200 e 399 é considerada em bom estado. Além disso, sondas de estado de funcionamento personalizados suportam dois critérios de correspondência. Critérios de correspondência podem ser utilizado para, opcionalmente, modifique a interpretação de padrão do que constitui uma resposta de bom estado de funcionamento.

A seguir é correspondentes aos critérios: 

- **Correspondência de código de estado de resposta HTTP** - critério para abertos ao recebimento de correspondência de sonda http resposta código ou resposta intervalos de código especificadas pelo utilizador. Códigos de estado de resposta separados por vírgulas individuais ou um intervalo de código de estado é suportado.
- **Correspondência de corpo de resposta HTTP** - critério que examina o corpo de resposta HTTP e as correspondências com um utilizador especificado a cadeia de caracteres de correspondência de sonda. O aspeto de apenas de correspondência de presença do utilizador especificado no corpo de resposta de cadeias de caracteres e não é uma correspondência de expressão regular completa.

Critérios de correspondência podem ser especificados utilizando o `New-AzApplicationGatewayProbeHealthResponseMatch` cmdlet.

Por exemplo:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Assim que os critérios de correspondência for especificado, pode ser anexado a configuração através de sonda um `-Match` parâmetro no PowerShell.

### <a name="default-health-probe-settings"></a>Predefinições de sonda de estado de funcionamento

| Propriedade de pesquisa | Value | Descrição |
| --- | --- | --- |
| URL de Pesquisa |http://127.0.0.1:\<port\>/ |Caminho do URL |
| Intervalo |30 |A quantidade de tempo em segundos a aguardar antes da próxima sonda de estado de funcionamento é enviada.|
| Tempo limite |30 |A quantidade de tempo em segundos, o gateway de aplicação aguarda uma resposta de sonda antes de os marcar a sonda como mau estado de funcionamento. Se uma sonda retornar saudável, o back-end correspondente será imediatamente marcado como em bom estado.|
| Limiar com funcionamento incorreto |3 |Controla quantas sondas para enviar no caso de falha da sonda de estado de funcionamento normal. Estas sondas de estado de funcionamento adicionais são enviadas numa rápida sucessão para determinar o estado de funcionamento do back-end rapidamente e não espere durante o intervalo de sonda. O servidor de back-end está marcado para baixo depois que a contagem de falhas consecutivas da sonda atinge o limiar de mau estado de funcionamento. |

> [!NOTE]
> A porta é a mesma porta que as definições de HTTP de back-end.

A sonda predefinida analisa apenas http:\//127.0.0.1:\<porta\> para determinar o estado de funcionamento. Se precisar de configurar a sonda de estado de funcionamento para ir para um URL personalizado ou modificar outras definições, tem de utilizar as pesquisas personalizadas.

### <a name="probe-intervals"></a>Intervalos de sonda

Todas as instâncias de Gateway de aplicação de sonda de back-end independente umas das outras. A mesma configuração de pesquisa aplica-se a cada instância de Gateway de aplicação. Por exemplo, se a configuração de sonda consiste em enviar sondas de estado de funcionamento a cada 30 segundos e o gateway de aplicação tem duas instâncias, em seguida, ambas as instâncias enviam a sonda de estado de funcionamento a cada 30 segundos.

Também se existirem várias escutas, cada serviço de escuta sonda o back-end independente umas das outras. Por exemplo, se existirem dois serviços de escuta que aponta para o mesmo conjunto de back-end em duas portas diferentes (configurado por duas definições de http de back-end), em seguida, cada serviço de escuta sondas back-end da mesmo forma independente. Neste caso, existem dois sondas de cada instância de gateway de aplicação para os dois serviços de escuta. Se existirem duas instâncias do gateway de aplicação neste cenário, a máquina virtual de back-end veria quatro sondas de acordo com o intervalo de sonda configurado.

## <a name="custom-health-probe"></a>Sonda de estado de funcionamento personalizados

As pesquisas personalizadas permitem que tenha um controle mais granular sobre a monitorização de estado de funcionamento. Ao utilizar sondas personalizadas, pode configurar o intervalo de pesquisa, o URL e caminho para testar e quantos respostas com falhas para aceitar antes de os marcar a instância de conjunto de back-end como mau estado de funcionamento.

### <a name="custom-health-probe-settings"></a>Definições de sonda de estado de funcionamento personalizados

A tabela seguinte fornece definições para as propriedades de uma sonda de estado de funcionamento personalizados.

| Propriedade de pesquisa | Descrição |
| --- | --- |
| Name |Nome da sonda. Este nome é utilizado para fazer referência a sonda nas definições de HTTP de back-end. |
| Protocolo |Protocolo utilizado para enviar a sonda. A sonda utiliza o protocolo definido nas definições de HTTP de back-end |
| Anfitrião |Nome de anfitrião para enviar a sonda. Aplicável apenas quando vários sites está configurada no Gateway de aplicação, caso contrário, utilize "127.0.0.1". Este valor é diferente do nome de anfitrião VM. |
| Caminho |Caminho relativo da sonda. O caminho válido começa com "/". |
| Intervalo |Intervalo de sonda em segundos. Este valor é o intervalo de tempo entre dois sondas consecutivos. |
| Tempo limite |Sonda de tempo limite em segundos. Se uma resposta válida não está a ser recebida durante este período de tempo limite, a sonda está marcada como falhado.  |
| Limiar com funcionamento incorreto |Contagem de repetições de sonda. O servidor de back-end está marcado para baixo depois que a contagem de falhas consecutivas da sonda atinge o limiar de mau estado de funcionamento. |

> [!IMPORTANT]
> Se o Gateway de aplicação está configurado para um único site, por predefinição, o anfitrião nome deve ser especificado como '127.0.0.1', a menos que caso contrário, é configurado na sonda personalizada.
> Para referência uma sonda personalizada é enviada ao \<protocolo\>://\<anfitrião\>:\<porta\>\<caminho\>. A porta utilizada será a mesma porta, conforme definido nas definições de HTTP de back-end.

## <a name="nsg-considerations"></a>Considerações de NSG

Se existir um grupo de segurança de rede (NSG) numa sub-rede de gateway de aplicação, intervalos de portas 65503 65534 têm de ser abertos na sub-rede de gateway de aplicação para tráfego de entrada. Estas portas são necessárias para o estado de funcionamento do back-end API para trabalhar.

Além disso, conectividade de Internet de saída não pode ser bloqueada e o tráfego de entrada provenientes a etiqueta AzureLoadBalancer têm de ser permitido.

## <a name="next-steps"></a>Passos Seguintes
Após a aprendizagem sobre a monitorização de estado de funcionamento do Gateway de aplicação, pode configurar uma [sonda de estado de funcionamento personalizados](application-gateway-create-probe-portal.md) no portal do Azure ou uma [sonda de estado de funcionamento personalizados](application-gateway-create-probe-ps.md) com o PowerShell e o Azure Resource Manager modelo de implementação.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
