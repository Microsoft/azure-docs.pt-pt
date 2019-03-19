---
title: Descrição geral do suporte de vários inquilino para recuperação após desastre de VM de VMware para Azure (CSP) com o Azure Site Recovery | Documentos da Microsoft
description: Fornece uma visão geral do suporte do Azure Site Recovery para recuperação de desastre do VMWare para o Azure num programa de ambiente multi-inquilino (CSP).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: d227b8d038dd686bde9b031ca2c58adc7dd6d76b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104459"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Descrição geral do suporte de multi-inquilino para a recuperação de desastre do VMware para o Azure com o CSP

[O Azure Site Recovery](site-recovery-overview.md) suporta ambientes de multi-inquilinos para subscrições de inquilino. Também suporta vários inquilinos para subscrições de inquilino que são criadas e geridas através do programa de fornecedor de soluções Cloud (CSP) da Microsoft.

Este artigo fornece uma descrição geral da implementação e gestão de multi-inquilino de VMware para replicação do Azure.

## <a name="multi-tenant-environments"></a>Ambientes de multi-inquilinos

Existem três modelos principais de multi-inquilinos:

* **Fornecedor de serviços de alojamento (HSP) partilhados**: O parceiro é o proprietário a infraestrutura física e utiliza partilhado recursos (vCenter, centros de dados, armazenamento físico e assim por diante) para alojar vários inquilinos VMs na mesma infraestrutura. O parceiro pode fornecer gestão de recuperação após desastre como um serviço gerido ou o inquilino pode ser proprietário recuperação após desastre como uma solução de self-service.

* **Fornecedor de serviços de alojamento de dedicado**: O parceiro é proprietário a infraestrutura física, mas usa recursos dedicados (vários vCenters, arquivos de dados físicos e assim por diante) para hospedar VMs de cada inquilino numa infraestrutura separada. O parceiro pode fornecer gestão de recuperação após desastre como um serviço gerido ou o inquilino pode possuí-lo como uma solução de self-service.

* **Fornecedor de serviços (MSP) gerido**: O cliente possui a infraestrutura física que aloja as VMs e o parceiro fornece à habilitação da recuperação após desastre e gestão.

## <a name="shared-hosting-services-provider-hsp"></a>Fornecedor de serviços de hospedagem compartilhada (HSP)

Os outros dois cenários são subconjuntos do cenário de hospedagem compartilhada e utilizam os mesmos princípios. As diferenças são descritas no final da orientação de hospedagem compartilhada.

O requisito básico num cenário de multi-inquilino é que os inquilinos devem ficar isolados. Um inquilino não deve ser capaz de observar o que tenha hosted noutro inquilino. Num ambiente gerido por um parceiro, este requisito não é tão importante quanto num ambiente de self-service, onde pode ser considerado crítico. Este artigo pressupõe que o isolamento de inquilino é obrigatório.

A arquitetura é mostrada no diagrama seguinte.

