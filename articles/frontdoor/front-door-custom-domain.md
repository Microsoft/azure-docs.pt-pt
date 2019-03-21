---
title: Tutorial – Adicionar um domínio personalizado à configuração do Azure Front Door | Microsoft Docs
description: Neste tutorial, ficará a saber como integrar um domínio personalizado no Azure Front Door.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 3c98359950bd9539ea75f5a031ac1ce9f2ebe812
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002723"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>Tutorial: Adicionar um domínio personalizado ao seu Front Door
Neste tutorial, ficará a saber como adicionar um domínio personalizado ao Front Door. Se utilizar o Azure Front Door Service para a entrega de aplicações e quiser que o seu próprio nome de domínio seja visível no pedido de utilizador final, precisará de um domínio personalizado. Ter um nome de domínio visível pode ser conveniente para os seus clientes e útil para fins de imagem corporativa.

Depois de criar um Front Door, o anfitrião de front-end predefinido, que é um subdomínio de `azurefd.net`, é incluído no URL para entregar conteúdos do Front Door a partir do seu back-end por predefinição (por exemplo, https:\//contoso.azurefd.net/activeusers.htm). Para sua comodidade, o Azure Front Door oferece a opção de associar um domínio personalizado ao anfitrião predefinido. Com esta opção, os seus conteúdos são entregues com um domínio personalizado no seu URL em vez de um nome de domínio pertencente ao Front Door (por exemplo, https:\//www.contoso.com/photo.png). 

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Criar um registo DNS CNAME.
> - Associar o domínio personalizado ao Front Door.
> - Verificar o domínio personalizado.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste tutorial, tem primeiro de criar um Front Door. Para obter mais informações, consulte [início rápido: Criar uma porta de entrada](quickstart-create-front-door.md).

Se ainda não tiver um domínio personalizado, tem primeiro de comprar um junto de um fornecedor de domínios. Por exemplo, veja [Buy a custom domain name](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain) (Comprar um nome de domínio personalizado).

Se estiver a utilizar o Azure para alojar os seus [domínios DNS](https://docs.microsoft.com/azure/dns/dns-overview), tem de delegar o sistema de nomes de domínio (DNS) do fornecedor do domínio a um DNS do Azure. Para obter mais informações, veja [Delegar um domínio ao DNS do Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). Caso contrário, se estiver a utilizar um fornecedor de domínios para processar o seu domínio DNS, avance para [Criar um registo DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Criar um registo DNS CNAME

Antes de poder utilizar um domínio personalizado com sua porta de entrada, primeiro tem de criar um registo de nome canónico (CNAME) com o seu fornecedor de domínio para apontar para o anfitrião de front-end da porta da frente predefinido (Digamos contoso.azurefd.net). Um registo CNAME é um tipo de registo DNS que mapeia um nome de domínio de origem para um nome de domínio de destino. Para o Azure Front Door Service, o nome de domínio de origem é o nome de domínio personalizado e o nome de domínio de destino é o nome do anfitrião predefinido do Front Door. Depois porta da frente verifica o CNAME gravar o que cria, tráfego endereçado ao domínio personalizado de origem (por exemplo, www\.contoso.com) é encaminhado para o host de front-end do padrão de porta de entrada destino especificado (por exemplo, contoso.azurefd.net). 

Os domínios personalizados e os seus subdomínios só podem ser associados a um único Front Door de cada vez. No entanto, pode utilizar diferentes subdomínios do mesmo domínio personalizado para diferentes Front Doors mediante a utilização de vários registos CNAME. Também pode mapear um domínio personalizado com subdomínios diferentes para o mesmo Front Door.


## <a name="map-the-temporary-afdverify-sub-domain"></a>Mapear o subdomínio afdverify temporário

Quando mapear um domínio existente que esteja em produção, existem considerações especiais. Enquanto estiver a registar o domínio personalizado no Portal do Azure, o domínio pode sofrer um breve período de inatividade. Para evitar a interrupção do tráfego da Web, comece por mapear o domínio personalizado para o anfitrião de front-end predefinido do Front Door com o subdomínio Azure afdverify para criar um mapeamento CNAME temporário. Com este método, os utilizadores podem aceder ao seu domínio sem interrupção enquanto o mapeamento de DNS decorre.

Caso contrário, se estiver a utilizar o domínio personalizado pela primeira vez e se não estiver a ser executado nenhum tráfego de produção, poderá mapeá-lo diretamente para o Front Door. Avance para [Mapear domínio personalizado permanente](#map-the-permanent-custom-domain).

Para criar um registo CNAME com o subdomínio afdverify:

1. Inicie sessão no Web site do fornecedor do domínio do seu domínio personalizado.

2. Encontre a página de gestão dos registos DNS ao consultar a documentação do fornecedor ou ao procurar áreas do Web site com o nome **Domain Name** (Nome de domínio), **DNS** ou **Name server management** (Gestão de servidores de nomes). 

3. Crie uma entrada de registo CNAME para o seu domínio personalizado e preencha os campos conforme mostrado na tabela seguinte (os nomes dos campos podem variar):

    | Origem                    | Tipo  | Destino                     |
    |---------------------------|-------|---------------------------------|
    | afdverify.www.contoso.com | CNAME | afdverify.contoso.azurefd.net |

    - Origem: Introduza o nome de domínio personalizado, incluindo o subdomínio de afdverify, no seguinte formato: afdverify.  _&lt;nome de domínio personalizado&gt;_. Por exemplo, afdverify.www.contoso.com.

    - Escreva: Enter *CNAME*.

    - Destino: Introduza o anfitrião de front-end predefinido de porta de entrada, incluindo o subdomínio de afdverify, no seguinte formato: afdverify.  _&lt;nome do ponto final&gt;_. azurefd.net. Por exemplo, afdverify.contoso.azurefd.net.

4. Guarde as alterações.

Por exemplo, o procedimento para a entidade de registo de domínios GoDaddy é o seguinte:

1. Inicie sessão e selecione o domínio personalizado que pretende utilizar.

2. Na secção Domínios, selecione **Manage All** (Gerir Tudo) e selecione **DNS** | **Manage Zones** (Gerir Zonas).

3. Em **Domain Name** (Nome de Domínio), introduza o domínio personalizado e selecione **Search** (Pesquisar).

4. Na página **DNS Management** (Gestão de DNS), selecione **Add** (Adicionar) e selecione **CNAME** na lista **Type** (Tipo).

5. Preencha os seguintes campos da entrada de CNAME:

    - Escreva: Deixe *CNAME* selecionado.

    - Host: Introduza o subdomínio do domínio personalizado para utilizar, incluindo o nome de subdomínio afdverify. Por exemplo, afdverify.www.

    - Pontos a: Introduza o nome de anfitrião do seu anfitrião de front-end de porta de entrada padrão, incluindo o nome de subdomínio afdverify. Por exemplo, afdverify.contoso.azurefd.net. 

    - TTL: Deixe *1 hora* selecionado.

6. Selecione **Guardar**.
 
    A entrada CNAME é adicionada à tabela de registos DNS.


## <a name="associate-the-custom-domain-with-your-front-door"></a>Associar o domínio personalizado ao Front Door

Depois de registar o domínio personalizado, pode adicioná-lo ao Front Door.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e navegue para o Front Door que contém o anfitrião de front-end que quer mapear para um domínio personalizado.
    
2. Na página **Estruturador do Front Door**, clique em “+” para adicionar um domínio personalizado.
    
3. Especifique o **Domínio personalizado**. 

4. Para **Anfitrião de front-end**, o anfitrião de front-end a utilizar como o domínio de destino do registo CNAME é preenchido previamente e é derivado do Front Door: *&lt;nome do anfitrião predefinido&gt;*.azurefd.net. Não pode ser alterado.

5. Para o **Nome de anfitrião personalizado**, introduza o seu domínio personalizado, incluindo o subdomínio para utilizar como o domínio de origem do seu registo de CNAME. Por exemplo, www\.contoso.com ou cdn.contoso.com. Não utilize o nome do subdomínio afdverify.

6. Selecione **Adicionar**.

   O Azure verifica se o registo CNAME existe para o nome de domínio personalizado que introduziu. Se o CNAME estiver correto, o seu domínio personalizado vai ser validado.

>[!WARNING]
> **Tem** de garantir que cada anfitrião de front-end (incluindo domínios personalizados) no Front Door tem uma regra de encaminhamento com um caminho predefinido (“/\*”) associado. Ou seja, em todas as regras de encaminhamento tem de existir, pelo menos, uma regra de encaminhamento para cada anfitrião de front-end definido no caminho predefinido (“/\*”). Se não o fizer, o tráfego de utilizador final poderá não ser encaminhado corretamente.

## <a name="verify-the-custom-domain"></a>Verificar o domínio personalizado

Depois de concluir o registo do domínio personalizado, confirme que este referencia o anfitrião de front-end do Front Door predefinido.
 
No browser, utilize o domínio personalizado para navegar para o endereço do ficheiro. Por exemplo, se o domínio personalizado for robotics.contoso.com, o URL para o ficheiro em cache deverá ser semelhante a: http:\//robotics.contoso.com/my-public-container/my-file.jpg. Confirme que o resultado é igual ao resultado que viu quando acedeu ao Front Door diretamente em *&lt;anfitrião do Front Door&gt;*.azurefd.net.


## <a name="map-the-permanent-custom-domain"></a>Mapear o domínio personalizado permanente

Se tiver verificado que o subdomínio afdverify foi mapeado com êxito para o Front Door (ou se estiver a utilizar um domínio personalizado novo que não está em produção), poderá mapear o domínio personalizado diretamente para o anfitrião de front-end do Front Door predefinido.

Para criar um registo CNAME para o domínio personalizado:

1. Inicie sessão no Web site do fornecedor do domínio do seu domínio personalizado.

2. Encontre a página de gestão dos registos DNS ao consultar a documentação do fornecedor ou ao procurar áreas do Web site com o nome **Domain Name** (Nome de domínio), **DNS** ou **Name server management** (Gestão de servidores de nomes). 

3. Crie uma entrada de registo CNAME para o seu domínio personalizado e preencha os campos conforme mostrado na tabela seguinte (os nomes dos campos podem variar):

    | Origem          | Tipo  | Destino           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso.azurefd.net |

   - Origem: Introduza o seu nome de domínio personalizado (por exemplo, www\.contoso.com).

   - Escreva: Enter *CNAME*.

   - Destino: Introduza o anfitrião de front-end de porta de entrada predefinido. Tem de estar no formato _&lt;nome do anfitrião&gt;_.azurefd.net. Por exemplo, contoso.azurefd.net.

4. Guarde as alterações.

5. Se tiver criado anteriormente um registo CNAME para o subdomínio afdverify temporário, elimine-o. 

6. Se estiver a utilizar este domínio personalizado em produção pela primeira vez, siga os passos para [Associar o domínio personalizado ao Front Door](#associate-the-custom-domain-with-your-front-door) e [Verificar o domínio personalizado](#verify-the-custom-domain).

Por exemplo, o procedimento para a entidade de registo de domínios GoDaddy é o seguinte:

1. Inicie sessão e selecione o domínio personalizado que pretende utilizar.

2. Na secção Domínios, selecione **Manage All** (Gerir Tudo) e selecione **DNS** | **Manage Zones** (Gerir Zonas).

3. Em **Domain Name** (Nome de Domínio), introduza o domínio personalizado e selecione **Search** (Pesquisar).

4. Na página **DNS Management** (Gestão de DNS), selecione **Add** (Adicionar) e selecione **CNAME** na lista **Type** (Tipo).

5. Preencha os campos da entrada de CNAME:

    - Escreva: Deixe *CNAME* selecionado.

    - Host: Introduza o subdomínio do domínio personalizado que vai utilizar. Por exemplo, www ou perfil.

    - Pontos a: Introduza o nome de anfitrião predefinido da sua porta de entrada. Por exemplo, contoso.azurefd.net. 

    - TTL: Deixe *1 hora* selecionado.

6. Selecione **Guardar**.
 
    A entrada CNAME é adicionada à tabela de registos DNS.

7. Se tiver um registo CNAME afdverify, selecione o ícone de lápis junto ao mesmo e, em seguida, selecione o ícone de caixote do lixo.

8. Selecione **Delete** para eliminar o registo CNAME.


## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, adicionou um domínio personalizado a um Front Door. Se já não pretender associar o Front Door a um domínio personalizado, poderá executar os passos abaixo para remover o domínio personalizado:
 
1. No estruturador do Front Door, selecione o domínio personalizado que quer remover.

2. No menu de contexto do domínio personalizado, clique em Eliminar.  

   O domínio personalizado é desassociado do ponto final.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> - Criar um registo DNS CNAME.
> - Associar o domínio personalizado ao Front Door.
> - Verificar o domínio personalizado.