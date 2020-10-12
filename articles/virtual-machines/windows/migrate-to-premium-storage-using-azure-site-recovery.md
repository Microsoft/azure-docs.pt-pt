---
title: Migrar os seus VMs do Windows para O Azure Premium Storage com recuperação do site Azure
description: Saiba como migrar os discos VM de uma conta de armazenamento padrão para uma conta de armazenamento premium utilizando a Recuperação do Site Azure.
author: luywang
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: 203176c4c7ffed95cb4f1616f29f3953fcc0afc5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320103"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrar para armazenamento premium usando a recuperação do site Azure

[Os SSDs premium Azure](disks-types.md) oferecem suporte de disco de alto desempenho e baixa latência para máquinas virtuais (VMs) que estão a executar cargas de trabalho intensivas de I/O. Este guia ajuda-o a migrar os discos VM de uma conta de armazenamento padrão para uma conta de armazenamento premium utilizando [a Recuperação do Sítio Azure.](../../site-recovery/site-recovery-overview.md)

A Recuperação do Site é um serviço Azure que contribui para a sua estratégia de continuidade de negócios e recuperação de desastres orquestrando a replicação de servidores físicos e VMs no local para a nuvem (Azure) ou para um centro de dados secundário. Quando ocorrem interrupções na sua localização primária, falha na localização secundária para manter as aplicações e cargas de trabalho disponíveis. Falhas na tua localização primária quando volta ao normal funcionamento. 

A Recuperação do Local fornece falhas de teste para apoiar exercícios de recuperação de desastres sem afetar ambientes de produção. Pode executar falhas com perda mínima de dados (dependendo da frequência de replicação) para desastres inesperados. No cenário de migração para o Armazenamento Premium, pode utilizar o [failover na Recuperação do Site](../../site-recovery/site-recovery-failover.md) para migrar discos-alvo para uma conta de armazenamento premium.

Recomendamos migrar para o Armazenamento Premium utilizando a Recuperação do Local porque esta opção proporciona um tempo mínimo de inatividade. Esta opção também evita a execução manual de discos de cópia e criação de novos VMs. A Recuperação do Site irá copiar sistematicamente os seus discos e criar novos VMs durante o failover. 

A Recuperação do Site suporta uma série de tipos de falha com tempo mínimo ou sem tempo de inatividade. Para planear o seu tempo de inatividade e estimar a perda de dados, consulte os [tipos de falha na Recuperação do Local.](../../site-recovery/site-recovery-failover.md) Se [se preparar para ligar aos VMs Azure após o failover,](../../site-recovery/vmware-azure-tutorial.md)deverá conseguir ligar-se ao Azure VM utilizando o RDP após a falha.

![Diagrama de recuperação de desastres][1]

## <a name="azure-site-recovery-components"></a>Componentes de recuperação do local de Azure

Estes componentes de Recuperação de Sítio são relevantes para este cenário de migração:

* **O servidor de configuração** é um VM Azure que coordena a comunicação e gere processos de replicação e recuperação de dados. Neste VM, executou um único ficheiro de configuração para instalar o servidor de configuração e um componente adicional, chamado servidor de processo, como um gateway de replicação. Leia sobre [os pré-requisitos do servidor de configuração](../../site-recovery/vmware-azure-tutorial.md). Configurar o servidor de configuração apenas uma vez e pode usá-lo para todas as migrações para a mesma região.

