---
title: Implementação da máquina virtual do Azure com o Chef | Documentos da Microsoft
description: Saiba como utilizar o Chef para implementações de máquina de virtual automatizado e configuração no Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: jeconnoc
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: diviso
ms.openlocfilehash: 6659b49ecfbcb6bf571fccc258a65aa547f72f86
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453818"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatizar a implementação de máquinas virtuais do Azure com o Chef
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef é uma ótima ferramenta para o fornecimento de automatização e assim o desejar configurações de estado.

Com a versão mais recente do API na nuvem, a Chef fornece uma integração perfeita com o Azure, dando-lhe a capacidade de aprovisionar e implementar os Estados de configuração através de um único comando.

Neste artigo, vai configurar o ambiente do Chef para aprovisionar máquinas virtuais do Azure e percorrer a criar uma política ou "Manual" e, em seguida, implementar este manual de uma máquina virtual do Azure.

## <a name="chef-basics"></a>Noções básicas do chef
Antes de começar, [rever os conceitos básicos do Chef](http://www.chef.io/chef).

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

Crie um diretório chamado C:\chef.

Transferir o Azure PowerShell [definições de publicação](https://docs.microsoft.com/dynamics-nav/how-to--download-and-import-publish-settings-and-subscription-information).

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

O `organization-validator.pem` devem ser baixadas separadamente, porque é uma chave privada e chaves privadas não devem ser armazenadas no servidor do Chef. Partir [Chef gerir](https://manage.chef.io/) e selecione "Repor a chave de validação", que fornece um arquivo para download separadamente. Guarde o ficheiro c:\chef.

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

validation_client_name   "myorg-validator" validation_key           ""#{current_dir}/myorg.pem"

Adicione também o seguinte ficheiro de definições de publicação de linha que reflete o nome do seu Azure.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Modificar o "cookbook_path" ao remover o /.... / do caminho para que seja apresentado como:

    cookbook_path  ["#{current_dir}/cookbooks"]

Estas linhas garantirá que referencia o diretório de guias detalhados em c:\chef\cookbooks Knife e também utiliza o nosso arquivo de definições de publicação do Azure durante as operações do Azure.

O ficheiro de knife.rb deve agora ter um aspeto semelhante ao seguinte exemplo:

![][6]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

```rb
knife.rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "mynode"
client_key               "#{current_dir}/user.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
validation_client_name   "myorg-validator"
validation_key           ""#{current_dir}/myorg.pem"
cookbook_path            ["#{current_dir}/cookbooks"]
knife[:azure_publish_settings_file] = "yourfilename.publishsettings"
```

## <a name="install-chef-workstation"></a>Instalar a estação de trabalho do Chef

Em seguida, [transferir e instalar](https://downloads.chef.io/chef-workstation/) Chef estação de trabalho.
Instale a estação de trabalho do Chef a localização predefinida. Esta instalação pode demorar alguns minutos.

Na área de trabalho, verá uma "PowerShell CW", o que é um ambiente carregado com a ferramenta, que precisará para interagir com os produtos da Chef. O PowerShell CW faz com que os novos comandos ad hoc disponíveis, como `chef-run` comandos CLI do Chef, bem como tradicionais, como `chef`. Consulte a versão instalada do Chef estação de trabalho e as ferramentas do Chef com `chef -v`. Também pode verificar a sua versão de estação de trabalho ao selecionar "Sobre o Chef estação de trabalho" a partir da aplicação de estação de trabalho do Chef.

`chef --version` deverá devolver algo como:

```
Chef Workstation: 0.2.29
  chef-run: 0.2.2
  Chef Client: 14.6.47x
  delivery-cli: master (6862f27aba89109a9630f0b6c6798efec56b4efe)
  berks: 7.0.6
  test-kitchen: 1.23.2
  inspec: 3.0.12
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

    knife azure image list

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

Para fazer isso, utilize o **criar servidor do azure de knife** comando.

Um exemplo do comando apresentada a seguir.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Os parâmetros são facilmente compreensíveis. Substitua as variáveis específicas e executar.

> [!NOTE]
> Através da linha de comando, eu estou também automatizar minhas regras de filtro de rede do ponto final ao utilizar o parâmetro de pontos de extremidade – tcp. Posso abrir as portas 80 e a 3389 para fornecer acesso a minha página da web e sessão RDP.
>
>

Depois de executar o comando, vá ao portal do Azure para ver o seu computador começar a aprovisionar.

![][13]

O prompt de comando apresentada a seguir.

![][10]

Assim que a implementação estiver concluída, deve ser capaz de ligar ao serviço web através da porta 80, uma vez que abrir a porta quando aprovisionou a máquina virtual com o comando Knife Azure. Como esta máquina virtual é a única máquina de virtual neste serviço em nuvem, ligá-lo com o url do serviço cloud.

![][11]

Este exemplo utiliza o código HTML criativo.

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


<!--Link references-->
