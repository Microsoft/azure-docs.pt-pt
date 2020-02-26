---
title: Quickstart - Configure uma máquina virtual Windows em Azure usando chef
description: Neste arranque rápido, você aprende a usar chef para implementar e configurar uma máquina virtual Windows em Azure
keywords: chef, azul, devops, máquina virtual
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590070"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Quickstart - Configure uma máquina virtual Windows em Azure usando chef

Chef permite-lhe entregar automação e configurações de estado desejadas.

Com o mais recente lançamento da Cloud API, o Chef proporciona uma integração perfeita com o Azure, dando-lhe a capacidade de fornecer e implementar estados de configuração através de um único comando.

Neste artigo, você configura o seu ambiente chef para fornecer máquinas virtuais Azure e caminhar através da criação de uma política ou livro de receitas e, em seguida, implementar este livro de receitas para uma máquina virtual Azure.

## <a name="chef-basics"></a>Básicos de chef

Antes de começar com este artigo, [reveja os conceitos básicos do Chef.](https://www.chef.io/chef)

O diagrama seguinte mostra a arquitetura chef de alto nível.

![Arquitetura de chef](media/chef-automation/chef-architecure.png)

Chef tem três componentes arquitetônicos principais: 
- Chef Server - O ponto de gestão e existem duas opções para o Chef Server: uma solução hospedada ou uma solução no local.
- Chef Client (nó) - O agente que se senta nos servidores que está a gerir.
- Chef Workstation - O nome tanto para a estação de trabalho de administração (onde você cria políticas e executa comandos de gestão) e o pacote de software de ferramentas Chef.

Geralmente, você vê **a sua estação** de trabalho como o local onde você executa comandos e **Chef Workstation** para o pacote de software.

Por exemplo, você descarrega o comando da faca como parte da **Estação**de Trabalho do Chef, mas você executa comandos de faca da **sua estação** de trabalho para gerir infraestruturas.

Chef também usa os conceitos de livros de *receitas* e *receitas.* Estes termos são as políticas que são definidas e aplicadas aos servidores, respectivamente.

## <a name="preparing-your-workstation"></a>Preparação do seu posto de trabalho

Primeiro, prepare a sua estação de trabalho criando um diretório para armazenar ficheiros de configuração do Chef e livros de receitas.

Crie um diretório com o nome `C:\Chef`.

Faça o download e instale a mais recente versão [Do ClI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) na sua estação de trabalho.

## <a name="configure-azure-service-principal"></a>Configurar o Principal de Serviço do Azure

Vamos usar um Diretor de Serviço para nos ajudar a criar recursos Azure a partir da nossa Estação de Trabalho chef.  Para criar o Diretor de Serviço relevante com as permissões necessárias, execute os seguintes comandos dentro da PowerShell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Tome nota do seu IDeI de Subscrição, TenantID, ClientID e Client Secret (a palavra-passe que definiu anteriormente neste tutorial) pois necessitará destes valores. 

## <a name="setup-chef-server"></a>Configurar Chef Server

Este guia assume que você vai se inscrever para chef hospedado.

Se ainda não está a usar um Chef Server, pode:

* Inscreva-se no [Chef Hospedado,](https://manage.chef.io/signup)que é a maneira mais rápida de começar com chef.
* Instale um Chef Server autónomo na máquina baseada em linux, seguindo as instruções de [instalação](https://docs.chef.io/install_server.html) do [Chef Docs.](https://docs.chef.io/)

### <a name="creating-a-hosted-chef-account"></a>Criar uma conta de Chef Hospedado

Inscreva-se para uma conta de Chef Hospedado [aqui.](https://manage.chef.io/signup)

Durante o processo de inscrição, ser-lhe-á pedido que crie uma nova organização.

![Criar janela de organização](media/chef-automation/create-organization.png)

Assim que a sua organização for criada, descarregue o kit de arranque.

![Chef de configuração](media/chef-automation/configure-chef.png)

> [!NOTE]
> Se receber um aviso rápido de que as suas chaves serão redefinidas, não há problema em prosseguir, uma vez que ainda não temos nenhuma infraestrutura existente configurada.
>

Este ficheiro zip do kit de arranque contém os ficheiros de configuração da sua organização e a chave do utilizador no diretório `.chef`.

O `organization-validator.pem` deve ser descarregado separadamente, porque é uma chave privada e as chaves privadas não devem ser armazenadas no Chef Server. A partir do [Chef Manage,](https://manage.chef.io/)entre na secção Administração e selecione "Chave de Validação de Reset", que fornece um ficheiro para que possa descarregar separadamente. Guarde o arquivo para c:\chef.

### <a name="configuring-your-chef-workstation"></a>Configurar a sua estação de trabalho chef

Extrair o conteúdo do `chef-starter.zip` para `c:\chef`.

Copie todos os ficheiros sob `chef-starter\chef-repo\.chef` para o seu diretório `c:\chef`.

Copie o ficheiro `organization-validator.pem` para `c:\chef`, se for guardado em `c:\Downloads`.

O seu diretório deve agora parecer-se com o seguinte exemplo.

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

Você deve agora ter cinco arquivos e quatro diretórios (incluindo o diretório de chef-repo vazio) na raiz do c:\chef.

### <a name="edit-kniferb"></a>Editar faca.rb

Os ficheiros PEM contêm a sua organização e chaves administrativas privadas para comunicação e o ficheiro faca.rb contém a configuração da sua faca. Precisamos editar o ficheiro faca.rb.

Abra o ficheiro faca.rb no editor da sua escolha. O ficheiro inalterado deve parecer algo como:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Adicione as seguintes informações à sua faca.rb, substituindo os espaços reservados pelas suas informações:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Estas linhas assegurarão que a Faca faz referência ao diretório de livros de receitas sob `c:\chef\cookbooks`.

O seu ficheiro `knife.rb` deve agora parecer semelhante ao seguinte exemplo:

![Exemplo de arquivo de faca](./media/chef-automation/knife-file-example.png)

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

## <a name="install-chef-workstation"></a>Instalar estação de trabalho chef

Em seguida, [descarregue e instale o Chef Workstation.](https://downloads.chef.io/chef-workstation/)

Instale o Chef Workstation na localização padrão.

No ambiente de trabalho, verá um CW PowerShell. Esta ferramenta é usada para interagir com os produtos Chef. O CW PowerShell disponibiliza novos comandos, como comandos `chef-run` e Chef CLI (como `chef`). Consulte a sua versão instalada do Chef Workstation e as ferramentas chef com `chef -v`. Você também pode verificar sua versão Workstation selecionando **Sobre chef workstation** da Chef Workstation App.

`chef --version` deve devolver algo como:

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
> A ordem do caminho é importante! Se os seus caminhos de opscode não estiverem na ordem correta, resultarão problemas.
>

Reinicie o seu posto de trabalho antes de continuar.

### <a name="install-knife-azure"></a>Instalar faca Azure

Este tutorial assume que está a usar o Gestor de Recursos Azure para interagir com a sua máquina virtual.

Instale a extensão Knife Azure, que inclui o Plugin Azure.

Executar o seguinte comando.

    chef gem install knife-azure ––pre

> [!NOTE]
> O argumento `–-pre` garante que está a receber a mais recente versão RC do Knife Azure Plugin que dá acesso ao mais recente conjunto de APIs.
>
>

É provável que várias dependências também sejam instaladas ao mesmo tempo.

![Saída da instalação de azul-faca](./media/chef-automation/install-knife-azure.png)

Para garantir que tudo está configurado corretamente, execute o seguinte comando.

    knife azurerm server list

Se tudo estiver configurado corretamente, verá uma lista de imagens Azure disponíveis a percorrer.

Parabéns! O seu posto de trabalho está pronto!

## <a name="creating-a-cookbook"></a>Criar um livro de receitas

Um livro de receitas é usado pelo Chef para definir um conjunto de comandos que você deseja executar sobre o seu cliente gerido. Criar um livro de receitas é simples, basta usar o comando `chef generate cookbook` para gerar o modelo de livro de receitas. Este livro de receitas é para um servidor web que implementa automaticamente o IIS.

Sob o seu `C:\Chef directory`, corra o seguinte comando.

    chef generate cookbook webserver

Este comando gera um conjunto de ficheiros sob o diretório C:\Chef\cookbooks\webserver. Em seguida, defina o conjunto de comandos para o cliente Chef correr na máquina virtual gerida.

Os comandos são armazenados no predefinido do ficheiro.rb. Neste ficheiro, defina um conjunto de comandos que instala o IIS, inicia o IIS e copia um ficheiro de modelo para a pasta `wwwroot`.

Modifique os c:\chef\cookbooks\webserver\recipes\default.rb file e adicione as seguintes linhas.

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

Guarde o ficheiro assim que terminar.

## <a name="creating-a-template"></a>Criando um modelo

Neste passo, você gerará um ficheiro de modelo para usar como a página `default.html`.

Executar o seguinte comando para gerar o modelo:

    chef generate template webserver Default.htm

Navegue para o ficheiro `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Editar o ficheiro adicionando um simples código *Hello World* HTML e, em seguida, guardar o ficheiro.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Faça upload do livro de receitas para o Chef Server

Neste passo, você faz uma cópia do livro de receitas que você criou na máquina local e carregá-lo para o Chef Hosted Server. Uma vez carregado, o livro de receitas aparece sob o separador **Política.**

    knife cookbook upload webserver

![Resultados da instalação de livro de receitas para o Chef Server](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Implante uma máquina virtual com Knife Azure

Implante uma máquina virtual Azure e aplique o livro de receitas `Webserver` utilizando o comando `knife`.

O comando `knife` também instalará o serviço web IIS e a página web predefinida.

```bash
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
```

O exemplo de comando `knife` cria uma máquina virtual *Standard_DS2_v2* com o Windows Server 2016 instalado na região dos EUA Ocidentais. Modifique estes valores de acordo com as necessidades da sua aplicação.

Depois de executar o comando, navegue até ao portal Azure para ver a sua máquina começar a fornecer.

![Máquina virtual sendo aprovisionada](./media/chef-automation/virtual-machine-being-provisioned.png)

O pedido de comando aparece a seguir.

![Saída de faca ao criar máquina virtual](./media/chef-automation/knife-output-when-creating-vm.png)

Uma vez concluída a implantação, é apresentado o endereço IP público da nova máquina virtual. Repasse este valor num navegador web para ver o novo website. Quando implantámos a máquina virtual, abrimos a porta 80 para que estivesse disponível externamente.   

![Testar a máquina virtual](./media/chef-automation/testing-the-virtual-machine.png)

Este exemplo utiliza código HTML criativo.

Você também pode ver o estado do nó [Chef Gerir.](https://manage.chef.io/) 

![Visualizando o estado do nó](./media/chef-automation/viewing-node-status.png)

Não se esqueça que também pode ligar através de uma sessão de RDP a partir do portal Azure através da porta 3389.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Chef em Azure](/azure/chef/)