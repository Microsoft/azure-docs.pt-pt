---
title: Tutorial - Adicione domínio personalizado à configuração da porta da frente do Azure
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
ms.openlocfilehash: 5ffa85a2a681bfd064bfeade77d9ae7b85b1f723
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79471766"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>Tutorial: Adicionar um domínio personalizado ao Front Door
Neste tutorial, ficará a saber como adicionar um domínio personalizado ao Front Door. Quando utiliza a Porta Frontal Azure para entrega de aplicações, é necessário um domínio personalizado se quiser que o seu próprio nome de domínio seja visível no seu pedido de utilizador final. Ter um nome de domínio visível pode ser conveniente para os seus clientes e útil para fins de imagem corporativa.

Depois de criar um Front Door, o anfitrião de front-end predefinido, que é um subdomínio de `azurefd.net`, é incluído no URL para entregar conteúdos do Front Door a partir do seu back-end por predefinição (por exemplo, https:\//contoso.azurefd.net/activeusers.htm). Para sua comodidade, o Azure Front Door oferece a opção de associar um domínio personalizado ao anfitrião predefinido. Com esta opção, os seus conteúdos são entregues com um domínio personalizado no seu URL em vez de um nome de domínio pertencente ao Front Door (por exemplo, https:\//www.contoso.com/photo.png). 

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Criar um registo DNS CNAME.
> - Associar o domínio personalizado ao Front Door.
> - Verificar o domínio personalizado.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> A Porta da Frente **não** suporta domínios personalizados com caracteres [de punycode.](https://en.wikipedia.org/wiki/Punycode) 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste tutorial, tem primeiro de criar um Front Door. Para obter mais informações, veja [Quickstart: Create a Front Door](quickstart-create-front-door.md) (Início Rápido: Criar um Front Door).

Se ainda não tiver um domínio personalizado, tem primeiro de comprar um junto de um fornecedor de domínios. Por exemplo, veja [Buy a custom domain name](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain) (Comprar um nome de domínio personalizado).

Se estiver a utilizar o Azure para alojar os seus [domínios DNS](https://docs.microsoft.com/azure/dns/dns-overview), tem de delegar o sistema de nomes de domínio (DNS) do fornecedor do domínio a um DNS do Azure. Para mais informações, consulte [Delegar um domínio para o DNS Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). Caso contrário, se estiver a utilizar um fornecedor de domínios para processar o seu domínio DNS, avance para [Criar um registo DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Criar um registo DNS CNAME

Antes de poder utilizar um domínio personalizado com a porta da frente, tem primeiro de criar um registo de nome canónico (CNAME) com o seu fornecedor de domínio para apontar para o anfitrião frontal padrão da Porta Frontal (digamos contoso.azurefd.net). Um registo CNAME é um tipo de registo DNS que mapeia um nome de domínio de origem para um nome de domínio de destino. Para a Porta Frontal Azure, o nome de domínio de origem é o seu nome de domínio personalizado e o nome de domínio de destino é o seu nome de anfitrião padrão da Porta Da Frente. Depois de a Porta Frontal verificar o registo CNAME que cria, o\.tráfego dirigido ao domínio personalizado de origem (como www contoso.com) é encaminhado para o anfitrião de frontend padrão de destino especificado (como contoso.azurefd.net). 

Um domínio personalizado e o seu subdomínio podem ser associados apenas a uma única Porta da Frente de cada vez. No entanto, pode utilizar subdomínios diferentes do mesmo domínio personalizado para diferentes Portas Frontais utilizando vários registos CNAME. Também pode mapear um domínio personalizado com subdomínios diferentes para a mesma Porta da Frente.


## <a name="map-the-temporary-afdverify-subdomain"></a>Mapear o subdomínio de afdcheck temporário

Quando mapear um domínio existente que esteja em produção, existem considerações especiais. Enquanto estiver a registar o domínio personalizado no Portal do Azure, o domínio pode sofrer um breve período de inatividade. Para evitar a interrupção do tráfego web, mapeie primeiro o seu domínio personalizado para o seu anfitrião frontal por defeito da Porta Frontal com o subdomínio De afdcheck Azure para criar um mapeamento CNAME temporário. Com este método, os utilizadores podem aceder ao seu domínio sem interrupção enquanto o mapeamento de DNS decorre.

Caso contrário, se estiver a utilizar o domínio personalizado pela primeira vez e se não estiver a ser executado nenhum tráfego de produção, poderá mapeá-lo diretamente para o Front Door. Avance para [Mapear domínio personalizado permanente](#map-the-permanent-custom-domain).

Para criar um registo CNAME com o subdomínio afdverify:

1. Inicie sessão no Web site do fornecedor do domínio do seu domínio personalizado.

2. Encontre a página de gestão dos registos DNS ao consultar a documentação do fornecedor ou ao procurar áreas do Web site com o nome **Domain Name** (Nome de domínio), **DNS** ou **Name server management** (Gestão de servidores de nomes). 

3. Crie uma entrada de registo CNAME para o seu domínio personalizado e preencha os campos conforme mostrado na tabela seguinte (os nomes dos campos podem variar):

    | Origem                    | Tipo  | Destino                     |
    |---------------------------|-------|---------------------------------|
    | afdverify.www.contoso.com | CNAME | afdverify.contoso.azurefd.net |

    - Fonte: Introduza o seu nome de domínio personalizado, incluindo o subdomínio afdcheck, no seguinte formato: afdcheck. _nome de&gt;domínio personalizado. &lt;_ Por exemplo, afdverify.www.contoso.com.

    - Type (Tipo): introduza *CNAME*.

    - Destino: Introduza o seu anfitrião frontal da Porta Frontal, incluindo o subdomínio afdcheck, no seguinte formato: afdcheck. nome final .azurefd.net. _ &lt;&gt;_ Por exemplo, afdverify.contoso.azurefd.net.

4. Guarde as alterações.

Por exemplo, o procedimento para a entidade de registo de domínios GoDaddy é o seguinte:

1. Inicie sessão e selecione o domínio personalizado que pretende utilizar.

2. Na secção Domínios, selecione **Manage All** (Gerir Tudo) e selecione **DNS** | **Manage Zones** (Gerir Zonas).

3. Em **Domain Name** (Nome de Domínio), introduza o domínio personalizado e selecione **Search** (Pesquisar).

4. Na página **DNS Management** (Gestão de DNS), selecione **Add** (Adicionar) e selecione **CNAME** na lista **Type** (Tipo).

5. Preencha os seguintes campos da entrada de CNAME:

    - Type (Tipo): deixe *CNAME* selecionado.

    - Anfitrião: introduza o subdomínio do domínio personalizado que vai utilizar, incluindo o nome de subdomínio afdverify. Por exemplo, afdverify.www.

    - Aponta para: introduza o nome do seu anfitrião de front-end do Front Door predefinido, incluindo o nome de subdomínio afdverify. Por exemplo, afdverify.contoso.azurefd.net. 

    - TTL: Deixe *uma hora* selecionada.

6. Selecione **Guardar**.
 
    A entrada CNAME é adicionada à tabela de registos DNS.


## <a name="associate-the-custom-domain-with-your-front-door"></a>Associar o domínio personalizado ao Front Door

Depois de registar o domínio personalizado, pode adicioná-lo ao Front Door.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e navegue para o Front Door que contém o anfitrião de front-end que quer mapear para um domínio personalizado.
    
2. Na página **Estruturador do Front Door**, clique em “+” para adicionar um domínio personalizado.
    
3. Especifique o **Domínio personalizado**. 

4. Para **Anfitrião de front-end**, o anfitrião de front-end a utilizar como o domínio de destino do registo CNAME é preenchido previamente e é derivado do Front Door: *&lt;nome do anfitrião predefinido&gt;*.azurefd.net. Não pode ser alterado.

5. Para o **Nome de anfitrião personalizado**, introduza o seu domínio personalizado, incluindo o subdomínio para utilizar como o domínio de origem do seu registo de CNAME. Por exemplo,\.www contoso.com ou cdn.contoso.com. Não utilize o nome do subdomínio afdverify.

6. Selecione **Adicionar**.

   O Azure verifica se o registo CNAME existe para o nome de domínio personalizado que introduziu. Se o CNAME estiver correto, o seu domínio personalizado vai ser validado.

>[!WARNING]
> **Tem** de garantir que cada anfitrião de front-end (incluindo domínios personalizados) no Front Door tem uma regra de encaminhamento com um caminho predefinido (“/\*”) associado. Ou seja, em todas as regras de encaminhamento tem de existir, pelo menos, uma regra de encaminhamento para cada anfitrião de front-end definida no caminho predefinido (“/\*”). Se não o fizer, o tráfego de utilizador final poderá não ser encaminhado corretamente.

## <a name="verify-the-custom-domain"></a>Verificar o domínio personalizado

Depois de concluir o registo do domínio personalizado, confirme que este referencia o anfitrião de front-end do Front Door predefinido.
 
No browser, utilize o domínio personalizado para navegar para o endereço do ficheiro. Por exemplo, se o domínio personalizado for robotics.contoso.com, o URL para o ficheiro em cache deverá ser semelhante a: http:\//robotics.contoso.com/my-public-container/my-file.jpg. Verifique se o resultado é o mesmo que quando aceder diretamente à porta da frente no * &lt;anfitrião&gt;da porta da frente*.azurefd.net.


## <a name="map-the-permanent-custom-domain"></a>Mapear o domínio personalizado permanente

Se tiver verificado que o subdomínio afdverify foi mapeado com êxito para o Front Door (ou se estiver a utilizar um domínio personalizado novo que não está em produção), poderá mapear o domínio personalizado diretamente para o anfitrião de front-end do Front Door predefinido.

Para criar um registo CNAME para o domínio personalizado:

1. Inicie sessão no Web site do fornecedor do domínio do seu domínio personalizado.

2. Encontre a página para gerir os registos dNS consultando a documentação do fornecedor ou procurando áreas do site com o nome de **domínio,** **DNS**ou **Gestão do Servidor**de Nomes. 

3. Crie uma entrada de registo CNAME para o seu domínio personalizado e preencha os campos conforme mostrado na tabela seguinte (os nomes dos campos podem variar):

    | Origem          | Tipo  | Destino           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso.azurefd.net |

   - Fonte: Introduza o seu nome\.de domínio personalizado (por exemplo, www contoso.com).

   - Type (Tipo): introduza *CNAME*.

   - Destino: introduza o anfitrião de front-end do Front Door predefinido. Deve estar no seguinte formato:_&lt;nome de&gt;anfitrião_.azurefd.net. Por exemplo, contoso.azurefd.net.

4. Guarde as alterações.

5. Se tiver criado anteriormente um registo CNAME para o subdomínio afdverify temporário, elimine-o. 

6. Se estiver a utilizar este domínio personalizado em produção pela primeira vez, siga os passos para [Associar o domínio personalizado ao Front Door](#associate-the-custom-domain-with-your-front-door) e [Verificar o domínio personalizado](#verify-the-custom-domain).

Por exemplo, o procedimento para a entidade de registo de domínios GoDaddy é o seguinte:

1. Inicie sessão e selecione o domínio personalizado que pretende utilizar.

2. Na secção Domínios, selecione **Manage All** (Gerir Tudo) e selecione **DNS** | **Manage Zones** (Gerir Zonas).

3. Em **Domain Name** (Nome de Domínio), introduza o domínio personalizado e selecione **Search** (Pesquisar).

4. Na página **DNS Management** (Gestão de DNS), selecione **Add** (Adicionar) e selecione **CNAME** na lista **Type** (Tipo).

5. Preencha os campos da entrada de CNAME:

    - Type (Tipo): deixe *CNAME* selecionado.

    - Host (Anfitrião): introduza o subdomínio do domínio personalizado que vai utilizar. Por exemplo, www ou perfil.

    - Aponta para: introduza o nome do anfitrião predefinido do Front Door. Por exemplo, contoso.azurefd.net. 

    - TTL: Deixe *uma hora* selecionada.

6. Selecione **Guardar**.
 
    A entrada CNAME é adicionada à tabela de registos DNS.

7. Se tiver um registo CNAME afdverify, selecione o ícone de lápis junto ao mesmo e, em seguida, selecione o ícone de caixote do lixo.

8. Selecione **Delete** para eliminar o registo CNAME.


## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, adicionou um domínio personalizado a um Front Door. Se já não pretender associar o Front Door a um domínio personalizado, poderá executar os passos abaixo para remover o domínio personalizado:
 
1. No estruturador do Front Door, selecione o domínio personalizado que quer remover.

2. No menu de contexto do domínio personalizado, clique em Eliminar.  

   O domínio personalizado é desassociado do ponto final.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> - Criar um registo DNS CNAME.
> - Associar o domínio personalizado ao Front Door.
> - Verificar o domínio personalizado.