---
title: Credenciais de reset vSphere para Solução VMware Azure
description: Saiba como redefinir as credenciais vSphere para a sua nuvem privada Azure VMware Solution e certifique-se de que o conector HCX tem as mais recentes credenciais vSphere.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: 1376b6322250da506d32b8ced0a62ddbf60ba9f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587632"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Credenciais de reset vSphere para Solução VMware Azure

Neste artigo, vamos percorrer os passos para redefinir as credenciais vCenter Server e NSX-T Manager para a sua nuvem privada Azure VMware Solution. Isto permitir-lhe-á garantir que o conector HCX tem as mais recentes credenciais do VCenter Server.

## <a name="reset-your-azure-vmware-solution-credentials"></a>Repor as suas credenciais de Solução VMware Azure

 Primeiro vamos redefinir as suas credenciais de componentes Azure VMare Solution. As suas credenciais de administração vCenter Server CloudAdmin e NSX-T não expiram; no entanto, pode seguir estes passos para gerar novas palavras-passe para estas contas.

> [!NOTE]
> Se utilizar as suas credenciais CloudAdmin para serviços conectados como HCX, vRealize Orchestrator, vRealizae Operations Manager ou VMware Horizon, as suas ligações deixarão de funcionar assim que atualizar a sua palavra-passe.  Estes serviços devem ser interrompidos antes de iniciar a rotação da palavra-passe.  Se não o fizer, poderá resultar em bloqueios temporários nas suas contas de administração do vCenter CloudAdmin e NSX-T, uma vez que estes serviços irão ligar continuamente utilizando as suas credenciais antigas.  Para obter mais informações sobre a criação de contas separadas para serviços [conectados, consulte Os Conceitos de Acesso e Identidade.](https://docs.microsoft.com/azure/azure-vmware/concepts-identity)

1. A partir do portal Azure, abra uma sessão Azure Cloud Shell.

2. Execute o seguinte comando para atualizar a sua palavra-passe vCenter CloudAdmin.  Terá de substituir {SubscriptionID}, {ResourceGroup}, e {PrivateCloudName} pelos valores reais da nuvem privada a que pertence a conta CloudAdmin.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. Execute o seguinte comando para atualizar a sua palavra-passe de administração NSX-T. Terá de substituir {SubscriptionID}, {ResourceGroup}, e {PrivateCloudName} pelos valores reais da nuvem privada a que pertence a conta de administração NSX-T.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vcenter-server-credentials"></a>Certifique-se de que o conector HCX tem as suas mais recentes credenciais vCenter Server

Agora que repôs as suas credenciais, siga estes passos para garantir que o conector HCX tem as suas credenciais atualizadas.

1. Assim que a sua palavra-passe for alterada, aceda à interface web do conector HCX no local utilizando https://{ip do aparelho de conector HCX}:443. Certifique-se de que utiliza a porta 443. Faça login usando as suas novas credenciais.

2. No painel VMware HCX, selecione **O Emparelhamento do Site**.
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Screenshot do Painel HCX VMware com Emparelhamento do Site em destaque.":::
 
3. Selecione a ligação correta à Solução VMware Azure (se houver mais de um) e selecione **Editar A Ligação**.
 
4. Forneça as novas credenciais de utilizador do vCenter Server CloudAdmin e **selecione Editar,** que guarda as credenciais. A poupança deve ser bem sucedida.

## <a name="next-steps"></a>Passos seguintes

Agora que cobriu as credenciais de reset vCenter Server e NSX-T Manager para Azure VMware Solution, talvez queira saber mais sobre:

- [Configurar componentes de rede NSX na Solução VMware Azure](configure-nsx-network-components-azure-portal.md).
- [Gestão do ciclo de vida da Azure VMware Solution VMs](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Implantação de recuperação de máquinas virtuais utilizando a Solução VMware Azure](disaster-recovery-for-virtual-machines.md).
