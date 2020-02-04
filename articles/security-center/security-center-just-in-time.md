---
title: Acesso à máquina virtual just-in-time na central de segurança do Azure | Microsoft Docs
description: Este documento demonstra como o acesso just-in-time à VM na central de segurança do Azure ajuda você a controlar o acesso às máquinas virtuais do Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 69a5452134e290f2072a9316ce1f067296ed2320
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939398"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Gerenciar o acesso à máquina virtual usando just-in-time

O acesso à máquina virtual (VM) just-in-time (VM) pode ser utilizado para bloquear o tráfego de entrada para os seus VMs Azure, reduzindo a exposição a ataques, proporcionando um fácil acesso à ligação aos VMs quando necessário.

> [!NOTE]
> O recurso just-in-time está disponível na camada Standard da central de segurança. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança.


> [!NOTE]
> O acesso à VM just-in-time da central de segurança atualmente dá suporte apenas a VMs implantadas por meio de Azure Resource Manager. Para saber mais sobre os modelos de implantação clássico e do Resource Manager, consulte [Azure Resource Manager vs. implantação clássica](../azure-resource-manager/management/deployment-models.md).

## <a name="attack-scenario"></a>Cenário de ataque

Ataques de força bruta normalmente direcionam as portas de gerenciamento como um meio de obter acesso a uma VM. Se for bem-sucedido, um invasor poderá assumir o controle da VM e estabelecer um destaque em seu ambiente.

