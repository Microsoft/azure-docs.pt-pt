---
title: 'Cenário: Firewall Azure - Encaminhamento interhub'
titleSuffix: Azure Virtual WAN
description: Cenários para encaminhamento - encaminhamento entre vários hubs que têm Firewall Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 1057dc28682c90374b2f8e8e0297f3d769c08bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568981"
---
# <a name="scenario-azure-firewall---interhub-preview"></a>Cenário: Azure Firewall - Interhub (Pré-visualização)

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Neste cenário, o objetivo é encaminhar entre vários hubs que contêm Azure Firewall. Para obter informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Arquitetura do cenário

Neste cenário, assumimos a seguinte configuração:

* Tem vários centros com Azure Firewall em cada centro.
* Está a utilizar o Secure Virtual Hub.
* Foram criadas as políticas adequadas para o tráfego privado (VNet), Internet e Branch.
* VNet-to-Internet (V2I), VNet-to-Branch (V2B), Branch-to-VNet (B2V), todos passam por Azure Firewall em cada hub.

Considerações adicionais:

* Para um cenário inter-hub com a Azure Firewall, o pressuposto é que os VNets falados não se associam a tabelas de rotas separadas. (por **exemplo, VNET 1** associado ao **quadro de rota A**e **VNET 2** associados ao **quadro de rota B**). Todos os VNets associam-se à mesma tabela de rotas em que residem as rotas para o Azure Firewall.
* A segurança via Azure Firewall está atualmente limitada apenas ao **tráfego de < >-> da Branch VNet** e do tráfego **de Internet.** Fluxo branch-to-Branch através da Firewall Azure atualmente não suportado.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/architecture.png" alt-text="arquitetura":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho de cenário

A fim de configurar este cenário, tome em consideração os seguintes passos:

### <a name="step-1"></a><a name="step-1"></a>Passo 1

Assumindo que já garantiu as ligações através do Azure Firewall Manager, o primeiro passo é garantir que todas as ligações de ramo e VNet propagam **Nenhuma**. Isto é necessário para garantir que o tráfego está a ser definido através da Firewall Azure.

1. Selecione o hub e edite a Tabela de Rota **De Ninguém.**
1. Atualização **propagação** para selecionar todos os ramos e todos os VNets.

### <a name="step-2"></a><a name="step-2"></a>Passo 2

Crie uma tabela de rota personalizada por hub.

1. Para **o Hub 1,** crie RT_Hub1 de tabela de **rotas**.

    * Se utilizar o Azure Firewall Manager, cria automaticamente uma rota para 0.0.0.0/0 com o próximo hop **AZFW1** na tabela de rotas predefinida do hub. Vamos modificar esta definição no passo 3. Para **RT_Hub1**, crie uma entrada para 0.0.0.0/0 explicitamente com o próximo lúpulo **AZFW1**. Esta definição ativará V2V, B2V e V2I via AZFW1.
    * Uma vez que pretende que os balcões do **Hub 2** sejam alcançados através do **AZFW2** a partir do **Hub 1** (em vez de via AZFW1), precisa de adicionar mais 2 rotas agregadas para os balcões **hub 2** (10.5.0.0/16->AZFW2) e VNets (10.2.0.0/16->AZFW2).

1. Para **o Hub 2,** crie a Tabela de **Rota RT_Hub2**.

    * Se utilizar o Azure Firewall Manager, cria automaticamente uma rota para 0.0.0.0/0 com o próximo hop **AZFW2** na tabela de rotas predefinida do hub. Vamos modificar esta definição no passo 3. Para **RT_Hub2**, crie uma entrada para 0.0.0.0/0 explicitamente com o próximo lúpulo **AZFW2**. Esta definição ativará V2V, B2V e V2I via **AZFW2**.
    * Como pretende que o tráfego inter-hub seja protegido pelo **AZFW2**do Hub 2, não precisa de adicionar explicitamente um passo semelhante à segunda bala no anterior passo do Hub 1.

### <a name="step-3"></a><a name="step-3"></a>Passo 3

Modificar a **Tabela de Rota Padrão** em cada um dos hubs para adicionar uma rota estática para o **Ramo para VNet** (B2V) e **o Ramo para a Internet** (B2I) flui através da Firewall Azure. Branch to Branch não é atualmente suportado através do Azure Firewall.

1. Para a **tabela de rotas padrão do Hub 1:**

    * **Filial para Hub 2 Ramos via AZFW2:** 10.5.0.0/16->AZFW2. Esta configuração configura uma rota para firewall Hub 2.
    * **Filial para Hub 2 VNets via AZFW2:** 10.2.0.0/16->AZFW2. Esta configuração configura uma rota para firewall Hub 2.
    * **Filial para sucursal (Local)/ Sucursal para VNet (Local)/ Sucursal para internet**: 0.0.0.0/0->AZFW1.

2. Para a **tabela de rotas padrão do Hub 2:**

    * Filial para sucursal (Local e Remota)/ Ramo para VNet (Local e Remoto)/ Ramo para a Internet: 0.0.0/0->AZFW2.

Isto resultará em alterações na configuração do encaminhamento, como visto na figura abaixo

**Figura 2**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/workflow.png" alt-text="fluxo de trabalho":::

## <a name="next-steps"></a>Próximos passos

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
* Para obter mais informações sobre como configurar o encaminhamento de hubs virtuais, consulte [Como configurar o encaminhamento do hub virtual](how-to-virtual-hub-routing.md).
