---
title: Usar um bloco de redução personalizada nos painéis do Azure
description: Saiba como adicionar um bloco de redução a um painel do Azure para exibir conteúdo estático
services: azure-portal
keywords: ''
author: mblythe
ms.author: mblythe
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3d2e6d2d0bde76a35a18373fabf64ce36c6c320e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640147"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Use um bloco de redução nos painéis do Azure para mostrar conteúdo personalizado

Você pode adicionar um bloco de redução aos painéis do Azure para exibir conteúdo estático e personalizado. Por exemplo, você pode mostrar instruções básicas, uma imagem ou um conjunto de hiperlinks com um bloco de redução.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Adicionar um bloco de redução ao seu painel

1. Selecione **painel** na barra lateral portal do Azure. Se você criou painéis personalizados, na exibição painel, use a lista suspensa para selecionar o painel em que o bloco de redução personalizada deve aparecer. Selecione o ícone Editar para abrir a **Galeria de blocos**.

   ![Captura de tela mostrando exibição de edição do painel](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. Na **Galeria de blocos**, localize o bloco chamado **redução** e clique em **Adicionar**. O bloco é adicionado ao painel e o painel **Editar redução** é aberto.

1. Edite os campos **título**, **subtítulo**e **conteúdo** para personalizar o bloco. No exemplo mostrado aqui, o bloco de redução foi editado para exibir informações personalizadas sobre o suporte técnico.

   ![Captura de tela mostrando exibição de bloco de redução](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. Selecione **concluído** para ignorar o painel de **redução de edição** . Seu conteúdo será exibido no bloco de redução, que pode ser redimensionado arrastando a alça no canto inferior direito.

   ![Captura de tela mostrando o bloco de redução personalizada](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Redução de limitações e recursos de conteúdo

Você pode usar qualquer combinação de texto sem formatação, sintaxe de redução e conteúdo HTML no bloco de redução. O portal do Azure usa uma biblioteca de software livre chamada _marcada_ para transformar o conteúdo em HTML que é mostrado no bloco. O HTML produzido por _marcado_ é previamente processado pelo portal antes de ser renderizado. Essa etapa ajuda a garantir que sua personalização não afetará a segurança ou o layout do Portal. Durante esse pré-processamento, qualquer parte do HTML que representa uma possível ameaça é removida. Os seguintes tipos de conteúdo não são permitidos pelo portal:

* As marcas JavaScript – `<script>` e as avaliações de JavaScript embutidas serão removidas.
* iframes-`<iframe>` marcações serão removidas.
* As marcas de `<style>` de estilo serão removidas. Atributos de estilo embutidos em elementos HTML não são oficialmente suportados. Você pode achar que alguns elementos de estilo embutidos funcionam para você, mas se interferem no layout do portal, eles podem parar de funcionar a qualquer momento. O bloco de redução destina-se a conteúdo básico e estático que usa os estilos padrão do Portal.

## <a name="next-steps"></a>Passos seguintes

* Para criar um painel personalizado, consulte [criar e compartilhar painéis no portal do Azure](../azure-portal/azure-portal-dashboards.md)
