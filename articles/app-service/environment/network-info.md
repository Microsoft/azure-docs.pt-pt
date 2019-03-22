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
ms.date: 03/14/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 73175b326c25d5d9a78155d0d9d888b655da1bfd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124138"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Considerações sobre o funcionamento em rede para um ambiente de serviço de aplicações #

## <a name="overview"></a>Descrição geral ##

 Azure [ambiente do serviço de aplicações] [ Intro] é uma implementação do serviço de aplicações do Azure numa sub-rede na rede virtual do Azure (VNet). Existem dois tipos de implementação para um ambiente de serviço de aplicações (ASE):

- **ASE externo**: Expõe as aplicações alojadas no ASE num endereço IP acessível pela internet. Para obter mais informações, consulte [criar um ASE externo][MakeExternalASE].
- **O ASE DE ILB**: Expõe as aplicações alojadas no ASE num endereço IP na sua VNet. O ponto final interno é um balanceador de carga interno (ILB), que é o motivo pelo qual ele é chamado um ASE de ILB. Para obter mais informações, consulte [criar e utilizar um ASE de ILB][MakeILBASE].

Existem duas versões do ambiente de serviço de aplicações: ASEv1 e ASEv2. Para obter informações sobre o ASEv1, veja [introdução ao ambiente de serviço de aplicações v1][ASEv1Intro]. ASEv1 pode ser implementado num clássico ou VNet do Resource Manager. ASEv2 só pode ser implementada numa VNet do Resource Manager.

Todas as chamadas de um ASE que aceder à internet, deixe a VNet através de um VIP atribuído para o ASE. O IP público deste VIP é o IP de origem para todas as chamadas a partir do ASE, aceda à internet. Se as aplicações no ASE fazem chamadas para recursos na sua VNet ou numa VPN, o IP de origem é um dos IPs na sub-rede utilizada pelo seu ASE. Uma vez que o ASE é dentro da VNet, também pode aceder aos recursos dentro da VNet sem qualquer configuração adicional. Se a VNet estiver ligada à sua rede no local, o aplicações no ASE também tem acesso a recursos existem sem configuração adicional.

![ASE externo][1] 

Se tiver um ASE externo, o VIP público também é o ponto final de que as suas aplicações de ASE resolver para:

* HTTP/S. 
* FTP/S. 
* Implementação na Web.
* Depuração remota.

![ASE DE ILB][2]

Se tiver um ASE de ILB, o endereço do ILB é o ponto de extremidade HTTP/S, FTP/S, implantação da web e depuração remota.

As portas de acesso de aplicação normal são:

| Utilizar | A partir de | Para |
|----------|---------|-------------|
|  HTTP/HTTPS  | Utilizador configurável |  80, 443 |
|  FTP/FTPS    | Utilizador configurável |  21, 990, 10001-10020 |
|  Visual Studio depuração remota  |  Utilizador configurável |  4020, 4022, 4024 |
|  Implementar serviço Web | Utilizador configurável | 8172 |

