---
title: Configurar um nome de domínio personalizado nos serviços de nuvem | Microsoft Docs
description: Saiba como expor seu aplicativo ou dados do Azure para a Internet em um domínio personalizado definindo as configurações de DNS.  Esses exemplos usam o portal do Azure.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: b1f75a5f7a97907bf5b8bb460ff2df420d053f9e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75386804"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Configurando um nome de domínio personalizado para um serviço de nuvem do Azure
Quando você cria um serviço de nuvem, o Azure o atribui a um subdomínio de **cloudapp.net**. Por exemplo, se seu serviço de nuvem for denominado "contoso", os usuários poderão acessar seu aplicativo em uma URL como `http://contoso.cloudapp.net`. O Azure também atribui um endereço IP virtual.

No entanto, você também pode expor seu aplicativo em seu próprio nome de domínio, como **contoso.com**. Este artigo explica como reservar ou configurar um nome de domínio personalizado para funções Web do serviço de nuvem.

Você já entendeu o que são registros CNAME e A? [Vá além da explicação](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Os procedimentos nesta tarefa se aplicam aos serviços de nuvem do Azure. Para serviços de aplicativos, consulte [mapear um nome DNS personalizado existente para aplicativos Web do Azure](../app-service/app-service-web-tutorial-custom-domain.md). Para contas de armazenamento, consulte [configurar um nome de domínio personalizado para o ponto de extremidade do armazenamento de BLOBs do Azure](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Obtenha mais rapidez--use o novo [guia](https://support.microsoft.com/kb/2990804)interativo do Azure.  Ele faz com que a associação de um nome de domínio personalizado e a proteção de comunicação (SSL) com os serviços de nuvem do Azure ou sites do Azure sejam um ajuste.
> 
> 

## <a name="understand-cname-and-a-records"></a>Entender os registros CNAME e A
Os registros CNAME (ou registros de alias) e A permitem que você associe um nome de domínio a um servidor específico (ou serviço neste caso), no entanto, eles funcionam de maneira diferente. Também há algumas considerações específicas ao usar registros A com os serviços de nuvem do Azure que você deve considerar antes de decidir qual deles usar.

### <a name="cname-or-alias-record"></a>Registro CNAME ou de alias
Um registro CNAME mapeia um domínio *específico* , como **contoso.com** ou **www\.contoso.com**, para um nome de domínio canônico. Nesse caso, o nome de domínio canônico é o nome de domínio **[MyApp]. cloudapp. net** do seu aplicativo hospedado do Azure. Depois de criada, o CNAME cria um alias para o **[MyApp]. cloudapp. net**. A entrada CNAME resolverá automaticamente o endereço IP do serviço **[MyApp]. cloudapp. net** , portanto, se o endereço IP do serviço de nuvem for alterado, você não precisará realizar nenhuma ação.

> [!NOTE]
> Alguns registradores de domínio só permitem que você mapeie subdomínios ao usar um registro CNAME, como www\.contoso.com, e não nomes de raiz, como contoso.com. Para obter mais informações sobre registros CNAME, consulte a documentação fornecida pelo seu registrador, [a entrada da Wikipédia no registro CNAME](https://en.wikipedia.org/wiki/CNAME_record)ou o documento [nomes de domínio IETF – implementação e especificação](https://tools.ietf.org/html/rfc1035) .

### <a name="a-record"></a>Um registro
Um registro *a* mapeia um domínio, como **contoso.com** ou **www\.contoso.com**, *ou um domínio curinga* , como **\*. contoso.com**, para um endereço IP. No caso de um serviço de nuvem do Azure, o IP virtual do serviço. Portanto, o principal benefício de um registro A em um registro CNAME é que você pode ter uma entrada que usa um curinga, como \* **. contoso.com**, que trataria as solicitações de vários subdomínios, como **mail.contoso.com**, **login.contoso.com**ou **www\.contso.com**.

> [!NOTE]
> Como um registro A é mapeado para um endereço IP estático, ele não pode resolver automaticamente as alterações no endereço IP do serviço de nuvem. O endereço IP usado pelo serviço de nuvem é alocado na primeira vez que você implanta em um slot vazio (produção ou preparo). Se você excluir a implantação para o slot, o endereço IP será liberado pelo Azure e qualquer implantação futura no slot poderá receber um novo endereço IP.
> 
> Convenientemente, o endereço IP de um determinado slot de implantação (produção ou preparo) é persistido ao alternar entre implantações de produção e de preparo ou executar uma atualização in-loco de uma implantação existente. Para obter mais informações sobre como executar essas ações, consulte [como gerenciar serviços de nuvem](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Adicionar um registro CNAME para seu domínio personalizado
Para criar um registro CNAME, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador. Cada registrador tem um método semelhante, mas ligeiramente diferente, de especificar um registro CNAME, mas os conceitos são os mesmos.

1. Use um desses métodos para localizar o nome de domínio **. cloudapp.net** atribuído ao seu serviço de nuvem.

   * Faça logon no [portal do Azure], selecione o serviço de nuvem, examine a seção **visão geral** e, em seguida, localize a entrada **URL do site** .

       ![seção de visão rápida mostrando a URL do site][csurl]

       **OR**
   * Instale e configure o [Azure PowerShell](/powershell/azure/overview)e, em seguida, use o seguinte comando:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Salve o nome de domínio usado na URL retornada por qualquer um dos métodos, pois você precisará dele ao criar um registro CNAME.
2. Faça logon no site do registrador de DNS e vá para a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como nome de **domínio**, **DNS**ou gerenciamento de **servidor de nome**.
3. Agora, encontre onde você pode selecionar ou inserir CNAME. Talvez seja necessário selecionar o tipo de registro em uma lista suspensa ou ir para uma página de configurações avançadas. Você deve procurar as palavras **CNAME**, **alias**ou **subdomínios**.
4. Você também deve fornecer o alias de domínio ou subdomínio para o CNAME, como **www** , se desejar criar um alias para **www\.customdomain.com**. Se você quiser criar um alias para o domínio raiz, ele poderá ser listado como o símbolo ' **\@** ' nas ferramentas de DNS do registrador.
5. Em seguida, você deve fornecer um nome de host canônico, que é o domínio **cloudapp.net** do seu aplicativo nesse caso.

Por exemplo, o seguinte registro CNAME encaminha todo o tráfego de **www\.contoso.com** para **contoso.cloudapp.net**, o nome de domínio personalizado do seu aplicativo implantado:

| Alias/nome do host/subdomínio | Domínio canônico |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> Um visitante do **www\.contoso.com** nunca verá o verdadeiro host (contoso.cloudapp.net), portanto, o processo de encaminhamento é invisível para o usuário final.
> 
> O exemplo acima só se aplica ao tráfego no subdomínio da **www** . Como não é possível usar curingas com registros CNAME, você deve criar um CNAME para cada domínio/subdomínio. Se você quiser direcionar o tráfego de subdomínios, como *. contoso.com, para seu endereço cloudapp.net, poderá configurar uma entrada de **redirecionamento de URL** ou de **encaminhamento de URL** nas configurações de DNS ou criar um registro a.

## <a name="add-an-a-record-for-your-custom-domain"></a>Adicionar um registro A para seu domínio personalizado
Para criar um registro A, primeiro você deve encontrar o endereço IP virtual do seu serviço de nuvem. Em seguida, adicione uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo seu registrador. Cada registrador tem um método semelhante, mas ligeiramente diferente, de especificar um registro A, mas os conceitos são os mesmos.

1. Use um dos métodos a seguir para obter o endereço IP do seu serviço de nuvem.

   * Faça logon no [portal do Azure], selecione o serviço de nuvem, examine a seção **visão geral** e localize a entrada **endereços IP públicos** .

       ![seção de visão rápida mostrando o VIP][vip]

       **OR**
   * Instale e configure o [Azure PowerShell](/powershell/azure/overview)e, em seguida, use o seguinte comando:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Salve o endereço IP, pois você precisará dele ao criar um registro A.
2. Faça logon no site do registrador de DNS e vá para a página de gerenciamento de DNS. Procure links ou áreas do site rotuladas como nome de **domínio**, **DNS**ou gerenciamento de **servidor de nome**.
3. Agora, encontre onde você pode selecionar ou inserir um registro. Talvez seja necessário selecionar o tipo de registro em uma lista suspensa ou ir para uma página de configurações avançadas.
4. Selecione ou insira o domínio ou subdomínio que usará este registro A. Por exemplo, selecione **www** se desejar criar um alias para o **www\.customdomain.com**. Se você quiser criar uma entrada de caractere curinga para todos os subdomínios, insira ' * * * * * '. Isso abrangerá todos os subdomínios, como **mail.customdomain.com**, **login.customdomain.com**e **www\.customdomain.com**.

    Se você quiser criar um registro A para o domínio raiz, ele poderá ser listado como o símbolo ' **\@** ' nas ferramentas de DNS do registrador.
5. Insira o endereço IP do seu serviço de nuvem no campo fornecido. Isso associa a entrada de domínio usada no registro a com o endereço IP da implantação do serviço de nuvem.

Por exemplo, o seguinte registro a encaminha todo o tráfego de **contoso.com** para **137.135.70.239**, o endereço IP do seu aplicativo implantado:

| Nome do host/subdomínio | Endereço IP |
| --- | --- |
| \@ |137.135.70.239 |

Este exemplo demonstra como criar um registro A para o domínio raiz. Se você quiser criar uma entrada de caractere curinga para abranger todos os subdomínios, insira ' * * * * * ' como o subdomínio.

> [!WARNING]
> Os endereços IP no Azure são dinâmicos por padrão. Provavelmente, você desejará usar um [endereço IP reservado](../virtual-network/virtual-networks-reserved-public-ip.md) para garantir que seu endereço IP não seja alterado.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Como gerir Serviços Cloud](cloud-services-how-to-manage-portal.md)
* [Como Mapear Conteúdo da CDN para um Domínio Personalizado](../cdn/cdn-map-content-to-custom-domain.md)
* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Portal do Azure]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png



