---
title: Orientações para coleções fiáveis
description: Diretrizes e recomendações para a utilização de coleções fiáveis de tecido de serviço numa aplicação de tecido de serviço Azure.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 63e6de436bdaceed7f1d2a78e8385dd14bfc0ed6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260926"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Diretrizes e recomendações para coleções fiáveis em tecido de serviço Azure
Esta secção fornece diretrizes para a utilização de Reliable State Manager e Reliable Collections. O objetivo é ajudar os utilizadores a evitar armadilhas comuns.

As diretrizes são organizadas como recomendações simples prefixadas com os termos *Fazer,* *Considerar,* *Evitar* e *Não.*

* Não modifique um objeto de tipo personalizado devolvido por operações de leitura (por exemplo, `TryPeekAsync` ou `TryGetValueAsync` ). Coleções Fiáveis, tal como Coleções Simultâneas, devolvem uma referência aos objetos e não a uma cópia.
* Copie profundamente o objeto devolvido de um tipo personalizado antes de o modificar. Uma vez que as estruturas e os tipos incorporados são pass-by-value, não é necessário fazer uma cópia profunda sobre eles, a menos que contenham campos ou propriedades dactilografadas de referência que pretende modificar.
* Não utilizar `TimeSpan.MaxValue` para intervalos de tempo. Os intervalos de tempo devem ser utilizados para detetar impasses.
* Não utilize uma transação depois de ter sido comprometida, abortada ou eliminada.
* Não utilize uma enumeração fora do âmbito de transação em que foi criada.
* Não crie uma transação dentro da declaração de outra transação `using` porque pode causar impasses.
* Não crie um estado fiável e `IReliableStateManager.GetOrAddAsync` utilize o estado fiável na mesma transação. Isto resulta numa InvalidOperationException.
* Certifique-se de que a sua `IComparable<TKey>` implementação está correta. O sistema assume dependência `IComparable<TKey>` para a fusão de postos de controlo e linhas.
* Utilize o bloqueio De atualização ao ler um item com a intenção de atualizá-lo para evitar uma determinada classe de impasses.
* Considere manter o número de Coleções Fiáveis por partição inferior a 1000. Prefere Coleções Fiáveis com mais itens em vez de coleções mais fiáveis com menos itens.
* Considere manter os seus itens (por exemplo, TKey + TValue para Dicionário Fiável) abaixo de 80 KBytes: menor quanto melhor. Isto reduz a quantidade de utilização da Heap de Objeto Grande, bem como os requisitos de IO de disco e rede. Muitas vezes, reduz a replicação de dados duplicados quando apenas uma pequena parte do valor está a ser atualizada. A forma comum de o conseguir no Dicionário Fiável é quebrar as suas linhas em várias linhas.
* Considere usar a funcionalidade de backup e restaurar para ter recuperação de desastres.
* Evite misturar operações de entidade única e operações multi-entidades (por `GetCountAsync` exemplo, `CreateEnumerableAsync` ) na mesma transação devido aos diferentes níveis de isolamento.
* Lide com InvalidOperationException. As transações de utilizadores podem ser abortadas pelo sistema por diversas razões. Por exemplo, quando o Gestor de Estado Fiável está a mudar o seu papel fora do Primário ou quando uma transação de longa duração está a bloquear a truncação do registo transacional. Nesses casos, o utilizador pode receber a InvalidOperationException indicando que a sua transação já foi encerrada. Assumindo que a térmia da transação não foi solicitada pelo utilizador, a melhor forma de lidar com esta exceção é alienar a transação, verificar se o token de cancelamento foi sinalizado (ou se o papel da réplica foi alterado), e se não criar uma nova transação e voltar a tentar.  

Eis algumas coisas a ter em mente:

* O tempo de padrão é de quatro segundos para todas as APIs de cobrança fiável. A maioria dos utilizadores deve utilizar o tempo de padrão.
* O token de cancelamento padrão está `CancellationToken.None` em todas as APIs de Cobranças Fidedignas.
* O parâmetro do tipo chave *(TKey)* para um Dicionário Fiável deve implementar corretamente `GetHashCode()` e `Equals()` . As chaves devem ser imutáveis.
* Para obter uma elevada disponibilidade para as Coleções Fiáveis, cada serviço deve ter pelo menos um objetivo e um tamanho definido de réplica mínima de 3.
* Leia as operações no secundário pode ler versões que não são comprometidas pelo quórum.
  Isto significa que uma versão de dados que é lida a partir de um único secundário pode ser falsamente progredida.
  As leituras de Primary são sempre estáveis: nunca podem ser falsas progredidas.
* A segurança/privacidade dos dados persistidos pela sua aplicação numa recolha fiável é a sua decisão e sujeita às proteções fornecidas pela sua gestão de armazenamento; Ou seja, o E. A encriptação do disco do Sistema Operativo pode ser usada para proteger os seus dados em repouso.
* `ReliableDictionary` a enumeração usa uma estrutura de dados ordenada ordenada por chave. Para tornar a enumeração eficiente, os compromissos são adicionados a um haxixe temporário e posteriormente transferidos para o principal posto de controlo da estrutura de dados ordenada. As atualizações/eliminações têm o melhor tempo de funcionaamento de O(1) e, no pior dos casos, o tempo de funcionaamento de O(log n), no caso de verificações de validação da presença da chave. O Gets pode ser O(1) ou O(log n) dependendo se você está lendo de um compromisso recente ou de um compromisso mais antigo.

## <a name="volatile-reliable-collections"></a>Coleções fiáveis voláteis
Ao decidir utilizar coleções voláteis e fiáveis, considere o seguinte:

* ```ReliableDictionary``` tem suporte volátil
* ```ReliableQueue``` tem suporte volátil
* ```ReliableConcurrentQueue``` NÃO tem suporte volátil
* Os serviços persistidoes não podem ser tornados voláteis. Mudar a ```HasPersistedState``` bandeira para exigir recriar todo o serviço do ```false``` zero
* Os serviços voláteis não podem ser persistidos. Mudar a ```HasPersistedState``` bandeira para exigir recriar todo o serviço do ```true``` zero
* ```HasPersistedState``` é um nível de serviço config. Isto significa que **todas as** coleções serão persistidos ou voláteis. Não se pode misturar coleções voláteis e persistiu
* Perda de quórum de uma partição volátil resulta em perda completa de dados
* Backup e restauro NÃO está disponível para serviços voláteis

## <a name="next-steps"></a>Passos seguintes
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transações e Fechaduras](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gestão de Dados
  * [Backup e Restauro](service-fabric-reliable-services-backup-restore.md)
  * [Notificações](service-fabric-reliable-services-notifications.md)
  * [Serialização e Atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Configuração fiável do Gestor de Estado](service-fabric-reliable-services-configuration.md)
* Outros
  * [Arranque rápido de serviços fiáveis](service-fabric-reliable-services-quick-start.md)
  * [Referência do programador para Coleções Fiáveis](/dotnet/api/microsoft.servicefabric.data.collections?view=azure-dotnet#microsoft_servicefabric_data_collections)
