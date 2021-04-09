---
title: Matriz de suporte DPM do MABS & System Center
description: Este artigo resume o suporte de backup do Azure quando utiliza o Microsoft Azure Backup Server (MABS) ou o System Center DPM para fazer backup nas instalações e recursos VM Azure.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: e888b43ea5641f1943a096f045747d547c52fcfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609758"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Matriz de suporte para backup com o Microsoft Azure Backup Server ou System Center DPM

Pode utilizar o [serviço de backup Azure](backup-overview.md) para fazer backup em máquinas e cargas de trabalho no local e máquinas virtuais Azure (VMs). Este artigo resume as definições e limitações de suporte para fazer backup de máquinas utilizando o Microsoft Azure Backup Server (MABS) ou o System Center Data Protection Manager (DPM) e Azure Backup.

## <a name="about-dpmmabs"></a>Sobre dPM/MABS

[System Center DPM](/system-center/dpm/dpm-overview) é uma solução empresarial que configura, facilita e gere o backup e recuperação de máquinas e dados empresariais. Faz parte do conjunto de produtos [do System Center.](https://www.microsoft.com/system-center/pricing)

MABS é um produto de servidor que pode ser usado para fazer cópias de segurança em servidores físicos, VMs e aplicações que executam neles.

O MABS baseia-se no Sistema Center DPM e fornece funcionalidades semelhantes com algumas diferenças:

- Nenhuma licença do System Center é necessária para executar mABS.
- Tanto para o MABS como para o DPM, o Azure fornece armazenamento de backup a longo prazo. Além disso, o DPM permite-lhe fazer o back up dados para armazenamento a longo prazo em fita. O MABS não fornece esta funcionalidade.
- [Pode fazer uma cópia de back up um servidor DPM primário com um servidor DPM secundário.](/system-center/dpm/back-up-the-dpm-server) O servidor secundário irá proteger a base de dados do servidor principal e as réplicas das origens de dados armazenadas no servidor principal. Se o servidor principal falhar, o servidor secundário pode continuar a proteger as cargas de trabalho que estão protegidas pelo servidor principal até que este esteja novamente disponível.  O MABS não fornece esta funcionalidade.

Você descarrega MABS do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57520). Pode ser executado no local ou num VM Azure.

Suporte DPM e MABS suportando uma grande variedade de aplicações, e servidores e sistemas operativos de clientes. Fornecem vários cenários de backup:

- Pode recuar ao nível da máquina com suporte de estado do sistema ou de metal nu.
- Pode fazer o back-up de volumes, ações, pastas e ficheiros específicos.
- Pode fazer o back up de aplicações específicas utilizando definições otimizadas de conhecimento de aplicações.

## <a name="dpmmabs-backup"></a>Backup DPM/MABS

A cópia de segurança utilizando o DPM/MABS e o Azure Backup funciona da seguinte forma:

1. O agente de proteção DPM/MABS é instalado em cada máquina que será apoiada.
1. As máquinas e aplicativos são apoiados até ao armazenamento local em DPM/MABS.
1. O agente Microsoft Azure Recovery Services (MARS) está instalado no servidor DPM/MABS.
1. O agente MARS apoia os discos DPM/MABS para um cofre de serviços de recuperação de backup em Azure, utilizando o Azure Backup.

Para obter mais informações:

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura MABS.
- [Reveja o que é apoiado](backup-support-matrix-mars-agent.md) para o agente MARS.

## <a name="supported-scenarios"></a>Cenários suportados

**Cenário** | **Agente** | **Localização**
--- | --- | ---
**Máquinas/cargas de trabalho de back-up no local** | O agente de proteção DPM/MABS funciona nas máquinas que pretende fazer recuar.<br/><br/> O agente MARS no servidor DPM/MABS.<br/> A versão mínima do agente microsoft Azure Recovery Services, ou agente Azure Backup, necessária para ativar esta funcionalidade é 2.0.8719.0.  | O DPM/MABS deve estar a funcionar no local.

## <a name="supported-deployments"></a>Implantações apoiadas

O DPM/MABS pode ser implantado como resumido na tabela seguinte.

