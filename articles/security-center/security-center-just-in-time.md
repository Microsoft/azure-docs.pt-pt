---
title: Acesso a máquinas virtuais just-in-time no Azure Security Center Microsoft Docs
description: Este documento demonstra como o acesso ao VM just-in-time no Azure Security Center ajuda-o a controlar o acesso às suas máquinas virtuais Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: b24e0487aef73ed7852cb4a64766a1f8d92aff94
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677441"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Proteja as suas portas de gestão com acesso just-in-time

Se estiver no nível de preços padrão do Security Center (ver [preços),](/azure/security-center/security-center-pricing)pode bloquear o tráfego de entrada para os seus VMs Azure com acesso à máquina virtual (VM) just-in-time (JIT). Isto reduz a exposição a ataques, ao mesmo tempo que proporciona fácil acesso à ligação aos VM quando necessário.

> [!NOTE]
> O acesso ao VM do Security Center just-in-time suporta atualmente apenas VMs implantados através do Azure Resource Manager. Para saber mais sobre os modelos clássicos e de implementação do Gestor de Recursos, consulte [o Azure Resource Manager vs. design clássico](../azure-resource-manager/management/deployment-models.md).

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>Igigure JIT em um VM

Há três formas de configurar uma política de JIT sobre um VM:

- [Configure acesso JIT no Centro de Segurança Azure](#jit-asc)
- [Configure acesso JIT em uma página VM Azure](#jit-vm)
- [Configure uma política de JIT num programatismo de VM](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>Configure JIT no Centro de Segurança Azure

A partir do Centro de Segurança, você pode configurar uma política JIT e solicitar acesso a um VM usando uma política JIT

### <a name="configure-jit-access-on-a-vm-in-security-center"></a>Configure acesso JIT em um VM no Centro de Segurança<a name="jit-asc"></a>

1. Abra o dashboard **Centro de Segurança**.

1. No painel esquerdo, selecione **acesso VM just-in-time**.

    ![Azulejo de acesso VM just-in-time](./media/security-center-just-in-time/just-in-time.png)

    A janela **de acesso VM just-in-time** abre e mostra informações sobre o estado dos seus VMs:

    - **Configurado** - VMs que foram configurados para suportar o acesso em VM just-in-time. Os dados apresentados são para a última semana e incluem para cada VM o número de pedidos aprovados, última data e hora de acesso e último utilizador.
    - **Recomendado** - VMs que podem suportar acesso em VM just-in-time, mas não foram configurados para. Recomendamos que ative o controlo de acesso em VM just-in-time para estes VMs.
    - **Nenhuma recomendação** - As razões que podem fazer com que uma VM não seja recomendada são:
      - NSG em falta - A solução just-in-time requer que um NSG esteja no lugar.
      - O acesso clássico do VM - Security Center just-in-time suporta apenas VMs implantados através do Azure Resource Manager. Uma implantação clássica não é suportada pela solução just-in-time. 
      - Outros - Um VM está nesta categoria se a solução just-in-time for desligada na política de segurança da subscrição ou do grupo de recursos, ou se o VM estiver faltando um IP público e não tiver um NSG no lugar.

1. Selecione o **separador Recomendado.**

1. Em **MÁQUINA VIRTUAL,** clique nos VMs que pretende ativar. Isto coloca uma marca de verificação ao lado de um VM.

      ![Ativar o acesso just-in-time](./media/security-center-just-in-time/enable-just-in-time.png)

1. Clique **em Ativar JIT em VMs**. Um painel abre-se mostrando as portas predefinidas recomendadas pelo Azure Security Center:
    - 22 - SSH
    - 3389 - PDR
    - 5985 - WinrM 
    - 5986 - WinrM
1. Opcionalmente, pode adicionar portas personalizadas à lista:

      1. Clique em **Adicionar**. A janela **de configuração da porta Add** abre-se.
      1. Para cada porta que escolher configurar, tanto predefinido como personalizado, pode personalizar as seguintes definições:
            - **Tipo de protocolo**- O protocolo que é permitido nesta porta quando um pedido é aprovado.
            - **Endereços IP de origem permitida**- As gamas IP que são permitidas nesta porta quando um pedido é aprovado.
            - **Tempo máximo de pedido**- O tempo máximo durante o qual uma porta específica pode ser aberta.

     1. Clique em **OK**.

1. Clique em **Guardar**.

> [!NOTE]
>Quando o JIT VM Access está ativado para um VM, o Azure Security Center cria regras de "negar todo o tráfego de entrada" para as portas selecionadas nos grupos de segurança de rede associados e a Azure Firewall com ele. Se tivessem sido criadas outras regras para os portos selecionados, as regras existentes têm prioridade sobre as novas regras de "negar todo o tráfego de entrada". Se não existirem regras existentes nas portas selecionadas, as novas regras de "negar todo o tráfego de entrada" têm prioridade máxima nos Grupos de Segurança da Rede e no Azure Firewall.


## <a name="request-jit-access-via-security-center"></a>Solicite acesso jit via Centro de Segurança

Para solicitar o acesso a um VM via Security Center:

1. No **acesso VM just-in-time,** selecione o **separador Configurado.**

1. Em **Máquina Virtual,** clique nos VMs para os que pretende solicitar acesso. Isto coloca uma marca de verificação ao lado do VM.

    - O ícone na coluna Detalhes de **Ligação** indica se o JIT está ativado no NSG ou FW. Se estiver ativado em ambos, apenas aparece o ícone Firewall.

    - A coluna **Detalhes de Ligação** fornece as informações necessárias para ligar o VM e as suas portas abertas.

      ![Pedir acesso just-in-time](./media/security-center-just-in-time/request-just-in-time-access.png)

1. Clique **em Pedir acesso.** A janela **de acesso do Pedido** abre.

      ![Detalhes do JIT](./media/security-center-just-in-time/just-in-time-details.png)

1. No **Acesso Ao Pedido,** para cada VM, configurar as portas que pretende abrir e os endereços IP de origem onde a porta é aberta e o tempo para o qual a porta estará aberta. Só será possível solicitar o acesso aos portos configurados na política just-in-time. Cada porta tem um tempo máximo permitido derivado da política just-in-time.

1. Clique **em Portas Abertas**.

> [!NOTE]
> Se um utilizador que está a solicitar acesso estiver por detrás de um representante, a opção **My IP** pode não funcionar. Poderá ser necessário definir o alcance completo do endereço IP da organização.



## <a name="edit-a-jit-access-policy-via-security-center"></a>Editar uma política de acesso JIT via Security Center

Pode alterar a política de prazos justos existente de um VM adicionando e configurando uma nova porta para proteger para esse VM, ou alterando qualquer outra definição relacionada com uma porta já protegida.

Para editar uma política just-in-time existente de um VM:

1. No separador **Configurado,** em **VMs,** selecione um VM para adicionar uma porta clicando nos três pontos dentro da linha para esse VM. 

1. Selecione **Editar**.

1. Na **configuração de acesso JIT VM,** pode editar as definições existentes de uma porta já protegida ou adicionar uma nova porta personalizada. 
  ![acesso jit vm](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>Atividade de acesso JIT de auditoria no Centro de Segurança

Você pode obter insights sobre as atividades de VM usando a pesquisa de registo. Para visualizar registos:

1. No **acesso VM just-in-time,** selecione o **separador Configurado.**
2. Em **VMs**, selecione um VM para visualizar informações clicando nos três pontos dentro da linha para esse VM e selecione Registo de **Atividades** no menu. O **registo de atividades** abre.

   ![Selecione registo de atividades](./media/security-center-just-in-time/select-activity-log.png)

   **O registo de atividades** fornece uma visão filtrada das operações anteriores para esse VM, juntamente com a hora, data e subscrição.

Pode descarregar as informações de registo selecionando **Clique aqui para descarregar todos os itens como CSV**.

Modifique os filtros e clique **em Aplicar** para criar uma pesquisa e log.



## <a name="configure-jit-access-from-an-azure-vms-page"></a>Configure acesso JIT a partir de uma página de Azure VM<a name="jit-vm"></a>

Para sua conveniência, pode ligar-se a um VM usando JIT diretamente a partir da página do VM no Centro de Segurança.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Configure acesso JIT em um VM através da página Azure VM

Para facilitar o acesso just-in-time através dos seus VMs, pode definir um VM para permitir apenas o acesso just-in-time diretamente a partir do VM.

1. A partir do [portal Azure,](https://ms.portal.azure.com)procure e selecione **máquinas Virtuais.** 
2. Selecione a máquina virtual que pretende limitar ao acesso just-in-time.
3. No menu, selecione **Configuração**.
4. No **acesso just-in-time**, selecione **Ative just-in-time**. 

Isto permite o acesso just-in-time para o VM utilizando as seguintes definições:

- Servidores windows:
    - Porta RDP 3389
    - Três horas de acesso máximo permitido
    - Os endereços IP de origem permitida estão definidos para Qualquer
- Servidores Linux:
    - Porta SSH 22
    - Três horas de acesso máximo permitido
    - Os endereços IP de origem permitida estão definidos para Qualquer
     
Se um VM já tiver um tempo único ativado, quando for à sua página de configuração poderá ver que o just-in-time está ativado e pode utilizar o link para abrir a política no Azure Security Center para visualizar e alterar as definições.

![jit config em vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>Solicite acesso a um VM através de uma página de Azure VM

No portal Azure, quando tenta ligar-se a um VM, o Azure verifica se tem uma política de acesso just-in-time configurada nesse VM.

- Se tiver uma política JIT configurada no VM, pode clicar em **Pedir acesso** a concessão de acesso de acordo com a política JIT definida para o VM. 

  >![pedido jit](./media/security-center-just-in-time/jit-request.png)

  O acesso é solicitado com os seguintes parâmetros predefinidos:

  - **origem IP**: 'Any' (*) (não pode ser alterado)
  - **intervalo de tempo**: Três horas (não pode ser alterada) <!--Isn't this set in the policy-->
  - **número da porta** Porta RDP 3389 para Windows / porta 22 para Linux (pode ser alterada)

    > [!NOTE]
    > Depois de um pedido ser aprovado para um VM protegido pela Azure Firewall, o Security Center fornece ao utilizador os detalhes de ligação adequados (o mapeamento da porta a partir da tabela DNAT) para usar para ligar ao VM.

- Se não tiver o JIT configurado num VM, será solicitado a configurar uma política de JIT sobre o mesmo.

  ![jit imediato](./media/security-center-just-in-time/jit-prompt.png)

## <a name="configure-a-jit-policy-on-a-vm-programmatically"></a>Configure uma política de JIT num programatismo de VM<a name="jit-program"></a>

Pode configurar e utilizar just-in-time através de REST APIs e via PowerShell.

### <a name="jit-vm-access-via-rest-apis"></a>Acesso JIT VM via REST APIs

A funcionalidade de acesso VM just-in-time pode ser utilizada através do API do Centro de Segurança Azure. Você pode obter informações sobre VMs configurados, adicionar novos, solicitar acesso a um VM, e muito mais, através desta API. Consulte as Políticas de [Acesso à Rede Jit,](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)para saber mais sobre a API rest just-in-time.

### <a name="jit-vm-access-via-powershell"></a>Acesso JIT VM via PowerShell

Para utilizar a solução de acesso VM just-in-time via PowerShell, utilize os cmdlets oficiais do Azure Security Center PowerShell e, especificamente `Set-AzJitNetworkAccessPolicy` ,

O exemplo a seguir define uma política de acesso em VM just-in-time num VM específico e define o seguinte:

1.    Feche as portas 22 e 3389.

2.    Desente um prazo máximo de 3 horas para cada um para que possam ser abertos por pedido aprovado.
3.    Permite ao utilizador que está a solicitar acesso para controlar os endereços IP de origem e permite ao utilizador estabelecer uma sessão de sucesso após um pedido de acesso just-in-time aprovado.

Corra o seguinte em PowerShell para realizar isto:

1.    Atribua uma variável que detém a política de acesso em VM just-in-time para um VM:

        $JitPolicy = (@{id="/subscrições/SUBSCRIÇID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";   portas=(@{ número=22;        protocolo=" \* ";        allowedSourceAddressPrefix=@"); \*        maxRequestAccessDuration="PT3H"}, @{ number=3389;        protocolo=" \* ";        allowedSourceAddressPrefix=@"); \*        maxRequestAccessDuration="PT3H"}}}}}

2.    Insira a política de acesso VM just-in-time a uma matriz:
    
        $JitPolicyArr=@($JitPolicy)

3.    Configure a política de acesso em VM just-in-time no VM selecionado:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Localização "LOCATION" - Nome "padrão" -Grupo de RecursosMina "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>Solicitar acesso a um VM via PowerShell

No exemplo seguinte, pode ver um pedido de acesso vm just-in-time a um VM específico no qual a porta 22 é solicitada para ser aberta para um endereço IP específico e por um período específico:

Executar o seguinte em PowerShell:
1.    Configure as propriedades de acesso de pedido vM

        $JitPolicyVm1 = (@{id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";   portas=(@{ número=22;      endTimeUtc="2018-09-17T17:00:00.3658798Z";      allowedSourceAddressPrefix=@("IPV4ADDRESS")}}}}}}}}}}}}}}))}
2.    Insira os parâmetros de pedido de acesso VM numa matriz:

        $JitPolicyArr=@($JitPolicyVm1)
3.    Envie o acesso ao pedido (use o ID de recurso que obteve no passo 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Para obter mais informações, consulte a documentação do [cmdlet PowerShell](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview).


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>Limpeza automática das regras redundantes do JIT 

Sempre que atualiza uma política JIT, uma ferramenta de limpeza corre automaticamente para verificar a validade de todo o seu conjunto de regras. A ferramenta procura desencontros entre regras na sua política e regras no NSG. Se a ferramenta de limpeza encontrar um desfasamento, determina a causa e, quando é seguro fazê-lo, remove regras incorporadas que já não são necessárias. O aspirador nunca apaga as regras que criaste.

Exemplos de cenários quando o aspirador pode remover uma regra incorporada:

- Quando existem duas regras com definições idênticas e uma tem uma prioridade maior do que a outra (ou seja, a regra da prioridade inferior nunca será utilizada)
- Quando uma descrição de regras inclui o nome de um VM que não corresponde ao destino IP na regra 


## <a name="next-steps"></a>Próximos passos

Neste artigo, você aprendeu como o acesso vm just-in-time no Centro de Segurança ajuda-o a controlar o acesso às suas máquinas virtuais Azure.

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

- O módulo Microsoft Learn [Proteja os seus servidores e VMs de ataques de força bruta e malware com o Azure Security Center](https://docs.microsoft.com/learn/modules/secure-vms-with-azure-security-center/)
- [Definir políticas de segurança](tutorial-security-policy.md) — Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos Azure.
- [Gerir recomendações de segurança](security-center-recommendations.md) - Saiba como as recomendações o ajudam a proteger os seus recursos Azure.
- [Monitorização de saúde de](security-center-monitoring.md) segurança - Aprenda a monitorizar a saúde dos seus recursos Azure.
