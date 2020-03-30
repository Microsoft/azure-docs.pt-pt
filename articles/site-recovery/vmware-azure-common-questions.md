---
title: Perguntas comuns sobre recuperação de desastres da VMware com recuperação do site Azure
description: Obtenha respostas a perguntas comuns sobre a recuperação de desastres de VMware VMs no local para Azure usando a Recuperação do Site Azure.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: ae16138ae44262f53a8f9948d6287f0acf621244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240028"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Perguntas comuns sobre a replicação do VMware para o Azure

Este artigo responde a perguntas comuns que podem surgir quando você implementa a recuperação de desastres de máquinas virtuais VMware no local (VMs) para O Azure.

## <a name="general"></a>Geral

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>O que preciso para a recuperação de desastres VMware VM?

[Conheça os componentes envolvidos](vmware-azure-architecture.md) na recuperação de desastres de VMware VMs.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Posso usar a Recuperação do Site para migrar VMware VMs para Azure?

Sim. Além de utilizar a Recuperação do Site para configurar a recuperação completa de desastres para VMware VMs, também pode usar a Recuperação do Site para migrar vMs no local para Azure. Neste cenário, você replica VMs no local para Armazenamento Azure. Depois, falhas das instalações para o Azure. Após o failover, as suas aplicações e cargas de trabalho estão disponíveis e em execução em VMs Azure. O processo é como preparar a recuperação total de desastres, exceto que numa migração não se pode falhar de Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>A minha conta Azure precisa de permissões para criar VMs?

Se é administrador de subscrição, tem as permissões de replicação de que necessita. Se não é administrador, precisa de permissões para tomar estas ações:

- Crie um VM Azure no grupo de recursos e rede virtual que especifique quando configura r'se de Recuperação do Site.
- Escreva para a conta de armazenamento selecionada ou para o disco gerido com base na sua configuração.

[Saiba mais](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) sobre as permissões necessárias.

### <a name="what-applications-can-i-replicate"></a>Que aplicações posso replicar?

