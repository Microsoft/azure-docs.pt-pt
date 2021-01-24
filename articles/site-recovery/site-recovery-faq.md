---
title: Questões gerais sobre o serviço de Recuperação do Local Azure
description: Este artigo discute questões gerais populares sobre a Recuperação do Sítio Azure.
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: ca30f9ba190dfa3c7e224e47b90be4d3bc5d47ae
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746480"
---
# <a name="general-questions-about-azure-site-recovery"></a>Perguntas gerais sobre o Azure Site Recovery

Este artigo resume perguntas frequentes sobre a Recuperação do Sítio Azure. Para cenários específicos, reveja estes artigos

- [Perguntas sobre a recuperação após desastre da VM do Azure no Azure](azure-to-azure-common-questions.md)
- [Perguntas sobre a recuperação após desastre da VM do VMware no Azure](vmware-azure-common-questions.md)
- [Perguntas sobre a recuperação após desastre da VM Hyper-V no Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Geral

### <a name="what-does-site-recovery-do"></a>O que faz a Recuperação de Sites?

A Recuperação do Site contribui para a estratégia de continuidade do seu negócio e recuperação de desastres (BCDR), orquestrando e automatizando a replicação de VMs Azure entre regiões, máquinas virtuais no local e servidores físicos para a Azure, e máquinas no local para um centro de dados secundário. [Saiba mais](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Posso proteger uma máquina virtual que tem um disco Docker?

Não, este é um cenário não apoiado.

### <a name="what-does-site-recovery-do-to-ensure-data-integrity"></a>O que faz a Recuperação do Site para garantir a integridade dos dados?

Existem várias medidas tomadas pela Recuperação do Local para garantir a integridade dos dados. É estabelecida uma ligação segura entre todos os serviços utilizando o protocolo HTTPS. Isto garante que qualquer malware ou entidades externas não podem adulterar os dados. Outra medida tomada é a utilização de cheques. A transferência de dados entre a fonte e o alvo é executada através da computação de dados entre eles. Isto garante que os dados transferidos são consistentes.

## <a name="service-providers"></a>Fornecedores de serviços

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Sou um prestador de serviços. A Recuperação do Local funciona para modelos de infraestruturas dedicados e partilhados?
Sim, a Recuperação de Sites suporta os modelos de infraestrutura partilhados e dedicados.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Para um prestador de serviços, a identidade do meu inquilino é partilhada com o serviço de Recuperação do Local?
Não. A identidade do inquilino permanece anónima. Os seus inquilinos não precisam de acesso ao portal de recuperação de Recuperação de Sites. Apenas o administrador do fornecedor de serviços interage com o portal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Os dados da aplicação do inquilino irão para OZure?
Quando há replicação entre sites pertencentes ao fornecedor de serviços, os dados das aplicações nunca chegam ao Azure. Os dados são encriptados em trânsito e replicados diretamente entre os sites do prestador de serviços.

Se está a replicar para o Azure, os dados da aplicação são enviados para armazenamento do Azure, mas não para o serviço de Recuperação de Sites. Os dados são encriptados em trânsito e permanecem encriptados em Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Os meus inquilinos receberão uma fatura de quaisquer serviços do Azure?
Não. A relação de faturação do Azure é diretamente com o fornecedor de serviços. Os fornecedores de serviços são responsáveis por gerar faturas específicas para os seus inquilinos.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Se estiver a replicar para o Azure, necessitamos executar as máquinas virtuais no Azure todo o tempo?
Não, os dados são replicados no armazenamento Azure na sua subscrição. Quando efetua uma ativação pós-falha de teste (simulação de recuperação após desastre) ou uma ativação pós-falha real, a Recuperação de Sites cria automaticamente máquinas virtuais na sua subscrição.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Garantem o isolamento de inquilinos quando faço replicação para o Azure?
Yes.

### <a name="what-platforms-do-you-currently-support"></a>Quais as plataformas a que atualmente dão suporte?
Apoiamos implementações do Azure Pack, Cloud Platform System e System Center (2012 e superior). [Saiba mais](/previous-versions/azure/windows-server-azure-pack/dn850370(v=technet.10)) sobre a integração do Azure Pack e da Recuperação do Local.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Suportam implementações únicas de Azure Pack e de servidor VMM?
Sim, pode replicar máquinas virtuais Hyper-V para Azure, ou entre sites de prestadores de serviços.  Note que se replicar entre sites de prestadores de serviços, a integração do runbook Azure não está disponível.

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-pricing-information"></a>Onde posso encontrar informações sobre preços?
Rever [detalhes de preços de recuperação do site.](https://azure.microsoft.com/pricing/details/site-recovery/)


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Como posso calcular os encargos aproximados durante a utilização da Recuperação do Local?

Pode utilizar a [calculadora de preços](https://aka.ms/asr_pricing_calculator) para estimar os custos durante a utilização da Recuperação do Local.

Para uma estimativa detalhada dos custos, execute a ferramenta de planejador de implementação para [VMware](./site-recovery-deployment-planner.md) ou [Hyper-V,](https://aka.ms/asr-deployment-planner)e use o [relatório de estimativa de custos](./site-recovery-vmware-deployment-planner-cost-estimation.md).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Os discos geridos são agora utilizados para replicar VMware VMs e servidores físicos. Incorrer em encargos adicionais para a conta de armazenamento de cache com discos geridos?

Não, não há taxas adicionais para cache. Quando se replica na conta de armazenamento padrão, este armazenamento em cache faz parte da mesma conta de armazenamento alvo.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Sou um utilizador de recuperação do site Azure há mais de um mês. Ainda tenho os primeiros 31 dias livres para cada instância protegida?

Yes. Todas as instâncias protegidas não incorrem em taxas de recuperação do local de Azure nos primeiros 31 dias. Por exemplo, se tem estado a proteger 10 instâncias nos últimos 6 meses e ligar uma 11ª instância à Recuperação do Site Azure, não há encargos para a 11ª instância nos primeiros 31 dias. As primeiras 10 ocorrências continuam a incorrer em acusações de recuperação do local de Azure, uma vez que estão protegidas há mais de 31 dias.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, vou incorrer em outras acusações do Azure?

Sim, mesmo que a Recuperação do Site seja gratuita durante os primeiros 31 dias de uma instância protegida, poderá incorrer em encargos para o Armazenamento Azure, transações de armazenamento e transferência de dados. Uma máquina virtual recuperada também pode incorrer em cargas de computação Azure.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Existe um custo associado para a realização de exercícios de recuperação de desastres/falha de teste?

Não há um custo separado para a broca DR. Haverá taxas de cálculo após a criação do VM após a falha do teste.



## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Sites?
Não, a Recuperação do Site não interceta dados replicados e não tem qualquer informação sobre o que está a ser feito nas suas máquinas virtuais ou servidores físicos.
Os dados de replicação são trocados entre os anfitriões Hyper-V no local, os hipervisores VMware ou os servidores físicos, e o armazenamento do Azure ou o seu site secundário. A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.  

A Recuperação do Local é ISO 27001:2013, 27018, HIPAA, DPA certificada, e está em processo de avaliações SOC2 e FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Por razões de conformidade, até os nossos metadados no local devem permanecer na mesma região geográfica. A Recuperação do Local pode ajudar-nos?
Yes. Quando cria um cofre de recuperação de locais numa região, garantimos que todos os metadados que precisamos para permitir e orquestrar a replicação e falha permanecem dentro da fronteira geográfica dessa região.

### <a name="does-site-recovery-encrypt-replication"></a>A Recuperação de Sites faz encriptação de replicação?
Para máquinas virtuais e servidores físicos, é suportada a replicação entre sites no local de encriptação em trânsito. Para máquinas virtuais e servidores físicos que se replicam no Azure, são suportados tanto a encriptação em trânsito como a [encriptação em repouso (em Azure).](../storage/common/storage-service-encryption.md)

### <a name="does-azure-to-azure-site-recovery-use-tls-12-for-all-communications-across-microservices-of-azure"></a>A recuperação do sítio Azure-to-Azure utiliza TLS 1.2 para todas as comunicações em microserviços da Azure?
Sim, o protocolo TLS 1.2 é aplicado por padrão para o cenário de recuperação do local de Azure-to-Azure. 

### <a name="how-can-i-enforce-tls-12-on-vmware-to-azure-and-physical-server-to-azure-site-recovery-scenarios"></a>Como posso impor o TLS 1.2 em cenários de recuperação do site VMware-to-Azure e do Servidor Físico-a-Azure?
Os agentes de mobilidade instalados nos itens replicados comunicam ao Process Server apenas no TLS 1.2. No entanto, a comunicação do Servidor de Configuração para O Azure e do Process Server para o Azure pode estar no TLS 1.1 ou 1.0. Por favor, siga as [orientações](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) para impor o TLS 1.2 em todos os Servidores de Configuração e Servidores de Processos configurado por si.

### <a name="how-can-i-enforce-tls-12-on-hyperv-to-azure-site-recovery-scenarios"></a>Como posso impor o TLS 1.2 em cenários de recuperação do site HyperV-to-Azure?
Toda a comunicação entre os microserviços da Recuperação do Sítio Azure ocorre no protocolo TLS 1.2. A Recuperação do Site utiliza fornecedores de segurança configurados no sistema (OS) e utiliza o mais recente protocolo TLS disponível. Será necessário ativar explicitamente o TLS 1.2 no Registo e, em seguida, a Recuperação do Local começará a utilizar o TLS 1.2 para comunicação com serviços. 

### <a name="how-can-i-enforce-restricted-access-on-my-storage-accounts-which-are-accessed-by-site-recovery-service-for-readingwriting-replication-data"></a>Como posso impor o acesso restrito nas minhas contas de armazenamento, que são acedidas pelo serviço de Recuperação do Local para ler/escrever dados de replicação?
Pode ligar a identidade gerida do cofre dos serviços de recuperação indo para a definição *de Identidade.* Assim que o cofre for registado no Azure Ative Directory, pode ir às suas contas de armazenamento e dar as seguintes funções para o cofre:

- Contas de armazenamento baseadas em gestores de recursos (Tipo Padrão):
  - [Contribuinte](../role-based-access-control/built-in-roles.md#contributor)
  - [Contribuinte de Dados do Armazenamento de Blobs](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Contas de armazenamento baseadas em gestores de recursos (Tipo Premium):
  - [Contribuinte](../role-based-access-control/built-in-roles.md#contributor)
  - [Proprietário dos Dados do Armazenamento de Blobs](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Contas clássicas de armazenamento:
  - [Colaborador clássico da conta de armazenamento](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Papel clássico do serviço do operador chave de armazenamento de armazenamento](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

## <a name="disaster-recovery"></a>Recuperação após desastre

### <a name="what-can-site-recovery-protect"></a>O que pode proteger a Recuperação do Local?
* **Azure VMs**: Recuperação do local pode replicar qualquer carga de trabalho em execução num Azure VM suportado
* **Máquinas virtuais hiper-V**: A recuperação do local pode proteger qualquer carga de trabalho em funcionamento num VM Hiper-V.
* **Servidores físicos**: A Recuperação do Site pode proteger servidores físicos que executam o Windows ou o Linux.
* **VMware máquinas virtuais**: A Recuperação do Local pode proteger qualquer carga de trabalho em execução num VMware VM.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quais as cargas de trabalho que podem ser protegidas com a Recuperação de Sites ?
Pode utilizar a Recuperação do Site para proteger a maioria das cargas de trabalho em execução num VM ou servidor físico suportado. A Recuperação do Site fornece suporte para replicação consciente da aplicação, para que as aplicações possam ser recuperadas para um estado inteligente. Integra-se com aplicações da Microsoft como SharePoint, Exchange, Dynamics, SQL Server e Ative Directory, e trabalha em estreita colaboração com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Posso gerir a recuperação após desastre para as minhas sucursais com a Recuperação de Sites?
Yes. Quando utilizar o Site Recovery para orquestrar a replicação e o failover nas suas filiais, terá uma orquestração unificada e uma visão de todas as cargas de trabalho da sua filial numa localização central. Pode facilmente executar as ativações pós-falha e administrar a recuperação após desastre de todas as sucursais a partir da sede, sem visitar as sucursais.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>A recuperação de desastres é apoiada para os VMs do Azure?

Sim, a Recuperação do Local apoia o desastre para os VMs Azure entre as regiões de Azure. [Reveja questões comuns](azure-to-azure-common-questions.md) sobre a recuperação de desastres em Azure VM. Se quiser replicar entre duas regiões do Azure no mesmo continente, por favor, use a nossa oferta Azure to Azure DR. Não é necessário configurar o servidor de configuração/servidor de processo e as ligações ExpressRoute.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>A recuperação de desastres é suportada para VMware VMs?

Sim, a Recuperação do Local suporta a recuperação de desastres de VMware VMware no local. [Reveja questões comuns](vmware-azure-common-questions.md) para a recuperação de desastres de VMware VMs.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>A recuperação de desastres é suportada para os Hiper-VMs?
Sim, a Recuperação do Local suporta a recuperação de desastres de Hiper-V VMs no local. [Reveja questões comuns](hyper-v-azure-common-questions.md) para a recuperação de desastres de VMs Hiper-V.

### <a name="is-disaster-recovery-supported-for-physical-servers"></a>A recuperação de desastres é suportada para servidores físicos?
Sim, a Recuperação do Site suporta a recuperação de desastres de servidores físicos no local que executam o Windows e o Linux para Azure ou para um site secundário. Conheça os requisitos para a recuperação de desastres para [Azure,](vmware-physical-azure-support-matrix.md#replicated-machines)e para[um local secundário.](vmware-physical-secondary-support-matrix.md#replicated-vm-support)
Note que os servidores físicos serão executados como VMs em Azure após o failover. O recuo do Azure para um servidor físico no local não é suportado atualmente. Só pode falhar de volta a uma máquina virtual VMware.





## <a name="replication"></a>Replicação

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Posso replicar sobre uma VPN local para Azure?
A Azure Site Recovery replica dados numa conta de armazenamento Azure ou discos geridos, sobre um ponto final público. A replicação não é sobre uma VPN local. 

### <a name="why-cant-i-replicate-over-vpn"></a>Por que não posso replicar sobre VPN?

Quando se replica em Azure, o tráfego de replicação atinge os pontos finais públicos de um Azure Storage. Assim, só pode replicar-se através da internet pública ou via ExpressRoute (olhando a Microsoft ou um olhar público existente).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Posso usar SteelHeads riverbed para replicação?

O nosso parceiro, Riverbed, fornece orientações detalhadas sobre o trabalho com a Azure Site Recovery. Reveja o seu [guia de solução.](https://community.riverbed.com/s/article/DOC-4627)

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Posso usar o ExpressRoute para replicar máquinas virtuais para o Azure?
Sim, [o ExpressRoute pode ser usado](concepts-expressroute-with-site-recovery.md) para replicar máquinas virtuais no local para Azure.

- A Azure Site Recovery replica dados para um Azure Storage sobre um ponto final público. É necessário configurar [a Microsoft a espreitar](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) ou utilizar um [espremia público](../expressroute/about-public-peering.md) existente (depreciado para novos circuitos) para utilizar o ExpressRoute para a replicação da recuperação do local.
- O espreitamento da Microsoft é o domínio de encaminhamento recomendado para replicação.
- A replicação não é suportada por um esprevamento privado.
- Se estiver a proteger máquinas VMware ou máquinas físicas, certifique-se de que os requisitos de rede para o Servidor de Configuração também estão [cumpridos.](vmware-azure-configuration-server-requirements.md#network-requirements) A conectividade com URLs específicos é necessária pelo Servidor de Configuração para orquestração da replicação da recuperação do local. O ExpressRoute não pode ser utilizado para esta conectividade.
- Depois de as máquinas virtuais terem falhado numa rede virtual Azure, pode aceder-lhes utilizando [a configuração de observação privada](../expressroute/expressroute-circuit-peerings.md#privatepeering) com a rede virtual Azure.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Se eu replicar para o Azure, que tipo de conta de armazenamento ou disco gerido preciso?

Precisa de um armazenamento LRS ou GRS. Recomendamos GRS para que os dados sejam resilientes se ocorrer uma falha regional ou se a região primária não pode ser recuperada. A conta tem de ser na mesma região que o cofre de Serviços de Recuperação. O armazenamento premium é suportado para VMware VM, Hiper-V VM e replicação do servidor físico, quando implementa a Recuperação do Local no portal Azure. Os discos geridos suportam apenas lRS.

### <a name="how-often-can-i-replicate-data"></a>Com que frequência posso replicar os dados?
* **Hiper-V:** Os VMs hiper-V podem ser replicados a cada 30 segundos (exceto para armazenamento premium), cinco minutos ou 15 minutos.
* **VMs Azure, VMware VMs, servidores físicos:** Uma frequência de replicação não é relevante aqui. A replicação é contínua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Posso estender a replicação do local de recuperação existente para outro local terciário?
Não é suportada a replicação expandida ou em cadeia. Solicite esta funcionalidade no [fórum de comentários.](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Posso fazer uma replicação offline pela primeira vez que faço a replicação para o Azure?
Tal não é suportado. Solicite esta funcionalidade no [fórum de comentários.](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)

### <a name="can-i-exclude-specific-disks-from-replication"></a>Posso excluir discos específicos na replicação?
Isto é suportado quando está a replicar VMs VMware e VMs hiper-V para Azure, utilizando o portal Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Posso replicar máquinas virtuais com discos dinâmicos?
Os discos dinâmicos são suportados ao replicar máquinas virtuais Hyper-V e ao replicar VMs VMware e máquinas físicas para Azure. O disco do sistema operativo deve ser um disco básico.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Posso acelerar a largura de banda atribuída para o tráfego de replicação?
Yes. Pode ler mais sobre a largura de banda de estrangulamento nestes artigos:

* [Planeamento de capacidade para replicar VMware VMs e servidores físicos](site-recovery-plan-capacity-vmware.md)
* [Planeamento de capacidade para replicar VMs hiper-V para Azure](./hyper-v-deployment-planner-overview.md)

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Posso permitir a replicação com consistência de aplicações nos servidores Linux? 
Yes. A Azure Site Recovery for Linux Operation System suporta scripts personalizados de aplicações para consistência de aplicações. O script personalizado com pré e pós-opções será usado pelo Agente de Mobilidade de Recuperação do Site Azure durante a consistência da aplicação. Abaixo estão os passos para o permitir.

1. Inscreva-se como raiz na máquina.
2. Alterar o diretório para o Agente de Mobilidade do Local de Azure instalar o local. O padrão é "/usr/local/ASR"<br>
    `# cd /usr/local/ASR`
3. Alterar o diretório para "VX/scripts" em localização de instalação<br>
    `# cd VX/scripts`
4. Crie um script de concha de bash chamado "customscript.sh" com permissões de execução para utilizador de raiz.<br>
    a. O script deve suportar opções de linha de comando "--pre" e "-post" (Note as linhas de comando duplas)<br>
    b. Quando o script é chamado com pré-opção, deve congelar a entrada/saída da aplicação e quando chamado com pós-opção, deve descongelar a entrada/saída da aplicação.<br>
    c. Um modelo de amostra -<br>

    `# cat customscript.sh`<br>

```
    #!/bin/bash

    if [ $# -ne 1 ]; then
        echo "Usage: $0 [--pre | --post]"
        exit 1
    elif [ "$1" == "--pre" ]; then
        echo "Freezing app IO"
        exit 0
    elif [ "$1" == "--post" ]; then
        echo "Thawed app IO"
        exit 0
    fi
```

5. Adicione o congelamento e descongele os comandos de entrada/saída em pré e pós-etapas para as aplicações que requerem consistência da aplicação. Pode optar por adicionar outro script especificando esses e invocá-lo de "customscript.sh" com pré e pós-opções.

>[!Note]
>A versão do agente de recuperação do site deve ser 9.24 ou superior para suportar scripts personalizados.

## <a name="replication-policy"></a>Política de replicação

### <a name="what-is-a-replication-policy"></a>O que é uma política de replicação?

Uma política de replicação define as definições para o histórico de retenção dos pontos de recuperação. A política também define a frequência de instantâneos consistentes com aplicações. Por predefinição, a Azure Site Recovery cria uma nova política de replicação com definições predefinidas de:

- 24 horas para o histórico de retenção de pontos de recuperação.
- 4 horas para a frequência de instantâneos consistentes com aplicações.

### <a name="what-is-a-crash-consistent-recovery-point"></a>O que é um ponto de recuperação consistente?

Um ponto de recuperação consistente com falhas tem os dados do disco como se tivesse retirado o cabo de alimentação do servidor durante a imagem. O ponto de recuperação consistente não inclui nada que estivesse na memória quando a foto foi tirada.

Hoje, a maioria das aplicações pode recuperar bem de instantâneos consistentes. Um ponto de recuperação consistente com falhas é geralmente suficiente para sistemas operativos sem base de dados e aplicações como servidores de ficheiros, servidores DHCP e servidores de impressão.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Qual é a frequência da geração de pontos de recuperação consistentes?

A Recuperação do Local cria um ponto de recuperação consistente a cada 5 minutos.

### <a name="what-is-an-application-consistent-recovery-point"></a>O que é um ponto de recuperação consistente de aplicações?

Os pontos de recuperação consistentes da aplicação são criados a partir de instantâneos consistentes com aplicações. Os pontos de recuperação consistentes da aplicação captam os mesmos dados que os instantâneos consistentes com falhas, ao mesmo tempo que capturam dados na memória e todas as transações em processo.

Devido ao seu conteúdo extra, as fotos consistentes com aplicações são as mais envolvidas e demoram mais tempo. Recomendamos pontos de recuperação consistentes de aplicações para sistemas operativos de base de dados e aplicações como o SQL Server.

>[!Note]
>A criação de pontos de recuperação consistentes com aplicações falha na máquina do Windows, se tiver mais de 64 volumes.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Qual é o impacto dos pontos de recuperação consistentes da aplicação no desempenho da aplicação?

Os pontos de recuperação consistentes da aplicação captam todos os dados na memória e no processo. Como os pontos de recuperação capturam esses dados, eles requerem uma estrutura como o Serviço de Cópia Sombra de Volume no Windows para quiesce a aplicação. Se o processo de captura for frequente, pode afetar o desempenho quando a carga de trabalho já está ocupada. Não recomendamos que utilize baixa frequência para pontos de recuperação consistentes com aplicações para cargas de trabalho não-base. Mesmo para a carga de trabalho da base de dados, uma hora é suficiente.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Qual é a frequência mínima de geração de pontos de recuperação consistentes de aplicação?

A Recuperação do Site pode criar um ponto de recuperação consistente com uma frequência mínima de 1 hora.

### <a name="how-are-recovery-points-generated-and-saved"></a>Como são gerados e salvos pontos de recuperação?

Para entender como a Recuperação do Site gera pontos de recuperação, vamos ver um exemplo de uma política de replicação. Esta política de replicação tem um ponto de recuperação com uma janela de retenção de 24 horas e um instantâneo de frequência consistente de 1 hora.

A Recuperação do Local cria um ponto de recuperação consistente a cada 5 minutos. Não pode mudar esta frequência. Durante a última hora, pode escolher entre 12 pontos consistentes com falhas e 1 ponto consistente com aplicações. À medida que o tempo avança, a Recuperação do Local adcade todos os pontos de recuperação para além da última hora e poupa apenas 1 ponto de recuperação por hora.

A imagem que se segue ilustra o exemplo. Na imagem:

- Na última hora, há pontos de recuperação com uma frequência de 5 minutos.
- Além da última hora, a Recuperação do Local mantém apenas 1 ponto de recuperação.

   ![Lista de pontos de recuperação gerados](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Até onde posso me recuperar?

O ponto de recuperação mais antigo que pode usar é de 72 horas.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Tenho uma política de replicação de 24 horas. O que acontecerá se um problema impedir a Recuperação do Local de gerar pontos de recuperação por mais de 24 horas? Os meus pontos de recuperação anteriores vão perder-se?

Não, a Recuperação do Site manterá todos os seus pontos de recuperação anteriores. Dependendo da janela de retenção dos pontos de recuperação, a Recuperação do Local substitui o ponto mais antigo apenas se gerar novos pontos. Devido ao problema, a Recuperação do Site não pode gerar novos pontos de recuperação. Até que haja novos pontos de recuperação, todos os pontos antigos permanecerão depois de chegar à janela de retenção.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Depois de a replicação ser ativada num VM, como posso alterar a política de replicação?

Aceda às políticas **de**  >  replicação da infraestrutura do local **de recuperação do local de recuperação do local.**  >   Selecione a política que pretende editar e guarde as alterações. Qualquer alteração aplicar-se-á também a todas as replicações existentes.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Todos os pontos de recuperação são uma cópia completa do VM ou um diferencial?

O primeiro ponto de recuperação gerado tem a cópia completa. Quaisquer pontos de recuperação sucessivos têm mudanças delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>O aumento do período de retenção dos pontos de recuperação aumenta o custo de armazenamento?

Sim, se aumentar o período de retenção de 24 horas para 72 horas, a Recuperação do Local poupará os pontos de recuperação por mais 48 horas. O tempo adicional incorrerá em taxas de armazenamento. Por exemplo, um único ponto de recuperação pode ter alterações delta de 10 GB com um custo por GB de $0,16 por mês. Os custos adicionais seriam $1,60 × 48 por mês.


## <a name="failover"></a>Ativação pós-falha
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Se estou a falhar com o Azure, como acedo aos VMs do Azure depois do fracasso?

Pode aceder a VMs do Azure através de uma ligação de Internet segura, através de uma VPN de site para site ou através do ExpressRoute do Azure. Tens de preparar uma série de coisas para te ligares. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se eu falhar com Azure, como é que o Azure garante que os meus dados são resistentes?
O Azure foi concebido para ser resiliente. A Recuperação do Local já está projetada para falha para um centro de dados secundário Azure, de acordo com o Azure SLA. Se isso acontecer, certificamo-nos de que os seus metadados e cofres permanecem na mesma região geográfica que escolheu para o seu cofre.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Se estou a replicar entre dois centros de dados o que acontece se o meu centro de dados primário tiver uma paragem inesperada?
Pode acionar uma ativação pós-falha não planeada do site secundário. A Recuperação de Sites não necessita de conectividade do site primário para efetuar a ativação pós-falha.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?
A ativação pós-falha não é automática. Inicia falhas com um único clique no portal ou pode utilizar o [PowerShell de Recuperação do Site](/powershell/module/az.recoveryservices) para desencadear uma falha. Falhar nas costas é uma ação simples no portal de Recuperação do Site.

Para automatizar, pode utilizar o Orquestrador ou Diretor de Operações no local para detetar uma falha na máquina virtual e, em seguida, desencadear a falha utilizando o SDK.

* [Leia mais](site-recovery-create-recovery-plans.md) sobre os planos de recuperação.
* [Leia mais](site-recovery-failover.md) sobre o fracasso.
* [Leia mais](./vmware-azure-failback.md) sobre vMware de volta e servidores físicos

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Se o meu anfitrião no local não está a responder ou a despenhar-se, posso voltar a ser um hospedeiro diferente?
Sim, pode usar a recuperação de localização alternativa para falhar com um anfitrião diferente do Azure.

* [Para máquinas virtuais VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Para máquinas virtuais Hiper-V](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

### <a name="what-is-the-difference-between-complete-migration-commit-and-disable-replication"></a>Qual é a diferença entre migração completa, cometer e desativar a replicação?

Uma vez que uma máquina da localização da fonte tenha sido falhada no local do alvo, então existem três opções disponíveis para você escolher. Todos os três servem propósitos diferentes -

1.  **A migração completa** significa que não voltará mais ao local de origem. Migraste para a região alvo e agora estás feito. Clicar em gatilhos de migração completa compromete-se e depois desativa a replicação internamente. 
2.  **Comprometer-se** significa que este não é o fim do seu processo de replicação. O item de replicação juntamente com toda a configuração permanecerá, e pode atingir **Re-protect** num momento posterior para permitir a replicação das suas máquinas de volta à região de origem. 
3.  **Desativar a replicação** irá desativar a replicação e remover toda a configuração relacionada. Não afetará a máquina já existente na região alvo.

## <a name="automation"></a>Automatização

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Posso automatizar cenários de recuperação de sítios com um SDK?
Yes. Pode automatizar fluxos de trabalho da Recuperação de Sites utilizando a API REST, PowerShell ou o SDK do Azure. Atualmente, os cenários suportados para a implementação da Recuperação do Local utilizando o PowerShell:

* [Replicar VMs hiper-V em nuvens VMMs para Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replicar VMs Hiper-V sem VMM para Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replicar VMware para Azure com PowerShell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Atualização de componentes/fornecedores

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Onde posso encontrar as notas de lançamento/atualização de atualizações de atualizações da Recuperação do Site

[Conheça novas](site-recovery-whats-new.md) atualizações e [obtenha informações sobre rollup](service-updates-how-to.md).

## <a name="next-steps"></a>Próximos passos
* Leia a [Descrição geral da Recuperação de Sites](site-recovery-overview.md)