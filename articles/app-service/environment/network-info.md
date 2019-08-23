---
title: Considerações de rede com o Ambiente do Serviço de Aplicativo-Azure
description: Explica o tráfego de rede do ASE e como definir NSGs e UDRs com seu ASE
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 01a7c4e41dd628ec8671555daf828b67bebba23e
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898671"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Considerações de rede para um Ambiente do Serviço de Aplicativo #

## <a name="overview"></a>Descrição geral ##

 O Azure [ambiente do serviço de aplicativo][Intro] é uma implantação do serviço de Azure app em uma sub-rede em sua VNet (rede virtual) do Azure. Há dois tipos de implantação para um ambiente do serviço de aplicativo (ASE):

- **Ase externo**: Expõe os aplicativos hospedados pelo ASE em um endereço IP acessível pela Internet. Para obter mais informações, consulte [criar um ase externo][MakeExternalASE].
- **ASE ILB**: Expõe os aplicativos hospedados pelo ASE em um endereço IP dentro de sua VNet. O ponto de extremidade interno é um ILB (balanceador de carga interno), que é o motivo pelo qual ele é chamado de ASE ILB. Para obter mais informações, consulte [criar e usar um ase ILB][MakeILBASE].

Todos os ASEs, external e ILB, têm um VIP público que é usado para tráfego de gerenciamento de entrada e como o endereço de ao fazer chamadas do ASE para a Internet. As chamadas de um ASE que vão para a Internet deixam a rede virtual por meio do VIP atribuído para o ASE. O IP público desse VIP é o IP de origem para todas as chamadas do ASE que vão para a Internet. Se os aplicativos em seu ASE fizerem chamadas para recursos em sua VNet ou por uma VPN, o IP de origem será um dos IPs na sub-rede usada pelo ASE. Como o ASE está dentro da VNet, ele também pode acessar recursos na VNet sem nenhuma configuração adicional. Se a VNet estiver conectada à sua rede local, os aplicativos em seu ASE também terão acesso aos recursos sem configuração adicional.

![ASE externo][1] 

Se você tiver um ASE externo, o VIP público também será o ponto de extremidade para o qual seus aplicativos ASE resolvem:

* HTTP/S 
* FTP/S
* Implantação da Web
* Depuração remota

![ASE ILB][2]

Se você tiver um ASE ILB, o endereço do endereço ILB será o ponto de extremidade para HTTP/S, FTP/S, implantação da Web e depuração remota.

## <a name="ase-subnet-size"></a>Tamanho da sub-rede ASE ##

O tamanho da sub-rede usada para hospedar um ASE não pode ser alterado depois que o ASE é implantado.  O ASE usa um endereço para cada função de infraestrutura, bem como para cada instância de plano do serviço de aplicativo isolado.  Além disso, há cinco endereços usados pela rede do Azure para cada sub-rede que é criada.  Um ASE sem nenhum plano do serviço de aplicativo usará 12 endereços antes de criar um aplicativo.  Se for um ASE ILB, ele usará 13 endereços antes de criar um aplicativo nesse ASE. À medida que você dimensiona seu ASE, as funções de infraestrutura são adicionadas a cada 15 e 20 de suas instâncias de plano do serviço de aplicativo.

   > [!NOTE]
   > Nada mais pode estar na sub-rede, mas no ASE. Certifique-se de escolher um espaço de endereço que permita o crescimento futuro. Você não pode alterar essa configuração mais tarde. Recomendamos um tamanho de `/24` com 256 endereços.

Quando você escala ou reduz verticalmente, novas funções do tamanho apropriado são adicionadas e, em seguida, suas cargas de trabalho são migradas do tamanho atual para o tamanho de destino. As VMs originais são removidas somente depois que as cargas de trabalho foram migradas. Se você tivesse um ASE com instâncias de 100 ASP, haveria um período em que é necessário dobrar o número de VMs.  É por esse motivo que recomendamos o uso de '/24 ' para acomodar quaisquer alterações que você possa precisar.  

## <a name="ase-dependencies"></a>Dependências do ASE ##

### <a name="ase-inbound-dependencies"></a>Dependências de entrada do ASE ###

Apenas para que o ASE opere, o ASE exige que as seguintes portas sejam abertas:

| Utilizar | De | Para |
|-----|------|----|
| Gestão | Endereços de gerenciamento do serviço de aplicativo | Sub-rede ASE: 454, 455 |
|  Comunicação interna do ASE | Sub-rede ASE: Todas as portas | Sub-rede ASE: Todas as portas
|  Permitir entrada do Azure Load Balancer | Balanceador de carga do Azure | Sub-rede ASE: 16001

