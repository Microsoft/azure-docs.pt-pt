---
title: Tutorial – implantar a lâmpada em uma máquina virtual do Linux no Azure
description: Neste tutorial, vai aprender a instalar a pilha LAMP numa máquina virtual do Linux no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 3b1f4ef9d4e36c35cc72716125392aaff05eab6d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034461"
---
# <a name="tutorial-install-a-lamp-web-server-on-a-linux-virtual-machine-in-azure"></a>Tutorial: Instalar um servidor Web da LAMP numa máquina virtual do Linux no Azure

Este artigo explica como implementar um servidor Web Apache, o MySQL e o PHP (pilha LAMP) numa VM do Ubuntu no Azure. Se preferir o servidor Web NGINX, veja o tutorial [Pilha LEMP](tutorial-lemp-stack.md). Para ver o servidor LAMP em ação, opcionalmente, pode instalar e configurar um site do WordPress. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VM do Ubuntu (o “L” na pilha LAMP)
> * Abrir a porta 80 para o tráfego da Web
> * Instalar o Apache, o MySQL e o PHP
> * Verificar a instalação e a configuração
> * Instalar o WordPress no servidor LAMP

Esta configuração é para testes rápidos ou uma prova de conceito. Para obter mais informações sobre a pilha LAMP, incluindo as recomendações para um ambiente de produção, veja a [Documentação do Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

Este tutorial usa a CLI dentro do [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), que é constantemente atualizado para a versão mais recente. Para abrir o Cloud Shell, selecione **Experimente** na parte superior de qualquer bloco de código.

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Instalar o Apache, o MySQL e o PHP

Execute os comandos seguintes para atualizar as origens do pacote Ubuntu e instalar o Apache, o MySQL e o PHP. Tenha em atenção ao acento circunflexo (^) no final do comando, que faz parte do nome do pacote `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```

É-lhe pedido para instalar os pacotes e outras dependências. Este processo instala as extensões PHP mínimas necessárias para utilizar o PHP com o MySQL.  

## <a name="verify-installation-and-configuration"></a>Verificar a instalação e a configuração


### <a name="verify-apache"></a>Verificar Apache

Verifique a versão do Apache com o seguinte comando:
```bash
apache2 -v
```

Com o Apache instalado e a porta 80 aberta para a VM, torna-se possível aceder ao servidor Web a partir da Internet. Para a Página Predefinida do Apache2 Ubuntu, abra um browser e introduza o endereço IP público da VM. Utilize o endereço IP público que utilizou para encaminhar o SSH para a VM:

![Página predefinida do Apache][3]


### <a name="verify-and-secure-mysql"></a>Verificar e proteger o MySQL

Verifique a versão do MySQL com o seguinte comando (tenha em atenção o parâmetro `V` em maiúscula):

```bash
mysql -V
```

Para ajudar a proteger a instalação do MySQL, incluindo a definição de uma senha raiz, execute o script `mysql_secure_installation`. 

```bash
sudo mysql_secure_installation
```

Opcionalmente, você pode configurar o plug-in de validação de senha (recomendado). Em seguida, defina uma senha para o usuário raiz do MySQL e defina as configurações de segurança restantes para o seu ambiente. Recomendamos que você responda "Y" (Sim) a todas as perguntas.

Se pretender experimentar funcionalidades do MySQL (criar uma base de dados MySQL, adicionar utilizadores ou alterar as definições de configuração), inicie sessão no MySQL. Este passo não é necessário para concluir este tutorial.

```bash
sudo mysql -u root -p
```

Quando terminar, sair da linha de comandos do mysql, escrevendo `\q`.

### <a name="verify-php"></a>Verificar PHP

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

Avance para o tutorial seguinte para aprender a proteger os servidores Web com certificados SSL.

> [!div class="nextstepaction"]
> [Proteger o servidor Web com SSL](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
