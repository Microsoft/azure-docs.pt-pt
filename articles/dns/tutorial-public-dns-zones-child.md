---
title: Criar zonas DNS para crianças Azure
titleSuffix: Azure DNS
description: Tutorial sobre como criar zonas DNS infantis no portal Azure.
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 7/16/2020
ms.author: jonbeck
ms.openlocfilehash: 3f35d39634470ccacffa4d35c272a82725e9001c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89088156"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>Tutorial: Criar uma nova zona de DNS infantil

Neste tutorial, ficará a saber como: 

> [!div class="checklist"]
> * A assinar no Portal Azure.
> * Criar zona de DNS para crianças através da nova zona DNS.
> * Criar zona de DNS para crianças através da zona de DNS dos pais.
> * Verificação da delegação NS para a nova zona de DNS infantil.



## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa.  Se não tiver uma conta, pode [criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Zona de DNS do progenitor existente.  

Para efeitos deste tutorial, usaremos contoso.com como zona-mãe e subdomain.contoso.com como o nome de domínio da criança.  Substitua *contoso.com* pelo nome de domínio dos seus pais e *subdomínio* pelo domínio da criança.  Se não criou a zona de DNS dos seus pais, consulte os passos para criar a [zona DNS utilizando o portal Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal#create-a-dns-zone). 


## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta do Azure.
Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar.

Há duas maneiras de criar a zona DNS do seu filho.
1.  Através da página do portal "Criar zona DNS".
1.  Através da página de configuração da zona de DNS dos pais.


## <a name="create-child-dns-zone-via-create-dns-zone"></a>Criar zona DE DNS para crianças através da criação da zona DNS

Neste passo, vamos criar uma nova zona de DNS infantil com nome **subdomain.contoso.com** e delerá-la para a zona de DNS dos pais existente **contoso.com**. Irá criar a zona DNS utilizando os separadores na página **da zona Criar DNS.**
1.  No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**. A **nova** janela aparece.
1.  Selecione **Networking**e, em seguida, selecione **a zona DNS** e, em seguida, selecione **Adicionar** o botão.

1.  No **separador básico,** escreva ou selecione os seguintes valores:
    * **Assinatura**: Selecione uma subscrição para criar a zona em.
    * **Grupo de recursos**: Introduza o seu grupo de Recursos existente ou pode criar um novo selecionando **Criar novo,** introduzir *o MyResourceGroup,* e selecionar **OK**. O nome do grupo de recursos deve ser único dentro da assinatura Azure.
    * Verifique esta caixa de verificação: **Esta zona é uma criança de uma zona já hospedada no Azure DNS**
    * **Subscrição**da zona dos pais : A partir desta queda, procure e/ou selecione o nome de subscrição sob o qual a zona de DNS dos pais *contoso.com* foi criada.
    * **Zona dos pais**: No tipo de barra de pesquisa *contoso.com* para carregá-lo na lista de dropdown. Uma vez carregado, selecione *contoso.com* da lista de dropdown.
    * **Nome:** Escreva *subdomínio* para este exemplo tutorial. Note que o nome da zona de DNS dos seus pais *contoso.com* é automaticamente adicionado como sufixo para nomear quando selecionamos a zona dos pais a partir do passo acima.

1. Selecione **Seguinte: Rever + criar**.
1. No **separador 'Rever +' criar,** rever o resumo, corrigir quaisquer erros de validação e, em seguida, selecionar **Criar**.
A criação da zona pode demorar alguns minutos.

 
    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="Screenshot da página de zona de criar DNS." lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>Criar zona de DNS infantil através da página geral da zona de DNS dos pais
Também pode criar uma nova zona de DNS para crianças e deledá-la na zona de DNS dos pais, utilizando o botão **Zona infantil** a partir da página geral da zona dos pais. A utilização deste botão pré-povoa automaticamente os parâmetros dos pais para a zona da criança. 

1.  No portal Azure, em **todos os recursos,** abra a zona *contoso.com* DNS no grupo de recursos **MyResourceGroup.** Pode introduzir *contoso.com* na caixa de **nomes filter para** encontrá-lo mais facilmente.
1.  Na página geral da zona DO DNS, selecione o botão **+Zona infantil.**

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="Screenshot da página de zona de criar DNS." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  A página de zona de criar DNS abrirá então. A opção de zona infantil já está verificada, e a subscrição da zona dos pais e a zona dos pais já estão povoadas para si nesta página.
1.  Digite o nome como *criança* para este exemplo tutorial. Note que o nome da zona de DNS dos pais contoso.com é automaticamente adicionado como prefixo para o nome.
1.  Selecione **Seguinte: Tags** e depois **Seguinte: Rever + criar**.
1.  No **separador 'Rever +' criar,** rever o resumo, corrigir quaisquer erros de validação e, em seguida, selecionar **Criar**.

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="Screenshot da página de zona de criar DNS." border="true"  lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::
## <a name="verify-child-dns-zone"></a>Verifique a zona de DNS infantil
Agora que tem uma nova zona de DNS infantil *subdomain.contoso.com* criada. Para verificar se a delegação aconteceu corretamente, deverá verificar se os registos de nomes (NS) para a zona do seu filho estão na zona dos pais, conforme descrito abaixo.  

**Recuperar servidores de nome da zona DE DNS infantil:**

1.  No portal Azure, em **todos os recursos,** abra a zona *subdomain.contoso.com* DNS no grupo de recursos **MyResourceGroup.** Pode introduzir *subdomain.contoso.com* na caixa de **nomes filter para** encontrá-lo mais facilmente.
1.  Recupere os servidores de nomes da página geral da zona DNS. Neste exemplo, a zona contoso.com foi atribuída a servidores *de nomes ns1-08.azure-dns.com, ns2-08.azure-dns.net, ns3-08.azure-dns.org*e *ns4-08.azure-dns.info:*

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="Screenshot da página de zona de criar DNS." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**Verifique o registo NS na zona de DNS dos pais:**

Agora, neste passo, vamos para a zona de DNS dos pais *contoso.com* e verificar se o seu recorde NS estabeleceu a entrada para os servidores de nomes de zonas infantis.

1. No portal Azure, em **todos os recursos,** abra a zona de DNS contoso.com no grupo de recursos **MyResourceGroup.** Pode introduzir contoso.com na caixa de **nomes filter para** encontrá-lo mais facilmente.
1.  Na página geral *contoso.com* zonas DNS, verifique se os recordes.
1.  Você vai descobrir que o conjunto de registos de tipo NS e subdomínio de nome já está criado na zona de DNS dos pais. Verifique os valores deste conjunto de registos é semelhante à lista de nomes que recuperamos da zona de DNS infantil em cima do degrau.

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="Screenshot da página de zona de criar DNS." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>Limpar os recursos
Quando já não precisar dos recursos que criou neste tutorial, remova-os eliminando o grupo de recursos **MyResourceGroup.** Abra o grupo de recursos **MyResourceGroup** e selecione **Delete resource group**.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Cenários de Zonas Privadas Azure DNS](private-dns-scenarios.md)
