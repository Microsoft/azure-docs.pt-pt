---
title: Recursos de segurança usados com máquinas virtuais do Azure – segurança do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos principais recursos de segurança do Azure que podem ser usados com as máquinas virtuais do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 7b33484084b4ada5aeaf89eb90167658ade15ad8
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899790"
---
# <a name="azure-virtual-machines-security-overview"></a>Visão geral de segurança de máquinas virtuais do Azure
Este artigo fornece uma visão geral dos principais recursos de segurança do Azure que podem ser usados com máquinas virtuais.

Você pode usar as máquinas virtuais do Azure para implantar uma ampla variedade de soluções de computação de maneira ágil. O serviço dá suporte aos serviços do Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e BizTalk do Azure. Assim, você pode implantar qualquer carga de trabalho e qualquer linguagem em praticamente qualquer sistema operacional.

Uma máquina virtual do Azure fornece-lhe a flexibilidade de virtualização sem ter de comprar e manter o hardware físico que executa a máquina virtual. Você pode criar e implantar seus aplicativos com a garantia de que seus dados estão protegidos e seguros em datacenters altamente seguros.

Com o Azure, você pode criar soluções compatíveis com segurança aprimoradas que:

* Proteja suas máquinas virtuais contra vírus e malware.
* Criptografe seus dados confidenciais.
* Proteger o tráfego de rede.
* Identifique e detecte ameaças.
* Atenda aos requisitos de conformidade.  

## <a name="antimalware"></a>Antimalware

Com o Azure, você pode usar o software antimalware de fornecedores de segurança, como Microsoft, Symantec, Trend Micro e Kaspersky. Esse software ajuda a proteger suas máquinas virtuais contra arquivos mal-intencionados, adware e outras ameaças.

O Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais é um recurso de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados.  O Microsoft antimalware para Azure fornece alertas configuráveis quando um software mal-intencionado ou indesejado conhecido tenta se instalar ou executar em seus sistemas do Azure.

O Microsoft antimalware para Azure é uma solução de agente único para aplicativos e ambientes de locatário. Ele foi projetado para ser executado em segundo plano sem intervenção humana. Você pode implantar a proteção com base nas necessidades de suas cargas de trabalho de aplicativo, com a configuração básica de segurança padrão ou personalizada avançada, incluindo o monitoramento antimalware.

Quando você implanta e habilita o Microsoft antimalware para o Azure, os seguintes recursos principais estão disponíveis:

* **Proteção em tempo real**: Monitora a atividade em serviços de nuvem e em máquinas virtuais para detectar e bloquear a execução de malware.
* **Verificação agendada**: Executa periodicamente a verificação direcionada para detectar malware, incluindo programas ativamente em execução.
* **Correção de malware**: Executa automaticamente a ação de malware detectado, como excluir ou colocar arquivos mal-intencionados em quarentena e limpar entradas de registro mal-intencionadas.
* **Atualizações de assinatura**: O instala automaticamente as assinaturas de proteção mais recentes (definições de vírus) para garantir que a proteção esteja atualizada em uma frequência predeterminada.
* **Atualizações do Mecanismo Antimalware**: Atualiza automaticamente o Microsoft antimalware para o mecanismo do Azure.
* **Atualizações da plataforma Antimalware**: Atualiza automaticamente a plataforma Microsoft antimalware para Azure.
* **Proteção ativa**: Relata metadados de telemetria para o Azure sobre ameaças detectadas e recursos suspeitos para garantir uma resposta rápida. Permite a entrega de assinatura síncrona em tempo real por meio do Microsoft Active Protection System (MAPS).
* **Relatórios de amostras**: Fornece e relata exemplos para o serviço Microsoft antimalware para Azure para ajudar a refinar o serviço e habilitar a solução de problemas.
* **Exclusões**: Permite que os administradores de aplicativos e serviços configurem determinados arquivos, processos e unidades para excluí-los da proteção e da verificação de desempenho e outros motivos.
* **Coleta de eventos**de AntiMalware: Registra a integridade do serviço Antimalware, atividades suspeitas e ações de correção executadas no log de eventos do sistema operacional e as coleta em sua conta de armazenamento do Azure.

Saiba mais sobre o software antimalware para ajudar a proteger suas máquinas virtuais:

