---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "90068745"
---
**Como vou ser notificado do SDK descontinuado?**

A Microsoft dará um aviso com 12 meses de antecedência antes do fim de suporte do SDK descontinuado para permitir uma transição suave para um SDK suportado. Será notificado através de vários canais de comunicação: o portal do Azure, as atualizações do Azure e através de comunicação direta com os administradores atribuídos aos serviços.

**Posso criar aplicações com um SDK do Azure Cosmos DB que vai ser descontinuado durante o período de 12 meses?** 

Sim, poderá criar, implementar e modificar aplicações com o SDK do Azure Cosmos DB que vai ser descontinuado durante o período de aviso de 12 meses. Recomendamos que migre para uma versão suportada mais recente do SDK do Azure Cosmos DB durante o período de aviso de 12 meses, conforme adequado. 

**Após a data da descontinuação, o que acontece às aplicações que utilizam o SDK do Azure Cosmos DB não suportado?** 

Após a data de descontinuação, o Azure Cosmos DB deixará de fazer correções de erros, adicionar funcionalidades novas e fornecer suporte às versões descontinuadas do SDK. Se preferir não atualizar, o serviço Azure Cosmos DB continua a servir os pedidos enviados das versões descontinuadas do SDK. 

**Que versões do SDK terão as mais recentes funcionalidades e atualizações?**

As funcionalidades e atualizações novas só vão ser adicionadas à última versão menor da última grande versão do SDK. Recomendamos que utilize sempre a última versão para tirar partido das funcionalidades, das melhorias ao desempenho e das correções de erro mais recentes. Se estiver a utilizar uma versão antiga e ainda em uso do SDK, os pedidos ao Azure Cosmos DB continuarão a funcionar, mas não terá acesso a nenhuma capacidade nova.  

**O que devo fazer se não conseguir atualizar a minha aplicação antes da data de descontinuação?**

Recomendamos que atualize para o último SDK o mais cedo possível. Quando um SDK é marcado para descontinuação, tem 12 meses para atualizar a sua aplicação. Se não conseguir atualizar até à data de descontinuação, os pedidos enviados das versões descontinuadas do SDK continuam a ser servidos pelo Azure Cosmos DB, pelo que as aplicações em execução continuam a funcionar. Contudo, o Azure Cosmos DB deixará de fazer correções de erros, adicionar funcionalidades novas e fornecer suporte às versões descontinuadas do SDK. 

Se tiver um plano de suporte e precisar de suporte técnico, preencha um pedido de suporte para [nos contactar](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
    


