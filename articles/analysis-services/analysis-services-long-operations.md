---
title: Melhores práticas para operações de longa duração nos Serviços de Análise Azure | Microsoft Docs
description: Este artigo descreve as melhores práticas para operações de longa duração.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 4e069effae0cb7f834b2c3dac696d05304d841a7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92014839"
---
# <a name="best-practices-for-long-running-operations"></a>Melhores práticas para operações de execução prolongada

Nos Serviços de Análise Azure, um *nó* representa uma máquina virtual hospedeira onde um recurso de servidor está em execução. Algumas operações, tais como consultas de longa duração, operações de atualização e sincronização de escala de consulta podem falhar se um recurso do servidor se mover para um nó diferente. As mensagens de erro comuns neste cenário incluem:

- "Ocorreu um erro ao tentar localizar um pedido de XMLA de longa duração. O pedido pode ter sido interrompido através da atualização do serviço ou do reinício do servidor."
- "Job with ID ' <guid> for model ' " foi cancelado devido a erro de serviço <database> (inatividade) com a mensagem 'Cancelar o pedido de atualização uma vez que estava preso sem quaisquer atualizações. Este é um problema de serviço interno. Por favor, reenviar o trabalho ou preencha um bilhete para obter ajuda se este problema acontecer repetidamente."

Há muitas razões pelas quais as operações de longo prazo podem ser interrompidas. Por exemplo, atualizações em Azure tais como: 
- Patches do Sistema Operativo 
- Atualizações de segurança
- Atualizações do serviço Azure Analysis Services
- Atualizações de Tecido de Serviço. O Service Fabric é um componente da plataforma utilizado por vários serviços na nuvem da Microsoft, incluindo os Azure Analysis Services.

Além das atualizações que ocorrem no serviço, há um movimento natural de serviços através de nós devido ao equilíbrio de carga. Os movimentos dos nóis são uma parte esperada de um serviço de nuvem. Os Serviços de Análise Azure tentam minimizar os impactos dos movimentos dos nós, mas é impossível eliminá-los completamente. 

Além dos movimentos do nó, há outras falhas que podem ocorrer. Por exemplo, um sistema de base de dados de fonte de dados pode estar offline ou a conectividade da rede é perdida. Se durante a atualização, uma partição tem 10 milhões de linhas e uma falha ocorre na 9 milionésimo linha, não há como reiniciar a renovação no ponto de falha. O serviço tem que recomeçar desde o início. 

## <a name="refresh-rest-api"></a>Refrescar REST API

As interrupções de serviço podem ser desafiantes para operações de longa duração, como atualização de dados. A Azure Analysis Services inclui uma API REST para ajudar a mitigar os impactos negativos das interrupções de serviço. Para saber mais, consulte [Asynchronous refresh with the REST API](analysis-services-async-refresh.md).
 
Além da API REST, existem outras abordagens que pode utilizar para minimizar potenciais problemas durante as operações de atualização de longa duração. O objetivo principal é evitar ter que reiniciar a operação de atualização desde o início, e em vez disso realizar refrescos em lotes menores que podem ser cometidos por etapas. 
 
A API REST permite esse reinício, mas não permite a plena flexibilidade da criação e supressão da partição. Se um cenário requer operações complexas de gestão de dados, a sua solução deve incluir alguma forma de loteamento na sua lógica. Por exemplo, ao utilizar transações para processar dados em vários lotes separados permite uma falha não exigir o reinício desde o início, mas sim a partir de um ponto de verificação intermédio. 
 
## <a name="scale-out-query-replicas"></a>Réplicas de consulta de escala

Quer utilize a lógica REST ou a lógica personalizada, as consultas de aplicação do cliente ainda podem devolver resultados inconsistentes ou intermédios enquanto os lotes estão a ser processados. Se forem necessários dados consistentes devolvidos pelas consultas de aplicação do cliente durante o processamento, e os dados do modelo estiverem num estado intermédio, pode utilizar [a escala](analysis-services-scale-out.md) com réplicas de consulta apenas de leitura.

Ao utilizar réplicas de consulta apenas de leitura, enquanto as atualizações estão a ser realizadas em lotes, os utilizadores de aplicações do cliente podem continuar a consultar a antiga imagem de dados nas réplicas apenas de leitura. Uma vez terminadas as atualizações, pode ser realizada uma operação Synch para atualizar as réplicas apenas de leitura.


## <a name="next-steps"></a>Passos seguintes

[Atualização assíncrona com a API REST](analysis-services-async-refresh.md)  
[Escalamento horizontal do Azure Analysis Services](analysis-services-scale-out.md)  
[Elevada disponibilidade do Analysis Services](analysis-services-bcdr.md)  
[Redação de orientação para os serviços da Azure](/azure/architecture/best-practices/retry-service-specific)