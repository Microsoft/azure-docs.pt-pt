---
title: Complete uma recuperação de desastres de máquinas virtuais
description: Este artigo mostra como completar uma recuperação de desastres de máquinas virtuais usando AVS
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 86f823444d4fff3edf8651f4d949c71d2c981ec7
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740554"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Complete uma recuperação de desastres de máquinas virtuais usando a Solução Azure VMWare

Este artigo contém o processo para completar uma recuperação de desastres das suas máquinas virtuais com a solução VMWare Hybrid Cloud Extension (HCX) e utilizando uma nuvem privada Azure VMWare Solution como o site de recuperação ou alvo.

A VMWare HCX fornece várias operações que proporcionam um controlo fino e granularidade nas políticas de replicação. As Operações Disponíveis incluem:

- Inverter – Depois de um desastre ter ocorrido. O inverso ajuda a tornar o site B o local de origem e o local A onde o VM protegido agora vive.

- Pausa – Pausa na atual política de replicação associada à máquina virtual selecionada.

- Currículo - Pausa na atual política de replicação associada à máquina virtual selecionada.

- Remover - Retire a atual política de replicação associada à máquina virtual selecionada.

- Sync Now – Máquina virtual de origem sincronizada ligada ao VM protegido.

Neste guia são abordados os seguintes cenários de replicação:

- Proteja um VM ou um grupo de VMs.

- Complete uma recuperação de teste de um VM ou de um grupo de VMs.

- Recupere um VM ou um grupo de VMs.

- Proteção Inversa de um VM ou de um grupo de VMs.

## <a name="protect-virtual-machines"></a>Proteger máquinas virtuais

Inicie sessão no **Cliente vSphere** no site de origem e aceda ao **plugin HCX**.

:::image type="content" source="./media/disaster-recovery/hcx-vsphere.png" alt-text="Opção HCX na vSphere" border="true":::

Introduza a área de **Recuperação** de Desastres e clique em **PROTECT VMS**.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine.png" alt-text="selecione proteger vms" border="true":::

Na janela que se abre, selecione a Fonte e os locais Remotos, o site Remoto neste caso deve ser a nuvem privada AVS.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machines.png" alt-text="proteger a janela VMs" border="true":::

Se necessário, selecione as opções de replicação Predefinidas:

- **Ativar a compressão:** Recomendado para cenários de baixa supressão.

- **Ativar quiescência:** Pausa sintetiza o VM para garantir que uma cópia consistente é sincronizada no local remoto.

- **Armazenamento de destino:** Selecione o depósito de dados remoto para os VM(s protegidos). Numa nuvem privada DaVS, esta seleção deve ser a loja de dados VSAN.

- **Recipiente computacional:** O cluster de vSphere ou piscina de recursos remotos.

- **Pasta de Destino:** A pasta de destino remoto, esta definição é opcional e se nenhuma pasta for selecionada, o VM(s) será acelerado diretamente sob o cluster selecionado.

- **RPO:** Este valor é o intervalo de sincronização entre a máquina virtual Fonte e a máquina virtual protegida e pode estar em qualquer lugar entre 5 minutos e 24 horas.

- **Intervalo instantâneo:** Intervalo entre instantâneos.

- **Número de snapshots:** Número total de instantâneos dentro do intervalo de instantâneo configurado.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine-options.png" alt-text="proteger as opções de máquinas virtuais" border="true":::

Selecione uma ou mais máquinas virtuais da lista e configure a Máquina Virtual as opções de replicação necessárias.

Por padrão, as máquinas virtuais herdarão a Política de Definições Globais configurada nas Opções de Replicação Predefinida. Para cada interface de rede no VM selecionado, configure o Grupo de Portas de **Rede** remota e selecione **Finish** para iniciar o processo de proteção.

:::image type="content" source="./media/disaster-recovery/network-interface-options.png" alt-text="opções de interface de rede" border="true":::

Como se pode ver na imagem seguinte, pode monitorizar o processo para cada uma das máquinas virtuais selecionadas na mesma área de recuperação de desastres.

:::image type="content" source="./media/disaster-recovery/protect-monitor-progress.png" alt-text="monitorizar o progresso da proteção" border="true":::

Depois de o VM ter sido protegido, as diferentes imagens podem ser vistas no separador **Snapshots.**

:::image type="content" source="./media/disaster-recovery/list-of-snapshots.png" alt-text="lista de instantâneos" border="true":::

O triângulo amarelo significa que os instantâneos e o virtual não foram testados numa operação de recuperação de testes.

