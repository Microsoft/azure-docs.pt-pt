---
title: Considerações de redes
description: Conheça o tráfego da rede ASE e como definir grupos de segurança de rede e rotas definidas pelo utilizador com o seu ASE.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 07/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0dfcf74ef07ff2bde7921860c6e13a59b0ccf023
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962541"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Considerações sobre a rede para um Ambiente de Serviço de Aplicações #

## <a name="overview"></a>Descrição geral ##

 Azure [App Service Environment][Intro] é uma implementação do Azure App Service numa sub-rede na sua rede virtual Azure (VNet). Existem dois tipos de implementação para um ambiente de Serviço de Aplicações (ASE):

- **ASE Externo**: Expõe as aplicações hospedadas pela ASE num endereço IP acessível à Internet. Para obter mais informações, consulte [Criar um ASE Externo.][MakeExternalASE]
- **ILB ASE**: Expõe as aplicações hospedadas pela ASE num endereço IP dentro do seu VNet. O ponto final interno é um equilibrador de carga interno (ILB), razão pela qual é chamado de ILB ASE. Para obter mais informações, consulte [Criar e utilizar um ILB ASE][MakeILBASE].

Todas as ASEs, Externas e ILB, têm um VIP público que é utilizado para o tráfego de gestão de entrada e como o endereço ao fazer chamadas da ASE para a internet. As chamadas de um ASE que vai para a internet deixam o VNet através do VIP atribuído para o ASE. O IP público deste VIP é a fonte IP para todas as chamadas do ASE que vão para a internet. Se as aplicações no seu ASE fizerem chamadas para recursos no seu VNet ou através de uma VPN, o IP de origem é um dos IPs na sub-rede utilizada pelo seu ASE. Como o ASE está dentro do VNet, também pode aceder a recursos dentro do VNet sem qualquer configuração adicional. Se o VNet estiver ligado à sua rede no local, as aplicações no seu ASE também têm acesso a recursos lá sem configuração adicional.

![ASE externo][1] 

Se tiver um ASE Externo, o VIP público é também o ponto final a que as suas aplicações ASE resolvem:

* HTTP/S 
* FTP/S
* Implementação web
* Depuração remota

![ILB ASE][2]

Se tiver um ILB ASE, o endereço do endereço ILB é o ponto final para HTTP/S, FTP/S, implementação web e depuração remota.

## <a name="ase-subnet-size"></a>Tamanho da sub-rede ASE ##

O tamanho da sub-rede utilizada para hospedar um ASE não pode ser alterado após a implantação do ASE.  O ASE utiliza um endereço para cada função de infraestrutura, bem como para cada instância do plano de aplicações isolada.  Além disso, existem cinco endereços utilizados pela Azure Networking para cada sub-rede que é criada.  Um ASE sem planos de Serviço de Aplicações utilizará 12 endereços antes de criar uma aplicação.  Se for um ILB ASE, então utilizará 13 endereços antes de criar uma aplicação nesse ASE. À medida que escala o seu ASE, as funções de infraestrutura são adicionadas a cada múltiplo de 15 e 20 instâncias do seu plano de App Service.

   > [!NOTE]
   > Nada mais pode estar na sub-rede a não ser o ASE. Não se esqueça de escolher um espaço de endereço que permita o crescimento futuro. Não pode mudar esta definição mais tarde. Recomendamos um tamanho `/24` de 256 endereços.

Quando escala ou desce, novas funções do tamanho apropriado são adicionadas e, em seguida, as suas cargas de trabalho são migradas do tamanho atual para o tamanho do alvo. Os VM originais só foram removidos após a migração das cargas de trabalho. Se tivesse um ASE com 100 instâncias ASP, haveria um período em que precisaria do dobro do número de VMs.  É por esta razão que recomendamos a utilização de um '/24' para acomodar quaisquer alterações que possa necessitar.  

## <a name="ase-dependencies"></a>Dependências do ASE ##

### <a name="ase-inbound-dependencies"></a>Dependências de entrada da ASE ###

Apenas para que a ASE funcione, a ASE exige que as seguintes portas estejam abertas:

| Utilização | De | Para |
|-----|------|----|
| Gestão | Endereços de gestão do Serviço de Aplicações | Sub-rede ASE: 454.455 |
|  Comunicação interna ase | Sub-rede ASE: Todas as portas | Sub-rede ASE: Todas as portas
|  Permitir a entrada do equilibrador de carga Azure | Balanceador de carga do Azure | Sub-rede ASE: 16001

Existem outras 2 portas que podem ser abertas numa varredura portuária, 7654 e 1221. Respondem com um endereço IP e nada mais. Podem ser bloqueados se desejarem. 

