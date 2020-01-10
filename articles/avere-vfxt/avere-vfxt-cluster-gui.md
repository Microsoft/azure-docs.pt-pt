---
title: Acessar o painel de controle do avere vFXT – Azure
description: Como se conectar ao cluster vFXT e ao painel de controle avere baseado em navegador para configurar o avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: fe2fc062f690498f3d1f588887279aa33d2434b8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75416155"
---
# <a name="access-the-vfxt-cluster"></a>Acessar o cluster vFXT

Para ajustar as configurações de cluster e monitorar o cluster, use o painel de controle avere. O painel de controle do avere é uma interface gráfica baseada em navegador para o cluster.

Como o cluster vFXT fica dentro de uma rede virtual privada, você deve criar um túnel SSH ou usar outro método para alcançar o endereço IP de gerenciamento do cluster.

Há duas etapas básicas:

1. Criar uma conexão entre a estação de trabalho e a rede virtual privada
1. Carregar o painel de controle do cluster em um navegador da Web

> [!NOTE]
> Este artigo pressupõe que você definiu um endereço IP público no controlador de cluster ou em outra VM dentro da rede virtual do cluster. Este artigo descreve como usar essa VM como um host para acessar o cluster. Se você estiver usando uma VPN ou ExpressRoute para acesso à rede virtual, pule para [conectar-se ao painel de controle do avere](#connect-to-the-avere-control-panel-in-a-browser).

Antes de se conectar, verifique se o par de chaves pública/privada SSH que você usou ao criar o controlador de cluster está instalado no computador local. Leia a documentação de chaves SSH para [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) ou para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) se precisar de ajuda. Se você usou uma senha em vez de uma chave pública, será solicitado a inseri-la ao se conectar.

## <a name="create-an-ssh-tunnel"></a>Criar um túnel SSH

Você pode criar um túnel SSH na linha de comando de um sistema cliente baseado em Linux ou Windows 10.

Use um comando de túnel SSH com este formulário:

ssh-L *local_port*:*cluster_mgmt_ip*: 443 *controller_username*\@*controller_public_IP*

Esse comando conecta-se ao endereço IP de gerenciamento do cluster por meio do endereço IP do controlador de cluster.

Exemplo:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

A autenticação será automática se você usou sua chave pública SSH para criar o cluster e a chave correspondente estiver instalada no sistema cliente. Se você usou uma senha, o sistema solicitará que você a insira.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Conectar-se ao painel de controle do avere em um navegador

Esta etapa usa um navegador da Web para se conectar ao utilitário de configuração no cluster vFXT.

* Para uma conexão de túnel SSH, abra o navegador da Web e navegue até `https://127.0.0.1:8443`.

  Você se conectou ao endereço IP do cluster quando criou o túnel, portanto, você só precisa usar o endereço IP do localhost no navegador. Se você usou uma porta local diferente de 8443, use o número da porta.

* Se você estiver usando uma VPN ou o ExpressRoute para acessar o cluster, navegue até o endereço IP de gerenciamento de cluster em seu navegador. Exemplo: ``https://203.0.113.51``

Dependendo do navegador, talvez seja necessário clicar em **avançado** e verificar se é seguro prosseguir para a página.

Insira o nome de usuário `admin` e a senha administrativa que você forneceu ao criar o cluster.

![Captura de tela da página de entrada do avere populada com o nome de usuário ' admin ' e uma senha](media/avere-vfxt-gui-login.png)

Clique em **logon** ou pressione Enter no teclado.

## <a name="next-steps"></a>Passos seguintes

Depois de fazer logon no painel de controle do cluster, habilite o [suporte](avere-vfxt-enable-support.md).
