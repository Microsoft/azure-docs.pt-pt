---
title: Como satisfazer os requisitos de residência de dados no Azure Cosmos DB
description: saiba como satisfazer os requisitos de residência de dados no Azure Cosmos DB para que os seus dados e backups permaneçam numa única região.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c5f8a4361774368e3934d1e2b16c8311876f5caa
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491870"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>Como satisfazer os requisitos de residência de dados no Azure Cosmos DB

Na Azure Cosmos DB, pode configurar os seus dados e backups para permanecer numa única região para satisfazer os[ requisitos de residência.](https://azure.microsoft.com/en-us/global-infrastructure/data-residency/)

## <a name="residency-requirements-for-data"></a>Requisitos de residência para dados

Em Azure Cosmos DB, você deve configurar explicitamente a replicação de dados entre regiões. Saiba como configurar a geo-replicação utilizando [o portal Azure](how-to-manage-database-account.md#addremove-regions-from-your-database-account), [Azure CLI](scripts/cli/common/regions.md). Para satisfazer os requisitos de residência de dados, pode criar uma política de Azure que permita a certas regiões impedir a replicação de dados em regiões indesejadas.

## <a name="residency-requirements-for-backups"></a>Requisitos de residência para backups

**Backups de modo contínuo :** Estas cópias de segurança são residentes por padrão, uma vez que são armazenadas num armazenamento redundante local ou na zona. Para saber mais, consulte o artigo [de backup contínuo.](continuous-backup-restore-portal.md)

**Backups periódicos do modo :** Por predefinição, as cópias de segurança da conta de modo periódico serão armazenadas em armazenamento geo-redundante. Para modos periódicos de backup, pode configurar a redundância de dados ao nível da conta. Existem três opções de redundância para o armazenamento de reserva. São redundância local, redundância de zona ou redundância geo. Para saber mais, consulte como configurar a [redundância de backup](configure-periodic-backup-restore.md#configure-backup-interval-retention) usando o portal.

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>Use a Política Azure para impor os requisitos de residência

Se tiver requisitos de residência de dados que o exijam para manter todos os seus dados numa única região de Azure, pode impor backups redundantes ou locais redundantes para a sua conta utilizando uma Política Azure.  Também pode impor uma política que as contas DB do Azure Cosmos não são geo-replicadas para outras regiões.

A Azure Policy é um serviço que pode usar para criar, atribuir e gerir políticas que aplicam regras aos recursos da Azure. A Azure Policy ajuda-o a manter estes recursos em conformidade com os seus padrões corporativos e acordos de nível de serviço. Para mais informações, consulte como utilizar a [Política Azure](policy.md) para implementar a governação e os controlos dos recursos DB da Azure Cosmos

## <a name="next-steps"></a>Passos seguintes

* Configurar e gerir backup periódico usando [o portal Azure](configure-periodic-backup-restore.md)
* Configure e gere a cópia de segurança contínua utilizando [o portal Azure](continuous-backup-restore-portal.md), [PowerShell,](continuous-backup-restore-powershell.md) [CLI](continuous-backup-restore-command-line.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
