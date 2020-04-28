---
title: Diagnosticar erros de pacote de código comum utilizando o Tecido de Serviço
description: Saiba como resolver erros de pacote de código comum com tecido de serviço Azure
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 344fef70522240da2236a020c96308c472c9c545
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75463108"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Diagnosticar erros de pacote de código comum utilizando o Tecido de Serviço

Este artigo descreve o que significa para um pacote de código terminar inesperadamente. Fornece informações sobre possíveis causas de códigos de erro comuns, juntamente com passos de resolução de problemas.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Quando termina um processo ou um contentor inesperadamente?

Quando a Azure Service Fabric recebe um pedido para iniciar um pacote de código, começa a preparar o ambiente no sistema local de acordo com as opções definidas nos manifestos app e serviço. Estas preparações podem incluir reservar pontos finais ou recursos de rede, configurar regras de firewall ou estabelecer restrições de governação de recursos. 

Depois de o ambiente ter sido configurado corretamente, o Service Fabric tenta trazer o pacote de código. Este passo é considerado bem sucedido se o sistema de execução do SISTEMA ou do contentor reportar que o processo ou o contentor foi ativado com sucesso. Se a ativação não for bem sucedida, deve ver uma mensagem de saúde no SFX que se assemelha ao seguinte:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Depois de o pacote de código ter sido ativado com sucesso, o Service Fabric começa a monitorizar a sua vida útil. Neste ponto, um processo ou recipiente pode terminar a qualquer momento por uma série de razões. Por exemplo, pode não ter conseguido inicializar um DLL, ou o SISTEMA poderia ter ficado sem espaço de pilha de ambiente de trabalho. Se o seu pacote de código for encerrado, deve ver a seguinte mensagem de saúde no SFX:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

O código de saída nesta mensagem de saúde é a única pista de que o processo ou o contentor fornece sobre o porquê de ter terminado. Pode ser gerado por qualquer nível da pilha. Por exemplo, este código de saída pode estar relacionado com um erro de Os ou com um problema .NET, ou pode ter sido levantado pelo seu código. Use este artigo como ponto de partida para o diagnóstico De fonte de códigos de saída de rescisão e possíveis soluções. Mas lembre-se que estas são soluções gerais para cenários comuns e podem não se aplicar ao erro que está a ver.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Como posso saber se o Tecido de Serviço terminou o meu pacote de código?

O Service Fabric pode ser responsável por terminar o seu pacote de código por uma variedade de razões. Por exemplo, pode decidir colocar o pacote de código noutro nó para fins de equilíbrio de carga. Pode verificar se o Tecido de Serviço terminou o seu pacote de código se vir algum dos códigos de saída na tabela seguinte.

>[!NOTE]
> Se o seu processo ou contentor terminar com um código de saída diferente dos códigos na tabela seguinte, o Tecido de Serviço não é responsável pela sua cessação.

Código de saída | Descrição
--------- | -----------
7147 | Indica que o Tecido de Serviço desliga graciosamente o processo ou o recipiente enviando-lhe um sinal CTRL+C.
7148 | Indica que o Tecido de Serviço encerrou o processo ou o recipiente. Por vezes, este código de erro indica que o processo ou o recipiente não responderam atempadamente após o envio de um sinal CTRL+C, e tiveram de ser encerrados.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Outros códigos de erro comuns e as suas correções potenciais

Código de saída | Valor hexadecimal | Descrição curta | Causa raiz | Correção potencial
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Este erro, por vezes, significa que a máquina está sem espaço para o ambiente de trabalho. Esta causa é especialmente provável se tiver inúmeros processos que pertencem à sua aplicação em funcionamento no nó. | Se o seu programa não foi construído para responder aos sinais CTRL+C, pode ativar a definição **EnableActivateNoWindow** no manifesto cluster. Ativar esta definição significa que o seu pacote de código funcionará sem uma janela GUI e não receberá sinais Ctrl+C. Esta ação também reduz a quantidade de espaço de pilha de ambiente de trabalho que cada processo consome. Se o seu pacote de código precisar de receber sinais Ctrl+C, pode aumentar o tamanho da pilha de ambiente de trabalho do seu nó.
3762504530 | 0xe0434352 | N/D | Este valor representa o código de erro para uma exceção não manipulada do código gerido (isto é, .NET). | Este código de saída indica que a sua aplicação levantou uma exceção que permanece destratada e que terminou o processo. Como primeiro passo para determinar o que desencadeou este erro, desinvista os registos da sua aplicação e despeje ficheiros.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o diagnóstico de [outros cenários comuns.](service-fabric-diagnostics-common-scenarios.md)
* Obtenha uma visão mais detalhada dos registos do Monitor Azure e do que oferecem através da leitura da [visão geral do Monitor Azure](../operations-management-suite/operations-management-suite-overview.md).
* Saiba mais sobre os registos do Monitor Azure [alertando](../log-analytics/log-analytics-alerts.md) para ajuda na deteção e diagnóstico.
* Conheça as funcionalidades de [pesquisa de registo e consulta](../log-analytics/log-analytics-log-searches.md) oferecidas como parte dos registos do Monitor Azure.