Há diferenças-chave entre um VM que é desligado e um que é ligado.
A imagem anterior mostra o processo de sincronização de uma máquina virtual alimentada. Iniciou o processo de sincronização até terminar o primeiro instantâneo, que é uma cópia completa do VM, e depois completa os seguintes no intervalo configurado.

Para um VM desligado, irá sincronizar uma cópia e, em seguida, o VM aparecerá como inativo e a operação de proteção mostrará como concluída.

Quando a máquina virtual estiver ativada, iniciará o processo de sincronização para o local remoto.

## <a name="complete-a-test-recover-of-virtual-machines"></a>Complete um teste de recuperação de máquinas virtuais

Inicie sessão no **Cliente vSphere** no site remoto, que é a nuvem privada AVS. Dentro do **plugin HCX**, na área de Recuperação de Desastres, selecione as elipses verticais em qualquer VM para exibir o menu de operações. Selecione **Teste recuperar VM**.

:::image type="content" source="./media/disaster-recovery/test-recover-virtual-machine.png" alt-text="Selecione Teste Recuperar VM" border="true":::

Na nova janela, selecione as opções para o teste. Selecione o instantâneo que pretende utilizar para testar diferentes estados da máquina virtual.

:::image type="content" source="./media/disaster-recovery/choose-snapshot.png" alt-text="escolher um teste de instantâneo e clicar" border="true":::

Depois de clicar no **Teste,** iniciar-se-á a operação de recuperação.

Quando a operação de recuperação de testes estiver concluída, o novo VM pode ser verificado no vCenter de nuvem privada AVS.

:::image type="content" source="./media/disaster-recovery/verify-test-recovery.png" alt-text="verificar operação de recuperação" border="true":::

Finalmente, após os testes no VM ou qualquer aplicação que o execute faça uma limpeza para eliminar a instância de teste.

:::image type="content" source="./media/disaster-recovery/cleanup-test-instance.png" alt-text="caso de teste de limpeza" border="true":::

## <a name="recover-virtual-machines"></a>Recuperar máquinas virtuais

Inicie sessão no **Cliente vSphere** no site remoto, que é a nuvem privada AVS, e aceda ao **plugin HCX**.

Para o cenário de recuperação, um grupo de máquinas virtuais utilizadas para este exemplo.

Selecione a máquina virtual a ser recuperada da lista, abra o menu **ACTIONS** e selecione **Recuperar VMs**.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines.png" alt-text="recuperar máquinas virtuais" border="true":::

Configure as opções de recuperação para cada instância e clique em **Recuperar** para iniciar a operação de recuperação.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines-confirm.png" alt-text="recuperar a confirmação de máquinas virtuais" border="true":::

Após a operação de recuperação estar concluída, os novos VMs aparecerão no inventário remoto vCenter Server.

## <a name="complete-a-reverse-replication-on-virtual-machines"></a>Complete uma replicação inversa em máquinas virtuais

Inicie sessão no **Cliente vSphere** na sua nuvem privada AVS e aceda ao **plugin HCX**.
É necessário que as máquinas virtuais originais no local de origem sejam desligadas antes de iniciar a replicação inversa. A operação falha se as máquinas virtuais não estiverem desligadas.

Selecione as máquinas virtuais a serem replicadas de volta ao site de origem a partir da lista, abra o menu **ACTIONS** e selecione **Reverter**. Na janela pop-up clique no **Inverso** para iniciar a replicação.

:::image type="content" source="./media/disaster-recovery/reverse-operation-virtual-machines.png" alt-text="Selecione a ação inversa sob operações de proteção" border="true":::

A replicação pode ser monitorizada na secção de detalhes de cada máquina virtual.

:::image type="content" source="./media/disaster-recovery/review-reverse-operation.png" alt-text="rever os resultados da ação inversa" border="true":::

## <a name="disaster-recovery-plan-automation"></a>Automatização do plano de recuperação de desastres

A VMWare HCX não tem atualmente um mecanismo incorporado para criar e automatizar um plano de recuperação de desastres. Esta capacidade não existe no HCX. No entanto, fornece um conjunto de APIs REST, incluindo APIs para a operação de recuperação de desastres.

A especificação DaPi pode ser acedida dentro do HCX Manager no URL.

As seguintes operações na Recuperação de Desastres são abrangidas por estas APIs.

- Proteger

- Recuperar

- Recuperação de teste

- Recuperação Planeada

- Inverter

- Consulta

- Limpeza de teste

- Colocar em pausa

- Retomar

- Remover proteção

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

Utilizando estas APIs, um cliente pode construir um mecanismo personalizado para automatizar a criação e a execução de um Plano de Recuperação de Desastres.
