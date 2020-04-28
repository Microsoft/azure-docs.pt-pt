---
title: Orientação de recuperação de desastres para Azure Data Lake Storage Gen1 Microsoft Docs
description: Orientação sobre recuperação de desastres para Azure Data Lake Storage Gen1
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75966060"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Orientação de recuperação de desastres para dados em Azure Data Lake Storage Gen1

O Azure Data Lake Storage Gen1 fornece armazenamento localmente redundante (LRS). Assim, os dados na sua conta data Lake Storage Gen1 são resilientes a falhas de hardware transitórias dentro de um datacenter através de réplicas automatizadas. Isto garante durabilidade e elevada disponibilidade, cumprindo o Data Lake Storage Gen1 SLA. Este artigo fornece orientações sobre como proteger ainda mais os seus dados de falhas raras em toda a região ou supressões acidentais.

## <a name="disaster-recovery-guidance"></a>Documentação de orientação da recuperação após desastre
É fundamental para cada cliente preparar o seu próprio plano de recuperação após desastre. Leia a informação neste artigo para construir o seu plano de recuperação de desastres. Eis alguns recursos que o podem ajudar a criar o seu plano.

* [Recuperação após desastre e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Orientações técnicas sobre resiliência do Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Melhores práticas
Recomendamos que copie os seus dados críticos para outra conta gen1 de armazenamento de data lake em outra região com uma frequência alinhada com as necessidades do seu plano de recuperação de desastres. Existem vários métodos para copiar dados, incluindo [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) ou [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). O Azure Data Factory é um serviço útil para criar e implementar pipelines de movimento de dados periodicamente.

Se ocorrer uma paragem regional, poderá aceder aos seus dados na região onde os dados foram copiados. Pode monitorizar o Painel de [Saúde do Serviço Azure](https://azure.microsoft.com/status/) para determinar o estado do serviço Azure em todo o mundo.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Orientações sobre a recuperação após danos em dados ou eliminação acidental
Embora o Data Lake Storage Gen1 forneça resiliência de dados através de réplicas automatizadas, isso não impede que a sua aplicação (ou desenvolvedores/utilizadores) corrompa dados ou os aprove acidentalmente.

### <a name="best-practices"></a>Melhores práticas
Para evitar a eliminação acidental, recomendamos que estabeleça primeiro as políticas de acesso corretas para a sua conta Data Lake Storage Gen1.  Isto inclui a aplicação de bloqueios de [recursos Azure](../azure-resource-manager/management/lock-resources.md) para bloquear recursos importantes, bem como a aplicação de controlo de acesso ao nível da conta e do nível de ficheiroutilizando as funcionalidades de segurança disponíveis para armazenamento [de dados do Lago Gen1](data-lake-store-security-overview.md). Recomendamos também que crie rotineiramente cópias dos seus dados críticos utilizando [a ADLCopy, ADLCopy,](data-lake-store-copy-data-azure-storage-blob.md) [Azure PowerShell](data-lake-store-get-started-powershell.md) ou [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) noutra conta Gen1, pasta ou subscrição do Data Lake Storage Gen1.  Esta ação pode ser utilizada para recuperar de um incidente de eliminação ou danos em dados. O Azure Data Factory é um serviço útil para criar e implementar pipelines de movimento de dados periodicamente.

As organizações também podem permitir o [registo de diagnóstico](data-lake-store-diagnostic-logs.md) para a sua conta Data Lake Storage Gen1 para recolher pistas de auditoria de acesso a dados que fornecem informações sobre quem pode ter apagado ou atualizado um ficheiro.

## <a name="next-steps"></a>Passos seguintes
* [Começar com azure data lake storage Gen1](data-lake-store-get-started-portal.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)

