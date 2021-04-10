---
title: Acesso a máquinas virtuais just-in-time no Azure Security Center | Microsoft Docs
description: Este documento demonstra como o acesso ao VM (JIT) no Azure Security Center ajuda a controlar o acesso às suas máquinas virtuais Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 60ae36d80e34f27ed68c679f47edacf3e402417c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98916155"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Proteja as suas portas de gestão com acesso just-in-time

Bloqueie o tráfego de entrada para as suas Máquinas Virtuais Azure com a função de acesso à máquina virtual (JIT) do Azure Security Center. Isto reduz a exposição a ataques, proporcionando fácil acesso quando precisa de se ligar a um VM.

Para uma explicação completa sobre como funciona o JIT e a lógica subjacente, consulte [Just-in-time explicado](just-in-time-explained.md).

Esta página ensina-lhe como incluir o JIT no seu programa de segurança. Vai aprender a: 

- **Ativar JIT nos seus VMs** - Pode ativar o JIT com as suas próprias opções personalizadas para um ou mais VMs utilizando o Security Center, PowerShell ou a API REST. Em alternativa, pode ativar o JIT com parâmetros padrão e codificados, a partir de máquinas virtuais Azure. Quando ativado, o JIT bloqueia o tráfego de entrada para os seus VMs Azure, criando uma regra no seu grupo de segurança de rede.
- **Solicitar acesso a um VM que tenha JIT ativado** - O objetivo do JIT é garantir que, mesmo que o seu tráfego de entrada esteja bloqueado, o Centro de Segurança ainda proporciona fácil acesso à ligação aos VM quando necessário. Pode solicitar acesso a um VM ativado pelo JIT a partir do Security Center, máquinas virtuais Azure, PowerShell ou a API REST.
- **Auditar a atividade** - Para garantir que os seus VMs são protegidos adequadamente, reveja os acessos aos seus VMs ativados pelo JIT como parte das suas verificações de segurança regulares.   



## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Disponibilidade Geral (GA)|
|Preços:|Requer [Azure Defender para servidores](defender-for-servers-introduction.md)|
|VMs suportados:|![Sim ](./media/icons/yes-icon.png) VMs implantados através do Azure Resource Manager.<br>![Não há ](./media/icons/no-icon.png) VMs implantados com modelos clássicos de implantação. [Saiba mais sobre estes modelos de implantação.](../azure-resource-manager/management/deployment-models.md)<br>![Sem ](./media/icons/no-icon.png) VMs protegidos por Firewalls Azure controlados pelo [Azure Firewall Manager](../firewall-manager/overview.md)|
|Funções e permissões necessárias:|**As** funções reader e **SecurityReader** podem visualizar o estado e os parâmetros do JIT.<br>Para criar funções personalizadas que possam funcionar com o JIT, veja [quais as permissões necessárias para configurar e utilizar o JIT?](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit)<br>Para criar um papel menos privilegiado para os utilizadores que precisam solicitar o acesso do JIT a um VM, e não realizar outras operações JIT, use o [script Set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) a partir das páginas comunitárias do Centro de Segurança GitHub.|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![Yes](./media/icons/yes-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||


## <a name="enable-jit-vm-access"></a>Permitir o acesso ao JIT VM <a name="jit-configure"></a>

Pode ativar o acesso ao JIT VM com as suas próprias opções personalizadas para um ou mais VMs utilizando o Security Center ou programaticamente. 

Em alternativa, pode ativar o JIT com parâmetros padrão e codificados, a partir de máquinas Virtuais Azure.

Cada uma destas opções é explicada num separado abaixo.

### <a name="azure-security-center"></a>[**Centro de Segurança do Azure**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>Ativar JIT nos seus VMs do Azure Security Center <a name="jit-asc"></a>

:::image type="content" source="./media/security-center-just-in-time/jit-config-security-center.gif" alt-text="Configurar o acesso jit VM no Centro de Segurança Azure":::

A partir do Centro de Segurança, pode ativar e configurar o acesso JIT VM.

1. Abra o painel de instrumentos Azure Defender e a partir da área de proteção avançada, selecione **acesso VM just-in-time**.

    A página **de acesso VM just-in-time** abre com os seus VMs agrupados nos seguintes separadores:

    - **Configurado** - VMs que já foram configurados para suportar o acesso em VM just-in-time. Para cada VM, o separador configurado mostra:
        - o número de pedidos aprovados do JIT nos últimos sete dias
        - a última data e hora de acesso
        - os detalhes de ligação configurados
        - o último utilizador
    - **Não configurado** - VMs sem JIT habilitado, mas que pode suportar JIT. Recomendamos que ative o JIT para estes VMs.
    - **Sem suporte** - VMs sem JIT ativado e que não suportam a funcionalidade. O seu VM pode estar neste separador pelas seguintes razões:
      - Grupo de segurança da rede em falta (NSG) - JIT exige que um NSG seja configurado
      - Classic VM - JIT suporta VMs que são implantados através do Azure Resource Manager, e não 'implantação clássica'. [Saiba mais sobre os modelos clássicos vs Azure Resource Manager.](../azure-resource-manager/management/deployment-models.md)
      - Outros - O seu VM pode estar neste separador se a solução JIT for desativada na política de segurança da subscrição ou do grupo de recursos.

1. A partir do separador **Não configurado,** marque os VMs para proteger com JIT e selecione **Ative JIT em VMs**. 

    A página de acesso JIT VM abre a listagem das portas que o Centro de Segurança recomenda proteger:
    - 22 - SSH
    - 3389 - PDR
    - 5985 - WinrM 
    - 5986 - WinrM

    Para aceitar as definições predefinidos, **selecione Guardar**.

1. Para personalizar as opções JIT:

    - Adicione portas personalizadas com o botão **Adicionar.** 
    - Modifique uma das portas predefinitivas, selecionando-a da lista.

    Para cada porta (personalizada e padrão) o painel **de configuração** da porta Add oferece as seguintes opções:

    - **Protocolo**- O protocolo que é permitido nesta porta quando um pedido é aprovado
    - **IPs de origem permitida**- As gamas IP que são permitidas nesta porta quando um pedido é aprovado
    - **Tempo máximo de pedido**- O período máximo durante o qual uma porta específica pode ser aberta

     1. Desaça a segurança do porto às suas necessidades.

     1. Selecione **OK**.

1. Selecione **Guardar**.



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>Editar a configuração JIT num VM ativado pelo JIT utilizando o Security Center <a name="jit-modify"></a>

Pode modificar a configuração just-in-time de um VM adicionando e configurando uma nova porta para proteger para esse VM, ou alterando qualquer outra definição relacionada com uma porta já protegida.

Para editar as regras jit existentes para um VM:

1. Abra o painel de instrumentos Azure Defender e a partir da área de proteção avançada, selecione **comandos de aplicação adaptáveis**.

1. A partir do separador **Configurado,** clique à direita no VM ao qual pretende adicionar uma porta e selecione editar. 

    ![Edição de uma configuração de acesso JIT VM no Centro de Segurança Azure](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. Na **configuração de acesso JIT VM,** pode editar as definições existentes de uma porta já protegida ou adicionar uma nova porta personalizada.

1. Quando terminar de editar as portas, **selecione Save**.
 


### <a name="azure-virtual-machines"></a>[**Máquinas virtuais Azure**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>Ativar JIT nos seus VMs a partir de máquinas virtuais Azure

Pode ativar o JIT num VM a partir das páginas de máquinas virtuais Azure do portal Azure.

![Configurar o acesso jit VM em máquinas virtuais Azure](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> Se um VM já tiver um tempo próprio ativado, quando for à sua página de configuração verá que o just-in-time está ativado e pode utilizar o link para abrir a página de acesso VM just-in-time no Centro de Segurança e visualizar e alterar as definições.

1. A partir do [portal Azure,](https://ms.portal.azure.com)procure e selecione **máquinas Virtuais.** 

1. Selecione a máquina virtual que pretende proteger com JIT.

1. No menu, selecione **Configuração**.

1. No **acesso just-in-time**, selecione **Ative just-in-time**. 

    Isto permite o acesso just-in-time para o VM utilizando as seguintes definições predefinidos:

    - Máquinas windows:
        - Porta RDP 3389
        - Três horas de acesso máximo permitido
        - Os endereços IP de origem permitida estão definidos para Qualquer
    - Máquinas Linux:
        - Porta SSH 22
        - Três horas de acesso máximo permitido
        - Os endereços IP de origem permitida estão definidos para Qualquer

1. Para editar qualquer um destes valores, ou adicionar mais portas à sua configuração JIT, utilize a página do Azure Security Center just-in-time:

    1. A partir do menu do Security Center, selecione **acesso VM just-in-time**.

    1. A partir do separador **Configurado,** clique à direita no VM ao qual pretende adicionar uma porta e selecione editar. 

        ![Edição de uma configuração de acesso JIT VM no Centro de Segurança Azure](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. Na **configuração de acesso JIT VM,** pode editar as definições existentes de uma porta já protegida ou adicionar uma nova porta personalizada.

    1. Quando terminar de editar as portas, **selecione Save**.


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>Ativar o JIT nas VMs com o PowerShell

Para ativar o acesso em VM a tempo da PowerShell, utilize o cmdlet oficial do Centro de Segurança Azure PowerShell `Set-AzJitNetworkAccessPolicy` .

**Exemplo** - Permitir o acesso em VM just-in-time a um VM específico com as seguintes regras:

* Feche as portas 22 e 3389
* Desente um prazo máximo de 3 horas para cada um para que possam ser abertos por pedido aprovado
* Permitir ao utilizador que está a solicitar acesso controlar os endereços IP de origem
* Permitir ao utilizador que está a solicitar acesso estabelecer uma sessão de sucesso após um pedido de acesso just-in-time aprovado

Os seguintes comandos PowerShell criam esta configuração JIT:

1. Atribua uma variável que detenha as regras de acesso em VM just-in-time para um VM:

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
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
    ```

1. Insira as regras de acesso VM just-in-time numa matriz:
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. Configure as regras de acesso em VM just-in-time no VM selecionado:
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    Utilize o parâmetro -Nome para especificar um VM. Por exemplo, para estabelecer a configuração JIT para dois VMs diferentes, VM1 e VM2, utilize: ```Set-AzJitNetworkAccessPolicy -Name VM1``` e ```Set-AzJitNetworkAccessPolicy -Name VM2``` .


### <a name="rest-api"></a>[**API REST**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>Ativar JIT nos seus VMs utilizando a API REST

A funcionalidade de acesso VM just-in-time pode ser utilizada através do API do Centro de Segurança Azure. Utilize esta API para obter informações sobre VMs configurados, adicione novos, solicite acesso a um VM, e muito mais. 

Saiba mais nas [políticas de acesso à rede JIT.](/rest/api/securitycenter/jitnetworkaccesspolicies)


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>Pedir acesso a uma VM com o JIT ativado

Pode solicitar acesso a um VM ativado pelo JIT a partir do portal Azure (em Centro de Segurança ou máquinas Virtuais Azure) ou programáticamente.

Cada uma destas opções é explicada num separado abaixo.

### <a name="azure-security-center"></a>[**Centro de Segurança do Azure**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>Solicitar acesso a um VM ativado pelo JIT do Centro de Segurança Azure 

Quando um VM tem um JIT ativado, tem de solicitar acesso para se ligar ao mesmo. Pode solicitar o acesso de qualquer uma das formas apoiadas, independentemente da forma como ativou o JIT.

:::image type="content" source="./media/security-center-just-in-time/jit-request-security-center.gif" alt-text="Solicitando acesso JIT do Centro de Segurança":::

1. A partir da página **de acesso VM just-in-time,** selecione o separador **Configurado.**

1. Marque os VMs a que quer aceder.

    - O ícone na coluna Detalhes de **Ligação** indica se o JIT está ativado no grupo de segurança da rede ou na firewall. Se estiver ativado em ambos, apenas aparece o ícone da firewall.

    - A coluna **Detalhes de Ligação** fornece as informações necessárias para ligar o VM e as suas portas abertas.

1. Selecione **O acesso ao Pedido**. A janela **de acesso do Pedido** abre.

1. No **Acesso Ao Pedido,** para cada VM, configurar as portas que pretende abrir e os endereços IP de origem onde a porta é aberta e o tempo para o qual a porta estará aberta. Só será possível solicitar o acesso às portas configuradas. Cada porta tem um tempo máximo permitido derivado da configuração JIT que criou.

1. Selecione **portas abertas**.

> [!NOTE]
> Se um utilizador que está a solicitar acesso estiver por detrás de um representante, a opção **My IP** pode não funcionar. Poderá ser necessário definir o alcance completo do endereço IP da organização.



### <a name="azure-virtual-machines"></a>[**Máquinas virtuais Azure**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Solicitar acesso a um VM ativado pelo JIT a partir da página de ligação da máquina virtual Azure

Quando um VM tem um JIT ativado, tem de solicitar acesso para se ligar ao mesmo. Pode solicitar o acesso de qualquer uma das formas apoiadas, independentemente da forma como ativou o JIT.

  >![pedido jit just-in-time](./media/security-center-just-in-time/jit-request-vm.png)


Para solicitar o acesso a partir de máquinas virtuais Azure:

1. No portal Azure, abra as páginas das máquinas virtuais.

1. Selecione o VM ao qual pretende ligar e abra a página **'Ligar'.**

    A azure verifica se o JIT está ativado nesse VM.

    - Se o JIT não estiver habilitado para o VM, será solicitado para o ativar.

    - Se o JIT estiver habilitado, selecione **Pedir acesso** para passar um pedido de acesso com o IP solicitador, intervalo de tempo e portas configuradas para esse VM.

> [!NOTE]
> Depois de um pedido ser aprovado para um VM protegido pela Azure Firewall, o Security Center fornece ao utilizador os detalhes de ligação adequados (o mapeamento da porta a partir da tabela DNAT) para usar para ligar ao VM.



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>Pedir acesso a uma VM com o JIT ativado com o PowerShell

No exemplo seguinte, pode ver um pedido de acesso vm just-in-time a um VM específico no qual a porta 22 é solicitada para ser aberta para um endereço IP específico e por um período específico:

Executar o seguinte em PowerShell:

1. Configure as propriedades de acesso de pedido VM:

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. Insira os parâmetros de pedido de acesso VM numa matriz:

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. Envie o acesso ao pedido (utilize o ID do recurso a partir do passo 1)

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

Saiba mais na documentação do [cmdlet PowerShell](/powershell/scripting/developer/cmdlet/cmdlet-overview).



### <a name="rest-api"></a>[**API REST**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>Solicitar acesso a um VMs habilitado a JIT utilizando a API REST

A funcionalidade de acesso VM just-in-time pode ser utilizada através do API do Centro de Segurança Azure. Utilize esta API para obter informações sobre VMs configurados, adicione novos, solicite acesso a um VM, e muito mais. 

Saiba mais nas [políticas de acesso à rede JIT.](/rest/api/securitycenter/jitnetworkaccesspolicies)

---








## <a name="audit-jit-access-activity-in-security-center"></a>Atividade de acesso JIT de auditoria no Centro de Segurança

Você pode obter insights sobre as atividades de VM usando a pesquisa de registo. Para ver os registos:

1. A partir do **acesso VM just-in-time,** selecione o **separador Configurado.**

1. Para o VM que pretende auditar, abra o menu de elipses no final da linha.
 
1. Selecione Registo de **Atividades** no menu.

   ![Selecione login de atividades JIT just-in-time](./media/security-center-just-in-time/jit-select-activity-log.png)

   O registo de atividade fornece uma visão filtrada das operações anteriores para esse VM juntamente com a hora, data e subscrição.

1. Para descarregar as informações de registo, selecione **Baixar como CSV**.








## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar e utilizar o acesso vm mesmo a tempo. Para saber por que o JIT deve ser usado, leia o artigo do conceito explicando as ameaças contra as quais defende:

> [!div class="nextstepaction"]
> [JIT explicou](just-in-time-explained.md)