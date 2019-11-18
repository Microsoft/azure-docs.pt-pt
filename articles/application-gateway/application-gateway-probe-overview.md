---
title: Visão geral do monitoramento de integridade para Aplicativo Azure gateway
description: Aplicativo Azure gateway monitora a integridade de todos os recursos em seu pool de back-end e remove automaticamente qualquer recurso considerado não íntegro do pool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: 2938665aa0c0a3df66b6ddcfd1c8c5fbc4598319
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130678"
---
# <a name="application-gateway-health-monitoring-overview"></a>Visão geral do monitoramento de integridade do gateway de aplicativo

O gateway de Aplicativo Azure, por padrão, monitora a integridade de todos os recursos em seu pool de back-ends e remove automaticamente qualquer recurso considerado não íntegro do pool. O gateway de aplicativo continua monitorando as instâncias não íntegras e as adiciona de volta ao pool de back-end íntegro quando elas ficam disponíveis e respondem a investigações de integridade. O gateway de aplicativo envia as investigações de integridade com a mesma porta que é definida nas configurações de HTTP de back-end. Essa configuração garante que a investigação esteja testando a mesma porta que os clientes usam para se conectar ao back-end.

![exemplo de investigação do gateway de aplicativo][1]

Além de usar o monitoramento de investigação de integridade padrão, você também pode personalizar a investigação de integridade para atender aos requisitos do seu aplicativo. Neste artigo, as investigações de integridade padrão e personalizada são cobertas.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Investigação de integridade padrão

Um gateway de aplicativo configura automaticamente uma investigação de integridade padrão quando você não define nenhuma configuração de investigação personalizada. O comportamento de monitoramento funciona fazendo uma solicitação HTTP para os endereços IP configurados para o pool de back-ends. Para investigações padrão se as configurações de http de back-end forem configuradas para HTTPS, a investigação usará HTTPS também para testar a integridade dos back-ends.

