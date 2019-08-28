---
title: Integrar o aplicativo com o serviço de Azure App de rede virtual do Azure
description: Mostra como conectar um aplicativo no serviço Azure App a uma rede virtual do Azure nova ou existente
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/21/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a96c02d1d7d2fae43e0a5915e9233bde842ce621
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066675"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar seu aplicativo a uma rede virtual do Azure
Este documento descreve o recurso de integração de rede virtual do serviço Azure App e como configurá-lo com aplicativos no [serviço Azure app](https://go.microsoft.com/fwlink/?LinkId=529714). [Redes virtuais do Azure][VNETOverview] (VNets) permitem que você coloque muitos dos seus recursos do Azure em uma rede roteável que não é da Internet.  

O serviço de Azure App tem duas variações. 

1. Os sistemas multilocatários que oferecem suporte a toda a gama de planos de preços, exceto os isolados
2. O Ambiente do Serviço de Aplicativo (ASE), que é implantado em sua VNet e dá suporte a aplicativos de plano de preços isolados

Este documento percorre os dois recursos de integração VNet, que é para uso no serviço de aplicativo multilocatário. Se seu aplicativo estiver em [ambiente do serviço de aplicativo][ASEintro], ele já estará em uma VNet e não exigirá o uso do recurso de integração VNet para alcançar recursos na mesma VNet. Para obter detalhes sobre todos os recursos de rede do serviço de aplicativo, leia [recursos de rede do serviço de aplicativo](networking-features.md)

Há dois formulários para o recurso de integração VNet

1. Uma versão habilita a integração com o VNets na mesma região. Essa forma do recurso requer uma sub-rede em uma VNet na mesma região. Este recurso ainda está em visualização, mas tem suporte para cargas de trabalho de produção do aplicativo do Windows com algumas limitações indicadas abaixo.
2. A outra versão permite a integração com o VNets em outras regiões ou com o VNets clássico. Esta versão do recurso requer a implantação de um gateway de rede virtual em sua VNet. Esse é o recurso baseado em VPN ponto a site e só é suportado com aplicativos do Windows.

Um aplicativo pode usar apenas um formulário do recurso de integração VNet de cada vez. Em seguida, a pergunta é qual recurso você deve usar. Você pode usar o para muitas coisas. No entanto, os diferenciadores claros são:

| Problema  | Solução | 
|----------|----------|
| Deseja alcançar um endereço RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) na mesma região | Integração de VNet regional |
| Deseja acessar recursos em uma VNet clássica ou em uma VNet em outra região | Integração VNet necessária do gateway |
| Deseja alcançar os pontos de extremidade RFC 1918 no ExpressRoute | Integração de VNet regional |
| Deseja acessar recursos entre pontos de extremidade de serviço | Integração de VNet regional |

Nenhum recurso permitirá que você alcance endereços não RFC 1918 no ExpressRoute. Para fazer isso, você precisa usar um ASE por enquanto.

O uso da integração VNet regional não conecta sua VNet ao local ou configura pontos de extremidade de serviço. Isso é uma configuração de rede separada. A integração VNet regional simplesmente permite que seu aplicativo faça chamadas entre esses tipos de conexão.

Independentemente da versão usada, a integração VNet dá ao seu aplicativo Web acesso aos recursos em sua rede virtual, mas não concede acesso privado de entrada ao seu aplicativo Web a partir da rede virtual. Acesso ao site privado refere-se a tornar seu aplicativo acessível somente de uma rede privada, como de dentro de uma rede virtual do Azure. A integração VNet é apenas para fazer chamadas de saída de seu aplicativo para sua VNet. 

O recurso de integração VNet:

* requer um plano de preços Standard, Premium ou PremiumV2 
* dá suporte a TCP e UDP
* funciona com aplicativos do serviço de aplicativo e aplicativos de funções

Há algumas coisas para as quais a integração VNet não oferece suporte, incluindo:

* montando uma unidade
* A integração do AD 
* NetBios

## <a name="regional-vnet-integration"></a>Integração de VNet regional 

