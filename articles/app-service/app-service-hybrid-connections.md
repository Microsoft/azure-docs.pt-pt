---
title: As ligações híbridas - serviço de aplicações do Azure | Documentos da Microsoft
description: Como criar e utilizar as ligações híbridas para aceder a recursos em redes diferentes
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4b125649dee51680625ac5a92b31bdc9f6830529
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069517"
---
# <a name="azure-app-service-hybrid-connections"></a>Ligações híbridas do serviço de aplicações do Azure #

As ligações híbridas são um serviço no Azure e um recurso do App Service do Azure. Como um serviço, ele tem utiliza e funcionalidades para além das que são utilizadas no serviço de aplicações. Para saber mais sobre as ligações híbridas e seu uso fora de serviço de aplicações, veja [ligações híbridas do reencaminhamento do Azure][HCService].

No serviço de aplicações, ligações híbridas pode servir para aceder aos recursos de aplicação nas outras redes. Ele fornece acesso a partir da sua aplicação para um ponto final da aplicação. Ela não permite uma capacidade alternativa aceder à sua aplicação. Como o utilizado no serviço de aplicações, cada ligação híbrida está correlacionada com uma combinação única do anfitrião e a porta TCP. Isso significa que o ponto de final de ligação híbrida pode ser em qualquer sistema operativo e qualquer aplicativo, fornecido estão a aceder a uma porta de escuta de TCP. A funcionalidade ligações híbridas não sabe nem se importa o que é o protocolo de aplicação ou o que está a aceder. Ele simplesmente está fornecendo acesso de rede.  


## <a name="how-it-works"></a>Como funciona ##
A funcionalidade ligações híbridas consiste em duas chamadas de saída para o reencaminhamento do Azure Service Bus. Existe uma ligação a partir de uma biblioteca no anfitrião onde a aplicação está em execução no serviço de aplicações. Também existe uma ligação de Gestor de ligação híbrida (HCM) para o reencaminhamento do Service Bus. O HCM é um serviço de reencaminhamento que implementar dentro da rede que aloja o recurso que está a tentar aceder. 

Por meio de duas conexões associadas, a aplicação tem um túnel TCP para uma combinação de anfitrião: porta fixa no outro lado do HCM. A ligação utiliza o TLS 1.2 para segurança e as chaves de assinatura (SAS) de acesso partilhado para autenticação e autorização.    

![Diagrama de fluxo de alto nível de ligação híbrida][1]

Quando a aplicação faz um pedido DNS que corresponde a um ponto de final de ligação híbrida configurado, o tráfego TCP de saída será redirecionado através da ligação híbrida.  

> [!NOTE]
> Isso significa que deve tentar utilizar sempre um nome DNS para a sua ligação híbrida. Algum software de cliente não faz uma pesquisa de DNS se o ponto de extremidade usa um endereço IP em vez disso.
>

### <a name="app-service-hybrid-connection-benefits"></a>Benefícios da ligação híbrida de serviço de aplicações ###

Há uma série de benefícios para a capacidade de ligações híbridas, incluindo:

- Aplicações podem aceder a serviços e sistemas no local com segurança.
- O recurso não necessita de um ponto final acessível pela internet.
- É rápido e fácil de configurar. 
- Cada ligação híbrida corresponde a uma combinação de anfitrião: porta única, útil para segurança.
- Não requer normalmente buracos de firewall. As ligações são toda a saída nas portas de web padrão.
- Uma vez que o recurso é o nível de rede, é agnóstica para o idioma utilizado pela sua aplicação e a tecnologia utilizada pelo ponto final.
- Ele pode ser usado para fornecer acesso em várias redes a partir de uma única aplicação. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Coisas que não é possível fazer com as ligações híbridas ###

Coisas que não é possível fazer com as ligações híbridas incluem:

- Monte uma unidade.
- Utilize o protocolo UDP.
- Acesso baseado em TCP serviços que utilizam portas dinâmicas, como modo passivo de FTP ou modo passivo expandido.
- Suporta o LDAP, porque ele pode exigir a UDP.
- Suporta o Active Directory, porque não é possível a associação a um domínio uma função de trabalho do serviço de aplicações.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Adicionar e criar as ligações híbridas na sua aplicação ##

Para criar uma ligação híbrida, vá para o [portal do Azure] [ portal] e selecione a sua aplicação. Selecione **Networking** > **configurar os pontos de extremidade da ligação híbrida**. Aqui pode ver as ligações híbridas que estão configurados para a sua aplicação.  

![Lista de captura de ecrã da ligação híbrida][2]

Para adicionar uma nova ligação híbrida, selecione **[+] adicionar ligação híbrida**.  Verá uma lista de ligações híbridas, que já criou. Para adicionar um ou mais dos-los à sua aplicação, selecione aqueles que pretende e, em seguida, selecione **selecionado de adicionar ligação híbrida**.  

![Portal de captura de ecrã da ligação híbrida][3]

Se quiser criar uma nova ligação híbrida, selecione **criar nova ligação híbrida**. Especifique o: 

