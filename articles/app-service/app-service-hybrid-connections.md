---
title: Ligações híbridas
description: Aprenda a criar e utilizar ligações híbridas no Serviço de Aplicações Azure para aceder a recursos em redes díspares.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: ec842530f3cae26b869a649617f279d204b98fcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047782"
---
# <a name="azure-app-service-hybrid-connections"></a>Conexões híbridas do serviço de aplicações Azure

A Hybrid Connections é simultaneamente um serviço em Azure e uma funcionalidade no Azure App Service. Como serviço, tem utilizações e capacidades para além das que são usadas no Serviço de Aplicações. Para saber mais sobre ligações híbridas e o seu uso fora do App Service, consulte [Ligações Híbridas Azure Relay][HCService].

Dentro do Serviço de Aplicações, as Ligações Híbridas podem ser usadas para aceder aos recursos de aplicações noutras redes. Fornece acesso da sua app a um ponto final de aplicação. Não permite uma capacidade alternativa de acesso à sua aplicação. Como utilizado no Serviço de Aplicações, cada Ligação Híbrida correlaciona-se com um único hospedeiro TCP e uma combinação de porta. Isto significa que o ponto final da Ligação Híbrida pode estar em qualquer sistema operativo e qualquer aplicação, desde que esteja a aceder a uma porta de escuta TCP. A funcionalidade Conexões Híbridas não sabe nem se importa com o protocolo de aplicação ou no que está a aceder. Está simplesmente a fornecer acesso à rede.  


## <a name="how-it-works"></a>Como funciona ##
A função Ligações Híbridas consiste em duas chamadas de saída para o Azure Service Bus Relay. Existe uma ligação a partir de uma biblioteca no anfitrião onde a sua aplicação está a funcionar no Serviço de Aplicações. Existe também uma ligação entre o Hybrid Connection Manager (HCM) e o Service Bus Relay. O HCM é um serviço de retransmissão que implementa dentro da rede que acolhe o recurso a que está a tentar aceder. 

Através das duas ligações unidas, a sua aplicação tem um túnel TCP para uma combinação fixa de hospedeiro:porta do outro lado do HCM. A ligação utiliza chaves TLS 1.2 para segurança e assinatura de acesso partilhado (SAS) para autenticação e autorização.    

![Diagrama de conexão híbrida fluxo de alto nível][1]

Quando a sua aplicação fizer um pedido dNS que corresponda a um ponto final de Ligação Híbrida configurado, o tráfego de TCP de saída será redirecionado através da Ligação Híbrida.  

> [!NOTE]
> Isto significa que deve tentar sempre usar um nome DNS para a sua Ligação Híbrida. Alguns softwares de cliente não fazem uma procura de DNS se o ponto final usar um endereço IP em vez disso. 
>

### <a name="app-service-hybrid-connection-benefits"></a>Benefícios de conexão híbrida do serviço de aplicação ###

Existem uma série de benefícios para a capacidade de Conexões Híbridas, incluindo:

- As aplicações podem aceder a sistemas e serviços no local de forma segura.
- A funcionalidade não requer um ponto final acessível à Internet.
- É rápido e fácil de configurar. 
- Cada Ligação Híbrida corresponde a uma única combinação de hospedeiro:porta, útil para a segurança.
- Normalmente não requer buracos de firewall. As ligações estão todas saídas sobre portas web padrão.
- Como a funcionalidade é ao nível da rede, é agnóstica ao idioma utilizado pela sua app e à tecnologia utilizada pelo ponto final.
- Pode ser usado para fornecer acesso em várias redes a partir de uma única aplicação. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Coisas que não pode fazer com Ligações Híbridas ###

As coisas que não pode fazer com Ligações Híbridas incluem:

- Monte uma unidade.
- Use UDP.
- Aceda a serviços baseados em TCP que utilizem portas dinâmicas, como o Modo Passivo FTP ou o Modo Passivo Estendido.
- Suporte LDAP, porque pode exigir UDP.
- Apoie o Diretório Ativo, porque não pode integrar um trabalhador do Serviço de Aplicações.

### <a name="prerequisites"></a>Pré-requisitos ###
 - O serviço de Aplicações Windows é necessário. Só está disponível no Windows.  

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Adicione e crie ligações híbridas na sua aplicação ##