* [Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais](antimalware.md)
* [Implementar Soluções Antimalware em Máquinas Virtuais do Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Como instalar e configurar o Trend Micro Deep Security as a Service em uma VM do Windows](/azure/virtual-machines/windows/classic/install-trend)
* [Como instalar e configurar o Endpoint Protection da Symantec em uma VM do Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Soluções de segurança no Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Para uma proteção ainda mais poderosa, considere o uso da [proteção avançada contra ameaças do Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). Com o Windows Defender ATP, você obtém:

* [Redução da superfície de ataque](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Proteção da próxima geração](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Endpoint Protection e resposta](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Investigação e correção automatizadas](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Pontuação segura](/windows/security/threat-protection/windows-defender-atp/overview-secure-score-windows-defender-advanced-threat-protection)
* [Busca avançada](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Gerenciamento e APIs](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Proteção contra ameaças da Microsoft](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Saiba mais:

* [Introdução ao WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Visão geral dos recursos do WDATP](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Módulo de segurança de hardware

Melhorar a segurança de chave pode aprimorar as proteções de criptografia e autenticação. Você pode simplificar o gerenciamento e a segurança de seus segredos e chaves críticos armazenando-os em Azure Key Vault.

O Key Vault oferece a opção de armazenar as suas chaves em módulos de hardware de segurança (HSMs) com certificação pela norma FIPS 140-2 de Nível 2. Suas chaves de criptografia SQL Server para backup ou Transparent [Data Encryption](https://msdn.microsoft.com/library/bb934049.aspx) podem ser armazenadas em Key Vault com quaisquer chaves ou segredos de seus aplicativos. As permissões e o acesso a esses itens protegidos são gerenciados por meio de [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Saiba mais:

* [O que é Azure Key Vault?](/azure/key-vault/key-vault-overview)
* [Blog de Azure Key Vault](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Criptografia de disco de máquina virtual

Azure Disk Encryption é um novo recurso para criptografar seus discos de máquina virtual Windows e Linux. O Azure Disk Encryption usa o recurso [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão do setor do Windows e o recurso [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o sistema operacional e os discos de dados.

A solução é integrada com Azure Key Vault para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e os segredos em sua assinatura do cofre de chaves. Ele garante que todos os dados nos discos da máquina virtual sejam criptografados em repouso no armazenamento do Azure.

Saiba mais:

* [Azure Disk Encryption para VMs IaaS](/azure/security/azure-security-disk-encryption-overview)
* [Quickstart: Criptografar uma VM IaaS do Windows com Azure PowerShell](../azure-disk-encryption-linux-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Cópia de segurança da máquina virtual

O backup do Azure é uma solução escalonável que ajuda a proteger os dados do aplicativo com zero investimento de capital e custos operacionais mínimos. Os erros das aplicações podem danificar os dados e os erros humanos podem introduzir erros nas suas aplicações. Com o backup do Azure, suas máquinas virtuais executando Windows e Linux são protegidas.

Saiba mais:

* [O que é o Azure Backup?](/azure/backup/backup-introduction-to-azure-backup)
* [Perguntas frequentes do serviço de backup do Azure](/azure/backup/backup-azure-backup-faq)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Uma parte importante da estratégia de BCDR da sua organização é descobrir como manter cargas de trabalho corporativas e aplicativos em execução quando ocorrem interrupções planejadas e não planejadas. Azure Site Recovery ajuda a orquestrar a replicação, o failover e a recuperação de cargas de trabalho e aplicativos para que fiquem disponíveis em um local secundário se o seu local principal ficar inativo.

Site Recovery:

* **Simplifica sua estratégia de BCDR**: Site Recovery facilita a manipulação de replicação, failover e recuperação de várias cargas de trabalho de negócios e aplicativos de um único local. Site Recovery orquestra a replicação e o failover, mas não intercepta os dados do aplicativo ou tem alguma informação sobre ele.
* **Fornece replicação flexível**: Usando Site Recovery, você pode replicar cargas de trabalho em execução em máquinas virtuais Hyper-V, máquinas virtuais VMware e servidores físicos Windows/Linux.
* **Dá suporte a failover e recuperação**: Site Recovery fornece failovers de teste para dar suporte a análises de recuperação de desastre sem afetar os ambientes de produção. Também pode executar as ativações pós-falha planeadas sem nenhuma perda de dados para as falhas esperadas ou as ativações pós-falha não planeadas com perda mínima de dados (dependendo da frequência de replicação) perante desastres inesperados. Após o failover, você pode fazer failback para seus sites primários. A Recuperação de Sites fornece planos de recuperação que podem incluir scripts e livros de automatização do Azure, para que possa personalizar a ativação pós-falha e a recuperação de aplicações de várias camadas.
* **Elimina data centers secundários**: Você pode replicar para um site secundário local ou para o Azure. O uso do Azure como destino para a recuperação de desastre elimina o custo e a complexidade da manutenção de um site secundário. Os dados replicados são armazenados no armazenamento do Azure.
* **Integra-se com as tecnologias BCDR existentes**: Site Recovery parceiros com os recursos de BCDR de outros aplicativos. Por exemplo, você pode usar Site Recovery para ajudar a proteger o SQL Server back-end de cargas de trabalho corporativas. Isso inclui suporte nativo para SQL Server Always On gerenciar o failover de grupos de disponibilidade.

Saiba mais:

* [O que é Azure Site Recovery?](/azure/site-recovery/site-recovery-overview)
* [Como funciona Azure Site Recovery?](/azure/site-recovery/site-recovery-components)
* [Quais cargas de trabalho são protegidas por Azure Site Recovery?](/azure/site-recovery/site-recovery-workload)

## <a name="virtual-networking"></a>Redes virtuais

As máquinas virtuais precisam de conectividade de rede. Para dar suporte a esse requisito, o Azure exige que as máquinas virtuais estejam conectadas a uma rede virtual do Azure.

Uma rede virtual do Azure é uma construção lógica criada sobre a malha de rede física do Azure. Cada rede virtual do Azure lógica é isolada de todas as outras redes virtuais do Azure. Esse isolamento ajuda a garantir que o tráfego de rede em suas implantações não seja acessível a outros clientes Microsoft Azure.

Saiba mais:

* [Visão geral da segurança de rede do Azure](network-overview.md)
* [Descrição Geral da Rede Virtual](/azure/virtual-network/virtual-networks-overview)
* [Recursos de rede e parcerias para cenários empresariais](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gerenciamento e relatórios de política de segurança

A central de segurança do Azure ajuda você a prevenir, detectar e responder a ameaças. A central de segurança oferece maior visibilidade e controle sobre a segurança de seus recursos do Azure. Ele fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure. Ele ajuda a detectar ameaças que poderiam passar despercebidas e trabalha com um amplo ecossistema de soluções de segurança.

A central de segurança ajuda a otimizar e monitorar a segurança de suas máquinas virtuais:

* Fornecendo [recomendações de segurança](/azure/security-center/security-center-recommendations) para as máquinas virtuais. As recomendações de exemplo incluem: aplicar atualizações do sistema, configurar pontos de extremidade de ACLs, habilitar Antimalware, habilitar grupos de segurança de rede e aplicar a criptografia de disco.
* Monitorando o estado de suas máquinas virtuais.

Saiba mais:

* [Introdução ao Centro de Segurança do Azure](/azure/security-center/security-center-intro)
* [Perguntas frequentes sobre a central de segurança do Azure](/azure/security-center/security-center-faq)
* [Planejamento e operações da central de segurança do Azure](/azure/security-center/security-center-planning-and-operations-guide)

## <a name="compliance"></a>Conformidade

As máquinas virtuais do Azure são certificadas para FISMA, FedRAMP, HIPAA, PCI DSS nível 1 e outros programas importantes de conformidade. Essa certificação torna mais fácil para seus próprios aplicativos do Azure atender aos requisitos de conformidade e para que seu negócio atenda a uma ampla gama de requisitos regulatórios nacionais e internacionais.

Saiba mais:

* [Central de confiabilidade da Microsoft: Regulamenta](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Nuvem confiável: Segurança, privacidade e conformidade do Microsoft Azure](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Computação confidencial

Embora a computação confidencial não seja tecnicamente parte da segurança da máquina virtual, o tópico da segurança da máquina virtual pertence ao assunto de "computação" de nível superior. A computação confidencial pertence dentro da categoria de segurança de "computação".

A computação confidencial garante que quando os dados estiverem "descriptografados", que são necessários para um processamento eficiente, os dados serão protegidos dentro de https://en.wikipedia.org/wiki/Trusted_execution_environment um ambiente de execução confiável (também conhecido como enclave), um exemplo do que é mostrado na figura abaixo .  

TEEs garantir que não haja nenhuma maneira de exibir dados ou as operações dentro de fora, mesmo com um depurador. Eles até mesmo garantem que apenas código autorizado tenha permissão para acessar dados. Se o código for alterado ou adulterado, as operações serão negadas e o ambiente será desabilitado. O "t" aplica essas proteções em toda a execução de código dentro dela.

Saiba mais:

* [Apresentando a computação confidencial do Azure](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Computação confidencial do Azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  
