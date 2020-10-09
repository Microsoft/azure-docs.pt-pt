---
title: Tutorial - Desdobre a LÂMPADA numa máquina virtual Linux em Azure
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
ms.openlocfilehash: d0d86e1a9c40eb6860508cf136ab9d466cc28ecd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88225908"
---
# <a name="tutorial-install-a-lamp-web-server-on-a-linux-virtual-machine-in-azure"></a>Tutorial: Instalar um servidor Web da LAMP numa máquina virtual do Linux no Azure

Este artigo explica como implementar um servidor Web Apache, o MySQL e o PHP (pilha LAMP) numa VM do Ubuntu no Azure. Para ver o servidor LAMP em ação, opcionalmente, pode instalar e configurar um site do WordPress. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VM do Ubuntu (o “L” na pilha LAMP)
> * Abrir a porta 80 para o tráfego da Web
> * Instalar o Apache, o MySQL e o PHP
> * Verificar a instalação e a configuração
> * Instalar o WordPress no servidor LAMP

Esta configuração é para testes rápidos ou uma prova de conceito. Para obter mais informações sobre a pilha LAMP, incluindo as recomendações para um ambiente de produção, veja a [Documentação do Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

Este tutorial utiliza o CLI dentro da [Azure Cloud Shell](../../cloud-shell/overview.md), que é constantemente atualizado para a versão mais recente. Para abrir a Cloud Shell, selecione **Experimente-a** a partir da parte superior de qualquer bloco de código.

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

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


### <a name="verify-and-secure-mysql"></a>Verifique e proteja o MySQL

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

Avance para o próximo tutorial para aprender a proteger servidores web com certificados TLS/SSL.

> [!div class="nextstepaction"]
> [Servidor web seguro com TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
