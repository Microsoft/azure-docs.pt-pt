---
title: Tutorial - Personalize um Linux VM com nuvem em Azure
description: Neste tutorial, você aprende a usar cloud-init e Key Vault para personalizar Os VMs Linux na primeira vez que eles iniciam em Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2019
ms.author: cynthn
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 456c42dc0b25e168744ce283cddbd63b877813ab
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747158"
---
# <a name="tutorial---how-to-use-cloud-init-to-customize-a-linux-virtual-machine-in-azure-on-first-boot"></a>Tutorial – Como utilizar o cloud-init para personalizar uma máquina virtual do Linux no Azure no primeiro arranque

Num tutorial anterior, aprendeu a realizar SSH para uma máquina virtual (VM) e instalar manualmente o NGINX. Para criar VMs de forma rápida e consistente, normalmente pretende-se alguma forma de automatização. Uma abordagem comum para personalizar uma VM no primeiro arranque é utilizar o [cloud-init](https://cloudinit.readthedocs.io). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um ficheiro de configuração do cloud-init
> * Criar uma VM que utiliza um ficheiro cloud-init
> * Ver uma aplicação Node.js em execução depois de a VM ser criada
> * Utilizar o Key Vault para armazenar certificados
> * Automatizar implementações seguras do NGINX com o cloud-init

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="cloud-init-overview"></a>Descrição geral da inicialização de cloud
[Cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM com Linux quando arranca pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Como o cloud-init é executado durante o processo de arranque inicial, não existem passos adicionais ou agentes necessários a aplicar à configuração.

O cloud-init também funciona em distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. O cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativa para a distribuição que selecionar.

Estamos a trabalhar com os nossos parceiros para que o cloud-init seja incluído e fique operacional nas imagens que fornecem ao Azure. A tabela seguinte descreve a disponibilidade atual do cloud-init nas imagens de plataforma do Azure:

| Publisher | Oferta | SKU | Versão | nuvem-init pronto |
|:--- |:--- |:--- |:--- |:--- |
|Canónico |UbuntuServer |18.04-LTS |mais recente |yes | 
|Canónico |UbuntuServer |16.04-LTS |mais recente |yes | 
|Canónico |UbuntuServer |14.04.5-LTS |mais recente |yes |
|CoreOS |CoreOS |Estável |mais recente |yes |
|OpenLogic 7.6 |CentOS |7-CI |mais recente |preview |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |yes |
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 |preview |


## <a name="create-cloud-init-config-file"></a>Criar ficheiro de configuração do cloud-init
Para ver o cloud-init em ação, crie uma VM que instala o NGINX e executa uma aplicação Node.js “Hello World” simples. A seguinte configuração do cloud-init instala os pacotes necessários, cria uma aplicação Node.js e, em seguida, inicializa e lança a aplicação.

No seu pedido de festa ou na Cloud Shell, crie um ficheiro nomeado *cloud-init.txt* e cole a seguinte configuração. Por exemplo, `sensible-editor cloud-init.txt` escreva para criar o ficheiro e ver uma lista de editores disponíveis. Certifique-se de que o ficheiro de inicialização da cloud é copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Para obter mais informações sobre as opções de configuração do cloud-init, veja [exemplos de configuração do cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/examples.html).

## <a name="create-virtual-machine"></a>Criar a máquina virtual
Antes de poder criar uma VM, tem de criar um grupo de recursos com [az group create](/cli/azure/group#az-group-create). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupAutomate* na localização *eualeste* :

```azurecli-interactive
az group create --name myResourceGroupAutomate --location eastus
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). Utilize o parâmetro `--custom-data` para passar o ficheiro de configuração de inicialização da cloud. Forneça o caminho completo para a configuração do *cloud-init.txt* , se tiver guardado o ficheiro fora do diretório de trabalho atual. O exemplo a seguir cria um VM chamado *myVM:*

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myAutomatedVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Demora alguns minutos até a VM ser criada, os pacotes serem instalados e a aplicação ser iniciada. Existem tarefas em segundo plano que continuam em execução após a CLI do Azure o devolver à linha de comandos. Poderão ser necessários mais alguns minutos antes de poder aceder à aplicação. Quando a VM tiver sido criada, tome nota do `publicIpAddress` apresentado pela CLI do Azure. Este endereço é utilizado para aceder à aplicação Node.js num browser.

Para permitir que o tráfego da Web aceda à VM, abra a porta 80 a partir da Internet com [az vm open-port](/cli/azure/vm#az-vm-open-port):

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myAutomatedVM
```

## <a name="test-web-app"></a>Testar a aplicação Web
Agora pode abrir um navegador web e inserir *http: \/ \/ \<publicIpAddress>* na barra de endereços. Forneça o seu próprio endereço IP público a partir do processo de criação da VM. A aplicação Node.js é apresentada conforme mostrada no exemplo seguinte:

![Ver site NGINX em execução](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Inserir certificados a partir do Key Vault
Esta secção opcional mostra como pode, em segurança, armazenar certificados no Azure Key Vault e inseri-los durante a implementação de VMs. Em vez de utilizar uma imagem personalizada que inclui os certificados integrados, este processo garante que os certificados mais atualizados são inseridos numa VM no primeiro arranque. Durante o processo, o certificado nunca sai da plataforma do Azure nem é exposto num script, histórico de linha de comandos ou modelo.

O Azure Key Vault salvaguarda as chaves criptográficas e os segredos, como os certificados ou as palavras-passe. O Key Vault ajuda a simplificar o processo de gestão de chaves e permite-lhe manter o controlo de chaves que acedem aos seus dados e os encriptam. Este cenário apresenta alguns conceitos do Key Vault para criar e utilizar um certificado, embora não seja uma descrição geral exaustiva de como utilizar o Key Vault.

Os passos seguintes mostram como pode:

- Criar um Azure Key Vault
- Gerar ou carregar um certificado para o Key Vault
- Criar um segredo do certificado para inserir numa VM
- Criar uma VM e inserir o certificado

### <a name="create-an-azure-key-vault"></a>Criar um Azure Key Vault
Em primeiro lugar, crie um Key Vault com [az keyvault create](/cli/azure/keyvault#az-keyvault-create) e ative-o para utilização quando implementar uma VM. Cada Key Vault requer um nome exclusivo com todas as letras minúsculas. Substitua *mykeyvault* no exemplo seguinte pelo nome exclusivo do seu Key Vault:

```azurecli-interactive
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Gerar um certificado e armazená-lo no Key Vault
Para efeitos de produção, deve importar um certificado válido assinado por um fornecedor fidedigno com [az keyvault certificate import](/cli/azure/keyvault/certificate#az-keyvault-certificate-import). Para este tutorial, o exemplo seguinte mostra como pode gerar um certificado autoassinado com [az keyvault certificate create](/cli/azure/keyvault/certificate#az-keyvault-certificate-create) que utiliza a política de certificado predefinida:

```azurecli-interactive
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy --output json)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Preparar um certificado para utilização numa VM
Para utilizar o certificado durante o processo de criação da VM, obtenha o ID do certificado com [az keyvault secret list-versions](/cli/azure/keyvault/secret#az-keyvault-secret-list-versions). A VM precisa do certificado num determinado formato para o inserir no arranque, por isso, converta o certificado com [az vm secret format](/cli/azure/vm). O exemplo seguinte atribui o resultado destes comandos a variáveis para uma utilização mais fácil nos passos que se seguem:

```azurecli-interactive
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secret "$secret" --output json)
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Criar uma configuração do cloud-init para proteger o NGINX
Quando criar uma VM, os certificados e as chaves são armazenados no diretório */var/lib/waagent/* protegido. Para automatizar a adição do certificado à VM e configurar o NGINX, pode utilizar uma configuração atualizada de cloud-init do exemplo anterior.

Crie um ficheiro com o nome *cloud-init-secured.txt* e cole a seguinte configuração. Se utilizar o Cloud Shell, crie o ficheiro config de nuvem lá e não na sua máquina local. Por exemplo, `sensible-editor cloud-init-secured.txt` escreva para criar o ficheiro e ver uma lista de editores disponíveis. Certifique-se de que o ficheiro de inicialização da cloud é copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Criar uma VM segura
Agora, crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). Os dados do certificado são inseridos a partir do Key Vault com o parâmetro `--secrets`. Como no exemplo anterior, também transmite a configuração do cloud-init com o parâmetro `--custom-data`:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

Demora alguns minutos até a VM ser criada, os pacotes serem instalados e a aplicação ser iniciada. Existem tarefas em segundo plano que continuam em execução após a CLI do Azure o devolver à linha de comandos. Poderão ser necessários mais alguns minutos antes de poder aceder à aplicação. Quando a VM tiver sido criada, tome nota do `publicIpAddress` apresentado pela CLI do Azure. Este endereço é utilizado para aceder à aplicação Node.js num browser.

Para permitir que o tráfego da Web seguro aceda à VM, abra a porta 443 a partir da Internet com [az vm open-port](/cli/azure/vm#az-vm-open-port):

```azurecli-interactive
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --port 443
```

### <a name="test-secure-web-app"></a>Testar uma aplicação Web segura
Agora pode abrir um navegador web e inserir *https: \/ \/ \<publicIpAddress>* na barra de endereços. Forneça o seu próprio endereço IP público conforme apresentado na saída do processo de criação da VM anterior. Aceite o aviso de segurança se utilizou um certificado autoassinado:

![Aceitar o aviso de segurança do browser](./media/tutorial-automate-vm-deployment/browser-warning.png)

O site NGINX protegido e a aplicação Node.js são apresentados como no exemplo seguinte:

![Ver site NGINX seguro em execução](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou as VMs no primeiro arranque com o cloud-init. Aprendeu a:

> [!div class="checklist"]
> * Criar um ficheiro de configuração do cloud-init
> * Criar uma VM que utiliza um ficheiro cloud-init
> * Ver uma aplicação Node.js em execução depois de a VM ser criada
> * Utilizar o Key Vault para armazenar certificados
> * Automatizar implementações seguras do NGINX com o cloud-init

Avance para o tutorial seguinte para aprender a criar imagens de VM personalizadas.

> [!div class="nextstepaction"]
> [Criar imagens de VM personalizadas](./tutorial-custom-images.md)
