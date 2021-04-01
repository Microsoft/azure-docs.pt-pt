---
title: Monitorize registos usando o Livro de Trabalho da Firewall Azure
description: Os livros de trabalho da Azure Firewall fornecem uma tela flexível para a análise de dados do Azure Firewall e a criação de relatórios visuais ricos dentro do portal Azure.
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 09/22/2020
ms.author: victorh
ms.openlocfilehash: e63336b0e84d303b51eda56f90ca6fb453d8ae0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831846"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>Monitorize registos usando o Livro de Trabalho da Firewall Azure

O Azure Firewall Workbook fornece uma tela flexível para a análise de dados do Azure Firewall. Pode usá-lo para criar relatórios visuais ricos dentro do portal Azure. Você pode aceder a várias Firewalls implantadas em Azure, e combiná-las em experiências interativas unificadas.

Você pode obter insights sobre eventos Azure Firewall, aprender sobre as suas regras de aplicação e rede, e ver estatísticas para atividades de firewall em URLs, portas e endereços. O Azure Firewall Workbook permite filtrar as suas firewalls e grupos de recursos e filtrar dinamicamente por categoria com conjuntos de dados fáceis de ler ao investigar um problema nos seus registos. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar, deverá [ativar](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal) o registo de diagnóstico através do portal Azure. Leia também [os registos e métricas do Azure Firewall](logs-and-metrics.md) para uma visão geral dos registos de diagnóstico e métricas disponíveis para a Azure Firewall.

## <a name="get-started"></a>Introdução

Para implementar o livro, vá ao [Livro de Trabalho do Monitor Azure para a Azure Firewall](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Workbook%20-%20Azure%20Firewall%20Monitor%20Workbook) e seguindo as instruções na página. O Azure Firewall Workbook é projetado para trabalhar em vários inquilinos, multi-subscrições, e é filtrado para várias firewalls.

## <a name="overview-page"></a>Página de descrição geral

A página geral fornece-lhe uma maneira de filtrar através de espaços de trabalho, tempo e firewalls. Mostra eventos por tempo através de firewalls e tipos de registo (aplicação, redes, informação de ameaça, procuração de DNS).

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Visão geral do livro de Azure Firewall":::

## <a name="application-rule-log-statistics"></a>Estatísticas de registo de regras de aplicação

Esta página mostra fontes únicas de endereço IP ao longo do tempo, utilização da contagem de regras de aplicação, FQDN negado/permitido ao longo do tempo, e dados filtrados. Pode filtrar dados com base no endereço IP.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Registo de regras de aplicação do livro de trabalho de Azure Firewall":::

## <a name="network-rule-log-statistics"></a>Estatísticas de registo de regras de rede

Esta página fornece uma visão por ação de regras – permitir/negar, porta-alvo por IP e DNAT ao longo do tempo. Também pode filtrar por ação, porta e tipo de destino.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Registo de regras da rede de livros de trabalho de Azure Firewall":::

Também pode filtrar registos com base na janela do tempo:

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Janela de tempo de registo de regras da rede de regras da rede Azure Firewall":::

## <a name="investigations"></a>Investigações

Pode olhar para os registos e entender mais sobre o recurso baseado no endereço IP de origem. Pode obter informações como nome de máquina virtual e nome de interface de rede. É simples filtrar o recurso dos registos.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Investigação do Livro de Obras de Azure Firewall":::

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Azure Firewall Diagnostics](firewall-diagnostics.md)