O tráfego de gestão de entrada fornece comando e controlo da ASE para além da monitorização do sistema. Os endereços de origem para este tráfego estão listados no documento [de endereços da ASE Management.][ASEManagement] A configuração de segurança da rede tem de permitir o acesso a partir dos endereços de gestão ASE nas portas 454 e 455. Se bloquear o acesso a partir desses endereços, o seu ASE ficará insalubre e ficará suspenso. O tráfego TCP que entra nos portos 454 e 455 deve voltar do mesmo VIP ou terá um problema de encaminhamento assimétrico. 

Dentro da sub-rede ASE, existem muitas portas utilizadas para a comunicação interna de componentes e podem mudar. Isto exige que todas as portas da sub-rede ASE estejam acessíveis a partir da sub-rede ASE. 

Para a comunicação entre o equilibrador de carga Azure e a sub-rede ASE, as portas mínimas que precisam de estar abertas são 454, 455 e 16001. O porto de 16001 é utilizado para manter o tráfego vivo entre o equilibrador de carga e o ASE. Se estiver a utilizar um ILB ASE, então pode bloquear o tráfego apenas nas portas 454, 455, 16001.  Se estiver a utilizar um ASE Externo, terá de ter em conta as portas normais de acesso à aplicação.  

As outras portas com as qual precisa de se preocupar são as portas de aplicação:

| Utilização | Portas |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Depuragem remota do Estúdio Visual  |  4020, 4022, 4024 |
|  Serviço de implementação web | 8172 |

Se bloquear as portas de aplicação, o SEU AINDA pode funcionar, mas a sua aplicação pode não funcionar.  Se estiver a utilizar endereços IP atribuídos a uma ASE Externa, terá de permitir o tráfego dos IPs atribuídos às suas apps para a sub-rede ASE nas portas mostradas no portal ASE > página de Endereços IP.

### <a name="ase-outbound-dependencies"></a>Dependências de saída da ASE ###

Para o acesso de saída, um ASE depende de vários sistemas externos. Muitas dessas dependências do sistema são definidas com nomes DNS e não mapeiam para um conjunto fixo de endereços IP. Assim, o ASE requer acesso de saída da sub-rede ASE a todos os IPs externos através de uma variedade de portos. 

A ASE comunica para endereços acessíveis à Internet nas seguintes portas:

| Utiliza | Portas |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| CRL, atualizações do Windows, dependências do Linux, serviços Azure | 80/443 |
| SQL do Azure | 1433 | 
| Monitorização | 12000 |

As dependências de saída estão listadas no documento que descreve [o bloqueio do tráfego de saída do App Service Environment](./firewall-integration.md). Se a ASE perder acesso às suas dependências, deixa de funcionar. Quando isso acontece o suficiente, o ASE é suspenso. 

### <a name="customer-dns"></a>DNS do cliente ###

Se o VNet estiver configurado com um servidor DNS definido pelo cliente, as cargas de trabalho do inquilino usam-no. A ASE utiliza o Azure DNS para fins de gestão. Se o VNet estiver configurado com um servidor DNS selecionado pelo cliente, o servidor DNS deve ser acessível a partir da sub-rede que contém o ASE.

Para testar a resolução DNS a partir da sua aplicação web, pode utilizar o nome do *comando da consola.* Vá à janela de depurar no seu site scm para a sua aplicação ou vá à aplicação no portal e selecione consola. A partir do pedido de concha, pode emitir o *nome de* comando, juntamente com o nome DNS que pretende procurar. O resultado que obtém é o mesmo que a sua app obteria enquanto fazia a mesma procura. Se utilizar o nslookup, irá fazer uma procura usando o Azure DNS.

Se alterar a definição de DNS do VNet em que o seu ASE está, terá de reiniciar o seu ASE. Para evitar reiniciar o seu ASE, é altamente recomendável configurar as definições de DNS para o seu VNet antes de criar o seu ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Dependências do portal ##

Além das dependências funcionais da ASE, existem alguns itens extra relacionados com a experiência do portal. Algumas das capacidades do portal Azure dependem do acesso direto ao _site SCM_. Para cada aplicação no Azure App Service, existem dois URLs. O primeiro URL é aceder à sua aplicação. O segundo URL é aceder ao site SCM, que também é chamado de _consola Kudu._ As funcionalidades que utilizam o site SCM incluem:

-   WebJobs
-   Funções
-   Streaming de registos
-   Kudu
-   Extensões
-   Explorador de Processos
-   Consola

Quando se utiliza um ILB ASE, o site SCM não está acessível a partir do exterior do VNet. Algumas capacidades não funcionarão a partir do portal da aplicação porque necessitam de acesso ao site SCM de uma aplicação. Pode ligar-se diretamente ao site SCM em vez de utilizar o portal. 

