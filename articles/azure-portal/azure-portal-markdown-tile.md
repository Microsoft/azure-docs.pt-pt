---
title: Use um azulejo de marcação personalizado nos tabliers Azure
description: Aprenda a adicionar um azulejo de marcação a um dashboard Azure para exibir conteúdo estático
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/08/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 74102423461a56bb6fc19c2eb9874f96a76e34e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310718"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Use um azulejo de marcação nos dashboards do Azure para mostrar conteúdo personalizado

Pode adicionar um azulejo de marcação aos seus painéis Azure para exibir conteúdo personalizado e estático. Por exemplo, pode mostrar instruções básicas, uma imagem ou um conjunto de hiperligações num azulejo de marcação.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Adicione um azulejo de marcação ao seu painel de instrumentos

1. Selecione **Dashboard** a partir da barra lateral do portal Azure.

   ![Screenshot mostrando barra lateral do portal](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Se criou algum dashboard personalizado, na vista do painel de instrumentos, utilize a queda para selecionar o painel onde deve aparecer o azulejo de marcação personalizado. Selecione o ícone de edição para abrir a **Tile Gallery**.

   ![Screenshot mostrando vista de edição do painel de instrumentos](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. Na **Tile Gallery,** localize o azulejo chamado **Markdown** e selecione **Adicionar**. O azulejo é adicionado ao painel de instrumentos e abre o painel **Edit Markdown.**

1. Introduza valores para **Título** e **Legenda,** que exibem no azulejo depois de se mudar para outro campo.

   ![Screenshot mostrando resultados de entrada no título e legenda](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Selecione uma das opções para incluir o conteúdo de marcação: **Edição inline** ou **inserção de conteúdo utilizando URL**.

   - Selecione **edição Inline** se quiser introduzir o markdown diretamente.

      ![Screenshot mostrando entrar em conteúdo inline](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Selecione **inserir conteúdo utilizando URL** se pretender utilizar o conteúdo de marcação existente que está hospedado online.

      ![Screenshot mostrando a entrada de URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Para maior segurança, pode criar um ficheiro de marcação e armazená-lo numa conta de [armazenamento Azure onde a encriptação está ativada](../storage/common/storage-service-encryption.md)e, em seguida, apontar para o ficheiro utilizando a opção URL. O conteúdo de marcação é encriptado através das opções de encriptação da conta de armazenamento. Apenas os utilizadores com permissões no ficheiro podem ver o conteúdo de marcação no painel de instrumentos.

1. Selecione **Feito** para descartar o painel **de edição markdown.** O seu conteúdo aparece no azulejo Markdown, que pode redimensionar arrastando a pega no canto inferior direito.

   ![Screenshot mostrando azulejo de marcação personalizado](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Capacidades e limitações de conteúdo de redução

Pode utilizar qualquer combinação de texto simples, sintaxe de Markdown e conteúdo HTML no azulejo de marcação. O portal Azure utiliza uma biblioteca de código aberto _chamada marcada_ para transformar o seu conteúdo em HTML que é mostrado no azulejo. O HTML produzido por _marca_ é pré-processado pelo portal antes de ser renderizado. Este passo ajuda a garantir que a sua personalização não afetará a segurança ou o layout do portal. Durante esse pré-processamento, qualquer parte do HTML que represente uma ameaça potencial é removida. Os seguintes tipos de conteúdo não são permitidos pelo portal:

* JavaScript `<script>` – etiquetas e avaliações javaScript inline serão removidos.
* iframes `<iframe>` - as etiquetas serão removidas.
* Estilo `<style>` - as etiquetas serão removidas. Os atributos de estilo inline em elementos HTML não são suportados oficialmente. Pode descobrir que alguns elementos de estilo inline funcionam para si, mas se interferirem com o layout do portal, podem deixar de trabalhar a qualquer momento. O azulejo Markdown destina-se a conteúdo básico e estático que utiliza os estilos padrão do portal.

## <a name="next-steps"></a>Passos seguintes

* Para criar um dashboard personalizado, consulte [Criar e partilhar dashboards no portal Azure](../azure-portal/azure-portal-dashboards.md)
