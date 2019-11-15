---
title: Configurar a recuperação de desastre do DNS/Active Directory com Azure Site Recovery
description: Este artigo descreve como implementar uma solução de recuperação de desastre para Active Directory e DNS com Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 8c1f85217db12b60cdcd8ea0bdb65792b8d02648
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084588"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Configurar a recuperação de desastre para Active Directory e DNS

Aplicativos empresariais, como o SharePoint, o Dynamics AX e o SAP, dependem de Active Directory e de uma infraestrutura de DNS para funcionar corretamente. Quando você configura a recuperação de desastre para aplicativos, geralmente precisa recuperar Active Directory e DNS antes de recuperar outros componentes de aplicativo, para garantir a funcionalidade correta do aplicativo.

Você pode usar [site Recovery](site-recovery-overview.md) para criar um plano de recuperação de desastres para Active Directory. Quando ocorrer uma interrupção, você poderá iniciar um failover. Você pode ter Active Directory em funcionamento em alguns minutos. Se você implantou Active Directory para vários aplicativos em seu site primário, por exemplo, para SharePoint e SAP, talvez queira fazer failover do site completo. Você pode fazer o failover primeiro Active Directory usando Site Recovery. Em seguida, faça failover dos outros aplicativos usando planos de recuperação específicos do aplicativo.

Este artigo explica como criar uma solução de recuperação de desastre para Active Directory. Ele inclui pré-requisitos e instruções de failover. Você deve estar familiarizado com Active Directory e Site Recovery antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Se você estiver replicando para o Azure, [prepare os recursos do Azure](tutorial-prepare-azure.md), incluindo uma assinatura, uma rede virtual do Azure, uma conta de armazenamento e um cofre dos serviços de recuperação.
* Reveja os [requisitos de suporte](site-recovery-support-matrix-to-azure.md) de todos os componentes.

## <a name="replicate-the-domain-controller"></a>Replicar o controlador de domínio

