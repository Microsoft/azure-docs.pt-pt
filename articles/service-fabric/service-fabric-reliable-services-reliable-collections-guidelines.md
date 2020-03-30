---
title: Diretrizes para Coleções Fiáveis
description: Diretrizes e Recomendações para a utilização de Coleções Fiáveis de Tecido de Serviço numa aplicação Azure Service Fabric.
ms.topic: conceptual
ms.date: 12/10/2017
ms.openlocfilehash: 37c734205877f9e0cb98ef2834462691e8e483d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645485"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Diretrizes e recomendações para Coleções Fiáveis em Tecido de Serviço Azure
Esta secção fornece orientações para a utilização de Reliable State Manager e Reliable Collections. O objetivo é ajudar os utilizadores a evitar armadilhas comuns.

As diretrizes são organizadas como recomendações simples pré-fixadas com os termos *Fazer*, *Considerar,* *Evitar* e *Não*.

* Não modifique um objeto de tipo personalizado devolvido `TryPeekAsync` `TryGetValueAsync`por operações de leitura (por exemplo, ou ). Coleções fiáveis, tal como coleções simultâneas, devolvem uma referência aos objetos e não uma cópia.
* Copie profundamente o objeto devolvido de um tipo personalizado antes de o modificar. Uma vez que as estruturas e os tipos incorporados são pass-by-value, não precisa de fazer uma cópia profunda sobre eles, a menos que contenham campos ou propriedades dactilografadas de referência que pretende modificar.
* Não utilize `TimeSpan.MaxValue` para intervalos. Os intervalos devem ser usados para detetar impasses.
* Não utilize uma transação depois de ter sido cometida, abortada ou eliminada.
* Não utilize uma enumeração fora do âmbito de transação em que foi criado.
* Não crie uma transação `using` dentro da declaração de outra transação porque pode causar impasses.
* Não crie um `IReliableStateManager.GetOrAddAsync` estado fiável e utilize o estado de confiança na mesma transação. Isto resulta numa InvalidaOperationException.
* Certifique-se `IComparable<TKey>` de que a sua implementação está correta. O sistema assume `IComparable<TKey>` a dependência da fusão de postos de controlo e linhas.
* Utilize o bloqueio de atualização ao ler um item com a intenção de atualizá-lo para evitar uma determinada classe de impasses.
* Considere manter o número de Coleções Fiáveis por partição inferior a 1000. Prefira Coleções Fiáveis com mais itens em mais Coleções Fiáveis com menos itens.
* Considere manter os seus itens (por exemplo, TKey + TValue for Reliable Dictionary) abaixo de 80 KBytes: menor, melhor. Isto reduz a quantidade de utilização do Large Object Heap, bem como os requisitos de io do disco e da rede. Muitas vezes, reduz a replicação de dados duplicados quando apenas uma pequena parte do valor está a ser atualizada. A forma comum de o conseguir no Dicionário Fiável é quebrar as suas fileiras em várias linhas.
* Considere usar a funcionalidade de backup e restaurar a funcionalidade para ter recuperação de desastres.
* Evite misturar operações de entidade única e `GetCountAsync` `CreateEnumerableAsync`operações multi-entidades (por exemplo, ) na mesma transação devido aos diferentes níveis de isolamento.
* Manuseie a InvalidOperationException. As transações de utilizadores podem ser abortadas pelo sistema por diversas razões. Por exemplo, quando o Gestor de Estado Fiável está a mudar o seu papel fora da Primary ou quando uma transação de longo prazo está a bloquear a truncação do registo transacional. Nesses casos, o utilizador pode receber InvalidOperationException indicando que a sua transação já foi encerrada. Assumindo que a rescisão da transação não foi solicitada pelo utilizador, a melhor forma de lidar com esta exceção é eliminar a transação, verificar se o sinal de cancelamento foi sinalizado (ou se o papel da réplica foi alterado), e se não criar um novo transação e novatentativa.  

Eis algumas coisas a ter em mente:

* O tempo de paragem padrão é de quatro segundos para todas as APIs de recolha fiável. A maioria dos utilizadores deve utilizar o tempo de paragem padrão.
* O símbolo de `CancellationToken.None` cancelamento padrão está em todas as APIs de Coleções Fiáveis.
* O parâmetro do tipo chave *(TKey)* para um `GetHashCode()` `Equals()`Dicionário Fiável deve ser corretamente implementado e . As chaves devem ser imutáveis.
* Para obter uma elevada disponibilidade para as Coleções Fiáveis, cada serviço deve ter pelo menos um alvo e um tamanho de réplica mínimo de 3.
* Ler as operações no secundário pode ler versões que não sejam comprometidas com quórum.
  Isto significa que uma versão de dados que é lida a partir de um único secundário pode ser falsamente progredida.
  As leituras das Primárias são sempre estáveis: nunca podem ser falsas progredidas.
* A segurança/privacidade dos dados persistidos pela sua aplicação numa recolha fiável é a sua decisão e está sujeita às proteções fornecidas pela sua gestão de armazenamento; I.E. A encriptação do disco do Sistema Operativo pode ser usada para proteger os seus dados em repouso.  

### <a name="next-steps"></a>Passos seguintes
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transações e Fechaduras](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gestão de Dados
  * [Backup e Restauro](service-fabric-reliable-services-backup-restore.md)
  * [Notificações](service-fabric-reliable-services-notifications.md)
  * [Serialização e Upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Configuração fiável do Gestor do Estado](service-fabric-reliable-services-configuration.md)
* Outros
  * [Início rápido dos Serviços Fiáveis](service-fabric-reliable-services-quick-start.md)
  * [Referência do desenvolvedor para Coleções Fiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
