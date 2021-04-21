---
title: Tutorial - Implementar LÂMPADA e WordPress num VM
description: Neste tutorial, aprende-se a instalar a pilha LAMP, e o WordPress, numa máquina virtual Linux em Azure.
author: cynthn
ms.collection: linux
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.openlocfilehash: 5365bad5fdea2a8213defc103f0cdd966ebe50a5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816355"
---
# <a name="tutorial-install-a-lamp-stack-on-an-azure-linux-vm"></a>Tutorial: Instale uma pilha de lâmpadas num Azure Linux VM

Este artigo explica como implementar um servidor Web Apache, o MySQL e o PHP (pilha LAMP) numa VM do Ubuntu no Azure. Para ver o servidor LAMP em ação, opcionalmente, pode instalar e configurar um site do WordPress. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VM do Ubuntu 
> * Abrir a porta 80 para o tráfego da Web
> * Instalar o Apache, o MySQL e o PHP
> * Verificar a instalação e a configuração
> * Instalar o WordPress 

Esta configuração é para testes rápidos ou uma prova de conceito. Para obter mais informações sobre a pilha LAMP, incluindo as recomendações para um ambiente de produção, veja a [Documentação do Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

Este tutorial utiliza o CLI dentro da [Azure Cloud Shell](../../cloud-shell/overview.md), que é constantemente atualizado para a versão mais recente. Para abrir a Cloud Shell, selecione **Experimente-a** a partir da parte superior de qualquer bloco de código.

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com o comando [z vm create](/cli/azure/vm). 

O exemplo seguinte cria uma VM com o nome *myVM* e cria chaves SSH caso estas ainda não existam numa localização chave predefinida. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`. O comando também define *azureuser* como um nome de utilizador administrador. Utilize este nome mais tarde para ligar à VM. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Quando a VM tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo. Tome nota do `publicIpAddress`. Este endereço é utilizado para aceder à VM nos passos posteriores.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Abrir a porta 80 para o tráfego da Web 

Por predefinição, só são permitidas ligações SSH a VMs do Linux implementadas no Azure. Uma vez que esta VM vai ser um servidor Web, tem de abrir a porta 80 a partir da Internet. Utilize o comando [az vm de porta aberta](/cli/azure/vm) para abrir a porta desejada.  
 
```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

Para obter mais informações sobre a abertura de portas para o seu VM, consulte [portas Abertas.](nsg-quickstart.md)

## <a name="ssh-into-your-vm"></a>Aceder através de SSH à VM

Se ainda não conhece o endereço IP público do seu VM, execute o comando [da lista de ip público da rede Az.](/cli/azure/network/public-ip) Precisará deste endereço IP para vários passos posteriores.

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Utilize o seguinte comando para criar uma sessão SSH com a máquina virtual. Substitua o endereço IP público correto da sua máquina virtual. Neste exemplo, o endereço IP é *40.68.254.142*. *azureuser* é o nome de utilizador administrador que definiu quando criou a VM.

```bash
ssh azureuser@40.68.254.142
```


## <a name="install-apache-mysql-and-php"></a>Instalar o Apache, o MySQL e o PHP

Execute os comandos seguintes para atualizar as origens do pacote Ubuntu e instalar o Apache, o MySQL e o PHP. Tenha em atenção ao acento circunflexo (^) no final do comando, que faz parte do nome do pacote `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```

É-lhe pedido para instalar os pacotes e outras dependências. Este processo instala as extensões PHP mínimas necessárias para utilizar o PHP com o MySQL.  

## <a name="verify-apache"></a>Verificar Apache

Verifique a versão do Apache com o seguinte comando:
```bash
apache2 -v
```

Com o Apache instalado e a porta 80 aberta para a VM, torna-se possível aceder ao servidor Web a partir da Internet. Para a Página Predefinida do Apache2 Ubuntu, abra um browser e introduza o endereço IP público da VM. Utilize o endereço IP público que utilizou para encaminhar o SSH para a VM:

![Página predefinida do Apache][3]


## <a name="verify-and-secure-mysql"></a>Verifique e proteja o MySQL

Verifique a versão do MySQL com o seguinte comando (tenha em atenção o parâmetro `V` em maiúscula):

```bash
mysql -V
```

Para ajudar a garantir a instalação do MySQL, incluindo a definição de uma palavra-passe de raiz, execute o `mysql_secure_installation` script. 

```bash
sudo mysql_secure_installation
```

Pode configurar opcionalmente o Plugin de senha validado (recomendado). Em seguida, defina uma palavra-passe para o utilizador raiz MySQL e configufique as definições de segurança restantes para o seu ambiente. Recomendamos que responda "Y" (sim) a todas as perguntas.

Se pretender experimentar funcionalidades do MySQL (criar uma base de dados MySQL, adicionar utilizadores ou alterar as definições de configuração), inicie sessão no MySQL. Este passo não é necessário para concluir este tutorial.

```bash
sudo mysql -u root -p
```

Quando terminar, sair da linha de comandos do mysql, escrevendo `\q`.

## <a name="verify-php"></a>Verificar PHP

Verifique a versão do PHP com o seguinte comando:

```bash
php -v
```

Se pretender mais testes, crie uma página de informações do PHP rápida, para a ver num browser. O comando seguinte cria a página de informações do PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Agora pode verificar a página de informações do PHP que criou. Abra um browser e aceda a `http://yourPublicIPAddress/info.php`. Substitua o endereço IP público da sua VM. Deve ter um aspeto semelhante a esta imagem.

![Página de informações do PHP][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou um servidor LAMP no Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM do Ubuntu
> * Abrir a porta 80 para o tráfego da Web
> * Instalar o Apache, o MySQL e o PHP
> * Verificar a instalação e a configuração
> * Instalar o WordPress no servidor LAMP

Avance para o próximo tutorial para aprender a proteger servidores web com certificados TLS/SSL.

> [!div class="nextstepaction"]
> [Servidor web seguro com TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
