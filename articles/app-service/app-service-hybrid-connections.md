---
title: Conexões híbridas – serviço de Azure App | Microsoft Docs
description: Como criar e usar Conexões Híbridas para acessar recursos em redes diferentes
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e26cf5ede2c8884719152b6d35f1b41eb092eda6
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071805"
---
# <a name="azure-app-service-hybrid-connections"></a>Serviço de Azure App Conexões Híbridas #

Conexões Híbridas é um serviço no Azure e um recurso no serviço Azure App. Como um serviço, ele tem usos e recursos além daqueles usados no serviço de aplicativo. Para saber mais sobre Conexões Híbridas e seu uso fora do serviço de aplicativo, confira [conexões híbridas de retransmissão do Azure][HCService].

No serviço de aplicativo, Conexões Híbridas pode ser usado para acessar recursos do aplicativo em outras redes. Ele fornece acesso de seu aplicativo para um ponto de extremidade do aplicativo. Ele não permite um recurso alternativo para acessar seu aplicativo. Conforme usado no serviço de aplicativo, cada conexão híbrida se correlaciona com uma única combinação de host e porta de TCP. Isso significa que o ponto de extremidade de conexão híbrida pode estar em qualquer sistema operacional e qualquer aplicativo, desde que você esteja acessando uma porta de escuta TCP. O recurso Conexões Híbridas não conhece ou se preocupa com o que é o protocolo de aplicativo ou o que você está acessando. Ele simplesmente fornece acesso à rede.  


## <a name="how-it-works"></a>Como funciona ##
O recurso Conexões Híbridas consiste em duas chamadas de saída para a retransmissão do barramento de serviço do Azure. Há uma conexão de uma biblioteca no host onde seu aplicativo está em execução no serviço de aplicativo. Também há uma conexão do Gerenciador de Conexões Híbridas (HCM) à retransmissão do barramento de serviço. A HCM é um serviço de retransmissão que você implanta na rede que hospeda o recurso que você está tentando acessar. 

Por meio das duas conexões Unidas, seu aplicativo tem um túnel TCP para uma combinação fixa de host: porta no outro lado da HCM. A conexão usa TLS 1,2 para a segurança e chaves SAS (assinatura de acesso compartilhado) para autenticação e autorização.    

![Diagrama de fluxo de alto nível de conexão híbrida][1]

Quando seu aplicativo faz uma solicitação DNS que corresponde a um ponto de extremidade de conexão híbrida configurado, o tráfego TCP de saída será redirecionado por meio da conexão híbrida.  

> [!NOTE]
> Isso significa que você deve tentar sempre usar um nome DNS para sua conexão híbrida. Um software cliente não fará uma pesquisa de DNS se o ponto de extremidade usar um endereço IP em vez disso.
>

### <a name="app-service-hybrid-connection-benefits"></a>Benefícios da conexão híbrida do serviço de aplicativo ###

Há vários benefícios para o recurso de Conexões Híbridas, incluindo:

- Os aplicativos podem acessar sistemas e serviços locais com segurança.
- O recurso não requer um ponto de extremidade acessível pela Internet.
- É rápido e fácil de configurar. 
- Cada conexão híbrida corresponde a uma única combinação de host: porta, útil para segurança.
- Normalmente, ele não requer brechas de firewall. As conexões são todas de saída por meio de portas da Web padrão.
- Como o recurso é de nível de rede, ele é independente do idioma usado pelo seu aplicativo e da tecnologia usada pelo ponto de extremidade.
- Ele pode ser usado para fornecer acesso em várias redes de um único aplicativo. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Coisas que você não pode fazer com Conexões Híbridas ###

As coisas que você não pode fazer com Conexões Híbridas incluem:

- Montar uma unidade.
- Use UDP.
- Acesse serviços baseados em TCP que usam portas dinâmicas, como o modo passivo FTP ou o modo passivo estendido.
- Suporte ao LDAP, pois ele pode exigir UDP.
- Suporte a Active Directory, porque não é possível ingressar no domínio em um trabalho do serviço de aplicativo.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Adicionar e criar Conexões Híbridas em seu aplicativo ##

Para criar uma conexão híbrida, vá para a [portal do Azure][portal] e selecione seu aplicativo. Selecione **rede** > **configurar seus pontos de extremidade de conexão híbrida**. Aqui você pode ver as Conexões Híbridas que estão configuradas para seu aplicativo.  

![Captura de tela da lista de conexões híbridas][2]

Para adicionar uma nova conexão híbrida, selecione **[+] Adicionar conexão híbrida**.  Você verá uma lista do Conexões Híbridas que você já criou. Para adicionar um ou mais deles ao seu aplicativo, selecione aqueles que você deseja e, em seguida, selecione **Adicionar conexão híbrida selecionada**.  

![Captura de tela do portal de conexão híbrida][3]

Se você quiser criar uma nova conexão híbrida, selecione **criar nova conexão híbrida**. Especifique: 

- Nome da conexão híbrida.
- Nome do host do ponto de extremidade.
- Porta do ponto de extremidade.
- Namespace do barramento de serviço que você deseja usar.