Se o seu ILB ASE for o nome de domínio *contoso.appserviceenvironment.net* e o nome da sua aplicação for *testapp,* a aplicação é alcançada em *testapp.contoso.appserviceenvironment.net*. O sítio SCM que o acompanha é alcançado em *testapp.scm.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>Endereços IP ASE ##

Um ASE tem alguns endereços IP para estar ciente. A saber:

- **Endereço IP de entrada pública**: Utilizado para tráfego de aplicações num ASE Externo, e tráfego de gestão tanto num ASE Externo como num ILB ASE.
- **OUTBOUND public IP**: Usado como o IP "de" para ligações de saída do ASE que saem do VNet, que não são encaminhados para baixo de uma VPN.
- **Endereço IP ILB**: O endereço IP ILB só existe num ASE ILB.
- **Endereços SSL baseados em aplicativos:** Só é possível com uma ASE Externa e quando o SSL baseado em IP estiver configurado.

Todos estes endereços IP estão visíveis no portal Azure a partir da UI ASE. Se tiver um ILB ASE, o IP para o ILB está listado.

   > [!NOTE]
   > Estes endereços IP não serão alterados enquanto o seu ASE se mantiver a funcionar.  Se o seu ASE ficar suspenso e restaurado, os endereços utilizados pelo seu ASE mudarão. A causa normal para a suspensão de um ASE é se bloquear o acesso à gestão de entrada ou bloquear o acesso a uma dependência ASE. 

![Endereços IP][3]

### <a name="app-assigned-ip-addresses"></a>Endereços IP atribuídos a aplicativos ###

Com um ASE Externo, pode atribuir endereços IP a aplicações individuais. Não podes fazer isso com um ILB ASE. Para obter mais informações sobre como configurar a sua aplicação para ter o seu próprio endereço IP, consulte [Secure um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service](../configure-ssl-bindings.md).

Quando uma aplicação tem o seu próprio endereço SSL baseado em IP, o ASE reserva duas portas para mapear para esse endereço IP. Uma porta é para tráfego HTTP, e a outra porta é para HTTPS. Essas portas estão listadas na UI ASE na secção endereços IP. O tráfego deve ser capaz de chegar a esses portos a partir do VIP ou as aplicações são inacessíveis. Este requisito é importante para se lembrar quando configurar grupos de segurança de rede (NSGs).

## <a name="network-security-groups"></a>Grupos de Segurança de Rede ##

[Os Grupos de Segurança da Rede][NSGs] fornecem a capacidade de controlar o acesso à rede dentro de um VNet. Quando se usa o portal, há uma regra implícita de negação na menor prioridade de negar tudo. O que constrói são as suas regras de permitir.

Numa ASE, não tem acesso aos VMs usados para hospedar o próprio ASE. Estão numa subscrição gerida pela Microsoft. Se pretender restringir o acesso às aplicações no ASE, deslove os NSGs na sub-rede ASE. Ao fazê-lo, preste atenção às dependências da ASE. Se bloqueares alguma dependência, o ASE deixa de funcionar.

Os NSGs podem ser configurados através do portal Azure ou através do PowerShell. A informação aqui mostra o portal Azure. Cria e gere os NSGs no portal como um recurso de alto nível em **rede**.

As entradas necessárias num NSG, para que um ASE funcione, são para permitir o tráfego:

**Entrada**
* TCP da tag de serviço IP AppServiceManagement nas portas 454.455
* TCP do balançador de carga na porta 16001
* da sub-rede ASE para a sub-rede ASE em todas as portas

**Saída**
* UDP a todos os IPs no porto 53
* UDP a todos os IPs no porto 123
* TCP para todos os IPs nos portos 80.443
* TCP para a marca de serviço IP AzureSQL nas portas 1433
* TCP para todos os IPs no porto 12000
* para a sub-rede ASE em todas as portas

Estas portas não incluem as portas que as suas aplicações necessitam para uma utilização bem sucedida. Como exemplo, a sua aplicação poderá ter de chamar um servidor MySQL na porta 3306. O Protocolo de Tempo de Rede (NTP) na porta 123 é o protocolo de sincronização de tempo utilizado pelo sistema operativo. Os pontos finais ntP não são específicos dos Serviços de Aplicação, podem variar com o sistema operativo, e não estão numa lista bem definida de endereços. Para evitar problemas de sincronização de tempo, é necessário permitir o tráfego da UDP a todos os endereços no porto 123. O tráfego TCP de saída para o porto 12000 destina-se ao apoio e análise do sistema. Os pontos finais são dinâmicos e não estão num conjunto bem definido de endereços.

As portas normais de acesso a aplicações são:

| Utilização | Portas |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Depuragem remota do Estúdio Visual  |  4020, 4022, 4024 |
|  Serviço de implementação web | 8172 |

Quando os requisitos de entrada e saída forem tomados em consideração, os NSGs devem ser semelhantes aos NSGs apresentados neste exemplo. 

