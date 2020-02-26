---
title: Acesso a máquina virtual just-in-time no Centro de Segurança Azure  Microsoft Docs
description: Este documento demonstra como o acesso vm just-in-time no Azure Security Center ajuda-o a controlar o acesso às suas máquinas virtuais Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 4b2b388fb736997010a6cbbdf93b23b77c7ef3a3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603982"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Proteja as suas portas de gestão com acesso just-in-time

Se estiver no nível de preços padrão do Security Center (ver [preços),](/azure/security-center/security-center-pricing)pode bloquear o tráfego de entrada para os seus VMs Azure com acesso à máquina virtual (VM) just-in-time (JIT). Isto reduz a exposição a ataques, proporcionando um fácil acesso à ligação aos VMs quando necessário.

> [!NOTE]
> O acesso vm just-in-time do Security Center suporta atualmente apenas VMs implantados através do Azure Resource Manager. Para saber mais sobre os modelos clássicos e de implantação do Gestor de Recursos, consulte [o Azure Resource Manager vs. classic deployment](../azure-resource-manager/management/deployment-models.md).

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>Configure jIT em um VM

Existem três formas de configurar uma política de JIT num VM:

- [Configure acesso JIT no Centro de Segurança Azure](#jit-asc)
- [Configure o acesso do JIT numa página Azure VM](#jit-vm)
- [Configure uma política de JIT num VM programática](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>Configure JIT no Centro de Segurança Azure

A partir do Security Center, pode configurar uma política jit e solicitar acesso a um VM usando uma política jit

### Configure o acesso jIT a um VM no Centro de Segurança<a name="jit-asc"></a>

1. Abra o dashboard **Centro de Segurança**.

1. No painel esquerdo, selecione **acesso VM just-in-time**.

    ![Azulejo de acesso VM just-in-time](./media/security-center-just-in-time/just-in-time.png)

    A janela **de acesso VM just-in-time** abre e mostra informações sobre o estado dos seus VMs:

    - **Configurados** - VMs que foram configurados para suportar acesso vM just-in-time. Os dados apresentados são para a última semana e incluem para cada VM o número de pedidos aprovados, última data e hora de acesso, e último utilizador.
    - **Recomendado** - VMs que podem suportar acesso VM just-in-time, mas não foram configurados para. Recomendamos que ative o controlo de acesso VM just-in-time para estes VMs.
    - **Nenhuma recomendação** - As razões que podem fazer com que uma VM não seja recomendada são:
      - NSG desaparecido - A solução just-in-time requer que um NSG esteja no lugar.
      - O acesso VM clássico do Centro de Segurança just-in-time suporta atualmente apenas VMs implantados através do Azure Resource Manager. Uma implantação clássica não é suportada pela solução just-in-time. 
      - Outros - Um VM está nesta categoria se a solução just-in-time for desligada na política de segurança da subscrição ou do grupo de recursos, ou se o VM está faltando um IP público e não tem um NSG no lugar.

1. Selecione o separador **Recomendado.**

1. Em VIRTUAL **MACHINE,** clique nos VMs que pretende ativar. Isto coloca uma marca de verificação ao lado de um VM.

      ![Ativar acesso just-in-time](./media/security-center-just-in-time/enable-just-in-time.png)

1. Clique **em ativar o JIT em VMs**. Abre-se um painel com as portas predefinidas recomendadas pelo Azure Security Center:
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinrM 
    - 5986 - WinRM
1. Opcionalmente, pode adicionar portas personalizadas à lista:

      1. Clique em **Adicionar**. A janela de **configuração** da porta Adicionar abre-se.
      1. Para cada porta, opte por configurar, tanto por defeito como personalizado, pode personalizar as seguintes definições:
            - **Tipo de protocolo**- O protocolo que é permitido nesta porta quando um pedido é aprovado.
            - **Endereços IP de origem permitidos**- As gamas IP que são permitidas nesta porta quando um pedido é aprovado.
            - **Tempo**máximo de pedido - O prazo máximo durante o qual uma porta específica pode ser aberta.

     1. Clique em **OK**.

1. Clique em **Guardar**.

> [!NOTE]
>Quando o JIT VM Access está ativado para um VM, o Azure Security Center cria "negar todas as regras de tráfego de entrada" para os portos selecionados nos grupos de segurança de rede associados e azure Firewall com ele. Se tivessem sido criadas outras regras para os portos selecionados, então as regras existentes têm prioridade sobre as novas regras de "negar todo o tráfego de entrada". Se não existirem regras existentes sobre os portos selecionados, as novas regras de "negar todo o tráfego de entrada" têm prioridade máxima nos Grupos de Segurança da Rede e na Firewall Azure.


## <a name="request-jit-access-via-security-center"></a>Solicitar acesso jIT via Security Center

Para solicitar acesso a um VM via Security Center:

1. Sob **acesso VM just-in-time,** selecione o separador **Configurado.**

1. Em **Virtual Machine,** clique nos VMs que pretende solicitar de acesso. Isto coloca uma marca de verificação ao lado do VM.

    - O ícone na coluna Detalhes de **Ligação** indica se o JIT está ativado no NSG ou FW. Se estiver ativado em ambos, apenas aparece o ícone firewall.

    - A coluna Detalhes de **Ligação** fornece as informações necessárias para ligar o VM e as suas portas abertas.

      ![Pedir acesso just-in-time](./media/security-center-just-in-time/request-just-in-time-access.png)

1. Clique no **acesso ao Pedido**. A janela de **acesso do Pedido** abre.

      ![Detalhes do JIT](./media/security-center-just-in-time/just-in-time-details.png)

1. No âmbito do **acesso a Pedido,** para cada VM, configure as portas que pretende abrir e os endereços IP de origem em que a porta está aberta e o prazo para o qual a porta estará aberta. Só será possível solicitar o acesso aos portos que estão configurados na política just-in-time. Cada porto tem um tempo máximo permitido derivado da política just-in-time.

1. Clique em **portas abertas**.

> [!NOTE]
> Se um utilizador que está a solicitar acesso estiver por detrás de um proxy, a opção **My IP** pode não funcionar. Poderá ter de definir toda a gama de endereços IP da organização.



## <a name="edit-a-jit-access-policy-via-security-center"></a>Editar uma política de acesso JIT via Security Center

Pode alterar a política existente de um VM just-in-time adicionando e configurando uma nova porta para proteger para esse VM, ou alterando qualquer outra definição relacionada com uma porta já protegida.

Para editar uma política just-in-time existente de um VM:

1. No separador **Configurado,** em **VMs,** selecione um VM para adicionar uma porta clicando nos três pontos dentro da linha para esse VM. 

1. Selecione **Editar**.

1. Sob a configuração de **acesso JIT VM,** pode editar as definições existentes de uma porta já protegida ou adicionar uma nova porta personalizada. 
  ![jit vm acesso](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>Atividade de acesso ao JIT em Centro de Segurança

Pode obter informações sobre as atividades vm usando a pesquisa de registo. Para ver os registos:

1. Sob **acesso VM just-in-time,** selecione o separador **Configurado.**
2. Em **VMs,** selecione um VM para visualizar informações clicando nos três pontos dentro da linha para esse VM e selecione Registo de **Atividade** seleção a partir do menu. O **registo de atividade** abre.

   ![Selecione o registo de atividade](./media/security-center-just-in-time/select-activity-log.png)

   **O registo** de atividade supor uma visão filtrada das operações anteriores para esse VM, juntamente com a hora, data e subscrição.

Pode descarregar as informações de registo selecionando **Clique aqui para baixar todos os itens como CSV**.

Modifique os filtros e clique **Em Aplicar** para criar uma pesquisa e registo.



## Configure o acesso jIT a partir de uma página de Azure VM<a name="jit-vm"></a>

Para sua conveniência, pode ligar-se a um VM usando JIT diretamente da página do VM no Centro de Segurança.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Configure o acesso jIT num VM através da página Azure VM

Para facilitar o acesso a tempo just-in através dos seus VMs, pode definir um VM para permitir apenas acesso a tempo diretamente a partir do VM.

1. A partir do [portal Azure,](https://ms.portal.azure.com)procure e selecione **máquinas Virtuais.** 
2. Selecione a máquina virtual que pretende limitar ao acesso just-in-time.
3. No menu, selecione **Configuração**.
4. Sob **acesso Just-in-time,** selecione **Enable just-in-time**. 

Isto permite o acesso just-in-time para o VM utilizando as seguintes definições:

- Servidores do Windows:
    - Porta RDP 3389
    - Três horas de acesso máximo permitido
    - Endereços IP de origem permitidos estão definidos para Qualquer
- Servidores Linux:
    - Porta SSH 22
    - Três horas de acesso máximo permitido
    - Endereços IP de origem permitidos estão definidos para Qualquer
     
Se um VM já tiver ativado atempo, quando for à sua página de configuração poderá ver que o tempo útil está ativado e pode utilizar o link para abrir a política no Azure Security Center para visualizar e alterar as definições.

![jit config em vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>Solicite acesso jIT a um VM através de uma página de Azure VM

No portal Azure, quando se tenta ligar a um VM, o Azure verifica se tem uma política de acesso just-in-time configurada nesse VM.

- Se tiver uma política de JIT configurada no VM, pode clicar no **Solicite acesso** ao acesso de acordo com a política jit definida para o VM. 

  >![jit pedido](./media/security-center-just-in-time/jit-request.png)

  É solicitado acesso com os seguintes parâmetros predefinidos:

  - **IP fonte:** 'Qualquer' (*) (não pode ser alterado)
  - **intervalo de tempo:** Três horas (não pode ser alterada) <!--Isn't this set in the policy-->
  - **número da porta** Porta RDP 3389 para Windows / porta 22 para Linux (pode ser alterada)

    > [!NOTE]
    > Depois de aprovado um pedido para um VM protegido pela Firewall Azure, o Security Center fornece ao utilizador os dados de ligação adequados (o mapeamento da porta a partir da tabela DNAT) para usar para ligar ao VM.

- Se não tiver o JIT configurado num VM, será solicitado a configurar uma política de JIT nele.

  ![jit pronta](./media/security-center-just-in-time/jit-prompt.png)

## Configure uma política de JIT num VM programática<a name="jit-program"></a>

Pode configurar e utilizar apenas no tempo através de APIs REST e via PowerShell.

### <a name="jit-vm-access-via-rest-apis"></a>Acesso JIT VM via REST APIs

A funcionalidade de acesso VM just-in-time pode ser utilizada através da API do Centro de Segurança Azure. Pode obter informações sobre VMs configurados, adicionar novos, solicitar acesso a um VM, e muito mais, através desta API. Consulte as Políticas de Acesso à [Rede Jit,](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)para saber mais sobre a Just-in-Time REST API.

### <a name="jit-vm-access-via-powershell"></a>Acesso JIT VM via PowerShell

Para utilizar a solução de acesso VM just-in-time via PowerShell, utilize os cmdlets oficiais do Centro de Segurança Azure PowerShell e, especificamente, `Set-AzJitNetworkAccessPolicy`.

O exemplo que se segue define uma política de acesso vm just-in-time num VM específico, e define o seguinte:

1.  Feche mato nos portos 22 e 3389.

2.  Detete uma janela de tempo máxima de 3 horas para cada um para que possam ser abertas por pedido aprovado.
3.  Permite ao utilizador que está a solicitar acesso para controlar os endereços IP de origem e permite ao utilizador estabelecer uma sessão de sucesso mediante um pedido de acesso just-in-time aprovado.

Execute o seguinte na PowerShell para conseguir isto:

1.  Atribuir uma variável que detém a política de acesso VM just-in-time para um VM:

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

2.  Insira a política de acesso VM just-in-time a uma matriz:
    
        $JitPolicyArr=@($JitPolicy)

3.  Configure a política de acesso vm just-in-time no VM selecionado:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>Solicitar acesso a um VM via PowerShell

No exemplo seguinte, pode ver um pedido de acesso VM just-in-time a um VM específico no qual é solicitado que o porto 22 seja aberto para um endereço IP específico e por um período específico de tempo:

Executar o seguinte na PowerShell:
1.  Configure as propriedades de acesso ao pedido vm

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Insira os parâmetros de pedido de acesso VM numa matriz:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Envie o acesso ao pedido (utilize o ID de recurso que obteve no passo 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Para mais informações, consulte a [documentação cmdlet PowerShell](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview).


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>Limpeza automática de regras redundantes do JIT 

Sempre que atualiza uma política de JIT, uma ferramenta de limpeza funciona automaticamente para verificar a validade de todo o seu conjunto de regras. A ferramenta procura desencontros entre regras na sua política e regras no NSG. Se a ferramenta de limpeza encontrar uma incompatibilidade, determina a causa e, quando é seguro fazê-lo, remove regras incorporadas que já não são necessárias. O limpador nunca apaga as regras que criaste.

Exemplos de cenários quando o aspirador pode remover uma regra incorporada:

- Quando existem duas regras com definições idênticas e uma tem uma prioridade maior do que a outra (ou seja, a regra da prioridade mais baixa nunca será utilizada)
- Quando uma descrição da regra inclui o nome de um VM que não corresponde ao IP de destino na regra 


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu como o acesso vm just-in-time no Security Center ajuda-o a controlar o acesso às suas máquinas virtuais Azure.

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

- [Definição de políticas](tutorial-security-policy.md) de segurança — Saiba como configurar as políticas de segurança para as suas subscrições e grupos de recursos Do Azure.
- [Gerir recomendações de segurança](security-center-recommendations.md) — Saiba como as recomendações ajudam a proteger os seus recursos Azure.
- [Monitorização da saúde](security-center-monitoring.md) em segurança — Saiba como monitorizar a saúde dos seus recursos Azure.