Há duas outras portas que podem ser exibidas como abertas em uma verificação de porta, 7654 e 1221. Eles respondem com um endereço IP e nada mais. Se desejado, eles poderão ser bloqueados. 

O tráfego de gerenciamento de entrada fornece o comando e o controle do ASE, além do monitoramento do sistema. Os endereços de origem para esse tráfego estão listados no documento [endereços de gerenciamento do ase][ASEManagement] . A configuração de segurança de rede precisa permitir o acesso dos endereços de gerenciamento do ASE nas portas 454 e 455. Se você bloquear o acesso desses endereços, seu ASE se tornará não íntegro e, em seguida, será suspenso. O tráfego TCP que entra nas portas 454 e 455 deve voltar do mesmo VIP ou você terá um problema de roteamento assimétrico. 

Na sub-rede do ASE, há muitas portas usadas para comunicação de componente interno e elas podem ser alteradas. Isso exige que todas as portas na sub-rede do ASE estejam acessíveis na sub-rede do ASE. 

Para a comunicação entre o balanceador de carga do Azure e a sub-rede do ASE, as portas mínimas que precisam ser abertas são 454, 455 e 16001. A porta 16001 é usada para o tráfego de Keep Alive entre o balanceador de carga e o ASE. Se você estiver usando um ASE ILB, poderá bloquear o tráfego para apenas as portas 454, 455 e 16001.  Se você estiver usando um ASE externo, precisará levar em conta as portas de acesso do aplicativo normal.  

As outras portas com as quais você precisa se preocupar são as portas do aplicativo:

| Utilizar | Portas |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Depuração remota do Visual Studio  |  4020, 4022, 4024 |
|  Serviço de Implantação da Web | 8172 |

Se você bloquear as portas do aplicativo, o ASE ainda poderá funcionar, mas seu aplicativo poderá não.  Se você estiver usando endereços IP atribuídos ao aplicativo com um ASE externo, será necessário permitir o tráfego dos IPs atribuídos aos seus aplicativos para a sub-rede do ASE nas portas mostradas no portal do ASE > página endereços IP.

### <a name="ase-outbound-dependencies"></a>Dependências de saída do ASE ###

Para acesso de saída, um ASE depende de vários sistemas externos. Muitas dessas dependências do sistema são definidas com nomes DNS e não são mapeadas para um conjunto fixo de endereços IP. Assim, o ASE requer acesso de saída da sub-rede do ASE para todos os IPs externos em uma variedade de portas. 

O ASE se comunica com endereços acessíveis à Internet nas seguintes portas:

| Utilizações | Portas |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| 8CRL, atualizações do Windows, dependências do Linux, serviços do Azure | 80/443 |
| SQL do Azure | 1433 | 
| Monitorização | 12000 |

As dependências de saída são listadas no documento que descreve o [bloqueio ambiente do serviço de aplicativo tráfego de saída](./firewall-integration.md). Se o ASE perder o acesso a suas dependências, ele deixará de funcionar. Quando isso acontece por tempo suficiente, o ASE é suspenso. 

### <a name="customer-dns"></a>DNS do cliente ###

Se a VNet estiver configurada com um servidor DNS definido pelo cliente, as cargas de trabalho do locatário o usarão. O ASE usa o DNS do Azure para fins de gerenciamento. Se a VNet estiver configurada com um servidor DNS selecionado pelo cliente, o servidor DNS deverá estar acessível a partir da sub-rede que contém o ASE.

Para testar a resolução DNS de seu aplicativo Web, você pode usar o comando do console *nameresolver*. Vá para a janela de depuração no site do SCM para seu aplicativo ou vá para o aplicativo no portal e selecione console. No prompt do Shell, você pode emitir o comando *nameresolver* junto com o nome DNS que deseja pesquisar. O resultado obtido é o mesmo que o seu aplicativo obteria ao fazer a mesma pesquisa. Se você usar o Nslookup, fará uma pesquisa usando o DNS do Azure.

Se você alterar a configuração de DNS da VNet em que seu ASE está, será necessário reinicializar o ASE. Para evitar a reinicialização do ASE, é altamente recomendável que você defina as configurações de DNS para sua VNet antes de criar seu ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Dependências do portal ##

Além das dependências funcionais do ASE, há alguns itens extras relacionados à experiência do Portal. Alguns dos recursos do portal do Azure dependem do acesso direto ao site do _SCM_. Para cada aplicativo no serviço Azure App, há duas URLs. A primeira URL é acessar seu aplicativo. A segunda URL é acessar o site do SCM, que também é chamado de _console do kudu_. Os recursos que usam o site do SCM incluem:

-   Trabalhos da Web
-   Functions
-   Transmissão em fluxo de registo
-   Kudu
-   Extensões
-   Explorador de processos
-   Consola

