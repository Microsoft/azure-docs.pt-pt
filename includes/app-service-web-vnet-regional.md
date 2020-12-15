---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 57b2955f8cec059cd20d353eba31dc39ad992d50
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506290"
---
A utilização da Integração VNet regional permite que a sua aplicação aceda:

* Recursos num VNet na mesma região que a sua aplicação.
* Os recursos em VNets espreguiçadados para o VNet a sua aplicação está integrada.
* Serviços seguros de serviço.
* Recursos através das ligações Azure ExpressRoute.
* Recursos no VNet com os qual está integrado.
* Recursos através de ligações espreitadas, que incluem ligações Azure ExpressRoute.
* Pontos finais privados 

Quando utilizar a Integração VNet com VNets na mesma região, pode utilizar as seguintes funcionalidades de networking Azure:

* **Grupos de segurança de rede (NSGs)**: Pode bloquear o tráfego de saída com um NSG colocado na sua sub-rede de integração. As regras de entrada não se aplicam porque não pode usar a Integração VNet para fornecer acesso à sua aplicação.
* **Tabelas de rotas (UDRs)**: Pode colocar uma tabela de rota na sub-rede de integração para enviar o tráfego de saída onde quiser.

Por padrão, a sua aplicação apenas encaminha o tráfego RFC1918 para o seu VNet. Se quiser encaminhar todo o tráfego de saída para o seu VNet, aplique a definição da aplicação WEBSITE_VNET_ROUTE_ALL para a sua aplicação. Para configurar a definição da aplicação:

1. Aceda ao **UI de configuração** no portal da aplicação. Selecione **Nova definição da aplicação**.
1. Introduza **WEBSITE_VNET_ROUTE_ALL** na caixa **Nome** e introduza **1** na caixa **Valor.**

   ![Fornecer definição de aplicação][4]

1. Selecione **OK**.
1. Selecione **Guardar**.

> [!NOTE]
> Se encaminhar todo o tráfego de saída para o seu VNet, está sujeito aos NSGs e UDRs que são aplicados na sua sub-rede de integração. Quando encaminha todo o tráfego de saída para o seu VNet, os seus endereços de saída ainda são os endereços de saída que estão listados nas propriedades da sua aplicação, a menos que forneça rotas para enviar o tráfego para outro lugar.

Existem algumas limitações com a utilização da Integração VNet com VNets na mesma região:

* Não se pode alcançar recursos através de ligações globais de observação.
* A funcionalidade está disponível em todas as unidades de escala de Serviço de Aplicações em Premium V2 e Premium V3. Também está disponível na Standard, mas apenas a partir de novas unidades de escala de Serviço de Aplicações. Se estiver numa unidade de escala mais antiga, só pode utilizar a funcionalidade a partir de um plano de Serviço de Aplicações Premium V2. Se quiser ter a certeza de poder utilizar a funcionalidade num plano standard de Serviço de Aplicações, crie a sua aplicação num plano premium de Serviço de Aplicações V3. Esses planos só são apoiados nas nossas unidades de escala mais recentes. Pode reduzir a escala se quiser depois disso.  
* A sub-rede de integração pode ser utilizada apenas por um plano de Serviço de Aplicações.
* A funcionalidade não pode ser utilizada por aplicações de plano isolado que se encontrem num Ambiente de Serviço de Aplicações.
* A funcionalidade requer uma sub-rede não utilizada que seja um /28 ou maior num VNet do Gestor de Recursos Azure.
* A aplicação e o VNet devem estar na mesma região.
* Não é possível eliminar um VNet com uma aplicação integrada. Remova a integração antes de eliminar o VNet.
* Só é possível integrar-se com VNets na mesma subscrição que a aplicação.
* Você pode ter apenas uma integração regional VNet por plano de Serviço de Aplicação. Várias aplicações no mesmo plano de Serviço de Aplicações podem usar o mesmo VNet.
* Não é possível alterar a subscrição de uma app ou de um plano enquanto há uma aplicação que está a usar a Integração VNet regional.
* A sua aplicação não consegue resolver endereços em Zonas Privadas Azure DNS sem alterações de configuração

A Integração VNet depende da utilização de uma sub-rede dedicada.  Quando se disponibiliza uma sub-rede, a sub-rede Azure perde 5 IPs desde o início. Um endereço é utilizado a partir da sub-rede de integração para cada instância de plano. Se escalar a sua aplicação para quatro instâncias, então são utilizados quatro endereços. O débito de 5 endereços do tamanho da sub-rede significa que os endereços máximos disponíveis por bloco CIDR são:

- /28 tem 11 endereços
- /27 tem 27 endereços
- /26 tem 59 endereços

Se escalar para cima ou para baixo em tamanho, precisa do dobro da sua necessidade de endereço por um curto período de tempo. Os limites de tamanho significam que as verdadeiras instâncias suportadas disponíveis por tamanho da sub-rede são, se a sua sub-rede for:

- /28, a sua escala horizontal máxima é de 5 instâncias
- /27, a sua escala horizontal máxima é de 13 instâncias
- /26, a sua escala horizontal máxima é de 29 instâncias

Os limites indicados na escala horizontal máxima pressupõe que terá de escalar para cima ou para baixo em tamanho ou SKU em algum ponto. 

Uma vez que o tamanho da sub-rede não pode ser alterado após a atribuição, use uma sub-rede suficientemente grande para acomodar qualquer escala que a sua aplicação possa alcançar. Para evitar problemas com capacidade de sub-rede, um /26 com 64 endereços é o tamanho recomendado.  

Se quiser que as suas apps num outro plano cheguem a um VNet que já esteja ligado a apps noutro plano, selecione uma sub-rede diferente da que está a ser usada pela integração VNet pré-existente.

