---
title: Boas práticas para operações de longo prazo nos Serviços de Análise do Azure [ Microsoft Docs
description: Este artigo descreve as melhores práticas para operações de longo prazo.
author: minewiskan
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 3f6b2194cc422a827bbc7a15c012173b3f814b52
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428111"
---
# <a name="best-practices-for-long-running-operations"></a>Boas práticas para operações de longo prazo

Nos Serviços de Análise Azure, um *nó* representa uma máquina virtual hospedeira onde um recurso de servidor está em execução. Algumas operações, tais como consultas de longa duração, operações de atualização e sincronização de escala de consulta podem falhar se um recurso do servidor se mover para um nó diferente. As mensagens de erro comuns neste cenário incluem:

- "Ocorreu um erro enquanto tentava localizar um pedido de XMLA de longa data. O pedido pode ter sido interrompido por atualização do serviço ou reinício do servidor."
- "Job with<guid>ID '<database>for model ' ' foi cancelado devido a erro de serviço (inatividade) com mensagem 'Cancelando o pedido de atualização uma vez que ficou preso sem qualquer atualização. Esta é uma questão de serviço interno. Por favor, reenvie o trabalho ou preencha um bilhete para obter ajuda se este assunto acontecer repetidamente."

Há muitas razões pelas quais as operações de longo prazo podem ser interrompidas. Por exemplo, atualizações em Azure tais como: 
- Patches do Sistema Operativo 
- Atualizações de segurança
- Atualizações de serviços de serviços de análise azure
- Atualizações de tecido de serviço. Service Fabric é um componente de plataforma utilizado por vários serviços na nuvem da Microsoft, incluindo os Serviços de Análise Azure.

Além das atualizações que ocorrem no serviço, há um movimento natural de serviços em nós devido ao equilíbrio de carga. Os movimentos do nó são uma parte esperada de um serviço na nuvem. Os Serviços de Análise Azure tentam minimizar os impactos dos movimentos do nó, mas é impossível eliminá-los totalmente. 

Além dos movimentos do nó, há outras falhas que podem ocorrer. Por exemplo, um sistema de base de dados de fonte de dados de dados pode estar offline ou a conectividade da rede é perdida. Se durante a atualização, uma partição tiver 10 milhões de linhas e ocorrer uma falha na 9 milionésima linha, não há como reiniciar a atualização no ponto de falha. O serviço tem que começar de novo desde o início. 

## <a name="refresh-rest-api"></a>Atualizar a API REST

As interrupções de serviço podem ser desafiantes para operações de longo curso, como a atualização de dados. Os Serviços de Análise Azure incluem uma API REST para ajudar a mitigar os impactos negativos das interrupções do serviço. Para saber mais, consulte [atualização assíncrona com a API REST](analysis-services-async-refresh.md).
 
Além da API REST, existem outras abordagens que pode utilizar para minimizar potenciais problemas durante operações de atualização de longa duração. O objetivo principal é evitar ter de reiniciar a operação de atualização desde o início e, em vez disso, realizar refrescos em lotes menores que podem ser cometidos por etapas. 
 
A API REST permite tal reinício, mas não permite a plena flexibilidade da criação e eliminação da partilha. Se um cenário requer operações complexas de gestão de dados, a sua solução deve incluir alguma forma de loteamento na sua lógica. Por exemplo, ao utilizar transações para processar dados em vários lotes separados permite que uma falha não exija o reinício desde o início, mas sim a partir de um ponto de verificação intermédio. 
 
## <a name="scale-out-query-replicas"></a>Réplicas de consulta de escala

Seja usando o REST ou a lógica personalizada, as consultas de aplicação do cliente ainda podem devolver resultados inconsistentes ou intermédios enquanto os lotes estão sendo processados. Se forem necessários dados consistentes devolvidos por consultas de aplicação do cliente durante o processamento, e os dados do modelo estiverem num estado intermédio, pode utilizar a [escala com](analysis-services-scale-out.md) réplicas de consulta apenas de leitura.

Ao utilizar réplicas de consulta sonora, enquanto as atualizações estão a ser realizadas em lotes, os utilizadores de aplicações de clientes podem continuar a consultar a imagem antiga dos dados sobre as réplicas apenas para leitura. Uma vez terminadas as atualizações, uma operação Synch pode ser executada para atualizar as réplicas apenas para leitura.


## <a name="next-steps"></a>Passos seguintes

[Atualização assíncrona com a API REST](analysis-services-async-refresh.md)  
[Escalamento horizontal do Azure Analysis Services](analysis-services-scale-out.md)  
[Serviços de Análise alta disponibilidade](analysis-services-bcdr.md)  
[Orientação de retry para os serviços Azure](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   

