---
title: Implantar o IBM DB2 pureScale no Azure
description: Saiba como implantar uma arquitetura de exemplo usada recentemente para migrar uma empresa de seu ambiente IBM DB2 executado em z/OS para IBM DB2 pureScale no Azure.
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
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: fe6e581963753cac33092285fee0c8d16959bde8
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530107"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Implantar o IBM DB2 pureScale no Azure

Este artigo descreve como implantar uma [arquitetura de exemplo](ibm-db2-purescale-azure.md) que um cliente corporativo usou recentemente para migrar de seu ambiente IBM DB2 executado em z/os para IBM DB2 PureScale no Azure.

Para seguir as etapas usadas para a migração, consulte os scripts de instalação no repositório [DB2onAzure](https://aka.ms/db2onazure) no github. Esses scripts se baseiam na arquitetura de uma carga de trabalho OLTP (processamento de transações online) típica de médio porte.

## <a name="get-started"></a>Começar

Para implantar essa arquitetura, baixe e execute o script deploy.sh encontrado no repositório [DB2onAzure](https://aka.ms/db2onazure) no github.

O repositório também tem scripts para configurar um painel do Grafana. Você pode usar o painel para consultar o Prometheus, o sistema de monitoramento e alertas de software livre incluído no DB2.

> [!NOTE]
> O script deploy.sh no cliente cria chaves SSH privadas e passa-as para o modelo de implantação por HTTPS. Para maior segurança, é recomendável usar [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) para armazenar segredos, chaves e senhas.

## <a name="how-the-deployment-script-works"></a>Como funciona o script de implantação

O script deploy.sh cria e configura os recursos do Azure para essa arquitetura. O script solicita a assinatura do Azure e as máquinas virtuais usadas no ambiente de destino e, em seguida, executa as seguintes operações:

-   Configura o grupo de recursos, a rede virtual e as sub-redes no Azure para a instalação.

-   Configura os grupos de segurança de rede e o SSH para o ambiente.

-   Configura várias NICs no armazenamento compartilhado e nas máquinas virtuais do DB2 pureScale.

-   Cria as máquinas virtuais de armazenamento compartilhado. Se você usar Espaços de Armazenamento Diretos ou outra solução de armazenamento, consulte [espaços de armazenamento diretos visão geral](/windows-server/storage/storage-spaces/storage-spaces-direct-overview).

-   Cria a máquina virtual Jumpbox.

-   Cria as máquinas virtuais do DB2 pureScale.

-   Cria a máquina virtual de testemunha que o DB2 pureScale efetua ping. Ignore esta parte da implantação se sua versão do DB2 pureScale não exigir uma testemunha.

-   Cria uma máquina virtual do Windows a ser usada para teste, mas não instala nada nela.

Em seguida, os scripts de implantação configuram uma rede de área de armazenamento virtual iSCSI (vSAN) para armazenamento compartilhado no Azure. Neste exemplo, o iSCSI se conecta ao cluster de armazenamento compartilhado. Na solução de cliente original, o GlusterFS foi usado. No entanto, a IBM não dá mais suporte a essa abordagem. Para manter o suporte da IBM, você precisa usar um sistema de arquivos compatível com iSCSI com suporte. A Microsoft oferece Espaços de Armazenamento Diretos (S2D) como uma opção.

Essa solução também oferece a opção de instalar os destinos iSCSI como um único nó do Windows. o iSCSI fornece uma interface de armazenamento de bloco compartilhado sobre TCP/IP que permite que o procedimento de configuração do DB2 pureScale use uma interface de dispositivo para se conectar ao armazenamento compartilhado.

Os scripts de implantação executam estas etapas gerais:

1.  Configure um cluster de armazenamento compartilhado no Azure. Esta etapa envolve pelo menos dois nós do Linux.

2.  Configure uma interface iSCSI direta em servidores Linux de destino para o cluster de armazenamento compartilhado.

3.  Configure o iniciador iSCSI nas máquinas virtuais Linux. O iniciador acessará o cluster de armazenamento compartilhado usando um destino iSCSI. Para obter detalhes de configuração, consulte [como configurar um destino iSCSI e um iniciador no Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) na documentação do RootUsers.

4.  Instale a camada de armazenamento compartilhado para a interface iSCSI.

Depois que os scripts criam o dispositivo iSCSI, a etapa final é instalar o DB2 pureScale. Como parte da configuração do DB2 pureScale, a [escala de espectro IBM](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (anteriormente conhecida como GPFS) é compilada e instalada no cluster GlusterFS. Esse sistema de arquivos clusterizado permite que o DB2 pureScale Compartilhe dados entre as máquinas virtuais que executam o mecanismo de pureScale do DB2. Para obter mais informações, consulte a documentação de [escala de espectro IBM](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) no site da IBM.

## <a name="db2-purescale-response-file"></a>Arquivo de resposta do DB2 pureScale

O repositório do GitHub inclui DB2server. rsp, um arquivo de resposta (. rsp) que permite gerar um script automatizado para a instalação do DB2 pureScale. A tabela a seguir lista as opções do DB2 pureScale que o arquivo de resposta usa para a instalação. Você pode personalizar o arquivo de resposta conforme necessário para seu ambiente.

> [!NOTE]
> Um arquivo de resposta de exemplo, DB2server. rsp, está incluído no repositório [DB2onAzure](https://aka.ms/db2onazure) no github. Se você usar esse arquivo, deverá editá-lo antes que ele possa funcionar em seu ambiente.

| Nome da tela               | Campo                                        | Valor                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Bem-vindo                   |                                              | Nova instalação                                                                                           |
| Escolher um produto          |                                              | Versão 11.1.3.3 do DB2. Edições de servidor com DB2 pureScale                                              |
| Configuração             | Diretório                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Selecione o tipo de instalação                 | Desafios                                                                                               |
|                           | Eu concordo com os termos da IBM                     | Assinalado                                                                                               |
| Proprietário da instância            | Usuário existente por exemplo, nome de usuário        | DB2sdin1                                                                                              |
| Usuário isolado               | Usuário existente, nome de usuário                     | DB2sdfe1                                                                                              |
| Sistema de arquivos do cluster       | Caminho do dispositivo de partição de disco compartilhado            | /dev/dm-2                                                                                             |
|                           | Ponto de montagem                                  | /DB2sd\_1804a                                                                                         |
|                           | Disco compartilhado para dados                         | /dev/dm-1                                                                                             |
|                           | Ponto de montagem (dados)                           | /DB2fs/datafs1                                                                                        |
|                           | Disco compartilhado para log                          | /dev/dm-0                                                                                             |
|                           | Ponto de montagem (log)                            | /DB2fs/logfs1                                                                                         |
|                           | Serviços de cluster do DB2 desempate. Caminho do dispositivo | /dev/dm-3                                                                                             |
| Lista de hosts                 | D1 [eth1], D2 [eth1], CF1 [eth1], CF2 [eth1] |                                                                                                       |
|                           | CF primário preferencial                         | cf1                                                                                                   |
|                           | CF secundário preferencial                       | cf2                                                                                                   |
| Arquivo de resposta e Resumo | primeira opção                                 | Instalar a edição do DB2 Server com o recurso pureScale do IBM DB2 e salvar minhas configurações em um arquivo de resposta |
|                           | Nome do arquivo de resposta                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Observações sobre esta implantação

- Os valores para/dev-dm0,/dev-DM1,/dev-dm2 e/dev-dm3 podem ser alterados após uma reinicialização na máquina virtual onde a instalação ocorre (D0 no script automatizado). Para localizar os valores corretos, você pode emitir o seguinte comando antes de concluir o arquivo de resposta no servidor em que a instalação será executada:

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

- Os scripts de instalação usam aliases para os discos iSCSI para que os nomes reais possam ser facilmente encontrados.

- Quando o script de instalação é executado em D0, os valores de **\*/dev/DM-** podem ser diferentes em D1, cf0 e CF1. A diferença nos valores não afeta a configuração do DB2 pureScale.

## <a name="troubleshooting-and-known-issues"></a>Resolução de problemas e problemas conhecidos

O repositório do GitHub inclui uma base de dados de conhecimento que os autores mantêm. Ele lista os possíveis problemas que você pode ter e as resoluções que você pode experimentar. Por exemplo, problemas conhecidos podem ocorrer quando:

-   Você está tentando acessar o endereço IP do gateway.

-   Você está compilando a licença pública geral (GPL).

-   O handshake de segurança entre os hosts falha.

-   O instalador do DB2 detecta um sistema de arquivos existente.

-   Você está instalando manualmente a escala IBM espectro.

-   Você está instalando o DB2 pureScale quando o IBM Spectrum escala já está criado.

-   Você está removendo o DB2 pureScale e a escala de espectro IBM.

Para obter mais informações sobre esses e outros problemas conhecidos, consulte o arquivo kb.md no repositório [DB2onAzure](https://aka.ms/DB2onAzure) .

## <a name="next-steps"></a>Passos seguintes

-   [Criando usuários necessários para uma instalação de recurso do DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt-comando de criação de instância](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Solução de dados de clusters DB2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Aliança de modernização de plataforma: IBM DB2 no Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Guia de comparação de precisão e deslocamento do data center virtual do Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
