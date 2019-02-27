---
title: Mover VMs de IaaS do Azure para outra região do Azure como VMs afixadas por zona com o serviço Azure Site Recovery | Documentos da Microsoft
description: Utilize o Azure Site Recovery para mover VMs de IaaS do Azure para outra região do Azure como VMs afixadas por zona.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 77c38089a4f9c6518d9736df7f3020c66ad17b3b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877049"
---
# <a name="move-azure-vms-into-availability-zones"></a>Mover VMs do Azure para zonas de disponibilidade
As zonas de disponibilidade no Azure protege as suas aplicações e dados de falhas de datacenter. Cada zonas de disponibilidade é constituído por um ou mais datacenters equipados com energia, refrigeração e redes. Para garantir a resiliência, existe um mínimo de três zonas separadas em todas as regiões ativadas. A separação física das zonas de disponibilidade numa região protege as aplicações e dados de falhas de datacenter. Com as zonas de disponibilidade, o Azure oferece o SLA de tempo de atividade VM do setor melhor 99,99%. Zona de disponibilidade é suportada em determinadas regiões, como mencionado [aqui](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones). 

Num cenário em que implementou as máquinas virtuais como instância única numa região específica, e pretender melhorar a disponibilidade ao mover estes na zona de disponibilidade, pode fazer isso com o Azure Site Recovery. Ainda mais podem ser categorizada em:

- Mover VMs de instância única para zonas de disponibilidade numa região de destino
- Mover as VMs num conjunto em zonas de disponibilidade numa região de destino de disponibilidade

> [!IMPORTANT]
> Atualmente o Azure Site Recovery suporta mover VMs a partir de na região para outra e não suporta a movimentação dentro de uma região. 

## <a name="verify-prerequisites"></a>Certifique-se de pré-requisitos

- Verificar se a região de destino tem [suporte para a zona de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones)-validar a sua escolha de [região de origem - combinação da região de destino é suportado](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) e tomar uma decisão informada sobre a região de destino.
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Veja os [requisitos e limitações de suporte](azure-to-azure-support-matrix.md).
- Verificar permissões de conta: Se acabou de criar a conta gratuita do Azure, então é o administrador da sua subscrição. Se não for o administrador da subscrição, combine com o administrador para atribuir as permissões que precisa. Para ativar a replicação para uma VM e, eventualmente, copiar dados para o destino com o Azure Site Recovery, tem de ter:

    1. Permissões para criar uma VM nos recursos do Azure. A função incorporada "Contribuinte de Máquina Virtual" tem estas permissões, que incluem:
        - Permissão para criar uma VM no grupo de recursos selecionado
        - Permissão para criar uma VM na rede virtual selecionada
        - Permissão para escrever na conta de armazenamento selecionada

    2. Também precisa de permissão para gerir as operações do Azure Site Recovery. A função "Contribuinte do Site Recovery" tem todas as permissões precisas para gerir as operações do Azure Site Recovery num cofre dos Serviços de Recuperação.

## <a name="prepare-the-source-vms"></a>Preparar as VMs de origem

