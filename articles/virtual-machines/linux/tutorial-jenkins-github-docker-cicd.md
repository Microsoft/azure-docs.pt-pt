---
title: Tutorial – Criar um pipeline de desenvolvimento no Azure com o Jenkins | Microsoft Docs
description: Tutorial – Neste tutorial, vai aprender a criar uma máquina virtual do Jenkins no Azure que solicita dados do GitHub em cada consolidação de código e cria um novo contentor do Docker para executar a aplicação.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6c6510113710ea19128fcd27adbf8671a8f083bc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996500"
---
# <a name="tutorial-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>Tutorial: Criar uma infraestrutura de desenvolvimento numa VM do Linux no Azure com Jenkins, GitHub e Docker

Para automatizar a fase de criação e teste do desenvolvimento de aplicações, pode utilizar um pipeline de integração e implementação (CI/CD) contínuas. Neste tutorial, vai criar um pipeline de CI/CD numa VM do Azure, incluindo como:

> [!div class="checklist"]
> * Criar uma VM do Jenkins
> * Instalar e configurar o Jenkins
> * Criar a integração de webhooks entre o GitHub e Jenkins
> * Criar e acionar tarefas de compilação do Jenkins a partir de consolidações do GitHub
> * Criar uma imagem do Docker para a aplicação
> * Verificar se as consolidações do GitHub criam uma nova imagem do Docker e atualizam a aplicação em execução

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-jenkins-instance"></a>Criar instância do Jenkins
Num tutorial anterior sobre [Como personalizar uma máquina virtual do Linux no primeiro arranque](tutorial-automate-vm-deployment.md), aprendeu a automatizar a personalização de VMs com inicialização da cloud. Este tutorial utiliza um ficheiro de inicialização da cloud para instalar o Jenkins e o Docker numa VM. O Jenkins é um servidor de automatização de código aberto popular, que se integra totalmente no Azure para permitir a integração contínua (CI) e a entrega contínua (CD). Para obter mais tutoriais sobre como utilizar o Jenkins, veja o [Jenkins no hub do Azure](https://docs.microsoft.com/azure/jenkins/).

Na sua shell atual, crie um ficheiro com o nome *cloud-init-jenkins.txt* e cole a seguinte configuração. Por exemplo, crie o ficheiro no Cloud Shell, não no seu computador local. Introduza `sensible-editor cloud-init-jenkins.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Certifique-se de que o ficheiro de inicialização da cloud é copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - apt install openjdk-8-jre-headless -y
  - wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
  - sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

Antes de poder criar uma VM, tem de criar um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupJenkins* na localização *eualeste*:

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Agora, crie uma VM com [az vm create](/cli/azure/vm). Utilize o parâmetro `--custom-data` para passar o ficheiro de configuração de inicialização da cloud. Forneça o caminho completo para *cloud-init-jenkins.txt*, se tiver guardado o ficheiro fora do diretório de trabalho atual.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

Demora alguns minutos até que a VM seja criada e configurada.

Para permitir que o tráfego Web alcance a sua VM, utilize [az vm open-port](/cli/azure/vm) para abrir a porta *8080* para o tráfego do Jenkins e a porta *1337* para a aplicação Node.js que é utilizada para executar uma aplicação de exemplo:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Configurar o Jenkins
Para aceder à sua instância do Jenkins, obtenha o endereço IP público da sua VM:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Por motivos de segurança, tem de introduzir a palavra-passe de administrador inicial que é armazenada num ficheiro de texto na VM para iniciar a instalação do Jenkins. Utilize o endereço IP público que obteve no passo anterior para encaminhar o SSH para a VM:

```bash
ssh azureuser@<publicIps>
```

Verifique se o Jenkins é executada com o `service` comando:

```bash
$ service jenkins status
● jenkins.service - LSB: Start Jenkins at boot time
   Loaded: loaded (/etc/init.d/jenkins; generated)
   Active: active (exited) since Tue 2019-02-12 16:16:11 UTC; 55s ago
     Docs: man:systemd-sysv-generator(8)
    Tasks: 0 (limit: 4103)
   CGroup: /system.slice/jenkins.service

Feb 12 16:16:10 myVM systemd[1]: Starting LSB: Start Jenkins at boot time...
...
```

Veja o `initialAdminPassword` da sua instalação do Jenkins e copie-o:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Se o ficheiro ainda não estiver disponível, aguarde alguns minutos para que a inicialização da cloud conclua a instalação do Jenkins e do Docker.

Agora, abra um browser e vá para `http://<publicIps>:8080`. Conclua a configuração inicial do Jenkins da seguinte forma:

- Escolha **Selecionar plug-ins a instalar**
- Procure *GitHub* na caixa de texto na parte superior. Marque a caixa de *GitHub* e, em seguida, selecione **Instalar**
- Crie o primeiro utilizador administrador. Introduza um nome de utilizador, como **administrador** e forneça a sua palavra-passe segura. Por fim, escreva um nome completo e o endereço de e-mail.
- Selecionar **Guardar e Concluir**
- Assim que o Jenkins estiver pronto, selecione **Começar a utilizar o Jenkins**
  - Se o browser apresentar uma página em branco quando começar a utilizar o Jenkins, reinicie o serviço Jenkins. A partir da sua sessão SSH, escreva `sudo service jenkins restart` e, em seguida, atualize o browser.
- Se for necessário, inicie sessão no Jenkins com o nome de utilizador e palavra-passe que criou.


## <a name="create-github-webhook"></a>Criar um webhook do GitHub
Para configurar a integração com o GitHub, abra a [aplicação de exemplo Node.js Hello World](https://github.com/Azure-Samples/nodejs-docs-hello-world) do repositório de exemplos do Azure. Para bifurcar o repositório para a sua própria conta do GitHub, selecione o botão **Fork** no canto superior direito.

Crie um webhook no interior do fork que criou:

- Selecione **configurações**, em seguida, selecione **Webhooks** no lado esquerdo.
- Escolher **adicionar webhook**, em seguida, introduza *Jenkins* na caixa Filtro.
- Para o **URL de Payload**, introduza `http://<publicIps>:8080/github-webhook/`. Certifique-se de que inclui / à direita
- Para **tipo de conteúdo**, selecione *application/x-www-form-urlencoded*.
- Para **que eventos deseja acionar este webhook?**, selecione *apenas o evento de push.*
- Definir **Active Directory** como marcada.
- Clique em **adicionar webhook**.

![Adicionar webhook do GitHub ao seu repositório bifurcado](media/tutorial-jenkins-github-docker-cicd/github_webhook.png)


## <a name="create-jenkins-job"></a>Criar tarefa do Jenkins
Para que o Jenkins responda a um evento no GitHub, como consolidar código, crie uma tarefa do Jenkins. Utilize os URLs do seu próprio fork do GitHub.

No seu site do Jenkins, selecione **Criar novas tarefas** na home page:

- Introduza *HelloWorld* no nome da tarefa. Escolha **Projeto de estilo livre** e selecione **OK**.
- Na secção **Geral**, selecione o projeto **GitHub** e introduza o URL do repositório bifurcado, como *https://github.com/cynthn/nodejs-docs-hello-world*
- Na secção  **Gestão de código fonte**, selecione o projeto **Git** e introduza o URL *.git* do repositório bifurcado, como *https://github.com/cynthn/nodejs-docs-hello-world.git*
- Na secção **Criar Acionadores**, selecione **Acionador de hook do GitHub para consulta GITScm**.
- Na secção **Compilar**, escolha **Adicionar passo de compilação**. Selecione **Executar shell** e introduza `echo "Test"` na janela de comandos.
- Selecione **Guardar** na parte inferior da janela de tarefas.


## <a name="test-github-integration"></a>Testar integração do GitHub
Para testar a integração do GitHub no Jenkins, consolide uma alteração no seu fork. 

Na IU Web do GitHub, selecione o repositório bifurcado e selecione o ficheiro **index.js**. Selecione o ícone de lápis para editar este ficheiro, de forma que a linha 6 seja:

```javascript
response.end("Hello World!");
```

Para consolidar as alterações, selecione o botão **Consolidar alterações** na parte inferior.

No Jenkins, uma nova compilação é iniciada na secção **Histórico de compilações** do canto inferior esquerdo da página de tarefas. Escolha a ligação do número da compilação e selecione **Saída da consola** no lado esquerdo. Pode ver os passos que o Jenkins executa, à medida que o seu código é obtido do GitHub e a ação de compilação gera a saída da mensagem `Test` para a consola. Sempre que uma consolidação é efetuada no GitHub, o webhook acede ao Jenkins e aciona uma nova compilação desta forma.


## <a name="define-docker-build-image"></a>Definir imagem de compilação do Docker
Para ver a aplicação Node.js em execução com base nas suas consolidações do GitHub, vamos construir uma imagem do Docker para executar a aplicação. A imagem é criada a partir de um Dockerfile que define como configurar o contentor que executa a aplicação. 

A partir da ligação SSH à sua VM, mude para o diretório de área de trabalho do Jenkins que tem o nome da tarefa que criou no passo anterior. Neste exemplo, foi-lhe atribuído o nome *HelloWorld*.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Crie um ficheiro neste diretório de área de trabalho com `sudo sensible-editor Dockerfile` e cole os seguintes conteúdos. Certifique-se de que todo o Dockerfile é copiado corretamente, especialmente a primeira linha:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

Este Dockerfile utiliza a imagem base do Node.js com Alpine Linux, expõe a porta 1337 na qual a aplicação Hello World é executada e, em seguida, copia os ficheiros da aplicação e inicializa-a.


## <a name="create-jenkins-build-rules"></a>Criar regras de compilação do Jenkins
Num passo anterior, criou uma regra de compilação básica do Jenkins que gera a saída de uma mensagem para a consola. Vamos criar o passo de compilação para utilizar o nosso Dockerfile e executar a aplicação.

Novamente na instância do Jenkins, selecione a tarefa que criou no passo anterior. Selecione **Configurar** no lado esquerdo e desloque-se para baixo até à secção **Compilar**:

- Remova o passo de compilação `echo "Test"` existente. Selecione a cruz vermelha no canto superior direito da caixa de passos de compilação existente.
- Escolha **Adicionar passo de compilação** e selecione **Executar shell**
- Na caixa **Comando**, introduza os seguintes comandos do Docker e selecione **Guardar**:

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Os passos de compilação do Docker criam uma imagem e identificam-na com o número de compilação do Jenkins para que possa manter um histórico de imagens. Quaisquer contentores existentes em execução na aplicação são parados e, em seguida, removidos. Um novo contentor é então iniciado com a imagem e executa a aplicação Node.js com base nas consolidações mais recentes no GitHub.


## <a name="test-your-pipeline"></a>Testar o pipeline
Para ver todo o pipeline em ação, edite novamente o ficheiro *index.js* no seu repositório bifurcado do GitHub e selecione **Consolidar alteração**. Uma nova tarefa é iniciada no Jenkins, com base no webhook do GitHub. Demora alguns segundos para criar a imagem do Docker e iniciar a sua aplicação num novo contentor.

Se for necessário, obtenha novamente o endereço IP público da sua VM:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Abra um browser e introduza `http://<publicIps>:1337`. A aplicação Node.js é apresentada e reflete as consolidações mais recentes no fork do GitHub da seguinte forma:

![Executar aplicação Node.js](media/tutorial-jenkins-github-docker-cicd/running_nodejs_app.png)

Agora, efetue outra edição no ficheiro *index.js* no GitHub e consolide a alteração. Aguarde alguns segundos até que a tarefa seja concluída no Jenkins e, em seguida, atualize o browser para ver a versão atualizada da sua aplicação em execução num novo contentor da seguinte forma:

![Executar a aplicação Node.js após outra consolidação do GitHub](media/tutorial-jenkins-github-docker-cicd/another_running_nodejs_app.png)


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, configurou o GitHub para executar uma tarefa de compilação do Jenkins em cada consolidação de código e, em seguida, implementar um contentor do Docker para testar a aplicação. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM do Jenkins
> * Instalar e configurar o Jenkins
> * Criar a integração de webhooks entre o GitHub e Jenkins
> * Criar e acionar tarefas de compilação do Jenkins a partir de consolidações do GitHub
> * Criar uma imagem do Docker para a aplicação
> * Verificar se as consolidações do GitHub criam uma nova imagem do Docker e atualizam a aplicação em execução

Avance para o próximo tutorial para saber mais sobre como integrar o Jenkins no Azure DevOps Services.

> [!div class="nextstepaction"]
> [Implementar aplicações com o Jenkins e o Azure DevOps Services](tutorial-build-deploy-jenkins.md)