* **O servidor de processo** é um portal de replicação que: 

  1. Recebe dados de replicação de VMs de origem.
  2. Otimiza os dados com caching, compressão e encriptação.
  3. Envia os dados para uma conta de armazenamento. 

  Também trata da instalação de push do serviço de mobilidade para obter VMs e realiza a descoberta automática de VMs de origem. O servidor de processo predefinido é instalado no servidor de configuração. Pode implementar servidores de processo autónomos adicionais para escalar a sua implementação. Leia sobre [as melhores práticas para a implementação do servidor de processos](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) e [implementação de servidores de processos adicionais](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Configurar o servidor de processo apenas uma vez, e pode usá-lo para todas as migrações para a mesma região.

* **O serviço de mobilidade** é um componente que é implantado em todos os VM padrão que pretende replicar. Captura os dados escritos no VM padrão e reencaminha-os para o servidor de processo. Leia sobre [pré-requisitos de máquinas replicadas](../../site-recovery/vmware-azure-tutorial.md).

Este gráfico mostra como estes componentes interagem:

![Interação dos componentes de recuperação do local][15]

> [!NOTE]
> A Recuperação do Site não suporta a migração de discos de Espaços de Armazenamento.

Para componentes adicionais para outros cenários, consulte [a arquitetura Scenario](../../site-recovery/vmware-azure-tutorial.md).

## <a name="azure-essentials"></a>Essencial azul

Estes são os requisitos do Azure para este cenário de migração:

* Uma subscrição do Azure.
* Uma conta de armazenamento premium Azure para armazenar dados replicados.
* Uma rede virtual Azure à qual os VMs se ligarão quando forem criados em falha. A rede virtual Azure deve estar na mesma região em que a Recuperação do Sítio funciona.
* Uma conta de armazenamento padrão Azure para armazenar registos de replicação. Esta pode ser a mesma conta de armazenamento para os discos VM que estão a ser migrados.

## <a name="prerequisites"></a>Pré-requisitos

* Compreenda os componentes relevantes do cenário de migração na secção anterior.
* Planeie o seu tempo de inatividade aprendendo sobre [o failover na Recuperação do Local.](../../site-recovery/site-recovery-failover.md)

## <a name="setup-and-migration-steps"></a>Etapas de configuração e migração

Pode utilizar a Recuperação do Sítio para migrar VMs Azure IaaS entre regiões ou dentro da mesma região. As seguintes instruções são adaptadas para este cenário de migração a partir do artigo [VMware Replicate ou servidores físicos para Azure](../../site-recovery/vmware-azure-tutorial.md). Por favor, siga os links para passos detalhados para além das instruções deste artigo.

### <a name="step-1-create-a-recovery-services-vault"></a>Passo 1: Criar um cofre dos Serviços de Recuperação

1. Abra o [portal do Azure](https://portal.azure.com).
2. Selecione **Criar uma**cópia de segurança de  >  **gestão**de recursos  >  **e recuperação do site (OMS)**. Em alternativa, pode selecionar **Browse**  >  **Recovery Services Vault**  >  **Add**.
   >[!NOTE]
   >Backup e Recuperação de Locais faziam anteriormente parte da [suite OMS.](../../azure-monitor/terminology.md#april-2018---retirement-of-operations-management-suite-brand)
1. Especifique uma região para a qual os VMs serão replicados. Para efeitos de migração na mesma região, selecione a região onde estão os VMs de origem e as contas de armazenamento de fontes. 

### <a name="step-2-choose-your-protection-goals"></a>Passo 2: Escolha os seus objetivos de proteção 

1. No VM onde pretende instalar o servidor de configuração, abra o [portal Azure](https://portal.azure.com).
2. Ir para os **cofres dos Serviços de**  >  **Recuperação Configurações**Passo de  >  **Recuperação do Local**  >  **1: Preparar a**meta de  >  **proteção da**infraestrutura .

   ![Navegar para o painel de objetivos de Proteção][2]

3. Sob **o objetivo de Proteção**, na primeira lista de drop-down, selecione Para O **Azure**. Na segunda lista de drop-down, selecione **Não virtualizado / Outros**, e, em seguida, selecione **OK**.

   ![Painel de objetivo de proteção com caixas cheias][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Passo 3: Configurar o ambiente de origem (servidor de configuração)

1. Descarregue **a configuração unificada de recuperação do local de Azure** e a chave de registo do cofre indo para a **infraestrutura Preparar**  >  **os**  >  painéis de adicionar**servidor.** 
 
   Vai precisar da chave de registo do cofre para executar a configuração unificada. A chave é válida durante cinco dias depois de gerá-la.

   ![Navegação no painel do Servidor de Adicionar][4]

2. No painel **'Adicionar servidor',** adicione um servidor de configuração.

   ![Adicione o painel do servidor com o servidor de configuração selecionado][5]

3. No VM que está a utilizar como servidor de configuração, executar Configuração Unificada para instalar o servidor de configuração e o servidor de processo. Pode [percorrer as imagens](../../site-recovery/vmware-azure-tutorial.md) para completar a instalação. Pode consultar as seguintes imagens para as etapas especificadas para este cenário de migração.

   1. Antes **de começar**, selecione **Instale o servidor de configuração e o servidor de processo**.

      ![Antes de começar a página][6]

   2. No **Registo,** navegue e selecione a chave de registo que descarregou a partir do cofre.

      ![Página de registo][7]

   3. Em **Detalhes do ambiente**, selecione se pretende replicar VMs do VMware. Para este cenário de migração, escolha **Nº.**

      ![Página de Detalhes do Ambiente][8]

4. Depois de concluída a instalação, faça o seguinte na janela do **Servidor de Configuração de Recuperação do Site do Microsoft Azure:**
 
   1. Utilize o **separador 'Gerir contas'** para criar a conta que a Recuperação do Site pode utilizar para a descoberta automática. (No cenário de proteger as máquinas físicas, a configuração da conta não é relevante, mas é necessário pelo menos uma conta para ativar um dos seguintes passos. Neste caso, pode nomear a conta e a palavra-passe como qualquer outra.) 
   2. Use o **separador De Registo de Cofre** para carregar o ficheiro de credencial do cofre.

      ![Separador de registo de cofre][9]

### <a name="step-4-set-up-the-target-environment"></a>Passo 4: Configurar o ambiente-alvo

Selecione **preparar a infraestrutura**  >  **Target**e especifique o modelo de implementação que pretende utilizar para VMs após a falha. Pode escolher **Classic** ou **Resource Manager,** dependendo do seu cenário.

![Painel de alvo][10]

A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis. 

> [!NOTE]
> Se estiver a utilizar uma conta de armazenamento premium para dados replicados, precisa de configurar uma conta de armazenamento padrão adicional para armazenar registos de replicação.

### <a name="step-5-set-up-replication-settings"></a>Passo 5: Configurar definições de replicação

Para verificar se o seu servidor de configuração está associado com sucesso à política de replicação que cria, siga [configurar as definições de replicação](../../site-recovery/vmware-azure-tutorial.md).

### <a name="step-6-plan-capacity"></a>Passo 6: Capacidade do plano

1. Utilize o [planejador de capacidade](../../site-recovery/site-recovery-capacity-planner.md) para estimar com precisão a largura de banda da rede, o armazenamento e outros requisitos para satisfazer as suas necessidades de replicação. 
2. Quando terminar, selecione **Sim, já o fiz** em **já completou o planeamento de capacidades?**

   ![Caixa para confirmar que completou o planeamento de capacidade][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Passo 7: Instalar o serviço de mobilidade e permitir a replicação

1. Pode optar por empurrar a [instalação](../../site-recovery/vmware-azure-tutorial.md) para os VMs de origem ou [instalar manualmente o serviço de mobilidade](../../site-recovery/vmware-azure-install-mobility-service.md) nos seus VMs de origem. Pode encontrar a exigência de empurrar a instalação e o caminho do instalador manual no link fornecido. Se estiver a fazer uma instalação manual, poderá ter de utilizar um endereço IP interno para encontrar o servidor de configuração.

   ![Página de detalhes do servidor de configuração][12]

   O VM falhado terá dois discos temporários: um do VM primário e outro criado durante o provisionamento do VM na região de recuperação. Para excluir o disco temporário antes da replicação, instale o serviço de mobilidade antes de permitir a replicação. Para saber mais sobre como excluir o disco temporário, consulte [Excluir discos da replicação](../../site-recovery/vmware-azure-tutorial.md).

2. Ative a replicação da seguinte forma:
   1. Selecione **Fonte de aplicação**  >  **replicada**. Depois de ter ativado a replicação pela primeira vez, selecione **+Replicar** no cofre para permitir a replicação de máquinas adicionais.
   2. No passo 1, configura a **Fonte** como servidor de processo.
   3. No passo 2, especifique o modelo de implementação pós-failover, uma conta de armazenamento premium para migrar, uma conta de armazenamento padrão para guardar registos, e uma rede virtual para não o fazer.
   4. No passo 3, adicione VMs protegidos por endereço IP. (Pode precisar de um endereço IP interno para os encontrar.)
   5. No passo 4, configurar as propriedades selecionando as contas que configura previamente no servidor de processos.
   6. No passo 5, escolha a política de replicação que criou anteriormente em "Passo 5: Configurar as definições de replicação".
   7. Selecione **OK**.

   > [!NOTE]
   > Quando um VM Azure é transatado e reiniciado, não há garantias de que obtenha o mesmo endereço IP. Se o endereço IP do servidor/processo de configuração ou os VMs Azure protegidos forem alterados, a replicação neste cenário pode não funcionar corretamente.

   ![Ativar o painel de replicação com a Fonte selecionada][13]

Ao conceber o seu ambiente de Armazenamento Azure, recomendamos que utilize contas de armazenamento separadas para cada VM num conjunto de disponibilidade. Recomendamos que siga as melhores práticas na camada de armazenamento para [utilizar várias contas de armazenamento para cada conjunto de disponibilidade](../linux/manage-availability.md). Distribuir discos VM para várias contas de armazenamento ajuda a melhorar a disponibilidade de armazenamento e distribui o E/S através da infraestrutura de armazenamento Azure.

Se os seus VMs estiverem num conjunto de disponibilidade, em vez de replicar discos de todos os VMs numa única conta de armazenamento, recomendamos vivamente a migração de vários VMs várias vezes. Desta forma, os VMs no mesmo conjunto de disponibilidade não partilham uma única conta de armazenamento. Utilize o painel **de replicação ativa** para configurar uma conta de armazenamento de destino para cada VM, um de cada vez.
 
Pode escolher um modelo de implementação pós-falha de acordo com a sua necessidade. Se escolher o Azure Resource Manager como o seu modelo de implementação pós-falha, pode falhar sobre um VM (Gestor de Recursos) para um VM (Gestor de Recursos), ou pode falhar sobre um VM (clássico) para um VM (Gestor de Recursos).

### <a name="step-8-run-a-test-failover"></a>Passo 8: Executar um teste de failover

Para verificar se a sua replicação está completa, selecione a sua instância de Recuperação do Site e, em seguida, selecione **Definições**  >  **De Itens Replicados**. Verá o estado e a percentagem do seu processo de replicação. 

Após a replicação inicial estar completa, executar um teste falha para validar a sua estratégia de replicação. Para obter etapas detalhadas de um teste de failover, consulte [executar uma falha de teste na recuperação do local](../../site-recovery/vmware-azure-tutorial.md). 

> [!NOTE]
> Antes de executar qualquer falha, certifique-se de que os seus VMs e estratégia de replicação cumprem os requisitos. Para obter mais informações sobre a execução de um teste de failover, consulte [test failover to Azure in Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Pode ver o estado do seu teste de failover em **Definições**  >  **Jobs**  >  *YOUR_FAILOVER_PLAN_NAME*. No painel, pode ver-se uma repartição dos passos e resultados de sucesso/falha. Se a falha do teste falhar em qualquer passo, selecione o passo para verificar a mensagem de erro. 

### <a name="step-9-run-a-failover"></a>Passo 9: Executar um failover

Após a conclusão do teste, executar uma falha para migrar os discos para o Armazenamento Premium e replicar as instâncias VM. Siga os passos detalhados em [Run a failover](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Certifique-se de **selecionar Desligue os VMs e sincronize os dados mais recentes**. Esta opção especifica que a Recuperação do Site deve tentar desligar os VM protegidos e sincronizar os dados de modo a que a versão mais recente dos dados seja chumbada. Se não selecionar esta opção ou a tentativa não for bem sucedida, a falha será do último ponto de recuperação disponível para o VM. 

A Recuperação do Site criará uma instância VM cujo tipo seja o mesmo que ou semelhante a um VM com capacidade de armazenamento premium. Pode verificar o desempenho e o preço de várias instâncias VM indo para o [Windows Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) ou [Linux Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Passos pós-migração

1. **Configure VMs replicados para o conjunto de disponibilidade, se aplicável**. A Recuperação do Site não suporta VMs migratórios juntamente com o conjunto de disponibilidade. Dependendo da implantação do seu VM replicado, faça um dos seguintes:
   * Para um VM criado através do modelo de implementação clássico: Adicione o VM à disponibilidade definida no portal Azure. Para etapas detalhadas, vá [a adicionar uma máquina virtual existente a um conjunto de disponibilidade](/previous-versions/azure/virtual-machines/linux/classic/configure-availability-classic).
   * Para um VM criado através do modelo de implementação do Gestor de Recursos: Guarde a sua configuração do VM e, em seguida, elimine e recorra os VMs no conjunto de disponibilidade. Para tal, utilize o script no [set Azure Resource Manager VM Availability set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Antes de executar este script, verifique as suas limitações e planeie o seu tempo de inatividade.

2. **Eliminar VMs e discos antigos.** Certifique-se de que os discos Premium são consistentes com discos de origem e que os novos VMs desempenham a mesma função que os VMs de origem. Elimine o VM e elimine os discos das suas contas de armazenamento de origem no portal Azure. Se houver um problema em que o disco não seja apagado, mesmo que tenha eliminado o VM, consulte erros de [eliminação de recursos de armazenamento de resolução de problemas](../troubleshooting/storage-resource-deletion-errors.md).

3. **Limpe a infraestrutura de recuperação do local de Azure**. Se a Recuperação do Local já não for necessária, pode limpar a sua infraestrutura. Elimine os itens replicados, o servidor de configuração e a política de recuperação e, em seguida, elimine o cofre de recuperação do local de Azure.

## <a name="troubleshooting"></a>Resolução de problemas

* [Proteção contra monitorização e resolução de problemas para máquinas virtuais e servidores físicos](../../site-recovery/site-recovery-monitor-and-troubleshoot.md)
* [Microsoft Q&Uma página de perguntas para a recuperação do site do Microsoft Azure](/answers/topics/azure-site-recovery.html)

## <a name="next-steps"></a>Passos seguintes

Para cenários específicos para máquinas virtuais migratórias, consulte os seguintes recursos:

* [Migrar Máquinas Virtuais Azure entre Contas de Armazenamento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Criar e carregar um VHD do Windows Server para o Azure](upload-generalized-managed.md)
* [Máquinas virtuais migratórias da Amazon AWS para o Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Consulte também os seguintes recursos para saber mais sobre o Azure Storage e as Máquinas Virtuais Azure:

* [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png
