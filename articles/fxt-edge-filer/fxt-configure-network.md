---
title: 'Tutorial: Configurar rede num cluster Azure FXT Edge Filer'
description: Como personalizar as definições de rede depois de criar o cluster Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 4ce7ffc66e0b6164b2e4ca9725b3f26403292a4a
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220776"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Tutorial: Configurar as definições de rede do cluster

Antes de utilizar um cluster de ficheiros Azure FXT Edge, deverá verificar e personalizar várias definições de rede para o seu fluxo de trabalho.

Este tutorial explica as definições de rede que poderá ter de ajustar para um novo cluster.

Aprenderá:

> [!div class="checklist"]
>
> * Quais as definições de rede que podem ter de ser atualizadas depois de criar um cluster
> * Quais casos de utilização do Azure FXT Edge Filer requerem um servidor de AD ou um servidor DNS
> * Como configurar o DNS round-robin (RRDNS) para carregar automaticamente os pedidos do cliente de equilíbrio para o cluster FXT

O tempo necessário para completar estes passos depende da quantidade de alterações de configuração necessárias no seu sistema:

* Se precisar apenas de ler o tutorial e verificar algumas definições, deve levar 10 a 15 minutos.
* Se precisar de configurar o DNS redondo, essa tarefa pode demorar uma hora ou mais.

## <a name="adjust-network-settings"></a>Ajustar as definições de rede

Várias tarefas relacionadas com a rede fazem parte da criação de um novo cluster Azure FXT Edge Filer. Consulte esta lista e decida quais se aplicam ao seu sistema.

Para saber mais sobre as definições de rede para o cluster, leia [os serviços de rede de configuração](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) no Guia de Configuração do Cluster.

