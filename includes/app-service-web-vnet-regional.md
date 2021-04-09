---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 821746856cb37781c8f6a2e58659ce7db43e1479
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609517"
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

Por padrão, a sua aplicação apenas encaminha o tráfego RFC1918 para o seu VNet. Se pretender encaminhar todo o tráfego de saída para o seu VNet, utilize os seguintes passos para adicionar a `WEBSITE_VNET_ROUTE_ALL` definição na sua aplicação: 

1. Aceda ao **UI de configuração** no portal da aplicação. Selecione **Nova definição da aplicação**.
1. `WEBSITE_VNET_ROUTE_ALL`Insira na caixa **Nome** e introduza na `1` caixa **Valor.**

   ![Fornecer definição de aplicação][4]

1. Selecione **OK**.
1. Selecione **Guardar**.

> [!NOTE]
> Quando encaminha todo o tráfego de saída para o seu VNet, está sujeito aos NSGs e UDRs que são aplicados na sua sub-rede de integração. Quando `WEBSITE_VNET_ROUTE_ALL` estiver programado para , o tráfego de saída ainda é enviado a partir dos `1` endereços que estão listados nas propriedades da sua aplicação, a menos que forneça rotas que direcionem o tráfego para outro lugar.
> 
> A integração regional do VNet não é capaz de usar a porta 25.

Existem algumas limitações com a utilização da Integração VNet com VNets na mesma região:

* Não se pode alcançar recursos através de ligações globais de observação.
* A funcionalidade está disponível em todas as unidades de escala de Serviço de Aplicações em Premium V2 e Premium V3. Também está disponível no Standard, mas apenas a partir de novas unidades de escala de Serviço de Aplicações. Se estiver numa unidade de escala mais antiga, só pode utilizar a funcionalidade a partir de um plano de Serviço de Aplicações Premium V2. Se quiser ter a certeza de que pode utilizar a funcionalidade num plano standard de Serviço de Aplicações, crie a sua aplicação num plano premium de Serviço de Aplicações V3. Esses planos só são apoiados nas nossas unidades de escala mais recentes. Pode reduzir a escala se quiser depois disso.  
* A sub-rede de integração pode ser utilizada apenas por um plano de Serviço de Aplicações.
* A funcionalidade não pode ser utilizada por aplicações de plano isolado que se encontrem num Ambiente de Serviço de Aplicações.
* A funcionalidade requer uma sub-rede não utilizada que seja um /28 ou maior num VNet do Gestor de Recursos Azure.
* A aplicação e o VNet devem estar na mesma região.
* Não é possível eliminar um VNet com uma aplicação integrada. Remova a integração antes de eliminar o VNet.
* Você pode ter apenas uma integração regional VNet por plano de Serviço de Aplicação. Várias aplicações no mesmo plano de Serviço de Aplicações podem usar o mesmo VNet.
* Não é possível alterar a subscrição de uma app ou de um plano enquanto há uma aplicação que está a usar a Integração VNet regional.
* A sua aplicação não consegue resolver endereços em Zonas Privadas Azure DNS sem alterações de configuração.

A Integração VNet depende de uma sub-rede dedicada. Quando se disponibiliza uma sub-rede, a sub-rede Azure perde cinco IPs desde o início. Um endereço é utilizado a partir da sub-rede de integração para cada instância de plano. Quando escala a sua aplicação para quatro instâncias, então são utilizados quatro endereços. 

Quando se escala ou desce em tamanho, o espaço de endereço necessário é duplicado por um curto período de tempo. Isto afeta os casos reais e disponíveis suportados para um determinado tamanho da sub-rede. O quadro a seguir mostra tanto os endereços máximos disponíveis por bloco CIDR como o impacto que isso tem na escala horizontal:

| Tamanho do bloco CIDR | Endereços disponíveis no máximo | Escala horizontal máxima (instâncias)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Assume que terá de escalar para cima ou para baixo em tamanho ou SKU em algum momento. 

Uma vez que o tamanho da sub-rede não pode ser alterado após a atribuição, use uma sub-rede suficientemente grande para acomodar qualquer escala que a sua aplicação possa alcançar. Para evitar problemas com capacidade de sub-rede, deve utilizar um /26 com 64 endereços.  

Quando quiser que as suas apps num outro plano cheguem a um VNet que já esteja ligado a apps noutro plano, selecione uma sub-rede diferente da que está a ser usada pela integração VNet pré-existente.

A funcionalidade é totalmente suportada tanto para aplicações Windows como Linux, incluindo [recipientes personalizados.](../articles/app-service/quickstart-custom-container.md) Todos os comportamentos agem da mesma forma entre aplicações Windows e aplicações Linux.

