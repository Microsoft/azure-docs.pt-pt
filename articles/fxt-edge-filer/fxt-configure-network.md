---
title: 'Tutorial: Configure a rede num cluster de ficheiros de borda Azure FXT'
description: Como personalizar as definições de rede após a criação do cluster Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 9b0154889544e0054e309cc5f43851b73b4396b4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754685"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Tutorial: Configure as definições de rede do cluster

Antes de utilizar um cluster de ficheiros de borda Supor Azure FXT, deve verificar e personalizar várias definições de rede para o seu fluxo de trabalho. 

Este tutorial explica as definições de rede que poderá necessitar de se ajustar para um novo cluster. 

Aprenderá: 

> [!div class="checklist"]
> * Quais as definições de rede que podem ter de ser atualizadas após a criação de um cluster
> * Que os casos de ficheiros de borda Azure FXT requerem um servidor AD ou um servidor DNS 
> * Como configurar dNS de robin redondo (RRDNS) para carregar automaticamente os pedidos do cliente de equilíbrio para o cluster FXT

O tempo que leva para completar estes passos depende de quantas mudanças de configuração são necessárias no seu sistema:

* Se precisar ler apenas o tutorial e verificar algumas definições, deve demorar 10 a 15 minutos. 
* Se precisar configurar dNS de robin redondo, essa tarefa pode demorar uma hora ou mais.

## <a name="adjust-network-settings"></a>Ajustar as definições da rede

Várias tarefas relacionadas com a rede fazem parte da criação de um novo cluster Azure FXT Edge Filer. Consulte esta lista e decida quais se aplicam ao seu sistema.

Para saber mais sobre as definições de rede para o cluster, leia os serviços de [rede de configuração](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) no Guia de Configuração do Cluster.

