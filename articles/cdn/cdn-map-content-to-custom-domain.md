---
title: Tutorial - Adicionar um domínio personalizado ao ponto final da CDN do Azure | Microsoft Docs
description: Neste tutorial, vai mapear o conteúdo do ponto final da CDN do Azure para um domínio personalizado.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: c5eb69ddd9c621024799b940ef58c34e7caaa3ff
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294030"
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>Tutorial: Adicionar um domínio personalizado ao ponto final da CDN do Azure
Este tutorial mostra como adicionar um domínio personalizado a um ponto final da Rede de Entrega de Conteúdos (CDN) do Azure. Se utilizar um ponto final da CDN para entregar conteúdos e se quiser que o seu próprio nome de domínio seja visível no URL da CDN, é necessário um domínio personalizado. Ter um nome de domínio visível pode ser conveniente para os seus clientes e útil para fins de imagem corporativa. 

Depois de criar um ponto final da CDN no seu perfil, o nome do mesmo, que é um subdomínio de azureedge.net, é incluído no URL da entrega de conteúdos da CDN por predefinição (por exemplo, \//contoso.azureedge.net/photo.png). Para sua comodidade, a CDN do Azure oferece a opção de associar um domínio personalizado a um ponto final da CDN. Com esta opção, os seus conteúdos são entregues com um domínio personalizado no seu URL em vez de um nome de ponto final (por exemplo, https:\//www.contoso.com/photo.png). 

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Criar um registo DNS CNAME.
> - Associar o domínio personalizado ao ponto final da CDN.
> - Verificar o domínio personalizado.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de concluir os passos neste tutorial, tem primeiro de criar um perfil da CDN e, pelo menos, um ponto final da CDN. Para obter mais informações, consulte [início rápido: Criar um perfil de CDN do Azure e o ponto final](cdn-create-new-endpoint.md).

Se ainda não tiver um domínio personalizado, tem primeiro de comprar um junto de um fornecedor de domínios. Por exemplo, veja [Buy a custom domain name](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain) (Comprar um nome de domínio personalizado).

Se estiver a utilizar o Azure para alojar os seus [domínios DNS](https://docs.microsoft.com/azure/dns/dns-overview), tem de delegar o sistema de nomes de domínio (DNS) do fornecedor do domínio a um DNS do Azure. Para obter mais informações, veja [Delegar um domínio ao DNS do Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). Caso contrário, se estiver a utilizar um fornecedor de domínios para processar o seu domínio DNS, avance para [Criar um registo DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Criar um registo DNS CNAME

Antes de poder utilizar um domínio personalizado com um ponto final da CDN do Azure, tem de criar um registo de nome canónico (CNAME) junto do seu fornecedor de domínios que aponte para o seu ponto final da CDN. Um registo CNAME é um tipo de registo DNS que mapeia um nome de domínio de origem para um nome de domínio de destino. Na CDN do Azure, o nome de domínio de origem é o nome de domínio personalizado e o nome de domínio de destino é o nome de anfitrião do ponto final da CDN. Depois de CDN do Azure verificar o CNAME gravar o que cria, tráfego endereçado ao domínio personalizado de origem (por exemplo, www\.contoso.com) é encaminhado para o nome de ponto final CDN de destino especificado anfitrião (por exemplo, contoso.azureedge.net). 

Os domínios personalizados e respetivos subdomínios só podem ser associados a um único ponto final de cada vez. No entanto, pode utilizar diferentes subdomínios do mesmo domínio personalizado para diferentes pontos finais de serviço do Azure diferente mediante a utilização de vários registos CNAME. Também pode mapear um domínio personalizado com subdomínios diferentes para o mesmo ponto final da CDN.

> [!NOTE]
> Qualquer tipo de registo de alias pode ser utilizado para domínios personalizados, se estiver a utilizar o DNS do Azure como o fornecedor de domínio. Estas instruções utilizam o tipo de registo CNAME. Se estiver a utilizar um ou tipos de registo AAAA basta seguem os passos abaixo ao substituir o CNAME com o tipo de registo da sua preferência. Se estiver a utilizar um registo de alias para adicionar um domínio de raiz como um domínio personalizado e que pretende ativar o SSL, tem de utilizar validação manual, conforme descrito [aqui](https://docs.microsoft.com/azure/cdn/cdn-custom-ssl?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate#custom-domain-is-not-mapped-to-your-cdn-endpoint)

## <a name="map-the-temporary-cdnverify-subdomain"></a>Mapear o subdomínio cdnverify temporário

Quando mapear um domínio existente que esteja em produção, existem considerações especiais. Enquanto estiver a registar o domínio personalizado no Portal do Azure, o domínio pode sofrer um breve período de inatividade. Para evitar a interrupção do tráfego da Web, comece por mapear o domínio personalizado para o nome de anfitrião do ponto final da CDN com o subdomínio Azure cdnverify, para criar um mapeamento CNAME temporário. Com este método, os utilizadores podem aceder ao seu domínio sem interrupção enquanto o mapeamento de DNS decorre. 

Caso contrário, se estiver a utilizar o domínio personalizado pela primeira vez e não esteja a ser executado nenhum tráfego de produção no mesmo, pode mapeá-lo diretamente para o ponto final da CDN. Avance para [Mapear domínio personalizado permanente](#map-the-permanent-custom-domain).

Para criar um registo CNAME com o subdomínio cdnverify:

1. Inicie sessão no Web site do fornecedor do domínio do seu domínio personalizado.

2. Encontre a página de gestão dos registos DNS ao consultar a documentação do fornecedor ou ao procurar áreas do Web site com o nome **Domain Name** (Nome de domínio), **DNS** ou **Name server management** (Gestão de servidores de nomes). 

3. Crie uma entrada de registo CNAME para o seu domínio personalizado e preencha os campos conforme mostrado na tabela seguinte (os nomes dos campos podem variar):

    | Origem                    | Tipo  | Destino                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Origem: Introduza o nome de domínio personalizado, incluindo o subdomínio cdnverify, no seguinte formato: cdnverify. &lt;nome de domínio personalizado&gt;. Por exemplo, cdnverify.www.contoso.com.

    - Escreva: Enter *CNAME*.

    - Destino: Introduza o anfitrião do ponto final da CDN, incluindo o subdomínio cdnverify, no seguinte formato: cdnverify.  _&lt;nome do ponto final&gt;_. azureedge.net. Por exemplo, cdnverify.contoso.azureedge.net.

4. Guarde as alterações.

Por exemplo, o procedimento para a entidade de registo de domínios GoDaddy é o seguinte:

1. Inicie sessão e selecione o domínio personalizado que pretende utilizar.

2. Na secção Domínios, selecione **Manage All** (Gerir Tudo) e selecione **DNS** | **Manage Zones** (Gerir Zonas).

3. Em **Domain Name** (Nome de Domínio), introduza o domínio personalizado e selecione **Search** (Pesquisar).

4. Na página **DNS Management** (Gestão de DNS), selecione **Add** (Adicionar) e selecione **CNAME** na lista **Type** (Tipo).

5. Preencha os seguintes campos da entrada de CNAME:

    ![Entrada de CNAME](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - Escreva: Deixe *CNAME* selecionado.

    - Host: Introduza o subdomínio do domínio personalizado para utilizar, incluindo o nome de subdomínio cdnverify. Por exemplo, cdnverify.www.

    - Pontos a: Introduza o nome de anfitrião do ponto final da CDN, incluindo o nome de subdomínio cdnverify. Por exemplo, cdnverify.contoso.azureedge.net. 

    - TTL: Deixe *1 hora* selecionado.

6. Selecione **Guardar**.
 
    A entrada CNAME é adicionada à tabela de registos DNS.

    ![Tabela de registos DNS](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Associar o domínio personalizado ao ponto final da CDN

Depois de registar o domínio personalizado, pode, então, adicioná-lo ao ponto final da CDN. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/) e navegue para o perfil da CDN que contém o ponto final que pretende mapear para um domínio personalizado.
    
2. Na página **perfil da CDN**, selecione o ponto final da CDN que vai ser associado ao domínio personalizado.

   É aberta a página **Ponto Final**.
    
3. Selecione **Domínio personalizado**. 

   ![Botão Domínio personalizado da CDN](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

   Abre-se a página **Adicionar um domínio personalizado**.

4. Para o **Nome de anfitrião do ponto final**, o nome de anfitrião do ponto final a utilizar como o domínio de destino do seu registo de CNAME é pré-preenchido e deriva do seu URL de ponto final de CDN: *&lt;nome de anfitrião do ponto final&gt;*.azureedge.net. Não pode ser alterado.

5. Para o **Nome de anfitrião personalizado**, introduza o seu domínio personalizado, incluindo o subdomínio para utilizar como o domínio de origem do seu registo de CNAME. Por exemplo, www\.contoso.com ou cdn.contoso.com. Não utilize o nome de subdomínio cdnverify.

   ![Caixa de diálogo de domínio personalizado da CDN](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

6. Selecione **Adicionar**.

   O Azure verifica se o registo CNAME existe para o nome de domínio personalizado que introduziu. Se o CNAME estiver correto, o seu domínio personalizado vai ser validado. 

   Pode demorar algum tempo para que as novas definições de domínios personalizados se propaguem a todos os nós periféricos da CDN: 
    - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
    - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
    - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída em 10 minutos.   


## <a name="verify-the-custom-domain"></a>Verificar o domínio personalizado

Depois de concluir o registo do seu domínio personalizado, confirme que este referencia o ponto final da CDN.
 
1. Verifique se tem conteúdo público que seja colocado em cache no ponto final. Por exemplo, se o ponto final da CDN estiver associado a uma conta de armazenamento, a CDN do Azure colocará em cache o conteúdo num contentor público. Para testar o domínio personalizado, verifique se o contentor está definido para permitir o acesso público e se contém pelo menos um ficheiro.

2. No browser, utilize o domínio personalizado para navegar para o endereço do ficheiro. Por exemplo, se o domínio personalizado for cdn.contoso.com, o URL para o ficheiro em cache deve ser semelhante a http:\//cdn.contoso.com/my-public-container/my-file.jpg. Verifique se o resultado é o mesmo que quando acede diretamente ao ponto final da CDN em *&lt;nome de anfitrião do ponto final&gt;*.azureedge.net.


## <a name="map-the-permanent-custom-domain"></a>Mapear o domínio personalizado permanente

Se tiver verificado que o subdomínio cdnverify foi mapeado com êxito para o ponto final (ou se estiver a utilizar um domínio personalizado novo que não está em produção), pode então mapear o domínio personalizado diretamente para o nome de anfitrião do ponto final da CDN.

Para criar um registo CNAME para o domínio personalizado:

1. Inicie sessão no Web site do fornecedor do domínio do seu domínio personalizado.

2. Encontre a página de gestão dos registos DNS ao consultar a documentação do fornecedor ou ao procurar áreas do Web site com o nome **Domain Name** (Nome de domínio), **DNS** ou **Name server management** (Gestão de servidores de nomes). 

3. Crie uma entrada de registo CNAME para o seu domínio personalizado e preencha os campos conforme mostrado na tabela seguinte (os nomes dos campos podem variar):

    | Origem          | Tipo  | Destino           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso.azureedge.net |

   - Origem: Introduza o seu nome de domínio personalizado (por exemplo, www\.contoso.com).

   - Escreva: Enter *CNAME*.

   - Destino: Introduza o nome de anfitrião do ponto final CDN. Tem de estar no formato _&lt;nome do ponto final&gt;_.azureedge.net. Por exemplo, contoso.azureedge.net.

4. Guarde as alterações.

5. Se tiver criado anteriormente um registo CNAME para o subdomínio cdnverify temporário, elimine-o. 

6. Se estiver a utilizar este domínio personalizado em produção pela primeira vez, siga os passos para [Associar o domínio personalizado com o ponto final da CDN](#associate-the-custom-domain-with-your-cdn-endpoint) e [Verificar o domínio personalizado](#verify-the-custom-domain).

Por exemplo, o procedimento para a entidade de registo de domínios GoDaddy é o seguinte:

1. Inicie sessão e selecione o domínio personalizado que pretende utilizar.

2. Na secção Domínios, selecione **Manage All** (Gerir Tudo) e selecione **DNS** | **Manage Zones** (Gerir Zonas).

3. Em **Domain Name** (Nome de Domínio), introduza o domínio personalizado e selecione **Search** (Pesquisar).

4. Na página **DNS Management** (Gestão de DNS), selecione **Add** (Adicionar) e selecione **CNAME** na lista **Type** (Tipo).

5. Preencha os campos da entrada de CNAME:

    ![Entrada de CNAME](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - Escreva: Deixe *CNAME* selecionado.

    - Host: Introduza o subdomínio do domínio personalizado que vai utilizar. Por exemplo, www ou cdn.

    - Pontos a: Introduza o nome de anfitrião do ponto final da CDN. Por exemplo, contoso.azureedge.net. 

    - TTL: Deixe *1 hora* selecionado.

6. Selecione **Guardar**.
 
    A entrada CNAME é adicionada à tabela de registos DNS.

    ![Tabela de registos DNS](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. Se tiver um registo CNAME cdnverify, selecione o ícone de lápis junto ao mesmo e, em seguida, selecione o ícone de caixote do lixo.

8. Selecione **Delete** para eliminar o registo CNAME.


## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, adicionou um domínio personalizado a um ponto final da CDN. Se já não pretender associar o ponto final a um domínio personalizado, pode executar os passos abaixo para remover o domínio personalizado:
 
1. No seu perfil da CDN, selecione o ponto final com o domínio personalizado que pretende remover.

2. Na página **Ponto Final**, em Domínios Personalizados, clique com o botão direito do rato no domínio personalizado que quer remover e selecione **Eliminar**, no menu de contexto.  

   O domínio personalizado é desassociado do ponto final.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> - Criar um registo DNS CNAME.
> - Associar o domínio personalizado ao ponto final da CDN.
> - Verificar o domínio personalizado.

Avançar para o próximo tutorial para saber como configurar HTTPS num domínio personalizado da CDN do Azure.

> [!div class="nextstepaction"]
> [Tutorial: Configurar HTTPS num domínio personalizado da CDN do Azure](cdn-custom-ssl.md)