Pode replicar qualquer aplicação ou carga de trabalho em execução num VMware VMque que satisfaça os requisitos de [replicação](vmware-physical-azure-support-matrix.md#replicated-machines).

- A Recuperação do Site suporta a replicação consciente da aplicação, para que as aplicações possam ser falhadas e falhadas de volta a um estado inteligente.
- A Recovery do Site integra-se com aplicações da Microsoft como SharePoint, Exchange, Dynamics, SQL Server e Ative Directory. Também trabalha em estreita colaboração com os principais fornecedores, incluindo oracle, SAP, IBM e Red Hat.

[Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Posso usar uma licença de servidor de osso convidado no Azure?

Sim, os clientes da Microsoft Software Assurance podem usar o [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) para economizar nos custos de licenciamento das máquinas Windows Server que são migradas para o Azure, ou para usar o Azure para a recuperação de desastres.

## <a name="security"></a>Segurança

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Que acesso aos servidores VMware necessita a Recuperação do Site?

O Site Recovery precisa de ter acesso a servidores VMware para:

- Configurar um VMware VM executando o servidor de configuração de recuperação do site.
- Descubra automaticamente os VMs para replicação.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Que acesso aos VMware VMs precisa de recuperação do site?

- Para replicar, um VMware VM deve ter o serviço de Mobilidade de Recuperação do Site instalado e em funcionamento. Pode implantar a ferramenta manualmente ou pode especificar que a Recuperação do Site faz uma instalação de pressão do serviço quando ativa a replicação de um VM.
- Durante a replicação, os VMs comunicam com a Recuperação do Local da seguinte forma:
    - Os VMs comunicam com o servidor de configuração na porta HTTPS 443 para gestão de replicação.
    - Os VMs enviam dados de replicação para o servidor de processos na porta HTTPS 9443. (Esta definição pode ser modificada.)
    - Se permitir a consistência multi-VM, os VMs comunicam entre si através da porta 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Os dados de replicação são enviados para a Recuperação do Site?

Não, a Recuperação do Site não interceta dados replicados e não tem nenhuma informação sobre o que está a ser feito nos seus VMs. Os dados de replicação são trocados entre hipervisores VMware e Armazenamento Azure. A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.

A Recuperação do Site está certificada para ISO 27001:2013 e 27018, HIPAA e DPA. Está em processo de avaliações soc2 e FedRAMP JAB.

## <a name="pricing"></a>Preços

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Como calculo as despesas aproximadas para a recuperação de desastres vMware?

Utilize a [calculadora](https://aka.ms/asr_pricing_calculator) de preços para estimar os custos enquanto utiliza a Recuperação do Site.

Para uma estimativa detalhada dos custos, execute a ferramenta de planificador de implementação para [VMware](https://aka.ms/siterecovery_deployment_planner) e utilize o relatório de estimativa de [custos](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Existe alguma diferença de custo entre replicar para armazenamento ou diretamente para discos geridos?

Os discos geridos são carregados de forma ligeiramente diferente das contas de armazenamento. [Saiba mais](https://azure.microsoft.com/pricing/details/managed-disks/) sobre os preços do disco gerido.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Existe alguma diferença de custo sintetizada na reparação da conta de armazenamento V2 general Purpose?

Normalmente, verá um aumento do custo das transações incorridos nas contas de armazenamento GPv2, uma vez que a Recuperação do Site Azure é pesada. [Leia mais](../storage/common/storage-account-upgrade.md#pricing-and-billing) para estimar a mudança.

## <a name="mobility-service"></a>Serviço de Mobilidade

### <a name="where-can-i-find-the-mobility-service-installers"></a>Onde posso encontrar os instaladores do serviço mobility?

Os instaladores estão na pasta %ProgramData%\ASR\home\svsystems\pushinstallsvc\repositório no servidor de configuração.

## <a name="how-do-i-install-the-mobility-service"></a>Como posso instalar o serviço de Mobilidade?

Em cada VM que pretende replicar, instale o serviço por um dos vários métodos:

- [Instalação de empurrar](vmware-physical-mobility-service-overview.md#push-installation)
- [Instalação manual](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) da UI ou PowerShell
- Implementação utilizando uma ferramenta de implementação como o Gestor de [Configuração](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Managed disks

### <a name="where-does-site-recovery-replicate-data-to"></a>Para onde é que a Recuperação do Site replica dados?

A Recuperação do Site replica VMs e servidores físicos geridos no Azure.

- O servidor do processo de recuperação do site escreve registos de replicação para uma conta de armazenamento de cache na região alvo.
- Estes registos são utilizados para criar pontos de recuperação em discos geridos pelo Azure que têm prefixo de **disco asrseedd .**
- Quando ocorre a falha, o ponto de recuperação que selecionar é utilizado para criar um novo disco gerido pelo alvo. Este disco gerido está ligado ao VM em Azure.
- Os VMs que foram anteriormente replicados numa conta de armazenamento (antes de março de 2019) não são afetados.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Posso replicar novas máquinas para armazenar contas?

Não. A partir de março de 2019, no portal Azure, só é possível replicar discos geridos pelo Azure.

A replicação de novos VMs numa conta de armazenamento só está disponível utilizando o PowerShell ou o REST API (versão 2018-01-10 ou 2016-08-10).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Quais são os benefícios de replicar discos geridos?

[Saiba como](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) A Recuperação do Site simplifica a recuperação de desastres com discos geridos.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Posso mudar o tipo de disco gerido depois de uma máquina estar protegida?

Sim, pode [facilmente alterar o tipo de disco gerido](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) para as replicações em curso. Antes de alterar o tipo, certifique-se de que não é gerado nenhum URL de assinatura de acesso partilhado no disco gerido:

1. Vá ao recurso **Do Disco Gerido** no portal Azure e verifique se tem um banner URL de assinatura de acesso partilhado na lâmina de visão **geral.**
1. Se o banner estiver presente, selecione-o para cancelar a exportação em curso.
1. Mude o tipo do disco nos próximos minutos. Se alterar o tipo de disco gerido, aguarde que novos pontos de recuperação sejam gerados pela Recuperação do Site Azure.
1. Utilize os novos pontos de recuperação para qualquer falha de teste ou falha no futuro.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Posso mudar a replicação de discos geridos para discos não geridos?

Não. A mudança de gerida para não gerida não é suportada.

## <a name="replication"></a>Replicação

### <a name="what-are-the-replicated-vm-requirements"></a>Quais são os requisitos de VM replicados?

[Saiba mais](vmware-physical-azure-support-matrix.md#replicated-machines) sobre os requisitos de suporte para VMware VMs e servidores físicos.

### <a name="how-often-can-i-replicate-to-azure"></a>Quantas vezes posso replicar o Azure?

A replicação é contínua ao replicar VMware VMs para Azure.

### <a name="can-i-extend-replication"></a>Posso estender a replicação?

Não é suportada a replicação expandida ou em cadeia. Solicite esta funcionalidade no fórum de [feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Posso fazer uma replicação inicial offline?

A replicação offline não é suportada. Solicite esta funcionalidade no fórum de [feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>O que é asrseeddisk?

Para cada disco de origem, os dados são replicados para um disco gerido em Azure. Este disco tem o prefixo do **disco asrseedd**. Armazena a cópia do disco de origem e todos os instantâneos do ponto de recuperação.

### <a name="can-i-exclude-disks-from-replication"></a>Posso excluir discos da replicação?

Sim, pode excluir discos.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Posso replicar VMs que têm discos dinâmicos?

Discos dinâmicos podem ser replicados. O disco do sistema operativo deve ser um disco básico.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Se eu usar grupos de replicação para a consistência multi-VM, posso adicionar um novo VM a um grupo de replicação existente?

Sim, pode adicionar novos VMs a um grupo de replicação existente quando permite a replicação para eles. Todavia:

- Não se pode adicionar um VM a um grupo de replicação existente depois de a replicação ter começado.
- Não se pode criar um grupo de replicação para os VMs existentes.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Posso modificar vMs que estão replicando adicionando ou redimensionando discos?

Para a replicação de VMware para o Azure, pode modificar o tamanho do disco de VMs de origem. Se quiser adicionar discos novos, tem de adicionar o disco e reativar a proteção para o VM.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Posso migrar máquinas no local para um novo servidor vCenter sem afetar a replicação em curso?

Não. Uma mudança de VMware Vcenter ou migração terá impacto na replicação em curso. Configurar a Recuperação do Site com o novo servidor vCenter e permitir a replicação para as máquinas novamente.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Posso replicar-me para uma conta de cache ou armazenamento de alvos que tenha uma rede virtual (com Firewalls Azure) configurada nele?

Não, a Recuperação do Site não suporta a replicação ao Armazenamento Azure em redes virtuais.

## <a name="component-upgrade"></a>Atualização de componentes

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>A minha versão do agente de serviços de Mobilidade ou servidor de configuração é antiga, e a minha atualização falhou. O que posso fazer?

A Recuperação do Site segue o modelo de suporte N-4. [Saiba mais](https://aka.ms/asr_support_statement) sobre como fazer upgrade a partir de versões muito antigas.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Onde posso encontrar as notas de lançamento e atualizações para a Recuperação do Site Azure?

[Conheça novas atualizações](site-recovery-whats-new.md)e obtenha informações de [rollup.](service-updates-how-to.md)

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Onde posso encontrar informações de atualização para a recuperação de desastres para o Azure?

[Saiba mais sobre a atualização.](https://aka.ms/asr_vmware_upgrades)

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Preciso reiniciar as máquinas de origem para cada atualização?

Recomenda-se um reboot, mas não obrigatório para cada atualização. [Saiba mais](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Servidor de configuração

### <a name="what-does-the-configuration-server-do"></a>O que faz o servidor de configuração?

O servidor de configuração executa os componentes de recuperação do site no local, incluindo:

- O servidor de configuração em si. O servidor coordena as comunicações entre os componentes no local e o Azure e gere a replicação de dados.
- O servidor de processos, que funciona como uma porta de entrada de replicação. Este servidor:
    1. Recebe os dados de replicação.
    2. Otimiza os dados com cache, compressão e encriptação.
    3. Envia os dados para o Armazenamento Azure.
  O servidor de processo também faz uma instalação push do Serviço de Mobilidade em VMs e realiza a descoberta automática de VMware VMware no local.
- O servidor alvo principal, que lida com os dados de replicação durante o failback do Azure.

[Saiba mais](vmware-azure-architecture.md) sobre os componentes e processos do servidor de configuração.

### <a name="where-do-i-set-up-the-configuration-server"></a>Onde configurarei o servidor de configuração?

Precisa de um VMM vM no local único, altamente disponível, para o servidor de configuração. Para a recuperação de desastres do servidor físico, instale o servidor de configuração numa máquina física.

### <a name="what-do-i-need-for-the-configuration-server"></a>O que preciso para o servidor de configuração?

Reveja os [pré-requisitos.](vmware-azure-deploy-configuration-server.md#prerequisites)

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Posso configurar manualmente o servidor de configuração em vez de usar um modelo?

Recomendamos que [crie o VM do servidor](vmware-azure-deploy-configuration-server.md) de configuração utilizando a versão mais recente do modelo de Formato de Virtualização Aberta (OVF). Se não puder utilizar o modelo (por exemplo, se não tiver acesso ao servidor VMware), [descarregue](physical-azure-set-up-source.md) o ficheiro de configuração a partir do portal e instale o servidor de configuração.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Um servidor de configuração pode replicar-se para mais de uma região?

Não. Para se replicar em mais de uma região, precisa de um servidor de configuração em cada região.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Posso hospedar um servidor de configuração em Azure?

Embora seja possível, o Azure VM que executa o servidor de configuração teria de comunicar com a sua infraestrutura VMware e VMs no local. Esta comunicação adiciona latência e impacta a replicação em curso.

### <a name="how-do-i-update-the-configuration-server"></a>Como atualizo o servidor de configuração?

[Saiba](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) como atualizar o servidor de configuração.

- Pode encontrar as últimas informações sobre a atualização na [página de atualizações do Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- Pode descarregar a versão mais recente a partir do portal. Ou pode descarregar a versão mais recente do servidor de configuração diretamente do [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Se a sua versão for mais de quatro versões mais antigas do que a versão atual, consulte a declaração de [suporte](https://aka.ms/asr_support_statement) para orientação de atualização.

### <a name="should-i-back-up-the-configuration-server"></a>Devo voltar a apoiar o servidor de configuração?

Recomendamos a obtenção de cópias de segurança regulares do servidor de configuração.

- Para um retrocesso bem sucedido, o VM que está a ser falhado deve existir na base de dados do servidor de configuração.
- O servidor de configuração deve estar em execução e em estado de ligação.
- [Saiba mais](vmware-azure-manage-configuration-server.md) sobre tarefas comuns de gestão do servidor de configuração.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Quando estiver a configurar o servidor de configuração, posso descarregar e instalar o MySQL manualmente?

Sim. Baixe mySQL e coloque-o na pasta C:\Temp\ASRSetup. Em seguida, instale-o manualmente. Quando configurar o VM do servidor de configuração e aceitar os termos, o MySQL será listado como **já instalado** no Download **e instalar**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Posso evitar descarregar mySQL mas deixar o Site Recovery instalá-lo?

Sim. Descarregue o instalador MySQL e coloque-o na pasta C:\Temp\ASRSetup. Quando configurar o VM do servidor de configuração, aceite os termos e selecione **Descarregar e instalar**. O portal utilizará o instalador que adicionou para instalar o MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Posso usar o VM do servidor de configuração para mais alguma coisa?

Não. Utilize o VM apenas para o servidor de configuração.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Posso clonar um servidor de configuração e usá-lo para orquestração?

Não. Instale um novo servidor de configuração para evitar problemas de registo.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Posso mudar o cofre no qual o servidor de configuração está registado?

Não. Depois de um cofre estar associado ao servidor de configuração, não pode ser alterado. [Aprenda](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) a registar um servidor de configuração com um cofre diferente.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Posso usar o mesmo servidor de configuração para recuperação de desastres tanto de VMware VMs como de servidores físicos?

Sim, mas note que a máquina física só pode ser falhada apenas para um VMware VM.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Onde posso descarregar a frase de passe para o servidor de configuração?

[Saiba](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) como descarregar a frase-passe.

### <a name="where-can-i-download-vault-registration-keys"></a>Onde posso baixar as chaves de registo do cofre?

No cofre dos Serviços de Recuperação, selecione **Servidores** de Configuração na**Gestão**da Infraestrutura > de **Recuperação**do Local . Em seguida, em **Servers**, selecione a **chave de registo de descarregamento** para descarregar o ficheiro de credenciais de cofre.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Pode um único servidor de configuração ser usado para proteger várias instâncias vCenter?

Sim, um único servidor de configuração pode proteger VMs em vários vCenters.  Não há limite suplicou quantas instâncias de vCenter podem ser adicionadas ao servidor de configuração, no entanto os limites para quantos VMs um único servidor de configuração pode proteger se aplicam.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Um único servidor de configuração pode proteger vários clusters dentro do vCenter?

Sim, a Recuperação do Sítio Azure pode proteger os VMs em diferentes clusters.

## <a name="process-server"></a>Servidor de processos

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Por que não consigo selecionar o servidor de processo sou incapaz de selecionar o servidor de processos quando ativo a replicação?

Atualizações nas versões 9.24 e mais tarde exibem agora a [saúde do servidor de processo quando ativa a replicação](vmware-azure-enable-replication.md#enable-replication). Esta funcionalidade ajuda a evitar o estrangulamento do servidor de processos e a minimizar o uso de servidores de processos pouco saudáveis.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Como atualizo o servidor de processos para a versão 9.24 ou mais tarde para obter informações precisas sobre saúde?

Começando pela [versão 9.24,](service-updates-how-to.md#links-to-currently-supported-update-rollups)foram adicionados mais alertas para indicar a saúde do servidor do processo. [Atualize os componentes da Recuperação do Site para a versão 9.24 ou posterior para](service-updates-how-to.md#links-to-currently-supported-update-rollups) que todos os alertas sejam gerados.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Como posso garantir uma alta disponibilidade do servidor de processos?

Ao configurar mais de um servidor de processo, o design proporciona flexibilidade para mover máquinas protegidas de um servidor de processo pouco saudável para o servidor de processos de trabalho. O movimento de uma máquina de um servidor de processo para outro deve ser iniciado explicitamente/manualmente através dos passos definidos aqui: [mover VMs entre servidores](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)de processos .

## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Posso usar o servidor de processo no local para o failback?

Recomendamos vivamente a criação de um servidor de processo em Azure para efeitos de falha, para evitar a transferência de dados de atrasos. Além disso, caso separe a rede de VMs de origem com a rede de visualização Azure no servidor de configuração, é essencial utilizar o servidor de processo criado em Azure para o failback.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Posso manter o endereço IP no failover?

Sim, pode manter o endereço IP no failover. Certifique-se de que especifica o endereço IP do alvo nas definições **de Compute e Rede** para o VM antes da falha. Além disso, desligue as máquinas no momento do failover para evitar conflitos de endereços IP durante o failback.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Posso alterar o tamanho do alvo vM ou o tipo VM antes de falhar?

Sim, pode alterar o tipo ou o tamanho do VM a qualquer momento antes de falhar. No portal, utilize as definições **de Compute e Rede** para o VM replicado.

### <a name="how-far-back-can-i-recover"></a>Até onde posso me recuperar?

Para vMware para Azure, o ponto de recuperação mais antigo que pode utilizar é de 72 horas.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Como posso aceder aos VMs Azure depois do fracasso?

Após o failover, você pode aceder a VMs Azure através de uma conexão de internet segura, através de um site-to-site VPN, ou sobre Azure ExpressRoute. Para se conectar, deve preparar várias coisas. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Os dados falhados são resistentes?

O Azure foi concebido para ser resiliente. A Recuperação do Site é projetada para falha num centro de dados secundário do Azure, conforme exigido pelo acordo de nível de serviço Azure (SLA). Quando ocorre uma falha, certificamo-nos de que os seus metadados e cofres permanecem na mesma região geográfica que escolheu para o seu cofre.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?

[Falhar](site-recovery-failover.md) não é automático. Inicia uma falha fazendo uma única seleção no portal, ou pode usar o [PowerShell](/powershell/module/az.recoveryservices) para desencadear uma falha.

### <a name="can-i-fail-back-to-a-different-location"></a>Posso voltar a um local diferente?

Sim. Se falhar com o Azure, pode falhar num local diferente se o original não estiver disponível. [Saiba mais](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Por que preciso de uma VPN ou ExpressRoute com o olhar privado para falhar?

Quando recua do Azure, os dados do Azure são copiados de volta para o seu VM no local, e é necessário acesso privado.


## <a name="automation-and-scripting"></a>Automação e scripting

### <a name="can-i-set-up-replication-with-scripting"></a>Posso configurar a replicação com scripts?

Sim. Pode automatizar fluxos de trabalho de recuperação do site utilizando o Rest API, PowerShell ou o Azure SDK. [Saiba mais](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Desempenho e capacidade

### <a name="can-i-throttle-replication-bandwidth"></a>Posso acelerar a largura de banda de replicação?

Sim. [Saiba mais](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Passos seguintes

- [Rever](vmware-physical-azure-support-matrix.md) os requisitos de apoio.
- [Configurar](vmware-azure-tutorial.md) VMware para a replicação de Azure.
