---
title: Implantação de máquina virtual do Azure com chefe
description: Saiba como usar o chefe para realizar a implantação e a configuração de máquinas virtuais automatizadas no Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: gwallace
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 58642cdbf164523390d5e4925290b43f6c05549b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039552"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatizar a implementação de máquinas virtuais do Azure com o Chef

Chefe é uma excelente ferramenta para fornecer automação e configurações de estado desejado.

Com a versão mais recente da API de nuvem, a chefe fornece integração direta com o Azure, oferecendo a você a capacidade de provisionar e implantar Estados de configuração por meio de um único comando.

Neste artigo, você configura o ambiente chefe para provisionar máquinas virtuais do Azure e percorre a criação de uma política ou "manual" e, em seguida, a implantação desse guia em uma máquina virtual do Azure.

## <a name="chef-basics"></a>Noções básicas do chefe
Antes de começar, [examine os conceitos básicos do chefe](https://www.chef.io/chef).

O diagrama a seguir ilustra a arquitetura chefe de alto nível.

![][2]

O chefe tem três componentes principais de arquitetura: chefe Server, cliente chefe (nó) e estação de trabalho chefe.

O servidor chefe é o ponto de gerenciamento e há duas opções para o servidor chefe: uma solução hospedada ou uma solução local.

O cliente chefe (nó) é o agente que reside nos servidores que você está gerenciando.

A estação de trabalho chefe, que é o nome para a estação de trabalho de administração em que você cria políticas e executa comandos de gerenciamento e o pacote de software das ferramentas do chefe.

Em geral, você verá _sua estação de trabalho_ como o local onde executa ações e a _estação de trabalho chefe_ para o pacote de software.
Por exemplo, você baixará o comando faca como parte da _estação de trabalho do chefe_, mas executará comandos de faca de _sua estação de trabalho_ para gerenciar a infraestrutura.

O chefe também usa os conceitos de "manuais" e "receitas", que são efetivamente as políticas que definimos e aplicamos aos servidores.

## <a name="preparing-your-workstation"></a>Preparando sua estação de trabalho

Primeiro, prepare sua estação de trabalho criando um diretório para armazenar os arquivos de configuração e os manuais do chefe.

Crie um diretório chamado C:\Chef.

Baixe e instale a versão mais recente do [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) em na sua estação de trabalho.

## <a name="configure-azure-service-principal"></a>Configurar o Principal de Serviço do Azure

Em termos mais simples, e a entidade de serviço do Azure é uma conta de serviço.   Usaremos uma entidade de serviço para nos ajudar a criar recursos do Azure de nossa estação de trabalho chefe.  Para criar a entidade de serviço relevante com as permissões necessárias, precisamos executar os seguintes comandos no PowerShell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Anote suas assinaturas, Tenantid, ClientID e segredo do cliente (a senha que você definiu acima). você precisará dela mais tarde. 

## <a name="setup-chef-server"></a>Configurar servidor chefe

Este guia pressupõe que você se inscreverá no chefe hospedado.

Se você ainda não estiver usando um servidor chefe, poderá:

* Inscreva-se no [chefe hospedado](https://manage.chef.io/signup), que é a maneira mais rápida de começar a usar o chefe.
* Instale um servidor chefe autônomo no computador baseado em Linux, seguindo as [instruções de instalação](https://docs.chef.io/install_server.html) do [chefe docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Criando uma conta do chefe hospedado

Inscreva-se para uma conta do chefe hospedado [aqui](https://manage.chef.io/signup).

Durante o processo de inscrição, você será solicitado a criar uma nova organização.

![][3]

Depois que sua organização for criada, baixe o kit do iniciante.

![][4]

> [!NOTE]
> Se você receber um aviso avisando que suas chaves serão redefinidas, não haverá problema em continuar, pois não há nenhuma infraestrutura existente configurada como ainda.
>

Este arquivo zip do Starter Kit contém os arquivos de configuração da sua organização e a chave do usuário no diretório `.chef`.

O `organization-validator.pem` deve ser baixado separadamente, porque é uma chave privada e as chaves privadas não devem ser armazenadas no servidor chefe. No [chefe gerenciar](https://manage.chef.io/), vá para a seção Administração e selecione "redefinir chave de validação", que fornece um arquivo para download separado. Salve o arquivo em c:\chef.

### <a name="configuring-your-chef-workstation"></a>Configurando sua estação de trabalho do chefe

Extraia o conteúdo do chef-Starter. zip para c:\chef.

Copie todos os arquivos em chef-starter\chef-repo\.chefe para seu diretório c:\chef.

Copie o arquivo `organization-validator.pem` para c:\chef, se ele for salvo em c:\Downloads

O diretório agora deve ser semelhante ao exemplo a seguir.

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

Agora você deve ter cinco arquivos e quatro diretórios (incluindo o diretório chefe-repositório vazio) na raiz de c:\chef.

### <a name="edit-kniferb"></a>Editar faca. rb

Os arquivos PEM contêm a sua organização e as chaves privadas administrativas para comunicação e o arquivo faca. rb contém sua configuração de faca. Será necessário editar o arquivo faca. rb.

Abra o arquivo faca. rb no editor de sua escolha. O arquivo inalterado deve ser semelhante a:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Adicione as seguintes informações à sua faca. rb:

validation_client_name "MyOrg-Validator"

validation_key           "#{current_dir}/myorg.pem"

knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"

knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

knife[:azure_client_secret] =     "#1234p$wdchef19"


Essas linhas garantirão que a faca faça referência ao diretório do Cookbooks em c:\chef\cookbooks e também use a entidade de serviço do Azure que você criou durante as operações do Azure.

O arquivo faca. rb agora deve ser semelhante ao exemplo a seguir:

![][14]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Instalar a estação de trabalho do chefe

Em seguida, [Baixe e instale](https://downloads.chef.io/chef-workstation/) a estação de trabalho chefe.
Instale a estação de trabalho chefe o local padrão. Essa instalação pode levar alguns minutos.

Na área de trabalho, você verá um "PowerShell de PV", que é um ambiente carregado com a ferramenta que você precisará para interagir com os produtos chefe. O PowerShell de PV disponibiliza novos comandos ad hoc, como `chef-run`, bem como comandos tradicionais da CLI do chefe, como `chef`. Consulte a versão instalada da estação de trabalho do chefe e as ferramentas do chefe com `chef -v`. Você também pode verificar a versão da estação de trabalho selecionando "sobre a estação de trabalho do chefe" no aplicativo chefe Workstation.

`chef --version` deve retornar algo como:

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> A ordem do caminho é importante! Se os caminhos de opscode não estiverem na ordem correta, você terá problemas.
>

Reinicialize sua estação de trabalho antes de continuar.

### <a name="install-knife-azure"></a>Instalar o Azure de faca

Este tutorial pressupõe que você esteja usando o Azure Resource Manager para interagir com sua máquina virtual.

Instale a extensão de faca do Azure. Isso fornece a faca com o "plug-in do Azure".

Execute o comando a seguir.

    chef gem install knife-azure ––pre

> [!NOTE]
> O argumento – pre garante que você esteja recebendo a versão RC mais recente do plug-in do Azure de faca, que fornece acesso ao conjunto mais recente de APIs.
>
>

É provável que várias dependências também sejam instaladas ao mesmo tempo.

![][8]

Para garantir que tudo esteja configurado corretamente, execute o comando a seguir.

    knife azurerm server list

Se tudo estiver configurado corretamente, você verá uma lista de imagens do Azure disponíveis na rolagem.

Parabéns! Sua estação de trabalho está configurada!

## <a name="creating-a-cookbook"></a>Criando um manual

Um manual é usado pelo chefe para definir um conjunto de comandos que você deseja executar em seu cliente gerenciado. Criar um manual é simples, basta usar o comando **chefe Generate Cookbook** para gerar o modelo do manual. Este manual é para um servidor Web que implanta automaticamente o IIS.

No diretório C:\Chef, execute o comando a seguir.

    chef generate cookbook webserver

Esse comando gera um conjunto de arquivos sob o diretório C:\Chef\cookbooks\webserver. Em seguida, defina o conjunto de comandos para o cliente chefe a ser executado na máquina virtual gerenciada.

Os comandos são armazenados no arquivo default. rb. Nesse arquivo, defina um conjunto de comandos que instala o IIS, inicia o IIS e copia um arquivo de modelo para a pasta wwwroot.

Modifique o arquivo C:\chef\cookbooks\webserver\recipes\default.rb e adicione as linhas a seguir.

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Depois de terminar, salve o arquivo.

## <a name="creating-a-template"></a>Criando um modelo
Nesta etapa, você gerará um arquivo de modelo para ser usado como a página default. html.

Execute o seguinte comando para gerar o modelo:

    chef generate template webserver Default.htm

Navegue até o arquivo de `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Edite o arquivo adicionando um código HTML simples "Olá, Mundo" e, em seguida, salve o arquivo.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Carregar o manual no servidor chefe
Nesta etapa, você faz uma cópia do manual criado no computador local e o carrega no servidor hospedado do chefe. Uma vez carregado, o manual aparece na guia **política** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Implantar uma máquina virtual com o Azure faca
Implante uma máquina virtual do Azure e aplique o manual do "WebServer", que instala o serviço Web do IIS e a página da Web padrão.

Para fazer isso, use o comando **faca azurerm Server CREATE** .

Um exemplo do comando é exibido em seguida.

    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"


O exemplo acima criará um Standard_DS2_v2 máquina virtual com o Windows Server 2016 instalado na região oeste dos EUA. Substitua suas variáveis específicas e execute.

> [!NOTE]
> Por meio da linha de comando, também estou automatizando minhas regras de filtro de rede de ponto de extremidade usando o parâmetro – TCP-Endpoint Points. Abri as portas 80 e 3389 para fornecer acesso à página da Web e à sessão RDP.
>
>

Depois de executar o comando, vá para a portal do Azure para ver seu computador começar a provisionar.

![][15]

O prompt de comando aparece em seguida.

![][16]

Depois que a implantação for concluída, o endereço IP público da nova máquina virtual será exibido na conclusão da implantação, você poderá copiá-la e colá-la em um navegador da Web e exibir o site que você implantou. Quando implantamos a máquina virtual, abrimos a porta 80, portanto, ela deve estar disponível externamente.   

![][11]

Este exemplo usa código HTML criativo.

Você também pode exibir o status do nó [chefe gerenciar](https://manage.chef.io/). 

![][17]

Não se esqueça de que você também pode se conectar por meio de uma sessão RDP da portal do Azure pela porta 3389.

Obrigado! Vá e comece sua infraestrutura como a jornada de código com o Azure hoje mesmo!

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png
[14]: media/chef-automation/14.png
[15]: media/chef-automation/15.png
[16]: media/chef-automation/16.png
[17]: media/chef-automation/17.png


<!--Link references-->
