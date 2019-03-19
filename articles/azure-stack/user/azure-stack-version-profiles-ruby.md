---
title: Através de perfis de versão de API com Ruby no Azure Stack | Documentos da Microsoft
description: Saiba como utilizar perfis de versão de API com o Ruby no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 5c7031b0d0a08106473409233db0ad95ecb32525
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095369"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Utilizar perfis de versão de API com o Ruby no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

## <a name="ruby-and-api-version-profiles"></a>Perfis de versão de API e Ruby

O SDK de Ruby para o Azure Stack Resource Manager fornece ferramentas para ajudar a criar e gerir a sua infraestrutura. Fornecedores de recursos no SDK incluem computação, redes virtuais e armazenamento com a linguagem Ruby. Perfis de API no SDK do Ruby permitem o desenvolvimento de cloud híbrida com a ajuda para alternar entre recursos do Azure global e recursos no Azure Stack.

Um perfil de API é uma combinação de fornecedores de recursos e as versões de serviço. Pode utilizar um perfil de API para combinar diferentes tipos de recursos.

- Para utilizar as versões mais recentes de todos os serviços, utilize o **mais recente** perfil do gem de rollup do SDK do Azure.
- Para utilizar os serviços compatíveis com o Azure Stack, utilize o **V2018_03_01** perfil do gem de rollup do SDK do Azure.
- Para utilizar a versão mais recente **versão de api** de um serviço, utilize o **mais recente** perfil da gem específica. Por exemplo, se gostaria de utilizar a versão mais recente **versão de api** do serviço de computação autónomo, utilize o **mais recente** perfil dos **computação** gem.
- Para utilizar um específico **versão de api** para um serviço, utilize as versões de API específicas definidas dentro da gem.

> [!NOTE]
> Pode combinar todas as opções no mesmo aplicativo.

## <a name="install-the-azure-ruby-sdk"></a>Instale o Azure SDK Ruby