Isso é verdade se estiver num ASE externo ou num ASE de ILB. Se estiver num ASE externo, atingir essas portas no VIP público. Se estiver num ASE de ILB, atingir essas portas no ILB. Se bloquear a porta 443, pode haver um efeito sobre algumas funcionalidades expostas no portal. Para obter mais informações, consulte [dependências de Portal](#portaldep).

## <a name="ase-subnet-size"></a>Tamanho da sub-rede do ASE ##

O tamanho da sub-rede utilizada para alojar um ASE não pode ser alterado depois de o ASE ser implementado.  O ASE utiliza um endereço para cada função de infraestrutura, bem como para cada instância de plano de serviço de aplicações isolado.  Além disso, existem 5 endereços utilizados pelo sistema de rede do Azure para cada sub-rede que for criado.  Um ASE com não existem planos de serviço de aplicações em todos os usará 12 endereços antes de criar uma aplicação.  Se for um ASE de ILB, em seguida, ele usará 13 endereços antes de criar uma aplicação nesse ASE. Ao dimensionar o ASE, funções de infraestrutura são adicionadas a cada múltiplo de 15 e 20 das suas instâncias do plano de serviço de aplicações.

   > [!NOTE]
   > Nada mais é possível na sub-rede, mas o ASE. Certifique-se de que escolha um espaço de endereços que permite para o crescimento futuro. Não é possível alterar esta definição mais tarde. Recomendamos um tamanho de `/24` com 256 endereços.

Quando aumenta ou reduzir verticalmente, são adicionadas novas funções do tamanho apropriado e, em seguida, as cargas de trabalho são migradas do tamanho atual para o tamanho de destino. Apenas depois das suas aplicações são migradas são as VMs originais removidas. Isso significa que se tivesse um ASE com 100 instâncias ASP teria um período em que precisa duplicar o número de VMs.  É por essa razão que recomendamos a utilização de uma "/ 24" para acomodar quaisquer alterações que necessite.  

## <a name="ase-dependencies"></a>Dependências de ASE ##

### <a name="ase-inbound-dependencies"></a>Dependências de entrada do ASE ###

O ASE de entrada de acesso são dependências:

| Utilizar | A partir de | Para |
|-----|------|----|
| Gestão | Endereços de gestão do serviço de aplicações | Sub-rede do ASE: 454, 455 |
|  Comunicação interna do ASE | Sub-rede do ASE: Todas as portas | Sub-rede do ASE: Todas as portas
|  Permitir que o Balanceador de carga do Azure de entrada | Balanceador de carga do Azure | Sub-rede do ASE: Todas as portas
|  Aplicação de endereços IP atribuída | Aplicação endereços atribuída | Sub-rede do ASE: Todas as portas

O tráfego de entrada de gestão fornece o comando e controlo do ASE, além de monitoramento do sistema. Os endereços de origem para este tráfego estão listados na [endereços de gestão do ASE] [ ASEManagement] documento. A configuração de segurança de rede tem de permitir o acesso de todos os IPs nas portas 454 e 455. Se bloquear o acesso a partir desses endereços, o seu ASE se tornará mau estado de funcionamento e, em seguida, torne-se suspensa.

Na sub-rede do ASE, existem muitas portas utilizadas para o componente interno podem alterar a comunicação e a eles.  Isto requer que todas as portas na sub-rede do ASE esteja acessível a partir da sub-rede do ASE. 

Para a comunicação entre o Balanceador de carga do Azure e a sub-rede do ASE o mínimo de portas que precisa de estar abertas é 454, 455 e 16001. A porta 16001 é utilizado para manter o tráfego ativo entre o Balanceador de carga e o ASE. Se estiver a utilizar um ASE de ILB, em seguida, pode bloquear o tráfego para baixo para apenas o 454, 455, 16001 portas.  Se estiver a utilizar um ASE externo, em seguida, precisa levar em conta as portas de acesso de aplicação normal.  Se estiver a utilizar endereços de aplicações atribuído terá de abri-lo para todas as portas.  Quando lhe for atribuído um endereço de uma aplicação específica, em seguida, o Balanceador de carga utilizará as portas que não são conhecidas de com antecedência para enviar o tráfego HTTP e HTTPS para o ASE.

Se estiver a utilizar aplicações atribuídas os endereços IP que tem de permitir o tráfego de IPs atribuídos às suas aplicações para a sub-rede do ASE.

O tráfego TCP que chega nas portas 454 e 455 deve voltar a partir do mesmo VIP ou terá um problema de encaminhamento assimétrico. 

### <a name="ase-outbound-dependencies"></a>Dependências de saída do ASE ###

Para acesso de saída, um ASE depende da existência de vários sistemas externos. Muitas dessas dependências do sistema são definidas com nomes DNS e não mapeiam para um conjunto fixo de endereços IP. Portanto, o ASE requer acesso de saída a partir da sub-rede do ASE para todos os IPs externos numa variedade de portas. 

A lista completa de dependências de saída são listados no documento que descreve [bloquear o tráfego de saída do ambiente de serviço de aplicações](./firewall-integration.md). Se o ASE perder o acesso às respetivas dependências, deixa de funcionar. Quando isso acontece o tempo suficiente, o ASE está suspensa. 

### <a name="customer-dns"></a>Cliente DNS ###

Se a VNet está configurada com um servidor DNS definida pelo cliente, as cargas de trabalho de inquilinos usá-lo. O ASE ainda precisa para comunicar com o DNS do Azure para fins de gestão. 

Se a VNet está configurada com um cliente DNS no outro lado de uma VPN, o servidor DNS tem de ser acessível a partir da sub-rede que contém o ASE.

Para testar a resolução da sua aplicação web pode utilizar o comando de consola *nameresolver*. Ir para a janela de depuração no seu site scm da sua aplicação ou vá para a aplicação no portal e selecione consola. Prompt do shell, pode emitir o comando *nameresolver* juntamente com o endereço que pretende procurar. O resultado, que obtém é o mesmo que o que seu aplicativo obteria quando fizer a pesquisa do mesmo. Se utilizar o nslookup fizer uma pesquisa com o DNS do Azure em vez disso.

Se alterar a definição de DNS da VNet que está a seu ASE, terá de reiniciar o seu ASE. Para evitar o reinício seu ASE, é altamente recomendável que configure as definições de DNS para a sua VNet antes de criar o seu ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Dependências de portais ##

Além das dependências de funcionais do ASE, existem alguns itens adicionais relacionados com a experiência do portal. Alguns dos recursos no portal do Azure dependem do acesso direto aos _SCM site_. Para todas as aplicações no serviço de aplicações do Azure, existem dois URLs. É o primeiro URL aceder à sua aplicação. O URL do segundo é aceder ao site SCM, que também é denominado o _consola Kudu_. As funcionalidades que utilizam o site do SCM incluem:

-   Tarefas Web
-   Funções
-   Transmissão em fluxo de registo
-   Kudu
-   Extensões
-   Explorador de Processos
-   Consola

Quando utiliza um ASE de ILB, o site do SCM não está acessível a partir de fora da VNet de internet. Quando a aplicação estiver alojada num ASE de ILB, alguns recursos não funcionarão no portal.  

Vários desses recursos que dependem do site do SCM também estão disponíveis diretamente na consola Kudu. Pode ligar à mesma diretamente, em vez de através do portal. Se a aplicação estiver alojada num ASE de ILB, utilize as suas credenciais de publicação para iniciar sessão. O URL para aceder ao site SCM de uma aplicação alojada num ASE de ILB tem o seguinte formato: 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

Se o ASE de ILB é o nome de domínio *contoso.net* e o nome de aplicação é *testapp*, a aplicação está a ser contatada *testapp.contoso.net*. O site do SCM que vem com ele é contatado *testapp.scm.contoso.net*.

## <a name="functions-and-web-jobs"></a>As funções e webjobs ##

As funções e Web tarefas dependem do site do SCM, mas são suportadas para utilização no portal, mesmo que as aplicações estão num ASE de ILB, desde que o browser pode contactar o site do SCM.  Se estiver a utilizar um certificado autoassinado com o ASE de ILB, terá de ativar o seu browser para confiar no certificado.  Para o IE e o Microsoft Edge, isso significa que o certificado tem de estar no arquivo de confiança do computador.  Se estiver a utilizar Chrome, em seguida, o que significa que aceite o certificado no browser anteriormente pelo supostamente diretamente a atingir o site do scm.  A melhor solução é usar um certificado comercial que esteja na cadeia de navegador de confiança.  

## <a name="ase-ip-addresses"></a>Endereços IP de ASE ##

Um ASE tem alguns endereços IP estar atento. São:

- **Endereço IP de entrada público**: Utilizado para tráfego de aplicações num ASE externo e o tráfego de gestão no ASE externo e um ASE de ILB.
- **IP público saído**: Utilizado como o IP de "de" para as ligações de saída a partir do ASE que deixam a VNet, que não são encaminhadas para baixo de uma VPN.
- **Endereço IP do ILB**: Se usar um ASE de ILB.
- **Endereços SSL baseado em IP aplicação atribuída**: Apenas possível com um ASE externo e quando o SSL baseado em IP estiver configurado.

Todos os, estes endereços IP são facilmente visíveis num ASEv2 no portal do Azure a partir da interface do Usuário do ASE. Se tiver um ASE de ILB, o IP para o ILB está listado.

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

Quando os requisitos de entrada e de saída são levados em conta, os NSGs devem ter um aspeto semelhantes para os NSGs mostrados neste exemplo. É o intervalo de endereços da VNet _192.168.250.0/23_, e a sub-rede que o ASE está no é _192.168.251.128/25_.

Os dois primeiros requisitos de entrada para o ASE para a função são apresentados na parte superior da lista neste exemplo. Eles ativar a gestão do ASE e permitem que o ASE se comunique consigo mesma. As outras entradas são todos os inquilinos configurável e podem regem o acesso à rede para as aplicações alojadas no ASE. 

![Regras de segurança de entrada][4]

Uma regra predefinida permite que os IPs na VNet para comunicar com a sub-rede do ASE. Outra regra padrão permite que o Balanceador de carga, também conhecido como o VIP público, para comunicar com o ASE. Para ver as regras predefinidas, selecione **regras predefinidas** junto a **Add** ícone. Se colocar uma negação de todo o resto da regra depois do NSG de regras apresentadas, impedir que o tráfego entre o VIP e o ASE. Para impedir que o tráfego que vem de dentro da VNet, adicione a sua própria regra para permitir a entrada. Use uma fonte igual a AzureLoadBalancer com um destino **qualquer** e um intervalo de portas de **\***. Uma vez que a regra NSG é aplicada à sub-rede do ASE, não precisa de ser específico no destino.

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

Quando ativar Pontos Finais de Serviço num recurso, são criadas rotas com uma prioridade mais alta do que todas as outras rotas. Se utilizar Pontos Finais de Serviço com um ASE com túnel forçado, o tráfego de gestão do SQL do Azure e do Armazenamento do Azure não fica com um túnel forçado. 

Quando estão ativados Pontos Finais de Serviço numa sub-rede com uma instância do SQL do Azure, todas as instâncias do SQL do Azure ligadas a partir dessa sub-rede precisam de ter Pontos Finais de Serviço ativados. Se quiser aceder a várias instâncias de SQL do Azure a partir da mesma sub-rede, não pode ativar Pontos Finais de Serviço numa instância do SQL do Azure e não na outra. O Armazenamento do Azure não tem um comportamento semelhante ao SQL do Azure. Quando ativar Pontos Finais de Serviço com o Armazenamento do Azure, pode bloquear o acesso a esse recurso a partir da sua sub-rede, mas pode continuar a aceder a outras contas de Armazenamento do Azure, mesmo que não tenham Pontos Finais de Serviço ativados.  

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
