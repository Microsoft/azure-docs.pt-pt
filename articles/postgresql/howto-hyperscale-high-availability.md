---
title: Configure alta disponibilidade - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Como ativar ou desativar a alta disponibilidade
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 78e6e8c18e67ce2dff0de94d298f06693a40135f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82584049"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Configurar Alta disponibilidade de Hiperescala (Citus)

A base de dados Azure para PostgreSQL - Hiperescala (Citus) fornece alta disponibilidade (HA) para evitar tempo de inatividade na base de dados. Com ha ativado, cada nó de um grupo de servidor terá um standby. Se o nó original se tornar insalubre, o seu standby será promovido para substituí-lo.

> [!IMPORTANT]
> Como o HA duplica o número de servidores do grupo, também duplicará o custo.

Ativar HA é possível durante a criação do grupo do servidor, ou depois no separador **Configurar** para o seu grupo de servidor no portal Azure. A interface do utilizador é semelhante em qualquer dos casos. Arraste o slider para **alta disponibilidade** para SIM:

![ha slider](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Clique no botão **Guardar** para aplicar a sua seleção. Permitir o HA pode demorar algum tempo, uma vez que as disposições do grupo do servidor estão em espera e transmitem-lhes dados.

O **separador Visão Geral** do grupo do servidor listará todos os nós e os seus standbys, juntamente com uma coluna **de alta disponibilidade** indicando se HA está habilitado com sucesso para cada nó.

![a coluna ha na visão geral do grupo do servidor](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a alta disponibilidade.](concepts-hyperscale-high-availability.md)