![Captura de tela da caixa de diálogo Criar nova conexão híbrida][4]

Cada conexão híbrida é vinculada a um namespace do barramento de serviço e cada namespace do barramento de serviço está em uma região do Azure. É importante tentar usar um namespace do barramento de serviço na mesma região que seu aplicativo, para evitar a latência de rede induzida.

Se você quiser remover sua conexão híbrida do seu aplicativo, clique com o botão direito do mousenela e selecione desconectar.  

Quando uma conexão híbrida é adicionada ao seu aplicativo, você pode ver detalhes sobre ela simplesmente selecionando-a. 

![Captura de tela de detalhes de conexões híbridas][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Criar uma conexão híbrida no portal de retransmissão do Azure ###

Além da experiência do portal de dentro de seu aplicativo, você pode criar Conexões Híbridas de dentro do portal de retransmissão do Azure. Para que uma conexão híbrida seja usada pelo serviço de aplicativo, ela deve:

* Exigir autorização do cliente.
* Ter um item de metadados, chamado ponto de extremidade, que contém uma combinação de host: porta como o valor.

## <a name="hybrid-connections-and-app-service-plans"></a>Conexões Híbridas e planos do serviço de aplicativo ##

Os Conexões Híbridas do serviço de aplicativo estão disponíveis apenas nas SKUs de preços Basic, Standard, Premium e Isolated. Há limites vinculados ao plano de preços.  

| Plano de preços | Número de Conexões Híbridas utilizáveis no plano |
|----|----|
| Básica | 5 |
| Standard | 25 |
| Premium | 200 |
| Plano Isolado | 200 |

A interface do usuário do plano do serviço de aplicativo mostra quantas Conexões Híbridas estão sendo usadas e por quais aplicativos.  

![Captura de tela das propriedades do plano do serviço de aplicativo][6]

Selecione a conexão híbrida para ver os detalhes. Você pode ver todas as informações vistas na exibição do aplicativo. Você também pode ver quantos outros aplicativos no mesmo plano estão usando essa conexão híbrida.

Há um limite no número de pontos de extremidade de conexão híbrida que podem ser usados em um plano do serviço de aplicativo. No entanto, cada conexão híbrida usada pode ser usada em qualquer número de aplicativos nesse plano. Por exemplo, uma única conexão híbrida que é usada em cinco aplicativos separados em um plano do serviço de aplicativo conta como uma conexão híbrida.

### <a name="pricing"></a>Preços ###

Além de haver um requisito de SKU do plano do serviço de aplicativo, há um custo adicional para usar Conexões Híbridas. Há um encargo para cada ouvinte usado por uma conexão híbrida. O ouvinte é o Gerenciador de Conexões Híbridas. Se você tiver cinco Conexões Híbridas com suporte de dois gerenciadores de conexões híbridas, seriam 10 ouvintes. Para obter mais informações, consulte [preços do barramento de serviço][sbpricing].

## <a name="hybrid-connection-manager"></a>Gerenciador de Conexões Híbridas ##

O recurso Conexões Híbridas requer um agente de retransmissão na rede que hospeda seu ponto de extremidade de conexão híbrida. Esse agente de retransmissão é chamado de Gerenciador de Conexões Híbridas (HCM). Para baixar a HCM, em seu aplicativo na [portal do Azure][portal], selecione **rede** > **configurar seus pontos de extremidade de conexão híbrida**.  

Essa ferramenta é executada no Windows Server 2012 e posterior. A HCM é executada como um serviço e conecta a saída à retransmissão do Azure na porta 443.  

Depois de instalar o HCM, você pode executar o HybridConnectionManagerUi. exe para usar a interface do usuário para a ferramenta. Esse arquivo está no diretório de instalação do Gerenciador de Conexões Híbridas. No Windows 10, você também pode apenas procurar *Gerenciador de conexões híbridas interface do usuário* na caixa de pesquisa.  

![Captura de tela de Gerenciador de Conexões Híbridas][7]

Quando você inicia a interface do usuário do HCM, a primeira coisa que você vê é uma tabela que lista todos os Conexões Híbridas configurados com essa instância da HCM. Se você quiser fazer alterações, primeiro autentique com o Azure. 

Para adicionar um ou mais Conexões Híbridas à HCM:

1. Inicie a interface do usuário do HCM.
2. Selecione **Configurar outra conexão híbrida**.
![Captura de tela de configurar novo Conexões Híbridas][8]

1. Entre com sua conta do Azure para colocar seu Conexões Híbridas disponível com suas assinaturas. A HCM não continua a usar sua conta do Azure além disso. 
1. Escolha uma assinatura.
1. Selecione o Conexões Híbridas que você deseja que o HCM retransmita.
![Captura de tela de Conexões Híbridas][9]

1. Selecione **Guardar**.

Agora você pode ver o Conexões Híbridas que você adicionou. Você também pode selecionar a conexão híbrida configurada para ver detalhes.

![Captura de tela de detalhes de conexão híbrida][10]

Para dar suporte ao Conexões Híbridas ele está configurado com, HCM requer:

- Acesso TCP ao Azure pela porta 443.
- Acesso TCP ao ponto de extremidade de conexão híbrida.
- A capacidade de fazer buscas de DNS no host do ponto de extremidade e no namespace do barramento de serviço.

> [!NOTE]
> A retransmissão do Azure depende de Web Sockets para conectividade. Esse recurso só está disponível no Windows Server 2012 ou posterior. Por isso, a HCM não tem suporte em nada anterior ao Windows Server 2012.
>

### <a name="redundancy"></a>Redundância ###

Cada HCM pode dar suporte a vários Conexões Híbridas. Além disso, qualquer conexão híbrida fornecida pode ser suportada por vários HCMs. O comportamento padrão é rotear o tráfego entre o HCMs configurado para qualquer ponto de extremidade específico. Se você quiser alta disponibilidade em seu Conexões Híbridas de sua rede, execute vários HCMs em computadores separados. O algoritmo de distribuição de carga usado pelo serviço de retransmissão para distribuir o tráfego para o HCMs é uma atribuição aleatória. 

### <a name="manually-add-a-hybrid-connection"></a>Adicionar uma conexão híbrida manualmente ###

Para permitir que alguém fora de sua assinatura hospede uma instância HCM para uma determinada conexão híbrida, compartilhe a cadeia de conexão do gateway para a conexão híbrida com elas. Você pode ver a cadeia de conexão do gateway nas propriedades de conexão híbrida no [portal do Azure][portal]. Para usar essa cadeia de caracteres, selecione **inserir manualmente** na HCM e cole na cadeia de conexão do gateway.

![Adicionar uma conexão híbrida manualmente][11]

### <a name="upgrade"></a>Actualizar ###

Há atualizações periódicas para a Gerenciador de Conexões Híbridas corrigir problemas ou fornecer melhorias. Quando as atualizações forem liberadas, um pop-up será exibido na interface do usuário da HCM. A aplicação da atualização aplicará as alterações e reiniciará a HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Adicionando uma conexão híbrida ao seu aplicativo de forma programática ##

As APIs indicadas abaixo podem ser usadas diretamente para gerenciar o Conexões Híbridas conectado aos seus aplicativos. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

O objeto JSON associado a uma conexão híbrida é semelhante a:

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

Uma maneira de usar essas informações é com o armclient, que pode ser obtido do projeto GitHub do [armclient][armclient] . Aqui está um exemplo de como anexar uma conexão híbrida pré-existente ao seu aplicativo. Crie um arquivo JSON de acordo com o esquema acima, como:

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

Para usar essa API, você precisa da ID de recurso Enviar chave e retransmissão. Se você salvou suas informações com o nome de arquivo hctest. JSON, emita este comando para anexar sua conexão híbrida ao seu aplicativo: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="troubleshooting"></a>Resolução de problemas ##

O status de "conectado" significa que pelo menos um HCM está configurado com essa conexão híbrida e é capaz de alcançar o Azure. Se o status de sua conexão híbrida não disser **conectado**, sua conexão híbrida não será configurada em nenhuma HCM que tenha acesso ao Azure.

O principal motivo pelo qual os clientes não podem se conectar ao ponto de extremidade é porque o ponto de extremidade foi especificado usando um endereço IP em vez de um nome DNS. Se seu aplicativo não puder alcançar o ponto de extremidade desejado e você tiver usado um endereço IP, alterne para o usando um nome DNS que seja válido no host onde o HCM está em execução. Verifique também se o nome DNS é resolvido corretamente no host onde o HCM está em execução. Confirme se há conectividade do host onde o HCM está em execução no ponto de extremidade de conexão híbrida.  

No serviço de aplicativo, a ferramenta de linha de comando **tcpping** pode ser invocada no console de ferramentas avançadas (kudu). Essa ferramenta pode informar se você tem acesso a um ponto de extremidade TCP, mas não informa se você tem acesso a um ponto de extremidade de conexão híbrida. Ao usar a ferramenta no console do em um ponto de extremidade de conexão híbrida, você só confirma que ela usa uma combinação de host: porta.  

Se você tiver um cliente de linha de comando para seu ponto de extremidade, poderá testar a conectividade no console do aplicativo. Por exemplo, você pode testar o acesso a pontos de extremidade do servidor Web usando a ondulação.

## <a name="biztalk-hybrid-connections"></a>Ligações Híbridas do BizTalk ##

A forma antecipada desse recurso foi chamada BizTalk Conexões Híbridas. Esse recurso saiu do fim de vida em 31 de maio de 2018 e as operações cessaram. As conexões híbridas do BizTalk foram removidas de todos os aplicativos e não estão acessíveis por meio do portal ou da API. Se você ainda tiver essas conexões mais antigas configuradas no Gerenciador de Conexões Híbridas, verá um status de descontinuado e exibirá uma instrução de fim de vida na parte inferior.

![Conexões Híbridas do BizTalk na HCM][12]


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
