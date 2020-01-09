---
title: Diretrizes para coleções confiáveis
description: Diretrizes e recomendações para usar Service Fabric coleções confiáveis em um aplicativo de Service Fabric do Azure.
ms.topic: conceptual
ms.date: 12/10/2017
ms.openlocfilehash: 37c734205877f9e0cb98ef2834462691e8e483d9
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645485"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Diretrizes e recomendações para coleções confiáveis no Azure Service Fabric
Esta seção fornece diretrizes para usar o Gerenciador de estado confiável e as coleções confiáveis. O objetivo é ajudar os usuários a evitar armadilhas comuns.

As diretrizes são organizadas como recomendações simples prefixadas com *os termos,* *considere*, *Evite* *e não*.

* Não modifique um objeto do tipo personalizado retornado por operações de leitura (por exemplo, `TryPeekAsync` ou `TryGetValueAsync`). As coleções confiáveis, assim como as coleções simultâneas, retornam uma referência aos objetos e não uma cópia.
* Faça uma cópia profunda do objeto retornado de um tipo personalizado antes de modificá-lo. Como structs e tipos internos são passagem por valor, você não precisa fazer uma cópia profunda neles, a menos que eles contenham campos ou propriedades de tipo de referência que você pretende modificar.
* Não use `TimeSpan.MaxValue` para tempos limite. Os tempos limite devem ser usados para detectar deadlocks.
* Não use uma transação depois que ela tiver sido confirmada, anulada ou descartada.
* Não use uma enumeração fora do escopo da transação em que foi criada.
* Não crie uma transação dentro da instrução de `using` de outra transação porque ela pode causar deadlocks.
* Não crie um estado confiável com `IReliableStateManager.GetOrAddAsync` e use o estado confiável na mesma transação. Isso resulta em uma InvalidOperationException.
* Certifique-se de que sua implementação de `IComparable<TKey>` está correta. O sistema assume a dependência de `IComparable<TKey>` para mesclar pontos de verificação e linhas.
* Use o bloqueio de atualização ao ler um item com uma intenção de atualizá-lo para evitar uma determinada classe de deadlocks.
* Considere manter o número de coleções confiáveis por partição inferior a 1000. Prefira coleções confiáveis com mais itens em coleções mais confiáveis com menos itens.
* Considere manter seus itens (por exemplo, TKey + TValue para dicionário confiável) abaixo de 80 KBytes: menor o melhor. Isso reduz a quantidade de uso de heap de objeto grande, bem como os requisitos de e/s de disco e rede. Geralmente, ele reduz a replicação de dados duplicados quando apenas uma pequena parte do valor está sendo atualizada. Uma maneira comum de conseguir isso no dicionário confiável é dividir as linhas em várias linhas.
* Considere o uso da funcionalidade de backup e restauração para ter recuperação de desastre.
* Evite misturar operações de entidade única e operações de várias entidades (por exemplo, `GetCountAsync`, `CreateEnumerableAsync`) na mesma transação devido aos diferentes níveis de isolamento.
* Lide com InvalidOperationException. As transações do usuário podem ser anuladas pelo sistema por vários motivos. Por exemplo, quando o Gerenciador de estado confiável está alterando sua função de primária ou quando uma transação de execução longa está bloqueando o truncamento do log transacional. Nesses casos, o usuário pode receber InvalidOperationException, indicando que sua transação já foi encerrada. Supondo que o encerramento da transação não foi solicitado pelo usuário, a melhor maneira de lidar com essa exceção é descartar a transação, verificar se o token de cancelamento foi sinalizado (ou a função da réplica foi alterada) e, se não criar um novo transação e tente novamente.  

Aqui estão algumas coisas para ter em mente:

* O tempo limite padrão é de quatro segundos para todas as APIs de coleção confiáveis. A maioria dos usuários deve usar o tempo limite padrão.
* O token de cancelamento padrão é `CancellationToken.None` em todas as APIs de coleções confiáveis.
* O parâmetro de tipo de chave (*TKey*) para um dicionário confiável deve implementar corretamente `GetHashCode()` e `Equals()`. As chaves devem ser imutáveis.
* Para obter alta disponibilidade para as coleções confiáveis, cada serviço deve ter pelo menos um destino e o tamanho mínimo do conjunto de réplicas de 3.
* As operações de leitura no secundário podem ler versões que não são confirmadas de quorum.
  Isso significa que uma versão de dados que é lida de um único secundário pode ser falsa progredida.
  As leituras do primário são sempre estáveis: nunca podem ser falsas progredidas.
* A segurança/privacidade dos dados persistidos pelo seu aplicativo em uma coleção confiável é sua decisão e sujeita às proteções fornecidas pelo gerenciamento de armazenamento; ,. A criptografia de disco do sistema operacional pode ser usada para proteger seus dados em repouso.  

### <a name="next-steps"></a>Passos seguintes
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transações e bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gerenciando dados
  * [Cópia de Segurança e Restauro](service-fabric-reliable-services-backup-restore.md)
  * [Notificações](service-fabric-reliable-services-notifications.md)
  * [Serialização e atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Configuração do Gerenciador de estado confiável](service-fabric-reliable-services-configuration.md)
* Outros
  * [Início rápido de Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Referência do desenvolvedor para coleções confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
