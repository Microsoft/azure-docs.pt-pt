---
title: Matriz de proteção MABS (Azure Backup Server) V3 UR1
description: Este artigo fornece uma matriz de suporte que lista todas as cargas de trabalho, tipos de dados e instalações que o Azure Backup Server protege.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: d8f2a38db468ec57cdc0a4f0813fe7ae8e341d52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609775"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>Matriz de proteção MABS (Azure Backup Server) V3 UR1

Este artigo lista os vários servidores e cargas de trabalho que pode proteger com o Azure Backup Server. A matriz que se segue lista o que pode ser protegido com o Servidor de Backup Azure.

Utilize a seguinte matriz para MABS v3 UR1:

* Cargas de trabalho – O tipo de tecnologia de carga de trabalho.

* Versão – Versão MABS suportada para as cargas de trabalho.

* Instalação MABS – O computador/local onde pretende instalar o MABS.

* Proteção e recuperação – Listar as informações detalhadas sobre as cargas de trabalho, tais como o contentor de armazenamento suportado ou a implantação suportada.

>[!NOTE]
>O suporte para o agente de proteção de 32 bits é precotado com MABS v3 UR1. Consulte a [depreciação do agente de proteção de 32 Bits](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation).

## <a name="protection-support-matrix"></a>Matriz de suporte da proteção

As seguintes secções detalham a matriz de suporte de proteção para mABS:

