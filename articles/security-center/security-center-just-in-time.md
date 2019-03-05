---
title: Acesso de máquina de virtual just-in-time no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento demonstra como just-in-time o acesso à VM no Centro de segurança do Azure ajuda a controlar o acesso às suas máquinas virtuais do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: monhaber
ms.openlocfilehash: 9cdcfbc6074f8e343e2571063cc5dafe54072753
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314777"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Gerir o acesso de máquina virtual com just-in-time

Acesso de máquina virtual (VM) just-in-time (JIT) pode ser utilizado para bloquear o tráfego de entrada para as VMs do Azure, reduzindo a exposição a ataques, fornecendo acesso fácil para ligar a VMs quando necessário.

> [!NOTE]
> A funcionalidade de just-in-time está disponível no escalão Standard do Centro de segurança.  Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança.
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="attack-scenario"></a>Cenário de ataque

Força bruta normalmente ataques portas de gestão de destino como um meio para obter acesso a uma VM. Se tiver êxito, um invasor pode assumir o controlo de ativação pós-falha da VM e estabelecer sua posição no seu ambiente.

É uma forma de reduzir a exposição a um ataque de força bruta limitar a quantidade de tempo que uma porta está aberta. As portas de gestão não precisam de estar abertas permanentemente. Apenas têm de estar abertas enquanto estiver ligado à VM, por exemplo, para realizar tarefas de gestão ou manutenção. Quando o just-in-time estiver ativado, o Centro de segurança utiliza [grupo de segurança de rede](../virtual-network/security-overview.md#security-rules) regras (NSG), que restringem o acesso às portas de gestão para que não pode ser visadas pelos atacantes.

![Cenário de just-in-time](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Como funciona o acesso JIT?

Quando o just-in-time estiver ativado, o Centro de segurança bloqueia o tráfego de entrada para as VMs do Azure através da criação de uma regra NSG. Selecione as portas na VM para o qual será bloqueado o tráfego de entrada. Estas portas são controladas mediante a solução just-in-time.

Quando um utilizador solicita acesso a uma VM, o Centro de segurança verifica se o utilizador possui [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/role-assignments-portal.md) permissões que permitem o acesso a uma VM de pedidos com êxito. Se o pedido for aprovado, o Centro de segurança configura automaticamente os grupos de segurança de rede (NSGs) para permitir o tráfego de entrada para as portas selecionadas e endereços IP de origem do pedido ou intervalos, para a quantidade de tempo especificado. Após a expiração do tempo, o Centro de segurança restaurar os NSGs para o respetivo estado anterior. Essas ligações estabelecidas já não sejam a ser interrompidas, no entanto.

> [!NOTE]
> VM de just-in-time do Centro de segurança de acesso atualmente suporta apenas as VMs implementadas através do Gestor de recursos do Azure. Para saber mais sobre o clássico e modelos de implementação do Resource Manager, veja [vs. de implementação clássica do Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

Pode acessar o JIT por meio de:
- [Utilizar o acesso JIT no Centro de segurança do Azure](#jit-asc)
- [Utilizar o acesso JIT num painel de VM do Azure](#jit-vm)

## Utilizar o acesso JIT no Centro de segurança do Azure <a name="jit-asc"></a>

1. Abra o dashboard **Centro de Segurança**.

2. No painel esquerdo, selecione **Just-in-time acesso à VM**.

![Mosaico de acesso VM just-in-time](./media/security-center-just-in-time/just-in-time.png)

O **Just-in-time acesso à VM** é aberta a janela.

![Mosaico de acesso VM just-in-time](./media/security-center-just-in-time/just-in-time-access.png)

**O acesso à VM just-in-time** fornece informações sobre o estado das suas VMs:

- **Configurado** -VMs que tenham sido configuradas para suportar o acesso à VM just-in-time. Os dados apresentados destina-se a última semana e incluem, para cada VM, o número de pedidos aprovados, o data do último acesso e o tempo e o último utilizador.
- **Recomendado** -VMs que podem suportar o acesso à VM just-in-time, mas que não tenham sido configuradas para. Recomendamos que ative o controlo de acesso VM just-in-time para estas VMs. Ver [configurar uma política de acesso just-in-time](#jit-config).
- **Nenhuma recomendação** - As razões que podem fazer com que uma VM não seja recomendada são:
  - NSG em falta - a solução just-in-time requer um NSG implementado.
  - VM clássica - o acesso VM just-in-time de centro de segurança atualmente suporta apenas VMs implementadas através do Gestor de recursos do Azure. Implementação clássica não é suportada pela solução just-in-time.
  - Outro - uma VM está nesta categoria se a solução just-in-time está desativada na política de segurança da subscrição ou o grupo de recursos, ou que a VM está em falta um IP público e não tem um NSG em vigor.

### Configurar uma política de acesso JIT<a name="jit-config"></a>

Para selecionar as VMs que pretende ativar:

1. Sob **Just-in-time acesso à VM**, selecione a **recomendado** separador.

  ![Ativar o acesso just-in-time](./media/security-center-just-in-time/enable-just-in-time-access.png)

2. Sob **máquina VIRTUAL**, selecione as VMs que pretende ativar. Isso coloca uma marca de verificação junto a uma VM.
3. Selecione **ativar JIT em VMs**.
  1. Este painel apresenta as portas predefinidas recomendadas pelo centro de segurança do Azure:
     - 22 - SSH
     - 3389 - RDP
     - 5985 - WinRM 
     - 5986 - WinRM
  2. Também pode configurar portas personalizadas. Para tal, selecione **adicionar**. 
  3. Na **Adicionar configuração da porta**, para cada porta pode optar por configurar, ambos padrão e personalizadas, pode personalizar as seguintes definições:
    - **Tipo de protocolo**-o protocolo que é permitido nesta porta quando um pedido for aprovado.
    - **Endereços IP de origem permitidos**-intervalos de IP que são permitidos nesta porta quando um pedido for aprovado.
    - **Tempo máximo do pedido**-a janela de tempo máximo durante o qual pode ser aberta uma porta específica.

4. Selecione **Guardar**.


> [!NOTE]
>Quando o acesso JIT da VM está ativado para uma VM, o Centro de segurança do Azure cria negar todas as regras de tráfego de entrada para as portas selecionadas nos grupos de segurança de rede associados a ele. As regras será a prioridade dos seus grupos de segurança de rede ou a prioridade mais baixa do que as regras existentes que já existem. Isso depende de uma análise realizada pelo centro de segurança do Azure que determina se uma regra é segura ou não.
>

### <a name="request-jit-access-to-a-vm"></a>Pedido de acesso JIT para uma VM

Para pedir acesso a uma VM:
1.  Sob **Just-in de acesso à VM de tempo**, selecione **configurado**.
2.  Sob **VMs**, verificar as VMs que pretende ativar o acesso just-in-time.
3.  Selecione **pedir acesso**. 
  ![pedir acesso à vm](./media/security-center-just-in-time/request-access-to-a-vm.png)
4.  Sob **pedir acesso**, para cada VM, configure as portas que pretende abrir e os endereços IP de origem que a porta é aberta no e a janela de tempo para que a porta será aberto. Só é possível pedir acesso para as portas que são configuradas na política just-in-time. Cada porta tem um tempo máximo permitido, derivado da política just-in-time.
5.  Selecione **abrir portas**.

> [!NOTE]
> Se um utilizador que está a pedir acesso é atrás de um proxy, a opção **o meu IP** poderá não funcionar. Poderá ter de definir o intervalo de endereços IP completo da organização.

### <a name="editing-a-jit-access-policy"></a>Editar uma política de acesso JIT

Pode alterar a política de just-in-time existente de uma VM ao adicionar e configurar uma nova porta para proteger para essa VM ou ao alterar qualquer outra definição relacionados a uma porta já protegida.

Para editar uma política just-in-time existente de uma VM:
1. Na **configurado** separador, em **VMs**, selecione uma VM ao qual pretende adicionar uma porta ao clicar nos três pontos na linha a essa VM. 
2. Selecione **Editar**.
3. Sob **configuração do acesso JIT da VM**, pode editar as definições existentes de uma porta já protegida ou adicionar uma nova porta personalizada. Para obter mais informações, consulte [configurar uma política de acesso just-in-time](#jit-config). 
  ![acesso JIT da vm](./media/security-center-just-in-time/edit-policy.png)

## Utilizar o acesso JIT num painel de VM do Azure <a name="jit-vm"></a>

Para sua comodidade, pode ligar a uma VM com o JIT diretamente a partir de dentro do painel da VM no Azure.

### <a name="configuring-a-just-in-time-access-policy"></a>Configurar uma política de acesso just-in-time 
Para que seja fácil de implementar o acesso just-in-time nas suas VMs, pode definir uma VM para permitir que apenas just-in-time acedam diretamente a partir de dentro da VM.

1. No portal do Azure, selecione **máquinas virtuais**.
2. Clique na máquina virtual que pretende limitar o acesso just-in-time.
3. No menu, clique em **configuração**.
4. Sob **apenas no tempo de acesso** clique em **ativar política just-in-time**. 

Isto permite o acesso just-in-time para a VM com as seguintes definições:

- Servidores do Windows:
    - Porta RDP 3389
    - 3 horas de acesso permitido máximo
    - Endereços IP de origem permitidos é definido como qualquer
- Servidores Linux:
    - Porta SSH 22
    - 3 horas de acesso permitido máximo
    - Endereços IP de origem permitidos é definido como qualquer
     
Se uma VM já tiver just-in-time ativada, quando vai para a página de configuração será capaz de ver que just-in-time estiver ativado e que pode utilizar a ligação para abrir a política no Centro de segurança do Azure para ver e alterar as definições.

![jit config in vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="requesting-jit-access-to-a-vm"></a>Pedir acesso JIT para uma VM

No portal do Azure, ao tentar ligar a uma VM, Azure verifica se tiver uma política de acesso just-in-time configurada nessa VM.

- Se não tiver configurado numa VM de JIT, será solicitado para configurar uma política JIT-lo.

  ![linha de comandos de JIT](./media/security-center-just-in-time/jit-prompt.png)

- Se tiver uma política JIT configurada na VM, pode clicar **pedir acesso** para que possa ter acesso de acordo com a política JIT definido para a VM.

  ![pedir acesso de JIT](./media/security-center-just-in-time/jit-request-access.png)

## <a name="auditing-jit-access-activity"></a>Auditoria de atividade de acesso JIT

Pode obter informações sobre atividades VM utilizando a pesquisa de registos. Para ver os registos:

1. Sob **Just-in-time acesso à VM**, selecione a **configurado** separador.
2. Sob **VMs**, selecione uma VM para ver informações sobre ao clicar nos três pontos na linha a essa VM. Esta ação abre um menu.
3. Selecione **registo de atividades** no menu. Esta ação abre **registo de atividades**.

  ![Selecione o registo de atividades](./media/security-center-just-in-time/select-activity-log.png)

  **Registo de atividades** fornece uma vista filtrada de operações anteriores para essa VM, juntamente com o tempo, a data e a subscrição.

Pode baixar as informações de registo, selecionando **clique aqui para transferir todos os itens como CSV**.

Modificar os filtros e selecione **aplicar** para criar um registo e de pesquisa.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Permissões necessárias para configurar e utilizar o JIT

Defina esses privilégios necessários para permitir que um utilizador configurar ou editar uma política JIT para uma VM.

Atribuí-las *ações* para a função: 
-   No âmbito de uma subscrição ou grupo de recursos que está associado com a VM:
   - Microsoft.Security/locations/jitNetworkAccessPolicies/write
-    No âmbito de uma subscrição ou grupo de recursos ou VM:
   - Microsoft.Compute/virtualMachines/write 

Defina esses privilégios para permitir que um utilizador com êxito pedir acesso JIT para uma VM: Atribuí-las *ações* ao usuário:
-   No âmbito de uma subscrição ou grupo de recursos que está associado com a VM:
   - Microsoft.Security/locations/{the_location_of_the_VM}/jitNetworkAccessPolicies/ initiate/action
-    No âmbito de uma subscrição ou grupo de recursos ou VM:
   - Microsoft.Compute/virtualMachines/read



## <a name="use-jit-programmatically"></a>Utilizar o JIT por meio de programação
Pode configurar e utilizar just-in-time através de REST APIs e através do PowerShell.

### <a name="using-just-in-time-vm-access-via-rest-apis"></a>Utilizar o acesso VM just-in-time através de REST APIs

A funcionalidade de acesso VM just-in-time pode ser utilizada através da API de centro de segurança do Azure. Pode obter informações sobre VMs configuradas, adicionar novos, pedir acesso a uma VM e mais, por meio desta API. Ver [políticas de acesso de rede Jit](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies), para saber mais sobre a API de REST de just-in-time.

### <a name="using-jit-vm-access-via-powershell"></a>Através do acesso JIT da VM através do PowerShell 

Para utilizar a solução de acesso à VM just-in-time através do PowerShell, utilize os cmdlets do PowerShell do Centro de segurança do Azure oficiais e, especificamente `Set-AzJitNetworkAccessPolicy`.

O exemplo seguinte define uma política de acesso VM just-in-time de uma VM específica e define o seguinte:
1.  Feche as portas 22 e a 3389.
2.  Defina uma janela de tempo máximo de 3 horas para cada, para que o podem ser abertos por pedido aprovado.
3.  Permite que o utilizador que está a pedir acesso para controlar os endereços IP de origem e permite ao utilizador estabelecer uma sessão com êxito após um pedido de acesso de just-in-time aprovados.

Execute o seguinte no PowerShell para fazer isso:

1.  Atribua uma variável que contém a política de acesso VM just-in-time para uma VM:

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  Insira a política de acesso VM do VM just-in-time para uma matriz:
    
        $JitPolicyArr=@($JitPolicy)

3.  Configure a política de acesso VM just-in-time na VM selecionada:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="requesting-access-to-a-vm"></a>Pedir acesso a uma VM

No exemplo a seguir, pode ver um pedido de acesso à VM just-in-time para uma VM específica na qual porta 22 é solicitada para ser aberto para um endereço IP específico e para um período de tempo específico:

Execute o seguinte no PowerShell:
1.  Configurar as propriedades de acesso de pedido VM

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Insira os parâmetros de pedido de acesso VM numa matriz:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Enviar o acesso de pedido (utilize o ID de recurso que obteve no passo 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Para obter mais informações, consulte a documentação de cmdlet do PowerShell.


## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu como just-in-time o acesso à VM no Centro de segurança ajuda a controlar o acesso às suas máquinas virtuais do Azure.

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

- [Definir políticas de segurança](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições do Azure e grupos de recursos.
- [Gerir recomendações de segurança](security-center-recommendations.md) – Saiba como o recomendações o ajudam a proteger os seus recursos do Azure.
- [Monitorização de estado de funcionamento de segurança](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
- [Gerir e responder a alertas de segurança](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Monitorizar soluções de parceiros](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
- [FAQ do Centro de segurança](security-center-faq.md) – encontre as perguntas mais frequentes sobre como utilizar o serviço.
- [Blogue de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

