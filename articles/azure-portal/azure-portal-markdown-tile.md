---
title: Use um azulejo de marcação personalizado em dashboards Azure
description: Saiba como adicionar um azulejo de marcação a um painel Azure para exibir conteúdo estático
ms.date: 01/08/2020
ms.topic: how-to
ms.custom: devx-track-js
ms.openlocfilehash: 5121142ecf568aa1ac9a7ec19f7211c6f9a6253f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745762"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Use um azulejo de marcação nos dashboards Azure para mostrar conteúdo personalizado

Pode adicionar um azulejo de marcação aos seus dashboards Azure para exibir conteúdo estático personalizado. Por exemplo, pode mostrar instruções básicas, uma imagem ou um conjunto de hiperligações num azulejo de marcação.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Adicione um azulejo de marcação ao seu painel de instrumentos

1. Selecione **Dashboard** a partir da barra lateral do portal Azure.

   ![Screenshot mostrando barra lateral do portal](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Se criou painéis personalizados, na vista do painel de instrumentos, utilize o drop-down para selecionar o painel de instrumentos onde deve aparecer o azulejo de marcação personalizado. Selecione o ícone de edição para abrir a **Galeria Tile**.

   ![Screenshot mostrando a vista de edição do painel de instrumentos](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. Na **Galeria Tile,** localize o azulejo chamado **Markdown** e selecione **Add**. O azulejo é adicionado ao painel de instrumentos e o painel **de edição de markdown** abre.

1. Introduza valores para **Título** e **Legenda,** que exibem no azulejo depois de se deslocar para outro campo.

   ![Screenshot mostrando os resultados da entrada do título e da legenda](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Selecione uma das opções para incluir o conteúdo de marcação: **Edição inline ou** **inserir conteúdo usando URL**.

   - Selecione **Inline editar** se pretender introduzir o marcador diretamente.

      ![Screenshot mostrando o conteúdo inline](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - **Selecione Insira o conteúdo usando URL** se pretender utilizar o conteúdo de marcação existente que está alojado online.

      ![Screenshot mostrando URL de entrada](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Para uma maior segurança, pode criar um ficheiro de marcação e armazená-lo numa [bolha de conta de armazenamento Azure onde a encriptação está ativada,](../storage/common/storage-service-encryption.md)e depois apontar para o ficheiro utilizando a opção URL. O conteúdo de marcação é encriptado através das opções de encriptação da conta de armazenamento. Apenas os utilizadores com permissões para o ficheiro podem ver o conteúdo de marcação no painel de instrumentos. Poderá ser necessário definir uma regra [de partilha de recursos de origem cruzada (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) na conta de armazenamento para que o portal Azure possa aceder ao ficheiro de _https://portal.azure.com/_ marcação na bolha.

1. Selecione **Feito** para descartar o **painel de ponto de edição.** O seu conteúdo aparece no azulejo markdown, que pode redimensionar arrastando a pega no canto inferior direito.

   ![Screenshot mostrando azulejo de marcação personalizado](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Capacidades e limitações de conteúdo de markdown

Pode utilizar qualquer combinação de texto simples, sintaxe markdown e conteúdo HTML no azulejo de marcação. O portal Azure utiliza uma biblioteca de código aberto chamada _marcada_ para transformar o seu conteúdo em HTML que é mostrado no azulejo. O HTML produzido por _marcado_ é pré-processado pelo portal antes de ser renderizado. Este passo ajuda a garantir que a sua personalização não afetará a segurança ou disposição do portal. Durante o pré-processamento, qualquer parte do HTML que represente uma ameaça potencial é removida. Os seguintes tipos de conteúdo não são permitidos pelo portal:

* JavaScript – `<script>` tags e avaliações javaScript inline serão removidas.
* iframes - `<iframe>` as etiquetas serão removidas.
* Estilo - `<style>` as etiquetas serão removidas. Os atributos de estilo inline em elementos HTML não são oficialmente suportados. Pode descobrir que alguns elementos de estilo inline funcionam para si, mas se interferirem com o layout do portal, podem deixar de funcionar a qualquer momento. O azulejo markdown destina-se a conteúdo básico e estático que utiliza os estilos padrão do portal.

## <a name="next-steps"></a>Passos seguintes

* Para criar um dashboard personalizado, consulte [Criar e partilhar dashboards no portal Azure](../azure-portal/azure-portal-dashboards.md)
