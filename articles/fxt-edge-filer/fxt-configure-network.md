---
title: Ajustar as configurações de rede para o Microsoft Azure cluster de filer do FXT Edge
description: Como personalizar as configurações de rede depois de criar o cluster de arquivos do Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: d250e566d884760244ee25e4c43d30fbe5323a7c
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254886"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Tutorial: definir as configurações de rede do cluster 

Antes de usar um cluster de filer do Azure FXT Edge criado recentemente, você deve verificar e personalizar várias configurações de rede para seu fluxo de trabalho. 

Este tutorial explica as configurações de rede que talvez você precise ajustar para um novo cluster. 

Aprenderá sobre: 

> [!div class="checklist"]
> * Quais configurações de rede talvez precisem ser atualizadas após a criação de um cluster
> * Quais casos de uso de filer do Azure FXT Edge exigem um servidor AD ou um servidor DNS 
> * Como configurar o DNS Round Robin (RRDNS) para balancear automaticamente a carga de solicitações do cliente para o cluster FXT

A quantidade de tempo que leva para concluir essas etapas depende de quantas alterações de configuração são necessárias no seu sistema:

* Se você só precisa ler o tutorial e verificar algumas configurações, deve levar de 10 a 15 minutos. 
* Se você precisar configurar o DNS Round Robin, essa tarefa poderá levar uma hora ou mais.

## <a name="adjust-network-settings"></a>Ajustar as configurações de rede

Várias tarefas relacionadas à rede fazem parte da configuração de um novo cluster de filer do Azure FXT Edge. Verifique esta lista e decida quais delas se aplicam ao seu sistema.

Para saber mais sobre as configurações de rede para o cluster, leia [Configurando serviços de rede](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) no guia de configuração do cluster.