Quando a integração VNet é usada com VNets na mesma região que seu aplicativo, ele requer o uso de uma sub-rede delegada com pelo menos 32 endereços. A sub-rede não pode ser usada para nada mais. Chamadas de saída feitas de seu aplicativo serão feitas a partir dos endereços na sub-rede delegada. Quando você usa essa versão da integração VNet, as chamadas são feitas de endereços em sua VNet. O uso de endereços em sua VNet permite que seu aplicativo:

* Fazer chamadas para serviços protegidos de ponto de extremidade de serviço
* Acessar recursos entre conexões do ExpressRoute
* Acesse recursos na VNet à qual você está conectado
* Acessar recursos entre conexões emparelhadas, incluindo conexões de ExpressRoute

Esse recurso está em versão prévia, mas há suporte para cargas de trabalho de produção do aplicativo do Windows com as seguintes limitações:

* Você só pode alcançar endereços que estejam no intervalo RFC 1918. Esses são endereços nos blocos de endereços 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.
* Você não pode acessar recursos entre conexões de emparelhamento globais
* Você não pode definir rotas no tráfego proveniente de seu aplicativo para sua VNet
* O recurso só está disponível em unidades de escala do serviço de aplicativo mais recentes que dão suporte a planos do serviço de aplicativo do PremiumV2.
* A sub-rede de integração só pode ser usada por um plano do serviço de aplicativo
* O recurso não pode ser usado por aplicativos de plano isolado que estão em um Ambiente do Serviço de Aplicativo
* O recurso requer uma sub-rede não usada que seja de/27 com 32 endereços ou maior em sua VNet do Resource Manager
* O aplicativo e a VNet devem estar na mesma região
* Você não pode excluir uma VNet com um aplicativo integrado. Você deve remover a integração primeiro 
* Você pode ter apenas uma integração VNet regional por plano do serviço de aplicativo. Vários aplicativos no mesmo plano do serviço de aplicativo podem usar a mesma VNet. 
* Você não pode alterar a assinatura de um aplicativo ou um plano do serviço de aplicativo enquanto houver um aplicativo que está usando a integração VNet regional

Um endereço é usado para cada instância do plano do serviço de aplicativo. Se você dimensionou seu aplicativo para 5 instâncias, 5 endereços são usados. Como o tamanho da sub-rede não pode ser alterado após a atribuição, você deve usar uma sub-rede que seja grande o suficiente para acomodar qualquer escala que seu aplicativo possa atingir. R/26 com 64 endereços é o tamanho recomendado. A/27 com 32 endereços acomodariam um plano do serviço de aplicativo Premium 20 instâncias se você não alterou o tamanho do plano do serviço de aplicativo. Ao dimensionar um plano do serviço de aplicativo para cima ou para baixo, você precisará de duas vezes mais endereços por um curto período de tempo. 

Se você quiser que seus aplicativos em outro plano do serviço de aplicativo alcancem uma VNet que está conectada ao já por aplicativos em outro plano do serviço de aplicativo, será necessário selecionar uma sub-rede diferente daquela que está sendo usada pela integração VNet pré-existente.  

O recurso também está em versão prévia para Linux. Para usar o recurso de integração VNet com uma VNet do Resource Manager na mesma região:

1. Vá para a interface do usuário de rede no Portal. Se seu aplicativo for capaz de usar o novo recurso, você verá uma opção para adicionar VNet (versão prévia).  

   ![Selecionar integração VNet][6]

1. Selecione **Adicionar VNet (versão prévia)** .  

1. Selecione a VNet do Resource Manager que você deseja integrar com o e, em seguida, crie uma nova sub-rede ou escolha uma sub-rede preexistente vazia. A integração leva menos de um minuto para ser concluída. Durante a integração, seu aplicativo é reiniciado.  Quando a integração for concluída, você verá detalhes sobre a VNet com a qual está integrado e uma faixa na parte superior que informa que o recurso está em versão prévia.

   ![Selecione a VNet e a sub-rede][7]

Depois que seu aplicativo estiver integrado à sua VNet, ele usará o mesmo servidor DNS com o qual sua VNet está configurada. 

A integração VNet regional requer que sua sub-rede de integração seja delegada para Microsoft. Web.  A interface do usuário de integração VNet delegará a sub-rede ao Microsoft. Web automaticamente. Se sua conta não tiver permissões de rede suficientes para definir isso, você precisará de alguém que possa definir atributos em sua sub-rede de integração para delegar a sub-rede. Para delegar manualmente a sub-rede de integração, vá para a interface do usuário da sub-rede da rede virtual do Azure e defina delegação para Microsoft. Web.

