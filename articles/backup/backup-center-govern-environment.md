---
title: Administrar as cópias de segurança realizadas através do Centro de Cópias de Segurança
description: Aprenda a governar o seu ambiente Azure para garantir que todos os seus recursos estão em conformidade com uma perspetiva de backup com o Backup Center.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 67b0591c7d7146d162687018854365d338105d76
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98893851"
---
# <a name="govern-your-backup-estate-using-backup-center-preview"></a>Regule o seu espólio de backup usando backup Center (Preview)

O Backup Center ajuda-o a governar o seu ambiente Azure para garantir que todos os seus recursos estão em conformidade com uma perspetiva de backup. Abaixo estão algumas das capacidades de governação do Backup Center:

* Ver e atribuir Políticas Azure para backup

* Consulte o cumprimento dos seus recursos em todas as Políticas Azure incorporadas para obter apoio.

* Ver todas as fontes de dados que não foram configuradas para obter cópias de segurança.

## <a name="supported-scenarios"></a>Cenários suportados

* Consulte a matriz de [suporte](backup-center-support-matrix.md) para obter uma lista detalhada de cenários suportados e não apoiados.

## <a name="azure-policies-for-backup"></a>Políticas Azure para backup

Para ver todas as [Políticas Azure](../governance/policy/overview.md) que estão disponíveis para cópia de segurança, selecione o item do menu **Azure Policies for Backup.** Isto irá exibir todas as [definições de política Azure incorporadas e personalizadas para cópias de segurança](policy-reference.md) que estão disponíveis para atribuição às suas subscrições e grupos de recursos.

A seleção de qualquer uma das definições permite [atribuir a apólice](../governance/policy/tutorials/create-and-manage.md#assign-a-policy) a um âmbito.

![Selecione definições de Política Azure](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>Conformidade de backup

Clicar no item do menu de conformidade de cópia de segurança ajuda-o a ver a [conformidade](../governance/policy/how-to/get-compliance-data.md) dos seus recursos de acordo com as várias políticas incorporadas que atribuiu ao seu ambiente Azure. Pode ver a percentagem de recursos que estão em conformidade com todas as políticas, bem como as políticas que têm um ou mais recursos não conformes.

![Ver conformidade de backup](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>Fontes de dados protegidas

A seleção do item do menu **De recursos humanos protegidos** permite-lhe visualizar todos os seus recursos de dados que não foram configurados para cópia de segurança. Pode filtrar a lista por subscrição de fonte de dados, grupo de recursos, localização, tipo e etiquetas. Uma vez identificado um recurso de dados que precisa de ser apoiado, pode clicar no item da grelha correspondente e selecionar **Backup** para configurar a cópia de segurança para o recurso.

![Menu de fontes de dados protegidos](./media/backup-center-govern-environment/protectable-datasources.png)

> [!NOTE]
> Se selecionar **o SQL em Azure VM** como o tipo de fonte de dados, a visualização de fontes de **dados protegidos** apresenta a lista de todos os VMs da Galeria que não possuem bases de dados SQL que tenham sido configuradas para cópia de segurança.
> Se selecionar **o Azure Storage (Azure Files)** como o tipo de fonte de dados, a visualização de fontes de **dados protegidos** apresenta a lista de todas as contas de armazenamento (que suportam ações de ficheiros) que não têm quaisquer ações de ficheiro que tenham sido configuradas para cópia de segurança.


## <a name="next-steps"></a>Próximos passos

* [Monitorizar e operar backups](backup-center-monitor-operate.md)
* [Executar ações usando o Backup Center](backup-center-actions.md)
* [Obtenha informações sobre as suas cópias de segurança](backup-center-obtain-insights.md)