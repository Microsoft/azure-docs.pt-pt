---
title: Ligações híbridas
description: Saiba como criar e utilizar ligações híbridas no Azure App Service para aceder a recursos em redes diferentes.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/08/2020
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: c7900341cfcda52dafcb269b833f5ad8dd0f55b7
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92739766"
---
# <a name="azure-app-service-hybrid-connections"></a>Ligações Híbridas do Serviço de Aplicações do Azure

Hybrid Connections é simultaneamente um serviço no Azure e uma funcionalidade no Azure App Service. Como serviço, tem utilizações e capacidades para além daquelas que são usadas no Serviço de Aplicações. Para saber mais sobre conexões híbridas e a sua utilização fora do Serviço de [Aplicações, consulte as Ligações Híbridas Azure Relay.][HCService]

Dentro do Serviço de Aplicações, as Ligações Híbridas podem ser usadas para aceder a recursos de aplicações em qualquer rede que possa fazer chamadas de saída para a Azure sobre a porta 443. A Hybrid Connections fornece acesso da sua app a um ponto final TCP e não permite uma nova forma de aceder à sua aplicação. Como usado no Serviço de Aplicações, cada Ligação Híbrida está relacionada com um único hospedeiro TCP e combinação de porta. Isto permite que as suas aplicações acedam a recursos em qualquer SISTEMA, desde que seja um ponto final TCP. A funcionalidade Ligações Híbridas não sabe nem se importa com o protocolo de aplicação ou com o que está a aceder. Simplesmente fornece acesso à rede.  

## <a name="how-it-works"></a>Como funciona ##
As Ligações Híbridas requerem que seja implantado um agente de retransmissão onde possa atingir tanto o ponto final pretendido como o Azure. O agente de retransmissão, Hybrid Connection Manager (HCM), chama a Azure Relay sobre a porta 443. A partir do site de aplicações, a infraestrutura do Serviço de Aplicações também se conecta ao Azure Relay em nome da sua aplicação. Através das ligações juntas, a sua aplicação é capaz de aceder ao ponto final pretendido. A ligação utiliza as chaves TLS 1.2 para segurança e assinatura de acesso partilhado (SAS) para autenticação e autorização.    

![Diagrama de ligação híbrida fluxo de alto nível][1]

Quando a sua aplicação fizer um pedido de DNS que corresponda a um ponto final de ligação híbrido configurado, o tráfego TCP de saída será redirecionado através da Ligação Híbrida.  

> [!NOTE]
> Isto significa que deve tentar sempre usar um nome DNS para a sua Ligação Híbrida. Alguns softwares de clientes não fazem uma procura de DNS se o ponto final utilizar um endereço IP. 
>

### <a name="app-service-hybrid-connection-benefits"></a>Benefícios de conexão híbrida do serviço de aplicações ###

Existem vários benefícios para a capacidade de conexões híbridas, incluindo:

- As aplicações podem aceder em segurança aos sistemas e serviços no local.
- A funcionalidade não requer um ponto final acessível à Internet.
- É rápido e fácil de configurar. Não são necessários portais
- Cada Ligação Híbrida corresponde a uma única combinação de anfitrião:porta, útil para a segurança.
- Normalmente não requer buracos de firewall. As ligações são todas de saída sobre portas web padrão.
- Como a funcionalidade é de rede, é agnóstico ao idioma utilizado pela sua app e à tecnologia utilizada pelo ponto final.
- Pode ser usado para fornecer acesso em várias redes a partir de uma única aplicação. 
- É suportado em GA para aplicações nativas do Windows e está em pré-visualização para aplicações Linux. Não é suportado para aplicações de contentores Windows.

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Coisas que não pode fazer com ligações híbridas ###

As coisas que não pode fazer com ligações híbridas incluem:

- Monte uma unidade.
- Use UDP.
- Aceda a serviços baseados em TCP que utilizem portas dinâmicas, como o Modo Passivo FTP ou o Modo Passivo Alargado.
- Apoiar lDAP, porque pode requerer UDP.
- Suporte Ative Directory, porque não pode fazer o domínio de um trabalhador do Serviço de Aplicações. 

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Adicione e crie conexões híbridas na sua aplicação ##