Para desconectar seu aplicativo da VNet, selecione **Desconectar**. Isso reiniciará seu aplicativo Web. 


#### <a name="web-app-for-containers"></a>Web App para Contentores

Se você usar o serviço de aplicativo no Linux com as imagens internas, o recurso de integração VNet regional funcionará sem alterações adicionais. Se você usar Aplicativo Web para Contêineres, precisará modificar a imagem do Docker para usar a integração VNet. Na imagem do Docker, use a variável de ambiente PORT como a porta de escuta do servidor Web principal, em vez de usar um número de porta codificado. A variável de ambiente PORT é definida automaticamente pela plataforma do serviço de aplicativo no momento da inicialização do contêiner.

### <a name="service-endpoints"></a>Pontos Finais de Serviço

O novo recurso de integração VNet permite que você use pontos de extremidade de serviço.  Para usar pontos de extremidade de serviço com seu aplicativo, use a nova integração de VNet para se conectar a uma VNet selecionada e, em seguida, configure os pontos de extremidade de serviço na sub-rede usada para a integração. 


### <a name="how-vnet-integration-works"></a>Como funciona a integração VNet

Os aplicativos no serviço de aplicativo são hospedados em funções de trabalho. Os planos de preços básicos e mais altos são planos de hospedagem dedicados em que não há nenhuma outra carga de trabalho de clientes em execução nos mesmos trabalhadores. A integração VNet funciona por meio da montagem de interfaces virtuais com endereços na sub-rede delegada. Como o endereço de está em sua VNet, ele tem acesso à maioria das coisas de ou por meio de sua VNet, assim como uma VM em sua VNet. A implementação de rede é diferente de executar uma VM em sua VNet e é por isso que alguns recursos de rede ainda não estão disponíveis ao usar esse recurso.

![Integração de VNet](media/web-sites-integrate-with-vnet/vnet-integration.png)

Quando a integração VNet estiver habilitada, seu aplicativo ainda fará chamadas de saída para a Internet por meio dos mesmos canais que o normal. Os endereços de saída listados no portal de propriedades do aplicativo ainda são os endereços usados pelo seu aplicativo. As alterações para seu aplicativo são chamadas para serviços protegidos de ponto de extremidade de serviço ou endereços RFC 1918 vão para sua VNet. 

O recurso só dá suporte a uma interface virtual por trabalho.  Uma interface virtual por trabalho significa uma integração VNet regional por plano do serviço de aplicativo. Todos os aplicativos no mesmo plano do serviço de aplicativo podem usar a mesma integração de VNet, mas se você precisar de um aplicativo para se conectar a uma VNet adicional, será necessário criar outro plano do serviço de aplicativo. A interface virtual usada não é um recurso ao qual os clientes têm acesso direto.

Devido à natureza de como essa tecnologia Opera, o tráfego usado com a integração VNet não aparece no observador de rede ou nos logs de fluxo do NSG.  

## <a name="gateway-required-vnet-integration"></a>Integração VNet necessária do gateway 

O gateway exigiu o recurso de integração VNet:

* Pode ser usado para se conectar ao VNets em qualquer região, seja ele o Resource Manager ou o VNets clássico
* Permite que um aplicativo se conecte a apenas uma VNet por vez
* Permite que até cinco VNets sejam integradas com o em um plano do serviço de aplicativo 
* Permite que a mesma VNet seja usada por vários aplicativos em um plano do serviço de aplicativo sem afetar o número total que pode ser usado por um plano do serviço de aplicativo.  Se você tiver 6 aplicativos usando a mesma VNet no mesmo plano do serviço de aplicativo, isso contará como uma VNet sendo usada. 
* Requer um gateway de rede virtual configurado com VPN ponto a site
* Dá suporte a um SLA de 99,9% devido ao SLA no gateway

Este recurso não oferece suporte a:
* Usar com aplicativos do Linux
* O acesso a recursos no ExpressRoute 
* O acesso a recursos nos Pontos Finais de Serviço 

### <a name="getting-started"></a>Introdução

Aqui estão algumas coisas para ter em mente antes de conectar seu aplicativo Web a uma rede virtual:

