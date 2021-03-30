---
title: VMware VM recuperação de desastres multi-inquilino com recuperação do local de Azure
description: Fornece uma visão geral do suporte de recuperação do site Azure para a recuperação de desastres VMWare para Azure em um programa de ambiente multi-inquilino (CSP).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 64e40341ec56a2e1c561b2bcbb5e584830c14015
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93145587"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Visão geral do apoio de vários inquilinos para a recuperação de desastres da VMware para Azure com CSP

[A Azure Site Recovery](site-recovery-overview.md) suporta ambientes multi-inquilinos para assinaturas de inquilinos. Também suporta vários arrendamentos para subscrições de inquilinos que são criados e geridos através do programa Microsoft Cloud Solution Provider (CSP).

Este artigo fornece uma visão geral da implementação e gestão de VMware multi-inquilino para a replicação do Azure.

## <a name="multi-tenant-environments"></a>Ambientes multi-inquilinos

Existem três grandes modelos multi-inquilinos:

* **Prestador de Serviços de Hospedagem Partilhada (HSP)**: O parceiro detém a infraestrutura física e utiliza recursos partilhados (vCenter, datacenters, armazenamento físico, e assim por diante) para hospedar vários VMs inquilinos na mesma infraestrutura. O parceiro pode fornecer a gestão de recuperação de desastres como um serviço gerido, ou o inquilino pode possuir a recuperação de desastres como uma solução de self-service.

* **Fornecedor de Serviços de Hospedagem Dedicado**: O parceiro detém a infraestrutura física, mas utiliza recursos dedicados (múltiplos vCenters, datastores físicos, e assim por diante) para hospedar os VMs de cada inquilino numa infraestrutura separada. O parceiro pode fornecer a gestão de recuperação de desastres como um serviço gerido, ou o inquilino pode possuí-lo como uma solução de self-service.

* **Prestador de Serviços Geridos (MSP)**: O cliente é dono da infraestrutura física que acolhe os VMs, e o parceiro fornece habilitação e gestão de recuperação de desastres.

## <a name="shared-hosting-services-provider-hsp"></a>Prestador de serviços de hospedagem partilhada (HSP)

Os outros dois cenários são subconjuntos do cenário de hospedagem partilhada, e usam os mesmos princípios. As diferenças são descritas no final da orientação de hospedagem partilhada.

O requisito básico num cenário multi-inquilino é que os inquilinos devem ser isolados. Um inquilino não deve ser capaz de observar o que outro inquilino acolheu. Num ambiente gerido por parceiros, esta exigência não é tão importante como num ambiente de autosserviço, onde pode ser crítica. Este artigo pressupõe que o isolamento do inquilino é necessário.

A arquitetura é mostrada no diagrama seguinte.

