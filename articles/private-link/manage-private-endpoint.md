---
title: Gerir uma ligação private endpoint em Azure
description: Saiba como gerir ligações de ponto sinuoso privado em Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 62b24b3e2f5c1b89fa7db581ac34cf58381db2a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75452971"
---
# <a name="manage-a-private-endpoint-connection"></a>Manage a Private Endpoint connection (Gerir uma ligação de Ponto Final Privado)
A Azure Private Link trabalha num modelo de fluxo de chamadas de aprovação, segundo o qual o consumidor do serviço Private Link pode solicitar uma ligação ao prestador de serviços para o consumo do serviço. O prestador de serviços pode então decidir se permite que o consumidor se coneca ou não. A Azure Private Link permite aos prestadores de serviços gerir a ligação de ponto final privado nos seus recursos. Este artigo fornece instruções sobre como gerir as ligações Private Endpoint.

![Gerir pontos finais privados](media/manage-private-endpoint/manage-private-endpoint.png)

Existem dois métodos de aprovação de ligação que um consumidor de serviço private link pode escolher:
- **Automática**: Se o consumidor de serviço tiver permissões RBAC no recurso do prestador de serviços, o consumidor pode escolher o método de aprovação automática. Neste caso, quando o pedido chega ao recurso do prestador de serviços, não é necessária qualquer ação por parte do prestador de serviços e a ligação é automaticamente aprovada. 
- **Manual**: Pelo contrário, se o consumidor de serviço não tiver permissões RBAC no recurso do prestador de serviços, o consumidor pode escolher o método de homologação manual. Neste caso, o pedido de ligação aparece nos recursos de serviço como **Pendente**. O prestador de serviços tem de aprovar manualmente o pedido antes de se estabelecer em conexões. Em casos manuais, o consumidor de serviços também pode especificar uma mensagem com o pedido de fornecer mais contexto ao prestador de serviços. O prestador de serviços tem as seguintes opções para escolher para todas as ligações Private Endpoint: **Aprovado,** **Rejeitar**, **Remover**.

O quadro abaixo mostra as várias ações do prestador de serviços e os estados de conexão resultantes para pontos finais privados.  O prestador de serviços também pode alterar o estado de ligação de ligação de ponto final privado mais tarde sem a intervenção do consumidor. A ação atualizará o estado do ponto final do lado do consumidor. 


|Ação do Prestador de Serviços   |Serviço Consumidor Privado Endpoint Estado   |Descrição   |
|---------|---------|---------|
|Nenhuma    |    Pendente     |    A ligação é criada manualmente e está pendente para aprovação pelo proprietário do recurso Private Link.       |
|Aprovar    |  Aprovado       |  A ligação foi aprovada automaticamente ou manualmente e está pronta a ser utilizada.     |
|Rejeitar     | Rejected        | A ligação foi rejeitada pelo proprietário de recursos de ligação privada.        |
|Remover    |  Desligado       | A ligação foi removida pelo proprietário do recurso de ligação privada, o ponto final privado torna-se informativo e deve ser eliminado para limpeza.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Manage Private Endpoint Connections on Azure PaaS resources (Gerir Ligações de Ponto Final Privado nos recursos PaaS do Azure)
Portal é o método preferido para gerir ligações de pontos finais privados nos recursos Do Azure PaaS. Atualmente, não temos suporte PowerShell/CLI para gerir ligações nos recursos Azure PaaS.
1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Navegue para o Private Link Center.
3. No âmbito **dos Recursos**, selecione o tipo de recurso que pretende gerir as ligações de ponto final privado.
4. Para cada um dos seus recursos, pode ver o número de Ligações De Ponto Final Privado associadas ao mesmo. Pode filtrar os recursos conforme necessário.
5. Selecione as ligações de ponto final privados.  Sob as ligações listadas, selecione a ligação que pretende gerir. 
6. Pode alterar o estado da ligação selecionando as opções no topo.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Gerir ligações private endpoint em um serviço de Private Link propriedade de cliente/parceiro

A Azure PowerShell e a Azure CLI são os métodos preferidos para gerir as ligações private endpoint nos Serviços de Parceiros da Microsoft ou nos serviços de propriedade do cliente. Atualmente, não temos nenhum suporte de portal para gerir ligações num serviço de Link Privado.  
 
### <a name="powershell"></a>PowerShell 
  
Utilize os seguintes comandos PowerShell para gerir ligações de ponto final privados.  
#### <a name="get-private-link-connection-states"></a>Obtenha estados de ligação de ligação de ligação privada 
Utilize `Get-AzPrivateLinkService` o cmdlet para obter as ligações Private Endpoint e os seus estados.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Aprovar uma ligação private endpoint 
 
Utilize `Approve-AzPrivateEndpointConnection` o cmdlet para aprovar uma ligação Private Endpoint. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Negar a ligação de Endpoint Privado 
 
Utilize `Deny-AzPrivateEndpointConnection` o cmdlet para rejeitar uma ligação Private Endpoint. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Remover a ligação de ponto final privado 
 
Utilize `Remove-AzPrivateEndpointConnection` o cmdlet para remover uma ligação private endpoint. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>CLI do Azure 
 
Utilize `az network private-link-service update` para gerir as suas ligações Private Endpoint. O estado de ligação ```azurecli connection-status``` é especificado no parâmetro. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Passos seguintes
- [Saiba mais sobre pontos finais privados](private-endpoint-overview.md)
 
