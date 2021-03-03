---
title: Configurar nomes DNS com Gestor de Tráfego
description: Saiba como configurar um domínio personalizado para uma aplicação Azure App Service que se integra com o Traffic Manager para equilibrar a carga.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 2910ea3f896ba3920126737965ca9c9dbabcfeb3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709109"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Configure um nome de domínio personalizado no Azure App Service com integração de Gestor de Tráfego

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Para serviços na nuvem, consulte [configurar um nome de domínio personalizado para um serviço de nuvem Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

Quando utiliza [o Azure Traffic Manager](../traffic-manager/index.yml) para carregar o tráfego de equilíbrio para o [Azure App Service,](overview.md)a aplicação do Serviço de Aplicações pode ser acedida através **\<traffic-manager-endpoint> de .trafficmanager.net**. Pode atribuir um nome de domínio personalizado, como o \. contoso.com, com a sua aplicação App Service, de forma a fornecer um nome de domínio mais reconhecível para os seus utilizadores.

Este artigo mostra-lhe como configurar um nome de domínio personalizado com uma aplicação de Serviço de Aplicações que está integrada com [o Traffic Manager.](../traffic-manager/traffic-manager-overview.md)

> [!NOTE]
> Apenas os registos [CNAME](https://en.wikipedia.org/wiki/CNAME_record) são suportados quando configura um nome de domínio utilizando o ponto final do Gestor de Tráfego. Como os registos A não são suportados, um mapeamento de domínio de raiz, como contoso.com também não é suportado.
> 

## <a name="prepare-the-app"></a>Preparar a aplicação

Para mapear um nome DNS personalizado para uma aplicação que esteja integrada com o Azure Traffic Manager, o plano de Serviço de [Aplicações](https://azure.microsoft.com/pricing/details/app-service/) da web deve estar no nível **Standard** ou superior. Neste passo, vai confirmar que a aplicação do Serviço de Aplicações está no escalão de preço suportado.

### <a name="check-the-pricing-tier"></a>Verificar o escalão de preço

No [portal Azure,](https://portal.azure.com)procure e selecione **Serviços de Aplicações.**

Na página **Serviços de Aplicações,** selecione o nome da sua aplicação Azure.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Na navegação à esquerda da página da aplicação, selecione **Scale up (Plano de Serviço de Aplicações)**.

![Menu de aumento vertical](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

O escalão atual da aplicação é realçado com um limite azul. Verifique se a aplicação está no nível **Standard** ou acima (qualquer nível na categoria **De Produção** ou **Isolado).** Se sim, feche a página **'Escala',** e salte para [criar o mapeamento CNAME](#create-the-cname-mapping).

![Verificar o escalão de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Aumentar verticalmente o plano do Serviço de Aplicações

Se precisar de aumentar a sua aplicação, selecione qualquer um dos níveis de preços na categoria **DeProdução.** Para obter opções adicionais, clique em **Ver opções adicionais**.

Clique em **Aplicar**.

## <a name="create-traffic-manager-endpoint"></a>Criar ponto final do Gestor de Tráfego

Seguindo os passos no [Add or Delete Endpoints,](../traffic-manager/traffic-manager-manage-endpoints.md)adicione a sua aplicação Serviço de Aplicações como ponto final no seu perfil de Gestor de Tráfego.

Uma vez que a sua aplicação De Serviço de Aplicações está num nível de preços suportado, ele aparece na lista de alvos disponíveis do Serviço de Aplicações quando adiciona o ponto final. Se a sua aplicação não estiver listada, [verifique o nível de preços da sua aplicação.](#prepare-the-app)

## <a name="create-the-cname-mapping"></a>Crie o mapeamento CNAME
> [!NOTE]
> Para configurar um [domínio de Serviço de Aplicações que adquiriu,](manage-custom-dns-buy-domain.md)salte esta secção e vá para [Ativar o domínio personalizado](#enable-custom-domain).
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Embora as especificidades de cada fornecedor de domínio variem, você mapeia de um nome *de* [domínio personalizado não raiz](#what-about-root-domains) (como **www.contoso.com**) *para* o nome de domínio do Gestor de Tráfego **(contoso.trafficmanager.net)** que está integrado com a sua aplicação. 

> [!NOTE]
> Se um registo já estiver a ser utilizado e precisar de ligar preventivamente as suas aplicações a ela, pode criar um registo CNAME adicional. Por exemplo, para ligar preventivamente o contoso.com à sua aplicação, crie um registo CNAME de **awverify.www** a **contoso.trafficmanager.net**. **\.** Em seguida, pode adicionar "www \. contoso.com" à sua app sem a necessidade de alterar o registo CNAME "www" . Para mais informações, consulte [Migrar um nome DNS ativo para o Azure App Service](manage-custom-dns-migrate-domain.md).

Uma vez terminada a adição ou modificação dos registos DNS no seu fornecedor de domínio, guarde as alterações.

### <a name="what-about-root-domains"></a>E os domínios das raízes?

Uma vez que o Traffic Manager só suporta mapeamento de domínio personalizado com registos CNAME, e porque os padrões DNS não suportam registos CNAME para mapeamento de domínios de raiz (por exemplo, **contoso.com),** o Traffic Manager não suporta mapeamento para domínios de raiz. Para contornar este problema, utilize um redirecionamento de URL a nível da aplicação. Em ASP.NET Core, por exemplo, pode utilizar [a reescrita de URL](/aspnet/core/fundamentals/url-rewriting). Em seguida, utilize o Gestor de Tráfego para carregar o equilíbrio do subdomínio **(www.contoso.com).** Outra abordagem é que pode [criar um registo de pseudónimo para o seu nome de domínio apex para referência a um perfil de Gestor de Tráfego Azure](../dns/tutorial-alias-tm.md). Um exemplo é contoso.com. Em vez de utilizar um serviço de redirecionamento, pode configurar o Azure DNS para fazer referência a um perfil de Gestor de Tráfego diretamente da sua zona. 

Para cenários de alta disponibilidade, pode implementar uma configuração de DNS de equilíbrio de carga sem Gestor de Tráfego, criando *vários registos A* que apontam do domínio raiz para o endereço IP de cada cópia de aplicações. Em seguida, [mapear o mesmo domínio raiz para todas as cópias da aplicação.](app-service-web-tutorial-custom-domain.md#map-an-a-record) Uma vez que o mesmo nome de domínio não pode ser mapeado para duas aplicações diferentes na mesma região, esta configuração só funciona quando as cópias da sua aplicação estão em diferentes regiões.

## <a name="enable-custom-domain"></a>Ativar o domínio personalizado
Depois de os registos do seu nome de domínio se terem propagado, utilize o navegador para verificar se o nome de domínio personalizado se resolve para a sua aplicação De Serviço de Aplicações.

> [!NOTE]
> Pode levar algum tempo para que o seu CNAME se propague através do sistema DNS. Pode utilizar um serviço de forma <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> a verificar se o CNAME está disponível.
> 
> 

1. Uma vez que a resolução de domínio tenha sucesso, para voltar à sua página de aplicações no [Portal Azure](https://portal.azure.com)
2. A partir da navegação à esquerda, selecione **Domínios personalizados**  >  **Adicione o nome de anfitrião**.
4. Digite o nome de domínio personalizado que mapeou anteriormente e **selecione Validate**.
5. Certifique-se de que **o tipo de registo hostname** está definido para **CNAME (www \. example.com ou qualquer subdomínio)**.

6. Uma vez que a aplicação Do Serviço de Aplicações está agora integrada com um ponto final do Traffic Manager, deverá ver o nome de domínio do Gestor de Tráfego na **configuração CNAME.** Selecione-o e clique **em Adicionar domínio personalizado.**

    ![Adicionar o nome DNS à aplicação](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Proteger um nome DNS personalizado com um enlace SSL no Serviço de Aplicações do Azure](configure-ssl-bindings.md)