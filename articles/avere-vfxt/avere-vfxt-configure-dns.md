---
title: DNS avere vFXT – Azure
description: Configurando um servidor DNS para balanceamento de carga Round Robin com avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: c28189bf227a6a81ae9e72e889a0dc598cd7949e
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256279"
---
# <a name="avere-cluster-dns-configuration"></a>Configuração de DNS do cluster do Avere

Esta seção explica as noções básicas de configuração de um sistema DNS para balanceamento de carga do cluster avere vFXT. 

Este documento não *inclui* instruções para configurar e gerenciar um servidor DNS no ambiente do Azure. 

Em vez de usar o DNS Round Robin para balancear a carga de um cluster vFXT no Azure, considere o uso de métodos manuais para atribuir endereços IP uniformemente entre os clientes quando eles forem montados. Vários métodos são descritos em [montar o cluster avere](avere-vfxt-mount-clients.md). 

Lembre-se destas coisas ao decidir se deve ou não usar um servidor DNS: 

* Se o sistema for acessado somente por clientes NFS, não será necessário usar o DNS-é possível especificar todos os endereços de rede usando endereços IP numéricos. 

* Se o seu sistema der suporte ao acesso SMB (CIFS), o DNS será necessário, pois você deve especificar um domínio DNS para o servidor de Active Directory.

* O DNS será necessário se você quiser usar a autenticação Kerberos.

## <a name="load-balancing"></a>Balanceamento de carga

Para distribuir a carga geral, configure seu domínio DNS para usar a distribuição de carga Round Robin para endereços IP voltados para o cliente.

## <a name="configuration-details"></a>Detalhes de configuração

Quando os clientes acessam o cluster, o RRDNS equilibra automaticamente suas solicitações entre todas as interfaces disponíveis.

Para obter um desempenho ideal, configure o servidor DNS para manipular endereços de cluster voltados para o cliente, conforme mostrado no diagrama a seguir.

Um VServer de cluster é mostrado à esquerda e os endereços IP aparecem no centro e à direita. Configure cada ponto de acesso para cliente com os registros A e os ponteiros conforme ilustrado.

![diagrama de DNS Round Robin do cluster do avere](media/avere-vfxt-rrdns-diagram.png) 
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Cada endereço IP voltado para o cliente deve ter um nome exclusivo para uso interno pelo cluster. (Neste diagrama, os IPs do cliente são nomeados VS1-Client-IP-* para fins de clareza, mas, em produção, você provavelmente deve usar algo mais conciso, como cliente *.)

Os clientes montam o cluster usando o nome do vserver como o argumento do servidor. 

Modifique o arquivo de ``named.conf`` do seu servidor DNS para definir a ordem cíclica de consultas para seu vserver. Essa opção garante que todos os valores disponíveis sejam alternados pelo. Adicione uma instrução como a seguinte:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Os comandos nsupdate a seguir fornecem um exemplo de configuração de DNS corretamente:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>Configurações de DNS do cluster

Especifique o servidor DNS que o cluster vFXT usa no **cluster** > página de configurações de **rede administrativa** . As configurações nessa página incluem:

* Endereço do servidor DNS
* Nome de domínio DNS
* Domínios de pesquisa DNS

Leia [configurações de DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) no guia de configuração do cluster avere para obter mais detalhes sobre como usar essa página.


