---
title: Funcionalidades de segurança do Azure utilizadas com máquinas virtuais do Azure | Documentos da Microsoft
description: Este artigo fornece uma descrição geral das principais funcionalidades de segurança do Azure que podem ser utilizadas com máquinas virtuais do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: fb6a984ff838305b4ce411538465c0b9b5c152da
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886919"
---
# <a name="azure-virtual-machines-security-overview"></a>Descrição geral da segurança de máquinas virtuais do Azure
Pode utilizar máquinas virtuais do Azure para implementar uma vasta gama de soluções de computação de forma ágil. O serviço suporta o Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e BizTalk Services do Azure. Portanto, pode implementar qualquer carga de trabalho e qualquer linguagem em praticamente qualquer sistema operativo.

Uma máquina virtual do Azure fornece-lhe a flexibilidade de virtualização sem ter de comprar e manter o hardware físico que executa a máquina virtual. Pode criar e implantar seus aplicativos com a garantia de que os dados estão protegidos e seguros nos datacenters de alta segurança.

Com o Azure, pode criar soluções compatíveis com segurança avançada, que:

* Proteja as suas máquinas virtuais contra vírus e malware.
* Criptografe seus dados confidenciais.
* Proteger o tráfego de rede.
* Identifique e detete ameaças.
* Cumprir os requisitos de conformidade.

O objetivo deste artigo é fornecer uma visão geral das principais funcionalidades de segurança do Azure que podem ser utilizadas com máquinas virtuais. Links para artigos fornecem detalhes de cada funcionalidade, para que possa saber mais.  

## <a name="antimalware"></a>Antimalware
Com o Azure, pode utilizar o software antimalware dos fornecedores de segurança, como a Microsoft, Symantec, Trend Micro e Kaspersky. Este software ajuda a proteger as suas máquinas virtuais contra ficheiros maliciosos, adware e outras ameaças.

O Microsoft Antimalware para serviços Cloud do Azure e máquinas virtuais é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outro software malicioso.  O Microsoft Antimalware para o Azure fornece alertas configuráveis quando conhecido software malicioso ou indesejável se tentar instalar-se ou executar em seus sistemas do Azure.

Microsoft Antimalware para o Azure é uma solução de agente de único para aplicações e ambientes de inquilino. Foi concebido para ser executado em segundo plano sem intervenção humana. Pode implementar proteção com base nas necessidades de suas cargas de trabalho de aplicação, com qualquer um dos básica seguras por padrão ou avançadas de configuração personalizada, incluindo a monitorização de antimalware.

Quando implementar e ativar o Antimalware da Microsoft para o Azure, os principais recursos seguintes estão disponíveis:

* **Proteção em tempo real**: monitoriza atividades nos serviços Cloud e nas máquinas virtuais para detetar e bloquear a execução de software maligno.
* **Agendado a análise**: realiza periodicamente a análise de destino para detetar software maligno, incluindo programas ativamente em execução.
* **Remediação de software maligno**: automaticamente age em software maligno detetado, como a eliminação ou os colocar em quarentena ficheiros maliciosos e limpeza de entradas de registro malicioso.
* **Atualizações de assinatura**: instala automaticamente as últimas assinaturas de proteção (definições de vírus) para se certificar de que a proteção está atualizada numa frequência previamente determinada.
* **Atualizações do mecanismo antimalware**: atualiza automaticamente o Microsoft Antimalware para o motor do Azure.
* **Atualizações de plataforma antimalware**: atualiza automaticamente o Microsoft Antimalware para a plataforma Azure.
* **Proteção ativa**: metadados de telemetria de relatórios para o Azure sobre ameaças detetadas e os recursos suspeitos para garantir que a resposta rápida. Habilita a entrega de assinatura de síncrono em tempo real através do sistema de proteção ativa Microsoft (MAPS).
* **Exemplos da geração de relatórios**: fornece e relatórios de exemplos para o Microsoft Antimalware para o serviço do Azure para ajudar a refinar o serviço e ativar a solução de problemas.
* **Exclusões**: permite que aplicativos e administradores de serviço configurar a determinados ficheiros, processos e unidades para os excluir da proteção e a análise de desempenho e outros motivos.
* **Recolha de eventos de antimalware**: regista o estado de funcionamento do antimalware service, atividades suspeitas e ações de remediação executadas no registo de eventos de sistema operativo e recolhe-los na sua conta de armazenamento do Azure.

