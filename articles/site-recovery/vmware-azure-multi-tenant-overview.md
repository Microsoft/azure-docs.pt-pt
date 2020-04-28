---
title: VMware VM recuperação de desastre multi-inquilino com recuperação do site Azure
description: Fornece uma visão geral do suporte de recuperação do site Azure para a recuperação de desastres VMWare para o Azure num programa de ambiente multi-inquilino (CSP).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 840049265d3b6e4d2fddd794646bfd5691aab9a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74083995"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Visão geral do apoio multi-inquilino para recuperação de desastres vMware para Azure com CSP

[A Azure Site Recovery](site-recovery-overview.md) suporta ambientes multi-inquilinos para subscrições de inquilinos. Também suporta o arrendamento multi-arrendamento para subscrições de inquilinos que são criados e geridos através do programa Microsoft Cloud Solution Provider (CSP).

Este artigo fornece uma visão geral da implementação e gestão da VMware multi-arrendatária à replicação do Azure.

## <a name="multi-tenant-environments"></a>Ambientes multi-inquilinos

Existem três grandes modelos multi-inquilinos:

* Fornecedor de Serviços de **Hospedagem Partilhada (HSP)**: O parceiro é dono da infraestrutura física, e utiliza recursos partilhados (vCenter, datacenters, armazenamento físico, e assim por diante) para acolher vários VMs de inquilinos na mesma infraestrutura. O parceiro pode fornecer a gestão de recuperação de desastres como um serviço gerido, ou o inquilino pode possuir a recuperação de desastres como uma solução de self-service.

* Provedor dedicado de Serviços de **Hospedagem**: O parceiro é dono da infraestrutura física, mas utiliza recursos dedicados (múltiplos vCenters, datastores físicas, e assim por diante) para acolher os VMs de cada inquilino numa infraestrutura separada. O parceiro pode fornecer a gestão de recuperação de desastres como um serviço gerido, ou o inquilino pode possuí-lo como uma solução de self-service.

* **Prestador de serviços geridos (MSP)**: O cliente é dono da infraestrutura física que acolhe os VMs, e o parceiro fornece habilitação e gestão de recuperação de desastres.

## <a name="shared-hosting-services-provider-hsp"></a>Prestador de serviços de hospedagem partilhada (HSP)

Os outros dois cenários são subconjuntos do cenário de hospedagem partilhada, e usam os mesmos princípios. As diferenças são descritas no final da orientação de hospedagem partilhada.

A exigência básica num cenário de multi-inquilinos é que os inquilinos devem ser isolados. Um inquilino não deve ser capaz de observar o que outro inquilino acolheu. Num ambiente gerido por parceiros, esta exigência não é tão importante como num ambiente de autosserviço, onde pode ser crítica. Este artigo pressupõe que o isolamento dos inquilinos é necessário.

A arquitetura é mostrada no diagrama seguinte.

