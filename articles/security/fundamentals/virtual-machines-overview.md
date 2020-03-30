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
ms.openlocfilehash: a1726e18ea8c1ba86d77d7b9ca3d50c444620361
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657168"
---
# <a name="azure-virtual-machines-security-overview"></a>Visão geral da segurança das Máquinas Virtuais Azure
Este artigo fornece uma visão geral das principais funcionalidades de segurança do Azure que podem ser usadas com máquinas virtuais.

Pode utilizar máquinas virtuais Azure para implementar uma vasta gama de soluções computorais de forma ágil. O serviço suporta os Serviços Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e Azure BizTalk Services. Assim, pode implementar qualquer carga de trabalho e qualquer idioma em quase qualquer sistema operativo.

Uma máquina virtual do Azure fornece-lhe a flexibilidade de virtualização sem ter de comprar e manter o hardware físico que executa a máquina virtual. Pode construir e implementar as suas aplicações com a garantia de que os seus dados estão protegidos e seguros em datacenters altamente seguros.

Com o Azure, pode construir soluções reforçadas em segurança e conformes que:

* Proteja as suas máquinas virtuais contra vírus e malware.
* Criptografe os seus dados sensíveis.
* Tráfego de rede seguro.
* Identifique e detete ameaças.
* Cumprir os requisitos de conformidade.  

## <a name="antimalware"></a>Antimalware

Com o Azure, pode utilizar software antimalware de fornecedores de segurança como microsoft, Symantec, Trend Micro e Kaspersky. Este software ajuda a proteger as suas máquinas virtuais de ficheiros maliciosos, adware e outras ameaças.

O Microsoft Antimalware para Serviços de Nuvem Azure e Máquinas Virtuais é uma capacidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares maliciosos.  O Microsoft Antimalware para o Azure fornece alertas configuráveis quando conhecidos software malicioso ou indesejado tenta instalar-se ou funcionar nos seus sistemas Azure.

O Microsoft Antimalware para o Azure é uma solução de agente único para aplicações e ambientes de inquilinos. É projetado para correr em segundo plano sem intervenção humana. Pode implementar proteção com base nas necessidades das cargas de trabalho da sua aplicação, com configuração personalizada básica de segurança por padrão ou avançada, incluindo monitorização antimalware.

Saiba mais sobre [o Microsoft Antimalware para o Azure](antimalware.md) e as principais funcionalidades disponíveis.

Saiba mais sobre o software antimalware para ajudar a proteger as suas máquinas virtuais:

* [Implementar Soluções Antimalware em Máquinas Virtuais do Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Como instalar e configurar a Trend Micro Deep Security como um serviço num VM windows](/azure/virtual-machines/windows/classic/install-trend)
* [Como instalar e configurar a Proteção de Endpoint Symantec num VM do Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Soluções de segurança no Mercado Azure](https://azure.microsoft.com/marketplace/?term=security)

Para uma proteção ainda mais poderosa, considere utilizar o [Windows Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). Com o Windows Defender ATP, obtém-se:

* [Redução da superfície de ataque](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Proteção da próxima geração](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Proteção e resposta de pontofinal](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Investigação e remediação automatizadas](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Pontuação segura](/windows/security/threat-protection/microsoft-defender-atp/configuration-score)
* [Caça avançada](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Gestão e APIs](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Proteção contra Ameaças da Microsoft](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Saiba mais:

* [Começar com a WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Visão geral das capacidades da WDATP](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Módulo de segurança de hardware

Melhorar a segurança das chaves pode melhorar as proteções de encriptação e autenticação. Pode simplificar a gestão e segurança dos seus segredos e chaves críticos armazenando-os no Cofre chave Azure.

A Key Vault oferece a opção de armazenar as suas chaves em módulos de segurança de hardware (HSMs) certificados para os padrões FIPS 140-2 Level 2. As chaves de encriptação do SQL Server para [encriptação](https://msdn.microsoft.com/library/bb934049.aspx) de dados de backup ou transparente podem ser armazenadas no Key Vault com quaisquer chaves ou segredos das suas aplicações. As permissões e o acesso a estes itens protegidos são geridos através do [Diretório Ativo Azure.](https://azure.microsoft.com/documentation/services/active-directory/)

Saiba mais:

* [O que é o cofre de chave do Azure?](/azure/key-vault/key-vault-overview)
* [Blog Azure Key Vault](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Encriptação de disco de máquina virtual

A Encriptação azure Disk é uma nova capacidade para encriptar os discos virtuais Windows e Linux. A Encriptação do Disco Azure utiliza a funcionalidade [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão da indústria do Windows e a funcionalidade [dm-cripta](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer encriptação de volume para o SISTEMA e os discos de dados.

A solução está integrada com o Azure Key Vault para ajudá-lo a controlar e gerir as chaves e segredos de encriptação do disco na subscrição do cofre chave. Garante que todos os dados nos discos de máquinas virtuais são encriptados em repouso no Armazenamento Azure.

Saiba mais:

* [Encriptação de disco azure para VMs IaaS](/azure/security/azure-security-disk-encryption-overview)
* [Início Rápido: Encriptar uma VM IaaS do Windows com o Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Backup de máquina virtual

O Azure Backup é uma solução escalável que ajuda a proteger os dados da sua aplicação com investimento de capital zero e custos mínimos de operação. Erros de aplicação podem corromper os seus dados, e erros humanos podem introduzir bugs nas suas aplicações. Com o Azure Backup, as suas máquinas virtuais que executam windows e Linux estão protegidas.

Saiba mais:

* [O que é o Backup do Azure?](/azure/backup/backup-introduction-to-azure-backup)
* [FaQ do serviço de backup Azure](/azure/backup/backup-azure-backup-faq)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Uma parte importante da estratégia BCDR da sua organização é descobrir como manter as cargas de trabalho corporativas e aplicações em funcionamento quando ocorrem interrupções planeadas e não planeadas. A Azure Site Recovery ajuda a orquestrar a replicação, a falha e a recuperação de cargas de trabalho e apps para que estejam disponíveis a partir de um local secundário se a sua localização primária descer.

Recuperação do site:

* **Simplifica a sua estratégia BCDR**: A Recuperação do Site facilita o manuseamento, falha e recuperação de várias cargas de trabalho e apps de negócios a partir de um único local. A Recuperação do Site orquestra a replicação e o failover, mas não interceta os dados da sua aplicação nem tem qualquer informação sobre o mesmo.
* **Fornece replicação flexível**: Ao utilizar a Recuperação do Site, pode replicar cargas de trabalho em máquinas virtuais Hyper-V, máquinas virtuais VMware e servidores físicos Windows/Linux.
* **Suportes failover e recuperação**: A Recuperação do Local fornece falhas de teste para apoiar exercícios de recuperação de desastres sem afetar os ambientes de produção. Também pode executar as ativações pós-falha planeadas sem nenhuma perda de dados para as falhas esperadas ou as ativações pós-falha não planeadas com perda mínima de dados (dependendo da frequência de replicação) perante desastres inesperados. Depois do fracasso, pode falhar de volta aos seus locais primários. A Recuperação de Sites fornece planos de recuperação que podem incluir scripts e livros de automatização do Azure, para que possa personalizar a ativação pós-falha e a recuperação de aplicações de várias camadas.
* **Elimina centros de dados secundários:** Pode replicar-se para um local secundário no local, ou para o Azure. Usar o Azure como destino para a recuperação de desastres elimina o custo e a complexidade da manutenção de um local secundário. Os dados replicados são armazenados no Armazenamento Azure.
* **Integra-se com as tecnologias BCDR existentes**: Parceiros de Recuperação do Site com as funcionalidades BCDR de outras aplicações. Por exemplo, pode utilizar a Recuperação do Site para ajudar a proteger o back back end do SQL Server das cargas de trabalho corporativas. Isto inclui suporte nativo para O Servidor SQL Always On para gerir a falha de grupos de disponibilidade.

Saiba mais:

* [O que é o Azure Site Recovery?](/azure/site-recovery/site-recovery-overview)
* [Como funciona o Azure Site Recovery?](/azure/site-recovery/site-recovery-components)
* [Que cargas de trabalho estão protegidas pela Recuperação do Site Azure?](/azure/site-recovery/site-recovery-workload)

## <a name="virtual-networking"></a>Redes virtuais

As máquinas virtuais precisam de conectividade de rede. Para suportar esta exigência, o Azure exige que as máquinas virtuais sejam ligadas a uma rede virtual Azure.

Uma rede virtual Azure é uma construção lógica construída em cima do tecido de rede azul a física. Cada rede virtual lógica do Azure está isolada de todas as outras redes virtuais Azure. Este isolamento ajuda a garantir que o tráfego de rede nas suas implementações não é acessível a outros clientes do Microsoft Azure.

Saiba mais:

* [Visão geral da segurança da rede Azure](network-overview.md)
* [Visão geral da Rede Virtual](/azure/virtual-network/virtual-networks-overview)
* [Recursos de networking e parcerias para cenários empresariais](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gestão e relatório de políticas de segurança

O Centro de Segurança do Azure ajuda-o a evitar, detetar e responder a ameaças. O Centro de Segurança dá-lhe maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições do Azure. Ajuda a detetar ameaças que de outra forma podem passar despercebidas e trabalha com um vasto ecossistema de soluções de segurança.

O Security Center ajuda-o a otimizar e monitorizar a segurança das suas máquinas virtuais:

* Fornecendo [recomendações](/azure/security-center/security-center-recommendations) de segurança para as máquinas virtuais. As recomendações de exemplo incluem: aplicar atualizações do sistema, configurar pontos finais de ACLs, ativar antimalware, ativar grupos de segurança de rede e aplicar encriptação de disco.
* Monitorizando o estado das suas máquinas virtuais.

Saiba mais:

* [Introdução ao Centro de Segurança do Azure](/azure/security-center/security-center-intro)
* [Centro de Segurança Azure frequentemente fez perguntas](/azure/security-center/security-center-faq)
* [Planeamento e operações do Centro de Segurança Azure](/azure/security-center/security-center-planning-and-operations-guide)

## <a name="compliance"></a>Conformidade

As Máquinas Virtuais Azure são certificadas para FISMA, FedRAMP, HIPAA, PCI DSS Level 1 e outros programas de conformidade chave. Esta certificação facilita que as suas próprias aplicações Azure cumpram os requisitos de conformidade e que o seu negócio responda a uma vasta gama de requisitos regulamentares nacionais e internacionais.

Saiba mais:

* [Microsoft Trust Center: Compliance](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Cloud fidedigna: Segurança, Privacidade e Conformidade do Microsoft Azure](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Computação Confidencial

Embora a computação confidencial não seja tecnicamente parte da segurança virtual das máquinas, o tema da segurança das máquinas virtuais pertence ao tema de nível mais elevado de segurança "computacional". A computação confidencial pertence à categoria de segurança "computacional".

A computação confidencial garante que quando os dados estão "claros", o que é necessário https://en.wikipedia.org/wiki/Trusted_execution_environment para um tratamento eficiente, os dados são protegidos dentro de um Ambiente de Execução Fidedigno (TEE - também conhecido como enclave), um exemplo que é mostrado na figura abaixo.  

Os TEEs garantem que não há forma de visualizar os dados ou as operações no interior do exterior, mesmo com um desordeiro. Garantem mesmo que só é permitido o código autorizado aceder aos dados. Se o código for alterado ou adulterado, as operações são negadas e o ambiente desativado. O TEE impõe estas proteções ao longo da execução do código no seu interior.

Saiba mais:

* [Introdução de computação confidencial azure](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Computação confidencial azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Passos seguintes

Conheça as [melhores práticas](iaas.md) de segurança para VMs e sistemas operativos.