* [Backup de aplicações](#applications-backup)
* [VM Backup](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>Backup de aplicações

| **Carga de trabalho**               | **Versão**                                                  | **Instalação do Servidor de Backup Azure**                       | **Servidor de backup suportado Azure** | **Proteção e recuperação**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| Computadores clientes (64 bits) | Windows 10                                                  | Servidor físico  <br><br>    Máquina virtual do Hyper-V   <br><br>   Máquina virtual VMware | V3 UR1                            | Volume, partilha, pasta, ficheiros, volumes deduped   <br><br>   Os volumes protegidos têm de ser NTFS. FAT e FAT32 não são suportados.  <br><br>    Os volumes têm de ter, pelo menos, 1 GB. O Azure Backup Server utiliza o Serviço de Cópias De Sombra de Volume (VSS) para tirar a imagem dos dados e a imagem só funciona se o volume for de, pelo menos, 1 GB. |
| Servidores (64 bits)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Máquina virtual Azure (quando a carga de trabalho está a funcionar como máquina virtual Azure)  <br><br>    Servidor físico  <br><br>    Máquina virtual do Hyper-V <br><br>     Máquina virtual VMware  <br><br>    Azure Stack | V3 UR1                            | Volume, partilha, pasta, ficheiro <br><br>    Volumes deduped (apenas NTFS) <br><br>Ao proteger um volume deduped WS 2016 com MABS v3 em execução no Windows Server 2019, as recuperações podem ser afetadas. Temos uma correção para fazer recuperações de uma forma não deduped que fará parte de versões posteriores do MABS. Contacte o suporte MABS se precisar desta correção no MABS v3 UR1.<br><br> Ao proteger um volume deduped WS 2019 com MABS v3 no Windows Server 2016, as cópias de segurança e restauros não serão desativadas. Isto significa que as cópias de segurança consumirão mais espaço no servidor MABS do que o volume original deduped NTFS.   <br><br>   Estado do sistema e metal nu (Não suportado quando a carga de trabalho está funcionando como máquina virtual Azure) |
| Servidores (64 bits)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (É necessário instalar o Quadro de [Gestão](https://www.microsoft.com/download/details.aspx?id=54616)do Windows ) | Servidor físico  <br><br>    Máquina virtual do Hyper-V  <br><br>      Máquina virtual VMware  <br><br>   Azure Stack | V3 UR1                            | Volume, partilha, pasta, ficheiro, estado do sistema/metal nu        |
| Servidor SQL                | SQL Server 2019, 2017, 2016 e [suportado SPs](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 e [SPs suportados](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | Servidor físico  <br><br>     Máquina virtual do Hyper-V   <br><br>     Máquina virtual VMware  <br><br>   Máquina virtual do Azure (quando a carga de trabalho é executada como máquina virtual do Azure)  <br><br>     Azure Stack | V3 UR1                            | Todos os cenários de implantação: base de dados       <br><br>  MABS v3 UR1 suporta a cópia de segurança das bases de dados SQL sobre volumes ReFS     <br><br>     O MABS não suporta bases de dados do SQL Server hospedadas no Windows Server 2012 Scale-Out File Servers (SOFS). <br><br>   O MABS não pode proteger o SqL server Distributed Availability Group (DAG) ou o Availability Group (AG), onde o nome de função no cluster de failover é diferente do denominado AG em SQL.       |
| Troca                   | Troca 2019, 2016                                         | Servidor físico   <br><br>   Máquina virtual do Hyper-V  <br><br>      Máquina virtual VMware  <br><br>   Azure Stack  <br><br>    Máquina virtual do Azure (quando a carga de trabalho é executada como máquina virtual do Azure) | V3 UR1                            | Proteger (todos os cenários de implementação): Servidor de intercâmbio autónomo, base de dados sob um grupo de disponibilidade de base de dados (DAG)  <br><br>    Recuperar (todos os cenários de implementação): caixa de correio, bases de dados de caixa de correio num DAG    <br><br>  Backup de Exchange over ReFS é suportado com MABS v3 UR1 |
| SharePoint                 | SharePoint 2019, 2016 com os mais recentes SPs                       | Servidor físico  <br><br>    Máquina virtual do Hyper-V <br><br>    Máquina virtual VMware  <br><br>   Máquina virtual do Azure (quando a carga de trabalho é executada como máquina virtual do Azure)   <br><br>   Azure Stack | V3 UR1                            | Proteger (todos os cenários de implementação): Conteúdo de servidor web de fazenda, frontend  <br><br>    Recuperar (todos os cenários de implementação): Quinta, base de dados, aplicação web, ficheiro ou item de lista, pesquisa do SharePoint, servidor web frontal  <br><br>    Proteger uma quinta SharePoint que está a utilizar a funcionalidade SQL Server 2012 AlwaysOn para as bases de dados de conteúdos não é suportado. |

## <a name="vm-backup"></a>VM Backup

| **Carga de trabalho**                                                 | **Versão**                                             | **Instalação do Servidor de Backup Azure**                      | **Servidor de backup suportado Azure** | **Proteção e recuperação**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Anfitrião hiper-V - agente de proteção MABS no servidor de anfitrião Hiper-V, cluster ou VM | Windows Server 2019, 2016, 2012 R2, 2012               | Servidor físico  <br><br>    Máquina virtual do Hyper-V <br><br>    Máquina virtual VMware | V3 UR1                             | Proteger: Computadores Hiper-V, volumes partilhados de cluster (CSVs)  <br><br>    Recuperar: Máquina virtual, recuperação ao nível de item de ficheiros e pastas disponíveis apenas para Windows, volumes, discos rígidos virtuais |
| VMware VMs                                                  | Servidor VMware 5.5, 6.0 ou 6.5, 6.7 (Versão Licenciada) | Máquina virtual Hiper-V  <br><br>   Máquina virtual VMware         | V3 UR1                             | Proteger: VMware VMs em volumes partilhados por cluster (CSVs), NFS e armazenamento SAN   <br><br>     Recuperar: Máquina virtual, recuperação ao nível de item de ficheiros e pastas disponíveis apenas para Windows, volumes, discos rígidos virtuais <br><br>    VMware vApps não são suportados. |

>[!NOTE]
> O MABS não suporta a cópia de segurança de máquinas virtuais com discos de passagem ou aqueles que utilizam um VHD remoto. Recomendamos que, nestes cenários, utilize backup de nível de hóspedes utilizando o MABS e instale um agente na máquina virtual para fazer o backup dos dados.

## <a name="linux"></a>Linux

| **Carga de trabalho** | **Versão**                               | **Instalação do Servidor de Backup Azure**                      | **Servidor de backup suportado Azure** | **Proteção e recuperação**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Linux funcionando como [hóspede Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) ou [VMware](backup-azure-backup-server-vmware.md) | Servidor físico, Hiper-V VM, Windows VM em VMware | V3 UR1                             | O Hiper-V deve estar a ser em execução no Windows Server 2012 R2, Windows Server 2016 ou Windows Server 2019. Proteger: Máquina virtual inteira   <br><br>   Recuperar: toda a máquina virtual   <br><br>    Apenas são suportados instantâneos consistentes com o ficheiro.    <br><br>   Para obter uma lista completa de distribuições e versões de Linux suportadas, consulte o artigo, [Linux sobre as distribuições endossadas pela Azure](../virtual-machines/linux/endorsed-distros.md). |

## <a name="azure-expressroute-support"></a>Suporte Azure ExpressRoute

Pode fazer o back up dos seus dados através do Azure ExpressRoute com o espreitamento público (disponível para circuitos antigos) e o espreitamento da Microsoft. Backup sobre olhando privado não é apoiado.

Com o acompanhamento público: Garantir o acesso aos seguintes domínios/endereços:

* URLs
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Endereços IP
  * 20.190.128.0/18
  * 40.126.0.0/18

Com o estomamento da Microsoft, selecione os seguintes serviços/regiões e valores comunitários relevantes:

* Diretório Ativo Azure (12076:5060)
* Região microsoft Azure (de acordo com a localização do cofre dos Serviços de Recuperação)
* Azure Storage (de acordo com a localização do cofre dos Serviços de Recuperação)

Para obter mais informações, consulte os [requisitos de encaminhamento ExpressRoute](../expressroute/expressroute-routing.md).

>[!NOTE]
>O "Peering Público" é precotado para novos circuitos.

## <a name="cluster-support"></a>Suporte do cluster

O Azure Backup Server pode proteger os dados nas seguintes aplicações agrupadas:

* Servidores de ficheiros

* SQL Server

* Hiper-V - Se proteger um cluster Hiper-V utilizando um agente de proteção MABS de escala, não pode adicionar proteção secundária às cargas de trabalho de Hiper-V protegidas.

* Exchange Server - O Azure Backup Server pode proteger clusters de discos não partilhados para versões suportadas do Exchange Server (replicação contínua do cluster), e também pode proteger o Servidor de Câmbio configurado para a replicação contínua local.

* SQL Server - O Azure Backup Server não suporta o backup das bases de dados do SQL Server hospedadas em volumes partilhados por clusters (CSVs).

>[!NOTE]
>O MABS suporta apenas a proteção de máquinas virtuais Hiper-V em Volumes Partilhados de Cluster (CSVs). A proteção de outras cargas de trabalho alojadas em CSV não é suportada.

O Azure Backup Server pode proteger cargas de trabalho de cluster que estão localizadas no mesmo domínio que o servidor MABS, e num domínio infantil ou fidedigno. Se pretender proteger fontes de dados em domínios ou grupos de trabalho não fidedvios, utilize a autenticação NTLM ou certificado para um único servidor ou a autenticação de certificado apenas para um cluster.

## <a name="data-protection-issues"></a>Problemas de proteção de dados

* O MABS não consegue fazer o back-up VMs usando unidades partilhadas (que estão potencialmente ligadas a outros VMs) uma vez que o escritor de Hiper-V VSS não consegue fazer recuar volumes que são apoiados por VHDs partilhados.

* Quando protege uma pasta partilhada, o caminho para a pasta partilhada inclui o caminho lógico no volume. Se mover a pasta partilhada, a proteção falhará. Se tiver de mover uma pasta partilhada protegida, retire-a do seu grupo de proteção e adicione-a à proteção após a mudança.  Além disso, se alterar o caminho de uma fonte de dados protegida num volume que utiliza o Sistema de Ficheiros Encriptados (EFS) e a nova trajetória de ficheiro exceder 5120 caracteres, a proteção de dados falhará.

* Não é possível alterar o domínio de um computador protegido e continuar a proteção sem interrupções. Além disso, não é possível alterar o domínio de um computador protegido e associar as réplicas e pontos de recuperação existentes ao computador quando este é reprotegido. Se tiver de alterar o domínio de um computador protegido, em seguida, retire primeiro as fontes de dados do computador da proteção. Em seguida, proteja a fonte de dados no computador depois de ter um novo domínio.

* Não pode alterar o nome de um computador protegido e continuar a proteção sem interrupções. Além disso, não é possível alterar o nome de um computador protegido e associar as réplicas e pontos de recuperação existentes ao computador quando este é reprotegido. Se tiver de alterar o nome de um computador protegido, remova primeiro da proteção as origens de dados no computador. Em seguida, proteja a origem de dados no computador depois de ter um nome novo.

* O MABS identifica automaticamente o fuso horário de um computador protegido durante a instalação do agente de proteção. Se um computador protegido for movido para um fuso horário diferente após a configuração da proteção, certifique-se de que altera a hora do computador no Painel de Controlo. Em seguida, atualize o fuso horário na base de dados MABS.

* O MABS pode proteger cargas de trabalho no mesmo domínio que o servidor MABS, ou em domínios infantis e fidedignos. Também pode proteger as seguintes cargas de trabalho em grupos de trabalho e domínios não fidedvios utilizando NTLM ou autenticação de certificados:

  * SQL Server
  * Servidor de Ficheiros
  * Hyper-V

  Estas cargas de trabalho podem estar em execução num único servidor ou numa configuração de cluster. Para proteger uma carga de trabalho que não esteja num domínio fidedigno, consulte [Prepare computadores em grupos de trabalho e domínios não confiáveis](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm) para obter detalhes exatos do que é suportado e que autenticação é necessária.

## <a name="unsupported-data-types"></a>Tipos de dados não suportados

O MABS não suporta a proteção dos seguintes tipos de dados:

* Ligações fixas

* Pontos de reanálise, incluindo ligações DFS e pontos de junção

* Metadados de pontos de montagem – um grupo de proteção pode conter dados com pontos de montagem. Neste caso, o DPM protege o volume montado que é o destino do ponto de montagem, mas não protege os metadados do ponto de montagem. Quando recuperar dados que contenham pontos de montagem, terá de recriar manualmente a hierarquia dos pontos de montagem.

* Dados em volumes montados dentro de volumes montados

* Reciclagem

* Ficheiros de paginação

* Pasta Informações de Volume de Sistema. Para proteger as informações do sistema para um computador, terá de selecionar o estado do sistema do computador como membro do grupo de proteção.

* Volumes não NTFS

* Ficheiros com ligações fixas ou simbólicas do Windows Vista.

* Dados sobre partilhas de ficheiros que hospedam UPDs (Discos de Perfil de Utilizador)

* Ficheiros com qualquer uma das seguintes combinações de atributos:

  * Encriptação e reanálise

  * Encriptação e Single Instance Storage (SIS)

  * Encriptação e sensibilidade às maiúsculas e minúsculas

  * Encriptação e dispersão

  * Sensibilidade às maiúsculas e minúsculas e SIS

  * Compressão e SIS

## <a name="next-steps"></a>Passos seguintes

* [Matriz de suporte para backup com o Microsoft Azure Backup Server ou System Center DPM](backup-support-matrix-mabs-dpm.md)
