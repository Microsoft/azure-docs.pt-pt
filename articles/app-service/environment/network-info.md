---
title: Considerações de redes
description: Conheça o tráfego da rede ASE e como definir grupos de segurança de rede e rotas definidas pelo utilizador com a sua ASE.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 01/24/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fb931c309b5f85902d8abc9cc6da45576bff4041
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356133"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Considerações de networking para um Ambiente de Serviço de Aplicações #

## <a name="overview"></a>Descrição geral ##

 O Azure [App Service Environment][Intro] é uma implantação do Azure App Service numa subnet na sua rede virtual Azure (VNet). Existem dois tipos de implementação para um ambiente de serviço de aplicações (ASE):

- **ASE Externa**: Expõe as aplicações hospedadas pela ASE num endereço IP acessível à Internet. Para mais informações, consulte [Criar uma ASE Externa][MakeExternalASE].
- **ILB ASE**: Expõe as aplicações hospedadas pela ASE num endereço IP dentro do seu VNet. O ponto final interno é um equilibrista interno de carga (ILB), razão pela qual é chamado de ILB ASE. Para mais informações, consulte [Criar e utilizar um ILB ASE][MakeILBASE].

Todas as ASEs, External e ILB, têm um VIP público que é utilizado para o tráfego de gestão de entrada e como endereço quando faz chamadas da ASE para a internet. As chamadas de uma ASE que vão para a internet deixam o VNet através do VIP atribuído à ASE. O IP público deste VIP é a fonte IP para todas as chamadas da ASE que vão para a internet. Se as aplicações da sua ASE fizerem chamadas para recursos no seu VNet ou através de uma VPN, o IP de origem é um dos IPs na subnet utilizado pela sua ASE. Como a ASE está dentro do VNet, também pode aceder a recursos dentro do VNet sem qualquer configuração adicional. Se o VNet estiver ligado à sua rede no local, as aplicações na sua ASE também têm acesso a recursos sem configuração adicional.

![ASE externa][1] 

Se tiver uma ASE Externa, o VIP público é também o ponto final a que as suas aplicações ASE resolvem para:

* HTTP/S 
* FTP/S
* Implantação web
* Depuração remota

![ILB ASE][2]

Se tiver um ILB ASE, o endereço do endereço ILB é o ponto final para HTTP/S, FTP/S, implantação web e depuração remota.

## <a name="ase-subnet-size"></a>Tamanho da subnet ASE ##

O tamanho da sub-rede utilizada para alojar uma ASE não pode ser alterado após a implantação da ASE.  A ASE utiliza um endereço para cada função de infraestrutura, bem como para cada instância de plano de serviço de aplicações isolada.  Além disso, existem cinco endereços utilizados pela Azure Networking para cada subrede que é criada.  Um ASE sem planos de Serviço de Aplicações utilizará 12 endereços antes de criar uma aplicação.  Se for um ILB ASE, então utilizará 13 endereços antes de criar uma aplicação nesse ASE. À medida que escala a sua ASE, as funções de infraestrutura são adicionadas a cada múltiplo de 15 e 20 instâncias do seu plano de App Service.

   > [!NOTE]
   > Nada mais pode estar na sub-rede a não ser a ASE. Certifique-se de escolher um espaço de endereço que permita o crescimento futuro. Não pode mudar este cenário mais tarde. Recomendamos um tamanho de `/24` com 256 endereços.

Quando escala para cima ou para baixo, são adicionados novos papéis do tamanho apropriado e, em seguida, as suas cargas de trabalho são migradas do tamanho atual para o tamanho do alvo. Os VMs originais removidos apenas após a migração das cargas de trabalho. Se tivesse uma ASE com 100 instâncias ASP, haveria um período em que precisaria do dobro do número de VMs.  É por esta razão que recomendamos a utilização de um '/24' para acomodar quaisquer alterações que possa necessitar.  

## <a name="ase-dependencies"></a>Dependências da ASE ##

### <a name="ase-inbound-dependencies"></a>Dependências de entrada da ASE ###

Apenas para que a ASE funcione, a ASE exige que sejam abertas as seguintes portas:

| Utilização | De | Para |
|-----|------|----|
| Gestão | Endereços de gestão de serviços de aplicações | Sub-rede ASE: 454.455 |
|  Comunicação interna da ASE | Sub-rede ASE: Todas as portas | Sub-rede ASE: Todas as portas
|  Permitir a entrada do equilibrador de carga Azure | Balanceador de carga do Azure | Sub-rede ASE: 16001

Existem outros 2 portos que podem mostrar como abertos numa varredura portuária, 7654 e 1221. Respondem com um endereço IP e nada mais. Podem ser bloqueados se desejarem. 