Para criar uma Ligação Híbrida, vá ao [portal Azure][portal] e selecione a sua aplicação. **Selecione Configurar a rede**  >  **configurar os seus pontos finais de ligação híbrida** . Aqui pode ver as Conexões Híbridas que estão configuradas para a sua aplicação.  

![Screenshot da lista de conexão híbrida][2]

Para adicionar uma nova Ligação Híbrida, selecione **[+] Adicionar ligação híbrida** .  Verá uma lista das Ligações Híbridas que já criou. Para adicionar um ou mais deles à sua aplicação, selecione as que pretende e, em seguida, **selecione Adicionar a Ligação Híbrida selecionada** .  

![Screenshot do portal De Ligação Híbrida][3]

Se pretender criar uma nova Ligação Híbrida, **selecione Criar uma nova ligação híbrida** . Especificar: 

- Nome de ligação híbrida.
- Nome de anfitrião de ponto final.
- Porta de ponto final.
- Serviço Espaço de nomes de ônibus que você deseja usar.

![Screenshot da Criação de nova caixa de diálogo de ligação híbrida][4]

Cada Conexão Híbrida está ligada a um espaço de nomes de ônibus de serviço, e cada espaço de nome de Service Bus está numa região de Azure. É importante tentar usar um espaço de nome de Service Bus na mesma região que a sua aplicação, para evitar a latência induzida pela rede.

Se pretender remover a sua Ligação Híbrida da sua aplicação, clique com o botão direito e selecione **Disconnect** .  

Quando uma Ligação Híbrida é adicionada à sua aplicação, pode ver detalhes sobre ela simplesmente selecionando-o. 

