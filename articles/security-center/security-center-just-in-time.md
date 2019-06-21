---
title: Acesso de máquina de virtual just-in-time no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento demonstra como just-in-time o acesso à VM no Centro de segurança do Azure ajuda a controlar o acesso às suas máquinas virtuais do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 671930b1-fc84-4ae2-bf7c-d34ea37ec5c7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/17/2019
ms.author: v-mohabe
ms.openlocfilehash: eb9366acf82c94bdf99c4d4f0c7c6bdf4f51e06d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295026"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Gerir o acesso de máquina virtual com just-in-time

Acesso de máquina virtual (VM) just-in-time (JIT) pode ser utilizado para bloquear o tráfego de entrada para as VMs do Azure, reduzindo a exposição a ataques, fornecendo acesso fácil para ligar a VMs quando necessário.

> [!NOTE]
> A funcionalidade de just-in-time está disponível no escalão Standard do Centro de segurança.  Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança.


> [!NOTE]
> VM de just-in-time do Centro de segurança de acesso atualmente suporta apenas as VMs implementadas através do Gestor de recursos do Azure. Para saber mais sobre o clássico e modelos de implementação do Resource Manager, veja [vs. de implementação clássica do Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="attack-scenario"></a>Cenário de ataque

Força bruta normalmente ataques portas de gestão de destino como um meio para obter acesso a uma VM. Se tiver êxito, um invasor pode assumir o controlo de ativação pós-falha da VM e estabelecer sua posição no seu ambiente.

É uma forma de reduzir a exposição a um ataque de força bruta limitar a quantidade de tempo que uma porta está aberta. As portas de gestão não precisam de estar abertas permanentemente. Apenas têm de estar abertas enquanto estiver ligado à VM, por exemplo, para realizar tarefas de gestão ou manutenção. Quando o just-in-time estiver ativado, o Centro de segurança utiliza [grupo de segurança de rede](../virtual-network/security-overview.md#security-rules) (NSG) e regras de Firewall do Azure, que restringem o acesso às portas de gestão para que não pode ser visadas pelos atacantes.

![Cenário de just-in-time](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Como funciona o acesso JIT?

Quando o just-in-time estiver ativado, o Centro de segurança bloqueia o tráfego de entrada para as VMs do Azure através da criação de uma regra NSG. Selecione as portas na VM para o qual será bloqueado o tráfego de entrada. Estas portas são controladas mediante a solução just-in-time.

Quando um utilizador solicita acesso a uma VM, o Centro de segurança verifica se o utilizador possui [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/role-assignments-portal.md) permissões que permitem o acesso a uma VM de pedidos com êxito. Se o pedido for aprovado, o Centro de segurança configura automaticamente os grupos de segurança de rede (NSGs) e o Firewall do Azure para permitir tráfego de entrada para as portas selecionadas e endereços IP de origem do pedido ou intervalos, para a quantidade de tempo especificado. Após a expiração do tempo, o Centro de segurança restaurar os NSGs para o respetivo estado anterior. Essas ligações estabelecidas já não sejam a ser interrompidas, no entanto.

 > [!NOTE]
 > Se um pedido de acesso JIT é aprovado para uma VM atrás de uma Firewall do Azure, em seguida, o Centro de segurança altera automaticamente as regras de política do NSG e do firewall. Para a quantidade de tempo em que foi especificado, as regras de permitem tráfego de entrada para as portas selecionadas e endereços IP de origem do pedido ou intervalos. Depois do tempo de terminar, o Centro de segurança restaura o firewall e regras NSG para o respetivo estado anterior.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Permissões necessárias para configurar e utilizar o JIT

| Para permitir que um utilizador: | Permissões para definir|
| --- | --- |
| Configurar ou editar uma política JIT para uma VM | *Atribua estas ações para a função:*  No âmbito de uma subscrição ou grupo de recursos que está associado com a VM: ```Microsoft.Security/locations/jitNetworkAccessPolicies/write``` No âmbito de uma subscrição ou grupo de recursos ou VM: ```Microsoft.Compute/virtualMachines/write``` | 
| ||
|Pedido de acesso JIT para uma VM | *Atribua estas ações ao utilizador:*  No âmbito de uma subscrição ou grupo de recursos que está associado com a VM:  ```Microsoft.Security/locations/{the_location_of_the_VM}/jitNetworkAccessPolicies/initiate/action``` No âmbito de uma subscrição ou grupo de recursos ou VM: ```Microsoft.Compute/virtualMachines/read``` |


## <a name="configure-jit-on-a-vm"></a>Configurar o JIT numa VM

Existem 3 formas de configurar uma política JIT numa VM:

- [Configurar o acesso JIT no Centro de segurança do Azure](#jit-asc)
- [Configurar o acesso JIT no painel de uma VM do Azure](#jit-vm)
- [Configurar uma política JIT numa VM através de programação](#jit-program)

## <a name="configure-jit-in-asc"></a>Configurar o JIT no ASC

ASC, pode configurar um JIT política e pedir o acesso a uma VM com uma política JIT


### Configurar o acesso JIT numa VM no ASC <a name="jit-asc"></a>

1. Abra o dashboard **Centro de Segurança**.

2. No painel esquerdo, selecione **Just-in-time acesso à VM**.

    ![Mosaico de acesso VM just-in-time](./media/security-center-just-in-time/just-in-time.png)

    O **Just-in-time acesso à VM** é aberta a janela.

      ![Ativar o acesso just-in-time](./media/security-center-just-in-time/enable-just-in-time.png)

    **O acesso à VM just-in-time** fornece informações sobre o estado das suas VMs:

    - **Configurado** -VMs que tenham sido configuradas para suportar o acesso à VM just-in-time. Os dados apresentados destina-se a última semana e incluem, para cada VM, o número de pedidos aprovados, o data do último acesso e o tempo e o último utilizador.
    - **Recomendado** -VMs que podem suportar o acesso à VM just-in-time, mas que não tenham sido configuradas para. Recomendamos que ative o controlo de acesso VM just-in-time para estas VMs. 
    - **Nenhuma recomendação** - As razões que podem fazer com que uma VM não seja recomendada são:
      - NSG em falta - a solução just-in-time requer um NSG implementado.
      - VM clássica - o acesso VM just-in-time de centro de segurança atualmente suporta apenas VMs implementadas através do Gestor de recursos do Azure. Implementação clássica não é suportada pela solução just-in-time. 
      - Outro - uma VM está nesta categoria se a solução just-in-time estiver desativada na política de segurança da subscrição ou o grupo de recursos, ou se a VM está em falta um IP público e não tem um NSG em vigor.

3. Selecione o **recomendado** separador.

4. Sob **máquina VIRTUAL**, clique nas VMs que pretende ativar. Isso coloca uma marca de verificação junto a uma VM.

5. Clique em **ativar JIT em VMs**.
   -. Este painel apresenta as portas predefinidas recomendadas pelo centro de segurança do Azure:
      - 22 - SSH
      - 3389 - RDP
      - 5985 - WinRM 
      - 5986 - WinRM
6. Também pode configurar portas personalizadas:

      1. Clique em **Adicionar**. O **Adicionar configuração da porta** é aberta a janela.
      2. Para cada porta, optar por configurar, ambos os padrão e personalizadas, pode personalizar as seguintes definições:

    - **Tipo de protocolo**-o protocolo que é permitido nesta porta quando um pedido for aprovado.
    - **Endereços IP de origem permitidos**-intervalos de IP que são permitidos nesta porta quando um pedido for aprovado.
    - **Tempo máximo do pedido**-a janela de tempo máximo durante o qual pode ser aberta uma porta específica.

     3. Clique em **OK**.

1. Clique em **Guardar**.

> [!NOTE]
>Quando acesso JIT da VM está ativado para uma VM, o Centro de segurança do Azure cria regras "Negar todo o tráfego de entrada" para as portas selecionadas nos grupos de segurança de rede associados e Firewall do Azure com o mesmo. Se outras regras haviam sido criadas para as portas selecionadas, em seguida, as regras existentes têm prioridade sobre as novas regras de acesso "Negar todo o tráfego de entrada". Se não existirem não existem regras existentes nas portas selecionadas, em seguida, as novas regras de acesso "Negar todo o tráfego de entrada" têm prioridade superior nos grupos de segurança de rede e Firewall do Azure.


## <a name="request-jit-access-via-asc"></a>Pedir acesso JIT por meio do ASC

Para pedir acesso a uma VM através do ASC:

1. Sob **Just-in de acesso à VM de tempo**, selecione a **configurado** separador.

2. Sob **Máquina Virtual**, clique nas VMs que pretende pedir acesso para. Isso coloca uma marca de verificação junto a VM.


    - O ícone na **detalhes de ligação** coluna indica se o JIT está ativada no NSG ou firewall. Se estiver ativada em ambos, é apresentada apenas o ícone de Firewall.

    - O **detalhes de ligação** coluna fornece as informações corretas necessárias para ligar a VM, bem como indica as portas abertas.

      ![Pedir acesso just-in-time](./media/security-center-just-in-time/request-just-in-time-access.png)

3. Clique em **pedir acesso**. O **pedir acesso** é aberta a janela.

      ![Detalhes JIT](./media/security-center-just-in-time/just-in-time-details.png)

4. Sob **pedir acesso**, para cada VM, configure as portas que pretende abrir e os endereços IP de origem que a porta é aberta no e a janela de tempo para que a porta será aberto. Só é possível pedir acesso para as portas que são configuradas na política just-in-time. Cada porta tem um tempo máximo permitido, derivado da política just-in-time.

5. Clique em **abrir portas**.

> [!NOTE]
> Se um utilizador que está a pedir acesso é atrás de um proxy, a opção **o meu IP** poderá não funcionar. Poderá ter de definir o intervalo de endereços IP completo da organização.

## <a name="edit-a-jit-access-policy-via-asc"></a>Editar uma política de acesso JIT por meio do ASC

Pode alterar a política de just-in-time existente de uma VM ao adicionar e configurar uma nova porta para proteger para essa VM ou ao alterar qualquer outra definição relacionados a uma porta já protegida.

Para editar uma política just-in-time existente de uma VM:
1. Na **configurado** separador, em **VMs**, selecione uma VM ao qual pretende adicionar uma porta ao clicar nos três pontos na linha a essa VM. 

1. Selecione **Editar**.
1. Sob **configuração do acesso JIT da VM**, pode editar as definições existentes de uma porta já protegida ou adicionar uma nova porta personalizada. 
  ![acesso JIT da vm](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-asc"></a>Auditar a atividade de acesso JIT no ASC

Pode obter informações sobre atividades VM utilizando a pesquisa de registos. Para ver os registos:

1. Sob **Just-in-time acesso à VM**, selecione a **configurado** separador.
2. Sob **VMs**, selecione uma VM para ver informações sobre ao clicar nos três pontos na linha dessa VM e selecione **registo de atividades** no menu. O **registo de atividades** abre.

   ![Selecione o registo de atividades](./media/security-center-just-in-time/select-activity-log.png)

   **Registo de atividades** fornece uma vista filtrada de operações anteriores para essa VM, juntamente com o tempo, a data e a subscrição.

Pode baixar as informações de registo, selecionando **clique aqui para transferir todos os itens como CSV**.

Modificar os filtros e clique em **aplicar** para criar um registo e de pesquisa.



## Configurar o acesso JIT no painel de uma VM do Azure <a name="jit-vm"></a>

Para sua comodidade, pode ligar a uma VM com o JIT diretamente a partir de dentro do painel da VM no Azure.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-blade"></a>Configurar o acesso JIT numa VM através do painel da VM do Azure

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

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>Pedido de acesso JIT para uma VM através do painel da VM do Azure

No portal do Azure, ao tentar ligar a uma VM, Azure verifica se tiver uma política de acesso just-in-time configurada nessa VM.

- Se tiver uma política JIT configurada na VM, pode clicar **pedir acesso** para que possa ter acesso de acordo com a política JIT definido para a VM. 

  >![pedido de JIT](./media/security-center-just-in-time/jit-request.png)

  O acesso for pedido com os seguintes parâmetros de predefinição:

  - **IP de origem**: 'Any' (*) (não é possível alterar)
  - **intervalo de tempo**: 3 horas (não é possível alterar)  <!--Isn't this set in the policy-->
  - **número de porta** porta 22 para Linux (pode ser alterado) / porta 3389 para Windows RDP

    > [!NOTE]
    > Depois de um pedido é aprovado para uma VM protegida pelo Firewall do Azure, o Centro de segurança fornece ao utilizador com os detalhes de ligação adequado (o mapeamento de portas da tabela DNAT) a utilizar para ligar à VM.

- Se não tiver configurado numa VM de JIT, será solicitado para configurar uma política JIT-lo.

  ![linha de comandos de JIT](./media/security-center-just-in-time/jit-prompt.png)

## Configurar uma política JIT numa VM através de programação  <a name="jit-program"></a>

Pode configurar e utilizar just-in-time através de REST APIs e através do PowerShell.

## <a name="jit-vm-access-via-rest-apis"></a>Acesso JIT da VM através de REST APIs

A funcionalidade de acesso VM just-in-time pode ser utilizada através da API de centro de segurança do Azure. Pode obter informações sobre VMs configuradas, adicionar novos, pedir acesso a uma VM e mais, por meio desta API. Ver [políticas de acesso de rede Jit](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies), para saber mais sobre a API de REST de just-in-time.

## <a name="jit-vm-access-via-powershell"></a>Acesso JIT da VM através do PowerShell

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

#### <a name="request-access-to-a-vm-via-powershell"></a>Pedir acesso a uma VM através do PowerShell

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

