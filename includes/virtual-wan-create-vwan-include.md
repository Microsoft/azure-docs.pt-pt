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
ms.openlocfilehash: 62d466e81309765540bcbd52714733b97d241ebc
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93354115"
---
Num browser, navegue para o Portal do Azure e inicie sessão com a sua conta do Azure.

1. Navegue para a página Virtual WAN. No portal, clique em **Criar um recurso**. Digite **WAN virtual** na caixa de pesquisa e selecione **Enter**.
1. Selecione **VIRTUAL WAN** a partir dos resultados. Na página VIRTUAL WAN, selecione **Criar** para abrir a página Create WAN.
1. Na página **Create WAN,** no separador **Básicos,** preencha os seguintes campos:

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="A screenshot mostra o painel Create WAN com o separador Basics selecionado.":::

   * **Subscription** (subscrição) - selecione a subscrição que quer utilizar.
   * **Grupo de recursos** - Criar novo ou utilizar existente.
   * **Localização do grupo de recursos** - Escolha uma localização de recurso a partir do dropdown. Uma WAN é um recurso global e não reside numa região específica. No entanto, deve selecionar uma região para gerir e localizar o recurso WAN que cria.
   * **Nome** - Digite o nome que pretende chamar de WAN.
   * **Tipo** - Básico ou Padrão. Selecione **Standard**. Se selecionar o BASIC VWAN, entenda que os VWANs Básicos só podem conter centros Básicos, o que limita o seu tipo de ligação ao local-a-local.
1. Depois de terminar de preencher os campos, selecione **Review +Create**.
1. Assim que a validação passar, **selecione Criar** para criar o WAN virtual.
