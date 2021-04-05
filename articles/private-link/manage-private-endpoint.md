---
title: Gerir uma ligação private endpoint em Azure
description: Saiba como gerir ligações privadas de ponto final em Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2987cd1ff8c678f7079e13e8b9bc657817c066f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95531377"
---
# <a name="manage-a-private-endpoint-connection"></a>Manage a Private Endpoint connection (Gerir uma ligação de Ponto Final Privado)
A Azure Private Link trabalha num modelo de fluxo de chamada de aprovação em que o consumidor do serviço Private Link pode solicitar uma ligação ao prestador de serviços para o consumo do serviço. O prestador de serviços pode então decidir se permite ou não a ligação do consumidor. O Azure Private Link permite aos prestadores de serviços gerir a ligação privada de ponto final nos seus recursos. Este artigo fornece instruções sobre como gerir as ligações Private Endpoint.

![Gerir pontos finais privados](media/manage-private-endpoint/manage-private-endpoint.png)

Existem dois métodos de aprovação de ligação que um consumidor de serviço de Ligação Privada pode escolher:
- **Automático**: Se o consumidor de serviço tiver permissões Azure RBAC no recurso do prestador de serviços, o consumidor pode escolher o método de aprovação automática. Neste caso, quando o pedido chega ao recurso do prestador de serviços, não é necessária qualquer ação do prestador de serviços e a ligação é automaticamente aprovada. 
- **Manual**: Pelo contrário, se o consumidor de serviço não tiver permissões Azure RBAC no recurso do prestador de serviços, o consumidor pode escolher o método de aprovação manual. Neste caso, o pedido de ligação aparece nos recursos de serviço como **Pendente**. O prestador de serviços tem de aprovar manualmente o pedido antes de serem estabelecidas ligações. Em casos manuais, o consumidor de serviços também pode especificar uma mensagem com o pedido de fornecer mais contexto ao prestador de serviços. O prestador de serviços tem as seguintes opções a escolher para todas as ligações Private Endpoint: **Aprovado,** **Rejeitar,** **Remover**.

A tabela abaixo mostra as várias ações do prestador de serviços e os estados de conexão resultantes para private endpoints.  O prestador de serviços também pode alterar o estado de ligação da ligação privada de ponto final mais tarde sem a intervenção do consumidor. A ação atualizará o estado do ponto final do lado do consumidor. 


|Ação do Prestador de Serviços   |Estado de Endpoint Privado de Consumidor de Serviço   |Descrição   |
|---------|---------|---------|
|Nenhum    |    Pendente     |    A ligação é criada manualmente e está pendente para aprovação pelo proprietário do recurso Private Link.       |
|Aprovar    |  Aprovado       |  A ligação foi aprovada automaticamente ou manualmente e está pronta a ser utilizada.     |
|Rejeitar     | Rejeitado        | A ligação foi rejeitada pelo proprietário de recursos de ligação privada.        |
|Remover    |  Desligado       | A ligação foi removida pelo proprietário do recurso de ligação privada, o ponto final privado torna-se informativo e deve ser eliminado para limpeza.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Manage Private Endpoint Connections on Azure PaaS resources (Gerir Ligações de Ponto Final Privado nos recursos PaaS do Azure)
Portal é o método preferido para gerir ligações privadas de ponto final nos recursos Azure PaaS. Atualmente, não temos suporte PowerShell/CLI para gerir ligações em recursos Azure PaaS.
1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Navegue para o Private Link Center.
3. Em **Recursos**, selecione o tipo de recurso que pretende gerir as ligações de ponto final privado.
4. Para cada um do seu tipo de recurso, pode visualizar o número de Ligações de Ponto Final Privadas associadas a ele. Pode filtrar os recursos conforme necessário.
5. Selecione as ligações de ponto final privado.  Sob as ligações listadas, selecione a ligação que pretende gerir. 
6. Pode alterar o estado da ligação selecionando a partir das opções no topo.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Gerir ligações Private Endpoint num serviço de Private Link propriedade do cliente/parceiro

A Azure PowerShell e Azure CLI são os métodos preferidos para gerir ligações Private Endpoint nos Serviços de Parceiros da Microsoft ou serviços de propriedade do cliente. Atualmente, não temos qualquer suporte ao portal para gerir ligações num serviço private Link.  
 
### <a name="powershell"></a>PowerShell 
  
Utilize os seguintes comandos PowerShell para gerir ligações privadas de ponto final.  
#### <a name="get-private-link-connection-states"></a>Obtenha estados de ligação de ligação privada 
Utilize o `Get-AzPrivateLinkService` cmdlet para obter as ligações Private Endpoint e os seus estados.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Aprovar uma ligação private endpoint 
 
Utilize o `Approve-AzPrivateEndpointConnection` cmdlet para aprovar uma ligação Private Endpoint. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Negar ligação private endpoint 
 
Utilize o `Deny-AzPrivateEndpointConnection` cmdlet para rejeitar uma ligação Private Endpoint. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Remover ligação de ponto final privado 
 
Utilize o `Remove-AzPrivateEndpointConnection` cmdlet para remover uma ligação private endpoint. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>CLI do Azure 
 
Utilize `az network private-link-service update` para gerir as suas ligações Private Endpoint. O estado de ligação é especificado no ```azurecli connection-status``` parâmetro. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Passos seguintes
- [Saiba mais sobre os pontos finais privados](private-endpoint-overview.md)
 
