---
title: Como adicionar um domínio personalizado à sua configuração Azure Front Door Standard/Premium SKU
description: Neste tutorial, você aprenderá a bordo de um domínio personalizado para Azure Front Door Standard/Premium SKU.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 164e06024844fb5262586450b737db9c807e373a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099892"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Crie um domínio personalizado no Azure Front Door Standard/Premium SKU (Preview) utilizando o portal Azure

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

Quando utiliza o Azure Front Door Standard/Premium para entrega de aplicações, é necessário um domínio personalizado se quiser que o seu próprio nome de domínio seja visível nos seus pedidos de utilizador final. Ter um nome de domínio visível pode ser conveniente para os seus clientes e útil para fins de imagem corporativa.

Depois de criar um perfil Azure Front Door Standard/Premium, o anfitrião frontal padrão terá um subdomínio de azurefd.net. Este subdomínio é incluído no URL quando o Azure Front Door Standard/Premium fornece conteúdo do seu backend por padrão. Por exemplo, `https://contoso-frontend.azurefd.net/activeusers.htm`. Para sua comodidade, o Azure Front Door oferece a opção de associar um domínio personalizado ao anfitrião predefinido. Com esta opção, você entrega o seu conteúdo com um domínio personalizado no seu URL em vez de um nome de domínio Azure Front Door Standard/Premium. Por exemplo, https://www.contoso.com/photo.png.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [**termos de utilização suplementares para pré-visualizações do Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos
* Para concluir os passos neste tutorial, tem primeiro de criar um Front Door. Para obter mais informações, consulte [Quickstart: Crie um Standard/Premium da Porta da Frente.](create-front-door-portal.md)

* Se ainda não tem um domínio personalizado, tem primeiro de comprar um com um fornecedor de domínio. Por exemplo, veja [Buy a custom domain name](../../app-service/manage-custom-dns-buy-domain.md) (Comprar um nome de domínio personalizado).

* Se estiver a utilizar o Azure para hospedar os seus [domínios DNS,](../../dns/dns-overview.md)tem de delegar o sistema de nome de domínio do fornecedor de domínio (DNS) num DNS Azure. Para obter mais informações, consulte [delegado de um domínio para Azure DNS](../../dns/dns-delegate-domain-azure-dns.md). Caso contrário, se estiver a utilizar um fornecedor de domínio para manusear o seu domínio DNS, tem de validar manualmente o domínio introduzindo registos DNS TXT solicitados.

## <a name="add-a-new-custom-domain"></a>Adicione um novo domínio personalizado

Um domínio personalizado é gerido pela secção De Domínios no portal. Um domínio personalizado pode ser criado e validado antes da associação a um ponto final. Um domínio personalizado e os seus subdomínios podem ser associados apenas a um único ponto final de cada vez. No entanto, pode utilizar subdomínios diferentes do mesmo domínio personalizado para diferentes Portas Frontais. Também pode mapear domínios personalizados com subdomínios diferentes para o mesmo ponto final da porta frontal.

1. Em Definições para o seu perfil da porta frontal Azure, selecione *Domínios* e, em seguida, adicione um botão **de domínio.**

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Screenshot do botão de domínio adicionado na página de aterragem de domínio.":::

1. Aparecerá a página **de domínio Add a dom** que pode introduzir informações sobre o domínio personalizado. Pode escolher DNS geridos pelo Azure, que é recomendado ou pode optar por utilizar o seu próprio fornecedor de DNS. Se escolher DNS geridos pelo Azure, selecione uma zona DNS existente e, em seguida, selecione um subdomínio personalizado ou crie um novo. Se estiver a utilizar outro fornecedor de DNS, introduza manualmente o nome de domínio personalizado. **Selecione Adicionar** para adicionar o seu domínio personalizado.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Screenshot de adicionar uma página de domínio.":::

    Um novo domínio personalizado é criado com um estado de validação de **Submissão.**

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="Screenshot do estado de validação de domínio submetendo.":::

    Aguarde até que o estado de validação mude para **Pendente**. Esta operação pode demorar alguns minutos.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="Screenshot do estado de validação de domínio pendente.":::

1. Selecione o estado de validação **pendente.** Aparecerá uma nova página com informações de registo DNS TXT necessárias para validar o domínio personalizado. O registo TXT é na forma de `_dnsauth.<your_subdomain>` . Se estiver a utilizar a zona baseada em DNS do Azure, selecione o botão **Add** e será criado um novo registo TXT com o valor de registo apresentado na zona Azure DNS. Se estiver a utilizar outro fornecedor de DNS, crie manualmente um novo registo de nome TXT `dnsauth.<your_subdomain>` com o valor recorde mostrado na página.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Screenshot da página de domínio personalizada validada.":::

1. Selecione o estado da atualização. Uma vez que o domínio seja validado utilizando o registo DNS TXT, o estado de validação mudará para **verificado**. Esta operação pode demorar alguns minutos a validar.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Screenshot de domínio personalizado verificado.":::

1. Feche a página para voltar à página de aterragem da lista de domínios personalizados. O estado de provisionamento do domínio personalizado deve ser alterado para **Estado de Provisionamento** e o estado de validação deverá ser alterado para **Aprovado**.

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Screenshot do estatuto previsto e aprovado.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Associe o domínio personalizado ao ponto final da porta frontal

Depois de validar o seu domínio personalizado, pode então adicioná-lo ao seu ponto final Azure Front Door Standard/Premium.

1. Uma vez validado o domínio personalizado, pode associá-lo a um ponto final e rota Azure Front Door Standard/Premium existente. Selecione o link **de associação Endpoint** para abrir a página **de ponto final e rotas Associado.** Selecione um ponto final e rotas com as quais pretende associar-se. Em seguida, selecione **Associate**. Feche a página assim que a operação de associado estiver concluída.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Screenshot do ponto final associado e página de rotas.":::

    O estado da associação Endpoint deve ser alterado para refletir o ponto final ao qual o domínio personalizado está atualmente associado. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="Screenshot da ligação de associação de ponto final.":::

1. Selecione a ligação de estado DNS.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="Screenshot da ligação estatal DNS.":::

1. A página **de registo Add ou update the CNAME** aparecerá e apresentará as informações de registo CNAME que devem ser fornecidas antes de o tráfego poder começar a fluir. Se estiver a utilizar zonas hospedadas em Azure DNS, os registos CNAME podem ser criados selecionando o botão **Adicionar** na página. Se estiver a utilizar outro fornecedor de DNS, deve introduzir manualmente o nome e o valor do registo CNAME, tal como mostrado na página.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="Screenshot do registo do ADD ou atualização da CNAME.":::

1. Assim que o registo CNAME for criado e o domínio personalizado estiver associado ao ponto final da Porta Frontal Azure, o fluxo de tráfego começará a fluir.

    > [!NOTE]
    > Se o HTTPS estiver ativado, o provisionamento e a propagação dos certificados podem demorar alguns minutos porque a propagação está a ser feita em todos os locais de ponta. 

## <a name="verify-the-custom-domain"></a>Verificar o domínio personalizado

Depois de validar e associar o domínio personalizado, verifique se o domínio personalizado está corretamente referenciado ao seu ponto final.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Screenshot do domínio personalizado validado e associado.":::

Por último, valide que o conteúdo da sua aplicação está a ser servido através de um browser.

## <a name="next-steps"></a>Passos seguintes

Para aprender a ativar HTTPS para o seu domínio personalizado, continue até ao próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar HTTPS para um domínio personalizado]()