![HSP partilhado com um vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Hospedagem compartilhada com um servidor vCenter**

No diagrama, cada cliente tem um servidor de gestão separados. Esta configuração limita o acesso de inquilino para VMs de inquilino específica e isolamento de inquilino ativa. A replicação de VM de VMware utiliza o servidor de configuração para detetar VMs e instalar agentes. Os mesmos princípios aplicam-se aos ambientes de multi-inquilino, com a adição de restringir a deteção de VMS com o controlo de acesso do vCenter.

O requisito de isolamento de dados significa que todas as informações de infraestrutura confidenciais (por exemplo, as credenciais de acesso) permaneçam undisclosed aos inquilinos. Por esse motivo, recomendamos que todos os componentes do servidor de gestão permanecem sob o controlo exclusivo do parceiro de. Os componentes de servidor de gestão são:

* Servidor de configuração
* Servidor de processos
* Servidor de destino mestre

Também é um servidor de processo de escalamento horizontal separado sob o controle do parceiro.

## <a name="configuration-server-accounts"></a>Contas de servidor de configuração

Cada servidor de configuração no cenário de multi-inquilino utiliza duas contas:

- **a conta de acesso do vCenter**: Esta conta é utilizada para detetar VMs inquilinas. Ele tem as permissões de acesso do vCenter atribuídas ao mesmo. Para ajudar a evitar fugas de acesso, recomendamos que os parceiros introduza estas credenciais próprios na ferramenta de configuração.

- **Conta de acesso à máquina virtual**: Esta conta é utilizada para instalar o agente do serviço de mobilidade num inquilino VMs, com um push automático. Normalmente, é uma conta de domínio que um inquilino poderão fornecer a um parceiro ou uma conta que o parceiro pode gerir diretamente. Se um inquilino não quer partilhar os detalhes com o parceiro diretamente, eles podem introduzir as credenciais por meio do acesso de tempo limitado para o servidor de configuração. Ou, com a assistência do parceiro, podem instalar o agente do serviço de mobilidade manualmente.

## <a name="vcenter-account-requirements"></a>requisitos de conta do vCenter

Configure o servidor de configuração com uma conta que tenha um papel especial atribuído ao mesmo.

- A atribuição de função tem de ser aplicada para a conta de acesso de vCenter para cada objeto de vCenter e não propagada aos objetos subordinados. Esta configuração garante o isolamento de inquilino, porque a propagação de acesso pode resultar em acidental acesso a outros objetos.

    ![A propagar a opção de objetos subordinados](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- A abordagem alternativa é atribuir a conta de utilizador e a função para o objeto de datacenter e propagá-las para os objetos subordinados. Em seguida, atribua à conta uma **sem acesso** função para cada objeto (por exemplo, as VMs que pertencem a outros inquilinos) que deve ser acessível a um inquilino específico. Esta configuração é complicada. Ele expõe controlos de acesso acidental, uma vez que cada novo objeto filho também automaticamente recebe acesso que é herdado do pai. Por conseguinte, recomendamos que utilize a primeira abordagem.

### <a name="create-a-vcenter-account"></a>Criar uma conta do vCenter

1. Criar uma nova função através da clonagem predefinidos *só de leitura* função e, em seguida, atribua um nome conveniente (por exemplo, Azure_Site_Recovery, conforme mostrado neste exemplo).
2. Atribua as seguintes permissões a esta função:

   * **Arquivo de dados**: Alocar espaço, arquivo de dados de procura, as operações de arquivo de nível baixo, remova o ficheiro, ficheiros de atualização de máquina virtual
   * **Rede**: Atribuir rede
   * **Recurso**: Atribuir VM a agrupamento de recursos, migrar desligado da VM, com tecnologia na VM para migrar
   * **Tarefas**: Criar tarefa, a tarefa de atualização
   * **VM - configuração**: Todos
   * **VM - interação** > responder a pergunta, ligação de dispositivos, suporte de dados de configurar o CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas do VMware
   * **VM - inventário** > criar a partir de existente, crie um novo, registar, anular o registo
   * **VM - aprovisionamento** > Permitir transferência de máquinas virtuais, o carregamento de ficheiros de máquina virtual de permitir
   * **VM - gestão de instantâneos** > Remover instantâneos

       ![A caixa de diálogo Editar função](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Atribua níveis de acesso à conta do vCenter (utilizado no servidor de configuração de inquilinos) para vários objetos, da seguinte forma:

>| Object | Função | Observações |
>| --- | --- | --- |
>| vCenter | Só de Leitura | É necessário apenas para permitir o acesso de vCenter para gerir diferentes objetos. Pode remover esta permissão, se a conta nunca vai ser fornecido para um inquilino ou utilizada para quaisquer operações de gestão no vCenter. |
>| Datacenter | Azure_Site_Recovery |  |
>| Anfitriões e cluster de anfitriões | Azure_Site_Recovery | Novamente garante que o acesso é ao nível do objeto, para que apenas os anfitriões acessíveis tenham VMs inquilinas antes da ativação pós-falha e depois da reativação pós-falha. |
>| Cluster de arquivo de dados e o arquivo de dados | Azure_Site_Recovery | Mesmo que a anterior. |
>| Rede | Azure_Site_Recovery |  |
>| Servidor de gestão | Azure_Site_Recovery | Inclui acesso a todos os componentes (CS, PS e MT) fora da máquina de CS. |
>| VMs do inquilino | Azure_Site_Recovery | Garante que nenhum inquilino novas VMs de um inquilino específico também obter este acesso, ou não vai ser descobertos por meio do portal do Azure. |

O acesso de conta do vCenter está agora concluído. Este passo cumpre o requisito de permissões mínimo para concluir as operações de reativação pós-falha. Também pode utilizar estas permissões de acesso com as políticas existentes. Basta modificar as suas permissões existentes, definidos como incluir permissões da função do passo 2, detalhadas anteriormente.

### <a name="failover-only"></a>Ativação pós-falha apenas
Para restringir operações de recuperação após desastre até a ativação pós-falha apenas (ou seja, sem as capacidades de reativação pós-falha), utilize o procedimento anterior, com as seguintes exceções:

- Em vez de atribuir a *Azure_Site_Recovery* função para a conta de acesso do vCenter, atribuir apenas um *só de leitura* função para essa conta. Este conjunto de permissões permite a replicação de VM e ativação pós-falha, e não permite a reativação pós-falha.
- Tudo o resto do processo anterior permanece como está. Para garantir o isolamento de inquilino e restringir a deteção de VMS, cada permissão é ainda atribuído ao nível do objeto apenas e não propagada a objetos filho.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Implementar recursos para a subscrição do inquilino

1. No portal do Azure, crie um grupo de recursos e, em seguida, implementar um cofre de serviços de recuperação pelo processo normal.
2. Transfira a chave de registo do cofre.
3. Registe o CS para o inquilino com a chave de registo do cofre.
4. Introduza as credenciais para as contas de acesso de duas, a conta para aceder ao servidor vCenter e a conta para aceder à VM.

    ![Contas de Gestor de servidor de configuração](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Registar os servidores no Cofre

1. No portal do Azure, no cofre que criou anteriormente, registe o servidor de vCenter para o servidor de configuração, com a conta do vCenter que criou.
2. Conclua o processo de "Preparar a infraestrutura" para o Site Recovery pelo processo normal.
3. As VMs agora estão prontas para ser replicada. Certifique-se de que apenas as VMs do inquilino são apresentadas na **replicar** > **selecionar máquinas virtuais**.

## <a name="dedicated-hosting-solution"></a>Dedicado a solução de hospedagem

Conforme mostrado no diagrama seguinte, a diferença de arquitetura numa solução de hospedagem dedicada é que a infraestrutura de cada inquilino está configurada para esse inquilino apenas.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Cenário de hospedagem dedicado com vários vCenters**

## <a name="managed-service-solution"></a>Solução de serviço gerido

Conforme mostrado no diagrama seguinte, a diferença de arquitetura numa solução de serviço gerido é que a infraestrutura de cada inquilino também é fisicamente separada da infraestrutura dos outros inquilinos. Este cenário existe, normalmente, quando o inquilino possui a infra-estrutura e quer que um fornecedor de soluções para gerir a recuperação após desastre.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Cenário de serviço com vários vCenters de geridos**

## <a name="next-steps"></a>Passos Seguintes
- [Saiba mais](site-recovery-role-based-linked-access-control.md) sobre o controlo de acesso baseado em funções no Site Recovery.
- Saiba como [configurar a recuperação após desastre de VMs de VMware para o Azure](vmware-azure-tutorial.md).
- Saiba mais sobre [vários inquilinos com CSP para as VMs de VMWare](vmware-azure-multi-tenant-csp-disaster-recovery.md).
