---
title: Usar um bloco de redução personalizada nos painéis do Azure
description: Saiba como adicionar um bloco de redução a um painel do Azure para exibir conteúdo estático
services: azure-portal
keywords: ''
author: mblythe
ms.author: mblythe
ms.date: 01/08/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 17d86b7c89ea5fb24c2adea22c5047c3e1ac3b6f
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832635"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Use um bloco de redução nos painéis do Azure para mostrar conteúdo personalizado

Você pode adicionar um bloco de redução aos painéis do Azure para exibir conteúdo estático e personalizado. Por exemplo, você pode mostrar instruções básicas, uma imagem ou um conjunto de hiperlinks em um bloco de redução.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Adicionar um bloco de redução ao seu painel

1. Selecione **painel** na barra lateral portal do Azure.

   ![Captura de tela mostrando a barra lateral do portal](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Se você criou painéis personalizados, na exibição painel, use a lista suspensa para selecionar o painel em que o bloco de redução personalizada deve aparecer. Selecione o ícone Editar para abrir a **Galeria de blocos**.

   ![Captura de tela mostrando exibição de edição do painel](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. Na **Galeria de blocos**, localize o bloco chamado **redução** e selecione **Adicionar**. O bloco é adicionado ao painel e o painel **Editar redução** é aberto.

1. Insira valores para **título** e **subtítulo**, que são exibidos no bloco depois de você passar para outro campo.

   ![Captura de tela mostrando os resultados da inserção do título e do subtítulo](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Selecione uma das opções para incluir o conteúdo de redução: **edição embutida** ou **Inserir conteúdo usando a URL**.

   - Selecione **edição embutida** se desejar inserir a redução diretamente.

      ![Captura de tela mostrando a inserção de conteúdo embutido](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Selecione **Inserir conteúdo usando a URL** se você quiser usar o conteúdo de redução existente que está hospedado online.

      ![Captura de tela mostrando a inserção da URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Para maior segurança, você pode criar um arquivo de redução e armazená-lo em um [BLOB da conta de armazenamento do Azure em que a criptografia está habilitada](../storage/common/storage-service-encryption.md)e, em seguida, apontar para o arquivo usando a opção URL. O conteúdo de redução é criptografado por meio das opções de criptografia da conta de armazenamento. Somente os usuários com permissões para o arquivo podem ver o conteúdo de redução no painel.

1. Selecione **concluído** para ignorar o painel de **redução de edição** . Seu conteúdo aparece no bloco de redução, que você pode redimensionar arrastando a alça no canto inferior direito.

   ![Captura de tela mostrando o bloco de redução personalizada](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Redução de limitações e recursos de conteúdo

Você pode usar qualquer combinação de texto sem formatação, sintaxe de redução e conteúdo HTML no bloco de redução. O portal do Azure usa uma biblioteca de software livre chamada _marcada_ para transformar o conteúdo em HTML que é mostrado no bloco. O HTML produzido por _marcado_ é previamente processado pelo portal antes de ser renderizado. Essa etapa ajuda a garantir que sua personalização não afetará a segurança ou o layout do Portal. Durante esse pré-processamento, qualquer parte do HTML que representa uma possível ameaça é removida. Os seguintes tipos de conteúdo não são permitidos pelo portal:

* As marcas JavaScript – `<script>` e as avaliações de JavaScript embutidas serão removidas.
* iframes-`<iframe>` marcações serão removidas.
* As marcas de `<style>` de estilo serão removidas. Atributos de estilo embutidos em elementos HTML não são oficialmente suportados. Você pode achar que alguns elementos de estilo embutidos funcionam para você, mas se interferem no layout do portal, eles podem parar de funcionar a qualquer momento. O bloco de redução destina-se a conteúdo básico e estático que usa os estilos padrão do Portal.

## <a name="next-steps"></a>Passos seguintes

* Para criar um painel personalizado, consulte [criar e compartilhar painéis no portal do Azure](../azure-portal/azure-portal-dashboards.md)