- Siga as instruções oficiais para instalar [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Siga as instruções oficiais para instalar [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
  - Durante a instalação, escolher **Ruby adicionar a variável de caminho**.
  - Instale o kit de desenvolvimento durante a instalação do Ruby quando lhe for pedido.
  - Em seguida, instale o bundler com o seguinte comando:  
    `Gem install bundler`
- Se não está disponível, criar uma subscrição e guarde o ID de subscrição a ser utilizado mais tarde. Instruções para criar uma subscrição são [aqui](../azure-stack-subscribe-plan-provision-vm.md).
- Criar um principal de serviço e guarde o seu ID e segredo. Instruções para criar um principal de serviço para o Azure Stack são [aqui](../azure-stack-create-service-principals.md).
- Certifique-se de que o seu principal de serviço tem a função de proprietário/Contribuidor na sua subscrição. Obter instruções sobre como atribuir a função ao principal de serviço são [aqui](../azure-stack-create-service-principals.md).

## <a name="install-the-rubygem-packages"></a>Instalar os pacotes de Rubygem

Pode instalar os pacotes de Azure Rubygem diretamente.

```Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

O SDK de Ruby do Azure Resource Manager está em pré-visualização e provavelmente terá quebras de interface em versões futuras. Um aumento do número na versão secundária pode indicar que as alterações de última hora.

## <a name="use-the-azuresdk-gem"></a>Utilizar a gem azure_sdk

A gem, **azure_sdk**, é um rollup de todos os pedras preciosas suportados no SDK do Ruby. Vejam essa pérola é composta por um **mais recente** perfil, que suporta a versão mais recente de todos os serviços. Ele introduz dois perfil com a versão **V2017_03_09** e **V2018_03_01** perfis, que são criados para o Azure Stack.

Pode instalar a gem de rollup de azure_sdk com o seguinte comando:  

```Ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o SDK de Ruby do Azure com o Azure Stack, tem de indicar os valores seguintes e, em seguida, defina os valores com variáveis de ambiente. Veja as instruções depois da tabela para o seu sistema de operativo sobre como definir as variáveis de ambiente.

| Value | Variáveis de ambiente | Descrição |
| --- | --- | --- |
| ID do inquilino | AZURE_TENANT_ID | O valor do seu Azure Stack [ID de inquilino](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview). |
| ID de Cliente | AZURE_CLIENT_ID | O serviço de ID da aplicação principal guardado quando principal de serviço foi criado na secção anterior deste documento.  |
| ID da subscrição | AZURE_SUBSCRIPTION_ID | O [ID de subscrição](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) é como acessa ofertas no Azure Stack. |
| Segredo do Cliente | AZURE_CLIENT_SECRET | A aplicação do principal de serviço segredo guardado quando principal de serviço foi criado. |
| Ponto final do Gestor de recursos | ARM_ENDPOINT | Ver [ponto final do Gestor de recursos do Azure Stack](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>O ponto de final do Gestor de recursos do Azure Stack

O Gestor de recursos do Microsoft Azure é uma estrutura de gestão que permite aos administradores implementar, gerir e monitorizar recursos do Azure. O Azure Resource Manager pode lidar com essas tarefas, como um grupo, em vez de individualmente, numa única operação.

Pode obter as informações de metadados do ponto final do Resource Manager. O ponto final devolve um ficheiro JSON com as informações necessárias para executar o seu código.

 > [!NOTE]  
 > O **ResourceManagerUrl** no Azure Stack Development Kit (ASDK) é: `https://management.local.azurestack.external/` O **ResourceManagerUrl** em sistemas integrados é: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
 > Para obter os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
 Ficheiro JSON de exemplo:

 ```json
 {
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
   "graphEndpoint": "https://graph.windows.net/",  
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": {
     "loginEndpoint": "https://login.windows.net/",
     "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
   }
 }
```

### <a name="set-environmental-variables"></a>Variáveis de ambiente de conjunto

**Microsoft Windows**  
Para definir as variáveis de ambiente, num prompt de comando do Windows, utilize o seguinte formato:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS, Linux e sistemas baseados em Unix** sistemas de baseados em Unix, pode utilizar o seguinte comando:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Perfis de API existentes

A gem de rollup de Azure_sdk tem os seguintes três perfis:

1. **V2018_03_01** perfil criado para o Azure Stack. Utilize este perfil para a utilização de todas as versões mais recentes dos serviços disponíveis no Azure Stack.
2. **V2017_03_09**  
  Perfil criado para o Azure Stack. Utilize este perfil para os serviços para ser mais compatível com o Azure Stack.
3. **Latest**  
  Perfil consiste em versões mais recentes de todos os serviços. Utilize as versões mais recentes de todos os serviços.

Para obter mais informações sobre os perfis do Azure Stack e a API, consulte a [perfis de resumo de API](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Utilização de perfil de API do SDK de Ruby do Azure

Pode utilizar o seguinte código para criar uma instância de um cliente de perfil. Este parâmetro só é necessário para o Azure Stack ou noutras clouds privadas. Global Azure já tem estas definições por predefinição.

```Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
  ENV['AZURE_TENANT_ID'],
  ENV['AZURE_CLIENT_ID'],
  ENV['AZURE_CLIENT_SECRET'],
  active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
  credentials: credentials,
  subscription_id: subscription_id,
  active_directory_settings: active_directory_settings,
  base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2018_03_01::Mgmt::Client.new(options)
```

O cliente de perfil pode ser utilizado para aceder aos fornecedores de recursos individuais, como computação, armazenamento e rede:

```Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2018_03_01::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-environment-setting-functions"></a>Definir funções de definição de ambiente do Azure Stack

Para autenticar o principal de serviço para o ambiente do Azure Stack, definir os pontos de extremidade usando `get_active_directory_settings()`. Este método utiliza a **ARM_Endpoint** variável de ambiente que configurou durante o estabelecimento de suas variáveis de ambiente:

```Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
  settings = MsRestAzure::ActiveDirectoryServiceSettings.new
  response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
  status_code = response.code
  response_content = response.body
  unless status_code == "200"
    error_model = JSON.load(response_content)
    fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
  end
  result = JSON.load(response_content)
  settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
  settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
  settings
end
```

## <a name="samples-using-api-profiles"></a>Exemplos de utilização de perfis de API

Pode utilizar os exemplos seguintes encontrados no GitHub como referência para a criação de soluções com perfis de Ruby e a API do Azure Stack:

- [Gerir recursos do Azure e grupos de recursos com Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)
- [Gerir máquinas virtuais através de Ruby](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM)
- [Implementar um SSH da VM com um modelo em Ruby ativada](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment)

### <a name="sample-resource-manager-and-groups"></a>Gestor de recursos de exemplo e grupos

Para executar o exemplo, certifique-se de que tem instalado o Ruby. Se estiver a utilizar o Visual Studio Code, transfira a extensão de Ruby SDK também.

> [!NOTE]  
> Pode obter o repositório para o exemplo em "[recursos de gerir o Azure e grupos de recursos com Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)".

1. Clone o repositório:

   ```bash
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. Instale as dependências com o pacote:

   ```Bash
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. Criar um Azure principal de serviço com o PowerShell e recuperar os valores necessários.

   Para obter instruções sobre como criar um principal de serviço, consulte [utilize o Azure PowerShell para criar um principal de serviço com um certificado](../azure-stack-create-service-principals.md).

   Valores necessários são:
   - ID do inquilino
   - ID de Cliente
   - Segredo do Cliente
   - ID da subscrição
   - Ponto final do Gestor de recursos

   Defina as seguintes variáveis de ambiente com as informações que obteve a partir do Principal de serviço que criou.

   - Exportar AZURE_TENANT_ID = {o seu id de inquilino}
   - Exportar AZURE_CLIENT_ID = {o seu id de cliente}
   - Exportar AZURE_CLIENT_SECRET = {o seu segredo de cliente}
   - Exportar AZURE_SUBSCRIPTION_ID = {o seu id de subscrição}
   - Exportar ARM_ENDPOINT = {seu url do Gestor de recursos de AzureStack}

   > [!NOTE]  
   > No Windows, utilize o conjunto em vez de exportação.

4. Certifique-se de que a variável de localização é definida para a localização do Azure Stack; Por exemplo, `LOCAL="local"`.

5. Adicione a seguinte linha de código, se estiver a utilizar o Azure Stack ou noutras clouds privadas para direcionar os pontos de extremidade do direito Active Directory:

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. A variável de opções, adicione as definições do Active Directory e o URL de base para trabalhar com o Azure Stack:

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. Crie um perfil de um cliente que tenha como destino o perfil do Azure Stack:

   ```ruby  
   client = Azure::Resources::Profiles::V2018_03_01::Mgmt::Client.new(options)
   ```

8. Para autenticar o principal de serviço com o Azure Stack, os pontos de extremidade devem ser definidos usando **get_active_directory_settings()**. Este método utiliza a **ARM_Endpoint** variável de ambiente que configurou durante o estabelecimento de suas variáveis de ambiente:

   ```ruby  
   def get_active_directory_settings(armEndpoint)
     settings = MsRestAzure::ActiveDirectoryServiceSettings.new
     response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
     status_code = response.code
     response_content = response.body
     unless status_code == "200"
       error_model = JSON.load(response_content)
       fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
     end
     result = JSON.load(response_content)
     settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
     settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
     settings
   end
   ```

9. Execute o exemplo.

   ```ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>Passos Seguintes

- [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)
- [Configurar o ambiente do PowerShell do utilizador do Azure Stack](azure-stack-powershell-configure-user.md)  