O tráfego de gestão de entrada fornece comando e controlo da ASE para além da monitorização do sistema. Os endereços de origem para este tráfego estão listados no documento de [endereços da ASE Management.][ASEManagement] A configuração de segurança da rede tem de permitir o acesso dos endereços de gestão da ASE nos portos 454 e 455. Se bloquear o acesso a partir desses endereços, a sua ASE tornar-se-á insalubre e depois será suspensa. O tráfego de TCP que entra nos portos 454 e 455 deve voltar do mesmo VIP ou terá um problema de encaminhamento assimétrico. 

Dentro da sub-rede ASE, existem muitas portas utilizadas para a comunicação de componentes internos e podem mudar. Isto exige que todas as portas da sub-rede ASE estejam acessíveis a partir da sub-rede ASE. 

Para a comunicação entre o equilibrador de carga Azure e a sub-rede ASE, as portas mínimas que precisam de ser abertas são 454, 455 e 16001. A porta 16001 é utilizada para manter vivo o tráfego entre o equilibrista de carga e a ASE. Se estiver a usar um ILB ASE, então pode bloquear o tráfego apenas para as portas 454,455, 16001.  Se estiver a utilizar uma ASE Externa, então tem de ter em conta as portas normais de acesso à aplicação.  

As outras portas com as suas necessidades são as portas de aplicação:

| Utilização | Portas |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Depuração remota do Estúdio Visual  |  4020, 4022, 4024 |
|  Serviço de implantação web | 8172 |

Se bloquear as portas de aplicação, a sua ASE ainda pode funcionar, mas a sua aplicação pode não.  Se estiver a utilizar endereços IP atribuídos à aplicação com uma ASE Externa, terá de permitir o tráfego dos IPs atribuídos às suas aplicações para a sub-rede ASE nas portas mostradas na página do portal ASE > IP Addresss.

### <a name="ase-outbound-dependencies"></a>Dependências de saída da ASE ###

Para acesso de saída, uma ASE depende de múltiplos sistemas externos. Muitas dessas dependências do sistema são definidas com nomes DNS e não mapeiam para um conjunto fixo de endereços IP. Assim, a ASE requer acesso de saída da sub-rede ASE a todos os IPs externos em várias portas. 

A ASE comunica com endereços acessíveis à Internet nas seguintes portas:

| Utilizações | Portas |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| CRL, atualizações do Windows, dependências linux, serviços Azure | 80/443 |
| SQL do Azure | 1433 | 
| Monitorização | 12000 |

As dependências de saída estão listadas no documento que descreve o bloqueio do tráfego de saída do [App Service Environment](./firewall-integration.md). Se a ASE perder o acesso às suas dependências, deixa de funcionar. Quando isso acontece tempo suficiente, a ASE é suspensa. 

### <a name="customer-dns"></a>DNS do cliente ###

Se o VNet estiver configurado com um servidor DNS definido pelo cliente, as cargas de trabalho do inquilino usam-no. A ASE utiliza DNS Azure para fins de gestão. Se o VNet estiver configurado com um servidor DNS selecionado pelo cliente, o servidor DNS deve ser acessível a partir da sub-rede que contém a ASE.

Para testar a resolução dNS a partir da sua aplicação web, pode utilizar o *nome*de comando da consola . Vá à janela de depuração no seu site scm para a sua aplicação ou vá à aplicação no portal e selecione a consola. A partir da instrução da concha pode emitir o *nome* de comando, juntamente com o nome DNS que deseja procurar. O resultado que obtém é o mesmo que a sua aplicação obteria ao fazer a mesma procura. Se utilizar o nslookup, irá fazer uma procura utilizando o Azure DNS.

Se alterar a definição de DNS do VNet em que a sua ASE está, terá de reiniciar a sua ASE. Para evitar reiniciar a Sua ASE, é altamente recomendável que configure as definições de DNS para o seu VNet antes de criar a sua ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Dependências do portal ##

Além das dependências funcionais da ASE, existem alguns itens extra relacionados com a experiência do portal. Algumas das capacidades do portal Azure dependem do acesso direto ao _site SCM._ Para cada aplicação no Azure App Service, existem dois URLs. O primeiro URL é aceder à sua aplicação. O segundo URL é aceder ao site SCM, que também é chamado de _consola Kudu_. As funcionalidades que utilizam o site SCM incluem:

-   Trabalhos web
-   Funções
-   Streaming de log
-   Rio Kudu
-   Extensões
-   Process Explorer
-   Consola

