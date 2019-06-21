---
title: O Azure Service Fabric diagnosticar erros de pacote de código | Documentos da Microsoft
description: Saiba como resolver erros comuns de pacote de código com o Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: grzuber
manager: gkhanna
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 235952388d2c044cc141b3020c67944c4250ea3d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276954"
---
# <a name="diagnose-common-code-package-errors-with-service-fabric"></a>Diagnosticar erros comuns de pacote de código com o Service Fabric

Este artigo ilustra o que significa para um pacote do código terminar inesperadamente e fornece informações sobre as causas possíveis de códigos de erro comuns, bem como passos de resolução de problemas que potencialmente podem mitigar o problema.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Quando um processo ou contentor encerrar inesperadamente?

Quando o Service Fabric recebe um pedido para iniciar um pacote do código, ele começa a preparação do ambiente do sistema local com base nas opções de configuração definidas na aplicação e manifestos de serviço. Estes preparações podem incluir reservar recursos ou pontos finais de rede, configurar regras de firewall ou configurar restrições de governação de recursos. Assim que o ambiente tiver sido configurado corretamente, o Service Fabric tenta abrir o pacote do código. Este passo é considerado bem-sucedida se o tempo de execução do sistema operacional ou o contentor de relatórios que o processo ou contentor foi ativado com êxito. Se a ativação foi concluída com êxito, deverá ver uma mensagem de estado de funcionamento no SFX que diz

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Assim que o pacote do código tenha sido colocado com sucesso cópia de segurança, o Service Fabric começa a monitorizar o seu ciclo de vida. Neste momento, pode terminar um processo ou contentor em qualquer altura por diversas razões. Poderia ter falhou ao inicializar uma DLL, o sistema operacional pode ter ficado sem espaço de heap de área de trabalho, etc. Se o seu pacote de código terminada, deverá ver uma mensagem de estado de funcionamento no SFX que diz

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

O código de saída fornecido no estado de funcionamento deste mensagem é a única pista fornecida pelo processo/contentor sobre por que motivo terminada, poderia ter sido gerado por qualquer nível da pilha. É difícil de entender se este código de saída foi relacionado, por exemplo, um erro de sistema operacional, um problema no .NET, ou foi gerado pelo seu código. Como resultado, este artigo pode ser utilizado como um ponto de partida para diagnosticar a origem dos códigos de saída de terminação e possíveis soluções, tendo em mente, estes são soluções gerais para cenários comuns e podem não se aplicam ao erro que estiver a ver.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Como posso saber se o Service Fabric terminada meu pacote do código?

Service Fabric pode ser responsável por seu pacote de código para uma variedade de motivos pelos quais a terminar. Por exemplo, pode optar por colocar o pacote de código noutro nó de balanceamento de carga fins. Pode dizer se o seu pacote de código foi terminado pelo Service Fabric, se vir de qualquer um dos códigos de saída na tabela a seguir:

>[!NOTE]
> Se o seu processo/contentor termina com um código de saída que não sejam aqueles na tabela a seguir, o Service Fabric não é responsável por encerrá-lo.

Código de saída | Descrição
--------- | -----------
7147 | Esses códigos de erro indicam que o Service Fabric for encerrada corretamente o processo/contentor, enviando um sinal Ctrl + C.
7148 | Esses códigos de erro indicam que o Service Fabric terminada o processo/contentor. Às vezes, este código de erro pode indicar que o processo/contentor não responder em tempo hábil depois de enviar um sinal Ctrl + C, então ele precisava ser terminadas.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Outros códigos de erro comuns e suas possíveis correções

Código de saída | Valor hexadecimal | Breve descrição | Causa Raiz | Correção potencial
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Este erro pode, potencialmente, significa que a máquina ficou sem espaço de heap de área de trabalho. Esta causa seja especialmente provável se tiver um grande número de processos que pertencem à sua aplicação em execução no nó. | Se o seu programa não foi criado para responder a sinais de Ctrl + C, pode ativar a definição de "EnableActivateNoWindow" no manifesto do Cluster. A ativação desta definição significaria que o pacote de código seria executado sem uma janela de GUI e não receberia sinais de Ctrl + C, mas seria reduzir a quantidade de espaço de heap de área de trabalho, que cada processo consome. Se o seu pacote de código precisa receber sinais de Ctrl + C, em seguida, pode aumentar o tamanho do heap de área de trabalho do seu nó.
3762504530 | 0xe0434352 | N/A | Este valor é o código de erro para uma exceção não processada no código gerenciado (ou seja, .NET). | Se estiver a ver este código de saída, isso significa que seu aplicativo provocou uma exceção que permanecia sem tratamento e o processo de terminada. Depuração de registos e informações do seu aplicativo deve ser o primeiro passo para determinar o que causou o erro.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [diagnosticar outros cenários comuns](service-fabric-diagnostics-common-scenarios.md)
* Obtenha uma visão geral mais detalhada de registos do Azure Monitor e o que ele oferece lendo [o que é o Azure Monitor registos?](../operations-management-suite/operations-management-suite-overview.md)
* Saiba mais sobre os registos do Azure Monitor [alertas](../log-analytics/log-analytics-alerts.md) para ajudar no diagnóstico e de deteção.
* Familiarizar-se com o [pesquisas e consultas de registo](../log-analytics/log-analytics-log-searches.md) funcionalidades oferecidos como parte dos registos do Azure Monitor