- Nome da ligação híbrida.
- Nome de anfitrião do ponto final.
- Porta de ponto final.
- Espaço de nomes do Service Bus que pretende utilizar.

![Captura de ecrã de criar nova caixa de diálogo de ligação de híbrida][4]

Cada ligação híbrida está vinculada a um espaço de nomes do Service Bus e cada espaço de nomes do Service Bus está numa região do Azure. É importante tentar utilizar um espaço de nomes do Service Bus na mesma região que a sua aplicação, para evitar a latência de rede induzidas pelo.

Se pretende remover a ligação híbrida a partir da sua aplicação, faça duplo clique nele e selecione **desligar**.  

Quando uma ligação híbrida é adicionada à sua aplicação, pode ver os detalhes no mesmo simplesmente ao selecioná-la. 

![Detalhes da ligação de captura de ecrã de híbrida][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Criar uma ligação híbrida no portal do reencaminhamento do Azure ###

Além da experiência do portal na sua aplicação, é possível criar as ligações híbridas no portal do reencaminhamento do Azure. Para uma ligação híbrida a ser utilizado pelo serviço de aplicações, deve:

* Requer autorização do cliente.
* Tem um item de metadados, o ponto final com o nome que contenha uma combinação de anfitrião: porta como o valor.

## <a name="hybrid-connections-and-app-service-plans"></a>Planos de ligações híbridas e o serviço de aplicações ##

Ligações híbridas do serviço de aplicações só estão disponíveis em Basic, Standard, Premium e SKUs de preços isolada. Existem limites associados para o plano de preços.  

| Plano de preços | Número de ligações híbridas utilizável no plano |
|----|----|
| Básica | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolado | 200 |

O plano do serviço de aplicações da interface do Usuário mostra as ligações híbridas quantas estão a ser utilizadas e por que aplicações.  

![Propriedades do plano de captura de ecrã do serviço de aplicações][6]

Selecione a ligação híbrida para ver os detalhes. Pode ver todas as informações que viu na vista de aplicação. Também pode ver quantos outros aplicativos no mesmo plano estiver a utilizar essa ligação híbrida.

Existe um limite no número de pontos de extremidade da ligação híbrida que pode ser utilizado num plano do serviço de aplicações. Cada ligação híbrida usado, no entanto, pode ser utilizada em qualquer número de aplicações nesse plano. Por exemplo, uma ligação híbrida única que é utilizado em cinco aplicativos separados num plano do serviço de aplicações é contabilizado como uma ligação híbrida.

### <a name="pricing"></a>Preços ###

Para além da existência de um requisito de SKU do plano de serviço de aplicações, existe um custo adicional para utilizar ligações híbridas. Existe uma cobrança para cada serviço de escuta utilizado por uma ligação híbrida. O serviço de escuta é o Gestor de ligações híbridas. Se tinha cinco ligações híbridas suportado por dois gestores de ligações híbridas, que seria 10 serviços de escuta. Para obter mais informações, consulte [preços do Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Gestor de ligações híbridas ##

A funcionalidade ligações híbridas requer um agente de retransmissão na rede que aloja o ponto final de ligação híbrida. Esse agente de reencaminhamento denomina-se o Gestor de ligação híbrida (HCM). Para transferir HCM, a partir da sua aplicação no [portal do Azure][portal], selecione **redes** > **configurar os pontos de extremidade da ligação híbrida**.  

Essa ferramenta é executada no Windows Server 2012 e versões posteriores. O HCM é executado como um serviço e liga a saída para o reencaminhamento do Azure na porta 443.  

Depois de instalar HCM, pode executar HybridConnectionManagerUi.exe a utilizar a interface do Usuário para a ferramenta. Este ficheiro está no diretório de instalação do Gestor de ligações híbridas. No Windows 10, pode também simplesmente procurar *IU do Gestor de ligações híbridas* na sua caixa de pesquisa.  

![Captura de ecrã do Gestor de ligações híbridas][7]

Quando iniciar a interface do Usuário de HCM, a primeira coisa que vê é uma tabela que lista todas as ligações híbridas que estão configurados com esta instância do HCM. Se desejar fazer alterações, primeiro autenticar com o Azure. 

Para adicionar uma ou mais ligações híbridas ao seu HCM:

1. Inicie a interface do Usuário de HCM.
2. Selecione **configurar outra ligação híbrida**.
![Captura de ecrã do configurar novas ligações híbridas][8]

1. Inicie sessão com a sua conta do Azure para obter as suas ligações híbridas com as suas subscrições. O HCM não continuar a utilizar a sua conta do Azure, além disso. 
1. Escolha uma subscrição.
1. Selecione as ligações híbridas que pretende que o HCM para reencaminhamento.
![Captura de ecrã de ligações híbridas][9]

1. Selecione **Guardar**.

Agora, pode ver as ligações híbridas que adicionou. Também pode selecionar a ligação híbrida configurado para ver os detalhes.

![Captura de ecrã de detalhes da ligação híbrida][10]

Para suportar o está configurado com as ligações híbridas, HCM requer:

- Acesso TCP para o Azure através da porta 443.
- Acesso TCP para o ponto final de ligação híbrida.
- A capacidade de fazer look-ups DNS no anfitrião de ponto final e o espaço de nomes do Service Bus.

> [!NOTE]
> Reencaminhamento do Azure baseia-se na Web Sockets de conectividade. Esta funcionalidade só está disponível no Windows Server 2012 ou posterior. Por isso, HCM não é suportada em qualquer coisa anteriores ao Windows Server 2012.
>

### <a name="redundancy"></a>Redundância ###

Cada HCM pode suportar várias ligações híbridas. Além disso, qualquer ligação híbrida fornecido pode ser suportada por vários HCMs. O comportamento padrão é encaminhar o tráfego entre os HCMs configurados para qualquer ponto de extremidade. Se quiser elevada disponibilidade no seu ligações híbridas da sua rede, execute várias HCMs em computadores separados. O algoritmo de distribuição de carga utilizado pelo serviço de reencaminhamento para distribuir o tráfego para os HCMs é atribuição aleatória. 

### <a name="manually-add-a-hybrid-connection"></a>Adicionar manualmente uma ligação híbrida ###

Para ativar a alguém fora da sua subscrição para alojar uma instância HCM para uma determinada ligação híbrida, partilhe a cadeia de ligação de gateway para a ligação híbrida com eles. Pode ver a cadeia de ligação de gateway nas propriedades da ligação híbrida na [portal do Azure][portal]. Para usar essa cadeia de caracteres, selecione **introduzir manualmente** no HCM e colar na cadeia de ligação de gateway.

![Adicionar manualmente uma ligação híbrida][11]

### <a name="upgrade"></a>Atualizar ###

Há atualizações periódicas para o Gestor de ligações do híbridas para corrigir problemas ou fornecer melhorias. Quando as atualizações são lançadas, um pop-up serão apresentados na IU do HCM. Aplicar a atualização irá aplicar as alterações e reinicie o HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Adicionar uma ligação híbrida à sua aplicação através de programação ##

As APIs indicadas abaixo, pode ser utilizadas diretamente para gerir as ligações híbridas ligados às suas aplicações. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

O objeto JSON associado com uma ligação híbrida é semelhante a:

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

Uma forma de utilizar esta informação é com armclient, que pode ser obtido a partir da [ARMClient] [ armclient] projetos do GitHub. Eis um exemplo ao ligar uma ligação híbrida já existente para a sua aplicação. Crie um ficheiro JSON pelo esquema acima, como:

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

Para utilizar esta API, terá do enviar chave e o reencaminhamento do ID de recurso. Se tiver guardado suas informações com o nome de ficheiro hctest.json, emita este comando para anexar a ligação híbrida para a sua aplicação: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="troubleshooting"></a>Resolução de problemas ##

O estado de "Ligado" significa que pelo menos um HCM está configurado com essa ligação híbrida e é capaz de alcançar o Azure. Se o estado da ligação híbrida não dizer **ligado**, a ligação híbrida não está configurada em qualquer HCM que tenha acesso ao Azure.

O principal motivo pelo qual os clientes não é possível ligar ao ponto de extremidade é porque o ponto final foi especificado, utilizando um endereço IP em vez de um nome DNS. Se a sua aplicação não é possível alcançar o ponto de final desejado e utilizou um endereço IP, mude para utilizar um nome DNS que é válido no anfitrião onde o HCM está em execução. Também pode verificar que o nome DNS resolve-se corretamente no anfitrião onde o HCM está em execução. Confirme que existe conectividade do anfitrião onde o HCM está em execução para o ponto final de ligação híbrida.  

No serviço de aplicações, o **tcpping** ferramenta de linha de comandos pode ser invocada a partir da consola de ferramentas avançadas (Kudu). Essa ferramenta pode informar se tiver acesso a um ponto final TCP, mas ele não informa se tem acesso a um ponto de final de ligação híbrida. Quando utiliza a ferramenta na consola em relação a um ponto de final de ligação híbrida, apenas confirma que utiliza uma combinação de anfitrião: porta.  

Se tiver um cliente de linha de comandos para o ponto final, pode testar a conectividade a partir da consola de aplicação. Por exemplo, pode testar o acesso a pontos finais de servidor web com o curl.

## <a name="biztalk-hybrid-connections"></a>Ligações Híbridas do BizTalk ##

O formulário de início desse recurso era chamado de ligações híbridas do BizTalk. Esta capacidade passou a fim de vida de 31 de Maio de 2018 e deixou de operações. Ligações híbridas BizTalk foram removidas do todas as aplicações e não estão acessíveis através do portal ou a API. Se ainda terá essas conexões mais antigos configurados no Gestor de ligações híbridas, irá ver o estado Discontinued e apresentar uma instrução de fim da vida na parte inferior.

![Ligações híbridas do BizTalk no HCM][12]


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