Quando se utiliza um ILB ASE, o site SCM não é acessível de fora da VNet. Algumas capacidades não funcionarão a partir do portal da aplicação porque exigem o acesso ao site SCM de uma aplicação. Pode ligar-se diretamente ao site SCM em vez de utilizar o portal. 

Se o seu ILB ASE for o nome de domínio *contoso.appserviceenvironment.net* e o seu nome de aplicação for *testapp*, a aplicação é contactada em *testapp.contoso.appserviceenvironment.net*. O site SCM que acompanha é alcançado em *testapp.scm.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>Endereços IP da ASE ##

Uma ASE tem alguns endereços IP para estar atento. São:

- **Endereço IP**de entrada pública : Utilizado para o tráfego de aplicações numa ASE Externa, e tráfego de gestão tanto numa ASE Externa como numa ASE ILB.
- **IP público de saída**: Usado como o IP "a partir" para ligações de saída da ASE que deixam o VNet, que não são encaminhados para baixo de uma VPN.
- **Endereço IP ILB**: O endereço IP ILB só existe num ILB ASE.
- **Endereços SSL baseados em aplicativos ip**: Só é possível com uma ASE externa e quando o SSL baseado em IP é configurado.

Todos estes endereços IP são visíveis no portal Azure da UI Da ASE. Se tiver um ILB ASE, o IP para o ILB está listado.

   > [!NOTE]
   > Estes endereços IP não se alterarão enquanto a Sua ASE se mantiver a funcionar.  Se a sua ASE ficar suspensa e restaurada, os endereços utilizados pela sua ASE mudarão. A causa normal para uma ASE ficar suspensa é se bloquear o acesso à gestão de entrada ou bloquear o acesso a uma dependência da ASE. 

![Endereços IP][3]

### <a name="app-assigned-ip-addresses"></a>Endereços IP atribuídos a aplicativos ###

Com uma ASE Externa, pode atribuir endereços IP a aplicações individuais. Não podes fazer isso com uma ASE ILB. Para obter mais informações sobre como configurar a sua aplicação para ter o seu próprio endereço IP, consulte [Secure um nome DNS personalizado com uma ligação SSL no Serviço de Aplicações Azure](../configure-ssl-bindings.md).

Quando uma aplicação tem o seu próprio endereço SSL baseado em IP, a ASE reserva duas portas para mapear para esse endereço IP. Uma porta é para tráfego HTTP, e a outra porta é para HTTPS. Essas portas estão listadas na UI Da ASE na secção de endereços IP. O tráfego deve ser capaz de chegar às portas do VIP ou as aplicações estão inacessíveis. Este requisito é importante de lembrar quando configura os Grupos de Segurança da Rede (NSGs).

## <a name="network-security-groups"></a>Grupos de Segurança de Rede ##

[Os Grupos de Segurança][NSGs] da Rede fornecem a capacidade de controlar o acesso à rede dentro de um VNet. Quando se usa o portal, há uma regra implícita de negação na menor prioridade para negar tudo. O que constrói são as suas regras de permitir.

Numa ASE, não tem acesso aos VMs usados para acolher a própria ASE. Estão numa subscrição gerida pela Microsoft. Se quiser restringir o acesso às aplicações na ASE, coloque NSGs na sub-rede ASE. Ao fazê-lo, preste atenção às dependências da ASE. Se bloquear qualquer dependência, a ASE deixa de funcionar.

Os NSGs podem ser configurados através do portal Azure ou através do PowerShell. A informação aqui mostra o portal Azure. Cria e gere nsGs no portal como um recurso de alto nível no âmbito do **Networking**.

As entradas necessárias num NSG, para que uma ASE funcione, são para permitir o tráfego:

**Entrada**
* da etiqueta de serviço IP AppServiceManagement nas portas 454.455
* do equilibrador de carga na porta 16001
* da sub-rede ASE à subrede ASE em todas as portas

**Saída**
* a todos os IPs na porta 123
* a todos os IPs nas portas 80,443
* para a etiqueta de serviço IP AzureSQL nas portas 1433
* a todos os IPs na porta 12000
* para a subrede ASE em todas as portas

A porta DNS não precisa de ser adicionada, uma vez que o tráfego para o DNS não é afetado pelas regras do NSG. Estas portas não incluem as portas que as suas aplicações necessitam para uma utilização bem sucedida. As portas normais de acesso à aplicação são:

| Utilização | Portas |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Depuração remota do Estúdio Visual  |  4020, 4022, 4024 |
|  Serviço de implantação web | 8172 |

Quando os requisitos de entrada e saída forem tomados em consideração, os NSGs devem ser semelhantes aos NSGs mostrados neste exemplo. 

