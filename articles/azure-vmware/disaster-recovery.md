---
title: Complete uma recuperação de desastres de máquinas virtuais
description: Este artigo mostra como completar uma recuperação de desastres de máquinas virtuais usando AVS
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 5ccaa009c8e3e059597636a8bb78cc3bd255fe68
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84749958"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Complete uma recuperação de desastres de máquinas virtuais usando Azure VMware Solution

Este artigo contém o processo para completar uma recuperação de desastres das suas máquinas virtuais com solução VMware Hybrid Cloud Extension (HCX) e utilizando uma nuvem privada Azure VMware Solution como local de recuperação ou alvo.

O VMware HCX fornece várias operações que proporcionam um controlo fino e granularidade nas políticas de replicação. As Operações disponíveis incluem:

- Inverter – Depois de um desastre. O reverso ajuda a fazer do Site B o local de origem e o local A onde o VM protegido agora vive.

- Pausa – Pausa na atual política de replicação associada à máquina virtual selecionada.

- Retomar - Pausa na atual política de replicação associada à máquina virtual selecionada.

- Remover - Remover a política de replicação atual associada à máquina virtual selecionada.

- Sync Now – Máquina virtual de origem sincronizada para o VM protegido.

Neste guia são abordados os seguintes cenários de replicação:

- Proteja um VM ou um grupo de VMs.

- Complete um Teste Recuperar de um VM ou um grupo de VMs.

- Recupere um VM ou um grupo de VMs.

- Proteção inversa de um VM ou de um grupo de VMs.

## <a name="protect-virtual-machines"></a>Proteger máquinas virtuais

Inicie sessão no **vSphere Cliente** no site de origem e aceda **ao plugin HCX**.

:::image type="content" source="./media/disaster-recovery/hcx-vsphere.png" alt-text="Opção HCX em vSphere" border="true":::

Insira a área **de recuperação de desastres** e clique em **PROTECT VMS**.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine.png" alt-text="selecionar proteger vms" border="true":::

Na janela que se abre, selecione a Fonte e os locais remotos, o site remoto neste caso deve ser a nuvem privada AVS.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machines.png" alt-text="proteger a janela VMs" border="true":::

Se necessário, selecione as opções de replicação predefinidos:

- **Ativar a compressão:** Recomendado para cenários de baixa produção.

- **Ativar a Quiescence:** Pausa o VM para garantir que uma cópia consistente é sincronizada com o site remoto.

- **Armazenamento de destino:** Selecione a loja de dados remota para os VM(s) protegidos. Numa nuvem privada AVS, esta seleção deve ser a loja de dados vSAN.

- **Recipiente de computação:** O cluster remoto vSphere ou piscina de recursos.

- **Pasta de destino:** A pasta de destino remoto, esta definição é opcional e se não for selecionada nenhuma pasta, os VM(s) serão orientados diretamente sob o cluster selecionado.

- **RPO:** Este valor é o intervalo de sincronização entre a máquina virtual Source e a máquina virtual protegida e pode estar em qualquer lugar entre 5 minutos e 24 horas.

- **Intervalo instantâneo:** Intervalo entre os instantâneos.

- **Número de instantâneos:** Número total de instantâneos dentro do intervalo de instantâneo configurado.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine-options.png" alt-text="proteger opções de máquinas virtuais" border="true":::

Selecione uma ou mais máquinas virtuais da lista e configuure a Máquina Virtual as opções de replicação conforme necessário.

Por predefinição, as máquinas virtuais herdarão a Política de Definições Globais configurada nas Opções de Replicação Predefinida. Para cada interface de rede no VM selecionado, configuure o **Grupo porta** de rede remoto e selecione **Finish** para iniciar o processo de proteção.

:::image type="content" source="./media/disaster-recovery/network-interface-options.png" alt-text="Opções de interface de rede" border="true":::

Como visto na imagem seguinte, pode monitorizar o processo para cada uma das máquinas virtuais selecionadas na mesma área de recuperação de desastres.

:::image type="content" source="./media/disaster-recovery/protect-monitor-progress.png" alt-text="monitorizar o progresso da proteção" border="true":::

Depois de protegido o VM, as diferentes imagens podem ser vistas no **separador Snapshots.**

:::image type="content" source="./media/disaster-recovery/list-of-snapshots.png" alt-text="lista de instantâneos" border="true":::

O triângulo amarelo significa que os instantâneos e o virtual não foram testados numa operação de recuperação de testes.

Há diferenças fundamentais entre um VM que é desligado e um que é ligado.
A imagem anterior mostra o processo de sincronização de uma máquina virtual alimentada. Iniciou o processo de sincronização até terminar o primeiro instantâneo, que é uma cópia completa do VM, e depois completa os seguintes no intervalo configurado.

