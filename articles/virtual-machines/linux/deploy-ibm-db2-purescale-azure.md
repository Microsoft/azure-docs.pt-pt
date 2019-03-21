---
title: Implementar o IBM DB2 pureScale no Azure
description: Saiba como implementar uma arquitetura de exemplo utilizada recentemente para migrar de uma empresa de seu ambiente do IBM DB2 em execução no z/OS para o IBM DB2 pureScale no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: fba6b5308b380b374611c09747302dbf8305dd9b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014989"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Implementar o IBM DB2 pureScale no Azure

Este artigo descreve como implementar um [arquitetura de exemplo](ibm-db2-purescale-azure.md) que um cliente empresarial recentemente utilizada para migrar a partir do seu ambiente do IBM DB2 em execução no z/OS para o IBM DB2 pureScale no Azure.

Para seguir os passos utilizados para a migração, veja os scripts de instalação no [DB2onAzure](https://aka.ms/db2onazure) repositório no GitHub. Estes scripts baseiam-se na arquitetura para cargas de trabalho típico, médias de transações online (OLTP) de processamento.

## <a name="get-started"></a>Introdução

Para implementar esta arquitetura, transfira e execute o script de deploy.sh encontrado no [DB2onAzure](https://aka.ms/db2onazure) repositório no GitHub.

O repositório também tem scripts de configuração de um dashboard da Grafana. Pode utilizar o dashboard para consultar Prometheus, monitorização de código-fonte aberto e sistema de alerta acompanha o DB2.

> [!NOTE]
> O script de deploy.sh no cliente cria as chaves SSH privadas e passa para o modelo de implementação através de HTTPS. Para maior segurança, recomendamos que utilize [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) para armazenar segredos, chaves e palavras-passe.

## <a name="how-the-deployment-script-works"></a>Como funciona o script de implementação

O script de deploy.sh cria e configura os recursos do Azure para esta arquitetura. O script pede-lhe a subscrição do Azure e máquinas virtuais utilizadas no ambiente de destino e, em seguida, realiza as seguintes operações:

-   Configura o grupo de recursos, rede virtual e sub-redes no Azure para a instalação

-   Configura os grupos de segurança de rede e o SSH para o ambiente

-   Configura as NICs no GlusterFS e as máquinas de virtuais de pureScale de DB2

-   Cria as máquinas de virtuais de armazenamento GlusterFS

-   Cria a máquina de virtual jumpbox

-   Cria as máquinas de virtuais de pureScale de DB2

-   Cria a máquina virtual de testemunho que pings de pureScale de DB2

-   Cria uma máquina virtual do Windows para utilizar em testes, mas não instala nada no mesmo

Em seguida, os scripts de implementação configurar uma rede de área de armazenamento virtual (vSAN) de iSCSI para o armazenamento partilhado no Azure. Neste exemplo, o iSCSI se conecta ao GlusterFS. Esta solução também lhe dá a opção para instalar os destinos iSCSI como um único nó do Windows. iSCSI fornece uma interface de armazenamento de blocos partilhado por TCP/IP, que permite que o procedimento de configuração de pureScale DB2 utilizar uma interface de dispositivo para ligar ao armazenamento partilhado. Para os princípios básicos de GlusterFS, consulte o [arquitetura: Tipos de volumes](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) tópico Gluster Docs.

Os scripts de implantação, execute estes passos gerais:

1.  Utilize GlusterFS para configurar um cluster de armazenamento partilhado no Azure. Este passo envolve, pelo menos, dois nós do Linux. Para obter detalhes de configuração, consulte [configuração do Red Hat Gluster Storage no Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) na documentação do Red Hat Gluster.

2.  Configure uma interface de iSCSI direto nos servidores do Linux de destino para GlusterFS. Para obter detalhes de configuração, consulte [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) no guia de administração de GlusterFS.

3.  Configure o iniciador de iSCSI nas máquinas virtuais Linux. O iniciador irá aceder ao cluster de GlusterFS usando um destino iSCSI. Para obter detalhes de configuração, consulte [como configurar um iSCSI Target e iniciador no Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) na documentação do RootUsers.

4.  Instale GlusterFS como a camada de armazenamento para a interface de iSCSI.

Depois dos scripts criam o dispositivo de iSCSI, a etapa final é instalar DB2 pureScale. Como parte da configuração de pureScale DB2, [IBM espectro dimensionamento](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (anteriormente conhecido como GPFS) é compilado e instalado no GlusterFS cluster. Este sistema de ficheiros em cluster permite pureScale DB2 partilhar dados entre as máquinas virtuais que executam o mecanismo de pureScale DB2. Para obter mais informações, consulte a [IBM espectro dimensionamento](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) documentação no site da IBM.

## <a name="db2-purescale-response-file"></a>Arquivo de resposta de pureScale de DB2

O repositório do GitHub inclui DB2server.rsp, um arquivo de resposta (.rsp) que permite-lhe gerar um script automatizado para a instalação de pureScale DB2. A tabela seguinte lista as opções de pureScale DB2 que o arquivo de resposta utiliza para a configuração. Pode personalizar o ficheiro de resposta, conforme necessário para o seu ambiente.

> [!NOTE]
> Um ficheiro de resposta de exemplo, DB2server.rsp, está incluído nos [DB2onAzure](https://aka.ms/db2onazure) repositório no GitHub. Se usar esse arquivo, tem de editá-lo antes de poder funcionar no seu ambiente.

| Nome do ecrã               | Campo                                        | Value                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Bem-vindo                   |                                              | Nova instalação                                                                                           |
| Escolha um produto          |                                              | Versão de DB2 11.1.3.3. Edições de servidor com pureScale de DB2                                              |
| Configuração             | Diretório                                    | /Data1/OPT/IBM/DB2/V11.1                                                                              |
|                           | Selecione o tipo de instalação                 | Típico                                                                                               |
|                           | Eu concordo com os termos da IBM                     | Assinalado                                                                                               |
| Proprietário da instância            | Instância de existente For de utilizador, nome de utilizador        | DB2sdin1                                                                                              |
| Utilizador bloqueado               | Utilizador existente, nome de utilizador                     | DB2sdfe1                                                                                              |
| Sistema de ficheiros do cluster       | Partilhado o caminho do dispositivo de partição de disco            | /dev/dm-2                                                                                             |
|                           | Ponto de montagem                                  | /DB2sd\_1804a                                                                                         |
|                           | Partilhado o disco de dados                         | /dev/dm-1                                                                                             |
|                           | Ponto de montagem (dados)                           | /DB2fs/datafs1                                                                                        |
|                           | Disco para o registo de partilhado                          | /dev/dm-0                                                                                             |
|                           | Ponto de montagem (registo)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2 Desempatador de serviços de Cluster. Caminho do dispositivo | /dev/dm-3                                                                                             |
| Lista de anfitriões                 | d1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | CF primário preferencial                         | cf1                                                                                                   |
|                           | Preferencial CF secundário                       | cf2                                                                                                   |
| Arquivo de resposta e o resumo | primeira opção                                 | Instale o DB2 Server Edition com a funcionalidade de pureScale IBM DB2 e guardar as minhas configurações num arquivo de resposta |
|                           | Nome de ficheiro de resposta                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Notas sobre esta implementação

- Os valores para /dev-dm0, /dev-dm1, /dev-dm2 e /dev-dm3 podem alterar após um reinício na máquina virtual onde ocorre a configuração (d0 no script automatizado). Para localizar os valores corretos, pode emitir o comando seguinte antes de concluir o arquivo de resposta no servidor onde irá executar a configuração:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- Os scripts de configuração utilizam aliases para os discos iSCSI para que os nomes reais podem ser encontrados facilmente.

- Quando o script de configuração é executado num d0, o **/desenvolvimento/dm -\***  valores podem ser diferentes em d1, cf0 e cf1. A diferença nos valores não afeta a configuração de pureScale DB2.

## <a name="troubleshooting-and-known-issues"></a>Problemas conhecidos e resolução de problemas

O repositório do GitHub inclui uma base de dados de conhecimento que os autores de manter. Ele lista poderá ter de potenciais problemas e resoluções que pode experimentar. Conhecido por exemplo, podem ocorrer problemas quando:

-   Está tentando alcançar o endereço IP do gateway.

-   Que esteja sendo compilado geral GPL (licença pública).

-   Falha no handshake de segurança entre anfitriões.

-   O instalador de DB2 Deteta um sistema de ficheiros existentes.

-   Estiver a instalar manualmente o IBM espectro dimensionamento.

-   Estiver a instalar o DB2 pureScale quando IBM espectro dimensionamento já foi criado.

-   Está removendo DB2 pureScale e o dimensionamento do espectro de IBM.

Para obter mais informações sobre estes e outros problemas conhecidos, consulte o arquivo de kb.md no [DB2onAzure](https://aka.ms/DB2onAzure) repositório.

## <a name="next-steps"></a>Passos Seguintes

-   [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [Criação de usuários necessários para uma instalação da funcionalidade de pureScale de DB2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt - criar instância de comando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2 pureScale solução de dados de Clusters](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Alliance de modernização de plataforma: IBM DB2 no Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Datacenter Virtual do Azure Lift- and -Shift guia](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