1. As suas VMs devem estar a utilizar discos geridos se quiser movê-los para a zona de disponibilidade com o Site Recovery. Pode converter existentes máquinas de virtuais de Windows (VMs) que utilizam discos não geridos, pode converter as VMs para utilizar discos geridos ao seguir os passos mencionados [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Para isso, certifique-se de que o conjunto de disponibilidade está configurado como "gerido. 
2. Verifique se todos os certificados de raiz mais recentes estão presentes nas VMs do Azure que pretende mover. Se os certificados de raiz mais recentes não estão presentes, a cópia de dados para a região de destino não pode ser ativada devido a restrições de segurança.

3. Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados de raiz fidedigna fiquem na máquina. Num ambiente desligado, siga o padrão do Windows Update e os processos de atualização de certificados da sua organização.

4. Para VMs do Linux, siga as orientações fornecidas pelo distribuidor do Linux, para obter os certificados de raiz fidedigna mais recentes e a lista de revogação de certificados na VM.
5. Certifique-se de que não estiver a utilizar um proxy de autenticação para controlar a conectividade de rede para as VMs que pretende mover.

6. Se a VM está a tentar mover não tem acesso à internet e estiver a utilizar um proxy de firewall para controlar o acesso de saída, verifique os requisitos [aqui](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

7. Identifique o esquema de rede de origem e de todos os recursos que estão a utilizar - incluindo balanceadores de carga, NSGs, pública etc. IP para a verificação.

## <a name="prepare-the-target-region"></a>Preparar a região de destino

1. Certifique-se de que a sua subscrição do Azure permite criar VMs na região de destino utilizada para recuperação após desastre. Contacte o suporte para ativar a quota necessária se for necessário.

2. Certifique-se de que a sua subscrição tem recursos suficientes para suportar as VMs com tamanhos que correspondem às VMs de origem. Se estiver a utilizar o Site Recovery para copiar dados para o destino, ele escolhe o mesmo tamanho ou o tamanho mais próximo possível da VM de destino.

3. Certifique-se de que criar um recurso de destino para cada componente identificado no esquema de rede de origem. Isso é importante para se certificar de que, postagens cutting durante para a região de destino, as VMs têm todas as funcionalidades e recursos que tinha na origem.

    > [!NOTE]
    > O Azure Site Recovery Deteta e cria uma rede virtual e a conta de armazenamento, quando ativar a replicação para a VM de origem, ou também pode criar previamente estes recursos e atribuir à VM como parte do passo ativar replicação automaticamente. Mas para outros recursos, conforme mencionado abaixo, terá de criá-los manualmente na região de destino.

     Consulte os seguintes documentos para criar os recursos de rede mais comumente usada relevantes para si, com base na configuração da VM de origem.

    - [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Balanceadores de carga] (https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials
        
     - [IP público](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Para outros componentes de rede, consulte o funcionamento em rede [documentação.](https://docs.microsoft.com/azure/#pivot=products&panel=network) 

> [!IMPORTANT]
> Certifique-se para utilizar um balanceador de carga com redundância de zona no destino. Pode ler mais [aqui](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Manualmente [criar uma rede de não produção](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) na região de destino se pretender testar a configuração antes de executar o desligamento final sobre a região de destino. Isso criará um mínimo interferência com o ambiente de produção e é recomendado.


> [!NOTE]
> A seguir etapas são para uma única VM, pode expandir o mesmo para as várias VMs ao navegar para o Cofre dos serviços de recuperação e clicar em + replicar e ao selecionar as VMs relevantes em conjunto.

## <a name="enable-replication"></a>Ativar a replicação
Os passos abaixo irão orientá-lo como utilizar o Azure Site Recovery para ativar a replicação de dados para a região de destino, antes de prosseguir, eventualmente, em zonas de disponibilidade.

1. No portal do Azure, clique em **máquinas virtuais**e selecione a VM que pretende mover para zonas de disponibilidade.
2. Nas **Operações**, clique em **Recuperação após desastre**.
3. Em **Configurar recuperação após desastre** > **Região de destino**, selecione a região de destino para a qual irá replicar. Certifique-se nesta região [suporta](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones) zonas de disponibilidade.
![enable-rep-1.PNG](media/azure-vms-to-zones/enable-rep-1.PNG)

1. Selecione **seguinte: Definições avançadas**
2. Escolha os valores de appropraite para destino subscriptiom, destino o grupo de recursos de VM e rede virtual.
3. Na **disponibilidade** secção, selecione a zona de disponibilidade na qual pretende mover a VM. 
> [!NOTE]
> Se não vir a opção para o conjunto de disponibilidade ou a zona de disponibilidade, certifique-se que o [pré-requisitos](#prepare-the-source-vms) são cumpridos e a [preparação](#prepare-the-source-vms) da origem de VMs estão concluídas.

   ![enable-rep-2.PNG](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Clique em Ativar replicação. Esta ação inicia uma tarefa para ativar a replicação para a VM.

## <a name="verify-settings"></a>Verificar as definições

Após a tarefa de replicação ser concluída, pode ver o estado da replicação, modificar a replicação e testar a implementação.

1. No menu VM, clique em **Recuperação após desastre**.
2. Pode verificar o estado de funcionamento da replicação, os pontos de recuperação que foram criados e as regiões de origem e destino num mapa.

   ![Estado de replicação](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Testar a configuração

1. No menu da máquina virtual, clique em **recuperação após desastre**.
2. Clique em **ativação pós-falha de teste** ícone.
3. Em **Ativação Pós-falha**, selecione um ponto de recuperação para utilizar na ativação pós-falha:

   - **Processado mais recentemente**: Falha da VM através do ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (Objetivo de Tempo de Recuperação) baixo
   - **Mais recente consistente com a aplicação**: Esta opção faz a ativação pós-falha de todas as VMs para o ponto de recuperação consistente com a aplicação mais recente. O carimbo de data/hora é apresentado.
   - **Custom**: Selecione qualquer ponto de recuperação.

3. Selecione o teste de rede virtual do Azure para o qual pretende mover as VMs do Azure para testar a configuração de destino. 

> [!IMPORTANT]
> Recomendamos que utilize uma rede de VM do Azure separada para a falha de teste e não a rede de produção na região de destino no qual deseja mover as suas VMs no final.

4. Para começar a testar a mudança, clique em **OK**. Para acompanhar o progresso, clique em VM para abrir as respetivas propriedades. Também pode clicar na tarefa **Ativação Pós-falha de Teste** no nome do cofre > **Definições** > **Tarefas** > **Tarefas do Site Recovery**.
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
6. Se pretender eliminar a VM criada como parte do teste a mudança, clique em **ativação pós-falha de teste de limpeza** no item replicado. Na **notas**, registe e guarde todas as observações associadas com o teste.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Efetuar a mudança para a região de destino e confirme.

1.  No menu da máquina virtual, clique em **recuperação após desastre**.
2. Clique em **ativação pós-falha** ícone.
3. Em **Ativação pós-falha**, selecione **Mais recente**. 
4. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**. 
5. Quando a tarefa estiver concluída, verifique se a VM aparece a região do Azure de destino conforme esperado.
6. Em **Itens replicados**, clique com o botão direito do rato na VM > **Consolidar**. Isto conclui o processo de mover para a região de destino. Aguarde até que a tarefa de consolidação é concluída.

## <a name="discard-the-resource-in-the-source-region"></a>Eliminar o recurso na região de origem 

1. Navegue para a VM.  Clique em **desative a replicação**.  Isso interromperá o processo de copiar os dados para a VM.  

> [!IMPORTANT]
> É importante executar o passo acima para evitar ser cobrado a mudança para o post de replicação do Site Recovery. As definições de replicação da origem são limpas automaticamente. Tenha em atenção que a extensão de recuperação de sites que é instalada como parte da replicação não é removida e têm de ser removidos manualmente. 

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial aumentou a disponibilidade de uma VM do Azure ao mover para um conjunto de disponibilidade ou a zona de disponibilidade. Agora pode configurar a recuperação de desastres para a VM movida.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)


