---
title: Aceda ao painel de controlo Avere vFXT - Azure
description: Como ligar ao cluster vFXT e ao painel de controlo Avere baseado no navegador para configurar o Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 69395a27500b8e7bd066536b367b8e6cb385602c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85505618"
---
# <a name="access-the-vfxt-cluster"></a>Aceder ao cluster vFXT

Para ajustar as definições do cluster e monitorizar o cluster, utilize o Painel de Controlo Avere. Avere Control Panel é uma interface gráfica baseada no navegador para o cluster.

Como o cluster vFXT se encontra dentro de uma rede virtual privada, você deve criar um túnel SSH ou usar outro método para alcançar o endereço IP de gestão do cluster.

Há dois passos básicos:

1. Crie uma ligação entre a sua estação de trabalho e a rede virtual privada
1. Carregue o painel de controlo do cluster num navegador web

> [!NOTE]
> Este artigo pressupõe que definiu um endereço IP público no controlador de cluster ou em outro VM dentro da rede virtual do seu cluster. Este artigo descreve como usar esse VM como hospedeiro para aceder ao cluster. Se estiver a utilizar uma VPN ou ExpressRoute para acesso virtual à rede, salte para [Ligar ao Painel de Controlo avere.](#connect-to-the-avere-control-panel-in-a-browser)

Antes de ligar, certifique-se de que o par de chaves SSH público/privado que utilizou ao criar o controlador de cluster está instalado na sua máquina local. Leia a documentação das chaves SSH para [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) ou [linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) se precisar de ajuda. Se utilizar uma palavra-passe em vez de uma chave pública, será solicitado a introduzi-la quando ligar.

## <a name="create-an-ssh-tunnel"></a>Criar um túnel SSH

Pode criar um túnel SSH a partir da linha de comando de um sistema de clientes baseado em Linux ou Windows 10.

Utilize um comando de túnel SSH com este formulário:

ssh -L *local_port:**cluster_mgmt_ip*cluster_mgmt_ip:443 controller_username *controller_username* \@ *controller_public_IP*

Este comando liga-se ao endereço IP de gestão do cluster através do endereço IP do controlador de cluster.

Exemplo:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

A autenticação é automática se utilizar a sua chave pública SSH para criar o cluster e a chave de correspondência estiver instalada no sistema cliente. Se usou uma palavra-passe, o sistema irá pedir-lhe para a introduzir.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Ligue-se ao Painel de Controlo avere num browser

Este passo utiliza um navegador web para se conectar ao utilitário de configuração no cluster vFXT.

* Para uma ligação ao túnel SSH, abra o seu navegador web e navegue para `https://127.0.0.1:8443` .

  Ligou-se ao endereço IP do cluster quando criou o túnel, pelo que só precisa de utilizar o endereço IP mais local do navegador. Se usou uma porta local que não seja 8443, use o seu número de porta.

* Se estiver a utilizar uma VPN ou ExpressRoute para chegar ao cluster, navegue para o endereço IP de gestão de clusters no seu navegador. Exemplo: ``https://203.0.113.51``

Dependendo do seu navegador, poderá ter de clicar **em Advanced** e verificar se é seguro proceder à página.

Introduza o nome de utilizador `admin` e a palavra-passe administrativa que forneceu ao criar o cluster.

![Screenshot do sinal avere na página povoada com o nome de utilizador 'administrador' e uma senha](media/avere-vfxt-gui-login.png)

Clique em **Iniciar sessão** ou prima para introduzir no teclado.

## <a name="next-steps"></a>Passos seguintes

Depois de ter iniciado sessão no painel de controlo do cluster, ative o [suporte](avere-vfxt-enable-support.md).