- Você deve configurar a replicação do Site Recovery, em pelo menos uma VM que hospeda um controlador de domínio ou DNS.
- Se você tiver vários controladores de domínio em seu ambiente, também deverá configurar um controlador de domínio adicional no site de destino. O controlador de domínio adicional pode estar no Azure ou em um datacenter local secundário.
- Se você tiver apenas alguns aplicativos e um controlador de domínio, talvez queira fazer failover de todo o site juntos. Nesse caso, é recomendável usar Site Recovery para replicar o controlador de domínio para o site de destino (no Azure ou em um datacenter local secundário). Você pode usar o mesmo controlador de domínio replicado ou máquina virtual DNS para [failover de teste](#test-failover-considerations).
- - Se você tiver muitos aplicativos e mais de um controlador de domínio em seu ambiente, ou se planejar fazer failover de alguns aplicativos por vez, além de replicar a máquina virtual do controlador de domínio com Site Recovery, recomendamos que você configure um controlador de domínio adicional no site de destino (no Azure ou em um datacenter local secundário). Para o [failover de teste](#test-failover-considerations), você pode usar o controlador de domínio que é replicado pelo site Recovery. Para o failover, você pode usar o controlador de domínio adicional no site de destino.

## <a name="enable-protection-with-site-recovery"></a>Habilitar proteção com Site Recovery

Você pode usar Site Recovery para proteger a máquina virtual que hospeda o controlador de domínio ou o DNS.

### <a name="protect-the-vm"></a>Proteger a VM
O controlador de domínio que é replicado usando Site Recovery é usado para [failover de teste](#test-failover-considerations). Verifique se ele atende aos seguintes requisitos:

1. O controlador de domínio é um servidor de catálogo global.
2. O controlador de domínio deve ser o proprietário da função FSMO para funções que são necessárias durante um failover de teste. Caso contrário, essas funções precisarão ser [executadas](https://aka.ms/ad_seize_fsmo) após o failover.

### <a name="configure-vm-network-settings"></a>Definir configurações de rede VM
Para a máquina virtual que hospeda o controlador de domínio ou DNS, em Site Recovery, defina as configurações de rede nas configurações de **computação e rede** da máquina virtual replicada. Isso garante que a máquina virtual esteja conectada à rede correta após o failover.

## <a name="protect-active-directory"></a>Proteger Active Directory

### <a name="site-to-site-protection"></a>Proteção site a site
Crie um controlador de domínio no site secundário. Ao promover o servidor a uma função de controlador de domínio, especifique o nome do mesmo domínio que está sendo usado no site primário. Você pode usar o snap-in **Active Directory sites e serviços** para definir as configurações no objeto de link de site ao qual os sites são adicionados. Ao definir as configurações em um link de site, você pode controlar quando ocorre a replicação entre dois ou mais sites e com que frequência ele ocorre. Para obter mais informações, consulte [agendando a replicação entre sites](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Proteção site a Azure
Primeiro, crie um controlador de domínio em uma rede virtual do Azure. Ao promover o servidor a uma função de controlador de domínio, especifique o mesmo nome de domínio usado no site primário.

Em seguida, reconfigure o servidor DNS para a rede virtual para usar o servidor DNS no Azure.

![Rede do Azure](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Proteção do Azure para o Azure
Primeiro, crie um controlador de domínio em uma rede virtual do Azure. Ao promover o servidor a uma função de controlador de domínio, especifique o mesmo nome de domínio usado no site primário.

Em seguida, reconfigure o servidor DNS para a rede virtual para usar o servidor DNS no Azure.

## <a name="test-failover-considerations"></a>Considerações de failover de teste
Para evitar o impacto nas cargas de trabalho de produção, o failover de teste ocorre em uma rede isolada da rede de produção.

A maioria dos aplicativos requer a presença de um controlador de domínio ou um servidor DNS. Portanto, antes do failover do aplicativo, você deve criar um controlador de domínio na rede isolada a ser usado para o teste de tolerância. A maneira mais fácil de fazer isso é usar Site Recovery para replicar uma máquina virtual que hospeda um controlador de domínio ou DNS. Em seguida, execute um failover de teste da máquina virtual do controlador de domínio antes de executar um failover de teste do plano de recuperação para o aplicativo. Veja como fazer isso:

1. Use Site Recovery para [replicar](vmware-azure-tutorial.md) a máquina virtual que hospeda o controlador de domínio ou o DNS.
2. Crie uma rede isolada. Qualquer rede virtual que você cria no Azure é isolada de outras redes por padrão. Recomendamos que você use o mesmo intervalo de endereços IP para essa rede que você usa em sua rede de produção. Não habilite a conectividade site a site nesta rede.
3. Forneça um endereço IP DNS na rede isolada. Use o endereço IP que você espera que a máquina virtual do DNS obtenha. Se você estiver replicando para o Azure, forneça o endereço IP para a máquina virtual que é usada no failover. Para inserir o endereço IP, na máquina virtual replicada, nas configurações de **computação e rede** , selecione as configurações de **IP de destino** .

    ![Rede de teste do Azure](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery tenta criar máquinas virtuais de teste em uma sub-rede com o mesmo nome e usando o mesmo endereço IP fornecido nas configurações de **computação e rede** da máquina virtual. Se uma sub-rede com o mesmo nome não estiver disponível na rede virtual do Azure que é fornecida para o failover de teste, a máquina virtual de teste será criada na primeira sub-rede em ordem alfabética.
    >
    > Se o endereço IP de destino fizer parte da sub-rede selecionada, Site Recovery tentará criar a máquina virtual de failover de teste usando o endereço IP de destino. Se o IP de destino não fizer parte da sub-rede selecionada, a máquina virtual de failover de teste será criada usando o próximo IP disponível na sub-rede selecionada.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Failover de teste para um site secundário

1. Se você estiver replicando para outro site local e usar DHCP, [Configure o DNS e o DHCP para failover de teste](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Faça um failover de teste da máquina virtual do controlador de domínio que é executada na rede isolada. Use o ponto de recuperação *consistente do aplicativo* mais recente disponível da máquina virtual do controlador de domínio para fazer o failover de teste.
3. Execute um failover de teste para o plano de recuperação que contém as máquinas virtuais em que o aplicativo é executado.
4. Quando o teste for concluído, *Limpe o failover de teste* na máquina virtual do controlador de domínio. Esta etapa exclui o controlador de domínio que foi criado para failover de teste.


### <a name="remove-references-to-other-domain-controllers"></a>Remover referências a outros controladores de domínio
Quando você inicia um failover de teste, não inclua todos os controladores de domínio na rede de teste. Para remover referências a outros controladores de domínio que existem em seu ambiente de produção, talvez seja necessário [executar as funções de Active Directory FSMO](https://aka.ms/ad_seize_fsmo) e fazer a [limpeza de metadados](https://technet.microsoft.com/library/cc816907.aspx) para controladores de domínio ausentes.


### <a name="issues-caused-by-virtualization-safeguards"></a>Problemas causados por proteções de virtualização

> [!IMPORTANT]
> Algumas das configurações descritas nesta seção não são configurações padrão do controlador de domínio. Se você não quiser fazer essas alterações em um controlador de domínio de produção, poderá criar um controlador de domínio dedicado para Site Recovery usar para o failover de teste. Faça essas alterações somente nesse controlador de domínio.  
>
>

A partir do Windows Server 2012, as [proteções adicionais são criadas em Active Directory Domain Services (AD DS)](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Essas proteções ajudam a proteger controladores de domínio virtualizados contra reversões de USN se a plataforma de hipervisor subjacente der suporte a **VM-generationid**. O Azure dá suporte **a VM-generationid**. Por isso, os controladores de domínio que executam o Windows Server 2012 ou posterior em máquinas virtuais do Azure têm essas proteções adicionais.


Quando **VM-generationid** é redefinida, o valor de **invocaid** do banco de dados AD DS também é redefinido. Além disso, o pool RID é Descartado e a pasta SYSVOL é marcada como não autoritativa. Para obter mais informações, consulte [introdução à virtualização de Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) e virtualização do [DFSR com segurança](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

O failover para o Azure pode fazer com que a **VM-generationid** seja redefinida. A redefinição de **VM-generationid** dispara garantias adicionais quando a máquina virtual do controlador de domínio é iniciada no Azure. Isso pode resultar em um *atraso significativo* na capacidade de entrar na máquina virtual do controlador de domínio.

Como esse controlador de domínio é usado somente em um failover de teste, as proteções de virtualização não são necessárias. Para garantir que o valor da **VM-generationid** para a máquina virtual do controlador de domínio não seja alterado, você pode alterar o valor de DWORD a seguir para **4** no controlador de domínio local:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Sintomas de proteções de virtualização

Se as proteções de virtualização forem disparadas após um failover de teste, você poderá ver um ou mais dos seguintes sintomas:  

* O valor de **generationid** é alterado.

    ![Alteração de ID de geração](./media/site-recovery-active-directory/Event2170.png)

* O valor de **inrevocationid** é alterado.

    ![Alteração de ID de invocação](./media/site-recovery-active-directory/Event1109.png)

* A pasta SYSVOL e os compartilhamentos NETLOGON não estão disponíveis.

    ![Compartilhamento de pasta SYSVOL](./media/site-recovery-active-directory/sysvolshare.png)

    ![Pasta do SYSVOL do NtFrs](./media/site-recovery-active-directory/Event13565.png)

* Os bancos de dados DFSR são excluídos.

    ![Os bancos de dados DFSR são excluídos](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Solucionar problemas do controlador de domínio durante o failover de teste

> [!IMPORTANT]
> Algumas das configurações descritas nesta seção não são padrão ou configurações padrão do controlador de domínio. Se você não quiser fazer essas alterações em um controlador de domínio de produção, poderá criar um controlador de domínio dedicado para Site Recovery failover de teste. Faça as alterações somente nesse controlador de domínio dedicado.  
>
>

1. No prompt de comando, execute o seguinte comando para verificar se a pasta SYSVOL e a pasta NETLOGON estão compartilhadas:

    `NET SHARE`

2. No prompt de comando, execute o seguinte comando para garantir que o controlador de domínio esteja funcionando corretamente:

    `dcdiag /v > dcdiag.txt`

3. No log de saída, procure o texto a seguir. O texto confirma que o controlador de domínio está funcionando corretamente.

    * "passou no teste de conectividade"
    * "comunicado de teste aprovado"
    * "passou no teste MachineAccount"

Se as condições anteriores forem satisfeitas, é provável que o controlador de domínio esteja funcionando corretamente. Se não estiver, conclua as seguintes etapas:

1. Faça uma restauração autoritativa do controlador de domínio. Tenha em mente as seguintes informações:
    * Embora não recomendemos a [replicação do FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), se você usar a replicação do FRS, siga as etapas para uma restauração autoritativa. O processo é descrito em [usando a chave do Registro BURFLAGS para reinicializar o serviço de replicação de arquivo](https://support.microsoft.com/kb/290762).

        Para obter mais informações sobre BurFlags, consulte a postagem do blog [D2 e D4: para quê?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Se você usar a replicação DFSR, conclua as etapas para uma restauração autoritativa. O processo é descrito em [forçar uma sincronização autoritativa e não autoritativa para a pasta SYSVOL replicada pelo DFSR (como "D4/D2" para o FRS)](https://support.microsoft.com/kb/2218556).

        Você também pode usar as funções do PowerShell. Para obter mais informações, consulte [funções do PowerShell de restauração autoritativa/não autoritativa de DFSR-SYSVOL](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. Ignore o requisito de sincronização inicial definindo a seguinte chave do registro como **0** no controlador de domínio local. Se o DWORD não existir, você poderá criá-lo no nó **parâmetros** .

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Para obter mais informações, consulte [solucionar problemas de ID de evento do dns 4013: o servidor DNS não pôde carregar zonas DNS integradas ao AD](https://support.microsoft.com/kb/2001093).

3. Desabilite o requisito de que um servidor de catálogo global esteja disponível para validar o logon do usuário. Para fazer isso, no controlador de domínio local, defina a seguinte chave do registro como **1**. Se o DWORD não existir, você poderá criá-lo no nó **LSA** .

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Para obter mais informações, consulte [desabilitar o requisito de disponibilização de um servidor de catálogo global para validar logons de usuário](https://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS e controlador de domínio em computadores diferentes

Se você estiver executando o controlador de domínio e o DNs na mesma VM, poderá ignorar este procedimento.


Se o DNS não estiver na mesma VM que o controlador de domínio, você precisará criar uma VM do DNS para o failover de teste. Você pode usar um novo servidor DNS e criar todas as zonas necessárias. Por exemplo, se seu domínio de Active Directory for contoso.com, você poderá criar uma zona DNS com o nome contoso.com. As entradas que correspondem a Active Directory devem ser atualizadas no DNS da seguinte maneira:

1. Verifique se essas configurações estão em vigor antes de qualquer outra máquina virtual no plano de recuperação iniciar:
   * A zona deve ser nomeada após o nome da raiz da floresta.
   * A zona deve ter backup de arquivo.
   * A zona deve ser habilitada para atualizações seguras e não seguras.
   * O resolvedor da máquina virtual que hospeda o controlador de domínio deve apontar para o endereço IP da máquina virtual DNS.

2. Execute o seguinte comando na VM que hospeda o controlador de domínio:

    `nltest /dsregdns`

3. Execute os comandos a seguir para adicionar uma zona no servidor DNS, permitir atualizações não seguras e adicionar uma entrada para a zona para DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [proteger cargas de trabalho corporativas com Azure site Recovery](site-recovery-workload.md).
