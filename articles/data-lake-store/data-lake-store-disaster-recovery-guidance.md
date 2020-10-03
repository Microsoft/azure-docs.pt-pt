---
title: Orientação de recuperação de desastres para Azure Data Lake Storage Gen1 Microsoft Docs
description: Saiba como proteger ainda mais os seus dados contra interrupções ou eliminações acidentais para além do armazenamento localmente redundante da Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: fe5940083d04c1b5cc60b21834adf609e523e059
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91666250"
---
# <a name="high-availability-and-disaster-recovery-guidance-for-data-lake-storage-gen1"></a>Alta disponibilidade e orientação de recuperação de desastres para data lake storage gen1

Data Lake Storage Gen1 fornece armazenamento localmente redundante (LRS). Por isso, os dados na sua conta de Data Lake Storage Gen1 são resilientes a falhas de hardware transitórios dentro de um datacenter através de réplicas automatizadas. Isto garante durabilidade e alta disponibilidade, atendendo ao Data Lake Storage Gen1 SLA. Este artigo fornece orientações sobre como proteger ainda mais os seus dados de interrupções raras em toda a região ou supressões acidentais.

## <a name="disaster-recovery-guidance"></a>Documentação de orientação da recuperação após desastre

É fundamental para si preparar um plano de recuperação de desastres. Reveja as informações neste artigo e estes recursos adicionais para ajudá-lo a criar o seu próprio plano.

* [Recuperação após desastre e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Orientações técnicas sobre resiliência do Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practice-recommendations"></a>Recomendações de boas práticas

Recomendamos que copie os seus dados críticos para outra conta da Data Lake Storage Gen1 noutra região com uma frequência alinhada com as necessidades do seu plano de recuperação de desastres. Existem uma variedade de métodos para copiar dados, incluindo [ADLCopy,](data-lake-store-copy-data-azure-storage-blob.md) [Azure PowerShell,](data-lake-store-get-started-powershell.md)ou [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). O Azure Data Factory é um serviço útil para criar e implementar pipelines de movimento de dados periodicamente.

Se ocorrer uma paragem regional, pode então aceder aos seus dados na região onde os dados foram copiados. Pode monitorizar o Painel de Saúde do [Serviço Azure](https://azure.microsoft.com/status/) para determinar o estado do serviço Azure em todo o mundo.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Orientações sobre a recuperação após danos em dados ou eliminação acidental

Embora a Data Lake Storage Gen1 forneça resiliência de dados através de réplicas automatizadas, isso não impede que a sua aplicação (ou desenvolvedores/utilizadores) corrompe os dados ou os extinga acidentalmente.

Para evitar a eliminação acidental, recomendamos que desementasse as políticas de acesso corretas para a sua conta Desarmar o Data Lake Storage Gen1. Isto inclui a aplicação de bloqueios de [recursos Azure](../azure-resource-manager/management/lock-resources.md) para bloquear recursos importantes e aplicar o controlo de acesso ao nível de conta e ficheiro usando as funcionalidades de segurança disponíveis [da Data Lake Storage Gen1](data-lake-store-security-overview.md). Recomendamos também que crie cópias dos seus dados críticos utilizando [a ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) ou [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) em outra conta da Data Lake Storage Gen1, pasta ou subscrição Azure. Esta ação pode ser utilizada para recuperar de um incidente de eliminação ou danos em dados. O Azure Data Factory é um serviço útil para criar e implementar pipelines de movimento de dados periodicamente.

Também pode permitir [o registo de diagnóstico](data-lake-store-diagnostic-logs.md) de uma conta gen1 de armazenamento de dados para recolher pistas de auditoria de acesso a dados. Os rastos de auditoria fornecem informações sobre quem poderia ter apagado ou atualizado um ficheiro.

## <a name="next-steps"></a>Próximas etapas

* [Começar com data lake storage gen1](data-lake-store-get-started-portal.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
