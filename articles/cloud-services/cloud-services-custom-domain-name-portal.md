---
title: Configure um nome de domínio personalizado em Cloud Services (clássico) | Microsoft Docs
description: Saiba como expor a sua aplicação ou dados Azure à internet num domínio personalizado configurando as definições de DNS.  Estes exemplos usam o portal Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: bced2345473dbcbb5b9adf0269de0bef0549e862
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742374"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service-classic"></a>Configurar um nome de domínio personalizado para um serviço de nuvem Azure (clássico)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Quando cria um Serviço cloud, o Azure atribui-o a um subdomínio de **cloudapp.net**. Por exemplo, se o seu Serviço cloud estiver nomeado "contoso", os seus utilizadores poderão aceder à sua aplicação num URL como `http://contoso.cloudapp.net` . O Azure também atribui um endereço IP virtual.

No entanto, também pode expor a sua aplicação no seu próprio nome de domínio, como **contoso.com**. Este artigo explica como reservar ou configurar um nome de domínio personalizado para funções web do Cloud Service.

Já entende o que são os registos CNAME e A? [Salta para além da explicação.](#add-a-cname-record-for-your-custom-domain)

> [!NOTE]
> Os procedimentos nesta tarefa aplicam-se aos Serviços Azure Cloud. Para serviços de [aplicações, consulte mapear um nome DNS personalizado existente para Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md). Para obter contas de armazenamento, consulte [configurar um nome de domínio personalizado para o seu ponto final de armazenamento Azure Blob](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Vá mais rápido-- use o novo [azure guiado walkthrough](https://support.microsoft.com/kb/2990804)!  Faz com que associar um nome de domínio personalizado e garantir a comunicação (TLS) com a Azure Cloud Services ou a Azure Websites seja um instantâneo.
> 
> 

## <a name="understand-cname-and-a-records"></a>Compreenda os registos CNAME e A
OS registos CNAME (ou pseudónimos) e A permitem associar um nome de domínio a um servidor específico (ou serviço neste caso), no entanto funcionam de forma diferente. Existem também algumas considerações específicas ao utilizar registos A com os serviços Azure Cloud que deve considerar antes de decidir qual usar.

### <a name="cname-or-alias-record"></a>RECORDE DE CNAME ou Alias
Um registo CNAME mapeia um domínio *específico,* como **contoso.com** ou **www \. contoso.com,** para um nome de domínio canónico. Neste caso, o nome de domínio canónico é o nome de domínio **[myapp].cloudapp.net** da sua aplicação azure hospedada. Uma vez criado, o CNAME cria um pseudónimo para o **[myapp].cloudapp.net**. A entrada CNAME irá resolver-se para o endereço IP do seu serviço **[myapp].cloudapp.net** automaticamente, por isso, se o endereço IP do serviço de nuvem mudar, não terá de tomar qualquer medida.

> [!NOTE]
> Alguns registradores de domínio só permitem mapear subdomínios quando utilizar um registo CNAME, como www \. contoso.com, e não nomes de raiz, como contoso.com. Para obter mais informações sobre os registos cname, consulte a documentação fornecida pelo seu registo, [a entrada na Wikipédia no registo CNAME](https://en.wikipedia.org/wiki/CNAME_record), ou o [documento IETF Domain Names - Implementation and Specification.](https://tools.ietf.org/html/rfc1035)

### <a name="a-record"></a>Um registo
Um registo *A* mapeia um domínio, como **contoso.com** ou **www \. contoso.com**, *ou um domínio wildcard* como **\* .contoso.com**, para um endereço IP. No caso de um Azure Cloud Service, o IP virtual do serviço. Assim, o principal benefício de um registo A sobre um registo CNAME é que você pode ter uma entrada que usa um wildcard, como \* *_.contoso.com_*, que lidaria com pedidos de vários sub-domínios como **mail.contoso.com**, **login.contoso.com**, ou **www \. contso.com**.

> [!NOTE]
> Uma vez que um registo A é mapeado para um endereço IP estático, não pode resolver automaticamente alterações no endereço IP do seu Serviço cloud. O endereço IP utilizado pelo seu Serviço cloud é atribuído na primeira vez que se implanta numa ranhura vazia (produção ou encenação.) Se eliminar a implementação da ranhura, o endereço IP é lançado pela Azure e quaisquer futuras implementações para a ranhura poderão receber um novo endereço IP.
> 
> Convenientemente, persiste o endereço IP de uma determinada ranhura de implantação (produção ou encenação) quando se troca entre a encenação e as implantações de produção ou realiza uma atualização no local de uma implantação existente. Para obter mais informações sobre a realização destas ações, consulte [Como gerir os serviços na nuvem.](cloud-services-how-to-manage-portal.md)
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Adicione um registo CNAME para o seu domínio personalizado
Para criar um registo CNAME, deve adicionar uma nova entrada na tabela DNS para o seu domínio personalizado utilizando as ferramentas fornecidas pelo seu registo. Cada registrador tem um método semelhante, mas ligeiramente diferente, de especificar um registo CNAME, mas os conceitos são os mesmos.

1. Utilize um destes métodos para encontrar o nome de domínio **.cloudapp.net** atribuído ao seu serviço na nuvem.

   * Faça login no [portal Azure], selecione o seu serviço na nuvem, veja a secção **'Vista Geral'** e, em seguida, encontre a entrada de URL do **Site.**

       ![secção de olhar rápido mostrando o URL do site][csurl]

       **OR**
   * Instale e configuure [Azure Powershell](/powershell/azure/), e, em seguida, use o seguinte comando:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Guarde o nome de domínio utilizado no URL devolvido por qualquer método, pois necessitará dele ao criar um registo CNAME.
2. Inicie sessão no site do seu registo DNS e vá à página para gerir o DNS. Procure links ou áreas do site rotulados como **Nome de Domínio**, **DNS** ou **Gestão do Servidor de Nome**.
3. Agora encontre onde pode selecionar ou inserir CNAME's. Pode ter de selecionar o tipo de gravação a partir de uma queda ou ir para uma página de definições avançadas. Deve procurar as palavras **CNAME,** **Alias** ou **Subdomínios.**
4. Deve também fornecer o nome de domínio ou subdomínio para o CNAME, como **por exemplo, se** pretende criar um pseudónimo para **www \. customdomain.com**. Se quiser criar um pseudónimo para o domínio raiz, pode ser listado como o **\@** símbolo ' nas ferramentas DNS do seu registo.
5. Em seguida, deve fornecer um nome de anfitrião canónico, que é o domínio **cloudapp.net** da sua aplicação neste caso.

Por exemplo, o seguinte registo CNAME remete todo o tráfego de **www \. contoso.com** para **contoso.cloudapp.net,** o nome de domínio personalizado da sua aplicação implementada:

| Pseudónimo/Nome de anfitrião/Subdomínio | Domínio canónico |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> Um visitante do **www \. contoso.com** nunca verá o verdadeiro anfitrião (contoso.cloudapp.net), pelo que o processo de encaminhamento é invisível para o utilizador final.
> 
> O exemplo acima referido aplica-se apenas ao tráfego no subdomínio **www.** Uma vez que não pode utilizar wildcards com registos CNAME, deve criar um CNAME para cada domínio/subdomínio. Se pretender direcionar o tráfego de subdomínios, tais como *.contoso.com, para o seu endereço cloudapp.net, pode configurar uma entrada **url redirect** ou **URL Forward** nas suas definições DNS ou criar um registo A.

## <a name="add-an-a-record-for-your-custom-domain"></a>Adicione um registo A para o seu domínio personalizado
Para criar um registo A, tem primeiro de encontrar o endereço IP virtual do seu serviço na nuvem. Em seguida, adicione uma nova entrada na tabela DNS para o seu domínio personalizado utilizando as ferramentas fornecidas pelo seu registo. Cada registrador tem um método semelhante, mas ligeiramente diferente, de especificar um registo A, mas os conceitos são os mesmos.

1. Utilize um dos seguintes métodos para obter o endereço IP do seu serviço na nuvem.

   * Faça login no [portal Azure], selecione o seu serviço de cloud, veja a secção **'Vista Geral'** e, em seguida, encontre a entrada dos **endereços IP do Público.**

       ![secção de olhar rápido mostrando o VIP][vip]

       **OR**
   * Instale e configuure [Azure Powershell](/powershell/azure/), e, em seguida, use o seguinte comando:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Guarde o endereço IP, pois necessitará dele ao criar um registo A.
2. Inicie sessão no site do seu registo DNS e vá à página para gerir o DNS. Procure links ou áreas do site rotulados como **Nome de Domínio**, **DNS** ou **Gestão do Servidor de Nome**.
3. Agora encontre onde pode selecionar ou introduzir um registo. Pode ter de selecionar o tipo de gravação a partir de uma queda ou ir para uma página de definições avançadas.
4. Selecione ou introduza o domínio ou subdomínio que utilizará este registo A. Por exemplo, selecione **www** se pretende criar um pseudónimo para **www \. customdomain.com**. Se quiser criar uma entrada wildcard para todos os subdomínios, insira '******'. Isto abrangerá todos os subd domínios como **mail.customdomain.com,** **login.customdomain.com** e **www \. customdomain.com**.

    Se pretender criar um registo A para o domínio raiz, pode ser listado como o **\@** símbolo ' nas ferramentas DNS do seu registo.
5. Insira o endereço IP do seu serviço de nuvem no campo fornecido. Isto associa a entrada de domínio utilizada no registo A com o endereço IP da sua implementação de serviço em nuvem.

Por exemplo, o seguinte registo A encaminha todo o tráfego de **contoso.com** para **137.135.70.239**, o endereço IP da sua aplicação implantada:

| Nome do anfitrião/Subdomínio | Endereço IP |
| --- | --- |
| \@ |137.135.70.239 |

Este exemplo demonstra a criação de um registo A para o domínio raiz. Se desejar criar uma entrada wildcard para cobrir todos os subdomínios, introduzirá '*****' como subdomínio.

> [!WARNING]
> Os endereços IP em Azure são dinâmicos por padrão. Provavelmente irá querer utilizar um [endereço IP reservado](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) para garantir que o seu endereço IP não se altere.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Como gerir Serviços Cloud](cloud-services-how-to-manage-portal.md)
* [Como Mapear Conteúdo da CDN para um Domínio Personalizado](../cdn/cdn-map-content-to-custom-domain.md)
* [Configuração geral do seu serviço na nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implementar um serviço de cloud](cloud-services-how-to-create-deploy-portal.md).
* Configure [os certificados TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Portal do Azure]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png