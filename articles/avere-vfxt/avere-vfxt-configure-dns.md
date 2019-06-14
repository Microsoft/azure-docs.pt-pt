---
title: Avere vFXT DNS - Azure
description: Configurar um servidor DNS round robin para balanceamento de carga com Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 9fd9eaf1e62d063026e0e656346baaaade87064f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60410149"
---
# <a name="avere-cluster-dns-configuration"></a>Configuração de DNS do cluster do Avere

Esta secção explica as noções básicas de configuração de um sistema DNS para o cluster de vFXT Avere de balanceamento de carga. 

Este documento *não inclui* instruções para configurar e gerir um servidor DNS no ambiente do Azure. 

Em vez de utilizar o DNS round robin para balancear um cluster de vFXT no Azure, considere a utilização de métodos manuais para atribuir endereços IP uniformemente entre os clientes quando estão montadas. Vários métodos são descritos nas [montar o cluster Avere](avere-vfxt-mount-clients.md). 

Mantenha essas coisas em mente ao decidir se deve ou não utilizar um servidor DNS: 

* Se o seu sistema é acessado por apenas a clientes NFS, através de DNS não é necessário - é possível especificar todos os endereços de rede através de endereços IP numérico. 

* Se o sistema suporta o acesso de SMB (CIFS), DNS é necessário, porque tem de especificar um domínio DNS para o servidor do Active Directory.

* DNS é necessário se pretender utilizar a autenticação Kerberos.

## <a name="load-balancing"></a>Balanceamento de carga

Para distribuir a carga geral, configure o seu domínio DNS para utilizar a distribuição de carga round robin para endereços IP com clientes.

## <a name="configuration-details"></a>Detalhes de configuração

Quando os clientes acessam o cluster, o RRDNS equilibra automaticamente os pedidos entre todas as interfaces disponíveis.

Para um desempenho ideal, configure o servidor DNS para lidar com endereços de cluster com clientes, conforme mostrado no diagrama seguinte.

Um vserver de cluster é apresentado no lado esquerdo, e endereços IP apresentados no centro e à direita. Configurar cada ponto de acesso de cliente com A registros e ponteiros, conforme ilustrado.

![Diagrama do Avere cluster round robin DNS](media/avere-vfxt-rrdns-diagram.png) 
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Cada endereço IP voltado para o cliente tem de ter um nome exclusivo para utilização interna pelo cluster. (Neste diagrama, os IPs de cliente são nomeados vs1-client - IP-* para maior clareza, mas na produção provavelmente usará algo mais concisa, como o cliente *.)

Os clientes montagem no cluster com o nome de vserver como o argumento de servidor. 

Modificar o seu servidor DNS ``named.conf`` ficheiro para definir cíclica consultas para seu vserver. Essa opção assegura que todos os valores disponíveis circulam por meio de. Adicione uma declaração semelhante ao seguinte:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Os seguintes comandos de nsupdate fornecem um exemplo de configuração DNS corretamente:

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

## <a name="cluster-dns-settings"></a>Definições de DNS do cluster

Especifique o servidor DNS que o cluster de vFXT utiliza na **Cluster** > **rede administrativas** página de definições. As definições dessa página incluem:

* Endereço do servidor DNS
* Nome de domínio DNS
* Domínios de pesquisa de DNS

Leia [as definições de DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) no guia de configuração de Cluster Avere para obter mais detalhes sobre como utilizar esta página.


