---
title: Mova os VMs Azure para outra região com recuperação do site Azure
description: Utilize a Recuperação do Sítio Azure para mover Os VMs Azure IaaS de uma região de Azure para outra.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: dc37cb6fa05a2be56de7bf5536d7274190257d85
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303924"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Mover VMs Azure para outra região de Azure

É melhor transferir a infraestrutura Azure como um serviço (IaaS) máquinas virtuais de uma região para outra para melhorar a fiabilidade, disponibilidade, gestão ou governação. Este tutorial mostra-lhe como mover VMs para outra região usando a Recuperação do Site Azure. Vai aprender a:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Prepare as VMs de origem
> * Preparar a região alvo
> * Copiar dados para a região alvo
> * Testar a configuração
> * Execute o movimento
> * Descartar os recursos da região nascente


> [!IMPORTANT]
> Este artigo descreve como mover VMs Azure de uma região para outra *como é.* Se o seu objetivo é melhorar a disponibilidade da sua infraestrutura movendo VMs para zonas de disponibilidade, consulte [Move Azure VMs para Zonas de Disponibilidade](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que tem VMs Azure na região *from*de origem Azure de onde pretende mover-se .
- Verifique se a sua escolha da combinação de [região-alvo de origem é apoiada](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), e escolha cuidadosamente a região alvo.
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Veja os [requisitos e limitações de suporte](azure-to-azure-support-matrix.md).
- Verifique as permissões da conta. Se acabou de criar a sua conta Azure gratuita, *é* o administrador da sua subscrição. Se não é o administrador, trabalhe com o administrador para obter as permissões de que precisa:
  -  Para permitir a replicação de um VM e copiar dados para o alvo utilizando a Recuperação do Site, deve ter permissões para criar um VM nos seus recursos Azure. A função incorporada Contribuinte de Máquina Virtual tem estas permissões. Com as permissões, pode:
        - Criar uma VM no grupo de recursos selecionado.
        - Criar uma VM na rede virtual selecionada.
        - Escrever na conta de armazenamento selecionada.

  - Também precisa de permissões para gerir as operações de Recuperação do Local. O papel do Colaborador de Recuperação do Site tem todas as permissões necessárias para gerir as operações de recuperação do local num cofre dos Serviços de Recuperação do Azure.

## <a name="prepare-the-source-vms"></a>Prepare as VMs de origem

1. Verifique se os VMs Azure que planeia mover têm os mais recentes certificados de raiz. Se não o fizerem, não pode permitir a cópia de dados para a região alvo devido a restrições de segurança.

    - Para os VMs do Windows, instale as mais recentes atualizações do Windows para que todos os certificados de raiz fidedignos estejam na máquina. Num ambiente desligado, siga os processos padrão de Atualização do Windows e atualização de certificados para a sua organização.
    - Para os VMs Linux, siga a orientação do seu distribuidor Linux para obter os mais recentes certificados de raiz fidedignos e lista de revogação de certificados.
2. Certifique-se de que não está a usar um proxy de autenticação para controlar a conectividade da rede para VMs que planeia mover-se.
3. Se um VM que pretende mover não tem acesso à internet e está a usar um representante de firewall para controlar o acesso de saída, verifique os [requisitos](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).
4. Documente o layout de rede de origem e todos os recursos que está a utilizar atualmente, incluindo (mas não se limitando a) os equilibradores de carga, os grupos de segurança da rede e os endereços IP públicos para verificação.

## <a name="prepare-the-target-region"></a>Preparar a região alvo

1. Na sua subscrição Azure, verifique se pode criar VMs na região alvo que é usada para a recuperação de desastres. Suporte de contato para ativar a quota necessária, se necessário.

2. Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar os seus VMs de origem. Se estiver a utilizar a Recuperação do Site para copiar dados para o alvo, ele escolhe o mesmo tamanho ou o tamanho disponível mais próximo para os VMs-alvo.

3. Certifique-se de que cria um recurso-alvo para todos os componentes identificados no layout de rede de origem. Isto garante que os seus VMs terão todas as funcionalidades e funcionalidades na região alvo que tinham na região de origem.

   A Recuperação do Site Azure descobre e cria automaticamente uma rede virtual e uma conta de armazenamento quando ativa a replicação para o VM de origem. Também pode pré-criar estes recursos e atribuí-los ao VM como parte do passo de replicação de habilitação. Mas deve criar manualmente quaisquer outros recursos na região alvo. Consulte os seguintes documentos para criar os recursos de rede mais utilizados com base na configuração VM de origem:

   - [Grupos de segurança da rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Balanceadores de carga](https://docs.microsoft.com/azure/load-balancer)
   - [IP público](../virtual-network/virtual-network-public-ip-address.md)
    
   Para qualquer outro componente de rede, consulte a documentação de [rede Azure](https://docs.microsoft.com/azure/?pivot=products&panel=network). 

4. Para testar a configuração antes de executar o movimento, [crie](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) manualmente uma rede de não produção na região alvo. Testar a configuração cria interferência mínima no ambiente de produção, e recomendamo-la.
    
## <a name="copy-data-to-the-target-region"></a>Copiar dados para a região alvo
Os passos seguintes utilizam a Recuperação do Site Azure para copiar dados para a região alvo.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Crie o cofre em qualquer região, exceto a fonte

1. Inscreva-se nos Serviços de > **Recuperação**do [portal Azure.](https://portal.azure.com)
2. Selecione Criar uma cópia de segurança**e recuperação**de**ferramentas** > de gestão de **recursos.** > 
3. Para **Nome**, especifique o nome amigável **ContosoVMVault**. Se tiver mais do que uma subscrição, selecione a que for adequada.
4. Crie um grupo de recursos **ContosoRG**.
5. Selecione uma região do Azure. Para verificar as regiões apoiadas, consulte os detalhes de preços de recuperação do [site do Azure.](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Para os cofres dos Serviços de Recuperação, selecione **Overview** > **ConsotoVMVault** > **+Replicate**.
7. Para **Origem,** selecione **Azure**.
8. Para **obter a localização,** selecione a região azure de origem onde os seus VMs estão atualmente em execução.
9. Selecione o modelo de implementação do Gestor de Recursos Azure. Em seguida, selecione a **subscrição Fonte** e **o grupo de recursos Source**.
10. Selecione **OK** para guardar as definições.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Ativar a replicação para VMs Azure e começar a copiar os dados

A Recuperação do Site recupera uma lista dos VMs que estão associados ao grupo de subscrição e recursos.

1. Selecione o VM que pretende mover e, em seguida, selecione **OK**.
2. Para **configurações,** selecione recuperação de **desastres**.
3. Para **configurar** > a recuperação de desastres**Região alvo,** selecione a região alvo a que está a replicar.
4. Opte por utilizar os recursos-alvo padrão ou aqueles que pré-criou.
5. **Selecione Ativar a replicação** para iniciar o trabalho.

   ![Ativar a replicação](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Testar a configuração


1. Vai para o cofre. Em **Definições** > **Itens Replicados,** selecione a máquina virtual que pretende mover-se para a região alvo. Em seguida, selecione **Failover de teste**.
2. No **Teste Failover,** selecione um ponto de recuperação para utilizar para a falha:

   - **Processado mais recentemente**: faz a ativação pós-falha da VM para o ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Não são gastos tempo de processamento de dados, pelo que esta opção proporciona um objetivo de tempo de recuperação baixo (RTO).
   - **Mais recente, consistente com aplicações:** Falha em todos os VMs até ao mais recente ponto de recuperação consistente com aplicações. O carimbo de data/hora é apresentado.
   - **Personalizado**: selecione qualquer ponto de recuperação.

3. Selecione a rede virtual Target Azure para a qual pretende mover os VMs Azure para testar a configuração.

   > [!IMPORTANT]
   > Recomendamos que utilize uma rede Azure VM separada para o failover do teste, e não para a rede de produção na região alvo.

4. Para começar a testar o movimento, selecione **OK**. Para acompanhar o progresso, selecione o VM para abrir as suas **Propriedades.** Ou, selecione o trabalho de **failover** de teste no cofre. Em seguida, selecione **Definições** > **Empregos** > **De Recuperação**do Site .
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
6. Para eliminar o VM que criou para testar, **selecione falha** no teste de limpeza no item replicado. A partir de **Notas,** grave e guarde quaisquer observações relacionadas com o teste.

## <a name="perform-the-move-and-confirm"></a>Execute o movimento e confirme

1. Vá ao cofre em**itens replicados**de **definições,** > selecione a máquina virtual e, em seguida, selecione **Failover**.
1. Para **Failover,** selecione **Latest**. 
2. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. A Recuperação do Local tenta desligar a Fonte VM antes de desencadear a falha. Mas a falha continua mesmo que o encerramento falhe. Pode acompanhar o progresso da falha na página **Jobs.**
3. Quando o trabalho estiver concluído, verifique se o VM aparece na região-alvo de Azure, como esperado.
4. Em **itens replicados,** clique à direita no VM e selecione **Commit**. Isto termina a jogada. Espere até que o trabalho de compromisso termine.

## <a name="discard-the-resources-from-the-source-region"></a>Descartar os recursos da região nascente

- Vá ao VM e selecione **Desativação Replicação**. Isto interrompe o processo de cópia dos dados para o VM.

  > [!IMPORTANT]
  > Complete este passo para evitar ser carregado para a replicação de Recuperação do Local após a mudança.

Se não pretende reutilizar nenhum dos recursos de origem, siga estes passos:

1. Elimine todos os recursos de rede relevantes na região de origem que enumerou no passo 4 de [Prepare as VMs de origem](#prepare-the-source-vms).
2. Eliminar a conta de armazenamento correspondente na região fonte.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a mover VMs Azure para uma região azure diferente. Agora pode configurar a recuperação de desastres para os VMs.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)