A funcionalidade é totalmente suportada tanto para aplicações Windows como Linux, incluindo [recipientes personalizados.](../articles/app-service/quickstart-custom-container.md) Todos os comportamentos agem da mesma forma entre aplicações Windows e aplicações Linux.

### <a name="service-endpoints"></a>Pontos finais de serviço

A Integração Regional de VNet permite-lhe utilizar pontos finais de serviço. Para utilizar pontos finais de serviço com a sua aplicação, utilize a Integração Regional de VNet para ligar a um VNet selecionado e, em seguida, configure os pontos finais do serviço com o serviço de destino na sub-rede que utilizou para a integração. Se então quisesse aceder a um serviço sobre os pontos finais de serviço:

1. Configure a Integração Regional de VNet com a sua aplicação web
1. ir para o serviço de destino e configurar pontos finais de serviço contra a sub-rede utilizada para a integração

### <a name="network-security-groups"></a>Grupos de segurança de rede

Pode utilizar grupos de segurança de rede para bloquear o tráfego de entrada e saída de recursos num VNet. Uma aplicação que utiliza a Integração VNet regional pode usar um [grupo de segurança de rede][VNETnsg] para bloquear o tráfego de saída para recursos no seu VNet ou na internet. Para bloquear o tráfego para endereços públicos, tem de ter a definição de aplicação WEBSITE_VNET_ROUTE_ALL definida para 1. As regras de entrada num NSG não se aplicam à sua aplicação porque a Integração VNet afeta apenas o tráfego de saída da sua aplicação.

Para controlar o tráfego de entrada na sua aplicação, utilize a funcionalidade Restrições de Acesso. Um NSG aplicado à sua sub-rede de integração está em vigor, independentemente de quaisquer rotas aplicadas à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL estiver definido para 1 e não tiver rotas que afetem o tráfego de endereço público na sua sub-rede de integração, todo o tráfego de saída ainda está sujeito a NSGs atribuídos à sua sub-rede de integração. Se WEBSITE_VNET_ROUTE_ALL não estiver definido, os NSGs são aplicados apenas ao tráfego RFC1918.

### <a name="routes"></a>Rotas

Pode utilizar as tabelas de rotas para encaminhar o tráfego de saída da sua app para onde quiser. Por predefinição, as tabelas de rotas apenas afetam o tráfego de destino RFC1918. Se definir WEBSITE_VNET_ROUTE_ALL para 1, todas as suas chamadas de saída são afetadas. As rotas que estão definidas na sua sub-rede de integração não afetarão respostas a pedidos de aplicações de entrada. Os destinos comuns podem incluir dispositivos de firewall ou gateways.

Se quiser encaminhar todo o tráfego de saída no local, pode utilizar uma mesa de rota para enviar todo o tráfego de saída para o seu gateway ExpressRoute. Se fizer a rota do tráfego para um gateway, certifique-se de definir rotas na rede externa para enviar quaisquer respostas de volta.

As rotas do Border Gateway Protocol (BGP) também afetam o tráfego da sua aplicação. Se tiver rotas BGP a partir de algo como um gateway ExpressRoute, o tráfego de saída da sua aplicação será afetado. Por padrão, as rotas BGP afetam apenas o tráfego de destino RFC1918. Se WEBSITE_VNET_ROUTE_ALL estiver definido para 1, todo o tráfego de saída pode ser afetado pelas suas rotas BGP.

### <a name="azure-dns-private-zones"></a>Zonas Privadas Azure DNS 

Depois de a sua aplicação se integrar com o seu VNet, utiliza o mesmo servidor DNS com o qual o seu VNet está configurado. Por padrão, a sua aplicação não funcionará com as Zonas Privadas Azure DNS. Para trabalhar com as Zonas Privadas Azure DNS, tem de adicionar as seguintes definições de aplicações:


1. WEBSITE_DNS_SERVER com o valor 168.63.129.16
1. WEBSITE_VNET_ROUTE_ALL com o valor 1


Estas definições enviarão todas as suas chamadas de saída da sua app para o seu VNet, além de permitir que a sua aplicação utilize zonas privadas Azure DNS.   Estas definições enviarão todas as chamadas de saída da sua aplicação para o seu VNet. Além disso, permitirá que a app utilize o Azure DNS consultando a Zona Privada de DNS ao nível do trabalhador. Esta funcionalidade deve ser utilizada quando uma aplicação em execução estiver a aceder a uma Zona Privada de DNS.

> [!NOTE]
>Tentar adicionar um domínio personalizado a uma Aplicação Web utilizando a Zona Privada de DNS não é possível com a Integração VNET. A validação de domínio personalizado é feita ao nível do controlador, não ao nível do trabalhador, o que impede que os registos dns sejam vistos. Para utilizar um domínio personalizado a partir de uma Zona Privada de DNS, a validação teria de ser contornada usando um Gateway de Aplicações ou um Ambiente de Serviço de Aplicações ILB.

### <a name="private-endpoints"></a>Pontos finais privados

Se quiser fazer chamadas para [Private Endpoints,][privateendpoints]então tem de garantir que as suas pesquisas de DNS serão resolvidas para o ponto final privado. Para garantir que as pesquisas dns da sua aplicação apontarão para os seus pontos finais privados, pode:

* integrar com Azure DNS Private Zones. Se o seu VNet não tiver um servidor DNS personalizado, este será automático
* gerir o ponto final privado no servidor DNS utilizado pela sua aplicação. Para isso, precisa de saber o endereço de ponto final privado e, em seguida, apontar o ponto final que está a tentar alcançar para esse endereço com um registo A.
* Configure o seu próprio servidor DNS para encaminhar para as zonas privadas do Azure DNS

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
