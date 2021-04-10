---
title: Visão geral do centro de backup
description: Este artigo fornece uma visão geral do Backup Center para Azure.
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: b42bb2719d03108212f62428dc97ed814899c63c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506059"
---
# <a name="overview-of-backup-center"></a>Visão geral do centro de backup

O Backup Center proporciona uma **única experiência de gestão unificada** em Azure para que as empresas governem, monitorizem, operem e analisem backups em escala. Como tal, é consistente com as experiências de gestão nativa do Azure.

Alguns dos principais benefícios do backup center incluem:

* **Painel único de vidro para gerir backups** – O centro de backup foi projetado para funcionar bem em um ambiente Azure grande e distribuído. Você pode usar o backup center para gerir eficientemente backups abrangendo vários tipos de carga de trabalho, cofres, subscrições, regiões e inquilinos [do Farol Azure.](../lighthouse/overview.md)
* **Gestão centrada em dados** – O backup center fornece vistas e filtros centrados nas fontes de dados que está a fazer (por exemplo, VMs e bases de dados). Isto permite que um proprietário de recursos ou um administrador de backup monitorize e opere cópias de segurança de itens sem precisar de se concentrar no cofre a que um item está apoiado. Uma característica chave deste design é a capacidade de filtrar visualizações por propriedades específicas de fontes de dados, tais como subscrição de fontes de dados, grupo de recursos de fonte de dados e tags de fonte de dados. Por exemplo, se a sua organização seguir uma prática de atribuir diferentes tags a VMs pertencentes a diferentes departamentos, pode usar o Backup Center para filtrar informações de backup com base nas etiquetas dos VMs subjacentes que estão a ser apoiados sem precisar de se concentrar na etiqueta do cofre.
* **Experiências conectadas** – O backup center proporciona integrações nativas aos serviços Azure existentes que permitem a gestão em escala. Por exemplo, o Backup Center utiliza a experiência [Azure Policy](../governance/policy/overview.md) para o ajudar a governar as suas cópias de segurança. Também aproveita os [livros de trabalho da Azure](../azure-monitor/visualize/workbooks-overview.md) e os [Registos do Monitor Azure](../azure-monitor/logs/data-platform-logs.md) para ajudá-lo a ver relatórios detalhados sobre cópias de segurança. Por isso, não precisa de aprender novos princípios para usar as funcionalidades variadas que o Backup Center oferece. Você também pode descobrir recursos comunitários a partir do centro de backup.

## <a name="supported-scenarios"></a>Cenários suportados

* O backup center é atualmente suportado para backup Azure VM, SQL em backup Azure VM, SAP HANA em backup Azure VM, backup Azure Files, backup Azure Blobs, backup de Discos Geridos Azure e Base de Dados Azure para backup do Servidor PostgreSQL.
* Consulte a matriz de [suporte](backup-center-support-matrix.md) para obter uma lista detalhada de cenários suportados e não apoiados.

## <a name="get-started"></a>Introdução

Para começar a utilizar o backup center, procure o **backup center** no portal Azure e navegue para o painel **de instrumentos do centro de backup.**

![Pesquisa de backup center](./media/backup-center-overview/backup-center-search.png)

O primeiro ecrã que vê é a **Visão Geral.** Contém dois azulejos - **Casos de emprego** e **backup.**

![Azulejos do Centro de Apoio](./media/backup-center-overview/backup-center-overview-widgets.png)

No azulejo **Jobs,** você tem uma visão resumida de todos os backups e restaurar empregos relacionados que foram desencadeados em toda a sua propriedade de reserva nas últimas 24 horas. Pode ver informações sobre o número de postos de trabalho que concluíram, falharam e estão em curso. A seleção de qualquer um dos números deste azulejo permite-lhe visualizar mais informações sobre trabalhos para um determinado tipo de fonte de dados, tipo de operação e estado.

No azulejo **de Back Instances,** obtém uma visão resumida de todas as instâncias de backup em toda a sua propriedade de reserva. Por exemplo, pode ver o número de casos de backup que estão em estado de apagação suave em comparação com o número de casos que ainda estão configurados para proteção. A seleção de qualquer um dos números deste azulejo permite-lhe visualizar mais informações sobre casos de backup para um determinado tipo de fonte de dados e estado de proteção. Também pode ver todas as instâncias de backup cuja fonte de dados subjacente não seja encontrada (a fonte de dados pode ser eliminada, ou pode não ter acesso à fonte de dados).

Veja o seguinte vídeo para entender as capacidades do backup center:

> [!VIDEO https://www.youtube.com/embed/pFRMBSXZcUk?t=497]

Siga os [próximos passos](#next-steps) para entender as diferentes capacidades que o Backup Center fornece e como pode usar essas capacidades para gerir eficientemente a sua propriedade de backup.

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar e operar backups](backup-center-monitor-operate.md)
* [Governe a sua propriedade de reserva](backup-center-govern-environment.md)
* [Obtenha informações sobre as suas cópias de segurança](backup-center-obtain-insights.md)
* [Execute ações usando o backup center](backup-center-actions.md)