* Uma rede virtual de destino deve ter uma VPN ponto a site habilitada com um gateway baseado em rota para que possa ser conectada ao aplicativo. 
* A VNet deve estar na mesma assinatura que o seu ASP (plano do serviço de aplicativo).
* Os aplicativos que se integram com uma VNet usam o DNS especificado para essa VNet.

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurar um gateway em sua VNet ###

Se você já tiver um gateway configurado com endereços ponto a site, poderá ignorar a configuração da integração VNet com seu aplicativo.  
Para criar um gateway:

1. [Crie uma sub-rede de gateway][creategatewaysubnet] em sua VNet.  

1. [Crie o gateway de VPN][creategateway]. Selecione um tipo de VPN baseado em rota.

1. [Defina os endereços ponto a site][setp2saddresses]. Se o gateway não estiver no SKU básico, o IKEV2 deverá ser desabilitado na configuração ponto a site e o SSTP deverá ser selecionado. O espaço de endereço deve estar nos blocos de endereço RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Se você estiver criando apenas o gateway para uso com a integração VNet do serviço de aplicativo, não será necessário carregar um certificado. A criação do gateway pode levar 30 minutos. Você não poderá integrar seu aplicativo à VNet até que o gateway seja provisionado. 

### <a name="configure-vnet-integration-with-your-app"></a>Configurar a integração VNet com seu aplicativo 

Para habilitar a integração VNet em seu aplicativo: 

1. Vá para seu aplicativo na portal do Azure e abra configurações do aplicativo e selecione rede > integração VNet. O ASP deve estar em um SKU Standard ou melhor para usar o recurso de integração VNet. 
 ![Interface do usuário de integração VNet][1]

1. Selecione **Adicionar VNet**. 
 ![Adicionar integração de VNet][2]

1. Selecione sua VNet. 
  ![Selecione sua VNet][8]
  
Seu aplicativo será reiniciado após esta última etapa.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Como funciona o recurso de integração VNet do gateway

O recurso de integração VNet do gateway necessário foi criado sobre a tecnologia VPN ponto a site. A tecnologia ponto a site limita o acesso à rede apenas à máquina virtual que hospeda o aplicativo. Os aplicativos são restritos a enviar somente tráfego para a Internet, por meio de Conexões Híbridas ou por meio da integração VNet. 

![Como funciona a integração VNet][3]

## <a name="managing-vnet-integration"></a>Gerenciando a integração VNet
A capacidade de se conectar e desconectar-se a uma VNet está em um nível de aplicativo. As operações que podem afetar a integração VNet entre vários aplicativos estão no nível do plano do serviço de aplicativo. No aplicativo > rede > Portal de integração VNet, você pode obter detalhes sobre sua VNet. Você pode ver informações semelhantes no nível ASP na rede > ASP > Portal de integração VNet, incluindo quais aplicativos nesse plano do serviço de aplicativo estão usando uma determinada integração.

 ![Detalhes da VNet][4]

As informações disponíveis para você na interface do usuário de integração de VNet são as mesmas entre os portais do aplicativo e do ASP.

* Nome da VNet-links para a interface do usuário da rede virtual
* Local – reflete o local da sua VNet. A integração com uma VNet em outro local pode causar problemas de latência para seu aplicativo. 
* Status do certificado – reflete se os certificados estão em sincronia entre o plano do serviço de aplicativo e sua VNet.
* Status do gateway – se você estiver usando a integração VNet necessária do gateway, poderá ver o status do gateway.
* Espaço de endereço de VNet – mostra o espaço de endereço IP para sua VNet. 
* Espaço de endereço ponto a site – mostra o espaço de endereço IP ponto a site para sua VNet. Ao fazer chamadas em sua VNet ao usar o recurso de gateway necessário, o endereço do seu aplicativo será um desses endereços. 
* Espaço de endereço site a site-você pode usar VPNs site a site para conectar sua VNet aos seus recursos locais ou a outra VNet. Os intervalos de IP definidos com essa conexão VPN são mostrados aqui.
* Servidores DNS – mostra os servidores DNS configurados com sua VNet.
* Endereços IP roteados para a VNet – mostra os blocos de endereços roteados usados para direcionar o tráfego para a VNet 