![Regras de segurança de entrada][4]

Uma regra predefinida permite que os IPs no VNet falem com a sub-rede ASE. Outra regra padrão permite ao equilibrador de carga, também conhecido como VIP público, comunicar com o ASE. Para ver as regras predefinitivas, selecione **regras predefinitivas** ao lado do ícone **Adicionar.** Se colocar uma regra de negar tudo o resto antes das regras padrão, evita o tráfego entre o VIP e o ASE. Para evitar que o tráfego venha de dentro do VNet, adicione a sua própria regra para permitir a entrada. Utilize uma fonte igual à AzureLoadBalancer com um destino de **Qualquer** e uma gama portuária de **\*** . Como a regra NSG é aplicada à sub-rede ASE, não precisa de ser específico no destino.

Se atribuiu um endereço IP à sua aplicação, certifique-se de que mantém as portas abertas. Para ver as portas, selecione endereços IP **do Ambiente de Serviço de Aplicações**  >  **IP addresses**.  

São necessários todos os artigos indicados nas seguintes regras de saída, com exceção do último item. Permitem o acesso à rede às dependências da ASE que foram notadas anteriormente neste artigo. Se bloquear algum deles, o seu ASE deixa de funcionar. O último item da lista permite ao seu ASE comunicar com outros recursos no seu VNet.

![Regras de segurança de saída][5]

Depois de definidos os NSGs, atribua-os à sub-rede em que o seu ASE está ligado. Se não se lembrar do ASE VNet ou sub-rede, pode vê-lo a partir da página do portal ASE. Para atribuir o NSG à sua sub-rede, vá à sub-rede UI e selecione o NSG.

## <a name="routes"></a>Rotas ##

O túnel forçado é quando define rotas no seu VNet para que o tráfego de saída não vá diretamente para a internet, mas em outro lugar como um gateway ExpressRoute ou um aparelho virtual.  Se precisar de configurar o seu ASE de tal forma, leia o documento sobre a configuração do seu Ambiente de Serviço de [Aplicações com Túneis Forçados][forcedtunnel].  Este documento dir-lhe-á as opções disponíveis para trabalhar com o ExpressRoute e o túnel forçado.

Quando cria um ASE no portal também criamos um conjunto de tabelas de rotas na sub-rede que é criada com o ASE.  Essas rotas simplesmente dizem para enviar tráfego de saída diretamente para a internet.  
Para criar as mesmas rotas manualmente, siga estes passos:

1. Aceda ao portal do Azure. Selecione **tabelas de**rota de rede  >  **Route Tables**.

2. Crie uma nova tabela de rotas na mesma região que o seu VNet.

3. A partir da sua tabela de rotas UI, selecione **Rotas**  >  **Add**.

4. Desacorra o **próximo tipo de lúpulo** para a **Internet** e o **prefixo do endereço** para **0.0.0.0/0**. Selecione **Guardar**.

    Em seguida, vê-se algo como o seguinte:

    ![Rotas funcionais][6]

5. Depois de criar a nova tabela de rotas, vá para a sub-rede que contém o seu ASE. Selecione a sua tabela de rotas a partir da lista no portal. Depois de guardar a alteração, deverá então ver os NSGs e as rotas anotados com a sua sub-rede.

    ![NSGs e rotas][7]

## <a name="service-endpoints"></a>Pontos Finais de Serviço ##

Os Pontos Finais de Serviço permitem restringir o acesso aos serviços multi-inquilino a um conjunto de sub-redes e redes virtuais do Azure. Pode ler mais sobre Pontos Finais de Serviço na documentação [Pontos Finais de Serviço de Rede Virtual][serviceendpoints]. 

Quando ativar Pontos Finais de Serviço num recurso, são criadas rotas com uma prioridade mais alta do que todas as outras rotas. Se utilizar os Pontos finais de serviço em qualquer serviço Azure, com um ASE de túneis forçados, o tráfego para esses serviços não será forçado a fazer um túnel. 

Quando estão ativados Pontos Finais de Serviço numa sub-rede com uma instância do SQL do Azure, todas as instâncias do SQL do Azure ligadas a partir dessa sub-rede precisam de ter Pontos Finais de Serviço ativados. Se quiser aceder a várias instâncias de SQL do Azure a partir da mesma sub-rede, não pode ativar Pontos Finais de Serviço numa instância do SQL do Azure e não na outra. Nenhum outro serviço Azure se comporta como a Azure SQL no que diz respeito aos Pontos Endpoint de Serviço. Quando ativar Pontos Finais de Serviço com o Armazenamento do Azure, pode bloquear o acesso a esse recurso a partir da sua sub-rede, mas pode continuar a aceder a outras contas de Armazenamento do Azure, mesmo que não tenham Pontos Finais de Serviço ativados.  

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
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md