![Regras de segurança de entrada][4]

Uma regra predefinida permite que os IPs no VNet falem com a sub-rede ASE. Outra regra predefinida permite ao equilibrador de carga, também conhecido como VIP público, comunicar com a ASE. Para ver as regras predefinidas, selecione **regras padrão** ao lado do ícone **Adicionar.** Se negar tudo o resto antes das regras padrão, evite o tráfego entre o VIP e a ASE. Para evitar que o tráfego venha de dentro do VNet, adicione a sua própria regra para permitir a entrada. Utilize uma fonte igual à AzureLoadBalancer com destino a **Qualquer** e uma gama de porta de **\*** . Como a regra NSG é aplicada à sub-rede ASE, não precisa de ser específica no destino.

Se atribuiu um endereço IP à sua aplicação, certifique-se de que mantém as portas abertas. Para ver as portas, selecione **App Service Ambiente** > **endereços IP**.  

Todos os itens apresentados nas seguintes regras de saída são necessários, exceto o último item. Permitem o acesso da rede às dependências da ASE que foram notadas anteriormente neste artigo. Se bloquear algum deles, a Sua ASE deixa de funcionar. O último item da lista permite à sua ASE comunicar com outros recursos no seu VNet.

![Regras de segurança de saída][5]

Depois de definidos os seus NSGs, atribua-os à sub-rede em que a sua ASE está. Se não se lembrar da ASE VNet ou subnet, pode vê-la na página do portal DaSE. Para atribuir o NSG à sua sub-rede, vá à UI de sub-rede e selecione o NSG.

## <a name="routes"></a>Rotas ##

O túnel forçado é quando estabelece rotas no seu VNet para que o tráfego de saída não vá diretamente para a internet, mas em outro lugar como um gateway ExpressRoute ou um aparelho virtual.  Se precisar de configurar a Sua ASE de tal forma, leia o documento sobre configurar o seu Ambiente de Serviço de [Aplicações com túnel forçado][forcedtunnel].  Este documento dir-lhe-á as opções disponíveis para trabalhar com a ExpressRoute e túneis forçados.

Quando se cria uma ASE no portal criamos também um conjunto de tabelas de rotas na subnet que é criada com a ASE.  Essas rotas simplesmente dizem para enviar tráfego de saída diretamente para a internet.  
Para criar as mesmas rotas manualmente, siga estes passos:

1. Aceda ao portal do Azure. Selecione **tabelas**de rota > **de rede**  .

2. Crie uma nova tabela de rotas na mesma região que o seu VNet.

3. A partir da tabela de rotas UI, selecione **Rotas** > **Adicionar**.

4. Desloque o **tipo seguinte de lúpulo** para a **Internet** e o **prefixo** do Endereço para **0.0.0.0/0**. Selecione **Guardar**.

    Então vê-se algo como o seguinte:

    ![Rotas funcionais][6]

5. Depois de criar a nova tabela de rotas, vá à subnet que contém a sua ASE. Selecione a tabela de rotas da lista no portal. Depois de guardar a mudança, deverá então ver os NSGs e as rotas anotadas com a sua sub-rede.

    ![NSGs e rotas][7]

## <a name="service-endpoints"></a>Pontos Finais de Serviço ##

Os Pontos Finais de Serviço permitem restringir o acesso aos serviços multi-inquilino a um conjunto de sub-redes e redes virtuais do Azure. Pode ler mais sobre os pontos finais do serviço na documentação de [Pontos Finais][serviceendpoints] do Serviço de Rede Virtual. 

Quando ativar Pontos Finais de Serviço num recurso, são criadas rotas com uma prioridade mais alta do que todas as outras rotas. Se utilizar pontos finais de serviço em qualquer serviço Azure, com uma ASE forçada, o tráfego para esses serviços não será forçado a fazer um túnel. 

Quando estão ativados Pontos Finais de Serviço numa sub-rede com uma instância do SQL do Azure, todas as instâncias do SQL do Azure ligadas a partir dessa sub-rede precisam de ter Pontos Finais de Serviço ativados. Se quiser aceder a várias instâncias de SQL do Azure a partir da mesma sub-rede, não pode ativar Pontos Finais de Serviço numa instância do SQL do Azure e não na outra. Nenhum outro serviço Azure se comporta como o Azure SQL no que diz respeito aos Pontos Finais de Serviço. Quando ativar Pontos Finais de Serviço com o Armazenamento do Azure, pode bloquear o acesso a esse recurso a partir da sua sub-rede, mas pode continuar a aceder a outras contas de Armazenamento do Azure, mesmo que não tenham Pontos Finais de Serviço ativados.  

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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
