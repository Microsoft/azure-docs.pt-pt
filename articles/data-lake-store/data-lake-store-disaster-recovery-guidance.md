---
title: Diretrizes de recuperação de desastre para Azure Data Lake Storage Gen1 | Microsoft Docs
description: Diretrizes sobre a recuperação de desastres para Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: b33977ca5184ea07b5651be18e3a132d30ce4b39
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75966060"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Diretrizes de recuperação de desastre para dados em Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 fornece armazenamento com redundância local (LRS). Portanto, os dados em sua conta de Data Lake Storage Gen1 são resilientes a falhas de hardware transitórias em um datacenter por meio de réplicas automatizadas. Isso garante a durabilidade e a alta disponibilidade, atendendo ao SLA de Data Lake Storage Gen1. Este artigo fornece orientação sobre como proteger ainda mais seus dados contra interrupções raras em toda a região ou exclusões acidentais.

## <a name="disaster-recovery-guidance"></a>Documentação de orientação da recuperação após desastre
É fundamental para cada cliente preparar o seu próprio plano de recuperação após desastre. Leia as informações neste artigo para criar seu plano de recuperação de desastre. Eis alguns recursos que o podem ajudar a criar o seu plano.

* [Recuperação após desastre e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Orientações técnicas sobre resiliência do Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Melhores práticas
Recomendamos que você copie seus dados críticos para outra conta de Data Lake Storage Gen1 em outra região com uma frequência alinhada às necessidades do seu plano de recuperação de desastre. Existem vários métodos para copiar dados, incluindo [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) ou [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). O Azure Data Factory é um serviço útil para criar e implementar pipelines de movimento de dados periodicamente.

Se ocorrer uma interrupção regional, você poderá acessar os dados na região em que os dados foram copiados. Você pode monitorar o [painel de integridade do serviço do Azure](https://azure.microsoft.com/status/) para determinar o status do serviço do Azure em todo o mundo.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Orientações sobre a recuperação após danos em dados ou eliminação acidental
Embora Data Lake Storage Gen1 forneça resiliência de dados por meio de réplicas automatizadas, isso não impede que seu aplicativo (ou desenvolvedores/usuários) corrompa dados ou os exclua acidentalmente.

### <a name="best-practices"></a>Melhores práticas
Para evitar a exclusão acidental, recomendamos que você primeiro defina as políticas de acesso corretas para sua conta de Data Lake Storage Gen1.  Isso inclui a aplicação de [bloqueios de recursos do Azure](../azure-resource-manager/management/lock-resources.md) para bloquear recursos importantes, bem como aplicar o controle de acesso de nível de conta e arquivo usando os [recursos de segurança data Lake Storage Gen1](data-lake-store-security-overview.md)disponíveis. Também recomendamos que você crie cópias de seus dados críticos rotineiramente usando [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) ou [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) em outra conta do data Lake Storage Gen1, pasta ou assinatura do Azure.  Esta ação pode ser utilizada para recuperar de um incidente de eliminação ou danos em dados. O Azure Data Factory é um serviço útil para criar e implementar pipelines de movimento de dados periodicamente.

As organizações também podem habilitar o [log de diagnóstico](data-lake-store-diagnostic-logs.md) para sua conta de data Lake Storage Gen1 para coletar trilhas de auditoria de acesso a dados que fornecem informações sobre quem pode ter excluído ou atualizado um arquivo.

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)