Para criar uma Ligação Híbrida, vá ao [portal Azure][portal] e selecione a sua aplicação. Selecione **Configurar a** > **rede dos seus pontos finais de ligação híbrida**. Aqui pode ver as Conexões Híbridas que estão configuradas para a sua aplicação.  

![Screenshot da lista de ligação híbrida][2]

Para adicionar uma nova Ligação Híbrida, selecione **[+] Adicionar ligação híbrida**.  Verá uma lista das Conexões Híbridas que já criou. Para adicionar um ou mais deles à sua aplicação, selecione os que deseja e, em seguida, selecione **Adicionar Ligação Híbrida selecionada**.  

![Screenshot do portal de conexão híbrida][3]

Se quiser criar uma nova Ligação Híbrida, selecione **Criar uma nova ligação híbrida**. Especificar: 

- Nome de ligação híbrida.
- Nome de anfitrião endpoint.
- Porta endpoint.
- Service Bus espaço de nome que deseja utilizar.

![Screenshot de Criar nova caixa de diálogo de ligação híbrida][4]

Cada Ligação Híbrida está ligada a um espaço de nome de ônibus de serviço, e cada espaço de nome de ônibus de serviço está em uma região de Azure. É importante tentar usar um espaço de nome service Bus na mesma região que a sua aplicação, para evitar a latência induzida pela rede.

Se pretender remover a sua Ligação Híbrida da sua aplicação, clique-a à direita e selecione **Desligar**.  

Quando uma Ligação Híbrida é adicionada à sua aplicação, pode ver detalhes sobre ele simplesmente selecionando-a. 

