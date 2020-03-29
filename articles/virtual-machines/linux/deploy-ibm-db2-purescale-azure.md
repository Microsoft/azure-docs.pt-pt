---
title: Implementar ibm DB2 pureScale em Azure
description: Aprenda a implementar um exemplo de arquitetura usada recentemente para migrar uma empresa do seu ambiente IBM DB2 que funciona em z/OS para IBM DB2 pureScale em Azure.
author: njray
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 98e912894a4d93a057a2f6a2153d0690deaed250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968901"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Implementar ibm DB2 pureScale em Azure

Este artigo descreve como implementar uma [arquitetura de exemplo](ibm-db2-purescale-azure.md) que um cliente empresarial usou recentemente para migrar do seu ambiente IBM DB2 em z/OS para IBM DB2 pureScale on Azure.

Para seguir os passos utilizados para a migração, consulte os scripts de instalação no repositório [DB2onAzure](https://aka.ms/db2onazure) no GitHub. Estes scripts são baseados na arquitetura para uma carga de trabalho típica de processamento de transações on-line (OLTP) típica.

## <a name="get-started"></a>Introdução

Para implementar esta arquitetura, descarregue e execute o roteiro deploy.sh encontrado no repositório [DB2onAzure](https://aka.ms/db2onazure) no GitHub.

O repositório também tem scripts para a criação de um painel grafana. Pode utilizar o dashboard para consultar prometheus, o sistema de monitorização e alerta de código aberto incluído com DB2.

> [!NOTE]
> O deploy.sh script no cliente cria chaves SSH privadas e passa-as para o modelo de implementação em HTTPS. Para maior segurança, recomendamos a utilização do [Cofre chave Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview) para armazenar segredos, chaves e senhas.

## <a name="how-the-deployment-script-works"></a>Como funciona o script de implantação

O roteiro deploy.sh cria e configura os recursos do Azure para esta arquitetura. O script solicita-lhe a subscrição do Azure e as máquinas virtuais utilizadas no ambiente-alvo e, em seguida, executa as seguintes operações:

-   Configura o grupo de recursos, rede virtual e subnets no Azure para a instalação.

-   Configura os grupos de segurança da rede e o SSH para o ambiente.

-   Configura vários NICs tanto no armazenamento partilhado como nas máquinas virtuais DB2 pureScale.

-   Cria as máquinas virtuais de armazenamento partilhado. Se utilizar espaços de armazenamento diretamente ou outra solução de armazenamento, consulte a visão geral direta dos espaços de [armazenamento.](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)

-   Cria a máquina virtual de jumpbox.

-   Cria as máquinas virtuais DB2 pureScale.

-   Cria a máquina virtual de testemunha que DB2 pureScale pings. Ignore esta parte da implantação se a sua versão de Db2 pureScale não necessitar de uma testemunha.

-   Cria uma máquina virtual do Windows para ser utilizada para testes, mas não instala nada nele.

Em seguida, os scripts de implementação configuram uma rede de área de armazenamento virtual iSCSI (vSAN) para armazenamento partilhado em Azure. Neste exemplo, o iSCSI liga-se ao cluster de armazenamento partilhado. Na solução original do cliente, foi utilizado o GlusterFS. No entanto, a IBM já não apoia esta abordagem. Para manter o seu suporte da IBM, precisa de utilizar um sistema de ficheiros compatível com iSCSI. A Microsoft oferece espaços de armazenamento direto (S2D) como opção.

Esta solução também lhe dá a opção de instalar os alvos do iSCSI como um único nó windows. O iSCSI fornece uma interface de armazenamento de blocos partilhados sobre TCP/IP que permite ao procedimento de configuração DB2 pureScale utilizar uma interface do dispositivo para se ligar ao armazenamento partilhado.

Os scripts de implantação executam estes passos gerais:

1.  Instale um aglomerado de armazenamento partilhado em Azure. Este passo envolve pelo menos dois nós linux.

2.  Instale uma interface iSCSI direta nos servidores target Linux para o cluster de armazenamento partilhado.

3.  Instale o iniciador iSCSI nas máquinas virtuais Linux. O iniciador acederá ao cluster de armazenamento partilhado utilizando um alvo iSCSI. Para obter detalhes de configuração, consulte [como configurar um alvo iSCSI e iniciador em Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) na documentação RootUsers.

4.  Instale a camada de armazenamento partilhada para a interface iSCSI.

Depois de as scripts criarem o dispositivo iSCSI, o passo final é instalar o DB2 pureScale. Como parte da configuração de pureScale DB2, a [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (anteriormente conhecida como GPFS) é compilada e instalada no cluster GlusterFS. Este sistema de ficheiros agrupado permite ao DB2 pureScale partilhar dados entre as máquinas virtuais que executam o motor DB2 pureScale. Para mais informações, consulte a documentação da [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) no site da IBM.

## <a name="db2-purescale-response-file"></a>Ficheiro de resposta PUREScale DB2

O repositório GitHub inclui DB2server.rsp, um ficheiro de resposta (.rsp) que lhe permite gerar um script automatizado para a instalação DB2 pureScale. A tabela que se segue lista as opções DB2 pureScale que o ficheiro de resposta utiliza para configuração. Pode personalizar o ficheiro de resposta conforme necessário para o seu ambiente.

> [!NOTE]
> Um ficheiro de resposta a amostras, DB2server.rsp, está incluído no repositório [DB2onAzure](https://aka.ms/db2onazure) no GitHub. Se utilizar este ficheiro, tem de o editar antes de poder funcionar no seu ambiente.

| Nome do ecrã               | Campo                                        | Valor                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Bem-vindo                   |                                              | Nova Instalação                                                                                           |
| Escolha um Produto          |                                              | Db2 Versão 11.1.3.3. Edições do Servidor com DB2 pureScale                                              |
| Configuração             | Diretório                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Selecione o tipo de instalação                 | Típico                                                                                               |
|                           | Concordo com os termos da IBM                     | Assinalado                                                                                               |
| Proprietário de instância            | Utilizador existente Por exemplo, nome de utilizador        | DB2sdin1                                                                                              |
| Utilizador vedado               | Utilizador existente, nome de utilizador                     | DB2sdfe1                                                                                              |
| Sistema de Ficheiros cluster       | Caminho do dispositivo de divisória de disco compartilhado            | /dev/dm-2                                                                                             |
|                           | Ponto de montagem                                  | /DB2sd\_1804a                                                                                         |
|                           | Disco partilhado para dados                         | /dev/dm-1                                                                                             |
|                           | Ponto de montagem (Dados)                           | /DB2fs/datafs1                                                                                        |
|                           | Disco partilhado para log                          | /dev/dm-0                                                                                             |
|                           | Ponto de montagem (Log)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2 Cluster Services Tiebreaker. Caminho do dispositivo | /dev/dm-3                                                                                             |
| Lista de anfitriões                 | d1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | CF primário preferido                         | cf1                                                                                                   |
|                           | CF secundário preferido                       | cf2                                                                                                   |
| Arquivo de Resposta e Resumo | primeira opção                                 | Instale a DB2 Server Edition com a funcionalidade IBM DB2 pureScale e guarde as minhas definições num ficheiro de resposta |
|                           | Nome do ficheiro de resposta                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Notas sobre esta implantação

- Os valores para /dev-dm0, /dev-dm1, /dev-dm2 e /dev-dm3 podem mudar após um reinício na máquina virtual onde a configuração ocorre (d0 no script automatizado). Para encontrar os valores certos, pode emitir o seguinte comando antes de completar o ficheiro de resposta no servidor onde a configuração será executada:

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

- Os scripts de configuração usam pseudónimos para os discos iSCSI para que os nomes reais possam ser encontrados facilmente.

- Quando o script de configuração é executado em d0, os valores **/dev/dm-podem\* ** ser diferentes em d1, cf0 e cf1. A diferença de valores não afeta a configuração de pureScale DB2.

## <a name="troubleshooting-and-known-issues"></a>Resolução de problemas e problemas conhecidos

O repo GitHub inclui uma base de conhecimento que os autores mantêm. Ele lista potenciais problemas que pode ter e resoluções que pode tentar. Por exemplo, problemas conhecidos podem ocorrer quando:

-   Estás a tentar chegar ao endereço IP da porta.

-   Está a compilar a Licença Pública Geral (GPL).

-   O aperto de mão de segurança entre os anfitriões falha.

-   O instalador DB2 deteta um sistema de ficheiros existente.

-   Está a instalar manualmente a Escala de Espectro IBM.

-   Está a instalar o DB2 pureScale quando a IBM Spectrum Scale já estiver criada.

-   Estás a remover o DB2 pureScale e a IBM Spectrum Scale.

Para obter mais informações sobre estes e outros problemas conhecidos, consulte o ficheiro kb.md no repo [DB2onAzure.](https://aka.ms/DB2onAzure)

## <a name="next-steps"></a>Passos seguintes

-   [Criação de utilizadores necessários para uma instalação de funcionalidade pureScale DB2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt - Criar comando de instância](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Solução de dados de clusters de pureScale DB2](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [Estúdio de Dados IBM](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Guia de elevador e turno do Centro de Dados Virtuais Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
