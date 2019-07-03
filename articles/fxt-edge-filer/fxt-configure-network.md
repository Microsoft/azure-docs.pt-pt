---
title: Ajustar as definições de rede para o cluster de ficheiros do Microsoft Azure FXT Edge
description: Como personalizar as definições de rede depois de criar o cluster de ficheiros do Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 36ed354304cb1c88e48088f4b36c1ad0350af0dc
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542996"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Tutorial: Configurar as definições de rede do cluster 

Antes de utilizar um cluster de ficheiros do Azure FXT Edge recém-criado, deve verificar e personalizar várias definições de rede para seu fluxo de trabalho. 

Este tutorial explica as definições de rede que poderá ter de ajustar para um novo cluster. 

Aprenderá: 

> [!div class="checklist"]
> * As definições de rede poderão ter de ser atualizados depois de criar um cluster
> * Os casos de utilização de ficheiros do Azure FXT Edge exigem um servidor do AD ou um servidor DNS 
> * Como configurar o round robin DNS (RRDNS) para automaticamente carregar pedidos de cliente de saldo e o cluster FXT

A quantidade de tempo que demora a concluir estes passos depende de quantas alterações de configuração são necessárias no seu sistema:

* Se só precisa de ler o tutorial e algumas definições de verificação, deve demorar 10 a 15 minutos. 
* Se precisar de configurar o DNS round robin, essa tarefa pode demorar uma hora ou mais.

## <a name="adjust-network-settings"></a>Ajustar as definições de rede

Várias tarefas relacionadas com a rede fazem parte da configuração de um novo cluster de ficheiros do Azure FXT Edge. Esta lista de verificação e decidir quais os que se aplicam ao seu sistema.

Para saber mais sobre as definições de rede para o cluster, leia [configurar os serviços de rede](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) no guia de configuração de Cluster.

* Configurar o round robin DNS para a rede com clientes (opcional)

  Tráfego de cluster de balanceamento de carga ao configurar o sistema DNS, conforme descrito em [configurar o DNS para o cluster de ficheiros de borda FXT](#configure-dns-for-load-balancing).

* Verifique as definições de NTP

* Configurar o Active Directory e o nome do nome de utilizador/grupo downloads (se necessário)

  Se seus hosts de rede utilizam o Active Directory ou outro tipo de serviço de diretório externo, é necessário modificar a configuração de serviços de diretório do cluster para configurar como o cluster transfere as informações de nome de utilizador e grupo. Leia **Cluster** > **serviços de diretório** no guia de configuração de Cluster para obter detalhes.

  É necessário um servidor de AD Se quiser suporte SMB. Configure o AD antes de começar a configurar o SMB.

* Definir VLANs (opcionais)
  
  Configure todas as VLANs adicionais necessárias antes de definir vservers e espaço de nomes global do seu cluster. Leia [trabalhando com VLANs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) no guia de configuração de Cluster para saber mais.

* Configurar servidores de proxy (se necessário)

  Se o cluster utiliza um servidor proxy para chegar endereços externos, siga estes passos para configurá-lo:

  1. Definir o servidor de proxy no **configuração do Proxy** página de definições
  1. Aplicar a configuração do servidor proxy com o **Cluster** > **configuração geral** página ou o **detalhes do filtro de núcleo** página.
  
  Para obter mais informações, leia [usando web proxies](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) no guia de configuração de Cluster.

* Carregue [certificados de encriptação](#encryption-certificates) para o cluster utilize (opcional)

### <a name="encryption-certificates"></a>Certificados de encriptação

O cluster de ficheiros de borda FXT utiliza certificados X.509 para estas funções:

* Para criptografar o tráfego de administração do cluster

* Para autenticar em nome de um cliente para servidores KMIP de terceiros

* Para verificar os certificados de servidor de fornecedores de cloud

Se precisar de carregar certificados para o cluster, utilize o **Cluster** > **certificados** página de definições. Os detalhes estão na [Cluster > certificados](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) página do guia de configuração do Cluster.

Para encriptar a comunicação de gestão de clusters, utilize o **Cluster** > **configuração geral** página de definições para selecionar o certificado a utilizar para SSL administrativo.

> [!Note] 
> As chaves de acesso de serviço em nuvem são armazenadas utilizando o **credênciais de Cloud** página de configuração. O [adicionar um filtro de núcleo](fxt-add-storage.md#add-a-core-filer) secção acima mostra um exemplo; leia o guia de configuração do Cluster [credenciais de Cloud](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) secção para obter detalhes. 

## <a name="configure-dns-for-load-balancing"></a>Configurar o DNS para o balanceamento de carga

Esta secção explica as noções básicas de configuração de um sistema de DNS (RRDNS) de round robin para distribuir a carga de cliente entre todos os endereços IP com clientes no seu cluster FXT Edge filtro. 

### <a name="decide-whether-or-not-to-use-dns"></a>Decidir se deve ou não utilizar o DNS

Balanceamento de carga é sempre recomendado, mas não tem de utilizar sempre o DNS. Por exemplo, com alguns tipos de fluxos de trabalho do cliente, poderá fazer mais sentido usar um script para atribuir endereços IP do cluster uniformemente entre os clientes quando eles montagem o cluster. Alguns métodos são descritos nas [montar o cluster](fxt-mount-clients.md). 

Mantenha essas coisas em mente ao decidir se deve ou não utilizar um servidor DNS: 

* Se o seu sistema é acedido por clientes NFS apenas, o DNS não é necessário. É possível especificar todos os endereços de rede através de endereços IP numérico. 

* Se o sistema suporta o acesso de SMB (CIFS), DNS é necessário, porque tem de especificar um domínio DNS para o servidor do Active Directory.

* DNS é necessário se pretender utilizar a autenticação Kerberos.

### <a name="round-robin-dns-configuration-details"></a>Detalhes de configuração de DNS round robin

Quando os clientes acessam o cluster, o RRDNS equilibra automaticamente os pedidos entre todas as interfaces disponíveis.

Para um desempenho ideal, configure o servidor DNS para lidar com endereços de cluster com clientes, conforme mostrado no diagrama seguinte.

Um vserver de cluster é apresentado no lado esquerdo, e endereços IP apresentados no centro e à direita. Configurar cada ponto de acesso de cliente com A registros e ponteiros, conforme ilustrado.

![Diagrama do cluster round robin DNS - ligação de texto alternativo detalhadas segue imagem](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
[detalhadas descrição de texto](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

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

O seguinte ``nsupdate`` comandos fornecem um exemplo de configuração DNS corretamente:

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

### <a name="enable-dns-in-the-cluster"></a>Ativar o DNS do cluster 

Especifique o servidor DNS que o cluster utiliza na **Cluster** > **rede administrativas** página de definições. As definições dessa página incluem:

* Endereço do servidor DNS
* Nome de domínio DNS
* Domínios de pesquisa de DNS

Para obter mais detalhes, leia [as definições de DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) no guia de configuração de Cluster.

## <a name="next-steps"></a>Passos Seguintes

Esta é a última etapa de configuração básica para o cluster de ficheiros do Azure FXT Edge. 

* Saiba mais sobre LEDs o sistema e outros indicadores na [monitorizar o estado do hardware](fxt-monitor.md).
* Saiba mais sobre como os clientes devem montar o filtro de borda FXT cluster no [montar o cluster](fxt-mount-clients.md). 
* Para obter mais informações sobre como operar e gerir um cluster de ficheiros de borda FXT, consulte a [guia de configuração de Cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html). 