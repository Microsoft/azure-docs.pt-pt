---
title: Iniciar a ligação da máquina virtual - Azure
description: Como configurar a máquina virtual de arranque na função de ligação.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 17f2b11544b05cd2a7105a1c9be5f5bd28d3edbd
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080501"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Iniciar a máquina virtual na ligação (pré-visualização)

> [!IMPORTANT]
> A funcionalidade Start VM on Connect encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A funcionalidade Start Virtual Machine (VM) on Connect (pré-visualização) permite-lhe economizar custos, permitindo-lhe negociar os seus VMs quando não os estiver a utilizar. Quando precisar de voltar a utilizar o VM, tudo o que tem de fazer é voltar a ligar os VM.

>[!NOTE]
>O Windows Virtual Desktop (clássico) não suporta esta funcionalidade.

## <a name="requirements-and-limitations"></a>Requisitos e limitações

Só é possível ativar a função Iniciar VM no Connect para piscinas de anfitriões pessoais. Para saber mais sobre piscinas de anfitriões pessoais, consulte [o ambiente de ambiente de trabalho virtual do Windows.](environment-setup.md#host-pools)

Os seguintes clientes remotos de desktop suportam a função Start VM on Connect:

- [O cliente web](connect-web.md)
- [O cliente Windows (versão 1.2748 ou posterior)](connect-windows-7-10.md)

Pode consultar anúncios sobre atualizações e apoio ao cliente no [fórum da Comunidade Tecnológica.](https://aka.ms/wvdtc)

>[!IMPORTANT]
>A função Start VM on Connect suporta atualmente apenas a PowerShell e a REST API, e não o portal Azure. Para mais informações, consulte [Criar ou atualizar uma piscina de anfitriões.](/rest/api/desktopvirtualization/hostpools/createorupdate)

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Crie uma função personalizada para iniciar vM no Connect

Antes de configurar a função Iniciar VM em Connect, terá de atribuir ao seu VM uma função personalizada de RBAC (controlo de acesso baseado em funções). Esta função permitirá ao Windows Virtual Desktop gerir os VMs na sua subscrição. Também pode usar esta função para ligar VMs, verificar o seu estado e reportar informações de diagnóstico. Se quiser saber mais sobre o que cada papel faz, dê uma olhada nos [papéis personalizados do Azure.](../role-based-access-control/custom-roles.md)

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Para utilizar o portal Azure para atribuir uma função personalizada para iniciar vM no Connect:

1. Abra o portal Azure e vá para **Subscrições**.

2. Vá ao **controlo de Acesso (IAM)** e selecione **Adicione uma função personalizada.**

    > [!div class="mx-imgBorder"]
    > ![Uma imagem de um menu suspenso a partir do botão Adicionar no controlo de acesso (IAM). "Adicionar um papel personalizado" é destacado a vermelho.](media/add-custom-role.png)

3. Em seguida, nomeie o papel personalizado e adicione uma descrição. Recomendamos que lhe dê o nome de "iniciar vM na ligação".

4. No separador **Permissões,** adicione as seguintes permissões à subscrição a que atribui a função a: 
 
   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/ler

5. Quando terminar, selecione **Ok**.

Depois disso, terá de atribuir a função para conceder acesso ao Windows Virtual Desktop.

Para atribuir o papel personalizado:

1. No **separador Controlo de Acesso (IAM),** **selecione adicionar atribuições de funções**.

2. Selecione o papel que acabou de criar.

3. Na barra de pesquisa, insira e selecione **o Windows Virtual Desktop**.

      >[!NOTE]
      >Pode ver duas aplicações se tiver implementado o Windows Virtual Desktop (clássico). Atribua o papel a ambas as aplicações que vê.
      >
      > [!div class="mx-imgBorder"]
      > ![Uma imagem do separador Access Control (IAM). Na barra de pesquisa, tanto o Windows Virtual Desktop como o Windows Virtual Desktop (clássico) são destacados a vermelho.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Crie um papel personalizado com um modelo de arquivo JSON

Se estiver a utilizar um ficheiro JSON para criar o papel personalizado, o exemplo a seguir mostra um modelo básico que pode utilizar. Certifique-se de que substitui o valor de ID de subscrição pelo ID de subscrição a que pretende atribuir a função.

```json
{
    "properties": {
        "roleName": "start VM on connect",
        "description": "Friendly description.",
        "assignableScopes": [
            "/subscriptions/<SubscriptionID>"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/virtualMachines/start/action",
                    "Microsoft.Compute/virtualMachines/read"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

## <a name="configure-the-start-vm-on-connect-feature"></a>Configure a função Iniciar VM no Connect

Agora que atribuiu a sua subscrição o papel, está na hora de configurar a funcionalidade Iniciar VM no Connect!

### <a name="deployment-considerations"></a>Considerações sobre implementação 

Iniciar VM no Connect é uma definição de piscina hospedeira. Se pretender apenas que um grupo selecionado de utilizadores utilize esta funcionalidade, certifique-se de que apenas atribui a função necessária aos utilizadores que pretende adicionar.

>[!IMPORTANT]
> Você só pode configurar esta funcionalidade em piscinas de anfitriões existentes. Esta funcionalidade não está disponível quando cria uma nova piscina de anfitriões.

### <a name="use-powershell"></a>Utilizar o PowerShell

Para configurar esta definição com o PowerShell, tem de se certificar de que tem os nomes do grupo de recursos e das piscinas hospedeiras que pretende configurar. Também terá de instalar [o módulo Azure PowerShell (versão 2.1.0 ou posterior)](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0).

Para configurar iniciar vm em ligar usando PowerShell:

1. Abra uma janela de comando PowerShell.

2. Executar o seguinte cmdlet para ativar iniciar vm em ligar:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Executar o seguinte cmdlet para desativar iniciar vm em ligar:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Experiência do utilizador

Nas sessões típicas, o tempo que um utilizador leva para se ligar a um VM deallocated aumenta porque o VM precisa de tempo para ligar novamente, tal como ligar um computador físico. O cliente Remote Desktop tem um indicador que permite ao utilizador saber que o PC está a ser ligado enquanto está a ser ligado.

## <a name="troubleshooting"></a>Resolução de problemas

Se a funcionalidade tiver problemas, recomendamos que utilize a funcionalidade de [diagnóstico](diagnostics-log-analytics.md) virtual do Windows Desktop para verificar se há problemas. Se receber uma mensagem de erro, certifique-se de que presta muita atenção ao conteúdo da mensagem e copie o nome de erro algures para referência.

Também pode utilizar [o Azure Monitor para o Windows Virtual Desktop](azure-monitor.md) para obter sugestões de como resolver problemas.

## <a name="next-steps"></a>Passos seguintes

Se encontrar algum problema que a documentação de resolução de problemas ou a funcionalidade de diagnóstico não conseguisse resolver, consulte o [VM start on Connect FAQ](start-virtual-machine-connect-faq.md).