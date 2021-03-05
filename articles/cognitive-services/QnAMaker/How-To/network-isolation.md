---
title: Isolamento da rede
description: Os utilizadores podem restringir o acesso do público aos recursos do QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 4e9b45c72e5fb4cbd9e548727faf8946e3e5ba8f
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125090"
---
# <a name="recommended-settings-for-network-isolation"></a>Configurações recomendadas para isolamento de rede

Deve seguir os passos abaixo para restringir o acesso do público aos recursos do QnA Maker. Proteja um recurso de Serviços Cognitivos do acesso público [configurando a rede virtual.](../../cognitive-services-virtual-networks.md?tabs=portal)

## <a name="restrict-access-to-cognitive-search-resource"></a>Restringir o acesso ao Recurso de Pesquisa Cognitiva

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Configurar a Pesquisa Cognitiva como um ponto final privado dentro de um VNET. Quando uma instância de Pesquisa é criada durante a criação de um recurso QnA Maker, pode forçar a Pesquisa Cognitiva a suportar uma configuração de ponto final privada criada inteiramente dentro do VNet de um cliente.

Todos os recursos devem ser criados na mesma região para utilizar um ponto final privado.

* Recurso do Fabricante QnA
* novo recurso de Pesquisa Cognitiva
* novo recurso de Rede Virtual

Complete os seguintes passos no [portal Azure:](https://portal.azure.com)

1. Criar um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).
2. Crie um novo recurso de Pesquisa Cognitiva com conectividade Endpoint (dados) definido para _Private_. Crie o recurso na mesma região que o recurso QnA Maker criado no passo 1. Saiba mais sobre [a criação de um recurso de Pesquisa Cognitiva,](../../../search/search-create-service-portal.md)em seguida, use este link para ir diretamente para a [página de criação do recurso](https://ms.portal.azure.com/#create/Microsoft.Search).
3. Criar um novo [recurso de Rede Virtual.](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM)
4. Configure o VNET no recurso de serviço app criado no passo 1 deste procedimento. Crie uma nova entrada de DNS no VNET para novo recurso de Pesquisa Cognitiva criado no passo 2. para o endereço IP de Pesquisa Cognitiva.
5. [Associar o serviço app ao novo recurso de Pesquisa Cognitiva](../how-to/set-up-qnamaker-service-azure.md) criado no passo 2. Em seguida, pode eliminar o recurso original de Pesquisa Cognitiva criado no passo 1.
    
No [portal QnA Maker,](https://www.qnamaker.ai/)crie a sua primeira base de conhecimento.

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

[Crie pontos finais privados](../reference-private-endpoint.md) para o recurso Azure Search.

---

## <a name="restrict-access-to-app-service-qna-runtime"></a>Restringir o acesso ao Serviço de Aplicações (Tempo de Execução QnA)

Pode adicionar IPs ao serviço app, permitindo que a lista de pessoas restrinja o acesso ou o Serviço de Aplicações Configure Environemnt para hospedar o QnA Maker App Service.

#### <a name="add-ips-to-app-service-allowlist"></a>Adicionar IPs à lista de admissões do Serviço de Aplicações

1. Permitir o tráfego apenas a partir de IPs de Serviços Cognitivos. Estes já estão incluídos na Etiqueta de `CognitiveServicesManagement` Serviço. Isto é necessário para que as APIs de Autoria (Create/Update KB) invoquem o serviço de aplicações e atualizem o serviço de Pesquisa Azure em conformidade. Confira [mais informações sobre etiquetas de serviço.](../../../virtual-network/service-tags-overview.md)
2. Certifique-se de que também permite outros pontos de entrada como o Azure Bot Service, o portal QnA Maker, etc. para a previsão de acesso a API "GenerateAnswer".
3. Siga estes passos para adicionar os intervalos de endereço IP a uma lista de admissões:

   1. Baixe [os Intervalos IP para todas as tags de serviço.](https://www.microsoft.com/download/details.aspx?id=56519)
   2. Selecione os IPs de "CognitiveServicesManagement".
   3. Navegue na secção de rede do seu recurso De Serviço de Aplicações e clique na opção "Configurar a Restrição de Acesso" para adicionar os IPs a uma lista de admissões.

Também temos um script automatizado para fazer o mesmo para o seu Serviço de Aplicações. Pode encontrar o [script PowerShell para configurar uma lista de admissões](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) no GitHub. Você precisa inserir id de subscrição, grupo de recursos e nome real do Serviço de Aplicações como parâmetros de script. A execução do script irá automaticamente adicionar o IPs à lista de admissões do Serviço de Aplicações.

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
4.  Crie uma instância de serviço cognitivo QnA Maker (Microsoft.CognitiveServices/accounts) utilizando o Azure Resource Manager, onde o ponto final do QnA Maker deve ser definido para o Ponto final do Serviço de Aplicações criado acima (https:// mywebsite.myase.p.azurewebsite.net).
    
---
