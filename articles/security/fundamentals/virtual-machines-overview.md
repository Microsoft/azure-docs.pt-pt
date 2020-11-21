---
title: Funcionalidades de segurança utilizadas com VMs Azure
titleSuffix: Azure security
description: Este artigo fornece uma visão geral das principais funcionalidades de segurança do Azure que podem ser usadas com máquinas virtuais Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: 91dfb3cb07cca8ec89c77dea43f1cd075db11bbf
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014769"
---
# <a name="azure-virtual-machines-security-overview"></a>Visão geral de segurança das máquinas virtuais Azure
Este artigo fornece uma visão geral das principais funcionalidades de segurança do Azure que podem ser usadas com máquinas virtuais.

Pode utilizar as Máquinas Virtuais Azure para implementar uma vasta gama de soluções de computação de forma ágil. O serviço suporta os serviços Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e Azure BizTalk Services. Assim, pode implementar qualquer carga de trabalho e qualquer idioma em quase qualquer sistema operativo.

Uma máquina virtual do Azure fornece-lhe a flexibilidade de virtualização sem ter de comprar e manter o hardware físico que executa a máquina virtual. Pode construir e implementar as suas aplicações com a garantia de que os seus dados estão protegidos e seguros em centros de dados altamente seguros.

Com o Azure, pode construir soluções reforçadas e compatíveis com a segurança que:

* Proteja as suas máquinas virtuais contra vírus e malware.
* Criptografe os seus dados sensíveis.
* Tráfego de rede seguro.
* Identificar e detetar ameaças.
* Cumprir os requisitos de conformidade.  

## <a name="antimalware"></a>Antimalware

Com o Azure, pode utilizar software antimalware de fornecedores de segurança como Microsoft, Symantec, Trend Micro e Kaspersky. Este software ajuda a proteger as suas máquinas virtuais de ficheiros maliciosos, adware e outras ameaças.

O Microsoft Antimalware para Azure Cloud Services e Virtual Machines é uma capacidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares maliciosos.  O Microsoft Antimalware for Azure fornece alertas configuráveis quando o software malicioso ou indesejado tenta instalar-se ou funcionar nos seus sistemas Azure.

O Microsoft Antimalware for Azure é uma solução de agente único para aplicações e ambientes de inquilinos. É projetado para funcionar em segundo plano sem intervenção humana. Pode implementar proteção com base nas necessidades das cargas de trabalho da sua aplicação, com configuração personalizada básica de segurança por defeito ou avançada, incluindo monitorização antimalware.

Saiba mais sobre [o Microsoft Antimalware para Azure](antimalware.md) e as funcionalidades principais disponíveis.

Saiba mais sobre software antimalware para ajudar a proteger as suas máquinas virtuais:

* [Implementar Soluções Antimalware em Máquinas Virtuais do Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Como instalar e configurar a Trend Micro Deep Security como um serviço num VM do Windows](../../virtual-machines/extensions/trend.md)
* [Como instalar e configurar a proteção de ponto final Symantec num VM do Windows](../../virtual-machines/extensions/symantec.md)
* [Soluções de segurança no Mercado Azure](https://azure.microsoft.com/marketplace/?term=security)

Para uma proteção ainda mais poderosa, considere utilizar a [proteção de ameaças avançadas do Windows Defender](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). Com o Windows Defender ATP, obtém-se:

* [Attack surface reduction (Redução da superfície de ataque)](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Proteção da próxima geração](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Proteção e resposta do ponto final](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Investigação e reparação automatizadas](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Classificação de segurança](/windows/security/threat-protection/microsoft-defender-atp/tvm-microsoft-secure-score-devices)
* [Caça avançada](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Gestão e APIs](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Proteção Contra Ameaças da Microsoft](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Saiba mais:

* [Começar com o WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Visão geral das capacidades do WDATP](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Módulo de segurança de hardware

Melhorar a segurança das chaves pode melhorar as proteções de encriptação e autenticação. Pode simplificar a gestão e segurança dos seus segredos e chaves críticos armazenando-os no Azure Key Vault.

O Key Vault oferece a opção de armazenar as suas chaves em módulos de segurança de hardware (HSMs) certificados para os padrões FIPS 140-2 Nível 2. As chaves de encriptação do SQL Server para encriptação de [dados de](/sql/relational-databases/security/encryption/transparent-data-encryption) backup ou transparente podem ser armazenadas no Key Vault com quaisquer chaves ou segredos das suas aplicações. As permissões e o acesso a estes itens protegidos são geridos através [do Azure Ative Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Saiba mais:

* [O que é o Azure Key Vault?](../../key-vault/general/overview.md)
* [Blog Azure Key Vault](/archive/blogs/kv/)

## <a name="virtual-machine-disk-encryption"></a>Encriptação de disco de máquina virtual

A Azure Disk Encryption é uma nova capacidade para encriptar os discos de máquinas virtuais Windows e Linux. A Azure Disk Encryption utiliza a funcionalidade [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) padrão da indústria do Windows e a funcionalidade [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer encriptação de volume para o SISTEMA e os discos de dados.

A solução está integrada com o Azure Key Vault para o ajudar a controlar e gerir as chaves e segredos de encriptação do disco na subscrição do cofre principal. Garante que todos os dados nos discos de máquinas virtuais são encriptados em repouso no Azure Storage.

Saiba mais:

* [Encriptação do disco Azure para IaaS VMs](./azure-disk-encryption-vms-vmss.md)
* [Início Rápido: Encriptar uma VM IaaS do Windows com o Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Backup de máquina virtual

O Azure Backup é uma solução escalável que ajuda a proteger os dados da sua aplicação com investimento de capital zero e custos operacionais mínimos. Os erros de aplicação podem corromper os seus dados, e os erros humanos podem introduzir erros nas suas aplicações. Com o Azure Backup, as suas máquinas virtuais que executam o Windows e o Linux estão protegidas.

Saiba mais:

* [O que é o Azure Backup?](../../backup/backup-overview.md)
* [Serviço de backup Azure FAQ](../../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Uma parte importante da estratégia bcdr da sua organização é descobrir como manter cargas de trabalho corporativas e aplicações em execução quando ocorrem interrupções planeadas e não planeadas. A Azure Site Recovery ajuda a orquestrar a replicação, failover e a recuperação de cargas de trabalho e aplicações para que estejam disponíveis a partir de um local secundário se a sua localização primária diminuir.

Recuperação do local:

* **Simplifica a sua estratégia BCDR**: A Recuperação do Site facilita o manusear a replicação, o failover e a recuperação de múltiplas cargas de trabalho e aplicações de negócios a partir de um único local. A Recuperação do Site orquestra a replicação e o failover, mas não interceta os dados da sua aplicação nem tem qualquer informação sobre o mesmo.
* **Fornece uma replicação flexível**: Ao utilizar a Recuperação do Site, pode replicar cargas de trabalho em funcionamento em máquinas virtuais Hiper-V, máquinas virtuais VMware e servidores físicos Windows/Linux.
* **Suportes de failover e recuperação**: A Recuperação do Local fornece falhas de teste para apoiar exercícios de recuperação de desastres sem afetar ambientes de produção. Também pode executar as ativações pós-falha planeadas sem nenhuma perda de dados para as falhas esperadas ou as ativações pós-falha não planeadas com perda mínima de dados (dependendo da frequência de replicação) perante desastres inesperados. Depois de falhar, pode falhar de volta aos seus locais primários. A Recuperação de Sites fornece planos de recuperação que podem incluir scripts e livros de automatização do Azure, para que possa personalizar a ativação pós-falha e a recuperação de aplicações de várias camadas.
* **Elimina centros de dados secundários**: Pode replicar-se num local secundário no local ou em Azure. Usar o Azure como destino para a recuperação de desastres elimina o custo e a complexidade da manutenção de um local secundário. Os dados replicados são armazenados no Azure Storage.
* **Integra-se com as tecnologias BCDR existentes**: Parceiros de Recuperação de Sítios com funcionalidades BCDR de outras aplicações. Por exemplo, pode utilizar a Recuperação do Site para ajudar a proteger o fim do SQL Server das cargas de trabalho corporativas. Isto inclui suporte nativo para SQL Server Always On para gerir o failover de grupos de disponibilidade.

Saiba mais:

* [O que é Azure Site Recovery?](../../site-recovery/site-recovery-overview.md)
* [Como funciona o Azure Site Recovery?](/azure/site-recovery/azure-to-azure-architecture)
* [Que cargas de trabalho estão protegidas pela Recuperação do Sítio Azure?](../../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Redes virtuais

As máquinas virtuais precisam de conectividade de rede. Para suportar este requisito, o Azure exige que as máquinas virtuais sejam ligadas a uma rede virtual Azure.

Uma rede virtual Azure é uma construção lógica construída em cima do tecido físico da rede Azure. Cada rede virtual lógica Azure está isolada de todas as outras redes virtuais Azure. Este isolamento ajuda a garantir que o tráfego de rede nas suas implementações não seja acessível a outros clientes da Microsoft Azure.

Saiba mais:

* [Visão geral da segurança da rede Azure](network-overview.md)
* [Visão geral da rede virtual](../../virtual-network/virtual-networks-overview.md)
* [Funcionalidades de networking e parcerias para cenários empresariais](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gestão e reporte de políticas de segurança

O Centro de Segurança do Azure ajuda-o a evitar, detetar e responder a ameaças. O Centro de Segurança dá-lhe uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições Azure. Ajuda a detetar ameaças que de outra forma poderiam passar despercebidas, e trabalha com um amplo ecossistema de soluções de segurança.

O Security Center ajuda-o a otimizar e monitorizar a segurança das suas máquinas virtuais através de:

* Fornecendo [recomendações](../../security-center/security-center-recommendations.md) de segurança para as máquinas virtuais. As recomendações de exemplo incluem: aplicar atualizações do sistema, configurar os pontos finais de ACLs, permitir antimalware, ativar grupos de segurança de rede e aplicar encriptação de discos.
* A monitorizar o estado das suas máquinas virtuais.

Saiba mais:

* [Introdução ao Centro de Segurança do Azure](../../security-center/security-center-introduction.md)
* [O Centro de Segurança Azure frequentemente fez perguntas](../../security-center/faq-general.md)
* [Planeamento e operações do Centro de Segurança Azure](../../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Conformidade

A Azure Virtual Machines é certificada para FISMA, FedRAMP, HIPAA, PCI DSS Level 1 e outros programas de conformidade chave. Esta certificação facilita que as suas próprias aplicações Azure cumpram os requisitos de conformidade e que o seu negócio responda a uma vasta gama de requisitos regulamentares nacionais e internacionais.

Saiba mais:

* [Microsoft Trust Center: Compliance](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Nuvem Confiável: Microsoft Azure Security, Privacy e Compliance](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Computação Confidencial

Embora a computação confidencial não seja tecnicamente parte da segurança da máquina virtual, o tópico da segurança da máquina virtual pertence ao tema de nível mais alto de segurança "compute". A computação confidencial pertence à categoria de segurança "compute".

A computação confidencial garante que quando os dados estão "claros", o que é necessário para um processamento eficiente, os dados são protegidos dentro de um Ambiente de Execução Fidedigna  https://en.wikipedia.org/wiki/Trusted_execution_environment (TEE - também conhecido como enclave), um exemplo que é mostrado na figura abaixo.  

Os TEEs garantem que não há forma de visualizar dados ou operações dentro do exterior, mesmo com um depuração. Garantem mesmo que apenas o código autorizado é permitido aceder aos dados. Se o código for alterado ou adulterado, as operações são negadas e o ambiente desativado. O TEE aplica estas proteções ao longo da execução do código no seu interior.

Saiba mais:

* [Introdução da computação confidencial da Azure](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Computação confidencial do Azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Próximos passos

Saiba mais [sobre as melhores práticas](iaas.md) de segurança para VMs e sistemas operativos.