A única operação que você pode executar na exibição do aplicativo de sua integração VNet é desconectar seu aplicativo da VNet à qual ele está atualmente conectado. Para desconectar seu aplicativo de uma VNet,selecione desconectar. Seu aplicativo será reiniciado quando você se desconectar de uma VNet. A desconexão não altera sua VNet. A sub-rede ou o gateway não é removido. Se você quiser excluir sua VNet, primeiro você precisa desconectar seu aplicativo da VNet e excluir os recursos nele, como gateways. 

Para acessar a interface do usuário de integração da VNet ASP, abra sua interface do usuário ASP e selecione **rede**.  Em integração de VNet, selecione **clique aqui para configurar** para abrir a interface do usuário de status do recurso de rede.

![Informações de integração de VNet ASP][5]

A interface do usuário de integração da VNet ASP mostrará todos os VNets que são usados pelos aplicativos em seu ASP. Para ver os detalhes em cada VNet, clique na VNet em que você está interessado. Há duas ações que você pode executar aqui.

* **Sincronizar rede**. A operação de rede de sincronização é apenas para o recurso de integração VNet dependente de gateway. A execução de uma operação de rede de sincronização garante que os certificados e as informações de rede estejam em sincronia. Se você adicionar ou alterar o DNS de sua VNet, será necessário executar uma operação de **sincronização de rede** . Esta operação reiniciará todos os aplicativos que usam essa VNet.
* **Adicionar rotas** A adição de rotas direcionará o tráfego de saída para sua VNet.

**Roteamento** do As rotas que são definidas em sua VNet são usadas para direcionar o tráfego para sua VNet do seu aplicativo. Se você precisar enviar tráfego de saída adicional para a VNet, poderá adicionar esses blocos de endereço aqui. Essa funcionalidade só funciona com a integração VNet necessária do gateway.

**Certificados** do Quando o gateway exigia a integração VNet habilitada, há uma troca necessária de certificados para garantir a segurança da conexão. Junto com os certificados estão a configuração de DNS, as rotas e outras coisas semelhantes que descrevem a rede.
Se os certificados ou as informações de rede forem alterados, você precisará clicar em "sincronizar rede". Quando você clica em "sincronizar rede", causa uma breve interrupção na conectividade entre seu aplicativo e sua VNet. Embora seu aplicativo não seja reiniciado, a perda de conectividade pode fazer com que o site não funcione corretamente. 

## <a name="accessing-on-premises-resources"></a>Acessando recursos locais
Os aplicativos podem acessar recursos locais integrando-se com o VNets que têm conexões site a site. Se estiver usando a integração VNet necessária do gateway, você precisará atualizar suas rotas de gateway de VPN locais com seus blocos de endereço ponto a site. Quando a VPN site a site é configurada pela primeira vez, os scripts usados para configurá-lo devem configurar as rotas corretamente. Se você adicionar os endereços de ponto a site depois de criar a VPN site a site, precisará atualizar as rotas manualmente. Os detalhes sobre como fazer isso variam por gateway e não são descritos aqui. Você não pode ter o BGP configurado com uma conexão VPN site a site.

Não há nenhuma configuração adicional necessária para que o recurso de integração VNet regional alcance por meio de sua VNet e para o local. Você simplesmente precisa conectar sua VNet ao local usando o ExpressRoute ou uma VPN site a site. 

> [!NOTE]
> O recurso de integração VNet do gateway necessário não integra um aplicativo a uma VNet que tem um gateway de ExpressRoute. Mesmo que o gateway de ExpressRoute esteja configurado no [modo][VPNERCoex] de coexistência, a integração VNet não funciona. Se você precisar acessar recursos por meio de uma conexão do ExpressRoute, poderá usar o recurso de integração VNet regional ou um [ambiente do serviço de aplicativo][ASE], que é executado em sua VNet. 
> 
> 

## <a name="peering"></a>Peering
Se você estiver usando o emparelhamento com a integração VNet regional, não será necessário fazer nenhuma configuração adicional. 

Se estiver usando a integração VNet necessária do gateway com o emparelhamento, você precisará configurar alguns itens adicionais. Para configurar o emparelhamento para trabalhar com seu aplicativo:

1. Adicione uma conexão de emparelhamento na VNet à qual seu aplicativo se conecta. Ao adicionar a conexão de emparelhamento, habilite **permitir acesso de rede virtual** e marque **Permitir tráfego encaminhado** e **Permitir trânsito de gateway**.
1. Adicione uma conexão de emparelhamento na VNet que está sendo emparelhada com a VNet à qual você está conectado. Ao adicionar a conexão de emparelhamento na VNet de destino, habilite **permitir acesso de rede virtual** e marque **Permitir tráfego encaminhado** e **permitir gateways remotos**.
1. Vá para o plano do serviço de aplicativo > rede > interface do usuário de integração VNet no Portal.  Selecione a VNet à qual seu aplicativo se conecta. Na seção roteamento, adicione o intervalo de endereços da VNet emparelhada com a VNet à qual seu aplicativo está conectado.  


## <a name="pricing-details"></a>Detalhes de preços
O recurso de integração VNet regional não tem nenhum custo adicional para uso além dos encargos do tipo de preço ASP.

Há três encargos relacionados ao uso do recurso de integração VNet exigido pelo gateway:

* Cobranças do tipo de preço do ASP – seus aplicativos precisam estar em um plano do serviço de aplicativo Standard, Premium ou PremiumV2. Você pode ver mais detalhes sobre esses custos aqui: [Preço do serviço de aplicativo][ASPricing]. 
* Custos de transferência de dados – há um encargo para a saída de dados, mesmo que a VNet esteja na mesma data center. Esses encargos são descritos em [transferência de dados detalhes de preços][DataPricing]. 
* Custos de gateway de VPN – há um custo para o gateway de VNet que é necessário para a VPN ponto a site. Os detalhes estão na página de [preços do gateway de VPN][VNETPricing] .


## <a name="troubleshooting"></a>Resolução de problemas
Embora o recurso seja fácil de configurar, isso não significa que sua experiência estará livre de problemas. Se você encontrar problemas para acessar o ponto de extremidade desejado, há alguns utilitários que podem ser usados para testar a conectividade no console do aplicativo. Há dois consoles que você pode usar. Um é o console do Kudu e o outro é o console do na portal do Azure. Para acessar o console do kudu do seu aplicativo, acesse ferramentas-> kudu. Você também pode acessar o console do Kudo em [SiteName]. SCM. azurewebsites. net. Depois que o site for carregado, vá para a guia Console de depuração. Para acessar o console do portal do Azure hospedado, em seguida, em seu aplicativo, acesse ferramentas-> console. 

#### <a name="tools"></a>Ferramentas
As ferramentas **ping**, **nslookup** e **tracert** não funcionarão no console do devido a restrições de segurança. Para preencher o void, duas ferramentas separadas são adicionadas. Para testar a funcionalidade do DNS, adicionamos uma ferramenta chamada nameresolver. exe. A sintaxe é:

    nameresolver.exe hostname [optional: DNS Server]

Você pode usar **nameresolver** para verificar os nomes de host dos quais seu aplicativo depende. Dessa forma, você pode testar se tem algo mal configurado com seu DNS ou talvez não tenha acesso ao seu servidor DNS. Você pode ver o servidor DNS que seu aplicativo usará no console examinando as variáveis de ambiente WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

A próxima ferramenta permite que você teste a conectividade TCP com uma combinação de host e porta. Essa ferramenta é chamada de **tcpping** e a sintaxe é:

    tcpping.exe hostname [optional: port]

O utilitário **tcpping** informa se você pode alcançar um host e uma porta específicos. Ele só pode mostrar êxito se: há um aplicativo ouvindo na combinação de host e porta, e há acesso à rede do seu aplicativo para o host e a porta especificados.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Depuração de acesso a recursos hospedados na VNet
Há várias coisas que podem impedir que seu aplicativo atinja um host e uma porta específicos. Na maioria das vezes, é uma das três coisas:

* **Um firewall está a caminho.** Se você tiver um firewall no caminho, atingirá o tempo limite de TCP. O tempo limite de TCP é de 21 segundos nesse caso. Use a ferramenta **tcpping** para testar a conectividade. Os tempos limite de TCP podem ser devido a muitas coisas além dos firewalls, mas começam lá. 
* **O DNS não está acessível.** O tempo limite do DNS é de três segundos por servidor DNS. Se você tiver dois servidores DNS, o tempo limite será de 6 segundos. Use nameresolver para ver se o DNS está funcionando. Lembre-se de que não é possível usar Nslookup, pois isso não usa o DNS com o qual a VNet está configurada. Se estiver inacessível, você pode ter um firewall ou NSG bloqueando o acesso ao DNS ou pode estar inoperante.

Se esses itens não responderem a seus problemas, veja primeiro algo como: 

**Integração de VNet regional**
* seu destino é um endereço RFC 1918
* Há um NSG bloqueando a saída de sua sub-rede de integração
* Se estiver entrando no ExpressRoute ou em uma VPN, o gateway local será configurado para rotear o tráfego de volta para o Azure? Se você puder acessar pontos de extremidade em sua VNet, mas não local, isso será bom para verificar.

**Integração VNet necessária do gateway**
* é o intervalo de endereços de ponto a site nos intervalos RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* O gateway é mostrado como estando ativo no portal? Se o seu gateway estiver inativo, ative-o novamente.
* Os certificados são mostrados como estando em sincronia ou você suspeita que a configuração de rede foi alterada?  Se os certificados estiverem fora de sincronia ou se você suspeitar de que houve uma alteração na configuração da VNet que não foi sincronizada com suas ASPs, pressione "sincronizar rede".
* Se estiver entrando no ExpressRoute ou em uma VPN, o gateway local será configurado para rotear o tráfego de volta para o Azure? Se você puder acessar pontos de extremidade em sua VNet, mas não local, isso será bom para verificar.

A depuração de problemas de rede é um desafio porque não é possível ver o que está bloqueando o acesso a uma combinação de hosts específicos: porta. Algumas das causas incluem:

* Você tem um firewall em seu host que impede o acesso à porta do aplicativo do intervalo de IP ponto a site. A cruzamento de sub-redes geralmente requer acesso público.
* o host de destino está inoperante
* seu aplicativo está inoperante
* Você tinha o IP ou o nome de host incorreto
* seu aplicativo está ouvindo em uma porta diferente da que você esperava. Você pode corresponder a sua ID de processo com a porta de escuta usando "netstat-aon" no host do ponto de extremidade. 
* os grupos de segurança de rede são configurados de forma que impeçam o acesso ao host do seu aplicativo e à porta do intervalo de IP ponto a site

Lembre-se de que você não sabe qual endereço seu aplicativo realmente usará. Pode ser qualquer endereço na sub-rede de integração ou intervalo de endereços de ponto a site, portanto, você precisa permitir o acesso de todo o intervalo de endereços. 

As etapas de depuração adicionais incluem:

* Conecte-se a uma VM em sua VNet e tente acessar a porta do host de recursos: a partir daí. Para testar o acesso TCP, use o comando do PowerShell **Test-** NetConnection. A sintaxe é:

      test-netconnection hostname [optional: -Port]

* abrir um aplicativo em uma VM e testar o acesso ao host e à porta do console a partir do seu aplicativo usando o **tcpping**

#### <a name="on-premises-resources"></a>Recursos locais ####

Se seu aplicativo não puder acessar um recurso local, verifique se você pode acessar o recurso de sua VNet. Use o comando **Test-** NetConnection do PowerShell para verificar o acesso a TCP. Se sua VM não conseguir acessar o recurso local, sua conexão VPN ou ExpressRoute poderá não estar configurada corretamente.

Se a VM hospedada na VNet puder acessar seu sistema local, mas seu aplicativo não puder, a causa provavelmente será um dos seguintes motivos:

* suas rotas não estão configuradas com a sub-rede ou intervalos de endereços de ponto a site em seu gateway local
* seus grupos de segurança de rede estão bloqueando o acesso ao intervalo de IP ponto a site
* seus firewalls locais estão bloqueando o tráfego do intervalo de IP ponto a site
* Você está tentando acessar um endereço não RFC 1918 usando o recurso de integração VNet regional


## <a name="powershell-automation"></a>Automação do PowerShell

Você pode integrar o serviço de aplicativo com uma rede virtual do Azure usando o PowerShell. Para um script pronto para execução, consulte [conectar um aplicativo no serviço de Azure app a uma rede virtual do Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