Quando você usa um ASE ILB, o site do SCM não é acessível de fora da VNet. Alguns recursos não funcionarão no portal do aplicativo, pois eles exigem acesso ao site do SCM de um aplicativo. Você pode se conectar ao site do SCM diretamente em vez de usar o Portal. 

Se o ASE ILB for o nome de domínio *contoso.appserviceenvironment.net* e o nome do aplicativo for *TestApp*, o aplicativo será atingido em *TestApp.contoso.appserviceenvironment.net*. O site do SCM que acompanha ele é alcançado em *TestApp.SCM.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>Endereços IP do ASE ##

Um ASE tem alguns endereços IP que você deve conhecer. São:

- **Endereço IP de entrada pública**: Usado para o tráfego de aplicativo em um ASE externo e o tráfego de gerenciamento em um ASE externo e um ASE ILB.
- **IP público de saída**: Usado como o IP "de" para conexões de saída do ASE que deixam a VNet, que não são roteadas para uma VPN.
- **Endereço IP do ILB**: O endereço IP ILB existe apenas em um ASE ILB.
- **Endereços SSL baseados em IP atribuídos ao aplicativo**: Possível somente com um ASE externo e quando o SSL baseado em IP está configurado.

Todos esses endereços IP estão visíveis na portal do Azure da interface do usuário do ASE. Se você tiver um ASE ILB, o IP para o ILB será listado.

   > [!NOTE]
   > Esses endereços IP não serão alterados desde que seu ASE permaneça ativo e em execução.  Se seu ASE for suspenso e restaurado, os endereços usados pelo seu ASE serão alterados. A causa normal para que um ASE se torne suspenso é se você bloquear o acesso de gerenciamento de entrada ou bloquear o acesso a uma dependência do ASE. 

![Endereços IP][3]

### <a name="app-assigned-ip-addresses"></a>Endereços IP atribuídos ao aplicativo ###

Com um ASE externo, você pode atribuir endereços IP a aplicativos individuais. Você não pode fazer isso com um ASE ILB. Para obter mais informações sobre como configurar seu aplicativo para ter seu próprio endereço IP, consulte [associar um certificado SSL personalizado existente ao serviço de Azure app](../app-service-web-tutorial-custom-ssl.md).

Quando um aplicativo tem seu próprio endereço SSL baseado em IP, o ASE reserva duas portas para mapear para esse endereço IP. Uma porta é para o tráfego HTTP e a outra porta é para HTTPS. Essas portas são listadas na interface do usuário do ASE na seção endereços IP. O tráfego deve ser capaz de alcançar essas portas do VIP ou os aplicativos estão inacessíveis. Esse requisito é importante de se lembrar quando você configura NSGs (grupos de segurança de rede).

## <a name="network-security-groups"></a>Grupos de Segurança de Rede ##

Os [grupos de segurança de rede][NSGs] fornecem a capacidade de controlar o acesso à rede em uma VNet. Quando você usa o portal, há uma regra de negação implícita na prioridade mais baixa para negar tudo. O que você cria é de suas regras de permissão.

Em um ASE, você não tem acesso às VMs usadas para hospedar o próprio ASE. Eles estão em uma assinatura gerenciada pela Microsoft. Se você quiser restringir o acesso aos aplicativos no ASE, defina NSGs na sub-rede do ASE. Ao fazer isso, preste muita atenção às dependências do ASE. Se você bloquear qualquer dependência, o ASE parará de funcionar.

NSGs pode ser configurado por meio do portal do Azure ou por meio do PowerShell. As informações aqui mostram o portal do Azure. Você cria e gerencia o NSGs no portal como um recurso de nível superior em **rede**.

As entradas necessárias em um NSG para que um ASE funcione, são permitir o tráfego:

**Entrada**
* na marca de serviço IP AppServiceManagement nas portas 454.455
* do balanceador de carga na porta 16001
* da sub-rede do ASE para a sub-rede do ASE em todas as portas

**SA**
* para todos os IPs na porta 123
* para todos os IPs nas portas 80, 443
* para a marca de serviço IP AzureSQL nas portas 1433
* para todos os IPs na porta 12000
* para a sub-rede do ASE em todas as portas

A porta DNS não precisa ser adicionada, pois o tráfego para DNS não é afetado pelas regras NSG. Essas portas não incluem as portas que seus aplicativos exigem para uso bem-sucedido. As portas de acesso do aplicativo normal são:

| Utilizar | Portas |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Depuração remota do Visual Studio  |  4020, 4022, 4024 |
|  Serviço de Implantação da Web | 8172 |

Quando os requisitos de entrada e saída são levados em conta, o NSGs deve ser semelhante ao NSGs mostrado neste exemplo. 

![Regras de segurança de entrada][4]

