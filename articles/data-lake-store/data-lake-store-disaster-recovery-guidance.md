---
title: Orientações sobre a recuperação após desastre para geração 1 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Orientações sobre recuperação de desastres para a geração 1 de armazenamento do Azure Data Lake
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
ms.openlocfilehash: b3f1888a73baf2b7f9efa9f5e7cdb3305aa9f90d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878295"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Orientações sobre a recuperação após desastre para dados na geração 1 de armazenamento do Azure Data Lake

Geração de armazenamento 1 do Azure Data Lake fornece armazenamento localmente redundante (LRS). Portanto, os dados na sua conta de geração 1 de armazenamento do Data Lake são resilientes a falhas de hardware transitórias num centro de dados através de réplicas automatizadas. Isto garante durabilidade e elevada disponibilidade, cumprindo o SLA de geração 1 de armazenamento do Data Lake. Este artigo fornece orientações sobre como proteger ainda mais os seus dados de falhas raras de toda a região ou eliminações acidentais.

## <a name="disaster-recovery-guidance"></a>Documentação de orientação da recuperação após desastre
É fundamental para cada cliente preparar o seu próprio plano de recuperação após desastre. Leia as informações neste artigo para criar o seu plano de recuperação após desastre. Eis alguns recursos que o podem ajudar a criar o seu plano.

* [Recuperação após desastre e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Orientações técnicas sobre resiliência do Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Melhores práticas
Recomendamos que copie os dados críticos para outra conta de geração 1 de armazenamento do Data Lake noutra região com uma frequência alinhada com as necessidades do seu plano de recuperação após desastre. Existem vários métodos para copiar dados, incluindo [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) ou [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). O Azure Data Factory é um serviço útil para criar e implementar pipelines de movimento de dados periodicamente.

Se ocorrer uma falha regional, em seguida, pode acessar seus dados na região onde os dados foram copiados. Pode monitorizar o [Dashboard de estado de funcionamento do serviço de Azure](https://azure.microsoft.com/status/) para determinar o estado do serviço do Azure em todo o mundo.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Orientações sobre a recuperação após danos em dados ou eliminação acidental
Enquanto Gen1 de armazenamento do Data Lake fornece resiliência de dados através de réplicas automatizadas, isto não impede que o seu aplicativo (ou os programadores/utilizadores) danifique dados ou acidentalmente.

### <a name="best-practices"></a>Melhores práticas
Para impedir a eliminação acidental, recomendamos que defina primeiro as políticas de acesso corretas para a sua conta de geração 1 de armazenamento do Data Lake.  Isto inclui aplicar [bloqueios de recursos do Azure](../azure-resource-manager/resource-group-lock-resources.md) bloquear recursos importantes, bem como aplicar controlo de acesso de nível ficheiro e da conta usando o disponíveis [recursos de segurança de geração 1 de armazenamento do Data Lake](data-lake-store-security-overview.md). Também recomendamos que crie regularmente cópias dos dados críticos com [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) ou [do Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) no outro Data Lake Storage geração 1 conta, pasta ou subscrição do Azure.  Esta ação pode ser utilizada para recuperar de um incidente de eliminação ou danos em dados. O Azure Data Factory é um serviço útil para criar e implementar pipelines de movimento de dados periodicamente.

Também podem ativar a organizações [registo de diagnósticos](data-lake-store-diagnostic-logs.md) da sua conta de geração 1 de armazenamento do Data Lake recolher registos de auditoria de acesso de dados que fornece informações sobre quem poderá ter eliminado ou atualizado um ficheiro.

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)

