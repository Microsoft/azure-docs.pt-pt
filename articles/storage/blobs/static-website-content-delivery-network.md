---
title: Integre um website estático com Azure CDN - Azure Storage
description: Saiba como cache o conteúdo estático do site a partir de uma conta de Armazenamento Azure utilizando a Rede de Entrega de Conteúdos Azure (CDN).
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: 02b7e02c33161db33420e2efe1ef4b70a138d127
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465223"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integrar um website estático com a Azure CDN

Pode ativar a [Rede de Entrega de Conteúdos Azure (CDN)](../../cdn/cdn-overview.md) para cache de conteúdo a partir de um website [estático](storage-blob-static-website.md) que está hospedado numa conta de armazenamento Azure. Pode utilizar o Azure CDN para configurar o ponto final de domínio personalizado para o seu website estático, providenciar certificados TLS/SSL personalizados e configurar regras de reescrita personalizadas. Configurar o Azure CDN resulta em custos adicionais, mas fornece latências baixas consistentes para o seu website de qualquer parte do mundo. O Azure CDN também fornece encriptação TLS com o seu próprio certificado. 

Para obter informações sobre os preços da Azure CDN, consulte [os preços da Azure CDN](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="enable-azure-cdn-for-your-static-website"></a>Ativar o Azure CDN para o seu website estático

Pode ativar o Azure CDN para o seu website estático diretamente a partir da sua conta de armazenamento. Se quiser especificar definições de configuração avançadas para o ponto final da CDN, como a [otimização da transferência de ficheiros grandes](../../cdn/cdn-optimization-overview.md#large-file-download), pode utilizar a [extensão da CDN do Azure](../../cdn/cdn-create-new-endpoint.md) para criar um perfil e um ponto final de CDN.

1. Localize a sua conta de armazenamento no portal Azure e apresente a visão geral da conta.

1. No menu **Blob Service,** selecione **Azure CDN** para abrir a página **Azure CDN:**

    ![Criar ponto final da CDN](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. Na secção **de perfil cdn,** especifique se deve criar um novo perfil cdn ou utilizar um existente. Um perfil CDN é uma coleção de pontos finais da CDN que partilham um nível de preços e fornecedor. Em seguida, insira um nome para o CDN que é único dentro da sua subscrição.

1. Especificar um nível de preços para o ponto final do CDN. Para saber mais sobre os preços, consulte os [preços da Rede de Entrega de Conteúdos.](https://azure.microsoft.com/pricing/details/cdn/) Para obter mais informações sobre as funcionalidades disponíveis em cada nível, consulte [as funcionalidades do produto Compare Azure CDN](../../cdn/cdn-features.md).

1. No campo de **nome do ponto final cdN,** especifique um nome para o seu ponto final CDN. O ponto final do CDN deve ser único em todo o Azure e fornece a primeira parte do URL do ponto final. O formulário valida que o nome do ponto final é único.

1. Especifique o seu ponto final estático no campo **origin hostname.** 

   Para encontrar o ponto final do seu site estático, navegue para as definições do **site Estática** para a sua conta de armazenamento.  Copie o ponto final primário e cole-o na configuração do CDN.

   > [!IMPORTANT]
   > Certifique-se de remover o identificador de protocolo *(por exemplo,* HTTPS) e o corte de fuga no URL. Por exemplo, se o ponto final do site estático `https://mystorageaccount.z5.web.core.windows.net/` for , então você especificaria `mystorageaccount.z5.web.core.windows.net` no campo de nome de hospedeiro **Origin.**

   A imagem a seguir mostra uma configuração de ponto final de exemplo:

   ![Screenshot mostrando a configuração do ponto final cdN da amostra](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Selecione **Criar**e, em seguida, aguarde que o CDN provisa. Depois da criação do ponto final, este é apresentado na lista de pontos finais. (Se tiver algum erro na forma, aparece um ponto de exclamação ao lado desse campo.)

1. Para verificar se o ponto final do CDN está configurado corretamente, clique no ponto final para navegar para as suas definições. A partir da visão geral do CDN para a sua conta de armazenamento, localize o nome de anfitrião do ponto final e navegue até ao ponto final, como mostra a imagem seguinte. O formato do seu ponto final CDN será semelhante a `https://staticwebsitesamples.azureedge.net` .

    ![Screenshot mostrando visão geral do ponto final do CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. Uma vez previsto o ponto final do CDN, navegar para o ponto final da CDN exibe o conteúdo do ficheiro index.html que já carregou para o seu website estático.

1. Para rever as definições de origem do seu ponto final CDN, navegue para **a Origem** sob a secção **Definições** para o seu ponto final CDN. Verá que o campo **do tipo Origin** está definido para *Origem Personalizada* e que o campo de nome de **anfitrião Origin** exibe o seu ponto final estático do site.

    ![Screenshot mostrando definições de origem para ponto final CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Remover conteúdo da CDN do Azure

Se já não pretender ter um objeto em cache na CDN do Azure, poderá efetuar um dos seguintes passos:

* Torne o contentor privado em vez de público. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).
* Desative ou elimine o ponto final da CDN ao utilizar o portal do Azure.
* Modifique o serviço alojado para deixar de responder a pedidos para o objeto.

Um objeto que já esteja em cache na CDN do Azure permanece em cache até o período TTL do objeto expirar ou até o ponto final ser [removido](../../cdn/cdn-purge-endpoint.md). Quando o período TTL expirar, a CDN do Azure determina se o ponto final da CDN ainda é válido e se o objeto ainda é acessível anonimamente. Se não forem, o objeto não já estará em cache.

## <a name="next-steps"></a>Passos seguintes

(Opcional) Adicione um domínio personalizado ao seu ponto final Azure CDN. Ver [Tutorial: Adicione um domínio personalizado ao seu ponto final Azure CDN](../../cdn/cdn-map-content-to-custom-domain.md).
