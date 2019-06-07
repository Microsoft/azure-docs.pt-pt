---
title: Considerações de redes com o ambiente de serviço de aplicações - Azure
description: Explica o tráfego de rede do ASE e como definir os NSGs e as UDRs com o seu ASE
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
ms.openlocfilehash: b29dec76fb6b1f9883c5c594d4719c9f3032089e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514631"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Considerações sobre o funcionamento em rede para um ambiente de serviço de aplicações #

## <a name="overview"></a>Descrição geral ##

 Azure [ambiente do serviço de aplicações] [ Intro] é uma implementação do serviço de aplicações do Azure numa sub-rede na rede virtual do Azure (VNet). Existem dois tipos de implementação para um ambiente de serviço de aplicações (ASE):

- **ASE externo**: Expõe as aplicações alojadas no ASE num endereço IP acessível pela internet. Para obter mais informações, consulte [criar um ASE externo][MakeExternalASE].
- **O ASE DE ILB**: Expõe as aplicações alojadas no ASE num endereço IP na sua VNet. O ponto final interno é um balanceador de carga interno (ILB), que é o motivo pelo qual ele é chamado um ASE de ILB. Para obter mais informações, consulte [criar e utilizar um ASE de ILB][MakeILBASE].

Todos os ASEs, externo e ILB, tem um VIP público, que é utilizado para o tráfego de entrada de gestão e como o endereço ao efetuar chamadas a partir do ASE à internet. As chamadas de um ASE que aceder à internet, deixe a VNet através do VIP atribuído para o ASE. O IP público deste VIP é o IP de origem para todas as chamadas a partir do ASE, aceda à internet. Se as aplicações no ASE fazem chamadas para recursos na sua VNet ou numa VPN, o IP de origem é um dos IPs na sub-rede utilizada pelo seu ASE. Uma vez que o ASE é dentro da VNet, também pode aceder aos recursos dentro da VNet sem qualquer configuração adicional. Se a VNet estiver ligada à sua rede no local, o aplicações no ASE também tem acesso a recursos existem sem configuração adicional.

![ASE externo][1] 

Se tiver um ASE externo, o VIP público também é o ponto final de que as suas aplicações de ASE resolver para:

* HTTP/S 
* FTP/S
* Implementação na Web
* Depuração remota

![ASE DE ILB][2]

Se tiver um ASE de ILB, o endereço do endereço ILB é o ponto de extremidade HTTP/S, FTP/S, implantação da web e depuração remota.

## <a name="ase-subnet-size"></a>Tamanho da sub-rede do ASE ##

Não é possível alterar o tamanho da sub-rede utilizada para alojar um ASE depois de implementar o ASE.  O ASE utiliza um endereço para cada função de infraestrutura, bem como para cada instância de plano de serviço de aplicações isolado.  Além disso, existem cinco endereços utilizados pelo sistema de rede do Azure para cada sub-rede que for criado.  Um ASE com não existem planos de serviço de aplicações em todos os usará 12 endereços antes de criar uma aplicação.  Se for um ASE de ILB, em seguida, ele usará 13 endereços antes de criar uma aplicação nesse ASE. Ao dimensionar o ASE, funções de infraestrutura são adicionadas a cada múltiplo de 15 e 20 das suas instâncias do plano de serviço de aplicações.

   > [!NOTE]
   > Nada mais é possível na sub-rede, mas o ASE. Certifique-se de que escolha um espaço de endereços que permite para o crescimento futuro. Não é possível alterar esta definição mais tarde. Recomendamos um tamanho de `/24` com 256 endereços.

Quando aumenta ou reduzir verticalmente, são adicionadas novas funções do tamanho apropriado e, em seguida, as cargas de trabalho são migradas do tamanho atual para o tamanho de destino. O original removidas as VMs apenas depois das cargas de trabalho foram migradas. Se tivesse um ASE com 100 instâncias ASP, haveria um período em que precisa duplicar o número de VMs.  É por essa razão que recomendamos a utilização de uma "/ 24" para acomodar quaisquer alterações que necessite.  

## <a name="ase-dependencies"></a>Dependências de ASE ##

### <a name="ase-inbound-dependencies"></a>Dependências de entrada do ASE ###

O ASE de entrada de acesso são dependências:

| Utilizar | De | Para |
|-----|------|----|
| Gestão | Endereços de gestão do serviço de aplicações | Sub-rede do ASE: 454, 455 |
|  Comunicação interna do ASE | Sub-rede do ASE: Todas as portas | Sub-rede do ASE: Todas as portas
|  Permitir que o Balanceador de carga do Azure de entrada | Balanceador de carga do Azure | Sub-rede do ASE: Todas as portas
|  Aplicação de endereços IP atribuída | Aplicação endereços atribuída | Sub-rede do ASE: Todas as portas