Uma maneira de reduzir a exposição a um ataque de força bruta é limitar a quantidade de tempo que uma porta está aberta. As portas de gerenciamento não precisam ser abertas o tempo todo. Eles só precisam ser abertos enquanto você estiver conectado à VM, por exemplo, para executar tarefas de gerenciamento ou manutenção. Quando o just-in-time está habilitado, a central de segurança usa o NSG ( [grupo de segurança de rede](../virtual-network/security-overview.md#security-rules) ) e as regras de firewall do Azure, que restringem o acesso às portas de gerenciamento para que não possam ser direcionadas por invasores

![Cenário just-in-time](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Como funciona o acesso JIT?

Quando o just-in-time está habilitado, a central de segurança bloqueia o tráfego de entrada para suas VMs do Azure criando uma regra NSG. Você seleciona as portas na VM para a qual o tráfego de entrada será bloqueado. Essas portas são controladas pela solução just-in-time.

Quando um usuário solicita acesso a uma VM, a central de segurança verifica se o usuário tem permissões de [RBAC (controle de acesso baseado em função)](../role-based-access-control/role-assignments-portal.md) para essa VM. Se a solicitação for aprovada, a central de segurança configurará automaticamente os NSGs (grupos de segurança de rede) e o Firewall do Azure para permitir o tráfego de entrada para as portas selecionadas e os endereços ou intervalos de IP de origem solicitados, para o período de tempo especificado. Depois que o tempo expirou, a central de segurança restaura o NSGs para seus Estados anteriores. No entanto, essas conexões já estabelecidas não estão sendo interrompidas.

 > [!NOTE]
 > Se uma solicitação de acesso JIT for aprovada para uma VM por trás de um firewall do Azure, a central de segurança alterará automaticamente as regras de política de firewall e NSG. Para o período de tempo especificado, as regras permitem o tráfego de entrada para as portas selecionadas e os endereços ou intervalos IP de origem solicitados. Após o tempo, a central de segurança restaura as regras de firewall e NSG para seus Estados anteriores.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Permissões necessárias para configurar e usar o JIT

| Para permitir que um usuário: | Permissões a serem definidas|
| --- | --- |
| Configurar ou editar uma política JIT para uma VM | *Atribua estas ações à função:*  <ul><li>No escopo de uma assinatura ou grupo de recursos associado à VM:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> No âmbito de uma subscrição ou grupo de recursos de VM: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Solicitar acesso JIT a uma VM | *Atribua estas ações ao usuário:*  <ul><li>No escopo de uma assinatura ou grupo de recursos associado à VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>No escopo de uma assinatura ou grupo de recursos associado à VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  No escopo de uma assinatura ou grupo de recursos ou VM:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  No escopo de uma assinatura ou grupo de recursos ou VM:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|


## <a name="configure-jit-on-a-vm"></a>Configurar o JIT em uma VM

Há três maneiras de configurar uma política JIT em uma VM:

- [Configurar o acesso JIT na central de segurança do Azure](#jit-asc)
- [Configurar o acesso JIT em uma folha de VM do Azure](#jit-vm)
- [Configurar uma política JIT em uma VM programaticamente](#jit-program)

## <a name="configure-jit-in-security-center"></a>Configurar o JIT na central de segurança

Na central de segurança, você pode configurar uma política JIT e solicitar o acesso a uma VM usando uma política JIT


### Configurar o acesso JIT em uma VM na central de segurança<a name="jit-asc"></a>

1. Abra o dashboard **Centro de Segurança**.

2. No painel esquerdo, selecione **acesso à VM just-in-time**.

    ![Bloco de acesso à VM just-in-time](./media/security-center-just-in-time/just-in-time.png)

    A janela de **acesso da VM just-in-time** é aberta.

      ![Habilitar o acesso just-in-time](./media/security-center-just-in-time/enable-just-in-time.png)

    O **acesso just-in-time à VM** fornece informações sobre o estado de suas VMs:

    - **Configuradas** -VMs que foram configuradas para dar suporte ao acesso de VM just-in-time. Os dados apresentados são para a última semana e incluem para cada VM o número de solicitações aprovadas, a data e hora do último acesso e o último usuário.
    - **Recomendado** – VMs que podem dar suporte ao acesso à VM just-in-time, mas não foram configuradas para. Recomendamos que você habilite o controle de acesso da VM just-in-time para essas VMs.
    - **Nenhuma recomendação** - As razões que podem fazer com que uma VM não seja recomendada são:
      - NSG ausente-a solução just-in-time exige que um NSG esteja em vigor.
      - VM clássica-o acesso à VM just-in-time da central de segurança atualmente dá suporte apenas a VMs implantadas por meio de Azure Resource Manager. Uma implantação clássica não é suportada pela solução just-in-time. 
      - Outro – uma VM estará nessa categoria se a solução just-in-time estiver desativada na política de segurança da assinatura ou no grupo de recursos, ou se a VM não tiver um IP público e não tiver um NSG em vigor.

3. Selecione a guia **recomendado** .

4. Em **máquina virtual**, clique nas VMs que você deseja habilitar. Isso coloca uma marca de seleção ao lado de uma VM.

5. Clique em **habilitar JIT em VMs**.
   -. Esta folha exibe as portas padrão recomendadas pela central de segurança do Azure:
      - 22-SSH
      - 3389-RDP
      - 5985-WinRM 
      - 5986-WinRM
6. Você também pode configurar portas personalizadas:

      1. Clique em **Adicionar**. A janela **Adicionar configuração de porta** é aberta.
      2. Para cada porta que você optar por configurar, tanto padrão quanto personalizada, você pode personalizar as seguintes configurações:

    - **Tipo de protocolo**– o protocolo que é permitido nessa porta quando uma solicitação é aprovada.
    - **Endereços IP de origem permitidos**-os intervalos de IP que são permitidos nesta porta quando uma solicitação é aprovada.
    - **Tempo máximo de solicitação**-a janela de tempo máxima durante a qual uma porta específica pode ser aberta.

     3. Clique em **OK**.

1. Clique em **Guardar**.

> [!NOTE]
>Quando o acesso à VM JIT está habilitado para uma VM, a central de segurança do Azure cria as regras "negar todo o tráfego de entrada" para as portas selecionadas nos grupos de segurança de rede associados e no firewall do Azure com ele. Se outras regras tiverem sido criadas para as portas selecionadas, as regras existentes têm prioridade sobre as novas regras "negar todo o tráfego de entrada". Se não houver nenhuma regra existente nas portas selecionadas, a nova regra "negar todo o tráfego de entrada" assumirá a prioridade mais alta nos grupos de segurança de rede e no firewall do Azure.


## <a name="request-jit-access-via-security-center"></a>Solicitar acesso JIT por meio da central de segurança

Para solicitar acesso a uma VM por meio da central de segurança:

1. Em **acesso à VM just-in-time**, selecione a guia **configurada** .

2. Em **máquina virtual**, clique nas VMs às quais você deseja solicitar acesso. Isso coloca uma marca de seleção ao lado da VM.


    - O ícone na coluna **detalhes da conexão** indica se o JIT está habilitado no NSG ou no FW. Se ele estiver habilitado em ambos, somente o ícone de firewall será exibido.

    - A coluna **detalhes da conexão** fornece as informações necessárias para conectar a VM e suas portas abertas.

      ![Pedir acesso just-in-time](./media/security-center-just-in-time/request-just-in-time-access.png)

3. Clique em **solicitar acesso**. A janela **solicitar acesso** é aberta.

      ![Detalhes do JIT](./media/security-center-just-in-time/just-in-time-details.png)

4. Em **solicitar acesso**, para cada VM, configure as portas que você deseja abrir e os endereços IP de origem nos quais a porta está aberta e a janela de tempo para a qual a porta será aberta. Só será possível solicitar acesso às portas configuradas na política just-in-time. Cada porta tem um tempo máximo permitido derivado da política just-in-time.

5. Clique em **abrir portas**.

> [!NOTE]
> Se um usuário que está solicitando acesso estiver protegido por um proxy, a opção **meu IP** poderá não funcionar. Talvez seja necessário definir o intervalo de endereços IP completo da organização.

## <a name="edit-a-jit-access-policy-via-security-center"></a>Editar uma política de acesso JIT por meio da central de segurança

Você pode alterar a política just-in-time existente de uma VM adicionando e configurando uma nova porta para proteger essa VM ou alterando qualquer outra configuração relacionada a uma porta já protegida.

Para editar uma política just-in-time existente de uma VM:
1. Na guia **configurado** , em **VMs**, selecione uma VM à qual adicionar uma porta clicando nos três pontos dentro da linha dessa VM. 

1. Selecione **Editar**.
1. Em **configuração de acesso à VM JIT**, você pode editar as configurações existentes de uma porta já protegida ou adicionar uma nova porta personalizada. 
  ![jit vm acesso](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-security-center"></a>Auditar atividade de acesso JIT na central de segurança

Você pode obter informações sobre as atividades de VM usando a pesquisa de logs. Para exibir os logs:

1. Em **acesso à VM just-in-time**, selecione a guia **configurada** .
2. Em **VMs**, selecione uma VM para exibir informações sobre como clicar nos três pontos dentro da linha dessa VM e selecione log de **atividades** no menu. O **log de atividades** é aberto.

   ![Selecionar log de atividades](./media/security-center-just-in-time/select-activity-log.png)

   O **log de atividades** fornece uma exibição filtrada das operações anteriores para essa VM junto com a hora, a data e a assinatura.

Você pode baixar as informações de log selecionando **clique aqui para baixar todos os itens como CSV**.

Modifique os filtros e clique em **aplicar** para criar uma pesquisa e um log.



## Configurar o acesso JIT na página de uma VM do Azure<a name="jit-vm"></a>

Para sua conveniência, você pode se conectar a uma VM usando o JIT diretamente de dentro da página da VM na central de segurança.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Configurar o acesso JIT em uma VM por meio da página VM do Azure

Para facilitar a distribuição do acesso just-in-time em suas VMs, você pode definir uma VM para permitir apenas o acesso just-in-time diretamente de dentro da VM.

1. No [portal do Azure](https://ms.portal.azure.com), procure e selecione **máquinas virtuais**. 
2. Selecione a máquina virtual que você deseja limitar ao acesso just-in-time.
3. No menu, selecione **configuração**.
4. Em **acesso just-in-time**, selecione **habilitar política just-in-time**. 

Isso habilita o acesso just-in-time para a VM usando as seguintes configurações:

- Servidores Windows:
    - Porta RDP 3389
    - Três horas de acesso máximo permitido
    - Os endereços IP de origem permitidos estão definidos como qualquer
- Servidores Linux:
    - Porta SSH 22
    - Três horas de acesso máximo permitido
    - Os endereços IP de origem permitidos estão definidos como qualquer
     
Se uma VM já tiver o just-in-time habilitado, ao ir para sua página de configuração, você poderá ver que o just-in-time está habilitado e pode usar o link para abrir a política na central de segurança do Azure para exibir e alterar as configurações.

![configuração JIT na VM](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>Solicitar acesso JIT a uma VM por meio da folha de VM do Azure

No portal do Azure, quando você tenta se conectar a uma VM, o Azure verifica se você tem uma política de acesso just-in-time configurada nessa VM.

- Se você tiver uma política JIT configurada na VM, poderá clicar em **solicitar acesso** para permitir que você tenha acesso de acordo com a política JIT definida para a VM. 

  >![solicitação JIT](./media/security-center-just-in-time/jit-request.png)

  O acesso é solicitado com os seguintes parâmetros padrão:

  - **IP de origem**: ' any ' (*) (não pode ser alterado)
  - **intervalo de tempo**: três horas (não pode ser alterado) <!--Isn't this set in the policy-->
  - **número da porta** Porta RDP 3389 para Windows/porta 22 para Linux (pode ser alterada)

    > [!NOTE]
    > Depois que uma solicitação for aprovada para uma VM protegida pelo firewall do Azure, a central de segurança fornecerá ao usuário os detalhes de conexão apropriados (o mapeamento de porta da tabela DNAT) a ser usada para se conectar à VM.

- Se você não tiver o JIT configurado em uma VM, será solicitado que você configure uma política JIT.

  ![prompt do JIT](./media/security-center-just-in-time/jit-prompt.png)

## Configurar uma política JIT em uma VM programaticamente<a name="jit-program"></a>

Você pode configurar e usar o just-in-time por meio de APIs REST e por meio do PowerShell.

## <a name="jit-vm-access-via-rest-apis"></a>Acesso à VM JIT por meio de APIs REST

O recurso de acesso da VM just-in-time pode ser usado por meio da API da central de segurança do Azure. Você pode obter informações sobre as VMs configuradas, adicionar novas, solicitar acesso a uma VM e muito mais, por meio dessa API. Consulte [políticas de acesso à rede JIT](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)para saber mais sobre a API REST just-in-time.

## <a name="jit-vm-access-via-powershell"></a>Acesso à VM JIT por meio do PowerShell

Para usar a solução de acesso da VM just-in-time por meio do PowerShell, use os cmdlets do PowerShell da central de segurança do Azure oficiais e, especificamente, `Set-AzJitNetworkAccessPolicy`.

O exemplo a seguir define uma política de acesso de VM just-in-time em uma VM específica e define o seguinte:

1.  Feche as portas 22 e 3389.

2.  Defina uma janela de tempo máxima de 3 horas para cada para que elas possam ser abertas por solicitação aprovada.
3.  Permite que o usuário que está solicitando acesso controle os endereços IP de origem e permite que o usuário estabeleça uma sessão bem-sucedida após uma solicitação de acesso just-in-time aprovada.

Execute o seguinte no PowerShell para fazer isso:

1.  Atribua uma variável que mantém a política de acesso da VM just-in-time para uma VM:

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

2.  Insira a política de acesso à VM just-in-time da VM em uma matriz:
    
        $JitPolicyArr=@($JitPolicy)

3.  Configure a política de acesso da VM just-in-time na VM selecionada:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="request-access-to-a-vm-via-powershell"></a>Solicitar acesso a uma VM por meio do PowerShell

No exemplo a seguir, você pode ver uma solicitação de acesso à VM just-in-time para uma VM específica na qual a porta 22 é solicitada para ser aberta para um endereço IP específico e por um período de tempo específico:

Execute o seguinte no PowerShell:
1.  Configurar as propriedades de acesso à solicitação da VM

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Insira os parâmetros de solicitação de acesso à VM em uma matriz:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Enviar o acesso de solicitação (use a ID do recurso que você obteve na etapa 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Para obter mais informações, consulte a documentação do cmdlet do PowerShell.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, você aprendeu como o acesso just-in-time à VM na central de segurança ajuda a controlar o acesso às máquinas virtuais do Azure.

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

- [Configurando políticas de segurança](tutorial-security-policy.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciando recomendações de segurança](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Monitoramento da integridade de segurança](security-center-monitoring.md) — saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciando e respondendo a alertas de segurança](security-center-managing-and-responding-alerts.md) – saiba como gerenciar e responder a alertas de segurança.
- [Monitorando soluções de parceiros](security-center-partner-solutions.md) — saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes da central de segurança](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço.
- [Blogue de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

