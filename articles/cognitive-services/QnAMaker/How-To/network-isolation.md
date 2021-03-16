---
title: Isolamento da rede
description: Os utilizadores podem restringir o acesso do público aos recursos do QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: afb396bc364a2fa2db923fbcbe6bfe1b7aedbc26
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467549"
---
# <a name="recommended-settings-for-network-isolation"></a>Configurações recomendadas para isolamento de rede

Deve seguir os passos abaixo para restringir o acesso do público aos recursos da QnA Maker. Proteja um recurso de Serviços Cognitivos do acesso público [configurando a rede virtual.](../../cognitive-services-virtual-networks.md?tabs=portal)

## <a name="restrict-access-to-app-service-qna-runtime"></a>Restringir o acesso ao Serviço de Aplicações (Tempo de Execução QnA)

Pode adicionar IPs ao serviço app, permitir a lista para restringir o acesso ou configurar o Ambiente de Serviço de Aplicações para acolher o Serviço de Aplicações QnA Maker.

#### <a name="add-ips-to-app-service-allow-list"></a>Adicionar IPs à lista de permitis do Serviço de Aplicações

1. Permitir o tráfego apenas a partir de IPs de Serviços Cognitivos. Estes já estão incluídos na Etiqueta de `CognitiveServicesManagement` Serviço. Isto é necessário para que as APIs de Autoria (Create/Update KB) invoquem o serviço de aplicações e atualizem o serviço de Pesquisa Azure em conformidade. Confira [mais informações sobre etiquetas de serviço.](../../../virtual-network/service-tags-overview.md)
2. Certifique-se de que também permite outros pontos de entrada como o Azure Bot Service, o portal QnA Maker, etc. para a previsão de acesso a API "GenerateAnswer".
3. Siga estes passos para adicionar os intervalos de endereço IP a uma lista de autorizações:

   1. Baixe [os Intervalos IP para todas as tags de serviço.](https://www.microsoft.com/download/details.aspx?id=56519)
   2. Selecione os IPs de "CognitiveServicesManagement".
   3. Navegue na secção de rede do seu recurso De Serviço de Aplicações e clique na opção "Configurar a Restrição de Acesso" para adicionar os IPs a uma lista de autorizações.

Também temos um script automatizado para fazer o mesmo para o seu Serviço de Aplicações. Pode encontrar o [script PowerShell para configurar uma lista de admissões](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) no GitHub. Você precisa inserir id de subscrição, grupo de recursos e nome real do Serviço de Aplicações como parâmetros de script. A execução do script irá adicionar automaticamente a lista de iPs ao Serviço de Aplicações.

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>Configure o Ambiente de Serviço de Aplicações para acolher o Serviço de Aplicações do Criador QnA
    
O Ambiente de Serviço de Aplicações (ASE) pode ser usado para hospedar o serviço QnA Maker App. Siga os passos abaixo:

1. Crie um Ambiente de Serviço de Aplicações e marque-o como "externo". Por favor, siga o [tutorial](../../../app-service/environment/create-external-ase.md) para instruções.
2.  Crie um serviço de Aplicações dentro do Ambiente de Serviço de Aplicações.
    1. Verifique a configuração do serviço de Aplicação e adicione 'PrimaryEndpointKey' como uma definição de aplicação. O valor de 'PrimaryEndpointKey' deve ser definido como " \<app-name\> -PrimaryEndpointKey". O Nome da Aplicação é definido no URL do serviço de aplicações. Por exemplo, se o URL do serviço de aplicações for "mywebsite.myase.p.azurewebsite.net", então o nome da aplicação é "mywebsite". Neste caso, o valor de 'PrimaryEndpointKey' deve ser definido como "mywebsite-PrimaryEndpointKey".
    2. Crie um serviço de pesquisa Azure.
    3. Certifique-se de que as configurações de pesquisa e aplicação do Azure estão devidamente configuradas. 
          Por favor, siga este [tutorial.](../reference-app-service.md?tabs=v1#app-service)
3.  Atualizar o Grupo de Segurança da Rede associado ao Ambiente de Serviço de Aplicações
    1. Atualizar regras de segurança de entrada pré-criadas de acordo com os seus requisitos.
    2. Adicione uma nova Regra de Segurança de Entrada com a fonte como 'Tag de Serviço' e etiqueta de serviço de origem como 'CognitiveServicesManagement'.
       
    ![exceções portuárias de entrada](../media/inbound-ports.png)

4.  Crie uma instância de serviço cognitivo QnA Maker (Microsoft.CognitiveServices/accounts) utilizando o Azure Resource Manager, onde o ponto final do QnA Maker deve ser definido para o Ponto final do Serviço de Aplicações criado acima (https:// mywebsite.myase.p.azurewebsite.net).
    
---

## <a name="restrict-access-to-cognitive-search-resource"></a>Restringir o acesso ao Recurso de Pesquisa Cognitiva

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

O exemplo de Pesquisa Cognitiva pode ser isolado através de um ponto final privado após a criação dos Recursos do Fabricante QnA. As ligações private Endpoint requerem um VNet através do qual a Instância do Serviço de Pesquisa pode ser acedida. 

Se o Serviço de Aplicações QnA Maker for restringido usando um Ambiente de Serviço de Aplicações, utilize o mesmo VNet para criar uma ligação private endpoint à instância de Pesquisa Cognitiva. Crie uma nova entrada de DNS no VNet para mapear o ponto final de Pesquisa Cognitiva para o endereço IP de Endpoint Privado de Pesquisa Cognitiva. 

Se um Ambiente de Serviço de Aplicações não for utilizado para o Serviço de Aplicações QnAMaker, crie primeiro um novo recurso VNet e, em seguida, crie a ligação Private Endpoint à instância de Pesquisa Cognitiva. Neste caso, o Serviço de Aplicações do Criador QnA precisa de [ser integrado com o VNet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet) para se ligar à instância de Pesquisa Cognitiva. 

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

[Crie pontos finais privados](../reference-private-endpoint.md) para o recurso Azure Search.

---