* Configurar o DNS Round Robin para a rede voltada para o cliente (opcional)

  Balancear a carga do tráfego de cluster Configurando o sistema DNS, conforme descrito em [Configurar o DNS para o cluster de arquivos de borda do FXT Edge](#configure-dns-for-load-balancing).

* Verificar configurações de NTP

* Configurar o Active Directory e os downloads de nomes de nome de usuário/grupo (se necessário)

  Se os hosts de rede usarem Active Directory ou outro tipo de serviço de diretório externo, você deverá modificar a configuração de serviços de diretório do cluster para configurar como o cluster baixa as informações de nome de usuário e grupo. Leia**serviços de diretório**  >  do **cluster**no guia de configuração do cluster para obter detalhes.

  Um servidor do AD será necessário se você quiser suporte a SMB. Configure o AD antes de começar a configurar o SMB.

* Definir VLANs (opcional)
  
  Configure quaisquer VLANs adicionais necessárias antes de definir o vservers e o namespace global do seu cluster. Leia [trabalhando com VLANs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) no guia de configuração de cluster para saber mais.

* Configurar servidores proxy (se necessário)

  Se o cluster usar um servidor proxy para acessar endereços externos, siga estas etapas para configurá-lo:

  1. Definir o servidor proxy na página Definições de **configuração de proxy**
  1. Aplique a configuração do servidor proxy com a página de**instalação geral** do **cluster** >  ou a página de **detalhes do Filer principal** .
  
  Para obter mais informações, leia [usando proxies da Web](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) no guia de configuração do cluster.

* Carregar [certificados de criptografia](#encryption-certificates) para o cluster usar (opcional)

### <a name="encryption-certificates"></a>Certificados de criptografia

O cluster de arquivos de borda do FXT usa certificados X. 509 para essas funções:

* Para criptografar o tráfego de administração do cluster

* Para autenticar em nome de um cliente para servidores KMIP de terceiros

* Para verificar os certificados de servidor dos provedores de nuvem

Se você precisar carregar certificados para o cluster, use a página de configurações de**certificados** do **cluster** > . Os detalhes estão na página [certificados de > de cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) do guia de configuração do cluster.

Para criptografar a comunicação de gerenciamento de cluster, use a página configurações**gerais de configuração** do **cluster** >  para selecionar qual certificado usar para SSL administrativo.

> [!Note] 
> As chaves de acesso do serviço de nuvem são armazenadas usando a página de configuração de **credenciais de nuvem** . A seção [Adicionar um Filer principal](fxt-add-storage.md#add-a-core-filer) acima mostra um exemplo; Leia a seção de [credenciais de nuvem](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) do guia de configuração do cluster para obter detalhes. 

## <a name="configure-dns-for-load-balancing"></a>Configurar o DNS para balanceamento de carga

Esta seção explica as noções básicas de configuração de um sistema DNS Round Robin (RRDNS) para distribuir a carga do cliente entre todos os endereços IP voltados para o cliente no cluster do Filer do FXT Edge. 

### <a name="decide-whether-or-not-to-use-dns"></a>Decida se deseja ou não usar o DNS

O balanceamento de carga é sempre recomendado, mas você não precisa usar sempre o DNS. Por exemplo, com alguns tipos de fluxos de trabalho de cliente, pode fazer mais sentido usar um script para atribuir endereços IP de cluster uniformemente entre clientes quando eles montam o cluster. Alguns métodos são descritos em [montar o cluster](fxt-mount-clients.md). 

Lembre-se destas coisas ao decidir se deve ou não usar um servidor DNS: 

* Se o sistema for acessado somente por clientes NFS, o DNS não será necessário. É possível especificar todos os endereços de rede usando endereços IP numéricos. 

* Se o seu sistema der suporte ao acesso SMB (CIFS), o DNS será necessário, pois você deve especificar um domínio DNS para o servidor de Active Directory.

* O DNS será necessário se você quiser usar a autenticação Kerberos.

### <a name="round-robin-dns-configuration-details"></a>Detalhes de configuração de DNS de Round Robin

Quando os clientes acessam o cluster, o RRDNS equilibra automaticamente suas solicitações entre todas as interfaces disponíveis.

Para obter um desempenho ideal, configure o servidor DNS para manipular endereços de cluster voltados para o cliente, conforme mostrado no diagrama a seguir.

Um VServer de cluster é mostrado à esquerda e os endereços IP aparecem no centro e à direita. Configure cada ponto de acesso para cliente com os registros A e os ponteiros conforme ilustrado.

diagrama de DNS de Round Robin ![Cluster-link de texto alternativo detalhado segue a imagem @ no__t-1[Descrição de texto detalhado](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

Cada endereço IP voltado para o cliente deve ter um nome exclusivo para uso interno pelo cluster. (Neste diagrama, os IPs do cliente são nomeados VS1-Client-IP-* para fins de clareza, mas, em produção, você provavelmente deve usar algo mais conciso, como cliente *.)

Os clientes montam o cluster usando o nome do vserver como o argumento do servidor. 

Modifique o arquivo ``named.conf`` do seu servidor DNS para definir a ordem cíclica de consultas para seu vserver. Essa opção garante que todos os valores disponíveis sejam alternados pelo. Adicione uma instrução como a seguinte:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Os comandos ``nsupdate`` a seguir fornecem um exemplo de configuração correta do DNS:

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

### <a name="enable-dns-in-the-cluster"></a>Habilitar o DNS no cluster 

Especifique o servidor DNS que o cluster usa na página de configurações de**rede administrativa** do **cluster** > . As configurações nessa página incluem:

* Endereço do servidor DNS
* Nome de domínio DNS
* Domínios de pesquisa DNS

Para obter mais detalhes, leia [configurações de DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) no guia de configuração do cluster.

## <a name="next-steps"></a>Passos seguintes

Esta é a última etapa de configuração básica para o cluster de arquivos do Azure FXT Edge. 

* Saiba mais sobre os LEDs do sistema e outros indicadores em [monitorar o status do hardware](fxt-monitor.md).
* Saiba mais sobre como os clientes devem montar o cluster do Filer do FXT Edge na [montagem do cluster](fxt-mount-clients.md). 
* Para obter mais informações sobre como operar e gerenciar um cluster de filer do FXT Edge, consulte o [Guia de configuração do cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html). 