---
title: Matriz de suporte para recuperação de desastres Azure VM com recuperação do local de Azure
description: Resume o apoio à recuperação de desastres dos VMs do Azure para uma região secundária com recuperação do local de Azure.
ms.topic: article
ms.date: 11/29/2020
ms.author: raynew
ms.openlocfilehash: 522af9738cac098dcc9e8cb73183c0bd6b3b4902
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100534685"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Matriz de suporte para recuperação após desastre de VMs do Azure entre regiões do Azure

Este artigo resume o suporte e os pré-requisitos para a recuperação de desastres de Azure VMs de uma região de Azure para outra, utilizando o serviço [de Recuperação do Local Azure.](site-recovery-overview.md)


## <a name="deployment-method-support"></a>Suporte ao método de implantação

**Implementação** |  **Suporte**
--- | ---
**Portal do Azure** | Suportado.
**PowerShell** | Suportado. [Saiba mais](azure-to-azure-powershell.md)
**API REST** | Suportado.
**CLI** | Atualmente, não é suportado


## <a name="resource-support"></a>Suporte para recursos

**Ação de recursos** | **Detalhes**
--- | ---
**Mover abóbadas através de grupos de recursos** | Não suportado
**Mover recursos de computação/armazenamento/rede em grupos de recursos** | Não suportado.<br/><br/> Se mover um VM ou componentes associados, como o armazenamento/rede após a replicação do VM, terá de desativar e, em seguida, voltar a ativar a replicação para o VM.
**Replicar VMs do Azure de uma subscrição para outra para recuperação de desastres** | Apoiado no mesmo inquilino do Azure Ative Directory.
**Migrar VMs através de regiões dentro de aglomerados geográficos apoiados (dentro e em todas as subscrições)** | Apoiado no mesmo inquilino do Azure Ative Directory.
**VMs migram dentro da mesma região** | Não suportado.
**Anfitriões Dedicados Azure** | Não suportado.

## <a name="region-support"></a>Suporte de região

Pode replicar e recuperar VMs entre duas regiões dentro do mesmo aglomerado geográfico. Os aglomerados geográficos são definidos mantendo a latência dos dados e a soberania em mente.


**Aglomerado geográfico** | **Regiões do Azure**
-- | --
América | Canadá Leste, Canadá Central, Centro Sul dos EUA, Centro-Oeste dos EUA, Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Oeste dos EUA 2, Central EUA, Norte-Americanos do Centro Norte
Europa | Reino Unido Oeste, Reino Unido Sul, Europa do Norte, Europa Ocidental, África do Sul Oeste, África do Sul Norte, Noruega Leste, França Central, Suíça Norte, Alemanha Central
Ásia | Índia do Sul, Índia Central, Índia Ocidental, Sudeste Asiático, Ásia Oriental, Japão Leste, Japão Ocidental, Coreia Central, Coreia do Sul
Austrália    | Austrália Leste, Austrália Sudeste, Austrália Central, Austrália Central 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD East, US DOD Central
Alemanha    | Alemanha Central, Alemanha Nordeste
China | China Leste, China Norte, China Norte2, China Leste2
Regiões restritas reservadas à recuperação de desastres no país |Suíça West reservada à Suíça Norte, França Sul reservada à France Central, UAE Central restrita para clientes do Norte dos Emirados Árabes Unidos, Noruega Oeste para clientes noruegueses do Leste

>[!NOTE]
>
> - Para **o Brasil Sul,** você pode replicar e falhar para estas regiões: South Central EUA, West Central EUA, Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Oeste dos EUA 2, e Norte-Americano Norte-Americano Norte-Americano.
> - O Brasil Sul só pode ser usado como uma região de origem a partir da qual os VMs podem replicar-se usando a Recuperação do Local. Não pode funcionar como uma região alvo. Isto deve-se a problemas de latência devido a distâncias geográficas. Note que se você falhar do Brasil Sul como uma região de origem para um alvo, o recuo para o Brasil Sul da região alvo é apoiado.
> - Pode trabalhar dentro de regiões para as quais tenha acesso adequado.
> - Se a região em que pretende criar um cofre não aparecer, certifique-se de que a sua subscrição tem acesso para criar recursos naquela região.
> - Se não conseguir ver uma região dentro de um cluster geográfico quando ativar a replicação, certifique-se de que a sua subscrição tem permissões para criar VMs nessa região.



## <a name="cache-storage"></a>Armazenamento em cache

Esta tabela resume o suporte para a conta de armazenamento de cache utilizada pela Recuperação do Local durante a replicação.

