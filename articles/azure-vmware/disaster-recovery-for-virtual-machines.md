---
title: Complete uma recuperação de desastres de máquinas virtuais
description: Este artigo mostra como completar uma recuperação de desastres de máquinas virtuais usando Azure VMware Solution
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 2ccb6546b9b01255e4a28aed79fd0d3ccbc4516c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580187"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Complete uma recuperação de desastres de máquinas virtuais usando Azure VMware Solution

Este artigo contém o processo para completar uma recuperação de desastres das suas máquinas virtuais (VMs) com solução VMware HCX e utilizando uma nuvem privada Azure VMware Solution como local de recuperação ou alvo.

O VMware HCX fornece várias operações que proporcionam um controlo fino e granularidade nas políticas de replicação. As Operações disponíveis incluem:

- **Inverter** – Depois de um desastre. O reverso ajuda a fazer do Site B o local de origem e o local A onde o VM protegido agora vive.

- **Pausa** – Pausa na atual política de replicação associada ao VM selecionado.

- **Retomar** - Pausa na atual política de replicação associada ao VM selecionado.

- **Remover** - Remover a política de replicação atual associada à VM selecionada.

- **Sync Now** – Fora da fonte de sincronização ligada VM para o VM protegido.

Neste guia são abordados os seguintes cenários de replicação:

- Proteja um VM ou um grupo de VMs.

- Complete um Teste Recuperar de um VM ou um grupo de VMs.

- Recupere um VM ou um grupo de VMs.

- Proteção inversa de um VM ou de um grupo de VMs.

## <a name="protect-vms"></a>Proteger VMs

1. Inicie sessão no **vSphere Cliente** no site de origem e aceda **ao plugin HCX**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="Opção HCX em vSphere" border="true":::

1. Introduza a área **de recuperação de desastres** e selecione **PROTECT VMS**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="Opção HCX em vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. Selecione a Fonte e os locais remotos. O site remoto neste caso deve ser a nuvem privada Azure VMware Solution.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="Opção HCX em vSphere" border="true":::

1. Se necessário, selecione as opções **de replicação predefinidos:**

   - **Ativar a compressão:** Recomendado para cenários de baixa produção.

   - **Ativar a Quiescence:** Pausa o VM para garantir que uma cópia consistente é sincronizada com o site remoto.

   - **Armazenamento de destino:** Loja de dados remota para os VMs protegidos, e numa nuvem privada Azure VMware Solution, esta deve ser a loja de dados vSAN.

   - **Recipiente de computação:** Cluster remoto vSphere ou Piscina de Recursos.

   - **Pasta de destino:** A pasta de destino remoto, que é opcional, e se não for selecionada nenhuma pasta, os VMs serão colocados diretamente sob o cluster selecionado.

   - **RPO:** Intervalo de sincronização entre a fonte VM e o VM protegido e pode ser de 5 minutos a 24 horas.

   - **Intervalo instantâneo:** Intervalo entre os instantâneos.

   - **Número de instantâneos:** Número total de instantâneos dentro do intervalo de instantâneo configurado.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="Opção HCX em vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. Selecione um ou mais VMs da lista e configuure as opções de replicação conforme necessário.

   Por predefinição, os VMs herdam a Política de Definições Globais configurada nas opções de replicação padrão. Para cada interface de rede no VM selecionado, configuure o **Grupo porta** de rede remoto e selecione **Finish** para iniciar o processo de proteção.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="Opção HCX em vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. Monitorize o processo para cada um dos VMs selecionados na mesma área de recuperação de desastres.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="Opção HCX em vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. Depois de protegido o VM, pode visualizar as diferentes imagens no **separador Snapshots.**

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="Opção HCX em vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   O triângulo amarelo significa que os instantâneos e o virtual não foram testados numa operação de recuperação de testes.

   Há diferenças fundamentais entre um VM que é desligado e um que é ligado. A imagem mostra o processo de sincronização para um alimentado em VM. Inicia o processo de sincronização até terminar o primeiro instantâneo, que é uma cópia completa do VM, e depois completa os seguintes no intervalo configurado. Para um VM desligado, sincroniza uma cópia e, em seguida, o VM aparece como inativo e a operação de proteção mostra como concluído.  Quando o VM é ligado, inicia o processo de sincronização para o local remoto.

## <a name="complete-a-test-recover-of-vms"></a>Complete um teste de recuperação de VMs

1. Inicie sessão no **vSphere Client** no site remoto, que é a nuvem privada Azure VMware Solution. 
1. Dentro do **plugin HCX**, na área de Recuperação de Desastres, selecione as elipses verticais em qualquer VM para visualizar o menu de operações e, em seguida, selecione **Test Recover VM**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="Opção HCX em vSphere" border="true":::

1. Selecione as opções para o teste e o instantâneo que pretende utilizar para testar diferentes estados do VM.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="Opção HCX em vSphere" border="true":::

1. Após a seleção **do Teste,** inicia-se a operação de recuperação.

1. Quando terminar, pode verificar o novo VM na nuvem privada Azure VMware Solution vCenter.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="Opção HCX em vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. Depois de ter sido efetuado o teste no VM ou em qualquer aplicação em execução, faça uma limpeza para apagar a instância de teste.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="Opção HCX em vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>Recuperar VMs

1. Inicie sessão no **vSphere Client** no site remoto, que é a nuvem privada Azure VMware Solution, e aceda ao **plugin HCX**.

   Para o cenário de recuperação, um grupo de VMs usados para este exemplo.

1. Selecione o VM a recuperar da lista, abra o menu **AÇÕES** e selecione **Recuperar VMs**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="Opção HCX em vSphere" border="true":::

1. Configure as opções de recuperação para cada instância e selecione **Recuperar** para iniciar a operação de recuperação.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="Opção HCX em vSphere" border="true":::

1. Após a conclusão da operação de recuperação, os novos VMs aparecem no inventário remoto do servidor vCenter.

## <a name="complete-a-reverse-replication-on-vms"></a>Complete uma replicação inversa em VMs

1. Inicie sessão no **cliente vSphere** na sua nuvem privada Azure VMware Solution e aceda **ao plugin HCX**.
   
   >[!NOTE]
   >Certifique-se de que os VM originais no local de origem estão desligados antes de iniciar a replicação inversa. A operação falha se os VM não forem desligados.

1. Na lista, selecione os VMs a replicar de volta para o site de origem, abra o menu **AÇÕES** e selecione **Reverso**. 
1. Selecione **Reverso** para iniciar a replicação.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="Opção HCX em vSphere" border="true":::

1. Monitorize na secção de detalhes de cada VM.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="Opção HCX em vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>Automatização de plano de recuperação de desastres

A VMware HCX não tem atualmente um mecanismo incorporado para criar e automatizar um plano de recuperação de desastres. No entanto, o VMware HCX fornece um conjunto de APIs de REST, incluindo APIs para a operação de recuperação de desastres. A especificação API pode ser acedida dentro do VMware HCX Manager no URL.

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

Utilizando estas APIs, um cliente pode construir um mecanismo personalizado para automatizar a criação e execução de um plano de recuperação de desastres.
