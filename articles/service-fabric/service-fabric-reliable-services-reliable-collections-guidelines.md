---
title: Diretrizes e recomendações para coleções fiáveis no Azure Service Fabric | Documentos da Microsoft
description: Diretrizes e recomendações para utilizar o Service Fabric Reliable Collections
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 12/10/2017
ms.author: aljo
ms.openlocfilehash: ddccb1c8a881cd15ef1c76b755c3bb74b7f3b49d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805761"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Orientações e recomendações para coleções fiáveis no Azure Service Fabric
Esta seção fornece diretrizes para utilizar a Reliable State Manager e das Reliable Collections. O objetivo é ajudar os utilizadores a evitar as armadilhas comuns.

As diretrizes são organizadas como recomendações simples com os termos de prefixo *fazer*, *considere*, *Evite* e *não*.

* Não modifique um objeto do tipo personalizado retornado por operações de leitura (por exemplo, `TryPeekAsync` ou `TryGetValueAsync`). As coleções fiáveis, tal como coleções em simultâneo, devolvem uma referência para os objetos e não uma cópia.
* Fazer a cópia em profundidade o objeto retornado de um tipo personalizado antes de modificá-lo. Uma vez que as estruturas e tipos internos são pass por valor, não é necessário fazer uma cópia em profundidade neles, a menos que eles contêm campos de tipos de referência ou propriedades que pretende modificar.
* Não utilize `TimeSpan.MaxValue` para tempos limite. Tempos limite deve ser usado para detectar deadlocks.
* Não utilize uma transação depois foi consolidada, abortada ou eliminado.
* Não utilize uma enumeração fora do escopo de transação que foi criado numa.
* Não crie uma transação na outra transação `using` instrução porque pode causar deadlocks.
* Certifique-se de que sua `IComparable<TKey>` implementação está correta. O sistema demora dependência `IComparable<TKey>` para mesclar os pontos de verificação e linhas.
* Utilize o bloqueio de atualização durante a leitura de um item com uma intenção atualizá-la para impedir que uma determinada classe de deadlocks.
* Considere o número de manutenção de coleções fiáveis por partição seja inferior a 1000. Prefira coleções fiáveis com mais itens em mais coleções fiáveis com menos itens.
* Considere a manter seus itens (por exemplo, TKey + TValue para dicionário fiável) de mensagens em fila abaixo 80 KBytes: menor melhores. Isso reduz a quantidade de requisitos de e/s de utilização, bem como o disco e rede Large Object Heap. Muitas vezes, isso reduz a replicação de dados duplicados quando está a ser atualizada apenas uma pequena parte do valor. É uma forma comum de alcançar isto no dicionário fiável, entrar suas linhas várias linhas.
* Considere utilizar a cópia de segurança e restaurar a funcionalidade para que a recuperação após desastre.
* Evite misturar as operações de entidade única e operações de multientidade (por exemplo `GetCountAsync`, `CreateEnumerableAsync`) na mesma transação devido aos níveis de isolamento diferentes.
* Processe a InvalidOperationException. Transações de utilizador podem ser anuladas pelo sistema para diversas razões. Por exemplo, quando o Reliable State Manager está mudando sua função fora de principal ou quando uma transação de longa execução está a bloquear truncagem do registo transacional. Nesses casos, o utilizador poderá receber InvalidOperationException com a indicação de que sua transação já foi terminada. Supondo que, a finalização da transação não foi pedida pelo usuário, melhor forma de lidar com essa exceção é descartar a transação, verifique se o token de cancelamento tiver sido assinalado (ou a função da réplica foi alterada) e se não criar um novo a transação e tente novamente.  

Seguem-se alguns aspetos a ter em mente:

* O tempo limite predefinido é de quatro segundos para todas as APIs de coleção fiável. A maioria dos usuários devem usar o tempo limite predefinido.
* O token de cancelamento de padrão é `CancellationToken.None` em todas as APIs de coleções fiáveis.
* O parâmetro de tipo de chave (*TKey*) para um dicionário fiável tem de implementar corretamente `GetHashCode()` e `Equals()`. As chaves devem ser imutáveis.
* Para assegurar elevada disponibilidade para coleções fiáveis, cada serviço deve ter, pelo menos, um destino e de réplica mínima, defina o tamanho de 3.
* Operações de leitura secundário podem ler versões não consolidada de quórum.
  Isso significa que uma versão de dados que são lidos a partir de um secundário único pode ser progrediu false.
  Leituras de principal são sempre estáveis: pode nunca ser false progrediu.
* Segurança/privacidade dos dados mantidas pela sua aplicação numa coleção fiável é sua decisão e o assunto para proteções fornecidas pelo seu gerenciamento de armazenamento; OU SEJA Encriptação de disco do sistema operativo pode ser utilizada para proteger os seus dados em repouso.  

### <a name="next-steps"></a>Passos Seguintes
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transações e bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gestão de dados
  * [Cópia de Segurança e Restauro](service-fabric-reliable-services-backup-restore.md)
  * [Notificações](service-fabric-reliable-services-notifications.md)
  * [Serialização e a atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Configuração do Reliable State Manager](service-fabric-reliable-services-configuration.md)
* Outros
  * [Início rápido de serviços fiável](service-fabric-reliable-services-quick-start.md)
  * [Referência do desenvolvedor do Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