O tráfego de entrada de gestão fornece o comando e controlo do ASE, além de monitoramento do sistema. Os endereços de origem para este tráfego estão listados na [endereços de gestão do ASE] [ ASEManagement] documento. A configuração de segurança de rede tem de permitir o acesso de todos os IPs nas portas 454 e 455. Se bloquear o acesso a partir desses endereços, o seu ASE se tornará mau estado de funcionamento e, em seguida, torne-se suspensa.

Dentro da sub-rede do ASE, existem muitas portas utilizadas para comunicação de componente interno e pode alterar. Isto requer que todas as portas na sub-rede do ASE esteja acessível a partir da sub-rede do ASE. 

Para a comunicação entre o Balanceador de carga do Azure e a sub-rede do ASE o mínimo de portas que precisa de estar abertas é 454, 455 e 16001. A porta 16001 é utilizado para manter o tráfego ativo entre o Balanceador de carga e o ASE. Se estiver a utilizar um ASE de ILB, em seguida, pode bloquear o tráfego para baixo para apenas o 454, 455, 16001 portas.  Se estiver a utilizar um ASE externo, em seguida, terá de levar em conta as portas de acesso de aplicação normal.  Se estiver a utilizar endereços atribuída de aplicação, terá de abri-lo para todas as portas.  Quando lhe for atribuído um endereço de uma aplicação específica, em seguida, o Balanceador de carga utilizará as portas que não são conhecidas de com antecedência para enviar o tráfego HTTP e HTTPS para o ASE.

Se estiver a utilizar endereços IP atribuída de aplicação, terá de permitir o tráfego de IPs atribuídos às suas aplicações para a sub-rede do ASE.

O tráfego TCP que chega nas portas 454 e 455 deve voltar a partir do mesmo VIP ou terá um problema de encaminhamento assimétrico. 

### <a name="ase-outbound-dependencies"></a>Dependências de saída do ASE ###

Para acesso de saída, um ASE depende da existência de vários sistemas externos. Muitas dessas dependências do sistema são definidas com nomes DNS e não mapeiam para um conjunto fixo de endereços IP. Portanto, o ASE requer acesso de saída a partir da sub-rede do ASE para todos os IPs externos numa variedade de portas. 

O ASE comunica horizontalmente para endereços acessível da internet nas portas seguintes:

| Port | Utilizações |
|-----|------|
| 53 | DNS |
| 123 | NTP |
| 80/443 | CRL, atualizações do Windows, as dependências do Linux, serviços do Azure |
| 1433 | SQL do Azure | 
| 12000 | Monitorização |

A lista completa de dependências de saída são listados no documento que descreve [bloquear o tráfego de saída do ambiente de serviço de aplicações](./firewall-integration.md). Se o ASE perder o acesso às respetivas dependências, deixa de funcionar. Quando isso acontece o tempo suficiente, o ASE está suspensa. 

### <a name="customer-dns"></a>Cliente DNS ###

Se a VNet está configurada com um servidor DNS definida pelo cliente, as cargas de trabalho de inquilinos usá-lo. O ASE utiliza o DNS do Azure para fins de gestão. Se a VNet está configurada com um servidor DNS de cliente selecionado, o servidor DNS tem de ser acessível a partir da sub-rede que contém o ASE.

Para testar a resolução DNS da sua aplicação web, pode usar o comando de consola *nameresolver*. Ir para a janela de depuração no seu site scm da sua aplicação ou vá para a aplicação no portal e selecione consola. Prompt do shell, pode emitir o comando *nameresolver* junto com o nome DNS que pretende procurar. O resultado, que obtém é o mesmo que o que seu aplicativo obteria quando fizer a pesquisa do mesmo. Se utilizar o nslookup, fará uma pesquisa com o DNS do Azure em vez disso.

Se alterar a definição de DNS da VNet que está a seu ASE, terá de reiniciar o seu ASE. Para evitar o reinício seu ASE, é altamente recomendável que configure as definições de DNS para a sua VNet antes de criar o seu ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Dependências de portais ##

Além das dependências de funcionais do ASE, existem alguns itens adicionais relacionados com a experiência do portal. Alguns dos recursos no portal do Azure dependem do acesso direto aos _SCM site_. Para todas as aplicações no serviço de aplicações do Azure, existem dois URLs. É o primeiro URL aceder à sua aplicação. O URL do segundo é aceder ao site SCM, que também é denominado o _consola Kudu_. As funcionalidades que utilizam o site do SCM incluem:

-   Tarefas Web
-   Funções
-   Registo de transmissão em fluxo
-   Kudu
-   Extensões
-   Explorador de Processos
-   Consola