* Configure DNS de robin redondo para a rede virada para o cliente (opcional)

  Tráfego de cluster de equilíbrio de carga configurando o sistema DNS conforme descrito no [Configure DNS para o cluster FXT Edge Filer](#configure-dns-for-load-balancing).

* Verifique as definições de NTP

* Configure o Diretório Ativo e os downloads de nome de utilizador/nome de grupo (se necessário)

  Se os anfitriões da rede utilizarem o Ative Directory ou outro tipo de serviço de diretório externo, deve modificar a configuração dos serviços de diretório do cluster para configurar como o cluster descarrega o nome de utilizador e as informações do grupo. Leia**os Serviços** de Diretório de **Cluster** > no Guia de Configuração do Cluster para mais detalhes.

  É necessário um servidor AD se quiser suporte SMB. Configure o D.C. antes de começar a configurar o SMB.

* Definir VLANs (opcional)
  
  Configure quaisquer VLANs adicionais necessários antes de definir os vservers do seu cluster e o espaço de nome global. Leia [Trabalhar com VLANs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) no Guia de Configuração de Cluster para saber mais.

* Configurar servidores proxy (se necessário)

  Se o seu cluster utilizar um servidor proxy para alcançar endereços externos, siga estes passos para o configurar:

  1. Defina o servidor proxy na página de **configurações** proxy
  1. Aplique a configuração do servidor proxy com a página de**Configuração Geral** do **Cluster** > ou a página de Detalhes do **Ficheiro Core.**
  
  Para mais informações, leia [utilizando proxies web](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) no Guia de Configuração do Cluster.

* Enviar certificados de [encriptação](#encryption-certificates) para o cluster a utilizar (opcional)

### <a name="encryption-certificates"></a>Certificados de encriptação

O cluster FXT Edge Filer utiliza certificados X.509 para estas funções:

* Para encriptar o tráfego da administração de clusters

* Autenticar em nome de um cliente a servidores KMIP de terceiros

* Para verificar os certificados de servidor dos fornecedores de nuvem

Se precisar de fazer o upload de certificados para o cluster, utilize a página de definições de**Certificados** de **Cluster.** >  Os detalhes estão na página de [Certificados de >](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) cluster do Guia de Configuração do Cluster.

Para encriptar a comunicação de gestão de clusters, utilize a página de configurações de**Configuração Geral** do **Cluster** > para selecionar qual o certificado a utilizar para TLS administrativo.

> [!Note] 
> As teclas de acesso ao serviço de nuvem são armazenadas utilizando a página de configuração **cloud Credentials.** A [secção de ficheiros de núcleo adicionar](fxt-add-storage.md#add-a-core-filer) acima mostra um exemplo; ler a secção de [credenciais](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) de nuvem do Guia de Configuração do Cluster para mais detalhes. 

## <a name="configure-dns-for-load-balancing"></a>Configure DNS para equilibrar a carga

Esta secção explica os fundamentos da configuração de um sistema DNS (RRDNS) de robin redondo para distribuir a carga do cliente entre todos os endereços IP virados para o cliente no seu cluster FXT Edge Filer. 

### <a name="decide-whether-or-not-to-use-dns"></a>Decida se deve ou não utilizar O DNS

O equilíbrio de carga é sempre recomendado, mas não é preciso usar sempre DNS. Por exemplo, com alguns tipos de fluxos de trabalho de clientes, talvez faça mais sentido usar um script para atribuir endereços IP do cluster uniformemente entre os clientes quando montam o cluster. Alguns métodos são descritos no [Monte do cluster](fxt-mount-clients.md). 

Tenha em mente estas coisas ao decidir se deve ou não utilizar um servidor DNS: 

* Se o seu sistema for acedido apenas por clientes NFS, o DNS não é necessário. É possível especificar todos os endereços da rede utilizando endereços IP numéricos. 

* Se o seu sistema suportar o acesso sMB (CIFS), é necessário dNS, pois deve especificar um domínio DNS para o servidor Ative Directory.

* O DNS é necessário se quiser utilizar a autenticação Kerberos.

### <a name="round-robin-dns-configuration-details"></a>Detalhes de configuração dNS de robin redondo

Quando os clientes acedem ao cluster, o RRDNS equilibra automaticamente os seus pedidos entre todas as interfaces disponíveis.

Para um desempenho ótimo, configure o seu servidor DNS para lidar com os endereços de cluster virados para o cliente, como mostrado no diagrama seguinte.

Um vserver de cluster é mostrado à esquerda, e os endereços IP aparecem no centro e na direita. Configure cada ponto de acesso a cada cliente com registos E indicações a ilustração.

![Diagrama de DNS redondo-robin cluster](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
- ligação de texto alt detalhada segue[descrição de texto detalhado](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html) imagem

Cada endereço IP virado para o cliente deve ter um nome único para uso interno pelo cluster. (Neste diagrama, os IPs do cliente são nomeados vs1-cliente-IP-* para clareza, mas na produção você provavelmente deve usar algo mais conciso, como cliente*.)

Os clientes montam o cluster usando o nome do servidor como argumento do servidor. 

Modifique o ficheiro ``named.conf`` do servidor DNS para definir a ordem cíclica para consultas ao seu servidor de vserver. Esta opção garante que todos os valores disponíveis sejam concluídos. Adicione uma declaração como a seguinte:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Os ``nsupdate`` seguintes comandos fornecem um exemplo de configuração correta do DNS:

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

### <a name="enable-dns-in-the-cluster"></a>Ativar DNS no cluster 

Especifique o servidor DNS que o cluster utiliza na página **de** > definições da**Rede Administrativa** cluster. As definições nessa página incluem:

* Endereço de servidor DNS
* Nome de domínio DNS
* Domínios de pesquisa dNS

Para mais detalhes, leia [as definições de DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) no Guia de Configuração do Cluster.

## <a name="next-steps"></a>Passos seguintes

Este é o último passo básico de configuração para o cluster Azure FXT Edge Filer. 

* Conheça os LEDs do sistema e outros indicadores no estado do [hardware do Monitor](fxt-monitor.md).
* Saiba mais sobre como os clientes devem montar o cluster FXT Edge Filer no [Monte do cluster](fxt-mount-clients.md). 
* Para mais informações sobre a operação e gestão de um cluster FXT Edge Filer, consulte o Guia de [Configuração](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)do Cluster . 