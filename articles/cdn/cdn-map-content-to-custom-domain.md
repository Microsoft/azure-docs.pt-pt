---
title: 'Tutorial: Adicione um domínio personalizado ao seu ponto final'
titleSuffix: Azure Content Delivery Network
description: Utilize este tutorial para adicionar um domínio personalizado a um ponto final da Rede de Entrega de Conteúdos Azure para que o seu nome de domínio seja visível no seu URL.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: b0e8f2b14d506eb408660b939a7c925a33215cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99537750"
---
# <a name="tutorial-add-a-custom-domain-to-your-endpoint"></a>Tutorial: Adicione um domínio personalizado ao seu ponto final

Este tutorial mostra como adicionar um domínio personalizado a um ponto final da Rede de Entrega de Conteúdos (CDN) do Azure. 

O nome final no seu perfil DE CDN é um subdomínio de azureedge.net. Por predefinição, ao entregar conteúdo, o domínio do perfil CDN está incluído no URL.

Por exemplo, **https://contoso.azureedge.net/photo.png**.

O Azure CDN oferece a opção de associar um domínio personalizado a um ponto final CDN. Esta opção fornece conteúdo com um domínio personalizado no seu URL em vez do domínio predefinido.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Criar um registo DNS CNAME.
> - Associar o domínio personalizado ao ponto final da CDN.
> - Verificar o domínio personalizado.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Antes de completar os passos neste tutorial, crie um perfil CDN e pelo menos um ponto final CDN. 
    * Para obter mais informações, veja [Início Rápido: Criar um perfil e um ponto final da CDN do Azure](cdn-create-new-endpoint.md).

* Se não tiver um domínio personalizado, compre um com um fornecedor de domínio. 
    * Para obter mais informações, consulte [Comprar um nome de domínio personalizado.](../app-service/manage-custom-dns-buy-domain.md)

* Se estiver a utilizar o Azure para hospedar os seus [domínios DNS,](../dns/dns-overview.md)delege o seu domínio personalizado para O DNS Azure. 
    * Para obter mais informações, consulte [delegado de um domínio para Azure DNS](../dns/dns-delegate-domain-azure-dns.md).

