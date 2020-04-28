---
title: Configure alta disponibilidade - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Como ativar ou desativar a alta disponibilidade
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a8d4b5949b34d16191e9ec10a1dd39faff3660dc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74977672"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Configure hiperescala (Citus) alta disponibilidade

A Base de Dados Azure para PostgreSQL - Hyperscale (Citus) fornece alta disponibilidade (HA) para evitar o tempo de inatividade da base de dados. Com HA ativado, cada nó de um grupo de servidores terá um standby. Se o nó original se tornar insalubre, o seu standby será promovido para substituí-lo.

> [!IMPORTANT]
> Como ha duplica o número de servidores no grupo, também vai duplicar o custo.

Ativar o HA é possível durante a criação do grupo de servidores, ou depois no separador **Configure** para o seu grupo de servidores no portal Azure. A interface do utilizador parece semelhante em qualquer dos casos. Arraste o slider para **alta disponibilidade** para SIM:

![ha slider](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Clique no botão **Guardar** para aplicar a sua seleção. Ativar o HA pode demorar algum tempo à medida que o grupo de servidores os espera e transmite dados para eles.

O separador **Overview** para o grupo de servidorlistará todos os nós e seus modos de espera, juntamente com uma coluna de **alta disponibilidade** indicando se ha está ativado com sucesso para cada nó.

![a coluna ha na visão geral do grupo de servidor](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a alta disponibilidade.](concepts-hyperscale-high-availability.md)
