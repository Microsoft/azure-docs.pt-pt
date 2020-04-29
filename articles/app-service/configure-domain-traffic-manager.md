---
title: Configure os nomes do DNS com o Gestor de Tráfego
description: Aprenda a configurar um domínio personalizado para uma aplicação Azure App Service que se integra com o Traffic Manager para equilibrar a carga.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8322c12669e41fc7c9aa88e99f95cf1b26ea87d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78944137"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Configure um nome de domínio personalizado no Serviço de Aplicações Azure com integração do Gestor de Tráfego

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Para serviços de nuvem, consulte [Configurar um nome de domínio personalizado para um serviço](../cloud-services/cloud-services-custom-domain-name.md)de nuvem Azure .

Quando utiliza o [Gestor de Tráfego Azure](/azure/traffic-manager/) para carregar o tráfego de equilíbrio para o Serviço de [Aplicações Azure,](overview.md)a aplicação do Serviço de Aplicações pode ser acedida utilizando o ** \<ponto final do traffic-manager>.trafficmanager.net**. Pode atribuir um nome de domínio\.personalizado, como www contoso.com, com a sua app App Service, de forma a fornecer um nome de domínio mais reconhecível para os seus utilizadores.

Este artigo mostra-lhe como configurar um nome de domínio personalizado com uma aplicação do App Service que está integrada com [o Traffic Manager](../traffic-manager/traffic-manager-overview.md).

> [!NOTE]
> Apenas os registos [CNAME](https://en.wikipedia.org/wiki/CNAME_record) são suportados quando configura um nome de domínio utilizando o ponto final do Gestor de Tráfego. Como os registos A não são suportados, um mapeamento de domínio raiz, como contoso.com também não é suportado.
> 

## <a name="prepare-the-app"></a>Preparar a aplicação

Para mapear um nome DNS personalizado para uma aplicação integrada com o Azure Traffic Manager, o plano de Serviço de [Aplicações](https://azure.microsoft.com/pricing/details/app-service/) da aplicação web deve estar em nível **Standard** ou superior. Neste passo, vai confirmar que a aplicação do Serviço de Aplicações está no escalão de preço suportado.

### <a name="check-the-pricing-tier"></a>Verificar o escalão de preço

No [portal Azure,](https://portal.azure.com)procure e selecione Serviços de **Aplicações.**

Na página de Serviços de **Aplicações,** selecione o nome da sua aplicação Azure.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Na navegação à esquerda da página da aplicação, selecione Scale up (plano de serviço de **aplicações)**.

![Menu de aumento vertical](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

O escalão atual da aplicação é realçado com um limite azul. Verifique se a aplicação está no nível **Standard** ou superior (qualquer nível na categoria **Produção** ou **Isolado).** Se sim, feche a página **Scale up** e salte para Criar [o mapeamento CNAME](#create-the-cname-mapping).

![Verificar o escalão de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Aumentar verticalmente o plano do Serviço de Aplicações

Se precisar de aumentar a sua aplicação, selecione qualquer um dos níveis de preços na categoria **Produção.** Para obter opções adicionais, clique em **Ver opções adicionais**.

Clique em **Aplicar**.

## <a name="create-traffic-manager-endpoint"></a>Criar ponto final do Gestor de Tráfego

Seguindo os passos em [Add ou Delete Endpoints,](../traffic-manager/traffic-manager-endpoints.md)adicione a sua aplicação app Service como um ponto final no perfil do Gestor de Tráfego.

Uma vez que a sua aplicação App Service está num nível de preços suportado, aparece na lista de alvos disponíveis do Serviço de Aplicações quando adiciona o ponto final. Se a sua aplicação não estiver listada, [verifique o nível de preços da sua aplicação.](#prepare-the-app)

## <a name="create-the-cname-mapping"></a>Criar o mapeamento CNAME
> [!NOTE]
> Para configurar um [domínio do Serviço de Aplicações que adquiriu,](manage-custom-dns-buy-domain.md)ignore esta secção e vá para o domínio personalizado [Enable](#enable-custom-domain).
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Embora as especificidades de cada fornecedor de domínio variem, você mapeia *desde* o nome de domínio personalizado (como **contoso.com)** *até o* nome de domínio do Gestor de Tráfego **(contoso.trafficmanager.net**) que está integrado com a sua aplicação.

> [!NOTE]
> Se um registo já estiver a ser utilizado e precisar de ligar preventivamente as suas aplicações ao mesmo, pode criar um registo CNAME adicional. Por exemplo, para ligar preventivamente **o contoso.com\.** à sua aplicação, crie um registo CNAME a partir de **awcheck.www** para **contoso.trafficmanager.net**. Em seguida, pode\.adicionar "www contoso.com" à sua aplicação sem a necessidade de alterar o registo "www" CNAME. Para mais informações, consulte [Migrate um nome DNS ativo para o Serviço de Aplicações Azure](manage-custom-dns-migrate-domain.md).

Depois de ter terminado de adicionar ou modificar os registos de DNS no seu fornecedor de domínio, guarde as alterações.

## <a name="enable-custom-domain"></a>Ativar domínio personalizado
Depois de se terem propagado os registos do seu nome de domínio, utilize o navegador para verificar se o seu nome de domínio personalizado se resolve na sua aplicação App Service.

> [!NOTE]
> Pode levar algum tempo para o seu CNAME propagar através do sistema DNS. Pode utilizar um serviço <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> de forma a verificar se o CNAME está disponível.
> 
> 

1. Uma vez que a resolução de domínio suceda, volte à sua página de aplicações no [Portal Azure](https://portal.azure.com)
2. A partir da navegação à esquerda, selecione **Domínios Personalizados** > **Adicione nome de anfitrião**.
4. Digite o nome de domínio personalizado que mapeou anteriormente e selecione **Validar**.
5. Certifique-se de que o tipo de **registo do Nome anfitrião** está definido para **CNAME\.(www example.com ou qualquer subdomínio)**.

6. Uma vez que a aplicação do Serviço de Aplicações está agora integrada com um ponto final do Traffic Manager, deve ver o nome de domínio do Gestor de Tráfego sob **a configuração CNAME**. Selecione-o e clique em **Adicionar domínio personalizado**.

    ![Adicionar o nome DNS à aplicação](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Proteger um nome DNS personalizado com um enlace SSL no Serviço de Aplicações do Azure](configure-ssl-bindings.md)