**Implementação** | **Suporte** | **Detalhes**
--- | --- | ---
**Implantados no local** | Servidor físico, mas não num aglomerado físico.<br/><br/>Hiper-V VM. Pode implantar o MABS como máquina de hóspedes num hipervisor ou aglomerado autónomo. Não pode ser implantado num nó de um aglomerado ou hipervisor autónomo. O Azure Backup Server foi concebido para funcionar num servidor dedicado e de uso único.<br/><br/> Como uma máquina virtual Windows num ambiente VMware. | Os servidores MABS no local não podem proteger cargas de trabalho baseadas em Azure. <br><br> Para mais informações, consulte [a matriz de proteção.](backup-mabs-protection-matrix.md)
**Implantado como um VM Azure Stack** | Apenas MABS | O DPM não pode ser usado para apoiar os VMs da Azure Stack.
**Implantado como um VM Azure** | Protege VMs e cargas de trabalho azure que estão a funcionar nesses VMs | DPM/MABS a funcionar em Azure não pode voltar atrás nas máquinas do local. Só pode proteger cargas de trabalho que estão a funcionar em VMs Azure IaaS.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Sistemas operativos MABS e DPM apoiados

O Azure Backup pode fazer backup de instâncias DPM/MABS que estão a executar qualquer um dos seguintes sistemas operativos. Os sistemas operativos devem estar a executar os mais recentes pacotes de serviços e atualizações.