![HSP compartilhado com um vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Hospedagem partilhada com um servidor vCenter**

No diagrama, cada cliente tem um servidor de gestão separado. Esta configuração limita o acesso do inquilino a VMs específicos do inquilino, e permite o isolamento do inquilino. A replicação VMware VM utiliza o servidor de configuração para descobrir VMs e instalar agentes. Os mesmos princípios aplicam-se aos ambientes multi-inquilinos, com a adição de restringir a descoberta de VM utilizando o controlo de acesso vCenter.

A exigência de isolamento de dados significa que todas as informações sensíveis sobre infraestruturas (como credenciais de acesso) permanecem não divulgadas aos inquilinos. Por esta razão, recomendamos que todos os componentes do servidor de gestão permaneçam sob o controlo exclusivo do parceiro. Os componentes do servidor de gestão são:

* Servidor de configuração
* Servidor de processo
* Servidor de destino mestre

Um servidor de processo de escala separada também está sob o controlo do parceiro.

## <a name="configuration-server-accounts"></a>Contas de servidor de configuração

Cada servidor de configuração no cenário multi-inquilino utiliza duas contas:

- **conta de acesso vCenter**: Esta conta é usada para descobrir VMs inquilinos. Tem permissões de acesso vCenter atribuídas. Para evitar fugas de acesso, recomendamos que os parceiros introduzam estas credenciais na ferramenta de configuração.

- **Conta de acesso à máquina virtual**: Esta conta é utilizada para instalar o agente de serviço de Mobilidade em VMs do arrendatário, com um empurrão automático. É geralmente uma conta de domínio que um inquilino pode fornecer a um parceiro, ou uma conta que o parceiro pode gerir diretamente. Se um inquilino não quiser partilhar os detalhes diretamente com o parceiro, pode introduzir as credenciais através de acesso por tempo limitado ao servidor de configuração. Ou, com a ajuda do parceiro, podem instalar manualmente o agente de serviço de Mobilidade.

## <a name="vcenter-account-requirements"></a>vCenter requisitos de conta

Configurar o servidor de configuração com uma conta que tenha uma função especial atribuída a ele.

- A atribuição de funções deve ser aplicada na conta de acesso do vCenter para cada objeto vCenter e não propagada aos objetos da criança. Esta configuração garante o isolamento do inquilino, porque a propagação do acesso pode resultar num acesso acidental a outros objetos.

    ![A opção Propagate to Child Objects](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- A abordagem alternativa é atribuir a conta do utilizador e a função no objeto do datacenter e propo-las aos objetos da criança. Em seguida, dê à conta uma função **de não acesso** para cada objeto (como VMs que pertencem a outros inquilinos) que deve ser inacessível a um determinado inquilino. Esta configuração é complicada. Expõe controlos de acesso acidentais, porque cada objeto novo também é automaticamente concedido acesso que é herdado do progenitor. Por isso, recomendamos que utilize a primeira abordagem.

### <a name="create-a-vcenter-account"></a>Criar uma conta vCenter

1. Crie um novo papel clonando o papel predefinido read-only e, em seguida, *dar-lhe* um nome conveniente (como Azure_Site_Recovery, como mostrado neste exemplo).
2. Atribuir as seguintes permissões a esta função:

   * **Datastore**: Aloque espaço, navegue na loja de dados, operações de ficheiros de baixo nível, remover ficheiros, atualizar ficheiros de máquinas virtuais
   * **Rede**: Atribuição de rede
   * **Recurso**: Atribuir VM ao pool de recursos, migrar para fora VM, Migrar alimentado em VM
   * **Tarefas**: Criar tarefa, atualizar tarefa
   * **VM - Configuração**: Todos
   * **VM - Interação** > Resposta Pergunta, Ligação do dispositivo, cd de configuração, meios de dissiculação, desligamento, ligação elétrica, instalação de ferramentas VMware
   * **VM - Inventário** > Criar a partir de existentes, Criar novo, Registar, Não Registar
   * **VM - Provisioning** > Permitir o descarregamento de máquinas virtuais, permitir o upload de ficheiros de máquinas virtuais
   * **VM - Gestão instantânea** > Remover instantâneos

       ![A caixa de diálogo editar role](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Atribua os níveis de acesso à conta vCenter (utilizada no servidor de configuração do inquilino) para vários objetos, da seguinte forma:

>| Objeto | Função | Observações |
>| --- | --- | --- |
>| vCenter | Read-Only | Necessário apenas para permitir o acesso do vCenter para a gestão de diferentes objetos. Pode remover esta permissão se a conta nunca for fornecida a um inquilino ou utilizado para quaisquer operações de gestão no vCenter. |
>| Datacenter | Azure_Site_Recovery |  |
>| Agrupamento de anfitriões e anfitriões | Azure_Site_Recovery | Re-garante que o acesso está ao nível do objeto, de modo que apenas os anfitriões acessíveis têm VMs inquilinos antes de falhar e depois do failback. |
>| Datastore e cluster de datastore | Azure_Site_Recovery | O mesmo que antes. |
>| Rede | Azure_Site_Recovery |  |
>| Servidor de Gestão | Azure_Site_Recovery | Inclui acesso a todos os componentes (CS, PS e MT) fora da máquina CS. |
>| VMs inquilinos | Azure_Site_Recovery | Garante que quaisquer novos VMs de um inquilino em particular também obtenham este acesso, ou não serão detetáveis através do portal Azure. |

O acesso à conta vCenter está agora completo. Este passo cumpre o requisito mínimo de permissões para completar as operações de failback. Também pode utilizar estas permissões de acesso com as suas políticas existentes. Basta modificar as permissões existentes definidas para incluir permissões de função a partir do passo 2, detalhadas anteriormente.

### <a name="failover-only"></a>Falha apenas
Para restringir as operações de recuperação de desastres até apenas o failover (isto é, sem capacidades de recuo), utilize o procedimento anterior, com estas exceções:

- Em vez de atribuir a *função Azure_Site_Recovery* à conta de acesso do vCenter, atribua apenas uma função *Read-Only* a essa conta. Este conjunto de permissões permite a replicação e falha de VM, e não permite o retrocesso.
- Tudo o resto no processo anterior permanece como está. Para garantir o isolamento do inquilino e restringir a descoberta de VM, todas as permissões são atribuídas apenas ao nível do objeto, e não propagadas a objetos infantis.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Mobilizar recursos para a subscrição do inquilino

1. No portal Azure, crie um grupo de recursos e, em seguida, implante um cofre dos Serviços de Recuperação de acordo com o processo habitual.
2. Transfira a chave de registo do cofre.
3. Registe o CS para o inquilino utilizando a chave de registo do cofre.
4. Introduza as credenciais para as duas contas de acesso, a conta para aceder ao servidor vCenter e a conta para aceder ao VM.

    ![Contas de servidor de configuração do gestor](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Registar servidores no cofre

1. No portal Azure, no cofre que criou anteriormente, registe o servidor vCenter no servidor de configuração, utilizando a conta vCenter que criou.
2. Termine o processo de "Preparar a infraestrutura" para recuperação do local de acordo com o processo habitual.
3. Os VMs estão agora prontos para serem replicados. Verifique se apenas os VM do arrendatário são apresentados em  >  **máquinas virtuais** Replicate Select .

## <a name="dedicated-hosting-solution"></a>Solução de hospedagem dedicada

Como mostrado no diagrama seguinte, a diferença arquitetónica numa solução de hospedagem dedicada é que a infraestrutura de cada inquilino é criada apenas para esse inquilino.

![Diagrama que mostra a diferença arquitetónica numa solução de hospedagem dedicada é que a infraestrutura de cada inquilino é criada apenas para esse inquilino.](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Cenário de hospedagem dedicado com vários vCenters**

## <a name="managed-service-solution"></a>Solução de serviço gerida

Como mostrado no diagrama seguinte, a diferença arquitetónica numa solução de serviço gerida é que a infraestrutura de cada inquilino também é fisicamente separada da infraestrutura de outros inquilinos. Este cenário normalmente existe quando o arrendatário é dono da infraestrutura e quer um fornecedor de soluções para gerir a recuperação de desastres.

![arquitetura-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Cenário de serviço gerido com vários vCenters**

## <a name="next-steps"></a>Passos seguintes
- [Saiba mais](site-recovery-role-based-linked-access-control.md) sobre o controlo de acesso baseado em funções na Recuperação do Local.
- Saiba como configurar a [recuperação de VMware VMs para Azure](vmware-azure-tutorial.md).
- Saiba mais sobre [o multi-arrendamento com CSP para VMWare VMs](vmware-azure-multi-tenant-csp-disaster-recovery.md).