Uma regra padrão permite que os IPs na VNet se comuniquem com a sub-rede do ASE. Outra regra padrão habilita o balanceador de carga, também conhecido como VIP público, para se comunicar com o ASE. Para ver as regras padrão, selecione **regras padrão** ao lado do ícone **Adicionar** . Se você colocar uma regra negar tudo antes das regras padrão, você impedirá o tráfego entre o VIP e o ASE. Para evitar o tráfego proveniente de dentro da VNet, adicione sua própria regra para permitir a entrada. Use uma origem igual a AzureLoadBalancer com um destino de **qualquer** e um intervalo de portas **\*** de. Como a regra NSG é aplicada à sub-rede do ASE, você não precisa ser específico no destino.

Se você tiver atribuído um endereço IP ao seu aplicativo, certifique-se de manter as portas abertas. Para ver as portas, selecione **ambiente do serviço de aplicativo** > **endereços IP**.  

Todos os itens mostrados nas regras de saída a seguir são necessários, exceto o último item. Eles habilitam o acesso à rede para as dependências do ASE que foram observadas anteriormente neste artigo. Se você bloquear qualquer um deles, seu ASE deixará de funcionar. O último item da lista permite que seu ASE se comunique com outros recursos em sua VNet.

![Regras de segurança de saída][5]

Depois que os NSGs forem definidos, atribua-os à sub-rede em que seu ASE está. Se você não se lembrar da VNet ou sub-rede do ASE, poderá vê-la na página do portal do ASE. Para atribuir o NSG à sua sub-rede, vá para a interface do usuário da sub-rede e selecione o NSG.

## <a name="routes"></a>Rotas ##

O túnel forçado é quando você define rotas em sua VNet para que o tráfego de saída não vá diretamente para a Internet, mas em outro lugar, como um gateway de ExpressRoute ou um dispositivo virtual.  Se você precisar configurar seu ASE de maneira tal, leia o documento sobre como [configurar seu ambiente do serviço de aplicativo com túnel forçado][forcedtunnel].  Este documento lhe dirá as opções disponíveis para trabalhar com o ExpressRoute e o túnel forçado.

Quando você cria um ASE no portal, também criamos um conjunto de tabelas de rotas na sub-rede que é criada com o ASE.  Essas rotas simplesmente dizem para enviar tráfego de saída diretamente para a Internet.  
Para criar as mesmas rotas manualmente, siga estas etapas:

1. Aceda ao portal do Azure. Selecione **rede** > **tabelas de rotas**.

2. Crie uma nova tabela de rotas na mesma região que sua VNet.

3. Na interface do usuário da tabela de rotas, selecione **rotas** > **Adicionar**.

4. Defina o **tipo do próximo salto** como **Internet** e o **prefixo de endereço** como **0.0.0.0/0**. Selecione **Guardar**.

    Em seguida, você verá algo semelhante ao seguinte:

    ![Rotas funcionais][6]

5. Depois de criar a nova tabela de rotas, vá para a sub-rede que contém o ASE. Selecione a tabela de rotas na lista no Portal. Depois de salvar a alteração, você deverá ver o NSGs e as rotas anotadas com sua sub-rede.

    ![NSGs e rotas][7]

## <a name="service-endpoints"></a>Pontos Finais de Serviço ##

Os Pontos Finais de Serviço permitem restringir o acesso aos serviços multi-inquilino a um conjunto de sub-redes e redes virtuais do Azure. Você pode ler mais sobre pontos de extremidade de serviço na documentação dos [pontos de extremidade de serviço de rede virtual][serviceendpoints] . 

Quando ativar Pontos Finais de Serviço num recurso, são criadas rotas com uma prioridade mais alta do que todas as outras rotas. Se você usar pontos de extremidade de serviço em qualquer serviço do Azure, com um ASE em túnel forçado, o tráfego para esses serviços não será ativado em túnel. 

Quando estão ativados Pontos Finais de Serviço numa sub-rede com uma instância do SQL do Azure, todas as instâncias do SQL do Azure ligadas a partir dessa sub-rede precisam de ter Pontos Finais de Serviço ativados. Se quiser aceder a várias instâncias de SQL do Azure a partir da mesma sub-rede, não pode ativar Pontos Finais de Serviço numa instância do SQL do Azure e não na outra. Nenhum outro serviço do Azure se comporta como o SQL do Azure em relação aos pontos de extremidade de serviço. Quando ativar Pontos Finais de Serviço com o Armazenamento do Azure, pode bloquear o acesso a esse recurso a partir da sua sub-rede, mas pode continuar a aceder a outras contas de Armazenamento do Azure, mesmo que não tenham Pontos Finais de Serviço ativados.  

![Pontos Finais de Serviço][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
