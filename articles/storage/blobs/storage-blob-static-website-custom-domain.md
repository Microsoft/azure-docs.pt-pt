---
title: 'Tutorial: Ativar o domínio personalizado com SSL num Web site estático com o CDN do Azure - armazenamento do Azure'
description: Saiba como configurar um domínio personalizado para o alojamento de Web site estático.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
ms.openlocfilehash: 4b074c9dee93ba44659b0321ae3eee7fbea1c61f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145045"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>Tutorial: Utilizar a CDN do Azure para ativar um domínio personalizado com SSL para um Web site estático

Este tutorial é a segunda parte de uma série. Nela, vai aprender a ativar um ponto final de domínio personalizado com SSL para o seu Web site estático. 

O tutorial mostra como usar [CDN do Azure](../../cdn/cdn-overview.md) para configurar o ponto de final de domínio personalizado para o seu Web site estático. Com a CDN do Azure, pode provisionar certificados personalizados com SSL, utilizar um domínio personalizado e configurar regras de reescrita personalizado todos ao mesmo tempo. Configurar a CDN do Azure resulta em encargos adicionais, mas fornece baixas latências consistentes ao seu Web site em qualquer lugar do mundo. A CDN do Azure também fornece encriptação de SSL com o seu próprio certificado. Para obter informações sobre os preços da CDN do Azure, consulte [preços da CDN do Azure](https://azure.microsoft.com/pricing/details/cdn/).

Na segunda parte da série, saiba como:

> [!div class="checklist"]
> * Criar um ponto final da CDN no ponto de final de Web site estático
> * Ativar o domínio personalizado e SSL

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, preencha a parte um, [Tutorial: Alojar um Web site estático no armazenamento de BLOBs](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão para o [portal do Azure](https://portal.azure.com/) para começar a utilizar.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Criar um ponto final da CDN no ponto de final de Web site estático

1. Abra o [portal do Azure](https://portal.azure.com/) no seu browser. 
1. Localize a sua conta de armazenamento e exibir a descrição geral da conta.
1. Selecione **CDN do Azure** sob a **serviço Blob** menu para configurar a CDN do Azure.
1. Na **novo ponto final** secção, preencha os campos para criar um novo ponto final da CDN.
1. Introduza um nome de ponto final, como *mystaticwebsiteCDN*.
1. Introduza o seu domínio do Web site como o nome de anfitrião para o ponto final da CDN.
1. Introduza o nome de anfitrião de origem, é o ponto de extremidade do Web site estático. Para localizar o ponto de final do Web site estático, navegue para o **Web site estático** secção para a sua conta de armazenamento e copiar o ponto final. 
1. Testar o ponto final de CDN ao navegar até *mywebsitecdn.azureedge.net* no seu browser.

## <a name="enable-custom-domain-and-ssl"></a>Ativar o domínio personalizado e SSL

1. Crie um registo CNAME com o fornecedor de nome de domínio para redirecionar o seu domínio personalizado para o ponto final da CDN. O registo CNAME para o *www* subdomínio deve ser semelhante ao seguinte:

    ![Especificar o registo CNAME para o subdomínio www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. No portal do Azure, clique no ponto final recentemente criado para configurar o domínio personalizado e o certificado SSL.
1. Selecione **Adicionar domínio personalizado** e introduza o seu nome de domínio, em seguida, clique em **Add**.
1. Selecione o mapeamento de domínio personalizado recentemente criado para aprovisionar um certificado SSL.
1. Definir **HTTPS de domínio personalizado** ao **ON**. Selecione **gerida de CDN** ter a CDN do Azure, gerir o seu certificado SSL. Clique em **Guardar**.
1. Teste o seu Web site ao aceder ao seu url do Web site.

## <a name="next-steps"></a>Passos Seguintes

Na segunda parte deste tutorial, aprendeu a configurar um domínio personalizado com SSL na CDN do Azure para o seu Web site estático.

Siga esta ligação para saber mais sobre o alojamento de Web site estático no armazenamento do Azure.

> [!div class="nextstepaction"]
> [Saiba mais sobre os Web sites estáticos](storage-blob-static-website.md)
