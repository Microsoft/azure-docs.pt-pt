---
title: 'Pedido de Gémeos Digitais Azure falhou com Status: 404 Sub-Domain não encontrados'
description: "As causas e as resoluções para o pedido de serviço falharam. Estado: 404 Sub-Domain não encontrados' em Azure Digital Twins."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/13/2021
ms.openlocfilehash: e3fe3ad22098d796faa309ff3509c318a7e1df4d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590285"
---
# <a name="service-request-failed-status-404-sub-domain-not-found"></a>O pedido de serviço falhou. Estado: 404 Sub-Domain não encontrados

Este artigo descreve as causas e as etapas de resolução para receber um erro de 404 dos pedidos de serviço para a Azure Digital Twins. 

## <a name="symptoms"></a>Sintomas

Este erro pode ocorrer ao aceder a uma instância Azure Digital Twins utilizando um principal serviço ou conta de utilizador que pertence a um [inquilino Azure Ative Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) diferente do caso. As [funções corretas](concepts-security.md) parecem ser atribuídas à identidade, mas os pedidos da API falham com um estado de erro de `404 Sub-Domain not found` .

## <a name="causes"></a>Causas

### <a name="cause-1"></a>Causa #1

A Azure Digital Twins exige que todos os utilizadores autenticados pertençam ao mesmo inquilino AZure AD que a instância Azure Digital Twins.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

## <a name="solutions"></a>Soluções

### <a name="solution-1"></a>#1 de solução

Você pode resolver esta questão tendo cada identidade federada de outro inquilino solicitar um **sinal** do inquilino "casa" da Azure Digital Twins. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

### <a name="solution-2"></a>#2 de solução

Se estiver a usar a `DefaultAzureCredential` classe no seu código e continuar a encontrar este problema depois de obter um token, pode especificar o inquilino da casa nas `DefaultAzureCredential` opções para esclarecer o inquilino mesmo quando a autenticação não chega a outro tipo.

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre segurança e permissões em Azure Digital Twins:
* [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md)
