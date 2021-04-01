---
title: Mover VMs Azure para outra região com recuperação do local de Azure
description: Utilize a Recuperação do Sítio azul para mover os VMs Azure IaaS de uma região de Azure para outra.
services: site-recovery
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 5ae930240872c00c8dbb45857e4e77d82766eadf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93398067"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Mover VMs Azure para outra região de Azure

Você pode querer mover a infraestrutura Azure como um serviço (IaaS) máquinas virtuais de uma região para outra para melhorar a fiabilidade, disponibilidade, gestão ou governação. Este tutorial mostra-lhe como mover VMs para outra região usando a Recuperação do Site Azure. Vai aprender a:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Preparar os VMs de origem
> * Preparar a região alvo
> * Copiar dados para a região-alvo
> * Teste a configuração.
> * Realizar o movimento
> * Descartar os recursos da região de origem


> [!IMPORTANT]
> Este artigo descreve como mover os VMs Azure de uma região para outra *como é*. Se o seu objetivo é melhorar a disponibilidade da sua infraestrutura movendo VMs para zonas de disponibilidade, consulte [Move Azure VMs para Zonas de Disponibilidade.](move-azure-vms-avset-azone.md)

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que tem VMs Azure na região de Azure *de* que pretende mover-se .
- Verifique se a sua escolha da [combinação região-alvo de origem é suportada,](./azure-to-azure-support-matrix.md#region-support)e escolha cuidadosamente a região alvo.
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Veja os [requisitos e limitações de suporte](azure-to-azure-support-matrix.md).
- Verifique as permissões da conta. Se acabou de criar a sua conta Azure gratuita, *é* o administrador da sua subscrição. Se não for o administrador, trabalhe com o administrador para obter as permissões de que precisa:
  -  Para permitir a replicação de um VM e copiar dados para o alvo utilizando a Recuperação do Site, tem de ter permissões para criar um VM nos seus recursos Azure. A função incorporada Contribuinte de Máquina Virtual tem estas permissões. Com as permissões, pode:
        - Criar uma VM no grupo de recursos selecionado.
        - Criar uma VM na rede virtual selecionada.
        - Escrever na conta de armazenamento selecionada.

  - Também precisa de permissões para gerir as operações de Recuperação do Local. A função de Contribuinte de Recuperação do Local tem todas as permissões necessárias para gerir as operações de Recuperação do Local num cofre dos Serviços de Recuperação do Azure.

## <a name="prepare-the-source-vms"></a>Preparar os VMs de origem

1. Verifique se os VMs Azure que pretende mover têm os mais recentes certificados de raiz. Se não o fizerem, não é possível permitir a cópia de dados para a região alvo devido a restrições de segurança.

    - Para os VMs do Windows, instale as atualizações mais recentes do Windows de modo a que todos os certificados de raiz fidedignos estejam na máquina. Num ambiente desligado, siga os processos padrão de atualização do Windows e atualização de certificados para a sua organização.
    - Para os VMs Linux, siga a orientação do seu distribuidor Linux para obter os mais recentes certificados de raiz fidedignos e lista de revogação de certificados.
2. Certifique-se de que não está a usar um representante de autenticação para controlar a conectividade da rede para VMs que planeia mover.
3. Se um VM que pretende mover não tiver acesso à internet e estiver a utilizar um representante de firewall para controlar o acesso de saída, verifique os [requisitos](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).
4. Documente o layout de rede de origem e todos os recursos que está a utilizar atualmente, incluindo (mas não se limitando a) equilibradores de carga, grupos de segurança de rede e endereços IP públicos para verificação.

## <a name="prepare-the-target-region"></a>Preparar a região alvo

1. Na sua assinatura Azure, verifique se pode criar VMs na região alvo que é usada para a recuperação de desastres. Contacte o Suporte para ativar a quota necessária, se necessário.

2. Certifique-se de que a sua subscrição tem recursos suficientes para suportar os seus VMs de origem. Se estiver a utilizar a Recuperação do Site para copiar dados para o alvo, ele escolhe o mesmo tamanho ou o tamanho disponível mais próximo para os VMs-alvo.

3. Certifique-se de que cria um recurso-alvo para todos os componentes identificados no layout de rede de origem. Isto garante que os seus VMs terão todas as funcionalidades e funcionalidades na região-alvo que tinham na região de origem.

   A Azure Site Recovery descobre e cria automaticamente uma conta de rede e armazenamento virtual quando ativa a replicação para o VM de origem. Também pode pré-criar estes recursos e atribuí-los ao VM como parte do passo de replicação de ativação. Mas deve criar manualmente quaisquer outros recursos na região alvo. Consulte os seguintes documentos para criar os recursos de rede mais utilizados com base na configuração VM da sua fonte:

   - [Grupos de segurança de rede](../virtual-network/manage-network-security-group.md)
   - [Balanceadores de carga](../load-balancer/index.yml)
   - [IP público](../virtual-network/virtual-network-public-ip-address.md)
    
   Para quaisquer outros componentes de rede, consulte a [documentação de networking Azure](../index.yml?pivot=products&panel=network). 

4. Para testar a configuração antes de efetuar o movimento, crie manualmente [uma rede de não produção](../virtual-network/quick-create-portal.md) na região alvo. Testar a configuração cria uma interferência mínima com o ambiente de produção, e recomendamos..
    
## <a name="copy-data-to-the-target-region"></a>Copiar dados para a região-alvo
Os seguintes passos utilizam a Recuperação do Sítio Azure para copiar dados para a região alvo.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Crie o cofre em qualquer região, exceto a fonte

1. Inscreva-se nos Serviços de Recuperação [do portal Azure.](https://portal.azure.com)  >  
2. Selecione **Criar uma** cópia de segurança das  >  **ferramentas de gestão** de recursos  >  **e recuperação do site.**
3. Para **nome**, especifique o nome amigável **ContosoVMVault**. Se tiver mais do que uma subscrição, selecione a que for adequada.
4. Crie um grupo de recursos **ContosoRG**.
5. Selecione uma região do Azure. Para verificar as regiões apoiadas, consulte os [detalhes dos preços de recuperação do local do Azure.](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Para cofres de Serviços de Recuperação, selecione **Overview**  >  **ConsotoVMVault**  >  **+Replicate**.
7. Para **obter**, selecione **Azure**.
8. Para **a localização da fonte,** selecione a região de Azure de origem onde os seus VMs estão atualmente em funcionamento.
9. Selecione o modelo de implementação do Gestor de Recursos Azure. Em seguida, selecione a **subscrição Source** e **o grupo de recursos Source**.
10. Selecione **OK** para guardar as definições.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Ativar a replicação para VMs Azure e começar a copiar os dados

A Recuperação do Site recupera uma lista dos VMs que estão associados ao grupo de subscrição e recursos.

1. Selecione o VM que pretende mover e, em seguida, selecione **OK**.
2. Para **Definições**, selecione **Recuperação de desastres**.
3. Para **configurar a região** alvo de recuperação de  >  **desastres,** selecione a região alvo a que está a replicar.
4. Opte por utilizar os recursos-alvo predefinidos ou aqueles que criou.
5. **Selecione Ativar** a replicação para iniciar o trabalho.


 

## <a name="test-the-configuration"></a>Teste a configuração.


1. Vai para o cofre. Em **Definições**  >  **Itens Replicados,** selecione a máquina virtual que pretende mover-se para a região alvo. Em seguida, selecione **Test Failover**.
2. No **Teste Failover**, selecione um ponto de recuperação para a falha:

   - **Processado mais recentemente**: faz a ativação pós-falha da VM para o ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Não é gasto tempo a processar dados, pelo que esta opção proporciona um objetivo de tempo de recuperação baixo (RTO).
   - **Aplicações mais recentes consistentes**: Falha em todos os VMs até ao mais recente ponto de recuperação consistente com aplicações. O carimbo de data/hora é apresentado.
   - **Personalizado**: selecione qualquer ponto de recuperação.

3. Selecione a rede virtual Target Azure para a qual pretende mover os VMs Azure para testar a configuração.

   > [!IMPORTANT]
   > Recomendamos que utilize uma rede Azure VM separada para a falha do teste, e não a rede de produção na região alvo.

4. Para começar a testar o movimento, selecione **OK**. Para acompanhar o progresso, selecione o VM para abrir as suas **Propriedades.** Ou, selecione o trabalho **de teste failover** no cofre. Em seguida, selecione **Settings**  >  **Jobs**  >  **Site Recovery Jobs Jobs Jobs Recovery jobs jobs jobs jobs recovery**
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
6. Para eliminar o VM que criou para testar, selecione Falha no **teste de limpeza** no item replicado. A partir de **Notas,** registem e guarde quaisquer observações relacionadas com o teste.

## <a name="perform-the-move-and-confirm"></a>Execute o movimento e confirme

1. Vá ao cofre em **Definições**  >  **Itens Replicados,** selecione a máquina virtual e, em seguida, selecione **Failover**.
1. Para **Failover**, selecione **Mais Recente**. 
2. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. A Recuperação do Local tenta desligar a fonte VM antes de desencadear a falha. Mas o fracasso continua mesmo que o encerramento falhe. Pode acompanhar o progresso falhado na página **Jobs.**
3. Quando o trabalho estiver concluído, verifique se o VM aparece na região de Azure alvo, como esperado.
4. Em **itens replicados,** clique com o botão direito no VM e selecione **Commit**. Isto termina a jogada. Espere até que o trabalho de compromisso termine.

## <a name="discard-the-resources-from-the-source-region"></a>Descartar os recursos da região de origem

- Vá ao VM e selecione **Replicação de Desativação.** Isto impede o processo de cópia dos dados para o VM.

  > [!IMPORTANT]
  > Complete este passo para evitar ser cobrado para a replicação da Recuperação do Local após o movimento.

Se não pretende reutilizar nenhum dos recursos de origem, siga estes passos:

1. Elimine todos os recursos de rede relevantes na região de origem que enumerou no passo 4 da [Prepare os VM de origem](#prepare-the-source-vms).
2. Eliminar a conta de armazenamento correspondente na região de origem.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a mover VMs Azure para uma região de Azure diferente. Agora pode configurar a recuperação de desastres para os VMs.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)
