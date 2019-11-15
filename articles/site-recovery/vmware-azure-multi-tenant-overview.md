---
title: Recuperação de desastre multilocatário de VM VMware com Azure Site Recovery
description: Fornece uma visão geral do suporte Azure Site Recovery para a recuperação de desastres do VMWare no Azure em um programa de CSP (ambiente multilocatário).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 840049265d3b6e4d2fddd794646bfd5691aab9a1
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083995"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Visão geral do suporte a vários locatários para a recuperação de desastres do VMware para o Azure com o CSP

O [Azure site Recovery](site-recovery-overview.md) dá suporte a ambientes de multilocatário para assinaturas de locatário. Ele também dá suporte a multilocação para assinaturas de locatário que são criadas e gerenciadas por meio do programa CSP (provedor de solução Microsoft Cloud).

Este artigo fornece uma visão geral de como implementar e gerenciar a replicação do VMware para o Azure de vários locatários.

## <a name="multi-tenant-environments"></a>Ambientes multilocatários

Há três modelos principais de vários locatários:

* **Provedor de serviços de hospedagem compartilhada (HSP)** : o parceiro possui a infraestrutura física e usa recursos compartilhados (vCenter, datacenters, armazenamento físico e assim por diante) para hospedar várias VMs de locatário na mesma infraestrutura. O parceiro pode fornecer gerenciamento de recuperação de desastre como um serviço gerenciado ou o locatário pode ter a recuperação de desastre como uma solução de autoatendimento.

* **Provedor de serviços de hospedagem dedicado**: o parceiro possui a infraestrutura física, mas usa recursos dedicados (vários vCenters, repositórios de armazenamento físicos e assim por diante) para hospedar VMs de cada locatário em uma infraestrutura separada. O parceiro pode fornecer gerenciamento de recuperação de desastre como um serviço gerenciado ou o locatário pode ser proprietário como uma solução de autoatendimento.

* **Provedor de serviços gerenciados (MSP)** : o cliente possui a infraestrutura física que hospeda as VMs, e o parceiro fornece habilitação e gerenciamento de recuperação de desastres.

## <a name="shared-hosting-services-provider-hsp"></a>Provedor de serviços de hospedagem compartilhada (HSP)

Os outros dois cenários são subconjuntos do cenário de hospedagem compartilhada e usam os mesmos princípios. As diferenças são descritas no final das diretrizes de hospedagem compartilhada.

O requisito básico em um cenário de multilocatário é que os locatários devem ser isolados. Um locatário não deve ser capaz de observar o que é hospedado por outro locatário. Em um ambiente gerenciado por parceiros, esse requisito não é tão importante quanto em um ambiente de autoatendimento, onde pode ser crítico. Este artigo pressupõe que o isolamento de locatário é necessário.

A arquitetura é mostrada no diagrama a seguir.