![Screenshot dos detalhes das ligações híbridas][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Criar uma ligação híbrida no portal Azure Relay ###

Além da experiência do portal dentro da sua app, pode criar Conexões Híbridas a partir do portal Azure Relay. Para que uma ligação híbrida seja utilizada pelo Serviço de Aplicações, deve:

* Requer autorização do cliente.
* Tenha um item de metadados, nomeado ponto final, que contenha uma combinação host:port como o valor.

## <a name="hybrid-connections-and-app-service-plans"></a>Conexões Híbridas e planos de Serviço de Aplicações ##

As conexões híbridas do Serviço de Aplicações só estão disponíveis em SKUs de preços básicos, standard, premium e isolados. Há limites ligados ao plano de preços.  

| Plano de preços | Número de Ligações Híbridas utilizáveis no plano |
|----|----|
| Básico | 5 por plano |
| Standard | 25 por plano |
| PremiumV2 | 200 por app |
| Isolado | 200 por app |

O plano de Serviço de Aplicações UI mostra-lhe quantas Ligações Híbridas estão a ser usadas e por que aplicações.  

![Screenshot das propriedades do plano de app service][6]

Selecione a Ligação Híbrida para ver detalhes. Pode ver toda a informação que viu na vista da aplicação. Também pode ver quantas outras aplicações no mesmo plano estão a usar essa Ligação Híbrida.

Existe um limite para o número de pontos finais de Conexão Híbrida que podem ser usados num plano de Serviço de Aplicações. Cada Ligação Híbrida utilizada, no entanto, pode ser usada em qualquer número de aplicações nesse plano. Por exemplo, uma única Ligação Híbrida que é usada em cinco aplicações separadas num plano de Serviço de Aplicações conta como uma Ligação Híbrida.

### <a name="pricing"></a>Preços ###

Além de existir um requisito de SKU do plano de serviço de aplicações, existe um custo adicional para a utilização de Conexões Híbridas. Há uma taxa para cada ouvinte usado por uma Ligação Híbrida. O ouvinte é o Gestor de Ligação Híbrida. Se tivesses cinco Conexões Híbridas apoiadas por dois Gestores de Conexão Híbrida, seriam 10 ouvintes. Para mais informações, consulte [os preços do Service Bus.][sbpricing]

## <a name="hybrid-connection-manager"></a>Gestor de Conexão Híbrida ##

A função Ligações Híbridas requer um agente de retransmissão na rede que hospeda o seu ponto de terminação de Ligação Híbrida. Este agente de retransmissão chama-se Gestor de Ligação Híbrida (HCM). Para baixar o HCM, a partir da sua aplicação no [portal Azure,][portal]selecione **Networking**  >  **Configure seus pontos finais de Ligação Híbrida** .  

Esta ferramenta é executado no Windows Server 2012 e posteriormente. O HCM funciona como um serviço e liga a saída ao Azure Relay na porta 443.  

Depois de instalar o HCM, pode HybridConnectionManagerUi.exe utilizar a UI para a ferramenta. Este ficheiro está no diretório de instalação do Hybrid Connection Manager. No Windows 10, também pode pesquisar o *UI do Gestor de Conexões Híbridos* na sua caixa de pesquisa.  

![Screenshot do Gestor de Conexão Híbrida][7]

Quando inicia o HCM UI, a primeira coisa que vê é uma tabela que lista todas as Conexões Híbridas que estão configuradas com este caso do HCM. Se quiser es alterar, autente primeiro com o Azure. 

Para adicionar uma ou mais ligações híbridas ao seu HCM:

1. Inicie a UI HCM.
2. Selecione **Configurar outra Ligação Híbrida** .
![Screenshot de Configurar novas ligações híbridas][8]

1. Faça sô-9 com a sua conta Azure para obter as suas Ligações Híbridas disponíveis com as suas subscrições. O HCM não continua a utilizar a sua conta Azure para além disso. 
1. Escolha uma subscrição.
1. Selecione as Ligações Híbridas que pretende que o HCM retransmite.
![Screenshot de conexões híbridas][9]

1. Selecione **Guardar** .

Agora pode ver as Ligações Híbridas que adicionou. Também pode selecionar a Ligação Híbrida configurada para ver detalhes.

![Screenshot de detalhes de conexão híbrida][10]

Para suportar as Ligações Híbridas com as qual está configurado, o HCM requer:

- Acesso TCP a Azure sobre a porta 443.
- Acesso TCP ao ponto final de ligação híbrida.
- A capacidade de fazer pesquisas DNS no anfitrião do ponto final e no espaço de nomes do Service Bus.

> [!NOTE]
> O Azure Relay conta com tomadas web para conectividade. Esta capacidade só está disponível no Windows Server 2012 ou posteriormente. Por isso, o HCM não é suportado em nada mais cedo do que o Windows Server 2012.
>

### <a name="redundancy"></a>Redundância ###

Cada HCM pode suportar várias Ligações Híbridas. Além disso, qualquer ligação híbrida dada pode ser suportada por vários HCMs. O comportamento padrão é encaminhar o tráfego através dos HCMs configurados para qualquer ponto final. Se pretender uma elevada disponibilidade nas suas Ligações Híbridas a partir da sua rede, execute vários HCMs em máquinas separadas. O algoritmo de distribuição de carga utilizado pelo serviço Relay para distribuir tráfego aos HCMs é uma atribuição aleatória. 

### <a name="manually-add-a-hybrid-connection"></a>Adicione manualmente uma ligação híbrida ###

Para permitir que alguém fora da sua subscrição apresente uma instância HCM para uma determinada Ligação Híbrida, partilhe com eles a cadeia de ligação gateway para a Ligação Híbrida. Pode ver a cadeia de ligação gateway nas propriedades de Conexão Híbrida no [portal Azure][portal]. Para utilizar esta corda, **selecione Introduza manualmente** no HCM e cole na cadeia de ligação gateway.

![Adicione manualmente uma ligação híbrida][11]

### <a name="upgrade"></a>Atualização ###

Existem atualizações periódicas ao Gestor de Ligação Híbrida para corrigir problemas ou fornecer melhorias. Quando as atualizações forem lançadas, um pop-up aparecerá na UI HCM. A aplicação da atualização aplicará as alterações e reiniciará o HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Adicionar uma Ligação Híbrida à sua aplicação programáticamente ##

Existe suporte Azure CLI para ligações híbridas. Os comandos fornecidos funcionam tanto a nível da aplicação como do plano do Serviço de Aplicações.  Os comandos de nível de aplicação são:

```azurecli
az webapp hybrid-connection

Group
    az webapp hybrid-connection : Methods that list, add and remove hybrid-connections from webapps.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a hybrid-connection to a webapp.
    list   : List the hybrid-connections on a webapp.
    remove : Remove a hybrid-connection from a webapp.
```

Os comandos do plano de serviço de aplicações permitem definir qual a chave que uma determinada ligação híbrida irá utilizar. Há duas teclas definidas em cada Ligação Híbrida, uma primária e uma secundária. Pode optar por utilizar a tecla primária ou secundária com os comandos abaixo. Isto permite-lhe trocar as teclas para quando pretender regenerar periodicamente as suas chaves. 

```azurecli
az appservice hybrid-connection --help

Group
    az appservice hybrid-connection : A method that sets the key a hybrid-connection uses.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    set-key : Set the key that all apps in an appservice plan use to connect to the hybrid-
                connections in that appservice plan.
```

## <a name="secure-your-hybrid-connections"></a>Proteja as suas ligações híbridas ##

Uma conexão híbrida existente pode ser adicionada a outras Aplicações Web do Serviço de Aplicações por qualquer utilizador que tenha permissões suficientes no Relé de autocarros do Serviço Azure subjacente. Isto significa que, se tiver de impedir que outros reune a mesma Ligação Híbrida (por exemplo, quando o recurso-alvo é um serviço que não dispõe de medidas de segurança adicionais para impedir o acesso não autorizado), deve bloquear o acesso ao Relé de Autocarros do Serviço Azure.

Qualquer pessoa que tenha `Reader` acesso ao Relé poderá _ver_ a Ligação Híbrida ao tentar adicioná-la à sua Web App no portal Azure, mas não poderá _adicioná-la,_ uma vez que não tem as permissões necessárias para recuperar a cadeia de ligação que é utilizada para estabelecer a ligação de retransmissão. Para poderem adicionar com sucesso a Ligação Híbrida, devem ter a `listKeys` permissão ( `Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action` ). O `Contributor` papel ou qualquer outra função que inclua esta permissão no Relé permitirá que os utilizadores utilizem a Conexão Híbrida e adicionem-na às suas próprias Aplicações Web.

## <a name="troubleshooting"></a>Resolução de problemas ##

O estado de "Conectado" significa que pelo menos um HCM está configurado com essa Ligação Híbrida, e é capaz de chegar a Azure. Se o estado da sua Ligação Híbrida não **disser Conectado,** a sua Ligação Híbrida não está configurada em nenhum HCM que tenha acesso ao Azure.

A principal razão pela qual os clientes não podem ligar-se ao seu ponto final é porque o ponto final foi especificado usando um endereço IP em vez de um nome DNS. Se a sua aplicação não conseguir alcançar o ponto final pretendido e utilizar um endereço IP, mude para usar um nome DNS válido no anfitrião onde o HCM está em execução. Verifique também se o nome DNS se resolve corretamente no hospedeiro onde o HCM está em funcionamento. Confirme que existe conectividade do hospedeiro onde o HCM está a correr até ao ponto final de ligação híbrida.  

No Serviço de Aplicações, a ferramenta da linha de comando **tcpping** pode ser invocada a partir da consola Advanced Tools (Kudu). Esta ferramenta pode dizer-lhe se tem acesso a um ponto final TCP, mas não lhe diz se tem acesso a um ponto final de Ligação Híbrida. Quando utilizar a ferramenta na consola contra um ponto final de ligação híbrida, está apenas a confirmar que utiliza uma combinação host:porta.  

Se tiver um cliente de linha de comando para o seu ponto final, pode testar a conectividade a partir da consola de aplicações. Por exemplo, pode testar o acesso aos pontos finais do servidor web utilizando o curl.


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
[HCService]: /azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: /azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