Quando utiliza um ASE de ILB, o site do SCM não está acessível a partir de fora da VNet. Alguns recursos não funcionará no portal de aplicação pois requerem acesso ao site do SCM de uma aplicação. Pode ligar ao site SCM diretamente em vez de utilizar o portal. 

Se o ASE de ILB é o nome de domínio *contoso.appserviceenvironnment.net* e o nome de aplicação é *testapp*, a aplicação está a ser contatada *testapp.contoso.appserviceenvironment.net*. O site do SCM que vem com ele é contatado *testapp.scm.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>Endereços IP de ASE ##

Um ASE tem alguns endereços IP estar atento. São:

- **Endereço IP de entrada público**: Utilizado para tráfego de aplicações num ASE externo e o tráfego de gestão no ASE externo e um ASE de ILB.
- **IP público saído**: Utilizado como o IP de "de" para as ligações de saída a partir do ASE que deixam a VNet, que não são encaminhadas para baixo de uma VPN.
- **Endereço IP do ILB**: O endereço IP do ILB só existe num ASE de ILB.
- **Endereços SSL baseado em IP aplicação atribuída**: Apenas possível com um ASE externo e quando o SSL baseado em IP estiver configurado.

Todos estes endereços IP são visíveis no portal do Azure a partir da interface do Usuário do ASE. Se tiver um ASE de ILB, o IP para o ILB está listado.

   > [!NOTE]
   > Estes endereços IP não serão alterado desde que o ASE permanece em execução.  Se o seu ASE torna-se suspenso e restaurado, irão alterar os endereços utilizados pelo seu ASE. A causa normal para um ASE tornam-se suspenso é se bloquear o acesso de gestão de entrada ou bloquear o acesso a uma dependência do ASE. 

![Endereços IP][3]

### <a name="app-assigned-ip-addresses"></a>Aplicação atribuída endereços IP ###

Com um ASE externo, pode atribuir endereços IP para aplicações individuais. Não é possível fazê-lo com um ASE de ILB. Para obter mais informações sobre como configurar a sua aplicação tenha seu próprio endereço IP, consulte [vincular um certificado SSL personalizado já existente para o serviço de aplicações do Azure](../app-service-web-tutorial-custom-ssl.md).

Quando uma aplicação tem seu próprio endereço SSL baseado em IP, o ASE reserva-se duas portas para mapear para esse endereço IP. É de uma porta para o tráfego HTTP e a outra porta é para HTTPS. Essas portas são listadas na IU do ASE, na secção de endereços IP. Tráfego deve ser capaz de alcançar essas portas do VIP ou as aplicações estão inacessíveis. Este requisito é importante lembrar-se ao configurar grupos de segurança de rede (NSGs).

## <a name="network-security-groups"></a>Grupos de Segurança de Rede ##

[Grupos de segurança de rede] [ NSGs] oferecem a capacidade de controlar o acesso de rede dentro de uma VNet. Ao utilizar o portal, existe uma regra de Negação implícita com a prioridade mais baixa para recusar tudo. O que criar são suas regras de permissão.

Num ASE, não tem acesso às VMs utilizadas para alojar o ASE em si. Eles estão numa subscrição gerida pela Microsoft. Se quiser restringir o acesso às aplicações no ASE, defina NSGs na sub-rede do ASE. Ao fazer isso, preste muita atenção para as dependências do ASE. Se bloquear todas as dependências, o ASE deixa de funcionar.

Os NSGs podem ser configurados através do portal do Azure ou através do PowerShell. As informações aqui mostram o portal do Azure. Criar e gerir NSGs no portal como um recurso de nível superior sob **redes**.

Se as entradas necessárias num NSG, para um ASE para a função permitir o tráfego:

**De entrada**
* o IP inicial etiqueta AppServiceManagement nas portas 454,455 de serviço
* do Balanceador de carga na porta 16001
* da sub-rede do ASE, à sub-rede do ASE em todas as portas

**Outbound**
* para todos os IPs na porta 123
* para todos os IPs nas portas 80, 443
* para o IP etiqueta AzureSQL nas portas 1433 de serviço
* para todos os IPs na porta 12000
* para a sub-rede do ASE em todas as portas

A porta DNS não tem de ser adicionado como o tráfego para o DNS não é afetado pelas regras do NSG. Estas portas não incluem as portas que as suas aplicações requerem para utilização com êxito. As portas de acesso de aplicação normal são:

| Utilizar | De | Para |
|----------|---------|-------------|
|  HTTP/HTTPS  | Utilizador configurável |  80, 443 |
|  FTP/FTPS    | Utilizador configurável |  21, 990, 10001-10020 |
|  Visual Studio depuração remota  |  Utilizador configurável |  4020, 4022, 4024 |
|  Implementar serviço Web | Utilizador configurável | 8172 |

