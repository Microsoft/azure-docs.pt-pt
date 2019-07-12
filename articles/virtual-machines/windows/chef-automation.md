---
title: Implementação da máquina virtual do Azure com o Chef | Documentos da Microsoft
description: Saiba como utilizar o Chef para implementações de máquina de virtual automatizado e configuração no Microsoft Azure
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
ms.devlang: na
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 74b92c277b1d6eaa0984e55a70459bad59c2bf84
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719269"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatizar a implementação de máquinas virtuais do Azure com o Chef

Chef é uma ótima ferramenta para o fornecimento de automatização e assim o desejar configurações de estado.

Com a versão mais recente do API na nuvem, a Chef fornece uma integração perfeita com o Azure, dando-lhe a capacidade de aprovisionar e implementar os Estados de configuração através de um único comando.

Neste artigo, vai configurar o ambiente do Chef para aprovisionar máquinas virtuais do Azure e percorrer a criar uma política ou "Manual" e, em seguida, implementar este manual de uma máquina virtual do Azure.

## <a name="chef-basics"></a>Noções básicas do chef
Antes de começar, [rever os conceitos básicos do Chef](https://www.chef.io/chef).

O diagrama seguinte ilustra a arquitetura geral do Chef.

![][2]

O chef tem três componentes de arquiteturais principais: Servidor do chef, o cliente do Chef (node) e o Chef estação de trabalho.

O servidor de Chef é o ponto de gestão e existem duas opções para o servidor do Chef: uma solução alojada ou uma solução no local.

O cliente do Chef (node) é o agente que encontra-se nos servidores que está gerenciando.

A estação de trabalho do Chef, que é o nome para o administrador de ambos os estação de trabalho onde pode criar políticas e executar comandos de gestão e o pacote de software de ferramentas da Chef.

Em geral, verá _sua estação de trabalho_ como a localização onde realizar ações e _estação de trabalho do Chef_ para o pacote de software.
Por exemplo, irá transferir o comando knife como parte da _estação de trabalho do Chef_, mas executar comandos knife _sua estação de trabalho_ de gerir a infraestrutura.

Chef também usa os conceitos de "Guias detalhados" e "Receitas", que são, efetivamente, as políticas que definem e aplicam-se aos servidores.

## <a name="preparing-your-workstation"></a>A preparar a estação de trabalho

Em primeiro lugar, de preparação de sua estação de trabalho através da criação de um diretório para armazenar ficheiros de configuração do Chef e guias detalhados.

Crie um diretório chamado C:\Chef.

Transfira e instale a versão mais recente [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) versão logon em sua estação de trabalho.

## <a name="configure-azure-service-principal"></a>Configurar o Principal de Serviço do Azure

No mais simples de termos e Principal de serviço do Azure é uma conta de serviço.   Usaremos um Principal de serviço para ajudar-na criar recursos do Azure a partir de nossa estação de trabalho do Chef.  Para criar o Principal de serviço relevante com as permissões necessárias, que é necessário para executar os seguintes comandos do PowerShell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Tire uma nota de SubscriptionID, TenantID, ID de cliente e segredo do cliente (a palavra-passe definido acima), irá precisar mais tarde. 

## <a name="setup-chef-server"></a>Configurar o servidor do Chef

Este guia assume que se irá inscrever alojado Chef.

Se ainda não estiver a utilizar um servidor do Chef, pode:

* Inscreva-se [alojada Chef](https://manage.chef.io/signup), que é a forma mais rápida para começar a utilizar com o Chef.
* Instalar um servidor do Chef autónomo no computador baseado em linux, seguindo a [instruções de instalação](https://docs.chef.io/install_server.html) partir [Chef Docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Criar uma conta do Chef alojado

Inscreva-se uma conta do Chef alojado [aqui](https://manage.chef.io/signup).

Durante o processo de inscrição, será solicitado para criar uma nova organização.

![][3]

Depois de criar a sua organização, Baixe o starter kit.

![][4]

> [!NOTE]
> Se receber uma mensagem avisando que as suas chaves serão repostas, há problema continuar porque não temos nenhuma infraestrutura existente configurada ainda.
>

Este ficheiro de zip do starter kit contém os ficheiros de configuração da organização e a chave de utilizador no `.chef` diretório.

O `organization-validator.pem` devem ser baixadas separadamente, porque é uma chave privada e chaves privadas não devem ser armazenadas no servidor do Chef. Partir [Chef gerir](https://manage.chef.io/), vá para a secção de administração e selecionar "Repor a chave de validação", que fornece um arquivo para download separadamente. Guarde o ficheiro c:\chef.

### <a name="configuring-your-chef-workstation"></a>Configurar a sua estação de trabalho do Chef

Extraia o conteúdo do chef-starter.zip para c:\chef.

Copiar todos os ficheiros no repositório do starter\chef chef\.chef para o seu diretório c:\chef.

Copiar o `organization-validator.pem` de ficheiros para c:\chef, se é guardado na c:\Downloads

O diretório deve agora ter um aspeto semelhante ao seguinte exemplo.

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

Agora, deve ter cinco ficheiros e quatro diretórios (incluindo o diretório do repositório de chef vazio) na raiz da c:\chef.

### <a name="edit-kniferb"></a>Editar knife.rb

Os ficheiros PEM contêm a sua organização e as chaves privadas administrativas para a comunicação e o ficheiro de knife.rb contém a configuração de knife. Precisamos de editar o ficheiro de knife.rb.

Abra o ficheiro de knife.rb num editor à sua escolha. O ficheiro inalterado deve ser algo semelhante:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Adicione as seguintes informações ao seu knife.rb:

validation_client_name   "myorg-validator"

validation_key           "#{current_dir}/myorg.pem"

knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"

knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

knife[:azure_client_secret] =     "#1234p$wdchef19"


Estas linhas garantirá que referencia o diretório de guias detalhados em c:\chef\cookbooks Knife e também utiliza o Principal de serviço do Azure que criou durante as operações do Azure.

O ficheiro de knife.rb deve agora ter um aspeto semelhante ao seguinte exemplo:

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

## <a name="install-chef-workstation"></a>Instalar a estação de trabalho do Chef

Em seguida, [transferir e instalar](https://downloads.chef.io/chef-workstation/) Chef estação de trabalho.
Instale a estação de trabalho do Chef a localização predefinida. Esta instalação pode demorar alguns minutos.

Na área de trabalho, verá uma "PowerShell CW", o que é um ambiente carregado com a ferramenta, que precisará para interagir com os produtos da Chef. O PowerShell CW faz com que os novos comandos ad hoc disponíveis, como `chef-run` comandos CLI do Chef, bem como tradicionais, como `chef`. Consulte a versão instalada do Chef estação de trabalho e as ferramentas do Chef com `chef -v`. Também pode verificar a sua versão de estação de trabalho ao selecionar "Sobre o Chef estação de trabalho" a partir da aplicação de estação de trabalho do Chef.

`chef --version` deverá devolver algo como:

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
> A ordem do caminho é importante! Se seus caminhos de opscode não estão na ordem correta, que terá problemas.
>

Reinicie a sua estação de trabalho antes de continuar.

### <a name="install-knife-azure"></a>Instalar Knife Azure

Este tutorial parte do princípio de que está a utilizar o Azure Resource Manager para interagir com a sua máquina virtual.

Instale a extensão Knife Azure. Esta opção fornece Knife com o "Azure Plug-in".

Execute o seguinte comando.

    chef gem install knife-azure ––pre

> [!NOTE]
> O argumento – pré garante que está recebendo a última versão RC do plug-in de Azure Knife que fornece acesso ao conjunto de APIs mais recentes.
>
>

É provável que um número de dependências também será instalado ao mesmo tempo.

![][8]

Para garantir que tudo está configurada corretamente, execute o seguinte comando.

    knife azurerm server list

Se tudo o que está configurado corretamente, verá uma lista de rolagem de imagens do Azure disponíveis por meio de.

Parabéns! A estação de trabalho é configurada!

## <a name="creating-a-cookbook"></a>Criação de um guia detalhado

Um guia detalhado é usado por Chef para definir um conjunto de comandos que deseja executar no seu cliente gerenciado. A criação de um guia detalhado é simples, basta utilizar o **chef cookbook de gerar** comando para gerar o modelo do manual. Este manual destina-se um servidor web que implementa automaticamente o IIS.

Em seu diretório C:\Chef execute o seguinte comando.

    chef generate cookbook webserver

Este comando gera um conjunto de ficheiros no diretório C:\Chef\cookbooks\webserver. Em seguida, defina o conjunto de comandos para o cliente do Chef executar na máquina virtual gerida.

Os comandos são armazenados em default.rb o ficheiro. Nesse arquivo, defina um conjunto de comandos que instala o IIS, inicia o IIS e copia um ficheiro de modelo para a pasta wwwroot.

Modificar o ficheiro de C:\chef\cookbooks\webserver\recipes\default.rb e adicione as seguintes linhas.

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

Quando tiver terminado, guarde o ficheiro.

## <a name="creating-a-template"></a>Criar um modelo
Neste passo, irá gerar um ficheiro de modelo para ser usada como a página default. HTML.

Execute o seguinte comando para gerar o modelo:

    chef generate template webserver Default.htm

Navegue para o `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` ficheiro. Edite o ficheiro ao adicionar um código de "Hello World" HTML simples e, em seguida, guarde o ficheiro.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Carregue o manual de para o servidor do Chef
Neste passo, fazer uma cópia do guia que tenha criado no computador local e carregá-lo para o servidor de alojado Chef. Depois de carregados, o Cookbook aparece sob o **política** separador.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Implementar uma máquina virtual com o Knife Azure
Implementar uma máquina virtual do Azure e aplique o manual de "Servidor Web", que instala o web service e o padrão página web do IIS.

Para fazer isso, utilize o **knife azurerm server criar** comando.

Um exemplo do comando apresentada a seguir.

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


O exemplo acima irá criar uma máquina de virtual Standard_DS2_v2 com o Windows Server 2016 instalado na região E.U.A. oeste. Substitua as variáveis específicas e executar.

> [!NOTE]
> Através da linha de comando, eu estou também automatizar minhas regras de filtro de rede do ponto final ao utilizar o parâmetro de pontos de extremidade – tcp. Posso abrir as portas 80 e a 3389 para fornecer acesso à página da web e sessão RDP.
>
>

Depois de executar o comando, vá ao portal do Azure para ver o seu computador começar a aprovisionar.

![][15]

O prompt de comando apresentada a seguir.

![][16]

Assim que a implementação estiver concluída, o endereço IP público da nova máquina virtual será apresentado o após a conclusão da implementação, pode copiar isso e cole-o num navegador da web e ver o site que tenha implementado. Quando implementar a máquina virtual, abrir a porta 80, pelo que deve ser disponível externamente.   

![][11]

Este exemplo utiliza o código HTML criativo.

Também pode ver o estado do nó [Chef gerir](https://manage.chef.io/). 

![][17]

Não se esqueça de que também pode ligar através de uma sessão RDP a partir do portal do Azure por meio da porta 3389.

Obrigado! Aceda e comece a infraestrutura como jornada de código com o Azure hoje mesmo!

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