![HSP compartilhado com um vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Hospedagem partilhada com um servidor vCenter**

No diagrama, cada cliente tem um servidor de gestão separado. Esta configuração limita o acesso dos inquilinos a VMs específicos do arrendatário, e permite o isolamento dos inquilinos. A replicação VMware VM utiliza o servidor de configuração para descobrir VMs e instalar agentes. Os mesmos princípios aplicam-se aos ambientes multi-inquilinos, com a adição de restringir a descoberta de VM utilizando o controlo de acesso vCenter.

O requisito de isolamento de dados significa que todas as informações sensíveis sobre infraestruturas (como credenciais de acesso) permanecem não reveladas aos inquilinos. Por esta razão, recomendamos que todos os componentes do servidor de gestão permaneçam sob o controlo exclusivo do parceiro. Os componentes do servidor de gestão são:

* Servidor de configuração
* Servidor de processos
* Servidor de destino mestre

Um servidor de processo separado também está sob o controlo do parceiro.

## <a name="configuration-server-accounts"></a>Contas de servidor de configuração

Todos os servidores de configuração no cenário multi-inquilino usam duas contas:

- **conta de acesso vCenter**: Esta conta é usada para descobrir VMs de inquilinos. Tem permissões de acesso vCenter atribuídas a ele. Para ajudar a evitar fugas de acesso, recomendamos que os parceiros introduzam estas credenciais na ferramenta de configuração.

- **Conta**de acesso à máquina virtual : Esta conta é utilizada para instalar o agente de serviço de Mobilidade em VMs de inquilinos, com um empurrão automático. Normalmente é uma conta de domínio que um inquilino pode fornecer a um parceiro, ou uma conta que o parceiro pode gerir diretamente. Se um inquilino não quiser partilhar os detalhes diretamente com o parceiro, pode introduzir as credenciais através de acesso limitado ao servidor de configuração. Ou, com a ajuda do parceiro, podem instalar manualmente o agente de serviço de Mobilidade.

## <a name="vcenter-account-requirements"></a>requisitos de conta vCenter

Configure o servidor de configuração com uma conta que tenha uma função especial atribuída ao mesmo.

- A atribuição de funções deve ser aplicada na conta de acesso vCenter para cada objeto vCenter, e não propagada aos objetos infantis. Esta configuração garante o isolamento dos inquilinos, porque a propagação do acesso pode resultar no acesso acidental a outros objetos.

    ![A opção Propagate to Child Objects](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- A abordagem alternativa é atribuir a conta do utilizador e o papel no objeto do datacenter e propagá-los aos objetos infantis. Em seguida, dê à conta uma função **de não acesso** para cada objeto (como VMs que pertencem a outros inquilinos) que devem ser inacessíveis a um inquilino em particular. Esta configuração é complicada. Expõe controlos de acesso acidentais, porque cada novo objeto infantil também é automaticamente concedido acesso que é herdado do progenitor. Por isso, recomendamos que utilize a primeira abordagem.

### <a name="create-a-vcenter-account"></a>Criar uma conta vCenter

1. Crie um novo papel clonando o papel predefinido de Leitura e, em seguida, *dê-lhe* um nome conveniente (como Azure_Site_Recovery, como mostra este exemplo).
2. Atribuir as seguintes permissões a este papel:

   * **Datastore**: Alocar espaço, navegar na loja de dados, operações de ficheirode baixo nível, Remover ficheiros, atualizar ficheiros de máquinas virtuais
   * **Rede**: Atribuição de rede
   * **Recurso**: Atribuir VM ao conjunto de recursos, migrar alimentado fora de VM, Migrar alimentado em VM
   * **Tarefas**: Criar tarefas, atualizar tarefas
   * **VM - Configuração:** Todos
   * **VM - Interação** > Resposta, Ligação do dispositivo, Mídia de CD configuração, meios de configuração, desligar, desligar, ligar, instalação de ferramentas VMware
   * **VM - Inventário** > Criar a partir de existente, Criar novo, Registar, Desregistar
   * **VM - Fornecimento** > Permitir o download de máquinas virtuais, permitir o upload de ficheiros de máquinas virtuais
   * **VM - Gestão instantânea** > Remover instantâneos

       ![A caixa de diálogo editar role](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Atribuir níveis de acesso à conta vCenter (utilizada no servidor de configuração do inquilino) para vários objetos, da seguinte forma:

>| Objeto | Função | Observações |
>| --- | --- | --- |
>| vCenter | Leitura-Apenas | Necessário apenas para permitir o acesso vCenter para a gestão de diferentes objetos. Pode remover esta permissão se a conta nunca for fornecida a um inquilino ou utilizada para quaisquer operações de gestão no vCenter. |
>| Datacenter | Azure_Site_Recovery |  |
>| Aglomerado de hospedeiros e hospedeiros | Azure_Site_Recovery | Re-garante que o acesso está ao nível do objeto, de modo que apenas os anfitriões acessíveis têm VMs de inquilinoantes de failover e depois do failback. |
>| Cluster datastore e datastore | Azure_Site_Recovery | O mesmo que antes. |
>| Rede | Azure_Site_Recovery |  |
>| Servidor de Gestão | Azure_Site_Recovery | Inclui acesso a todos os componentes (CS, PS e MT) fora da máquina CS. |
>| VMs de inquilino | Azure_Site_Recovery | Garante que qualquer novo inquilino VMs de um inquilino em particular também obtém este acesso, ou eles não serão detetáveis através do portal Azure. |

O acesso à conta vCenter está agora completo. Este passo cumpre o requisito mínimo de permissões para completar as operações de reprovação. Também pode utilizar estas permissões de acesso com as suas políticas existentes. Basta modificar as suas permissões existentes definidas para incluir permissões de funções do passo 2, detalhadas anteriormente.

### <a name="failover-only"></a>Falha apenas
Para restringir as operações de recuperação de desastres até apenas falhar (isto é, sem capacidades de retrocesso), utilize o procedimento anterior, com estas exceções:

- Em vez de atribuir o papel *Azure_Site_Recovery* à conta de acesso vCenter, atribuir apenas uma função *de Leitura-Apenas* a essa conta. Este conjunto de permissões permite a replicação e falha de VM, e não permite o recuo.
- Tudo o resto no processo anterior permanece como está. Para garantir o isolamento dos inquilinos e restringir a descoberta de VM, todas as permissões ainda são atribuídas apenas ao nível do objeto, e não propagadas a objetos infantis.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Implementar recursos para a subscrição do arrendatário

1. No portal Azure, crie um grupo de recursos e, em seguida, implemente um cofre de Serviços de Recuperação por processo habitual.
2. Transfira a chave de registo do cofre.
3. Registe o CS para o inquilino utilizando a chave de registo do cofre.
4. Insira as credenciais para as duas contas de acesso, a conta para aceder ao servidor vCenter e a conta para aceder ao VM.

    ![Gestor de contas de servidor de configuração](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Registar servidores no cofre

1. No portal Azure, no cofre que criou anteriormente, registe o servidor vCenter no servidor de configuração, utilizando a conta vCenter que criou.
2. Termine o processo "Preparar a infraestrutura" para a recuperação do local de acordo com o processo habitual.
3. Os VMs estão agora prontos para serem replicados. Verifique se apenas os VMs do inquilino são apresentados em**máquinas virtuais** **Replicate** > Select .

## <a name="dedicated-hosting-solution"></a>Solução de hospedagem dedicada

Como mostra o seguinte diagrama, a diferença arquitetónica numa solução de hospedagem dedicada é que a infraestrutura de cada inquilino está criada apenas para esse inquilino.

![arquitetura-compartilhado-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Cenário de hospedagem dedicado com vários vCenters**

## <a name="managed-service-solution"></a>Solução de serviço gerida

Como mostra o seguinte diagrama, a diferença arquitetónica numa solução de serviço gerida é que a infraestrutura de cada inquilino também está fisicamente separada da infraestrutura de outros inquilinos. Este cenário geralmente existe quando o inquilino é dono da infraestrutura e quer um fornecedor de solução para gerir a recuperação de desastres.

![arquitetura-compartilhado-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Cenário de serviço gerido com vários vCenters**

## <a name="next-steps"></a>Passos seguintes
- [Saiba mais](site-recovery-role-based-linked-access-control.md) sobre o controlo de acesso baseado em papéis na Recuperação do Site.
- Saiba como configurar a [recuperação de VMware VMs para o Azure](vmware-azure-tutorial.md).
- Saiba mais sobre [o multi-arrendamento com CSP para VMWare VMs](vmware-azure-multi-tenant-csp-disaster-recovery.md).