Quando os requisitos de entrada e de saída são levados em conta, os NSGs devem ter um aspeto semelhantes para os NSGs mostrados neste exemplo. 

![Regras de segurança de entrada][4]

Uma regra predefinida permite que os IPs na VNet para comunicar com a sub-rede do ASE. Outra regra padrão permite que o Balanceador de carga, também conhecido como o VIP público, para comunicar com o ASE. Para ver as regras predefinidas, selecione **regras predefinidas** junto a **Add** ícone. Se colocar uma negação de todo o resto da regra antes das regras predefinidas, impedir que o tráfego entre o VIP e o ASE. Para impedir que o tráfego que vem de dentro da VNet, adicione a sua própria regra para permitir a entrada. Use uma fonte igual a AzureLoadBalancer com um destino **qualquer** e um intervalo de portas de **\*** . Uma vez que a regra NSG é aplicada à sub-rede do ASE, não precisa de ser específico no destino.

Se um endereço IP atribuído à sua aplicação, certifique-se de que manter as portas abertas. Para ver as portas, selecione **ambiente do serviço de aplicações** > **endereços IP**.  

Todos os itens que mostra as seguintes regras de saída são necessários, exceto o último item. Elas permitem o acesso à rede para as dependências de ASE registados anteriormente no artigo. Se bloquear qualquer um deles, o ASE deixa de funcionar. O último item na lista permite que o ASE para comunicar com outros recursos na sua VNet.

![Regras de segurança de saída][5]

Depois de serem definidos seus NSGs, atribuí-las para a sub-rede que o ASE está ativada. Se não sabe o ASE VNet ou sub-rede, pode ver na página do portal do ASE. Para atribuir o NSG à sua sub-rede, vá para a sub-rede da interface do Usuário e selecione o NSG.

## <a name="routes"></a>Rotas ##

O túnel forçado é quando define rotas na sua VNet, para que o tráfego de saída não vai diretamente para a internet, mas lugar, como um gateway do ExpressRoute ou uma aplicação virtual.  Se precisar de configurar o ASE de uma forma, em seguida, continue a ler o documento [configurar o ambiente de serviço de aplicações com túnel forçado][forcedtunnel].  Este documento informa as opções disponíveis para trabalhar com o ExpressRoute e o protocolo de túnel forçado.

Quando criar um ASE no portal do também criamos um conjunto de tabelas de rotas na sub-rede que é criada com o ASE.  Basta dizem as rotas enviar tráfego de saída diretamente à internet.  
Para criar as rotas mesmo manualmente, siga estes passos:

1. Aceda ao portal do Azure. Selecione **Networking** > **tabelas de rotas**.

2. Crie uma nova tabela de rotas na mesma região que a sua VNet.

3. Na sua tabela de rota da interface do Usuário, selecione **rotas** > **Add**.

4. Definir o **tipo de próximo salto** ao **Internet** e o **prefixo de endereço** para **0.0.0.0/0**. Selecione **Guardar**.

    Verá algo semelhante ao seguinte:

    ![Rotas funcionais][6]

5. Depois de criar a nova tabela de rota, vá para a sub-rede que contém o seu ASE. Selecione a tabela de rotas na lista no portal. Depois de guardar a alteração, em seguida, deverá ver os NSGs e as rotas indicadas com sua sub-rede.

    ![Rotas e NSGs][7]

## <a name="service-endpoints"></a>Pontos Finais de Serviço ##

Os Pontos Finais de Serviço permitem restringir o acesso aos serviços multi-inquilino a um conjunto de sub-redes e redes virtuais do Azure. Pode ler mais sobre Pontos Finais de Serviço na documentação [Pontos Finais de Serviço de Rede Virtual][serviceendpoints]. 

Quando ativar Pontos Finais de Serviço num recurso, são criadas rotas com uma prioridade mais alta do que todas as outras rotas. Se utilizar pontos finais de serviço em qualquer serviço do Azure, com um ASE com túnel forçado, o tráfego para esses serviços não será forçado encapsulada. 

Quando estão ativados Pontos Finais de Serviço numa sub-rede com uma instância do SQL do Azure, todas as instâncias do SQL do Azure ligadas a partir dessa sub-rede precisam de ter Pontos Finais de Serviço ativados. Se quiser aceder a várias instâncias de SQL do Azure a partir da mesma sub-rede, não pode ativar Pontos Finais de Serviço numa instância do SQL do Azure e não na outra. Nenhum outro serviço do Azure se comporta como o SQL Azure em relação aos pontos finais de serviço. Quando ativar Pontos Finais de Serviço com o Armazenamento do Azure, pode bloquear o acesso a esse recurso a partir da sua sub-rede, mas pode continuar a aceder a outras contas de Armazenamento do Azure, mesmo que não tenham Pontos Finais de Serviço ativados.  

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
