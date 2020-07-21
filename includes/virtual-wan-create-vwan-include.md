---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/09/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 56f79bf38b627f80d73b59dbbfbb73dddd809458
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525186"
---
Num browser, navegue para o Portal do Azure e inicie sessão com a sua conta do Azure.

1. Navegue para a página Virtual WAN. No portal, clique em **Criar um recurso**. Digite **WAN virtual** na caixa de pesquisa e selecione Enter.
1. Selecione **VIRTUAL WAN** a partir dos resultados. Na página VIRTUAL WAN, clique em **Criar** para abrir a página Create WAN.
1. Na página **Create WAN,** no separador **Básicos,** preencha os seguintes campos:

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="Informações básicas":::

   * **Subscription** (subscrição) - selecione a subscrição que quer utilizar.
   * **Grupo de recursos** - Criar novo ou utilizar existente.
   * **Localização do grupo de recursos** - Escolha uma localização de recurso a partir do dropdown. Uma WAN é um recurso global e não reside numa região específica. Contudo, tem de selecionar uma região para poder gerir e localizar mais facilmente o recurso WAN que criou.
   * **Nome** - Digite o nome que pretende chamar de WAN.
   * **Tipo** - Básico ou Padrão. Se criar um WAN básico, pode criar apenas um hub Básico. Os centros básicos são capazes apenas de conectividade local-a-local VPN.
1. Depois de terminar de preencher os campos, selecione **Review +Create**.
1. Assim que a validação passar, **selecione Criar** para criar o WAN virtual.