Por exemplo: você configura o gateway de aplicativo para usar os servidores back-end a, B e C para receber o tráfego de rede HTTP na porta 80. O monitoramento de integridade padrão testa os três servidores a cada 30 segundos para uma resposta HTTP íntegra. Uma resposta HTTP íntegra tem um [código de status](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 e 399.

Se a verificação de investigação padrão falhar para o servidor A, o gateway de aplicativo a removerá de seu pool de back-end e o tráfego de rede parará de fluir para esse servidor. A investigação padrão ainda continua a verificar o servidor A cada 30 segundos. Quando o servidor A responde com êxito a uma solicitação de uma investigação de integridade padrão, ele é adicionado de volta como íntegro para o pool de back-end e o tráfego começa a fluir para o servidor novamente.

### <a name="probe-matching"></a>Correspondência de investigação

Por padrão, uma resposta HTTP (S) com código de status entre 200 e 399 é considerada íntegra. As investigações de integridade personalizadas também dão suporte a dois critérios de correspondência. Os critérios de correspondência podem ser usados para modificar opcionalmente a interpretação padrão do que constitui uma resposta íntegra.

Estes são os critérios correspondentes: 

- **Correspondência de código de status de resposta http** – critério de correspondência de investigação para aceitar o código de resposta HTTP especificado pelo usuário ou intervalos de códigos de resposta. Códigos de status de resposta separados por vírgulas individuais ou um intervalo de códigos de status têm suporte.
- **Correspondência de corpo de resposta http** – critério de correspondência de investigação que examina o corpo da resposta http e corresponde a uma cadeia de caracteres especificada pelo usuário. A correspondência só procura a presença da cadeia de caracteres especificada pelo usuário no corpo da resposta e não é uma correspondência de expressão regular completa.

Os critérios de correspondência podem ser especificados usando o cmdlet `New-AzApplicationGatewayProbeHealthResponseMatch`.

Por exemplo:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Depois que os critérios de correspondência forem especificados, eles poderão ser anexados à configuração de investigação usando um parâmetro `-Match` no PowerShell.

### <a name="default-health-probe-settings"></a>Configurações de investigação de integridade padrão

| Propriedade de investigação | Valor | Descrição |
| --- | --- | --- |
| URL de investigação |http://127.0.0.1:\<port\>/ |Caminho da URL |
| Intervalo |30 |A quantidade de tempo em segundos a aguardar antes que a próxima investigação de integridade seja enviada.|
| Tempo limite |30 |A quantidade de tempo em segundos que o gateway de aplicativo aguarda uma resposta de investigação antes de marcar a investigação como não íntegra. Se uma investigação retornar como íntegra, o back-end correspondente será imediatamente marcado como íntegro.|
| Limite não íntegro |3 |Governa a quantidade de investigações a serem enviadas caso haja uma falha na investigação de integridade regular. Essas investigações de integridade adicionais são enviadas em sucessão rápida para determinar a integridade do back-end rapidamente e não aguardam o intervalo de investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite não íntegro. |

> [!NOTE]
> A porta é a mesma porta que as configurações de HTTP de back-end.

A investigação padrão se parece apenas com http:\//127.0.0.1:\<porta\> para determinar o status de integridade. Se você precisar configurar a investigação de integridade para ir para uma URL personalizada ou modificar qualquer outra configuração, deverá usar investigações personalizadas.

### <a name="probe-intervals"></a>Intervalos de investigação

Todas as instâncias do gateway de aplicativo investigam o back-end independente um do outro. A mesma configuração de investigação se aplica a cada instância do gateway de aplicativo. Por exemplo, se a configuração de investigação for enviar investigações de integridade a cada 30 segundos e o gateway de aplicativo tiver duas instâncias, ambas as instâncias enviarão a investigação de integridade a cada 30 segundos.

Além disso, se houver vários ouvintes, cada ouvinte investigará o back-end independentemente um do outro. Por exemplo, se houver dois ouvintes apontando para o mesmo pool de back-end em duas portas diferentes (configuradas por duas configurações de http de back-end), cada ouvinte investigará o mesmo back-end independentemente. Nesse caso, há duas investigações de cada instância do gateway de aplicativo para os dois ouvintes. Se houver duas instâncias do gateway de aplicativo nesse cenário, a máquina virtual de back-end veria quatro investigações de acordo com o intervalo de investigação configurado.

## <a name="custom-health-probe"></a>Investigação de integridade personalizada

As investigações personalizadas permitem que você tenha um controle mais granular sobre o monitoramento de integridade. Ao usar investigações personalizadas, você pode configurar o intervalo de investigação, a URL e o caminho para teste e quantas respostas com falha aceitar antes de marcar a instância do pool de back-end como não íntegra.

### <a name="custom-health-probe-settings"></a>Configurações de investigação de integridade personalizadas

A tabela a seguir fornece definições para as propriedades de uma investigação de integridade personalizada.

| Propriedade de investigação | Descrição |
| --- | --- |
| Nome |Nome da investigação. Esse nome é usado para fazer referência à investigação nas configurações de HTTP de back-end. |
| Protocolo |Protocolo usado para enviar a investigação. A investigação usa o protocolo definido nas configurações de HTTP de back-end |
| Anfitrião |Nome do host para enviar a investigação. Aplicável somente quando multissite está configurado no gateway de aplicativo; caso contrário, use ' 127.0.0.1 '. Esse valor é diferente do nome de host da VM. |
| Caminho |Caminho relativo da investigação. O caminho válido começa com '/'. |
| Intervalo |Intervalo de investigação em segundos. Esse valor é o intervalo de tempo entre duas investigações consecutivas. |
| Tempo limite |Tempo limite de investigação em segundos. Se uma resposta válida não for recebida nesse período de tempo limite, a investigação será marcada como com falha.  |
| Limite não íntegro |Contagem de repetição de investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite não íntegro. |

> [!IMPORTANT]
> Se o gateway de aplicativo estiver configurado para um único site, por padrão, o nome do host deverá ser especificado como ' 127.0.0.1 ', a menos que configurado de outra forma na investigação personalizada.
> Para referência, uma investigação personalizada é enviada para \<protocolo\>://\<host\>:\<porta\>\<caminho\>. A porta usada será a mesma porta definida nas configurações de HTTP de back-end.

## <a name="nsg-considerations"></a>Considerações sobre o NSG

Se houver um NSG (grupo de segurança de rede) em uma sub-rede de gateway de aplicativo, os intervalos de porta 65503-65534 devem ser abertos na sub-rede do gateway de aplicativo para o tráfego de entrada. Essas portas são necessárias para que a API de integridade de back-end funcione.

Além disso, a conectividade de Internet de saída não pode ser bloqueada e o tráfego de entrada proveniente da marca AzureLoadBalancer deve ser permitido.

## <a name="next-steps"></a>Passos seguintes
Depois de aprender sobre o monitoramento de integridade do gateway de aplicativo, você pode configurar uma [investigação de integridade personalizada](application-gateway-create-probe-portal.md) no portal do Azure ou uma [investigação de integridade personalizada](application-gateway-create-probe-ps.md) usando o PowerShell e o modelo de implantação de Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
