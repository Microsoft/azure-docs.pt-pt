---
title: Diagnosticar erros comuns do pacote de código usando o Tecido de Serviço
description: Saiba como resolver erros comuns do pacote de código com o Azure Service Fabric
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 400651b240f0518a85b9deb7a7293a77a88b0861
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591701"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Diagnosticar erros comuns do pacote de código usando o Tecido de Serviço

Este artigo descreve o que significa para um pacote de código terminar inesperadamente. Fornece informações sobre possíveis causas de códigos de erro comuns, juntamente com etapas de resolução de problemas.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Quando é que um processo ou contentor termina inesperadamente?

Quando a Azure Service Fabric recebe um pedido de início de um pacote de código, começa a preparar o ambiente no sistema local de acordo com as opções definidas nos manifestos da App e Serviço. Estes preparativos podem incluir a reserva de pontos finais ou recursos da rede, a configuração das regras de firewall ou a criação de restrições de governação de recursos. 

Depois de o ambiente ter sido configurado corretamente, o Service Fabric tenta trazer o pacote de código. Este passo é considerado bem sucedido se o sistema operativo ou o tempo de funcionamento do contentor informassem que o processo ou o contentor foi ativado com sucesso. Se a ativação não for bem sucedida, deverá ver uma mensagem de saúde no SFX que se assemelha ao seguinte:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Depois de o pacote de código ter sido ativado com sucesso, o Service Fabric começa a monitorizar a sua vida útil. Neste momento, um processo ou recipiente pode terminar a qualquer momento por uma série de razões. Por exemplo, pode ter falhado em inicializar um DLL, ou o SO poderia ter ficado sem espaço de amontoados de ambiente de trabalho. Se o seu pacote de código terminar, deverá ver a seguinte mensagem de saúde em SFX:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

O código de saída desta mensagem de saúde é a única pista que o processo ou recipiente fornece sobre o porquê de ter terminado. Pode ser gerado por qualquer nível da pilha. Por exemplo, este código de saída pode estar relacionado com um erro de SO ou um problema .NET, ou pode ter sido levantado pelo seu código. Utilize este artigo como ponto de partida para diagnosticar a fonte de códigos de saída de rescisão e possíveis soluções. Mas lembre-se que estas são soluções gerais para cenários comuns e podem não se aplicar ao erro que está a ver.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Como posso saber se o Service Fabric terminou o meu pacote de código?

A Service Fabric poderá ser responsável por terminar o seu pacote de códigos por diversas razões. Por exemplo, pode decidir colocar o pacote de código noutro nó para efeitos de equilíbrio de carga. Pode verificar se o Service Fabric encerrou o seu pacote de código se vir algum dos códigos de saída na tabela seguinte.

>[!NOTE]
> Se o seu processo ou recipiente terminar com um código de saída diferente dos códigos da tabela seguinte, a Service Fabric não é responsável por terminá-lo.

Código de saída | Description
--------- | -----------
7147 | Indica que o Tecido de Serviço desliga graciosamente o processo ou o recipiente enviando-lhe um sinal Ctrl+C.
7148 | Indica que o Tecido de Serviço encerrou o processo ou o contentor. Por vezes, este código de erro indica que o processo ou o contentor não respondeu em tempo útil após o envio de um sinal Ctrl+C, e teve de ser encerrado.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Outros códigos de erro comuns e as suas correções potenciais

Código de saída | Valor hexadecimal | Breve descrição | Causa raiz | Correção potencial
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Este erro, por vezes, significa que a máquina esgotou o espaço do monte de ambiente de trabalho. Esta causa é especialmente provável se tiver inúmeros processos que pertencem à sua aplicação em execução no nó. | Se o seu programa não foi construído para responder aos sinais Ctrl+C, pode ativar a definição **EnableActivateNoWindow** no manifesto Cluster. Ativar esta definição significa que o seu pacote de código funcionará sem uma janela GUI e não receberá sinais Ctrl+C. Esta ação também reduz a quantidade de espaço de pilha de ambiente de trabalho que cada processo consome. Se o seu pacote de código precisar de receber sinais Ctrl+C, pode aumentar o tamanho da pilha de ambiente de trabalho do seu nó.
3762504530 | 0xe0434352 | N/D | Este valor representa o código de erro para uma exceção não manipulada do código gerido (isto é, .NET). | Este código de saída indica que a sua aplicação levantou uma exceção que permanece desacomprugada e que terminou o processo. Como primeiro passo para determinar o que desencadeou este erro, depurar os registos da sua aplicação e desi depositar ficheiros.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o diagnóstico de outros cenários comuns.](service-fabric-diagnostics-common-scenarios.md)
* Obtenha uma visão geral mais detalhada dos registos do Azure Monitor e do que oferecem lendo [a visão geral do Azure Monitor](../azure-monitor/overview.md).
* Saiba mais sobre os registos do Azure Monitor [alertando](../azure-monitor/alerts/alerts-overview.md) para ajuda na deteção e diagnóstico.
* Familiarize-se com as funcionalidades de pesquisa e consulta de [registos](../azure-monitor/logs/log-query-overview.md) oferecidas como parte dos registos do Azure Monitor.
