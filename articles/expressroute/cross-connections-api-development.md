---
title: Azure ExpressRoute CrossConnnections Desenvolvimento e integração da API
description: Este artigo fornece uma visão detalhada para os parceiros ExpressRoute sobre o tipo de recurso expressRouteCrossConnections.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187016"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>Desenvolvimento e integração da API da ExpressRoute CrossConnnections

O API do ExpressRoute Partner Resource Manager permite aos parceiros expressRoute gerir a configuração camada 2 e camada 3 dos circuitos ExpressRoute do cliente. A API do Gestor de Recursos parceiros expressRoute introduz um novo tipo de recurso, **expressRouteCrossConnections**. Os parceiros usam este recurso para gerir os circuitos ExpressRoute do cliente.

## <a name="workflow"></a>Fluxo de trabalho

O recurso expressRouteCrossConnections é um recurso-sombra para o circuito ExpressRoute. Quando um cliente Azure cria um circuito ExpressRoute e seleciona um parceiro ExpressRoute específico, a Microsoft cria um recurso expressRouteCrossConnections na subscrição de gestão do parceiro Azure ExpressRoute. Ao fazê-lo, a Microsoft define um grupo de recursos para criar o recurso expressRouteCrossConnections. O padrão de nomeação para o grupo de recursos é * CrossConnection-PeeringLocation***;*CrossConnection-* onde peeringLocation = a Localização ExpressRoute. Por exemplo, se um cliente criar um circuito ExpressRoute em Denver, o CrossConnection será criado na subscrição azure do parceiro no seguinte grupo de recursos: **CrossConnnection-Denver**.

Os parceiros ExpressRoute gerem a configuração da camada 2 e da camada 3 através da emissão de operações REST contra o recurso expressRouteCrossConnections.

## <a name="benefits"></a>Vantagens

Benefícios de se mudar para o recurso expressRouteCrossConnections:

* Quaisquer futuras melhorias para os parceiros ExpressRoute serão disponibilizadas no recurso ExpressRouteCrossConnection.

* Os parceiros podem aplicar o [Controlo de Acesso Baseado em Funções](https://docs.microsoft.com/azure/role-based-access-control/overview) no recurso expressRouteCrossConnection. Estes controlos podem definir permissões para as quais as contas dos utilizadores podem modificar o recurso expressRouteCrossConnection e adicionar/atualizar/eliminar configurações de peering.

* O recurso expressRouteCrossConnection expõe APIs que podem ser úteis na resolução de problemas das ligações ExpressRoute. Isto inclui tabela ARP, resumo da tabela de rota bGP e detalhes da tabela de rota bGP. Esta capacidade não é suportada por APIs de implantação clássica.

* Os parceiros também podem procurar as comunidades anunciadas no peering da Microsoft usando o recurso *RouteFilter.*

## <a name="api-development-and-integration-steps"></a>Etapas de desenvolvimento e integração da API

Para desenvolver contra a Partner API, os parceiros ExpressRoute alavancam uma configuração de cliente de teste e parceiro de teste. A configuração do cliente de teste será usada para criar circuitos ExpressRoute em locais de teste de observação que mapeiam para dispositivos e portas manequim. A configuração do parceiro de teste é utilizada para gerir os circuitos ExpressRoute criados no local de observação de teste.

### <a name="1-enlist-subscriptions"></a>1. Inscrições de alistamento

Para solicitar o parceiro de teste e a configuração do cliente de teste, inscreva duas subscrições Pay-As-You-Go Azure para o seu contacto de engenharia ExpressRoute:
* **ExpressRoute_API_Dev_Provider_Sub:** Esta subscrição será utilizada para gerir circuitos ExpressRoute criados em locais de teste de observação em dispositivos e portas manequim.

* **ExpressRoute_API_Dev_Customer_Sub:** Esta subscrição será usada para criar circuitos ExpressRoute em locais de teste de observação que mapeiam para dispositivos e portas manequim.

Os locais de observação do teste: dispositivos de boneco e portas não estão expostos a clientes de produção por padrão. Para criar circuitos ExpressRoute que mapeiem a configuração do teste, uma bandeira de recurso de subscrição precisa de ser ativada.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Registe a subscrição Dev_Provider para aceder à API expressRouteCrossConnections

Para aceder à API expressRouteCrossConnections, a subscrição do parceiro precisa de ser inscrita no **Microsoft.Network Resource Provider**. Siga os passos nos fornecedores de [recursos azure e tipo](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) de artigo para completar o processo de registo.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Configurar a autenticação para chamadas da API do Gestor de Recursos Do Azure

A maioria dos serviços Azure exige que o código do cliente autentique com o Gestor de Recursos, utilizando credenciais válidas, antes de chamar apis de serviço. A autenticação é coordenada entre os vários atores pela Azure AD e fornece ao cliente um sinal de acesso como prova de autenticação.

O processo de autenticação envolve dois passos principais:

1. [Registe o cliente](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Criar o pedido de acesso.](https://docs.microsoft.com/rest/api/azure/#create-the-request)

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. Fornecer autorização do Colaborador da Rede para a aplicação do cliente

Uma vez configurada com sucesso a autenticação, é necessário conceder ao Colaborador da Rede acesso à sua aplicação de cliente, no âmbito do Dev_Provider_Sub. Para conceder permissão, inscreva-se no [portal Azure](https://ms.portal.azure.com/#home) e complete os seguintes passos:

1. Navegue para Subscrições e selecione o Dev_Provider_Sub
2. Navegar para controlo de acesso (IAM)
3. Adicionar Atribuição de Papéis
4. Selecione a função de colaborador de rede
5. Atribuir acesso ao Utilizador, Grupo ou Diretor de Serviço da AD Azure
6. Selecione a sua aplicação de cliente
7. Guardar alterações

### <a name="5-develop"></a>5. Desenvolver

Desenvolva-se contra a [API expressRouteCrossConnections](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections).

## <a name="rest-api"></a>API REST

Consulte [ExpressRoute CrossConnections REST API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) para obter documentação REST API.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre todas as APIs EXPRESSRoute REST, consulte [ExpressRoute REST APIs](https://docs.microsoft.com/rest/api/expressroute/).