![HSP compartilhado com um vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Hospedagem compartilhada com um servidor vCenter**

No diagrama, cada cliente tem um servidor de gerenciamento separado. Essa configuração limita o acesso de locatário a VMs específicas de locatário e habilita o isolamento de locatário. A replicação de VM do VMware usa o servidor de configuração para descobrir VMs e instalar agentes. Os mesmos princípios se aplicam a ambientes multilocatários, com a adição de restrição da descoberta de VM usando o controle de acesso do vCenter.

O requisito de isolamento de dados significa que todas as informações confidenciais de infraestrutura (como credenciais de acesso) permanecem desdivulgadas para os locatários. Por esse motivo, recomendamos que todos os componentes do servidor de gerenciamento permaneçam sob o controle exclusivo do parceiro. Os componentes do servidor de gerenciamento são:

* Servidor de configuração
* Servidor de processo
* Servidor de destino mestre

Um servidor de processo expandido separado também está sob o controle do parceiro.

## <a name="configuration-server-accounts"></a>Contas do servidor de configuração

Cada servidor de configuração no cenário multilocatário usa duas contas:

- **conta de acesso do vCenter**: essa conta é usada para descobrir VMs de locatário. Ele tem permissões de acesso do vCenter atribuídas a ele. Para ajudar a evitar vazamentos de acesso, recomendamos que os parceiros insiram essas credenciais na ferramenta de configuração.

- **Conta de acesso à máquina virtual**: essa conta é usada para instalar o agente de serviço de mobilidade em VMs de locatário, com um envio automático. Normalmente, é uma conta de domínio que um locatário pode fornecer a um parceiro ou uma conta que o parceiro possa gerenciar diretamente. Se um locatário não quiser compartilhar os detalhes diretamente com o parceiro, eles poderão inserir as credenciais por meio de acesso em tempo limitado ao servidor de configuração. Ou, com a assistência do parceiro, eles podem instalar o agente do serviço de mobilidade manualmente.

## <a name="vcenter-account-requirements"></a>requisitos de conta do vCenter

Configure o servidor de configuração com uma conta que tenha uma função especial atribuída a ele.

- A atribuição de função deve ser aplicada à conta de acesso do vCenter para cada objeto do vCenter e não propagada para os objetos filho. Essa configuração garante o isolamento de locatário, pois a propagação de acesso pode resultar em acesso acidental a outros objetos.

    ![A opção propagar para objetos filho](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- A abordagem alternativa é atribuir a conta de usuário e a função no objeto datacenter e propagá-las aos objetos filho. Em seguida, dê à conta uma função **sem acesso** para todos os objetos (como as VMs que pertencem a outros locatários) que devem ser inacessíveis a um locatário específico. Essa configuração é complicada. Ele expõe controles de acesso acidentais, pois cada novo objeto filho também recebe automaticamente o acesso herdado do pai. Portanto, recomendamos que você use a primeira abordagem.

### <a name="create-a-vcenter-account"></a>Criar uma conta do vCenter

1. Crie uma nova função clonando a função *somente leitura* predefinida e dê a ela um nome conveniente (como Azure_Site_Recovery, conforme mostrado neste exemplo).
2. Atribua as seguintes permissões a esta função:

   * **Repositório de armazenamento**: alocar espaço, procurar repositório de armazenamento, operações de arquivo de nível baixo, remover arquivo, atualizar arquivos de máquina virtual
   * **Rede**: atribuição de rede
   * **Recurso**: atribuir VM ao pool de recursos, MIGRAr VM desligada, migrar VM ligada
   * **Tarefas**: criar tarefa, atualizar tarefa
   * **VM-configuração**: todas
   * **VM-interação** > pergunta de resposta, conexão do dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas do VMware
   * **VM-inventário** > criar a partir de existente, criar novo, registrar, cancelar registro
   * **VM-provisionamento** > permitir download de máquina virtual, permitir carregamento de arquivos de máquina virtual
   * **VM-gerenciamento de instantâneo** > remover instantâneos

       ![A caixa de diálogo Editar função](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Atribua níveis de acesso à conta do vCenter (usada no servidor de configuração do locatário) para vários objetos, da seguinte maneira:

>| Objeto | Função | Observações |
>| --- | --- | --- |
>| vCenter | Somente leitura | Necessário apenas para permitir que o vCenter acesse o gerenciamento de objetos diferentes. Você poderá remover essa permissão se a conta nunca for fornecida a um locatário ou usada para qualquer operação de gerenciamento no vCenter. |
>| Datacenter | Azure_Site_Recovery |  |
>| Host e cluster de hosts | Azure_Site_Recovery | Verifica novamente se o acesso está no nível do objeto, de modo que somente hosts acessíveis tenham VMs de locatário antes do failover e após o failback. |
>| Datastore e cluster de armazenamento de datastore | Azure_Site_Recovery | O mesmo que o anterior. |
>| Rede | Azure_Site_Recovery |  |
>| Servidor de gerenciamento | Azure_Site_Recovery | Inclui acesso a todos os componentes (CS, PS e MT) fora da máquina CS. |
>| VMs de locatário | Azure_Site_Recovery | Garante que todas as VMs de locatário novas de um locatário específico também obtenham esse acesso, ou elas não serão descobertas por meio do portal do Azure. |

O acesso à conta do vCenter agora está concluído. Esta etapa atende o requisito mínimo de permissões para concluir as operações de failback. Você também pode usar essas permissões de acesso com suas políticas existentes. Basta modificar as permissões existentes definidas para incluir permissões de função da etapa 2, detalhadas anteriormente.

### <a name="failover-only"></a>Somente failover
Para restringir as operações de recuperação de desastre até o failover somente (ou seja, sem recursos de failback), use o procedimento anterior, com estas exceções:

- Em vez de atribuir a função de *Azure_Site_Recovery* à conta de acesso do vCenter, atribua apenas uma função *somente leitura* a essa conta. Esse conjunto de permissões permite a replicação e o failover da VM e não permite o failback.
- Tudo o mais no processo anterior permanece como está. Para garantir o isolamento do locatário e restringir a descoberta de VM, cada permissão ainda é atribuída somente no nível do objeto e não propagada para objetos filho.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Implantar recursos na assinatura de locatário

1. Na portal do Azure, crie um grupo de recursos e, em seguida, implante um cofre dos serviços de recuperação de acordo com o processo usual.
2. Transfira a chave de registo do cofre.
3. Registre o CS para o locatário usando a chave de registro do cofre.
4. Insira as credenciais para as duas contas de acesso, a conta para acessar o servidor vCenter e a conta para acessar a VM.

    ![Contas do servidor de configuração do Gerenciador](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Registrar servidores no cofre

1. No portal do Azure, no cofre que você criou anteriormente, registre o servidor do vCenter no servidor de configuração usando a conta do vCenter que você criou.
2. Conclua o processo de "preparar a infraestrutura" para Site Recovery de acordo com o processo usual.
3. As VMs agora estão prontas para serem replicadas. Verifique se apenas as VMs do locatário são exibidas em **replicar** > **selecione máquinas virtuais**.

## <a name="dedicated-hosting-solution"></a>Solução de hospedagem dedicada

Conforme mostrado no diagrama a seguir, a diferença arquitetônica em uma solução de hospedagem dedicada é que a infraestrutura de cada locatário é configurada somente para esse locatário.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Cenário de hospedagem dedicada com vários vCenters**

## <a name="managed-service-solution"></a>Solução de serviço gerenciado

Conforme mostrado no diagrama a seguir, a diferença arquitetônica em uma solução de serviço gerenciado é que a infraestrutura de cada locatário também é fisicamente separada da infraestrutura de outros locatários. Esse cenário geralmente existe quando o locatário possui a infraestrutura e deseja que um provedor de solução gerencie a recuperação de desastre.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Cenário de serviço gerenciado com vários vCenters**

## <a name="next-steps"></a>Passos seguintes
- [Saiba mais](site-recovery-role-based-linked-access-control.md) sobre o controle de acesso baseado em função no site Recovery.
- Saiba como [Configurar a recuperação de desastre de VMs do VMware no Azure](vmware-azure-tutorial.md).
- Saiba mais sobre [multilocação com o CSP para VMs VMware](vmware-azure-multi-tenant-csp-disaster-recovery.md).
