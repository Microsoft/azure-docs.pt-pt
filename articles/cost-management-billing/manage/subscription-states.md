---
title: Estados de subscrição do Azure
description: Este artigo descreve os diferentes estados de uma subscrição do Azure.
keywords: estado da subscrição do Azure
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: andalmia
ms.openlocfilehash: 5267b333e66a0ae7b2ad05399406fecc32af74b0
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430131"
---
# <a name="azure-subscription-states"></a>Estados de subscrição do Azure

Este artigo descreve os vários estados que uma subscrição do Azure pode apresentar. Verá esses estados em **Estado** nas áreas de subscrição do portal do Azure.

| Estado da Subscrição | Descrição |
|-------------| ----------------|
| **Ativa**/**Ativada** | A sua subscrição do Azure está ativa. Pode utilizar a subscrição para implementar recursos novos e gerir recursos existentes.<br><br>Todas as operações (PUT, PATCH, DELETE, POST, GET) estão disponíveis para os fornecedores de recursos que estão [registados na sua subscrição](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| **Eliminado** | A sua subscrição do Azure foi eliminada juntamente com todos os recursos/dados subjacentes.<br><br>Não existem operações disponíveis. |
| **Desativado** | A sua subscrição do Azure está desativada e já não pode ser utilizada para criar nem gerir recursos do Azure. Neste estado, as suas máquinas virtuais são desalocadas, os endereços IP temporários são libertados, o armazenamento é só de leitura e os outros serviços são desativados. As subscrições podem ser desativadas devido aos seguintes motivos: O seu crédito poderá ter expirado. Poderá ter atingido o limite de gastos. Tem uma fatura vencida por liquidar. O limite do seu cartão de crédito foi excedido. Ou pode ter sido desativada ou cancelada explicitamente. Consoante o tipo de subscrição, as subscrições podem permanecer desativadas entre 1 e 90 dias. Depois desse período, são eliminadas definitivamente. Para obter mais informações, veja [Reativar uma subscrição do Azure desativada](subscription-disabled.md).<br><br>As operações para criar ou atualizar recursos (PUT, PATCH) estão desativadas. As operações que realizam ações (POST) também estão desativadas. Pode obter ou eliminar recursos (GET, DELETE). Os recursos continuam disponíveis. |
| **Expirada** | A subscrição do Azure expirou porque foi cancelada. Pode reativar uma subscrição expirada. Para obter mais informações, veja [Reativar uma subscrição do Azure desativada](subscription-disabled.md).<br><br>As operações para criar ou atualizar recursos (PUT, PATCH) estão desativadas. As operações que realizam ações (POST) também estão desativadas. Pode obter ou eliminar recursos (GET, DELETE).|
| **Vencido** | A sua subscrição do Azure tem um pagamento pendente. A subscrição continua ativa, mas se não pagar os montantes em dívida, a subscrição poderá ser desativada. Para obter mais informações, veja [Resolver saldos vencidos da sua subscrição do Azure](resolve-past-due-balance.md).<br><br>Estão disponíveis todas as operações. |
| **Avisado** | A sua subscrição Azure encontra-se num estado avisado e será desativada em breve se o motivo de aviso não for abordado. Uma subscrição pode estar num estado avisado se estiver vencida, tiver sido cancelada pelo utilizador, tiver expirado, etc.<br><br>Pode recuperar ou eliminar recursos (GET/DELETE), mas não poderá criar quaisquer recursos (PUT/PATCH/POST) |
