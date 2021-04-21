---
title: Guia de resolução de problemas para a | de autocarros da Azure Service Microsoft Docs
description: Saiba mais sobre dicas e recomendações de resolução de problemas para alguns problemas que poderá ver ao utilizar o Azure Service Bus.
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: 27249d7e016ea8aee0552bbbf1687647760d4b6f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786572"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Guia de resolução de problemas para o ônibus de serviço Azure
Este artigo fornece dicas e recomendações de resolução de problemas para alguns problemas que você pode ver ao usar o Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemas de conectividade, certificado ou timeout
Os seguintes passos podem ajudá-lo com problemas de conectividade/certificado/timeout para todos os serviços sob *.servicebus.windows.net. 

- Navegue para ou [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` . Ajuda a verificar se tem problemas de filtragem IP ou rede virtual ou cadeia de certificados, que são comuns quando se utiliza java SDK.

    Um exemplo de mensagem bem sucedida:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Um exemplo de mensagem de erro de falha:

    ```xml
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Verifique o seguinte comando para verificar se alguma porta está bloqueada na firewall. As portas utilizadas são 443 (HTTPS), 5671 (AMQP) e 9354 (Net Messaging/SBMP). Dependendo da biblioteca que utiliza, outras portas também são utilizadas. Aqui está o comando da amostra que verifica se a porta 5671 está bloqueada. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Em Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Quando houver problemas de conectividade intermitentes, verifique o seguinte comando para verificar se existem pacotes abandonados. Este comando tentará estabelecer 25 ligações TCP diferentes a cada 1 segundo com o serviço. Em seguida, pode verificar quantos deles conseguiram/falharam e também ver a latência da ligação TCP. Pode descarregar a ferramenta a `psping` partir [daqui.](/sysinternals/downloads/psping)

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Pode utilizar comandos equivalentes se estiver a utilizar outras ferramentas `tnc` `ping` como, e assim por diante. 
- Obtenha um rastreio de rede se os passos anteriores não ajudarem e analisá-lo usando ferramentas como [o Wireshark](https://www.wireshark.org/). Contacte [o Microsoft Support](https://support.microsoft.com/) se necessário. 
- Para encontrar os endereços IP certos para adicionar para permitir a lista para as suas ligações, consulte [os endereços IP que preciso adicionar à lista de admissão](service-bus-faq.yml#what-ip-addresses-do-i-need-to-add-to-allow-list-). 


## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemas que podem ocorrer com atualizações de serviço/reinícios

### <a name="symptoms"></a>Sintomas
- Os pedidos podem ser momentaneamente estrangulados.
- Pode haver uma queda nas mensagens/pedidos de entrada.
- O ficheiro de registo pode conter mensagens de erro.
- As aplicações podem ser desligadas do serviço durante alguns segundos.

### <a name="cause"></a>Causa
As atualizações e reiniciões do serviço backend podem causar estes problemas nas suas aplicações.

### <a name="resolution"></a>Resolução
Se o código de aplicação utilizar o SDK, a política de retíria já está incorporada e ativa. A aplicação reconectará-se sem impacto significativo na aplicação/fluxo de trabalho.

## <a name="unauthorized-access-send-claims-are-required"></a>Acesso não autorizado: São necessárias reclamações de envio

### <a name="symptoms"></a>Sintomas 
Pode ver este erro ao tentar aceder a um tópico de Service Bus a partir do Visual Studio num computador no local, utilizando uma identidade gerida atribuída pelo utilizador com permissões de envio.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>Causa
A identidade não tem permissões para aceder ao tema do Service Bus. 

### <a name="resolution"></a>Resolução
Para resolver este erro, instale a biblioteca [Microsoft.Azure.Services.AppAuthentication.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)  Para mais informações, consulte [a autenticação de desenvolvimento local.](/dotnet/api/overview/azure/service-to-service-authentication#local-development-authentication) 

Para aprender a atribuir permissões a funções, consulte [Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos recursos do Azure Service Bus](service-bus-managed-service-identity.md).

## <a name="service-bus-exception-put-token-failed"></a>Exceção do autocarro de serviço: Colocar o símbolo falhou

### <a name="symptoms"></a>Sintomas
Quando tentar enviar mais de 1000 mensagens utilizando a mesma ligação Service Bus, receberá a seguinte mensagem de erro: 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>Causa
Há um limite no número de fichas que são usadas para enviar e receber mensagens usando uma única ligação a um espaço de nomes de Service Bus. São mil. 

### <a name="resolution"></a>Resolução
Abra uma nova ligação ao espaço de nomes do Service Bus para enviar mais mensagens.

## <a name="adding-virtual-network-rule-using-powershell-fails"></a>Adicionar regra de rede virtual usando PowerShell falha

### <a name="symptoms"></a>Sintomas
Configuraste duas sub-redes de uma única rede virtual numa regra de rede virtual. Quando tenta remover uma sub-rede utilizando o cmdlet [Remove-AzServiceBusVirtualNetrule,](/powershell/module/az.servicebus/remove-azservicebusvirtualnetworkrule) não remove a sub-rede da regra da rede virtual. 

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName $resourceGroupName -Namespace $serviceBusName -SubnetId $subnetId
```

### <a name="cause"></a>Causa
O ID do Gestor de Recursos Azure que especificou para a sub-rede pode ser inválido. Isto pode acontecer quando a rede virtual está num grupo de recursos diferente daquele que tem o espaço de nomes do Service Bus. Se não especificar explicitamente o grupo de recursos da rede virtual, o comando CLI constrói o ID do Gestor de Recursos Azure utilizando o grupo de recursos do espaço de nomes do Service Bus. Então, não remove a sub-rede da regra da rede. 

### <a name="resolution"></a>Resolução
Especifique o ID completo do Gestor de Recursos Azure da sub-rede que inclui o nome do grupo de recursos que tem a rede virtual. Por exemplo:

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName myRG -Namespace myNamespace -SubnetId "/subscriptions/SubscriptionId/resourcegroups/ResourceGroup/myOtherRG/providers/Microsoft.Network/virtualNetworks/myVNet/subnets/mySubnet"
```

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos: 

- [Exceções do Gestor de Recursos Azure](service-bus-resource-manager-exceptions.md). Lista exceções geradas ao interagir com o Azure Service Bus utilizando o Azure Resource Manager (através de modelos ou chamadas diretas).
- [Exceções de mensagens.](service-bus-messaging-exceptions.md) Fornece uma lista de exceções geradas pela .NET Framework para a Azure Service Bus.