### <a name="service-endpoints"></a>Pontos finais de serviço

A Integração Regional de VNet permite-lhe chegar aos serviços Azure que são protegidos com pontos finais de serviço. Para aceder a um serviço de serviço seguro de ponto final, tem de fazer o seguinte:

1. Configure a Integração Regional de VNet com a sua aplicação web para se conectar a uma sub-rede específica para integração.
1. Vá ao serviço de destino e configuure os pontos finais do serviço contra a sub-rede de integração.

### <a name="network-security-groups"></a>Grupos de segurança de rede

Pode utilizar grupos de segurança de rede para bloquear o tráfego de entrada e saída de recursos num VNet. Uma aplicação que utiliza a Integração VNet regional pode usar um [grupo de segurança de rede][VNETnsg] para bloquear o tráfego de saída para recursos no seu VNet ou na internet. Para bloquear o tráfego para endereços públicos, tem de ter a definição de aplicação `WEBSITE_VNET_ROUTE_ALL` definida para `1` . As regras de entrada num NSG não se aplicam à sua aplicação porque a Integração VNet afeta apenas o tráfego de saída da sua aplicação.

Para controlar o tráfego de entrada na sua aplicação, utilize a funcionalidade Restrições de Acesso. Um NSG aplicado à sua sub-rede de integração está em vigor, independentemente de quaisquer rotas aplicadas à sua sub-rede de integração. Se `WEBSITE_VNET_ROUTE_ALL` estiver definido e não tiver rotas que `1` afetem o tráfego de endereço público na sua sub-rede de integração, todo o tráfego de saída ainda está sujeito a NSGs atribuídos à sua sub-rede de integração. Quando `WEBSITE_VNET_ROUTE_ALL` não está definido, os NSGs são aplicados apenas ao tráfego RFC1918.

### <a name="routes"></a>Rotas

Pode utilizar as tabelas de rotas para encaminhar o tráfego de saída da sua app para onde quiser. Por predefinição, as tabelas de rotas apenas afetam o tráfego de destino RFC1918. Quando se prepara `WEBSITE_VNET_ROUTE_ALL` `1` para, todas as suas chamadas de saída são afetadas. As rotas que estão definidas na sua sub-rede de integração não afetarão respostas a pedidos de aplicações de entrada. Os destinos comuns podem incluir dispositivos de firewall ou gateways.

Se quiser encaminhar todo o tráfego de saída no local, pode utilizar uma mesa de rota para enviar todo o tráfego de saída para o seu gateway ExpressRoute. Se fizer a rota do tráfego para um gateway, certifique-se de definir rotas na rede externa para enviar quaisquer respostas de volta.

As rotas do Border Gateway Protocol (BGP) também afetam o tráfego da sua aplicação. Se tiver rotas BGP a partir de algo como um gateway ExpressRoute, o tráfego de saída da sua aplicação é afetado. Por padrão, as rotas BGP afetam apenas o tráfego de destino RFC1918. Quando `WEBSITE_VNET_ROUTE_ALL` estiver programado para , todo o tráfego de saída pode ser afetado pelas suas rotas `1` BGP.

### <a name="azure-dns-private-zones"></a>Zonas privadas Azure DNS 

Depois de a sua aplicação se integrar com o seu VNet, utiliza o mesmo servidor DNS com o qual o seu VNet está configurado. Por padrão, a sua aplicação não funcionará com zonas privadas Azure DNS. Para trabalhar com as zonas privadas Azure DNS, é necessário adicionar as seguintes definições de aplicações:

1. `WEBSITE_DNS_SERVER` com valor `168.63.129.16`
1. `WEBSITE_VNET_ROUTE_ALL` com valor `1`

Estas definições enviam todas as suas chamadas de saída da sua app para o seu VNet e permitem que a sua aplicação aceda a uma zona privada do Azure DNS. Com estas definições, a sua aplicação pode utilizar o Azure DNS consultando a zona privada dns ao nível do trabalhador.  

### <a name="private-endpoints"></a>Pontos Finais Privados

Se quiser fazer chamadas para [Private Endpoints,][privateendpoints]então deve certificar-se de que as suas pesquisas de DNS resolvem-se para o ponto final privado. Pode impor este comportamento de uma das seguintes formas: 

* Integre com zonas privadas Azure DNS. Quando o seu VNet não tem um servidor DNS personalizado, isto é feito automaticamente.
* Gerencie o ponto final privado no servidor DNS utilizado pela sua aplicação. Para isso, deve conhecer o endereço de ponto final privado e, em seguida, indicar o ponto final que está a tentar alcançar para esse endereço usando um registo A.
* Configure o seu próprio servidor DNS para encaminhar para as zonas privadas do Azure DNS.

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