![Screenshot dos detalhes das ligações híbridas][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Criar uma conexão híbrida no portal Azure Relay ###

Além da experiência do portal dentro da sua app, pode criar Conexões Híbridas a partir do portal Azure Relay. Para que uma Ligação Híbrida seja utilizada pelo Serviço de Aplicações, deve:

* Requerem autorização do cliente.
* Tenha um item de metadados, chamado ponto final, que contenha uma combinação hospedeira:porta como o valor.

## <a name="hybrid-connections-and-app-service-plans"></a>Planos de Conexões Híbridas e Serviço de Aplicações ##

As ligações híbridas do serviço de aplicações só estão disponíveis em SKUs de preços básicos, standard, premium e isolados. Há limites ligados ao plano de preços.  

| Plano de preços | Número de Conexões Híbridas utilizáveis no plano |
|----|----|
| Básico | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolado | 200 |

O plano de serviço de aplicações UI mostra-lhe quantas Ligações Híbridas estão a ser usadas e por que aplicações.  

![Screenshot das propriedades do plano do app service][6]

Selecione a Ligação Híbrida para ver detalhes. Pode ver toda a informação que viu na vista da aplicação. Também pode ver quantas outras aplicações no mesmo plano estão a usar essa Ligação Híbrida.

Existe um limite para o número de pontos finais de Ligação Híbrida que podem ser usados num plano de Serviço de Aplicações. Cada Ligação Híbrida utilizada, no entanto, pode ser usada em várias aplicações nesse plano. Por exemplo, uma única Ligação Híbrida que é usada em cinco aplicações separadas num plano de Serviço de Aplicações conta como uma Ligação Híbrida.

### <a name="pricing"></a>Preços ###

Além de existir um plano de serviço de aplicação SKU, existe um custo adicional para a utilização de Ligações Híbridas. Há uma taxa para cada ouvinte usado por uma Conexão Híbrida. O ouvinte é o Gestor de Ligação Híbrida. Se tivessecinco Ligações Híbridas apoiadas por dois Gestores de Conexão Híbrida, seriam 10 ouvintes. Para mais informações, consulte os preços dos [autocarros de serviço.][sbpricing]

## <a name="hybrid-connection-manager"></a>Gestor de Conexão Híbrida ##

A função Ligações Híbridas requer um agente de retransmissão na rede que acolhe o seu ponto final de Ligação Híbrida. Este agente de retransmissão chama-se Hybrid Connection Manager (HCM). Para baixar o HCM, a partir da sua aplicação no [portal Azure,][portal]selecione **Networking** > **Configure os seus pontos finais de Ligação Híbrida**.  

Esta ferramenta funciona no Windows Server 2012 e posteriormente. O HCM funciona como um serviço e liga a saída para o Azure Relay na porta 443.  

Depois de instalar o HCM, pode executar o HybridConnectionManagerUi.exe para utilizar o UI para a ferramenta. Este ficheiro está no diretório de instalação do Hybrid Connection Manager. No Windows 10, também pode pesquisar o *Hybrid Connection Manager UI* na sua caixa de pesquisa.  

![Screenshot do Gestor de Conexão Híbrida][7]

Quando inicia o HCM UI, a primeira coisa que vê é uma tabela que lista todas as Conexões Híbridas que estão configuradas com esta instância do HCM. Se quiser fazer alterações, autente primeiro com azure. 

Para adicionar uma ou mais Ligações Híbridas ao seu HCM:

1. Inicie a HCM UI.
2. **Selecione Configurar outra Ligação Híbrida**.
![Screenshot de Configurar novas conexões híbridas][8]

1. Inscreva-se na sua conta Azure para obter as suas Ligações Híbridas disponíveis com as suas subscrições. O HCM não continua a utilizar a sua conta Azure para além disso. 
1. Escolha uma subscrição.
1. Selecione as Ligações Híbridas que pretende que o HCM retransmita.
![Screenshot de conexões híbridas][9]

1. Selecione **Guardar**.

Agora pode ver as Conexões Híbridas que adicionou. Também pode selecionar a Ligação Híbrida configurada para ver detalhes.

![Screenshot dos detalhes da conexão híbrida][10]

Para suportar as Ligações Híbridas com as que está configurada, o HCM requer:

- Acesso tCP a Azure sobre o porto 443.
- Acesso tCP ao ponto final da Ligação Híbrida.
- A capacidade de fazer os look-ups DNS no anfitrião do ponto final e no espaço de nome do Ônibus de serviço.

> [!NOTE]
> O Azure Relay conta com tomadas Web para conectividade. Esta capacidade só está disponível no Windows Server 2012 ou posteriormente. Por isso, o HCM não é suportado em nada mais cedo do que o Windows Server 2012.
>

### <a name="redundancy"></a>Redundância ###

Cada HCM pode suportar múltiplas Ligações Híbridas. Além disso, qualquer ligação híbrida dada pode ser suportada por vários HCMs. O comportamento padrão é direcionar o tráfego através dos HCMs configurados para qualquer ponto final. Se pretender uma grande disponibilidade nas suas Ligações Híbridas a partir da sua rede, execute vários HCMs em máquinas separadas. O algoritmo de distribuição de carga utilizado pelo serviço Relay para distribuir tráfego para os HCMs é uma atribuição aleatória. 

### <a name="manually-add-a-hybrid-connection"></a>Adicione manualmente uma Ligação Híbrida ###

Para permitir que alguém fora da sua subscrição apresente uma instância HCM para uma determinada Ligação Híbrida, partilhe com eles a cadeia de ligação gateway para a Ligação Híbrida. Pode ver a cadeia de ligação gateway nas propriedades de Ligação Híbrida no [portal Azure][portal]. Para utilizar esta corda, selecione **Enter Manualmente** no HCM e cola na cadeia de ligação gateway.

![Adicione manualmente uma Ligação Híbrida][11]

### <a name="upgrade"></a>Atualizar ###

Existem atualizações periódicas para o Hybrid Connection Manager para corrigir problemas ou fornecer melhorias. Quando as atualizações forem lançadas, aparecerá um popup no UI HCM. A aplicação da atualização aplicará as alterações e reiniciará o HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Adicionar uma Ligação Híbrida à sua aplicação programática ##

As APIs a seguir podem ser utilizadas diretamente para gerir as Ligações Híbridas ligadas às suas apps. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

O objeto JSON associado a uma Conexão Híbrida parece:

    {
      "name": "[hybrid connection name]",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "[location]",
      "properties": {
        "serviceBusNamespace": "[namespace name]",
        "relayName": "[hybrid connection name]",
        "relayArmUri": "/subscriptions/[subscription id]/resourceGroups/[resource group name]/providers/Microsoft.Relay/namespaces/[namespace name]/hybridconnections/[hybrid connection name]",
        "hostName": "[endpoint host name]",
        "port": [port],
        "sendKeyName": "defaultSender",
        "sendKeyValue": "[send key]"
      }
    }

Uma forma de usar esta informação é com o armclient, que pode obter do projeto [ARMClient][armclient] GitHub. Aqui está um exemplo na anexação de uma Ligação Híbrida pré-existente à sua aplicação. Crie um ficheiro JSON por um esquema acima como:

    {
      "name": "relay-demo-hc",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "North Central US",
      "properties": {
        "serviceBusNamespace": "demo-relay",
        "relayName": "relay-demo-hc",
        "relayArmUri": "/subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myrelay-rg/providers/Microsoft.Relay/namespaces/demo-relay/hybridconnections/relay-demo-hc",
        "hostName": "my-wkstn.home",
        "port": 1433,
        "sendKeyName": "defaultSender",
        "sendKeyValue": "Th9is3is8a82lot93of3774stu887ff122235="
      }
    }

Para utilizar esta API, precisa da chave de envio e do ID de recurso de retransmissão. Se guardou as suas informações com o nome de ficheiro hctest.json, emita este comando para anexar a sua Ligação Híbrida à sua aplicação: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="secure-your-hybrid-connections"></a>Proteja as suas Conexões Híbridas ##

Uma conexão híbrida existente pode ser adicionada a outras Aplicações Web do Serviço de Aplicações por qualquer utilizador que tenha permissões suficientes no relé de ônibus de serviço azure subjacente. Isto significa que se tiver de impedir que outros reutilizem essa mesma Ligação Híbrida (por exemplo, quando o recurso-alvo é um serviço que não tenha quaisquer medidas de segurança adicionais em vigor para impedir o acesso não autorizado), deve bloquear o acesso ao Azure Retransmissão de ônibus de serviço.

Qualquer `Reader` pessoa com acesso ao Retransmissor poderá _ver_ a Ligação Híbrida ao tentar adicioná-la à sua Web App no Portal Azure, mas não poderá _adicioná-la_ por não ter permissão para recuperar a cadeia de ligação que é utilizada para estabelecer a ligação ao relé. Para adicionar com sucesso a Ligação Híbrida, devem ter a `listKeys` permissão (`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`). A `Contributor` função ou qualquer outra função que inclua esta permissão no Retransmissor permitirá que os utilizadores utilizem a Conexão Híbrida e adicioná-la às suas próprias Aplicações Web.

## <a name="troubleshooting"></a>Resolução de problemas ##

O estado de "Conectado" significa que pelo menos um HCM está configurado com essa Ligação Híbrida, e é capaz de chegar ao Azure. Se o estado da sua Ligação Híbrida não disser **Connected,** a sua Ligação Híbrida não está configurada em nenhum HCM que tenha acesso ao Azure.

A principal razão pela qual os clientes não conseguem ligar-se ao seu ponto final é porque o ponto final foi especificado utilizando um endereço IP em vez de um nome DNS. Se a sua aplicação não conseguir atingir o ponto final desejado e tiver utilizado um endereço IP, mude para utilizar um nome DNS válido no hospedeiro onde o HCM está em execução. Verifique também se o nome DNS se resolve corretamente no hospedeiro onde o HCM está em funcionamento. Confirme que existe conectividade do hospedeiro onde o HCM está a correr até ao ponto final da Ligação Híbrida.  

No Serviço de Aplicações, a ferramenta de linha de comando **tcpping** pode ser invocada a partir da consola Advanced Tools (Kudu). Esta ferramenta pode dizer-lhe se tem acesso a um ponto final de TCP, mas não lhe diz se tem acesso a um ponto final de Ligação Híbrida. Quando utilizar a ferramenta na consola contra um ponto final de Ligação Híbrida, está apenas a confirmar que utiliza uma combinação de hospedeiro:porta.  

Se tiver um cliente de linha de comando para o seu ponto final, pode testar a conectividade a partir da consola de aplicações. Por exemplo, pode testar o acesso aos pontos finais do servidor web utilizando caracóis.

## <a name="biztalk-hybrid-connections"></a>Conexões Híbridas BizTalk ##

A forma inicial desta funcionalidade chamava-se BizTalk Hybrid Connections. Esta capacidade foi final de vida em 31 de maio de 2018 e cessou as suas operações. As ligações híbridas BizTalk foram removidas de todas as aplicações e não são acessíveis através do portal ou DaPI. Se ainda tiver estas ligações mais antigas configuradas no Hybrid Connection Manager, verá um estado de Descontinuação e apresentará uma declaração de Fim de Vida na parte inferior.

![Ligações Híbridas BizTalk no HCM][12]


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: https://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: https://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
