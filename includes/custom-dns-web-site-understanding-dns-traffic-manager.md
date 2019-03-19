---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: f42a97cdd74d360bc047ef561cbe626d526f9e4a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58124486"
---
O sistema de nomes de domínio (DNS) é utilizado para localizar coisas na internet. Por exemplo, quando introduzir um endereço no seu browser ou clica numa hiperligação numa página web, utiliza DNS para traduzir o domínio para um endereço IP. O endereço IP é semelhante a um endereço de rua, mas não é compatível com muito humana. Por exemplo, é muito mais fácil lembrar-se de um nome DNS como **contoso.com** que vale lembrar-se de um endereço IP, como 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

O sistema DNS baseia *registos*. Registos de associar um específico *nome*, tal como **contoso.com**, com um endereço IP ou outro nome DNS. Quando um aplicativo, como um navegador da web, procura um nome no DNS, localiza o registo e, utiliza a tudo o que ele aponta para, como o endereço. Se o valor que ele aponta para um endereço IP, o navegador irá utilizar esse valor. Se ele aponta para outro nome DNS, a aplicação tem de repetir a resolução. Em última análise, resolução de nomes de todos os vai terminar dentro de um endereço IP.

Quando cria um Web site do Azure, um nome DNS é atribuído automaticamente ao site. Este nome é formada  **&lt;yoursitename&gt;. azurewebsites.net**. Quando adiciona o seu Web site como um ponto de final do Gestor de tráfego do Azure, o seu Web site, em seguida, está acessível através da  **&lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** domínio.

> [!NOTE]
> Quando o seu Web site está configurado como um ponto de final do Gestor de tráfego, utilizará a **. trafficmanager.net** durante a criação de registos DNS de endereços.
> 
> Só pode utilizar os registos CNAME com o Gestor de tráfego
> 
> 

Também existem vários tipos de registos, cada um com suas próprias funções e limitações, mas configurado para como pontos finais do Gestor de tráfego de Web sites, precisamos apenas saber sobre um; *CNAME* registos.

### <a name="cname-or-alias-record"></a>Registo CNAME ou Alias
Um registo CNAME mapeia um *específicos* nome de DNS, como **mail.contoso.com** ou **www\.contoso.com**, para outro nome de domínio (canônica). No caso de Web sites do Azure utilizando o Gestor de tráfego, o nome de domínio canônico é o  **&lt;myapp >. trafficmanager.net** nome de domínio do perfil do Traffic Manager. Depois de criado, o CNAME cria um alias para o  **&lt;myapp >. trafficmanager.net** nome de domínio. A entrada CNAME será resolvido para o endereço IP do seu  **&lt;myapp >. trafficmanager.net** nome de domínio automaticamente, portanto, se alterar o endereço IP do Web site, não é necessário efetuar qualquer ação.

Assim que o tráfego chega ao Gestor de tráfego, em seguida, encaminha o tráfego para o seu Web site, usando o método que está configurado para balanceamento de carga. Isso é completamente transparente para os visitantes ao seu Web site. Apenas verá o nome de domínio personalizado no browser.

> [!NOTE]
> Algumas entidades de registo do domínio apenas permitem mapear subdomínios quando utilizar um registo CNAME, como **www\.contoso.com**e não raiz nomes, como **contoso.com**. Para obter mais informações sobre os registos CNAME, consulte a documentação fornecida pela sua entidade de registo <a href="https://en.wikipedia.org/wiki/CNAME_record">a entrada na Wikipedia sobre o registo CNAME</a>, ou o <a href="https://tools.ietf.org/html/rfc1035">nomes de domínio de IETF - implementação e a especificação de</a> documento.