Para um VM desligado, sincronizará uma cópia e, em seguida, o VM aparecerá como uma operação inativa e de proteção aparecerá conforme concluído.

Quando a máquina virtual é ativada, iniciará o processo de sincronização para o local remoto.

## <a name="complete-a-test-recover-of-virtual-machines"></a>Complete um teste de recuperação de máquinas virtuais

Inicie sessão no **vSphere Client** no site remoto, que é a nuvem privada AVS. Dentro do **plugin HCX**, na área de Recuperação de Desastres, selecione as elipses verticais em qualquer VM para visualizar o menu de operações. Selecione **Teste recuperar VM**.

:::image type="content" source="./media/disaster-recovery/test-recover-virtual-machine.png" alt-text="Selecione Teste Recuperar VM" border="true":::

Na nova janela, selecione as opções para o teste. Selecione a imagem instantânea que pretende utilizar para testar diferentes estados da máquina virtual.

:::image type="content" source="./media/disaster-recovery/choose-snapshot.png" alt-text="escolha um instantâneo e clique no teste" border="true":::

Depois de clicar no **Teste,** iniciar-se-á a operação de recuperação.

Quando a operação de Recuperação de Testes estiver concluída, o novo VM pode ser verificado no vCenter de nuvem privada AVS.

:::image type="content" source="./media/disaster-recovery/verify-test-recovery.png" alt-text="verificar a operação de recuperação" border="true":::

Finalmente, após o teste ter sido feito no VM ou qualquer aplicação em execução nele fazer uma limpeza para apagar a instância de teste.

:::image type="content" source="./media/disaster-recovery/cleanup-test-instance.png" alt-text="caso de teste de limpeza" border="true":::

## <a name="recover-virtual-machines"></a>Recuperar máquinas virtuais

Inicie sessão no **vSphere Client** no site remoto, que é a nuvem privada AVS, e aceda ao **plugin HCX**.

Para o cenário de recuperação, um grupo de máquinas virtuais usadas para este exemplo.

Selecione a máquina virtual a recuperar da lista, abra o menu **AÇÕES** e selecione **Recuperar VMs**.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines.png" alt-text="recuperar máquinas virtuais" border="true":::

Configure as opções de recuperação para cada instância e clique em **Recuperar** para iniciar a operação de recuperação.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines-confirm.png" alt-text="recuperar a confirmação de máquinas virtuais" border="true":::

Após a conclusão da operação de recuperação, os novos VM aparecerão no inventário remoto do Servidor vCenter.

## <a name="complete-a-reverse-replication-on-virtual-machines"></a>Complete uma replicação inversa em máquinas virtuais

Inicie sessão no **vSphere Client** na sua nuvem privada AVS e aceda **ao plugin HCX**.
É necessário que as máquinas virtuais originais no local de origem sejam desligadas antes de iniciar a replicação inversa. A operação falha se as máquinas virtuais não forem desligadas.

Selecione as máquinas virtuais a replicar-se de volta para o site de origem da lista, abra o menu **AÇÕES** e selecione **Reverso**. Na janela pop-up clique no **Reverso** para iniciar a replicação.

:::image type="content" source="./media/disaster-recovery/reverse-operation-virtual-machines.png" alt-text="Selecione ação inversa sob operações de proteção" border="true":::

A replicação pode ser monitorizada na secção de detalhes de cada máquina virtual.

:::image type="content" source="./media/disaster-recovery/review-reverse-operation.png" alt-text="rever os resultados da ação inversa" border="true":::

## <a name="disaster-recovery-plan-automation"></a>Automatização de plano de recuperação de desastres

A VMware HCX não tem atualmente um mecanismo incorporado para criar e automatizar um plano de recuperação de desastres. Esta capacidade não existe em HCX. No entanto, fornece um conjunto de APIs de REST, incluindo APIs para a operação de recuperação de desastres.

A especificação API pode ser acedido dentro do HCX Manager no URL.

As seguintes operações de recuperação de desastres são abrangidas por estas APIs.

- Proteger

- Recuperar

- Recuperação de testes

- Recuperação Planeada

- Inverter

- Consulta

- Limpeza de Testes

- Colocar em pausa

- Retomar

- Remover Proteção

- Reconfigurar

Um exemplo de uma carga útil de operação de recuperação em JSON é mostrado abaixo.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Utilizando estas APIs, um cliente pode construir um mecanismo personalizado para automatizar a criação e execução de um Plano de Recuperação de Desastres.