**Definição** | **Suporte** | **Detalhes**
--- | --- | ---
Contas de armazenamento V2 para fins gerais (nível quente e fresco) | Suportado | A utilização de GPv2 não é recomendada porque os custos de transação para V2 são substancialmente superiores às contas de armazenamento V1.
Armazenamento Premium | Não suportado | As contas de armazenamento padrão são usadas para armazenamento de cache, para ajudar a otimizar os custos.
Firewalls de armazenamento Azure para redes virtuais  | Suportado | Se estiver a utilizar a conta de armazenamento de cache ativada por firewall ou a conta de armazenamento alvo, certifique-se de que ["Permita serviços de Microsoft confiáveis".](../storage/common/storage-network-security.md#exceptions)<br></br>Além disso, certifique-se de que permite o acesso a pelo menos uma sub-rede de Vnet de origem.

A tabela abaixo lista os limites em termos de número de discos que podem replicar-se numa única conta de armazenamento.

**Tipo de conta de armazenamento**    |    **Churn = 4 MBps por disco**    |    **Churn = 8 MBps por disco**
---    |    ---    |    ---
Conta de armazenamento V1    |    300 discos    |    150 discos
Conta de armazenamento V2    |    750 discos    |    375 discos

À medida que a agitação média nos discos aumenta, o número de discos que uma conta de armazenamento pode suportar diminui. O quadro acima pode ser utilizado como guia para tomar decisões sobre o número de contas de armazenamento que precisam de ser a provisionadas.

Por favor, note que os limites acima são específicos de Azure para Azure e zona para zona de cenários DR. 

## <a name="replicated-machine-operating-systems"></a>Sistemas operativos de máquinas replicadas

A Recuperação do Site suporta a replicação de VMs Azure que executam os sistemas operativos listados nesta secção. Note que se uma máquina já replicante for posteriormente atualizada (ou degradada) para um núcleo importante diferente, é necessário desativar a replicação e voltar a ativar a replicação após a atualização.

### <a name="windows"></a>Windows


**Sistema operativo** | **Detalhes**
--- | ---
Windows Server 2019 | Suportado para Server Core, Servidor com Experiência de Ambiente de Trabalho.
Windows Server 2016  | Núcleo de Servidor Suportado, Servidor com Experiência de Ambiente de Trabalho.
Windows Server 2012 R2 | Suportado.
Windows Server 2012 | Suportado.
Windows Server 2008 R2 com SP1/SP2 | Suportado.<br/><br/> A partir da versão [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) da extensão do serviço mobility para VMs Azure, é necessário instalar uma atualização de pilha de manutenção do Windows [(SSU)](https://support.microsoft.com/help/4490628) e [SHA-2](https://support.microsoft.com/help/4474419) nas máquinas que executam o Windows Server 2008 R2 SP1/SP2.  O SHA-1 não é suportado a partir de setembro de 2019, e se a assinatura de código SHA-2 não estiver ativada, a extensão do agente não instalará/atualizará como esperado. Saiba mais sobre [a atualização e requisitos SHA-2](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Suportado.
Windows 8.1 (x64) | Suportado.
Windows 8 (x64) | Suportado.
Windows 7 (x64) com SP1 em diante | A partir da versão [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) da extensão do serviço mobility para VMs Azure, é necessário instalar uma atualização de stack de manutenção do Windows [(SSU)](https://support.microsoft.com/help/4490628) e [SHA-2](https://support.microsoft.com/help/4474419) nas máquinas que executam o Windows 7 com SP1.  O SHA-1 não é suportado a partir de setembro de 2019, e se a assinatura de código SHA-2 não estiver ativada, a extensão do agente não instalará/atualizará como esperado. Saiba mais sobre [a atualização e requisitos SHA-2](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**Sistema operativo** | **Detalhes**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4564347/), [7.9](https://support.microsoft.com/help/4578241/), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1, [8.2](https://support.microsoft.com/help/4570609/), [8.8](https://support.microsoft.com/help/4597409/)
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10 </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, [7.8](https://support.microsoft.com/help/4564347/), [versão 7.9 pré-GA](https://support.microsoft.com/help/4578241/), versão 7.9 GA é suportada a partir de 9.37 hot fix patch** </br> 8.0, 8.1, [8.2](https://support.microsoft.com/en-us/help/4570609), [8.3](https://support.microsoft.com/help/4597409/)
Ubuntu 14.04 LTS Server | Inclui suporte para todos os 14.04. *versões x;* [Versões de kernel suportadas;](#supported-ubuntu-kernel-versions-for-azure-virtual-machines) 
Ubuntu 16.04 LTS Server | Inclui suporte para todos os 16.04. *versões x;* [Versão suportada do núcleo](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Os servidores Ubuntu que utilizam a autenticação e o sinstrução baseados em palavras-passe, e o pacote de init de nuvem para configurar VMs em nuvem, podem ter sinal baseado em palavra-passe desativado na falha (dependendo da configuração do cloudinit). O sessão baseado em palavra-passe pode ser re-activado na máquina virtual, repondo a palavra-passe a partir do menu 'Resolução de problemas >' > de resolução de problemas (do que falhou em VM no portal Azure).
Ubuntu 18.04 LTS Server | Inclui suporte para todos os 18.04. *versões x;* [Versão suportada do núcleo](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Os servidores Ubuntu que utilizam a autenticação e o sinstrução baseados em palavras-passe, e o pacote de init de nuvem para configurar VMs em nuvem, podem ter sinal baseado em palavra-passe desativado na falha (dependendo da configuração do cloudinit). O sessão baseado em palavra-passe pode ser re-activado na máquina virtual, repondo a palavra-passe a partir do menu 'Resolução de problemas >' > de resolução de problemas (do que falhou em VM no portal Azure).
Servidor Ubuntu 20.04 LTS | Inclui suporte para todos os 20.04. *versões x;* [Versão suportada do núcleo](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | Inclui apoio para todos os 7. *versões x* [Versões suportadas do kernel](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | Inclui suporte para todos os 8. *versões x* [Versões suportadas do kernel](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 9 | Inclui suporte para 9.1 a 9.13. Debian 9.0 não é apoiado. [Versões de kernel suportadas](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [Versões de kernel suportadas](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5  [(Versões suportadas do núcleo)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15, SP1, SP2[(Versões suportadas do núcleo)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> O upgrade das máquinas de replicação de SP3 para SP4 não é suportado. Se uma máquina replicada tiver sido atualizada, é necessário desativar a replicação e voltar a ativar a replicação após a atualização.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4573888/), [7.9](https://support.microsoft.com/help/4597409), [8.0](https://support.microsoft.com/help/4573888/), [8.1](https://support.microsoft.com/help/4573888/) (funcionando o kernel compatível com chapéu vermelho ou unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)<br/><br/>8.1 (em funcionamento em todos os núcleos UEK e <de kernel RedHat = 3.10.0-1062.* são suportados em [9.35](https://support.microsoft.com/help/4573888/). O suporte para o resto dos núcleos RedHat está disponível em [9.36](https://support.microsoft.com/help/4578241/))

> [!NOTE]
> Para as versões Linux, a Azure Site Recovery não suporta imagens de SO personalizadas. Apenas os núcleos de stock que fazem parte da versão/atualização da versão menor de distribuição são suportados.

*Nota: Para suportar os núcleos linux mais recentes no prazo de 15 dias após o lançamento, a Azure Site Recovery lança um patch de correção quente em cima da versão mais recente do agente de mobilidade. Esta correção é lançada entre duas grandes versões. Para atualizar a versão mais recente do agente de mobilidade (incluindo o patch de correção quente) siga as etapas mencionadas [neste artigo](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure). Este patch é atualmente lançado para agentes de mobilidade usados no cenário Azure to Azure DR.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versões suportadas do kernel Ubuntu para máquinas virtuais Azure

**Libertar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
14.04 LTS | [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/), [9.38](https://support.microsoft.com/help/4590304/), [9.39](https://support.microsoft.com/help/4597409/), [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 3.13.0-24-genérico a 3.13.0-170-genérico,<br/>3.16.0-25-genérico a 3.16.0-77-genéricos,<br/>3.19.0-18-genérico a 3.19.0-80-genérico,<br/>4.2.0-18-genérico a 4.2.0-42-genérico,<br/>4.4.0-21-genérico a 4.4.0-148-genérico,<br/>4.15.0-1023-azure para 4.15.0-1045-azure |
|||
16.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-genérico a 4.4.0-197-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genéricos,<br/>4.15.0-13-genérico a 4.15.0-128-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure para 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1102-azure </br> 4.15.0-132-genérico, 4.4.0-200-genérico, 4.15.0-1106-azure, 4.15.0-133-genérico, 4.4.0-201-genérico através de 9.40 patch quente**|
16.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-genérico a 4.4.0-194-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genéricos,<br/>4.15.0-13-genérico a 4.15.0-123-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure para 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1098-azure </br> 4.4.0-197-genérico, 4.15.0-126-genérico, 4.15.0-128-genérico, 4.15.0-1100-azure, 4.15.0-1102-azure através de 9.39 patch de fixação quente**|
16.04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-genérico a 4.4.0-190-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genéricos,<br/>4.15.0-13-genérico a 4.15.0-118-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure para 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1096-azure </br> 4.4.0-193-genérico, 4.15.0-120-genérico, 4.15.0-122-genérico, 4.15.0-1098-azure através de 9.38 hot fix patch**|
16.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-genérico a 4.4.0-189-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genéricos,<br/>4.15.0-13-genérico a 4.15.0-115-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure para 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1093-azure </br> 4.4.0-190-genérico, 4.15.0-117-genérico, 4.15.0-118-genérico, 4.15.0-1095-azure, 4.15.0-1096-azure através de 9.37 patch de fixação quente**|
16.04 LTS | [9.36](https://support.microsoft.com/help/4578241/)| 4.4.0-21-genérico a 4.4.0-187-genérico,<br/>4.8.0-34-genérico a 4.8.0-58-genérico,<br/>4.10.0-14-genérico a 4.10.0-42-genérico,<br/>4.11.0-13-genérico a 4.11.0-14-genérico,<br/>4.13.0-16-genérico a 4.13.0-45-genéricos,<br/>4.15.0-13-genérico a 4.15.0-112-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure para 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1092-azure |
|||
18.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-genérico a 4.15.0-129-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-63-genérico </br> 5.3.0-19-genérico a 5.3.0-69-genérico </br> 5.4.0-37 genérico a 5.4.0-59-genérico</br> 4.15.0-1009-azure para 4.15.0-1103-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1036-azure </br> 5.3.0-1007-azure para 5.3.0-1035-azure </br> 5.4.0-1020-azure para 5.4.0-1035-azure </br> 4.15.0-1104-azure, 4.15.0-130-genérico, 4.15.0-132-genérico, 5.4.0-1036-azure, 5.4.0-60-genérico, 5.4.0-62-genérico, genérico, 5.4.0-60-genérico, 5.4.0-62-genérico, 5.4.0-62-genérico, 5.4.0-62-genérico, 5.4.0-62-genérico, 5.4.0-62-genérico, 5.4.0 4.15.0-1106-azure, 4.15.0-134-genérico, 4.15.0-135-genérico, 5.04.0-1039-azure, 5.4.0-64-genérico, 5.4.0-65-genérico através de 9.40 patch de fixação quente**|
18.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-genérico a 4.15.0-123-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-63-genérico </br> 5.3.0-19-genérico a 5.3.0-69-genérico </br> 5.4.0-37-genérico a 5.4.0-53-genérico</br> 4.15.0-1009-azure para 4.15.0-1099-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1036-azure </br> 5.3.0-1007-azure para 5.3.0-1035-azure </br> 5.4.0-1020-azure para 5.4.0-1031-azure </br> 4.15.0-124-genérico, 5.4.0-54-genérico, 5.4.0-1032-azure, 5.4.0-56-genérico, 4.15.0-1100-azure, 4.15.0-126-genérico, 4.15.0-128-genérico, 5.4.0-58-genérico, 4.15.0-1102-azure, 5.4.0-1034-azure através de 9.39 hot patch**|
18.04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-genérico a 4.15.0-118-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-61-genérico </br> 5.3.0-19-genérico a 5.3.0-67-genérico </br> 5.4.0-37-genérico a 5.4.0-48-genérico</br> 4.15.0-1009-azure para 4.15.0-1096-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1036-azure </br> 5.3.0-1007-azure para 5.3.0-1035-azure </br> 5.4.0-1020-azure para 5.4.0-1026-azure </br> 4.15.0-121-genérico, 4.15.0-122-genérico, 5.0.0-62-genérico, 5.3.0-68-genérico, 5.4.0-51-genérico, 5.4.0-52-genérico, 4.15.0-1099-azure, 5.4.0-1031-azure através de 9,38 patch de fixação quente**|
18.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-genérico a 4.15.0-115-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-60-genérico </br> 5.3.0-19-genérico a 5.3.0-66-genérico </br> 5.4.0-37 genérico a 5.4.0-45-genérico</br> 4.15.0-1009-azure para 4.15.0-1093-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1036-azure </br> 5.3.0-1007-azure para 5.3.0-1035-azure </br> 5.4.0-1020-azure para 5.4.0-1023-azure</br> 4.15.0-117-genérico, 4.15.0-118-genérico, 5.0.0-61-genérico, 5.3.0-67-genérico, 5.4.0-47-genérico, 5.4.0-48-genérico, 4.15.0-1095-azure, 4.15.0-1096-azure, 5.4.0-1025-azure, 5.4.0-1026-azure através de 9.37 hot patch**|
18.04 LTS | [9.36](https://support.microsoft.com/help/4578241/) | 4.15.0-20-genérico a 4.15.0-112-genérico </br> 4.18.0-13-genérico a 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-58-genérico </br> 5.3.0-19 genérico a 5.3.0-65-genérico </br> 5.4.0-37-genérico a 5.4.0-42-genérico</br> 4.15.0-1009-azure para 4.15.0-1092-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1036-azure </br> 5.3.0-1007-azure para 5.3.0-1032-azure </br> 5.4.0-1020-azure para 5.4.0-1022-azure </br> 5.0.0-60 genéricos & 5.3.0-1035-azure através de 9.36 patch de correção quente**|
|||
20.04 LTS |[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-genérico a 5.4.0-59 </br> -genérico 5.4.0-1010-azure a 5.4.0-1035-azure </br> 5.8.0-29-genérico a 5.8.0-34-genérico </br> 5.4.0-1036-azure, 5.4.0-60-genérico, 5.4.0-62-genérico, 5.8.0-36-genérico, 5.8.0-38-genérico, 5.4.0-1039-azure, 5.4.0-64-genérico, 5.4.0-65-genérico, 5.8.0-40-genérico, 5.8.0-41-genérico através de 9.40 hot patch**|
20.04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-genérico a 5.4.0-53 </br> -genérico 5.4.0-1010-azure a 5.4.0-1031-azure </br> 5.4.0-54-genérico, 5.8.0-29-genérico, 5.4.0-1032-azure, 5.4.0-56-genérico, 5.8.0-31-genérico, 5.8.0-33-genérico, 5.4.0-58-genérico, 5.4.0-1034-azure através de 9.39 hot fix patch**
20.04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-genérico a 5.4.0-53 </br> -genérico 5.4.0-1010-azure a 5.4.0-1031-azure </br> 5.4.0-54-genérico, 5.8.0-29-genérico, 5.4.0-1032-azure, 5.4.0-56-genérico, 5.8.0-31-genérico, 5.8.0-33-genérico, 5.4.0-58-genérico, 5.4.0-1034-azure através de 9.39 hot fix patch**
20.04 LTS |[9,38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-genérico a 5.4.0-48 </br> -genérico 5.4.0-1010-azure para 5.4.0-1026-azure </br> 5.4.0-51-genérico, 5.4.0-52-genérico, 5.8.0-23-genérico, 5.8.0-25-genérico, 5.4.0-1031-azure através de 9.38 hot fix patch**
20.04 LTS |[9.37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-genérico a 5.4.0-45 </br> -genérico 5.4.0-1010-azure para 5.4.0-1023-azure </br> 5.4.0-47-genérico, 5.4.0-48-genérico, 5.4.0-1025-azure, 5.4.0-1026-azure através de 9.37 hot fix patch**
20.04 LTS |[9.36](https://support.microsoft.com/help/4578241/) | 5.4.0-26-genérico a 5.4.0-42 </br> -genérico 5.4.0-1010-azure para 5.4.0-1022-azure

*Nota: Para suportar os núcleos linux mais recentes no prazo de 15 dias após o lançamento, a Azure Site Recovery lança um patch de correção quente em cima da versão mais recente do agente de mobilidade. Esta correção é lançada entre duas grandes versões. Para atualizar a versão mais recente do agente de mobilidade (incluindo o patch de correção quente) siga as etapas mencionadas [neste artigo](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure). Este patch é atualmente lançado para agentes de mobilidade usados no cenário Azure to Azure DR.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versões suportadas de kernel Debian para máquinas virtuais Azure

**Libertar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
Debian 7 | [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/), [9.38](https://support.microsoft.com/help/4590304/), [9.39](https://support.microsoft.com/help/4597409/), [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/), [9.38](https://support.microsoft.com/help/4590304/), [9.39](https://support.microsoft.com/help/4597409/), [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 3.16.0-4-amd64 a 3.16.0-11-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 |
|||
Debian 9.1 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64 a 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 a 4.19.0-0.bpo.13-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 a 4.19.0-0.bpo.13-cloud-amd64 </br> 
Debian 9.1 | [9.39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64 a 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 a 4.19.0-0.bpo.12-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 a 4.19.0-0.bpo.12-cloud-amd64 </br> 4.19.0-0.bpo.13-amd64, 4.19.0-0.bpo.13-cloud-amd64 até 9.39 hot fix patch**</br> 
Debian 9.1 | [9,38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 a 4.9.0-13-amd64 </br> 4.19.0-0.bpo.1-amd64 a 4.19.0-0.bpo.11-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 a 4.19.0-0.bpo.11-cloud-amd64 </br> 4.9.0-14-amd64, 4.19.0-0.bpo.12-amd64, 4.19.0-0.bpo.12-cloud-amd64 através de 9.38 hot fix patch**
Debian 9.1 | [9.37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 a 4.9.0-13-amd64, 4.19.0-0.bpo.6-amd64 a 4.19.0-0.bpo.10-amd64, 4.19.0-0.bpo.6-cloud-amd64 a 4.19.0-0.bpo.10-cloud-amd64
|||
Debian 10 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-amd64 a 4.19.0-13-amd64 </br> 4.19.0-6-cloud-amd64 a 4.19.0-13-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Suportadas versões de kernel SUSE Linux Enterprise Server 12 para máquinas virtuais Azure

**Libertar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Todas as [ações SUSE 12 SP1,SP2,SP3,SP4,SP5 são suportadas.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.4.138-4.7-azure para 4.4.180-4.31-azure,</br>4.12.14-6.3-azure para 4.12.14-6.43-azure </br> 4.12.14-16.7-azure para 4.12.14-16.38-azure </br> 4.12.14-16.41-azure através de 9.40 patch de correção quente**|
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.39](https://support.microsoft.com/help/4597409/) | Todas as [ações SUSE 12 SP1,SP2,SP3,SP4,SP5 são suportadas.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.4.138-4.7-azure para 4.4.180-4.31-azure,</br>4.12.14-6.3-azure para 4.12.14-6.43-azure </br> 4.12.14-16.7-azure para 4.12.14-16.34-azure </br> 4.12.14-16.38-azure através de 9.39 patch de correção quente**|
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9,38](https://support.microsoft.com/help/4590304/) | Todas as [ações SUSE 12 SP1,SP2,SP3,SP4,SP5 são suportadas.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.4.138-4.7-azure para 4.4.180-4.31-azure,</br>4.12.14-6.3-azure para 4.12.14-6.43-azure </br> 4.12.14-16.7-azure para 4.12.14-16.28-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/) | Todas as [ações SUSE 12 SP1,SP2,SP3,SP4,SP5 são suportadas.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.4.138-4.7-azure para 4.4.180-4.31-azure,</br>4.12.14-6.3-azure para 4.12.14-6.43-azure </br> 4.12.14-16.7-azure para 4.12.14-16.22-azure </br> 4.12.14-16.25-azure, 4.12.14-16.28-azure através de 9.37 hot fix patch**|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Suportadas versões SUSE Linux Enterprise Server 15 para máquinas virtuais Azure

**Libertar** | **Versão do serviço de mobilidade** | **Versão de kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | Por predefinição, todas as [ações SUSE 15, SP1, SP2 kernels](https://www.suse.com/support/kb/doc/?id=000019587) são suportadas.</br></br> 4.12.14-5.5-azure para 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure para 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure para 5.3.18-18.29-azure </br> 5.3.18-18.32-azure, 4.12.14-8.58-azure através de 9.40 patch de correção quente**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.39](https://support.microsoft.com/help/4597409/)  | Por predefinição, todas as [ações SUSE 15, SP1, SP2 kernels](https://www.suse.com/support/kb/doc/?id=000019587) são suportadas.</br></br> 4.12.14-5.5-azure para 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure para 4.12.14-8.47-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure para 5.3.18-18.21-azure </br> 4.12.14-8.52-azure, 5.3.18-18.24-azure, 4.12.14-8.55-azure, 5.3.18-18.29-azure através de 9.39 hot patch**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,38](https://support.microsoft.com/help/4590304/)  | Por predefinição, todas as [ações SUSE 15, SP1, SP2 kernels](https://www.suse.com/support/kb/doc/?id=000019587) são suportadas.</br></br> 4.12.14-5.5-azure para 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure para 4.12.14-8.44-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure para 5.3.18-18.18-azure </br> 4.12.14-8.47-azure, 5.3.18-18.21-azure através de 9.38 patch de correção quente**
SUSE Linux Enterprise Server 15 e 15 SP1 | [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/)  | Por predefinição, todas as [ações SUSE 15, SP1, SP2 kernels](https://www.suse.com/support/kb/doc/?id=000019587) são suportadas.</br></br> 4.12.14-5.5-azure para 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure para 4.12.14-8.38-azure </br> 4.12.14-8.41-azure, 4.12.14-8.44-azure através de 9.37 patch de fixação quente**


## <a name="replicated-machines---linux-file-systemguest-storage"></a>Máquinas replicadas - Sistema de ficheiros Linux/armazenamento de hóspedes

* Sistemas de ficheiros: ext3, ext4, XFS, BTRFS
* Gestor de volume: LVM2

> [!NOTE]
> O software multi-path não é suportado.


## <a name="replicated-machines---compute-settings"></a>Máquinas replicadas - configurações de cálculo

**Definição** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho | Qualquer tamanho Azure VM com pelo menos 2 núcleos cpu e 1 GB de RAM | Verifique os [tamanhos da máquina virtual Azure](../virtual-machines/sizes.md).
Conjuntos de disponibilidade | Suportado | Se ativar a replicação de um VM Azure com as opções predefinidas, é criado automaticamente um conjunto de disponibilidade, com base nas definições da região de origem. Pode modificar estas definições.
Zonas de disponibilidade | Suportado |
Benefício de Utilização Híbrida (HUB) | Suportado | Se a fonte VM tiver uma licença HUB ativada, um teste falhado ou falhado sobre vM também utiliza a licença HUB.
Conjuntos de dimensionamento de máquinas virtuais | Não suportado |
Imagens da galeria Azure - Microsoft publicada | Suportado | Suportado se o VM funcionar num sistema operativo suportado.
Imagens da Galeria Azure - Terceiro publicado | Suportado | Suportado se o VM funcionar num sistema operativo suportado.
Imagens personalizadas - Terceiros publicados | Suportado | Suportado se o VM funcionar num sistema operativo suportado.
VMs migraram usando a Recuperação do Local | Suportado | Se um VMware VM ou uma máquina física foi migrado para Azure usando a Recuperação do Site, é necessário desinstalar a versão mais antiga do serviço de mobilidade em funcionamento na máquina e reiniciar a máquina antes de a replicar para outra região do Azure.
Políticas de Azure RBAC | Não suportado | As políticas de controlo de acesso baseado em funções (Azure RBAC) em VM não são replicadas para o VM de failover na região-alvo.
Extensões | Não suportado | As extensões não são replicadas para o VM de failover na região-alvo. Tem de ser instalado manualmente após a falha.
Grupos de colocação de proximidade | Suportado | As máquinas virtuais localizadas dentro de um Grupo de Colocação de Proximidade podem ser protegidas através da Recuperação do Local.
Etiquetas  | Suportado | As tags geradas pelo utilizador aplicadas em máquinas virtuais de origem são transportadas para as máquinas virtuais alvo após o teste de failover ou failover. As etiquetas nos VM(s) são replicadas uma vez a cada 24 horas durante o tempo que os VM(s) estiverem/estiverem presentes na região-alvo.


## <a name="replicated-machines---disk-actions"></a>Máquinas replicadas - ações de disco

**Ação** | **Detalhes**
-- | ---
Redimensione o disco em VM replicado | Suportado na fonte VM antes do failover. Não há necessidade de desativar/reativar a replicação.<br/><br/> Se alterar o VM de origem após o failover, as alterações não são capturadas.<br/><br/> Se alterar o tamanho do disco no Azure VM após o failover, as alterações não são capturadas pela Recuperação do Site, e o failback será para o tamanho VM original.
Adicione um disco a um VM replicado | Suportado
Alterações offline para discos protegidos | Desligar discos e fazer modificações offline para eles requer desencadear um ressínc completo.

## <a name="replicated-machines---storage"></a>Máquinas replicadas - armazenamento

Esta tabela resumiu o suporte para o disco Azure VM OS, disco de dados e disco temporário.

- É importante observar os limites e alvos do disco VM para [discos geridos](../virtual-machines/disks-scalability-targets.md) para evitar quaisquer problemas de desempenho.
- Se implementar com as definições predefinidos, a Recuperação do Site cria automaticamente discos e contas de armazenamento com base nas definições de origem.
- Se personalizar, certifique-se de que segue as diretrizes.

**Componente** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho máximo do disco de SO | 2048 GB | [Saiba mais](../virtual-machines/managed-disks-overview.md) sobre discos VM.
Disco temporário | Não suportado | O disco temporário está sempre excluído da replicação.<br/><br/> Não guarde dados persistentes no disco temporário. [Saiba mais](../virtual-machines/managed-disks-overview.md).
Tamanho máximo do disco de dados | 32 TB para discos geridos<br></br>4 TB para discos não geridos|
Tamanho mínimo do disco de dados | Sem restrições para discos não geridos. 2 GB para discos geridos |
Número máximo do disco de dados | Até 64, de acordo com o suporte para um tamanho Azure VM específico | [Saiba mais](../virtual-machines/sizes.md) sobre os tamanhos de VM.
Taxa de alteração do disco de dados | Máximo de 20 MBps por disco para armazenamento premium. Máximo de 2 MBps por disco para armazenamento standard. | Se a taxa média de alteração de dados no disco for continuamente superior ao máximo, a replicação não se alcança.<br/><br/>  No entanto, se o máximo for ultrapassado esporadicamente, a replicação pode recuperar, mas poderá ver pontos de recuperação ligeiramente atrasados.
Disco de dados - conta de armazenamento padrão | Suportado |
Disco de dados - conta de armazenamento premium | Suportado | Se um VM tiver discos espalhados por contas de armazenamento premium e standard, pode selecionar uma conta de armazenamento de alvo diferente para cada disco, para garantir que tem a mesma configuração de armazenamento na região alvo.
Disco gerido - padrão | Apoiado nas regiões de Azure em que a Recuperação do Sítio Azure é apoiada. |
Disco gerido - premium | Apoiado nas regiões de Azure em que a Recuperação do Sítio Azure é apoiada. |
SSD Standard | Suportado |
Redundância | LRS e GRS são apoiados.<br/><br/> O ZRS não é apoiado.
Armazenamento fresco e quente | Não suportado | Os discos VM não são suportados em armazenamento fresco e quente
Espaços de Armazenamento | Suportado |
Interface de armazenamento NVMe | Não suportado
Encriptação em repouso (SSE) | Suportado | SSE é a definição padrão nas contas de armazenamento.
Encriptação em repouso (CMK) | Suportado | Tanto as teclas de Software como as teclas HSM são suportadas para discos geridos
Dupla encriptação em repouso | Suportado | Saiba mais sobre regiões apoiadas para [Windows](../virtual-machines/disk-encryption.md) e [Linux](../virtual-machines/disk-encryption.md)
Encriptação do disco Azure (ADE) para o Windows OS | Suportado para VMs com discos geridos. | Os VMs que utilizam discos não geridos não são suportados. <br/><br/> As chaves protegidas pelo HSM não são suportadas. <br/><br/> A encriptação de volumes individuais num único disco não é suportada. |
Encriptação do disco Azure (ADE) para Linux OS | Suportado para VMs com discos geridos. | Os VMs que utilizam discos não geridos não são suportados. <br/><br/> As chaves protegidas pelo HSM não são suportadas. <br/><br/> A encriptação de volumes individuais num único disco não é suportada. <br><br> Problema conhecido com ativar a replicação. [Saiba mais.](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
Rotação da chave SAS | Não suportado | Se a chave SAS para contas de armazenamento for rotativa, o cliente precisa de desativar e reativar a replicação. |
Adicionar quente    | Suportado | Ativar a replicação de um disco de dados que adiciona a um VM Azure replicado é suportado para VMs que utilizam discos geridos. <br/><br/> Apenas um disco pode ser adicionado quente a um Azure VM de cada vez. A adição paralela de vários discos não é suportada. |
Disco de remoção quente    | Não suportado | Se remover o disco de dados no VM, tem de desativar a replicação e ativar novamente a replicação para o VM.
Excluir o disco | Apoio. Tem de usar [o PowerShell](azure-to-azure-exclude-disks.md) para configurar. |    Os discos temporários são excluídos por defeito.
Direto de Espaços de Armazenamento  | Apoiado por pontos de recuperação consistentes de colisão. Os pontos de recuperação consistentes da aplicação não são suportados. |
Servidor de ficheiros de escala  | Apoiado por pontos de recuperação consistentes de colisão. Os pontos de recuperação consistentes da aplicação não são suportados. |
DRBD | Os discos que fazem parte de uma configuração DRBD não são suportados. |
LRS | Suportado |
GRS | Suportado |
RA-GRS | Suportado |
ZRS | Não suportado |
Armazenamento fresco e quente | Não suportado | Os discos de máquinas virtuais não são suportados em armazenamento fresco e quente
Firewalls de armazenamento Azure para redes virtuais  | Suportado | Se restringir o acesso de rede virtual a contas de armazenamento, ative [permitir serviços de Microsoft fidedignos](../storage/common/storage-network-security.md#exceptions).
Contas de armazenamento V2 para fins gerais (nível quente e fresco) | Suportado | Os custos de transação aumentam substancialmente em comparação com as contas de armazenamento V1 de finalidade geral
Geração 2 (bota UEFI) | Suportado
Discos NVMe | Não suportado
Discos partilhados do Azure | Não suportado
Opção de transferência segura | Suportado
Escreva discos ativados pelo acelerador | Não suportado
Etiquetas  | Suportado | As tags geradas pelo utilizador são replicadas a cada 24 horas.

>[!IMPORTANT]
> Para evitar problemas de desempenho, certifique-se de que segue a escalabilidade do disco VM e os objetivos de desempenho dos [discos geridos](../virtual-machines/disks-scalability-targets.md). Se utilizar as definições predefinidas, a Recuperação do Site cria os discos e contas de armazenamento necessários, com base na configuração de origem. Se personalizar e selecionar as suas próprias definições, siga a escalabilidade do disco e os alvos de desempenho para os seus VMs de origem.

## <a name="limits-and-data-change-rates"></a>Limites e taxas de alteração de dados

A tabela seguinte resume os limites de recuperação do local.

- Estes limites são baseados nos nossos testes, mas obviamente não cobrem todas as combinações possíveis de I/O de aplicação.
- Os resultados reais podem variar em função da sua mistura de I/O.
- Existem dois limites a considerar, por conta de dados de disco e por cada dados de máquinas virtuais.
- O limite atual para os dados de máquinas virtuais é de 54 MB/s, independentemente do tamanho.

**Alvo de armazenamento** | **Disco de origem médio I/O** |**Média de alterações a dados do disco de origem** | **Total de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |    336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior |20 MB/s | 1684 GB por disco

## <a name="replicated-machines---networking"></a>Máquinas replicadas - networking
**Definição** | **Suporte** | **Detalhes**
--- | --- | ---
NIC | Número máximo suportado para um tamanho VM Azure específico | Os NICs são criados quando o VM é criado durante o failover.<br/><br/> O número de NICs no VM de failover depende do número de NICs na fonte VM quando a replicação foi ativada. Se adicionar ou remover um NIC depois de permitir a replicação, não afeta o número de NICs no VM replicado após a falha. <br/><br/> A ordem dos NICs após a falha não é garantida como a ordem original. <br/><br/> Pode renomear OS NICs na região alvo com base nas convenções de nomeação da sua organização. O renomeamento do NIC é suportado com recurso ao PowerShell.
Balanceador de Carga de Externo | Não suportado | Pode configurar equilibradores de carga público/internet na região primária. No entanto, os equilibradores de carga público/internet não são suportados pela Recuperação do Sítio Azure na região DR.
Equilibrador de carga interna | Suportado | Associe o equilibrador de carga pré-configurado utilizando um script Azure Automation num plano de recuperação.
Endereço IP público | Suportado | Associe um endereço IP público existente com o NIC. Ou, crie um endereço IP público e associe-o ao NIC utilizando um script Azure Automation num plano de recuperação.
NSG em NIC | Suportado | Associe o NSG ao NIC utilizando um script Azure Automation num plano de recuperação.
NSG na sub-rede | Suportado | Associe o NSG à sub-rede utilizando um script Azure Automation num plano de recuperação.
Endereço IP reservado (estático) | Suportado | Se o NIC na fonte VM tiver um endereço IP estático, e a sub-rede alvo tiver o mesmo endereço IP disponível, é atribuído ao falhado sobre VM.<br/><br/> Se a sub-rede-alvo não tiver o mesmo endereço IP disponível, um dos endereços IP disponíveis na sub-rede está reservado para o VM.<br/><br/> Também pode especificar um endereço IP fixo e sub-rede em **itens replicados**  >  **Definições**  >  **Computação e**  >  **interfaces de rede de** rede .
Endereço IP dinâmico | Suportado | Se o NIC na fonte tiver um endereço IP dinâmico, o NIC no falhado sobre VM também é dinâmico por padrão.<br/><br/> Pode modificá-lo para um endereço IP fixo, se necessário.
Vários endereços IP | Não suportado | Quando falha sobre um VM que tem um NIC com vários endereços IP, apenas o endereço IP primário do NIC na região de origem é mantido. Para atribuir vários endereços IP, pode adicionar VMs a um [plano de recuperação](recovery-plan-overview.md) e anexar um script para atribuir endereços IP adicionais ao plano, ou pode fazer a alteração manualmente ou com um script após a falha.
Gestor de Tráfego     | Suportado | Pode pré-configurar o Gestor de Tráfego para que o tráfego seja encaminhado regularmente para o ponto final da região de origem e para o ponto final na região alvo em caso de falha.
DNS do Azure | Suportado |
DNS Personalizado    | Suportado |
Procuração não autenticada | Suportado | [Saiba mais](./azure-to-azure-about-networking.md)
Procuração autenticada | Não suportado | Se o VM estiver a utilizar um representante autenticado para a conectividade de saída, não pode ser replicado utilizando a Recuperação do Local de Azure.
Ligação local-local à VPN para as instalações<br/><br/>(com ou sem ExpressRoute)| Suportado | Certifique-se de que as UDRs e os NSGs estão configurados de modo a que o tráfego de Recuperação do Local não seja encaminhado para o local. [Saiba mais](./azure-to-azure-about-networking.md)
Ligação VNET à VNET    | Suportado | [Saiba mais](./azure-to-azure-about-networking.md)
Pontos Finais de Serviço de Rede Virtual | Suportado | Se estiver a restringir o acesso da rede virtual às contas de armazenamento, certifique-se de que os serviços confiáveis da Microsoft têm acesso à conta de armazenamento.
Redes aceleradas | Suportado | A rede acelerada deve ser ativada na origem VM. [Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).
Aparelho da rede Palo Alto | Não suportado | Com aparelhos de terceiros, muitas vezes existem restrições impostas pelo fornecedor dentro da Máquina Virtual. A Azure Site Recovery precisa de agente, extensões e conectividade de saída para estar disponível. Mas o aparelho não permite configurar nenhuma atividade de saída dentro da Máquina Virtual.
IPv6  | Não suportado | As configurações mistas que incluem o IPv4 e o IPv6 também não são suportadas. Por favor, liberte a sub-rede do intervalo IPv6 antes de qualquer operação de recuperação do local.
Acesso de ligação privada ao serviço de Recuperação de Sítios | Suportado | [Saiba mais](azure-to-azure-how-to-enable-replication-private-endpoints.md)
Etiquetas  | Suportado | As tags geradas pelo utilizador nos NICs são replicadas a cada 24 horas.



## <a name="next-steps"></a>Passos seguintes

- Leia [orientação de rede](./azure-to-azure-about-networking.md)  para replicar VMs Azure.
- Implementar a recuperação de [desastres replicando VMs Azure](./azure-to-azure-quickstart.md).
