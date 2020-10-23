---
title: Azure ExpressRoute CrossConnnections Desenvolvimento e integração da API
description: Este artigo fornece uma visão geral detalhada para os parceiros ExpressRoute sobre o tipo de recurso expressRouteCrossConnections.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: b80392231dba26a10141dcd8247b092e8171894c
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202485"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>ExpressRoute CrossConnnections Desenvolvimento e integração da API

A API Partner Resource Manager da ExpressRoute permite aos parceiros ExpressRoute gerir a configuração de camada-2 e camada-3 dos circuitos ExpressRoute do cliente. A API, Gestora de Recursos parceiros ExpressRoute, introduz um novo tipo de recurso, **expressRouteCrossConnections.** Os parceiros utilizam este recurso para gerir os circuitos ExpressRoute do cliente.

## <a name="workflow"></a>Fluxo de trabalho

O recurso expressRouteCrossConnections é um recurso sombra para o circuito ExpressRoute. Quando um cliente Azure cria um circuito ExpressRoute e seleciona um parceiro ExpressRoute específico, a Microsoft cria um recurso expressRouteCrossConnections na subscrição de gestão Azure ExpressRoute do parceiro. Ao fazê-lo, a Microsoft define um grupo de recursos para criar o recurso expressRouteCrossConnections. O padrão de nomeação para o grupo de recursos é **CrossConnection-* PeeringLocation***; onde PeeringLocation = a Localização ExpressRoute. Por exemplo, se um cliente criar um circuito ExpressRoute em Denver, o CrossConnection será criado na subscrição Azure do parceiro no seguinte grupo de recursos: **CrossConnnection-Denver**.

Os parceiros ExpressRoute gerem a configuração da camada 2 e da camada 3, emitindo operações REST contra o recurso expressRouteCrossConnections.

## <a name="benefits"></a>Benefícios

Benefícios da mudança para o recurso expressRouteCrossConnections:

* Quaisquer melhorias futuras para os parceiros ExpressRoute serão disponibilizadas no recurso ExpressRouteCrossConnection.

* Os parceiros podem aplicar [o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md) ao recurso expressRouteCrossConnection. Estes controlos podem definir permissões para as quais as contas dos utilizadores podem modificar o recurso expressRouteCrossConnection e adicionar/atualizar/eliminar configurações de peering.

* O recurso expressRouteCrossConnection expõe APIs que podem ser úteis na resolução de problemas das ligações ExpressRoute. Isto inclui tabela ARP, Resumo da Tabela de Rota BGP e detalhes da Tabela de Rota bGP. Esta capacidade não é suportada por APIs de implementação clássica.

* Os parceiros também podem procurar as comunidades anunciadas no microsoft espreitando usando o recurso *RouteFilter.*

## <a name="api-development-and-integration-steps"></a>Etapas de desenvolvimento e integração da API

Para se desenvolverem contra a API parceira, os parceiros ExpressRoute aproveitam uma configuração de cliente de teste e parceiro de teste. A configuração do cliente de teste será usada para criar circuitos ExpressRoute em locais de observação de teste que mapeiam para dispositivos falsos e portas. A configuração do parceiro de teste é utilizada para gerir os circuitos ExpressRoute criados no local de observação do teste.

### <a name="1-enlist-subscriptions"></a>1. Inscrições em lista

Para solicitar o parceiro de teste e a configuração do cliente de teste, recrute duas subscrições Pay-As-You-Go Azure para o seu contacto de engenharia ExpressRoute:
* **ExpressRoute_API_Dev_Provider_Sub:** Esta subscrição será utilizada para gerir os circuitos ExpressRoute criados em locais de observação de testes em dispositivos e portas falsos.

* **ExpressRoute_API_Dev_Customer_Sub:** Esta subscrição será usada para criar circuitos ExpressRoute em locais de observação de teste que mapeiam para dispositivos falsos e portas.

Os locais de observação do teste: os dispositivos falsos e as portas não estão expostos aos clientes de produção por defeito. Para criar circuitos ExpressRoute que mapeiem para a configuração do teste, é necessário ativar uma bandeira de funções de subscrição.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Registe a subscrição Dev_Provider para aceder à API expressRouteCrossConnections

Para aceder à API expressRouteCrossConnections, a subscrição do parceiro tem de ser inscrita no **Microsoft.Network Resource Provider**. Siga os passos nos [fornecedores de recursos Azure e artigo de tipo para](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) completar o processo de registo.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Configurar a autenticação para chamadas AZure Resource Manager REST API

A maioria dos serviços da Azure exigem que o código do cliente autença com o Gestor de Recursos, utilizando credenciais válidas, antes de chamar APIs do serviço. A autenticação é coordenada entre os vários atores da Azure AD e proporciona ao cliente um token de acesso como prova de autenticação.

O processo de autenticação envolve dois passos principais:

1. [Registe o cliente.](/rest/api/azure/#register-your-client-application-with-azure-ad)
2. [Crie o pedido de acesso.](/rest/api/azure/#create-the-request)

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. Fornecer autorização do Contribuinte de Rede para a aplicação do cliente

Uma vez configurada a autenticação com sucesso, tem de conceder ao Network Contributor acesso à sua aplicação de cliente, ao abrigo do Dev_Provider_Sub. Para conceder permissão, inscreva-se no [portal Azure](https://ms.portal.azure.com/#home) e complete os seguintes passos:

1. Navegue para Subscrições e selecione o Dev_Provider_Sub
2. Navegar para o Controlo de Acesso (IAM)
3. Adicionar atribuição de funções
4. Selecione a Função contribuinte de rede
5. Atribuir acesso ao Azure AD User, Group ou Service Principal
6. Selecione a sua aplicação ao cliente
7. Guardar alterações

### <a name="5-develop"></a>5. Desenvolver

Desenvolva-se contra a [API expressRouteCrossConnections](/rest/api/expressroute/expressroutecrossconnections).

## <a name="rest-api"></a>API REST

Consulte [ExpressRoute CrossConnections REST API](/rest/api/expressroute/expressroutecrossconnections) para documentação REST API.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre todas as APIs expressRoute REST, consulte [ExpressRoute REST APIs](/rest/api/expressroute/).