Saiba mais sobre o software antimalware para ajudar a proteger as suas máquinas virtuais:

* [O Microsoft Antimalware para serviços Cloud do Azure e máquinas virtuais](azure-security-antimalware.md)
* [Implementar Soluções Antimalware em Máquinas Virtuais do Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Como instalar e configurar o Trend Micro Deep Security como serviço numa VM do Windows](../virtual-machines/windows/classic/install-trend.md)
* [Como instalar e configurar o Symantec Endpoint Protection numa VM do Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Soluções de segurança no Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Módulo de hardware de segurança
Aprimorando a segurança de chave pode melhorar as proteções de criptografia e autenticação. Pode simplificar a gestão e segurança dos seus segredos e chaves críticos, armazenando-os no Azure Key Vault. 

O Key Vault oferece a opção de armazenar as suas chaves em módulos de hardware de segurança (HSMs) com certificação pela norma FIPS 140-2 de Nível 2. Chaves de encriptação do SQL Server para cópia de segurança ou [encriptação de dados transparente](https://msdn.microsoft.com/library/bb934049.aspx) podem ser armazenadas no Key Vault com quaisquer chaves ou segredos das suas aplicações. Permissões e acesso a estes itens protegidos são geridos através de [do Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Saiba mais:

* [O que é o Azure Key Vault?](../key-vault/key-vault-whatis.md)
* [Introdução ao Azure Key Vault](../key-vault/key-vault-get-started.md)
* [Blogue do Azure Key Vault](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Encriptação de disco da máquina virtual
O Azure Disk Encryption é uma nova funcionalidade para encriptar discos da máquina virtual Windows e Linux. O Azure Disk Encryption utiliza o padrão da indústria [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) recurso do Windows e o [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) funcionalidade do Linux para fornecer encriptação de volume para o sistema operacional e os discos de dados.

A solução está integrada no Azure Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves. Ele garante que todos os dados nos discos da máquina virtual são encriptados em inatividade no armazenamento do Azure.

Saiba mais:

* [Azure Disk Encryption para IaaS VMs](../security/azure-security-disk-encryption-overview.md)
* [Início rápido: Encriptar uma VM de IaaS do Windows com o Azure PowerShell](../security/quick-encrypt-vm-powershell.md)

## <a name="virtual-machine-backup"></a>Cópia de segurança da máquina virtual
O Azure Backup é uma solução dimensionável que ajuda a proteger os dados da sua aplicação com nenhum investimento capital e custos de funcionamento mínimos. Os erros das aplicações podem danificar os dados e os erros humanos podem introduzir erros nas suas aplicações. Com o Azure Backup, as máquinas virtuais que executam o Windows e Linux são protegidas.

Saiba mais:

* [O que é o Azure Backup?](../backup/backup-introduction-to-azure-backup.md)
* [Percurso de aprendizagem de cópia de segurança do Azure](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [FAQ do serviço de cópia de segurança do Azure](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Uma parte importante da estratégia BCDR da sua organização é perceber como manter as cargas de trabalho corporativas e as aplicações em execução quando planeada e falhas não planeadas ocorrerem. O Azure Site Recovery ajuda a orquestrar a replicação, ativação pós-falha e recuperação de cargas de trabalho e aplicações para que eles estão disponíveis a partir de uma localização secundária se a sua localização principal ficar inativo.

Recuperação de sites:

* **Simplifica a sua estratégia BCDR**: recuperação de Site torna mais fácil processar a replicação, ativação pós-falha e recuperação de várias aplicações e cargas de trabalho empresariais a partir de uma única localização. Recuperação de sites orquestra a replicação e ativação pós-falha, mas não intercetar dados da sua aplicação ou informação sobre ele.
* **Proporciona replicação flexível**: ao utilizar o Site Recovery, pode replicar cargas de trabalho em execução em máquinas de virtuais de Hyper-V, máquinas virtuais VMware e servidores físicos Windows/Linux.
* **Suporta a ativação pós-falha e recuperação**: recuperação de sites fornece ativações pós-falha de teste para oferecer suporte a testes de recuperação após desastre sem afetar os ambientes de produção. Também pode executar as ativações pós-falha planeadas sem nenhuma perda de dados para as falhas esperadas ou as ativações pós-falha não planeadas com perda mínima de dados (dependendo da frequência de replicação) perante desastres inesperados. Após a ativação pós-falha, pode efetuar a ativação para os sites primários. A Recuperação de Sites fornece planos de recuperação que podem incluir scripts e livros de automatização do Azure, para que possa personalizar a ativação pós-falha e a recuperação de aplicações de várias camadas.
* **Elimina os datacenters secundários**: pode replicar para um site secundário no local ou para o Azure. Utilizar o Azure como um destino para recuperação após desastre, elimina o custo e a complexidade de manter um site secundário. Os dados replicados são armazenados no armazenamento do Azure.
* **Integra-se com tecnologias BCDR existentes**: parceiros da recuperação de sites com funcionalidades de BCDR dos outros aplicativos. Por exemplo, pode utilizar o Site Recovery para ajudar a proteger o SQL Server back-end de cargas de trabalho empresariais. Isto inclui suporte nativo para o SQL Server Always On gerir a ativação pós-falha de grupos de disponibilidade.

Saiba mais:

* [O que é o Azure Site Recovery?](../site-recovery/site-recovery-overview.md)
* [Como funciona o Azure Site Recovery?](../site-recovery/site-recovery-components.md)
* [Que cargas de trabalho estão protegidas pelo Azure Site Recovery?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Redes virtuais
As máquinas virtuais têm conectividade de rede. Para oferecer suporte a esse requisito, o Azure requer máquinas virtuais para ser ligada a uma rede virtual do Azure. 

Uma rede virtual do Azure é uma construção lógica criada sobre os recursos de infraestrutura do Azure de rede física. Cada rede virtual do Azure lógica é isolada de todas as outras redes virtuais do Azure. Esse isolamento ajuda a assegurar que o tráfego de rede das implementações não está acessível a outros clientes do Microsoft Azure.

Saiba mais:

* [Descrição geral da segurança de rede do Azure](security-network-overview.md)
* [Descrição Geral da Rede Virtual](../virtual-network/virtual-networks-overview.md)
* [Recursos de rede e parcerias para cenários de empresa](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gestão de políticas de segurança e relatórios
Centro de segurança do Azure ajuda-o a prevenir, detetar e responder a ameaças. Fornece o Centro de segurança maior visibilidade e controlo sobre a segurança dos seus recursos do Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure. Ele ajuda a detetar ameaças que caso contrário podem passar despercebidas e funcionam com um ecossistema abrangente de soluções de segurança.

Centro de segurança ajuda-o a otimizar e monitorizar a segurança das suas máquinas virtuais por:

* Fornecendo [recomendações de segurança](../security-center/security-center-recommendations.md) para as máquinas virtuais. Recomendações de exemplo incluem: aplicar atualizações do sistema, configurar pontos finais de ACLs, ativar o antimalware, ativar grupos de segurança de rede e aplicar encriptação de disco.
* Monitorizar o estado das suas máquinas virtuais.

Saiba mais:

* [Introdução ao Centro de Segurança do Azure](../security-center/security-center-intro.md)
* [Perguntas mais frequentes sobre o Centro de segurança do Azure](../security-center/security-center-faq.md)
* [Operações e planeamento do Centro de segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Conformidade
Máquinas virtuais do Azure está certificado para FISMA, FedRAMP, HIPAA, PCI DSS nível 1 e outros programas de conformidade essenciais. Essa certificação torna mais fácil para as suas próprias aplicações do Azure satisfazer os requisitos de conformidade e para a sua empresa satisfaça uma ampla gama de requisitos de regulamentação nacionais e internacionais.

Saiba mais:

* [Microsoft Trust Center: conformidade](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Cloud de confiança: Segurança do Microsoft Azure, privacidade e conformidade](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
