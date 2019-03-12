---
title: Configurar um nome de domínio personalizado nos serviços Cloud | Documentos da Microsoft
description: Saiba como expor a sua aplicação do Azure ou a dados na internet num domínio personalizado ao configurar as definições de DNS.  Estes exemplos utilizam o portal do Azure.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 5783a246-a151-4fb1-b488-441bfb29ee44
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 5321ab80440e59be42b6354fda47cd78041b9078
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57781549"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Configurar um nome de domínio personalizado para um serviço cloud do Azure
Quando cria um serviço em nuvem, o Azure atribui-lo a um subdomínio de **cloudapp.net**. Por exemplo, se o seu serviço Cloud com o nome "contoso", os utilizadores serão capazes de aceder à sua aplicação numa URL como http://contoso.cloudapp.net. O Azure também atribui um endereço IP virtual.

No entanto, também é possível expor a aplicação no seu próprio nome de domínio, tal como **contoso.com**. Este artigo explica como reserva ou configurar um nome de domínio personalizado para funções de web do serviço Cloud.

Já compreende o que são CNAME e registros? [Atalhos anteriores a explicação](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Os procedimentos nesta tarefa aplicam-se para serviços Cloud do Azure. Para serviços de aplicações, consulte [mapear um nome DNS existente personalizado para aplicações Web do Azure](../app-service/app-service-web-tutorial-custom-domain.md). Para contas de armazenamento, consulte [configurar um nome de domínio personalizado para o ponto final de armazenamento de Blobs do Azure](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Comece a trabalhar mais rapidamente – utilizar o Azure nova [instruções](https://support.microsoft.com/kb/2990804)!  Faz a associação de um nome de domínio personalizado e de comunicação proteger (SSL) com serviços Cloud do Azure ou Web sites do Azure muito simples.
> 
> 

## <a name="understand-cname-and-a-records"></a>Compreender os registos CNAME e A
CNAME (ou mais registos de alias) e um registos permitem-lhe associar um nome de domínio com um servidor específico (ou de serviço neste caso,) entretanto eles funcionam de forma diferente. Também existem algumas considerações específicas ao utilizar registos com serviços Cloud do Azure que deve considerar antes de decidir qual deles usar.

### <a name="cname-or-alias-record"></a>Registo CNAME ou Alias
Um registo CNAME mapeia um *específicos* domínio, como **contoso.com** ou **www.contoso.com**, para um nome de domínio canônico. Neste caso, o nome de domínio canônico é o **.cloudapp [myapp] .net** aplicação alojada de nome de domínio do seu do Azure. Depois de criado, o CNAME cria um alias para o **.cloudapp [myapp] .net**. A entrada CNAME será resolvido para o endereço IP do seu **.cloudapp [myapp] .net** service automaticamente, portanto, se o endereço IP do serviço cloud for alterada, não é necessário efetuar qualquer ação.

> [!NOTE]
> Algumas entidades de registo do domínio apenas permitem mapear subdomínios quando utilizar um registo CNAME, como www.contoso.com e não nomes de raiz, como contoso.com. Para obter mais informações sobre os registos CNAME, consulte a documentação fornecida pela sua entidade de registo [a entrada na Wikipedia sobre o registo CNAME](https://en.wikipedia.org/wiki/CNAME_record), ou o [nomes de domínio de IETF - implementação e a especificação de](https://tools.ietf.org/html/rfc1035) documento.
> 
> 

### <a name="a-record"></a>Um registo
Uma *uma* registo mapeia um domínio, tal como **contoso.com** ou **www.contoso.com**, *ou um domínio de caráter universal* como  **\*. contoso.com**, para um endereço IP. No caso de um serviço Cloud do Azure, o IP virtual do serviço. Portanto, o principal benefício de um registo ao longo de um registo CNAME é que pode ter uma entrada que utiliza um caráter universal, por exemplo, \* **. contoso.com**, que trataria, tais como pedidos para vários subdomínios  **Mail.contoso.com**, **login.contoso.com**, ou **www.contso.com**.

> [!NOTE]
> Uma vez que um registo é mapeado para um endereço IP estático, ele não é possível resolver automaticamente as alterações para o endereço IP do seu serviço Cloud. O endereço IP utilizado pelo seu serviço de nuvem é alocado na primeira vez que implementar para uma ranhura vazia (produção ou teste.) Se eliminar a implementação para o bloco, o endereço IP é libertado pelo Azure e todas as implementações futuras para a ranhura poderão receber um novo endereço IP.
> 
> Convenientemente, o endereço IP de um bloco de implementação fornecido (produção ou teste) é mantido durante a troca entre o preparo e implementações de produção ou executar uma atualização direta de uma implementação existente. Para obter mais informações sobre estas ações a efetuar, consulte [como gerir serviços cloud](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Adicione um registo CNAME para o seu domínio personalizado
Para criar um registo CNAME, tem de adicionar uma nova entrada na tabela de DNS para o seu domínio personalizado, utilizando as ferramentas fornecidas pelo sua entidade de registo. Cada entidade de registo tem um método semelhante, mas ligeiramente diferente de especificar um registo CNAME, mas os conceitos são os mesmos.

1. Utilize um dos seguintes métodos para encontrar os **. cloudapp.net** nome de domínio atribuído ao seu serviço cloud.
   
   * Início de sessão para o [portal do Azure], selecione o seu serviço cloud, examinar a **Essentials** secção e, em seguida, localize o **URL do Site** entrada.
     
       ![secção de leitura rápida, que mostra o URL do site][csurl]
     
       **OR**
   * Instalar e configurar [do Azure Powershell](/powershell/azure/overview)e, em seguida, utilize o seguinte comando:
     
       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```
     
     Guarde o nome de domínio utilizado no URL devolvido por qualquer um dos métodos, pois irá precisar dele quando criar um registo CNAME.
2. Inicie sessão no Web site do seu DNS da entidade de registo e vá para a página para a gestão de DNS. Procurar ligações ou áreas do site identificadas como **nome de domínio**, **DNS**, ou **nome do servidor de gestão**.
3. Localize agora onde pode selecionar ou introduzir do CNAME. Poderá ter de selecionar o tipo de registo de um menu pendente ou ir para uma página de definições avançadas. Deve procurar as palavras **CNAME**, **Alias**, ou **subdomínios**.
4. Tem também de fornecer o domínio ou subdomínio alias para o CNAME, tal como **www** se pretender criar um alias para **www.customdomain.com**. Se pretender criar um alias para o domínio de raiz, poderá ser listado como o '**\@**"símbolo nas ferramentas DNS da entidade de registo.
5. Em seguida, tem de fornecer um nome de anfitrião canônico, que é o seu aplicativo **cloudapp.net** domínio neste caso.

Por exemplo, o seguinte registo CNAME encaminha todo o tráfego dos **www.contoso.com** ao **contoso.cloudapp.net**, o nome de domínio personalizado da sua aplicação implementada:

| Nome de alias/anfitrião/subdomínio | Domínio Canonical |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> Um visitante do **www.contoso.com** nunca verão o anfitrião verdadeiro (contoso.cloudapp.net), para que o processo de reencaminhamento é invisível para o utilizador final.
> 
> O exemplo acima aplica-se apenas ao tráfego no **www** subdomínio. Uma vez que não é possível utilizar carateres universais com os registos CNAME, tem de criar um CNAME para cada domínio/subdomínio. Se pretende direcionar o tráfego de subdomínios, por exemplo, *. contoso.com, para o seu endereço de cloudapp.net, pode configurar uma **URL de redirecionamento** ou **URL para a frente** entrada nas definições do DNS, ou criar um registo.
> 
> 

## <a name="add-an-a-record-for-your-custom-domain"></a>Adicionar um registo para o seu domínio personalizado
Para criar um registo, tem primeiro de localizar o endereço IP virtual do seu serviço cloud. Em seguida, adicione uma nova entrada na tabela de DNS para o seu domínio personalizado, utilizando as ferramentas fornecidas pelo sua entidade de registo. Cada entidade de registo tem um método semelhante, mas ligeiramente diferente de especificar um registo, mas os conceitos são os mesmos.

1. Utilize um dos seguintes métodos para obter o endereço IP do seu serviço cloud.
   
   * Início de sessão para o [portal do Azure], selecione o seu serviço cloud, examinar a **Essentials** secção e, em seguida, localize o **endereços IP públicos** entrada.
     
       ![secção de leitura rápida, que mostra o VIP][vip]
     
       **OR**
   * Instalar e configurar [do Azure Powershell](/powershell/azure/overview)e, em seguida, utilize o seguinte comando:
     
       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```
     
     Guarde o endereço IP, uma vez que precisará dela quando criar um registo.
2. Inicie sessão no Web site do seu DNS da entidade de registo e vá para a página para a gestão de DNS. Procurar ligações ou áreas do site identificadas como **nome de domínio**, **DNS**, ou **nome do servidor de gestão**.
3. Localize agora onde pode selecionar ou introduzir um registo. Poderá ter de selecionar o tipo de registo de um menu pendente ou ir para uma página de definições avançadas.
4. Selecione ou introduza o domínio ou subdomínio que irá utilizar este registo. Por exemplo, seleccione **www** se pretender criar um alias para **www.customdomain.com**. Se quiser criar uma entrada de carateres universais para todos os subdomínios, introduza "***'. Este irá cobrir como todos os subdomínios **mail.customdomain.com**, **login.customdomain.com**, e **www.customdomain.com**.
   
    Se pretender criar um registo para o domínio de raiz, poderá ser listado como o '**\@**"símbolo nas ferramentas DNS da entidade de registo.
5. Introduza o endereço IP do seu serviço cloud no campo fornecido. Isso associa a entrada de domínio utilizada no registo com o endereço IP da sua implementação do serviço cloud.

Por exemplo, o seguinte um registo por sua vez encaminha todo o tráfego dos **contoso.com** ao **137.135.70.239**, o endereço IP da sua aplicação implementada:

| Nome de anfitrião/subdomínio | Endereço IP |
| --- | --- |
| \@ |137.135.70.239 |

Este exemplo demonstra a criação de um registo para o domínio de raiz. Se quiser criar uma entrada de caráter universal para abranger todos os subdomínios, insira "***' como o subdomínio.

> [!WARNING]
> Endereços IP no Azure são dinâmicos por predefinição. Provavelmente irá querer utilizar um [reservado de endereços IP](../virtual-network/virtual-networks-reserved-public-ip.md) para se certificar de que o seu endereço IP não é alterada.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
* [Como gerir Serviços Cloud](cloud-services-how-to-manage-portal.md)
* [Como Mapear Conteúdo da CDN para um Domínio Personalizado](../cdn/cdn-map-content-to-custom-domain.md)
* [Configuração geral do seu serviço cloud](cloud-services-how-to-configure-portal.md).
* Saiba como [implementar um serviço cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Portal do Azure]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
