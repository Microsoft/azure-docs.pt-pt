---
title: 'Tutorial: habilitar o domínio personalizado & SSL para um site estático no Azure'
description: Saiba como configurar um domínio personalizado para o alojamento de Web site estático.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 893ac53dc9f0b6b162c5ec22e478cd15706e50fb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327502"
---
# <a name="tutorial-enable-custom-domain--ssl-for-a-static-website-in-azure"></a>Tutorial: habilitar o domínio personalizado & SSL para um site estático no Azure

Este tutorial é a segunda parte de uma série. Nela, vai aprender a ativar um ponto final de domínio personalizado com SSL para o seu Web site estático. 

O tutorial mostra como usar a [CDN do Azure](../../cdn/cdn-overview.md) para configurar o ponto de extremidade de domínio personalizado para seu site estático. Com a CDN do Azure, pode provisionar certificados personalizados com SSL, utilizar um domínio personalizado e configurar regras de reescrita personalizado todos ao mesmo tempo. Configurar a CDN do Azure resulta em encargos adicionais, mas fornece baixas latências consistentes ao seu Web site em qualquer lugar do mundo. A CDN do Azure também fornece encriptação de SSL com o seu próprio certificado. Para obter informações sobre os preços da CDN do Azure, consulte [preços da CDN do Azure](https://azure.microsoft.com/pricing/details/cdn/).

Na segunda parte da série, saiba como:

> [!div class="checklist"]
> * Criar um ponto final da CDN no ponto de final de Web site estático
> * Ativar o domínio personalizado e SSL

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, conclua a parte 1, [tutorial: hospedar um site estático no armazenamento de BLOBs](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Entre no [portal do Azure](https://portal.azure.com/) para começar.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Criar um ponto final da CDN no ponto de final de Web site estático

1. Localize sua conta de armazenamento no portal do Azure e exiba a visão geral da conta.
1. Selecione **CDN do Azure** no menu **serviço blob** para configurar a CDN do Azure.
1. Na seção **perfil CDN** , especifique um perfil CDN novo ou existente. Para obter mais informações, veja [Início Rápido: Criar um perfil e um ponto final da CDN do Azure](../../cdn/cdn-create-new-endpoint.md).
1. Especifique um tipo de preço para o ponto de extremidade da CDN. Este tutorial usa o tipo de preço **Standard Akamai** , pois ele é propagado rapidamente, normalmente em alguns minutos. Outros tipos de preço podem levar mais tempo para serem propagados, mas também podem oferecer outras vantagens. Para obter mais informações, consulte [comparar recursos do produto CDN do Azure](../../cdn/cdn-features.md).
1. No campo **nome do ponto de extremidade da CDN** , especifique um nome para o ponto de extremidade da CDN. O ponto de extremidade da CDN deve ser exclusivo no Azure.
1. Especifique que você é o ponto de extremidade estático do site no campo **nome do host de origem** . Para localizar o ponto de extremidade do site estático, navegue até as configurações de **site estático** para sua conta de armazenamento. Copie o ponto de extremidade primário e cole-o na configuração da CDN, removendo o identificador de protocolo (*por exemplo*, HTTPS).

    A imagem a seguir mostra uma configuração de ponto de extremidade de exemplo:

    ![Captura de tela mostrando exemplo de configuração de ponto de extremidade CDN](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Crie o ponto de extremidade da CDN e aguarde sua propagação.
1. Para verificar se o ponto de extremidade CDN está configurado corretamente, clique no ponto de extremidade para navegar até suas configurações. Na visão geral da CDN para sua conta de armazenamento, localize o nome do host do ponto de extremidade e navegue até o ponto de extremidade, conforme mostrado na imagem a seguir. O formato do ponto de extremidade da CDN será semelhante a `https://staticwebsitesamples.azureedge.net`.

    ![Captura de tela mostrando visão geral do ponto de extremidade CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Depois que a propagação do ponto de extremidade da CDN for concluída, navegar até o ponto de extremidade da CDN exibirá o conteúdo do arquivo index. html que você carregou anteriormente em seu site estático.

1. Para examinar as configurações de origem do ponto de extremidade da CDN, navegue até **origem** na seção **configurações** do ponto de extremidade da CDN. Você verá que o campo **tipo de origem** está definido como *origem personalizada* e que o campo **nome do host de origem** exibe seu ponto de extremidade de site estático.

    ![Captura de tela mostrando as configurações de origem do ponto de extremidade CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Ativar o domínio personalizado e SSL

1. Crie um registo CNAME com o fornecedor de nome de domínio para redirecionar o seu domínio personalizado para o ponto final da CDN. O registro CNAME para o subdomínio *www* deve ser semelhante ao seguinte:

    ![Especificar o registo CNAME para o subdomínio www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. No portal do Azure, exiba as configurações para o ponto de extremidade da CDN. Navegue até **domínios personalizados** em **configurações** para configurar o domínio personalizado e o certificado SSL.
1. Selecione **Adicionar domínio personalizado** e insira seu nome de domínio e clique em **Adicionar**.
1. Selecione o novo mapeamento de domínio personalizado para provisionar um certificado SSL.
1. Defina **domínio personalizado https** como **ativado**e clique em **salvar**. Pode levar várias horas para configurar seu domínio personalizado. O portal exibe o progresso conforme mostrado na imagem a seguir.

    ![Captura de tela mostrando o progresso da configuração de domínio personalizado](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Teste o mapeamento do seu site estático para seu domínio personalizado acessando a URL para seu domínio personalizado.

Para obter mais informações sobre como habilitar HTTPS para domínios personalizados, consulte [tutorial: configurar HTTPS em um domínio personalizado da CDN do Azure](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Passos seguintes

Na segunda parte deste tutorial, aprendeu a configurar um domínio personalizado com SSL na CDN do Azure para o seu Web site estático.

Para obter mais informações sobre como configurar e usar a CDN do Azure, consulte [o que é a CDN do Azure?](../../cdn/cdn-overview.md).