* Configure DNS redondos para a rede virada para o cliente (opcional)

  Carregue o tráfego do cluster de balanço configurando o sistema DNS conforme descrito no [Configure DNS para o cluster FXT Edge Filer](#configure-dns-for-load-balancing).

* Verificar as definições de NTP

* Configurar o Ative Directy e os downloads de nome de utilizador/nome de grupo (se necessário)

  Se os anfitriões da sua rede utilizarem o Ative Directory ou outro tipo de serviço de diretório externo, tem de modificar a configuração dos serviços de diretório do cluster para configurar como o cluster descarrega o nome de utilizador e as informações de grupo. Leia **Cluster**  >  **os Serviços de Diretório de** Cluster no Guia de Configuração do Cluster para obter mais detalhes.

  É necessário um servidor AD se quiser suporte SMB. Configurar a AD antes de começar a configurar o SMB.

* Definir VLANs (opcional)
  
  Configure quaisquer VLANs adicionais necessários antes de definir os vservers do seu cluster e o espaço de nome global. Leia [trabalhar com VLANs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) no Guia de Configuração do Cluster para saber mais.

* Configure servidores de procuração (se necessário)

  Se o seu cluster utilizar um servidor proxy para chegar a endereços externos, siga estes passos para o configurar:

  1. Defina o servidor proxy na página de **definições de configuração de procuração**
  1. Aplique a configuração do servidor proxy com a página de Configuração Geral do **Cluster**  >  **General Setup** ou a página **'Detalhes do Ficheiro Principal'.**
  
  Para obter mais informações, leia [Usando os proxies da web](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) no Guia de Configuração do Cluster.

* Carregar [certificados de encriptação](#encryption-certificates) para o cluster usar (opcional)

### <a name="encryption-certificates"></a>Certificados de encriptação

O cluster FXT Edge Filer utiliza certificados X.509 para estas funções:

* Para encriptar o tráfego da administração do cluster

* Para autenticar em nome de um cliente para servidores KMIP de terceiros

* Para verificar os certificados de servidor dos fornecedores de nuvem

Se precisar de enviar certificados para **Cluster**o cluster, utilize a  >  página de definições de**Certificados** de Cluster. Os detalhes estão na página [de Certificados > cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) do Guia de Configuração do Cluster.

Para encriptar a comunicação de gestão de clusters, utilize a página de configurações de configuração geral do **cluster**  >  **General Setup** para selecionar qual o certificado a utilizar para TLS administrativos.

> [!Note]
> As teclas de acesso ao serviço em nuvem são armazenadas utilizando a página de configuração **de Credenciais cloud.** A secção [de ficheiros de núcleo](fxt-add-storage.md#add-a-core-filer) acima mostra um exemplo; ler a secção [de credenciais](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) de nuvem do guia de configuração do cluster para obter detalhes.

## <a name="configure-dns-for-load-balancing"></a>Configurar DNS para equilibrar carga

Esta secção explica o básico de configurar um sistema DNS (RRDNS) redondo para distribuir a carga do cliente entre todos os endereços IP virados para o cliente no seu cluster FXT Edge Filer.

### <a name="decide-whether-or-not-to-use-dns"></a>Decida se deve ou não utilizar o DNS

O equilíbrio da carga é sempre recomendado, mas não é preciso utilizar sempre DNS. Por exemplo, com alguns tipos de fluxos de trabalho do cliente, talvez faça mais sentido usar um script para atribuir endereços IP de cluster uniformemente entre os clientes quando montam o cluster. Alguns métodos são descritos no [Monte do cluster.](fxt-mount-clients.md)

Tenha estas coisas em mente ao decidir se deve ou não utilizar um servidor DNS:

* Se o seu sistema for acedido apenas por clientes NFS, o DNS não é necessário. É possível especificar todos os endereços de rede utilizando endereços IP numéricos.

* Se o seu sistema suportar o acesso SMB (CIFS), é necessário DNS, pois tem de especificar um domínio DNS para o servidor Ative Directory.

* O DNS é necessário se pretender utilizar a autenticação Kerberos.

### <a name="round-robin-dns-configuration-details"></a>Detalhes da configuração do DNS round-robin

Quando os clientes acedem ao cluster, a RRDNS equilibra automaticamente os seus pedidos entre todas as interfaces disponíveis.

Para obter um melhor desempenho, configuure o seu servidor DNS para manusear os endereços de cluster virados para o cliente, como mostrado no diagrama seguinte.

Um aglomerado é mostrado à esquerda, e os endereços IP aparecem no centro e à direita. Configure cada ponto de acesso ao cliente com registos E ponteiros A como ilustrado.

![Diagrama dns-robin de cluster - link de texto alt detalhado segue ](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
 [a descrição de texto detalhado da](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html) imagem

Cada endereço IP virado para o cliente deve ter um nome único para uso interno pelo cluster. (Neste diagrama, os IPs do cliente são nomeados vs1-cliente-IP-* para clareza, mas na produção você provavelmente deve usar algo mais conciso, como cliente*.)

Os clientes montam o cluster usando o nome vserver como argumento do servidor.

Modifique o ficheiro do seu servidor DNS ``named.conf`` para definir a encomenda cíclica para consultas no seu vserver. Esta opção garante que todos os valores disponíveis sejam perceiros. Adicione uma declaração como a seguinte:

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

### <a name="enable-dns-in-the-cluster"></a>Ativar o DNS no cluster

Especificar o servidor DNS que o cluster utiliza na página de definições da Rede Administrativa do **Cluster.**  >  **Administrative Network** As definições nessa página incluem:

* Endereço do servidor DNS
* Nome de domínio DNS
* Domínios de pesquisa de DNS

Para mais detalhes, leia [as Definições DE DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) no Guia de Configuração do Cluster.

## <a name="next-steps"></a>Passos seguintes

Este é o último passo básico de configuração para o cluster Azure FXT Edge Filer.

* Saiba mais sobre os LEDs do sistema e outros indicadores no [estado de hardware do Monitor](fxt-monitor.md).
* Saiba mais sobre como os clientes devem montar o cluster FXT Edge Filer no [Mount the cluster](fxt-mount-clients.md).
* Para obter mais informações sobre a operação e gestão de um cluster fxt edge filer, consulte o [Guia de Configuração](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)do Cluster .