* Se estiver a utilizar um fornecedor de domínio para manusear o seu domínio DNS, continue a [criar um registo DE DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Criar um registo DNS CNAME

Antes de utilizar um domínio personalizado com um ponto final Azure CDN, tem primeiro de criar um registo de nome canónico (CNAME) com O Azure DNS ou o seu fornecedor DNS para apontar para o seu ponto final CDN. 

Um registo CNAME é um registo DNS que mapeia um nome de domínio de origem para um nome de domínio de destino. 

Na CDN do Azure, o nome de domínio de origem é o nome de domínio personalizado e o nome de domínio de destino é o nome de anfitrião do ponto final da CDN. 

A Azure CDN encaminha o tráfego dirigido ao domínio personalizado de origem para o nome de anfitrião do ponto final cdn de destino depois de verificar o registo CNAME.

Um domínio personalizado e o seu subdomínio podem ser associados a um único ponto final de cada vez. 

Utilize vários registos CNAME para diferentes subdomínios do mesmo domínio personalizado para diferentes serviços Azure.

Pode mapear um domínio personalizado com subdomínios diferentes para o mesmo ponto final cdn.

> [!NOTE]
> Este tutorial utiliza o tipo de gravação CNAME. Se estiver a utilizar tipos de registo A ou AAAA, siga os mesmos passos abaixo e substitua o CNAME pelo tipo de registo à sua escolha.

---
# <a name="azure-dns"></a>[**DNS do Azure**](#tab/azure-dns)

O Azure DNS utiliza registos de pseudónimos para recursos Azure dentro da mesma subscrição.

Para adicionar um registo de pseudónimo para o seu ponto final Azure CDN:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No menu à esquerda, selecione **Todos os recursos** e, em seguida, a zona Azure DNS para o seu domínio personalizado.

3. Na zona DNS para o seu domínio personalizado, selecione **+ Conjunto de registos**.

4. In **Adicionar recorde de gravação** introduza ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome  | Introduza o pseudónimo que pretende utilizar para o seu ponto final CDN. Por exemplo, **www.** |
    | Tipo  | **Selecione CNAME**. |
    | Conjunto de registos de alias | Selecione **Yes** (Sim). |
    | Tipo de alias | Selecione **recurso Azure**. |
    | Escolha uma subscrição | Selecione a sua subscrição. |
    | Recurso do Azure | Selecione o seu ponto final Azure CDN. |

5. Mude o **TTL** para o registo para o seu valor.

6. Selecione **OK**.

# <a name="dns-provider"></a>[**Fornecedor de DNS**](#tab/dns-provider)

## <a name="map-the-temporary-cdnverify-subdomain"></a>Mapear o subdomínio cdnverify temporário

Quando mapear um domínio existente que esteja em produção, existem considerações especiais. 

Pode ocorrer um breve período de inatividade para o domínio ao registar o seu domínio personalizado no portal Azure.

Para evitar interrupções no tráfego web, mapeear o seu domínio personalizado para o seu nome de anfitrião do ponto final cdN com o subdomínio Azure **cdnverify.** Este processo cria um mapeamento CNAME temporário. 

Com este método, os utilizadores podem aceder ao seu domínio sem interrupção enquanto o mapeamento de DNS decorre. 

Se as considerações de tempo de produção não forem uma preocupação, pode mapear diretamente o seu domínio personalizado para o seu ponto final CDN. Continue a [mapear o domínio personalizado permanente.](#map-the-permanent-custom-domain)

Para criar um registo CNAME com o subdomínio cdnverify:

1. Inscreva-se no site do fornecedor DNS para o seu domínio personalizado.

2. Crie uma entrada de registo CNAME para o seu domínio personalizado e preencha os campos conforme mostrado na tabela seguinte (os nomes dos campos podem variar):

    | Origem                    | Tipo  | Destino                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Fonte: Introduza o seu nome de domínio personalizado, incluindo o subdomínio cdnverify, no seguinte formato: **cdnverify. \<custom-domain-name>**
        - Por exemplo: **cdnverify.www.contoso.com**

    - Tipo: Introduza ou selecione **CNAME**.

    - Destino: Introduza o seu nome de anfitrião do ponto final CDN, incluindo o subdomínio cdnverify, no seguinte formato: **cdnverify. \<endpoint-name> azureedge.net.** 
        - Por exemplo: **cdnverify.contoso.azureedge.net**

3. Guarde as alterações.

## <a name="map-the-permanent-custom-domain"></a>Mapear o domínio personalizado permanente

Nesta secção, mapeia o domínio personalizado permanente para o ponto final do CDN. 

Para criar um registo CNAME para o domínio personalizado:

1. Inicie sessão no Web site do fornecedor do domínio do seu domínio personalizado.

2. Crie uma entrada de registo CNAME para o seu domínio personalizado e preencha os campos conforme mostrado na tabela seguinte (os nomes dos campos podem variar):

    | Origem          | Tipo  | Destino           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Fonte: Introduza o seu nome de domínio personalizado.
        - Por exemplo: **www.contoso.com**

    - Tipo: Introduza ou selecione **CNAME**.

    - Destino: Introduza o seu nome de anfitrião do ponto final CDN no seguinte formato: **\<endpoint-name> .azureedge.net**. 
        - Por exemplo: **contoso.azureedge.net**

3. Guarde as alterações.

4. Se tiver criado anteriormente um registo CNAME para o subdomínio cdnverify temporário, elimine-o. 

5. Se estiver a utilizar este domínio personalizado em produção pela primeira vez, siga os passos para [associar o domínio personalizado ao seu ponto final CDN](#associate-the-custom-domain-with-your-cdn-endpoint) e Verifique o domínio [personalizado](#verify-the-custom-domain).

---
## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Associar o domínio personalizado ao ponto final da CDN

Depois de registar o domínio personalizado, pode, então, adicioná-lo ao ponto final da CDN. 


---
# <a name="azure-portal"></a>[**Portal Azure**](#tab/azure-portal)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/) e navegue para o perfil da CDN que contém o ponto final que pretende mapear para um domínio personalizado.
    
2. Na página **perfil da CDN**, selecione o ponto final da CDN que vai ser associado ao domínio personalizado.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-endpoint-selection.png" alt-text="Seleção de pontos finais CDN" border="true":::
    
3. Selecione **+ domínio personalizado**. 

   :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png" alt-text="Adicionar botão de domínio personalizado" border="true":::

4. In **Add a custom domain**, **Endpoint hostname**, é pré-preenchido e é derivado do seu URL de ponto final CDN: **\<endpoint-hostname>** .azureedge.net. Não pode ser alterado.

5. Para o **Nome de anfitrião personalizado**, introduza o seu domínio personalizado, incluindo o subdomínio para utilizar como o domínio de origem do seu registo de CNAME. 
    1. Por exemplo, **www.contoso.com** ou **cdn.contoso.com**. **Não utilize o nome de subdomínio cdnver.**

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png" alt-text="Adicionar domínio personalizado" border="true":::

6. Selecione **Adicionar**.

   O Azure verifica se o registo CNAME existe para o nome de domínio personalizado que introduziu. Se o CNAME estiver correto, o seu domínio personalizado vai ser validado. 

   Pode demorar algum tempo para que as novas definições de domínios personalizados se propaguem a todos os nós periféricos da CDN: 
    - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
    - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
    - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída em 10 minutos.   

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

1. Inscreva-se no Azure PowerShell:

```azurepowershell-interactive
    Connect-AzAccount

```
2. Utilize [New-AzCdnCustomDomain](/powershell/module/az.cdn/new-azcdncustomdomain) para mapear o domínio personalizado para o seu ponto final CDN. 

    * Substitua **myendpoint8675.azureedge.net** pelo url do ponto final.
    * Substitua **o ponto de terminação myend8675** pelo seu nome de ponto final CDN.
    * Substitua **www.contoso.com** pelo seu nome de domínio personalizado.
    * Substitua **o myCDN** pelo nome do seu perfil CDN.
    * Substitua **o myResourceGroupCDN** pelo nome do grupo de recursos.

```azurepowershell-interactive
    $parameters = @{
        Hostname = 'myendpoint8675.azureedge.net'
        EndPointName = 'myendpoint8675'
        CustomDomainName = 'www.contoso.com'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    New-AzCdnCustomDomain @parameters
```

O Azure verifica se o registo CNAME existe para o nome de domínio personalizado que introduziu. Se o CNAME estiver correto, o seu domínio personalizado vai ser validado. 

   Pode demorar algum tempo para que as novas definições de domínios personalizados se propaguem a todos os nós periféricos da CDN: 

- Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
- Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
- Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída em 10 minutos.   


---
## <a name="verify-the-custom-domain"></a>Verificar o domínio personalizado

Depois de ter concluído o registo do seu domínio personalizado, verifique se o domínio personalizado faz referência ao seu ponto final CDN.
 
1. Verifique se tem conteúdo público que seja colocado em cache no ponto final. Por exemplo, se o ponto final da CDN estiver associado a uma conta de armazenamento, a CDN do Azure colocará em cache o conteúdo num contentor público. Desatide o seu recipiente para permitir o acesso do público e contém pelo menos um ficheiro para testar o domínio personalizado.

2. No browser, utilize o domínio personalizado para navegar para o endereço do ficheiro. Por exemplo, se o seu domínio personalizado `www.contoso.com` for, o URL do ficheiro em cache deve ser semelhante ao seguinte URL: `http://www.contoso.com/my-public-container/my-file.jpg` . Verifique se o resultado é o mesmo que quando acede diretamente ao ponto final do CDN em **\<endpoint-hostname>** .azureedge.net.

## <a name="clean-up-resources"></a>Limpar os recursos

---
# <a name="azure-portal"></a>[**Portal Azure**](#tab/azure-portal-cleanup)

Se já não pretender associar o seu ponto final a um domínio personalizado, remova o domínio personalizado fazendo os seguintes passos:
 
1. No seu perfil da CDN, selecione o ponto final com o domínio personalizado que pretende remover.

2. Na página **Ponto Final**, em Domínios Personalizados, clique com o botão direito do rato no domínio personalizado que quer remover e selecione **Eliminar**, no menu de contexto. Selecione **Yes** (Sim).

   O domínio personalizado é desassociado do ponto final.

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell-cleanup)

Se já não pretender associar o seu ponto final a um domínio personalizado, remova o domínio personalizado fazendo os seguintes passos:

1. Utilize [Remove-AzCdnCustomDomain](/powershell/module/az.cdn/remove-azcdncustomdomain) para remover o domínio personalizado do ponto final:

    * Substitua **o ponto de terminação myend8675** pelo seu nome de ponto final CDN.
    * Substitua **www.contoso.com** pelo seu nome de domínio personalizado.
    * Substitua **o myCDN** pelo nome do seu perfil CDN.
    * Substitua **o myResourceGroupCDN** pelo nome do grupo de recursos.


```azurepowershell-interactive
    $parameters = @{
        CustomDomainName = 'www.contoso.com'
        EndPointName = 'myendpoint8675'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    Remove-AzCdnCustomDomain @parameters
```

---
## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> - Criar um registo DNS CNAME.
> - Associar o domínio personalizado ao ponto final da CDN.
> - Verificar o domínio personalizado.

Avançar para o próximo tutorial para saber como configurar HTTPS num domínio personalizado da CDN do Azure.

> [!div class="nextstepaction"]
> [Tutorial: Configurar o HTTPS num domínio personalizado da CDN do Azure](cdn-custom-ssl.md)