**Cenário** | **DPM/MABS**
--- | ---
**MABS em um Azure VM** |  Datacenter windows 2016.<br/><br/> Datacenter Windows 2019.<br/><br/> Recomendamos que comece com uma imagem do mercado.<br/><br/> Standard_A4_v2 mínimo com quatro núcleos e RAM de 8 GB.
**DPM em um Azure VM** | System Center 2012 R2 com atualização 3 ou mais tarde.<br/><br/> Sistema operativo Windows, conforme [exigido pelo System Center](/system-center/dpm/prepare-environment-for-dpm#dpm-server).<br/><br/> Recomendamos que comece com uma imagem do mercado.<br/><br/> Standard_A4_v2 mínimo com quatro núcleos e RAM de 8 GB.
**MABS no local** |  MABS v3 e mais tarde: Windows Server 2016 ou Windows Server 2019
**DPM no local** | Servidor físico/Hiper-V VM: System Center 2012 SP1 ou mais tarde.<br/><br/> VMware VM: System Center 2012 R2 com atualização 5 ou mais tarde.

>[!NOTE]
>A instalação do Azure Backup Server não é suportada no Windows Server Core ou no Microsoft Hyper-V Server.

## <a name="management-support"></a>Apoio à gestão

**Problema** | **Detalhes**
--- | ---
**Instalação** | Instale o DPM/MABS numa máquina de uso único.<br/><br/> Não instale DPM/MABS num controlador de domínio, numa máquina com a instalação de função do Servidor de Aplicações, numa máquina que está a executar o Microsoft Exchange Server ou o System Center Operations Manager, ou num nó de cluster.<br/><br/> [Reveja todos os requisitos do sistema DPM.](/system-center/dpm/prepare-environment-for-dpm#dpm-server)
**Domínio** | DPM/MABS deve ser associado a um domínio. Instale primeiro e, em seguida, junte o DPM/MABS a um domínio. Mover o DPM/MABS para um novo domínio após a implementação não é suportado.
**Armazenamento** | O armazenamento moderno de backup (MBS) é suportado a partir de DPM 2016/MABS v2 e mais tarde. Não está disponível para MABS v1.
**Atualização MABS** | Pode instalar diretamente mABS v3 ou atualizar para MABS v3 a partir de MABS v2. [Saiba mais](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**MABS em movimento** | Mover o MABS para um novo servidor enquanto conserva o armazenamento é suportado se estiver a utilizar MBS.<br/><br/> O servidor deve ter o mesmo nome que o original. Não pode alterar o nome se quiser manter o mesmo pool de armazenamento e utilizar a mesma base de dados MABS para armazenar pontos de recuperação de dados.<br/><br/> Vai precisar de uma cópia de segurança da base de dados do MABS porque terá de a restaurar.

>[!NOTE]
>O nomeamento do servidor DPM/MABS não é suportado.

## <a name="mabs-support-on-azure-stack"></a>Suporte MABS na Pilha Azure

Pode implementar o MABS num VM Azure Stack para que possa gerir a cópia de segurança dos VMs da Azure Stack e cargas de trabalho a partir de um único local.

**Componente** | **Detalhes**
--- | ---
**MABS em Azure Stack VM** | Pelo menos tamanho A2. Recomendamos que comece com uma imagem do Windows Server 2012 R2 ou do Windows Server 2016 a partir do Azure Marketplace.<br/><br/> Não instale mais nada no MABS VM.
**Armazenamento MABS** | Utilize uma conta de armazenamento separada para o MABS VM. O agente MARS que funciona no MABS necessita de armazenamento temporário para uma localização de cache e para reter dados restaurados da nuvem.
**Piscina de armazenamento MABS** | O tamanho do armazém MABS é determinado pelo número e tamanho dos discos que estão ligados ao MABS VM. Cada tamanho VM Azure Stack tem um número máximo de discos. Por exemplo, A2 são quatro discos.
**Retenção de MABS** | Não retenha dados de segurança nos discos MABS locais por mais de cinco dias.
**MABS escalar** | Para aumentar a sua implantação, pode aumentar o tamanho do MABS VM. Por exemplo, pode mudar de série A para D.<br/><br/> Também pode garantir que está a descarregar dados com cópia de segurança para o Azure regularmente. Se necessário, pode implementar servidores MABS adicionais.
**.Net Framework on MABS** | O MABS VM necessita do Quadro 3.NET 3.3 SP1 ou posteriormente instalado nele.
**Domínio MABS** | O MABS VM deve ser associado a um domínio. Um utilizador de domínio com privilégios administrativos deve instalar MABS no VM.
**Backup de dados Azure Stack VM** | Pode fazer o back up ficheiros, pastas e aplicativos.
**Backup apoiado** | Estes sistemas operativos são suportados para VMs que pretende apoiar:<br/><br/> Windows Server Semi-Annual Channel (Datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Suporte do SQL Server para VMs Azure Stack** | Back up SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Recua e recupera uma base de dados.
**Suporte SharePoint para VMs Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Recue e recupere uma quinta, base de dados, fronte e servidor web.
**Requisitos de rede para VMs apoiados** | Todos os VMs na carga de trabalho Azure Stack devem pertencer à mesma rede virtual e pertencer à mesma subscrição.

## <a name="dpmmabs-networking-support"></a>Suporte de rede DPM/MABS

### <a name="url-access"></a>Acesso a URL

O servidor DPM/MABS necessita de acesso a estes URLs e endereços IP:

* URLs
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Endereços IP
  * 20.190.128.0/18
  * 40.126.0.0/18:

### <a name="azure-expressroute-support"></a>Suporte Azure ExpressRoute

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

- Diretório Ativo Azure (12076:5060)
- Região microsoft Azure (de acordo com a localização do cofre dos Serviços de Recuperação)
- Azure Storage (de acordo com a localização do cofre dos Serviços de Recuperação)

Para obter mais informações, consulte os [requisitos de encaminhamento ExpressRoute](../expressroute/expressroute-routing.md).

>[!NOTE]
>O "Peering Público" é precotado para novos circuitos.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Conectividade DPM/MABS com Azure Backup

A conectividade com o serviço de backup Azure é necessária para que as cópias de segurança funcionem corretamente, e a subscrição do Azure deve estar ativa. A tabela seguinte mostra o comportamento se estas duas coisas não ocorrerem.

**MABS para Azure** | **Subscrição** | **Backup/Restaurar**
--- | --- | ---
Ligada | Ativo | Volte ao disco DPM/MABS.<br/><br/> De volta a Azure.<br/><br/> Restaurar do disco.<br/><br/> Restaurar de Azure.
Ligada | Expirado/desprovisionado | Sem cópia de segurança no disco ou no Azure.<br/><br/> Se a subscrição expirar, pode restaurar a partir do disco ou do Azure.<br/><br/> Se a subscrição for desativada, não poderá restaurar do disco ou do Azure. Os pontos de recuperação do Azure são eliminados.
Sem conectividade por mais de 15 dias | Ativo | Sem cópia de segurança no disco ou no Azure.<br/><br/> Pode restaurar do disco ou do Azure.
Sem conectividade por mais de 15 dias | Expirado/desprovisionado | Sem cópia de segurança no disco ou no Azure.<br/><br/> Se a subscrição expirar, pode restaurar a partir do disco ou do Azure.<br/><br/> Se a subscrição for desativada, não poderá restaurar do disco ou do Azure. Os pontos de recuperação do Azure são eliminados.

## <a name="domain-and-domain-trusts-support"></a>Domínio e Domínio confia no suporte

|Requisito |Detalhes |
|---------|---------|
|Domínio    | O servidor DPM/MABS deve estar num Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 domínio.        |
|Fidedignidade do domínio   |  O DPM/MABS apoia a proteção de dados através das florestas, desde que estabeleça uma confiança de dois sentidos entre as florestas separadas.   <BR><BR>   O DPM/MABS pode proteger servidores e estações de trabalho em todos os domínios, dentro de uma floresta que tem uma relação de confiança bidirecionais com o domínio do servidor DPM/MABS. Para proteger computadores em grupos de trabalho ou domínios não fidehecidos, consulte [Back up e restaure cargas de trabalho em grupos de trabalho e domínios não fidedqui os dois.](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains) <br><br> Para fazer o back up clusters de servidores Hiper-V, devem estar localizados no mesmo domínio que o servidor MABS ou num domínio de confiança ou criança. Pode fazer cópias de segurança de servidores e clusters numa carga de trabalho ou num domínio não fidedigno através de NTLM ou de autenticação de certificados para um único servidor ou de autenticação de certificados apenas para um cluster.  |

## <a name="dpmmabs-storage-support"></a>Suporte de armazenamento DPM/MABS

Os dados que são reservados até DPM/MABS são armazenados no armazenamento de discos locais.

As unidades USB ou amovíveis não são suportadas.

A compressão NTFS não é suportada em volumes DPM/MABS.

O BitLocker só pode ser ativado depois de adicionar o disco à piscina de armazenamento. Não ative o BitLocker antes de o adicionar.

O armazenamento ligado à rede (NAS) não é suportado para ser utilizado na piscina de armazenamento DPM.

**Armazenamento** | **Detalhes**
--- | ---
**MBS** | O armazenamento moderno de backup (MBS) é suportado a partir de DPM 2016/MABS v2 e mais tarde. Não está disponível para MABS v1.
**Armazenamento MABS em Azure VM** | Os dados são armazenados em discos Azure que são anexados ao DPM/MABS VM, e que são geridos em DPM/MABS. O número de discos que podem ser utilizados para o armazenamento DPM/MABS é limitado pelo tamanho do VM.<br/><br/> A2 VM: 4 discos; A3 VM: 8 discos; A4 VM: 16 discos, com um tamanho máximo de 1 TB para cada disco. Isto determina o total de armazenamento de reserva disponível.<br/><br/> A quantidade de dados que pode fazer é de que depende do número e do tamanho dos discos anexos.
**Retenção de dados MABS na Azure VM** | Recomendamos que guarde os dados durante um dia no disco DPM/MABS Azure e faça o back up de DPM/MABS para o cofre para uma retenção mais longa. Desta forma, pode proteger uma maior quantidade de dados descarregando-os para a Azure Backup.

### <a name="modern-backup-storage-mbs"></a>Armazenamento moderno de backup (MBS)

A partir de DPM 2016/MABS v2 (em execução no Windows Server 2016) e mais tarde, pode aproveitar o armazenamento de backup moderno (MBS).

- As cópias de segurança MBS são armazenadas num disco resilient File System (ReFS).
- O MBS utiliza a clonagem do bloco ReFS para uma cópia de segurança mais rápida e uma utilização mais eficiente do espaço de armazenamento.
- Quando adiciona volumes à piscina de armazenamento local DPM/MABS, configura-os com letras de unidade. Em seguida, pode configurar o armazenamento da carga de trabalho em diferentes volumes.
- Quando criar grupos de proteção para fazer o back up de dados para DPM/MABS, selecione a unidade que pretende utilizar. Por exemplo, pode armazenar backups para SQL ou outras cargas de trabalho de IOPS elevadas numa unidade de alto desempenho, e armazenar cargas de trabalho que são apoiadas com menos frequência numa unidade de desempenho mais baixa.

## <a name="supported-backups-to-mabs"></a>Backups apoiados no MABS

Para obter informações sobre os vários servidores e cargas de trabalho que pode proteger com o Azure Backup Server, consulte a Matriz de Proteção do [Servidor de Backup Azure](./backup-mabs-protection-matrix.md#protection-support-matrix).

## <a name="supported-backups-to-dpm"></a>Backups apoiados para DPM

Para obter informações sobre os vários servidores e cargas de trabalho que pode proteger com o Gestor de Proteção de Dados, consulte o artigo O que pode fazer com o [DPM de volta?](/system-center/dpm/dpm-protection-matrix)

- As cargas de trabalho agrupadas apoiadas pelo DPM/MABS devem estar no mesmo domínio que o DPM/MABS ou num domínio infantil/fidedigno.
- Pode utilizar a autenticação NTLM/certificado para fazer cópias de segurança em domínios não fidedvios ou grupos de trabalho.

## <a name="deduplicated-volumes-support"></a>Suporte para volumes com eliminação de duplicados

>[!NOTE]
> O suporte de deduplicação para mABS depende do suporte do sistema operativo.

### <a name="for-ntfs-volumes"></a>Para volumes NTFS

| Sistema operativo do servidor protegido  | Sistema operativo do servidor MABS  | Versão MABS  | Suporte para eliminação de duplicados |
| ------------------------------------------ | ------------------------------------- | ------------------ | -------------------- |
| Windows Server 2019                       | Windows Server 2019                  | MABS v3            | Y                    |
| Windows Server 2016                       | Windows Server 2019                  | MABS v3            | Y*                   |
| Windows Server 2012 R2                    | Windows Server 2019                  | MABS v3            | N                    |
| Windows Server 2012                       | Windows Server 2019                  | MABS v3            | N                    |
| Windows Server 2019                       | Windows Server 2016                  | MABS v3            | Y**                  |
| Windows Server 2016                       | Windows Server 2016                  | MABS v3            | Y                    |
| Windows Server 2012 R2                    | Windows Server 2016                  | MABS v3            | Y                    |
| Windows Server 2012                       | Windows Server 2016                  | MABS v3            | Y                    |

- \* Ao proteger um volume deduped WS 2016 com MABS v3 em execução na WS 2019, as recuperações podem ser afetadas. Temos uma solução para fazer recuperações de uma forma não desativada. Contacte o suporte MABS se precisar desta correção no MABS v3 UR1.
- \** Ao proteger um volume deduped WS 2019 com MABS v3 em WS 2016, os backups e restauros serão não deduped. Isto significa que as cópias de segurança consumirão mais espaço no servidor MABS do que o volume original deduped NTFS.

**Problema**: Se atualizar o sistema operativo protegido do Windows Server 2016 para o Windows Server 2019, então a cópia de segurança do volume deduped NTFS será afetada devido a alterações na lógica de desduplicação.

**Solução :** Contacte o suporte do MABS caso necessite desta correção para MABS v3 UR1.

### <a name="for-refs-volumes"></a>Para volumes ReFS

>[!NOTE]
> Identificámos alguns problemas com cópias de segurança de volumes de ReFS desproduplicou. Estamos a trabalhar na sua fixação e atualizaremos esta secção assim que tivermos uma correção disponível. Até lá, estamos a remover o suporte para o backup dos volumes de ReFS desproduplicou do MABS v3.
>
> MABS v3 UR1 e mais tarde continua a apoiar a proteção e recuperação de volumes reFS normais.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura MABS.
- [Reveja](backup-support-matrix-mars-agent.md) o que é apoiado para o agente MARS.
- [Crie](backup-azure-microsoft-azure-backup.md) um servidor MABS.
- [Configurar o DPM](/system-center/dpm/install-dpm).
