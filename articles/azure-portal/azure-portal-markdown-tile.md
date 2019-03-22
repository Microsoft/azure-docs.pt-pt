---
title: Utilize um mosaico de markdown personalizadas nos dashboards do Azure
description: Saiba como adicionar um mosaico de markdown para um dashboard do Azure para exibir o conteúdo estático
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ec8cbddda4137656a53fd4968c451cd413959274
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58110788"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Utilize um mosaico de markdown no dashboards do Azure para mostrar o conteúdo personalizado

Pode adicionar um mosaico de markdown para os seus dashboards do Azure para exibir conteúdo personalizado, estático. Por exemplo, pode mostrar instruções básicas, uma imagem ou um conjunto de hiperlinks com um mosaico de markdown.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Adicionar um mosaico markdown ao seu dashboard

1. Selecione **Dashboard** na barra lateral portal do Azure. Se tiver criado quaisquer dashboards personalizados, na vista do dashboard, utilize a lista pendente para selecionar o dashboard onde deve aparecer no mosaico markdown personalizada. Selecione o ícone de edição para abrir o **Galeria de mosaicos**.

   ![Captura de ecrã a mostrar exibição de edição dashboard](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. Na **Galeria de mosaicos**, localize o mosaico chamado **Markdown** e clique em **Add**. O mosaico é adicionado ao dashboard e o **editar Markdown** painel abre-se.

1. Editar a **Title**, **subtítulo**, e **conteúdo** campos para personalizar o mosaico. No exemplo mostrado aqui, o mosaico markdown foi editado para apresentar informações de suporte técnico de ajuda personalizada.

   ![Captura de ecrã que mostra a exibição de edição de mosaico markdown](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. Selecione **feito** para descartar a **editar Markdown** painel. Seu conteúdo será apresentado no mosaico Markdown, o que pode ser redimensionado, em seguida, arrastando o identificador no canto inferior direito.

   ![Mosaico de markdown personalizada captura de ecrã a mostrar](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Recursos de conteúdo de markdown e limitações

Pode utilizar qualquer combinação de texto sem formatação, sintaxe de Markdown e conteúdo HTML no mosaico markdown. O portal do Azure utiliza uma biblioteca de código aberto chamada _marcado_ para transformar o seu conteúdo em HTML que é apresentado no mosaico. O HTML produzido pelo _marcado_ previamente é processado pelo portal antes que ele é renderizado. Este passo ajuda a tornar-se de que sua personalização não afeta a segurança ou o layout do portal. Durante esse processamento prévio de, qualquer parte do HTML que constitui uma potencial ameaça é removido. Os seguintes tipos de conteúdo não são permitidos pelo portal:

* JavaScript – `<script>` etiquetas e avaliações de JavaScript inline serão removidas.
* IFrames - `<iframe>` etiquetas serão removidas.
* Guia de estilo – `<style>` etiquetas serão removidas. Atributos de estilo embutidas em elementos HTML oficialmente não são suportados. Pode achar que alguns elementos de estilo inline trabalharem por si, mas se eles interferem com o layout do portal, podem parar de funcionar em qualquer altura. O mosaico Markdown destina-se para o conteúdo básico, estático que usa os estilos predefinidos do portal do.

## <a name="next-steps"></a>Passos Seguintes

* Para criar um dashboard personalizado, consulte [criar e partilhar dashboards no portal do Azure](../azure-portal/azure-portal-dashboards.md)
