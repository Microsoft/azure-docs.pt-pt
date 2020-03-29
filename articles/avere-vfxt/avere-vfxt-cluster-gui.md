---
title: Aceda ao painel de controlo Avere vFXT - Azure
description: Como ligar ao cluster vFXT e ao Painel de Controlo Avere baseado no navegador para configurar o Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: fe2fc062f690498f3d1f588887279aa33d2434b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75416155"
---
# <a name="access-the-vfxt-cluster"></a>Aceder ao cluster vFXT

Para ajustar as definições do cluster e monitorizar o cluster, utilize o Painel de Controlo Avere. O Avere Control Panel é uma interface gráfica baseada no navegador para o cluster.

Como o cluster vFXT se encontra dentro de uma rede virtual privada, você deve criar um túnel SSH ou usar outro método para alcançar o endereço IP de gestão do cluster.

Há dois passos básicos:

1. Crie uma ligação entre a sua estação de trabalho e a rede virtual privada
1. Carregue o painel de controlo do cluster num navegador web

> [!NOTE]
> Este artigo pressupõe que definiu um endereço IP público no controlador de cluster ou em outro VM dentro da rede virtual do seu cluster. Este artigo descreve como usar esse VM como hospedeiro para aceder ao cluster. Se estiver a utilizar uma VPN ou ExpressRoute para acesso virtual à rede, salte para [Connect to the Avere Control Panel](#connect-to-the-avere-control-panel-in-a-browser).

Antes de ligar, certifique-se de que o par de chaves público/privado SSH que usou ao criar o controlador de cluster está instalado na sua máquina local. Leia a documentação das teclas SSH para [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) ou para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) se precisar de ajuda. Se usou uma palavra-passe em vez de uma chave pública, será solicitado a inseri-la quando se ligar.

## <a name="create-an-ssh-tunnel"></a>Crie um túnel SSH

Pode criar um túnel SSH a partir da linha de comando de um sistema cliente baseado em Linux ou Windows 10.

Utilize um comando de túnel SSH com esta forma:

*ssh*-L local_port :*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

Este comando liga-se ao endereço IP de gestão do cluster através do endereço IP do controlador de cluster.

Exemplo:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

A autenticação é automática se utilizou a sua chave pública SSH para criar o cluster e a chave correspondente é instalada no sistema cliente. Se usou uma palavra-passe, o sistema irá insira-o para a introduzir.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Ligue-se ao Painel de Controlo de Avere num browser

Este passo utiliza um navegador web para se ligar ao utilitário de configuração do cluster vFXT.

* Para uma ligação ao túnel SSH, abra o seu navegador web e navegue para `https://127.0.0.1:8443`.

  Ligou-se ao endereço IP do cluster quando criou o túnel, por isso só precisa de utilizar o endereço IP local no navegador. Se usou uma porta local que não o 8443, utilize o seu número de porta.

* Se estiver a utilizar uma VPN ou ExpressRoute para chegar ao cluster, navegue para o endereço IP de gestão de clusters no seu navegador. Exemplo: ``https://203.0.113.51``

Dependendo do seu navegador, poderá ter de clicar em **Advanced** e verificar se é seguro proceder à página.

Introduza o `admin` nome de utilizador e a palavra-passe administrativa que forneceu ao criar o cluster.

![Screenshot do sinal de Avere na página povoada com o nome de utilizador 'administrador' e uma palavra-passe](media/avere-vfxt-gui-login.png)

Clique **em Iniciar sessão** ou prima introduza no teclado.

## <a name="next-steps"></a>Passos seguintes

Depois de ter iniciado o sessão no painel de controlo do cluster, ative [o suporte](avere-vfxt-enable-support.md).
