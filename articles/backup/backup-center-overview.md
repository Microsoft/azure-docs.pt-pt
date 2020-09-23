---
title: Visão geral do Backup Center
description: Este artigo fornece uma visão geral do Backup Center for Azure.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: e7577f302b9a137f5f3c257dc35d0fdf339f69a4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997008"
---
# <a name="overview-of-backup-center"></a>Visão geral do Backup Center

O Backup Center proporciona uma **única experiência de gestão unificada** em Azure para que as empresas governem, monitorizem, operem e analisem backups em escala. Como tal, é consistente com as experiências de gestão nativa do Azure.

Alguns dos principais benefícios do Backup Center incluem:

* **Painel único de vidro para gerir backups** – O Backup Center foi concebido para funcionar bem em um ambiente Azure grande e distribuído. Você pode usar backup Center para gerir eficientemente backups abrangendo vários tipos de carga de trabalho, cofres, subscrições, regiões e inquilinos [do Farol Azure.](https://docs.microsoft.com/azure/lighthouse/overview)
* **Gestão centrada em dados** – O Backup Center fornece vistas e filtros centrados nas fontes de dados que está a fazer (por exemplo, VMs e bases de dados). Isto permite que um proprietário de recursos ou um administrador de backup monitorize e opere cópias de segurança de itens sem precisar de se concentrar no cofre a que um item está apoiado. Uma característica chave deste design é a capacidade de filtrar visualizações por propriedades específicas de fontes de dados, tais como subscrição de fontes de dados, grupo de recursos de fonte de dados e tags de fonte de dados. Por exemplo, se a sua organização seguir uma prática de atribuir diferentes tags a VMs pertencentes a diferentes departamentos, pode usar o Backup Center para filtrar informações de backup com base nas etiquetas dos VMs subjacentes que estão a ser apoiados sem precisar de se concentrar na etiqueta do cofre.
* **Experiências conectadas** – O Backup Center proporciona integrações nativas aos serviços Azure existentes que permitem a gestão em escala. Por exemplo, o Backup Center utiliza a experiência [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) para o ajudar a governar as suas cópias de segurança. Também aproveita os [livros de trabalho da Azure](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview) e os [Registos do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) para ajudá-lo a ver relatórios detalhados sobre cópias de segurança. Por isso, não precisa de aprender novos princípios para usar as funcionalidades variadas que o Backup Center oferece.

## <a name="supported-scenarios"></a>Cenários suportados

* O Backup Center é atualmente suportado para cópia de segurança Azure VM e Base de Dados Azure para backup do Servidor PostgreSQL.
* Consulte a matriz de [suporte](backup-center-support-matrix.md) para obter uma lista detalhada de cenários suportados e não apoiados.

## <a name="get-started"></a>Introdução

Para começar a utilizar o Backup Center, procure o **Backup Center** no portal Azure e navegue para o painel **de instrumentos do Backup Center (Preview).**

![Pesquisa de backup center](./media/backup-center-overview/backup-center-search.png)

O primeiro ecrã que vê é a **Visão Geral.** Contém dois azulejos - **Casos de emprego** e **backup.**

![Azulejos do Centro de Apoio](./media/backup-center-overview/backup-center-overview-widgets.png)

No azulejo **Jobs,** você tem uma visão resumida de todos os backups e restaurar empregos relacionados que foram desencadeados em toda a sua propriedade de reserva nas últimas 24 horas. Pode ver informações sobre o número de postos de trabalho que concluíram, falharam e estão em curso. A seleção de qualquer um dos números deste azulejo permite-lhe visualizar mais informações sobre trabalhos para um determinado tipo de fonte de dados, tipo de operação e estado.

No azulejo **de Back Instances,** obtém uma visão resumida de todas as instâncias de backup em toda a sua propriedade de reserva. Por exemplo, pode ver o número de casos de backup que estão em estado de apagação suave em comparação com o número de casos que ainda estão configurados para proteção. A seleção de qualquer um dos números deste azulejo permite-lhe visualizar mais informações sobre casos de backup para um determinado tipo de fonte de dados e estado de proteção.

Siga os passos abaixo para entender as diferentes capacidades que o Backup Center fornece e como pode usar essas capacidades para gerir a sua propriedade de backup de forma eficiente.

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar e operar backups](backup-center-monitor-operate.md)
* [Governe a sua propriedade de reserva](backup-center-govern-environment.md)
* [Obtenha informações sobre as suas cópias de segurança](backup-center-obtain-insights.md)
* [Executar ações usando o Backup Center](backup-center-actions.md)
