---
title: Integrar um site estático com O CDN Azure - Armazenamento Azure
description: Saiba como cache conteúdo estático do site a partir de uma conta de Armazenamento Azure utilizando a Rede de Entrega de Conteúdos Azure (CDN).
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 01/22/2020
ms.openlocfilehash: 8eeff5187d27cb75b9e55eba8311dede8970bc4a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435225"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integrar um site estático com o Azure CDN

Pode ativar a Rede de Entrega de [Conteúdos Azure (CDN)](../../cdn/cdn-overview.md) para cache conteúdo a partir de um [site estático](storage-blob-static-website.md) que está hospedado numa conta de armazenamento Azure. Pode utilizar o Azure CDN para configurar o ponto final de domínio personalizado para o seu website estático, fornecer certificados Personalizados TLS/SSL e configurar regras de reescrita personalizadas. Configurar o CDN Azure resulta em custos adicionais, mas fornece baixas latenciências consistentes ao seu website de qualquer parte do mundo. O Azure CDN também fornece encriptação TLS com o seu próprio certificado. 

Para obter informações sobre os preços do CDN Azure, consulte [os preços do CDN azure](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="enable-azure-cdn-for-your-static-website"></a>Ative o Azure CDN para o seu site estático

Pode ativar o Azure CDN para o seu website estático diretamente da sua conta de armazenamento. Se quiser especificar definições de configuração avançadas para o ponto final da CDN, como a [otimização da transferência de ficheiros grandes](../../cdn/cdn-optimization-overview.md#large-file-download), pode utilizar a [extensão da CDN do Azure](../../cdn/cdn-create-new-endpoint.md) para criar um perfil e um ponto final de CDN.

1. Localize a sua conta de armazenamento no portal Azure e exiba a visão geral da conta.

2. Selecione **Azure CDN** no menu **do Serviço Blob** para configurar o Azure CDN.

    A página **CDN do Azure** é apresentada.

    ![Criar ponto final da CDN](../../cdn/media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)

3. Na secção de **perfil da CDN,** especifique um novo perfil CDN ou existente. 

4. Especifique um nível de preços para o ponto final da CDN. Para saber mais sobre os preços, consulte os preços da Rede de [Entrega de Conteúdos.](https://azure.microsoft.com/pricing/details/cdn/) Para obter mais informações sobre as funcionalidades disponíveis em cada nível, consulte as [funcionalidades do produto Compare Azure CDN](../../cdn/cdn-features.md).

5. No campo de nome final da **CDN,** especifique um nome para o seu ponto final cdn. O ponto final do CDN deve ser único em Azure.

6. Especifique que é o ponto final do site estático no campo **origin hostname.** 

   Para encontrar o ponto final do seu site estático, navegue para as definições do **site Estática** para a sua conta de armazenamento.  Copie o ponto final primário e cole-o na configuração CDN.

   > [!IMPORTANT]
   > Certifique-se de remover o identificador de protocolo *(por exemplo,* HTTPS) e o corte de rasto no URL. Por exemplo, se o ponto `https://mystorageaccount.z5.web.core.windows.net/`final do `mystorageaccount.z5.web.core.windows.net` site estático for , então especificaria no campo **origin hostname.**

   A imagem que se segue mostra uma configuração de ponto final exemplo:

   ![Screenshot mostrando a configuração do ponto final do CDN da amostra](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

7. Selecione **Criar**e, em seguida, aguarde que se propague. Depois da criação do ponto final, este é apresentado na lista de pontos finais.

8. Para verificar se o ponto final do CDN está configurado corretamente, clique no ponto final para navegar para as suas definições. A partir da visão geral da CDN para a sua conta de armazenamento, localize o nome de anfitrião do ponto final e navegue até ao ponto final, como mostra a seguinte imagem. O formato do seu ponto final `https://staticwebsitesamples.azureedge.net`cdN será semelhante a .

    ![Screenshot mostrando visão geral do ponto final da CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

9. Uma vez concluída a propagação do ponto final do CDN, navegar para o ponto final do CDN mostra o conteúdo do ficheiro index.html que já fez o upload para o seu website estático.

10. Para rever as definições de origem do seu ponto final CDN, navegue para **a Origem** sob a secção **Definições** para o seu ponto final cdN. Verá que o campo **do tipo Origem** está definido para a Origem *Personalizada* e que o campo de nome de **anfitrião Origin** exibe o seu ponto final do site estático.

    ![Screenshot mostrando as definições de Origem para o ponto final do CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Remover conteúdo da CDN do Azure

Se já não pretender ter um objeto em cache na CDN do Azure, poderá efetuar um dos seguintes passos:

* Torne o contentor privado em vez de público. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).
* Desative ou elimine o ponto final da CDN ao utilizar o portal do Azure.
* Modifique o serviço alojado para deixar de responder a pedidos para o objeto.

Um objeto que já esteja em cache na CDN do Azure permanece em cache até o período TTL do objeto expirar ou até o ponto final ser [removido](../../cdn/cdn-purge-endpoint.md). Quando o período TTL expirar, a CDN do Azure determina se o ponto final da CDN ainda é válido e se o objeto ainda é acessível anonimamente. Se não forem, o objeto não já estará em cache.

## <a name="next-steps"></a>Passos seguintes

(Opcional) Adicione um domínio personalizado ao seu ponto final Do CDN Azure. Consulte [Tutorial: Adicione um domínio personalizado ao seu ponto final Azure CDN](../../cdn/cdn-map-content-to-custom-domain.md).
