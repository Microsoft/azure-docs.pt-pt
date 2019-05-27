---
title: 'Tutorial: Ativar o domínio personalizado com SSL num Web site estático com o CDN do Azure - armazenamento do Azure'
description: Saiba como configurar um domínio personalizado para o alojamento de Web site estático.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
ms.openlocfilehash: 2b0bb94be2ba8ea983cda8fd015d05fcd532f2bc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226107"
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

1. Localize a sua conta de armazenamento no portal do Azure e exibir a descrição geral da conta.
1. Selecione **CDN do Azure** sob a **serviço Blob** menu para configurar a CDN do Azure.
1. Na **perfil da CDN** secção, especifique um perfil CDN novo ou existente. Para obter mais informações, consulte [início rápido: Criar um perfil de CDN do Azure e o ponto final](../../cdn/cdn-create-new-endpoint.md).
1. Especifica um escalão de preço para o ponto final CDN. Este tutorial utiliza a **Standard da Akamai** escalão de preço, porque ele é propagada rapidamente, normalmente dentro de alguns minutos. Outros escalões de preços podem demorar mais tempo a propagar, mas também poderá oferecer outras vantagens. Para obter mais informações, consulte [funcionalidades do produto da CDN do Azure de comparar](../../cdn/cdn-features.md).
1. Na **nome do ponto final CDN** campo, especifique um nome para o ponto final da CDN. Ponto final da CDN tem de ser exclusivo em todo o Azure.
1. Especifique a é o ponto final de Web site estático no **nome do anfitrião** campo. Para localizar o ponto de final do Web site estático, navegue para o **Web site estático** definições para a sua conta de armazenamento. Copie o ponto final primário e cole-o para a configuração da CDN, removendo o identificador de protocolo (*por exemplo,* , HTTPS).

    A imagem seguinte mostra um exemplo de configuração de ponto final:

    ![Captura de ecrã que mostra configuração de ponto final de CDN de exemplo](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Criar o ponto final CDN e aguarde pela-lo para propagar.
1. Para verificar se o ponto final da CDN está configurado corretamente, clique no ponto final para navegar para as respetivas definições. Desde a descrição geral da CDN para a sua conta de armazenamento, localize o nome de anfitrião do ponto final e navegue para o ponto de extremidade, conforme mostrado na imagem seguinte. O formato do ponto final da CDN será semelhante a `https://staticwebsitesamples.azureedge.net`.

    ![Descrição geral de mostrar captura de ecrã do ponto final CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Depois de concluída a propagação do ponto final CDN, navegar para o ponto final da CDN exibe o conteúdo do ficheiro Index. HTML que tenha carregado anteriormente para o seu Web site estático.

1. Para rever as definições de origem para o ponto final da CDN, navegue para **Origin** sob a **definições** secção para o ponto final da CDN. Verá que o **tipo de origem** campo é definido como *origem personalizada* e que o **nome do anfitrião** campo apresenta o ponto de final do Web site estático.

    ![Captura de ecrã que mostra as definições da origem para o ponto final CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Ativar o domínio personalizado e SSL

1. Crie um registo CNAME com o fornecedor de nome de domínio para redirecionar o seu domínio personalizado para o ponto final da CDN. O registo CNAME para o *www* subdomínio deve ser semelhante ao seguinte:

    ![Especificar o registo CNAME para o subdomínio www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. No portal do Azure, visualize as definições para o ponto final da CDN. Navegue para **domínios personalizados** sob **definições** para configurar o domínio personalizado e o certificado SSL.
1. Selecione **Adicionar domínio personalizado** e introduza o seu nome de domínio, em seguida, clique em **Add**.
1. Selecione o novo mapeamento de domínio personalizado para aprovisionar um certificado SSL.
1. Definir **HTTPS de domínio personalizada** para **ON**, em seguida, clique em **guardar**. Pode demorar várias horas para configurar o seu domínio personalizado. O portal apresenta o progresso, conforme mostrado na imagem seguinte.

    ![Captura de ecrã que mostra o progresso da configuração de domínio personalizado](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Teste o mapeamento do seu Web site estático ao seu domínio personalizado ao aceder ao URL para o seu domínio personalizado.

Para obter mais informações sobre como ativar o HTTPS para domínios personalizados, consulte [Tutorial: Configurar HTTPS num domínio personalizado da CDN do Azure](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Passos Seguintes

Na segunda parte deste tutorial, aprendeu a configurar um domínio personalizado com SSL na CDN do Azure para o seu Web site estático.

Para obter mais informações sobre como configurar e utilizar a CDN do Azure, consulte [o que é o Azure CDN?](../../cdn/cdn-overview.md).