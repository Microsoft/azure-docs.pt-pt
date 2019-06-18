---
title: Configurar a recuperação de desastres do Active Directory e DNS com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como implementar uma solução de recuperação após desastre para o Active Directory e DNS com o Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 58e360bb355c7faf9608b00dd65b14f27aca4367
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61038845"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Configurar a recuperação de desastres do Active Directory e DNS

Aplicações empresariais, como SharePoint, Dynamics AX e o SAP dependem do Active Directory e uma infraestrutura DNS para funcionar corretamente. Quando configurar a recuperação após desastre para aplicações, com freqüência precisar de recuperar o Active Directory e DNS antes de os recuperar outros componentes da aplicação, para garantir que a funcionalidade de aplicação correta.

Pode usar [recuperação de Site](site-recovery-overview.md) para criar um plano de recuperação após desastre para o Active Directory. Quando ocorre uma interrupção, pode iniciar uma ativação pós-falha. Pode ter-se ao Active Directory cópia de segurança e em execução dentro de alguns minutos. Se tiver implementado o Active Directory para várias aplicações no seu site primário, por exemplo, para o SharePoint e SAP, poderá fazer a ativação pós-falha do site concluído. Em primeiro lugar pode efetuar a ativação pós-falha do Active Directory com o Site Recovery. Em seguida, a ativação pós-falha nos outros aplicativos, com planos de recuperação específico do aplicativo.

Este artigo explica como criar uma solução de recuperação após desastre para o Active Directory. Inclui pré-requisitos e instruções de ativação pós-falha. Deve estar familiarizado com o Active Directory e o Site Recovery antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Se estiver a replicar para o Azure, [preparar recursos do Azure](tutorial-prepare-azure.md), incluindo uma subscrição, uma rede Virtual do Azure, uma conta de armazenamento e um cofre dos serviços de recuperação.
* Reveja os [requisitos de suporte](site-recovery-support-matrix-to-azure.md) de todos os componentes.

## <a name="replicate-the-domain-controller"></a>Replicar o controlador de domínio

- Tem de definir a replicação do Site Recovery, em pelo menos uma VM que aloja um controlador de domínio ou DNS.
- Se tiver vários controladores de domínio no seu ambiente, também tem de configurar um controlador de domínio adicionais no site de destino. O controlador de domínio adicional pode ser no Azure ou num datacenter secundário no local.
- Se tiver apenas alguns aplicativos e um controlador de domínio, pode querer fazer a ativação pós-falha de todo o site em conjunto. Neste caso, recomendamos utilizar o Site Recovery para replicar o controlador de domínio para o site de destino (seja no Azure ou num datacenter secundário no local). Pode utilizar o mesmo controlador de domínio replicados ou a máquina virtual DNS para [ativação pós-falha de teste](#test-failover-considerations).
- - Se tiver muitos aplicativos e mais do que um controlador de domínio no seu ambiente ou, se planeja fazer a ativação pós-falha alguns aplicativos cada vez, além para replicar a máquina de virtual do controlador de domínio com o Site Recovery, recomendamos que configure um controlador de domínio adicional no site de destino (seja no Azure ou num datacenter secundário no local). Para [ativação pós-falha de teste](#test-failover-considerations), pode usar o controlador de domínio que é replicado pelo Site Recovery. Para ativação pós-falha, pode utilizar o controlador de domínio adicionais no site de destino.

## <a name="enable-protection-with-site-recovery"></a>Ativar a proteção com o Site Recovery

Pode utilizar o Site Recovery para proteger a máquina virtual que aloja o controlador de domínio ou DNS.

### <a name="protect-the-vm"></a>Proteger a VM
O controlador de domínio que é replicado utilizando a recuperação de Site é utilizado para [ativação pós-falha de teste](#test-failover-considerations). Certifique-se de que cumpre os seguintes requisitos:

1. O controlador de domínio é um servidor de catálogo global.
2. O controlador de domínio deve ser o proprietário de função FSMO para as funções que são necessários durante uma ativação pós-falha de teste. Caso contrário, estas funções terão de ser [confiscada](https://aka.ms/ad_seize_fsmo) após a ativação pós-falha.

### <a name="configure-vm-network-settings"></a>Configurar as definições de rede VM
Para a máquina virtual que aloja o controlador de domínio ou DNS, no Site Recovery, configurar as definições de rede sob o **computação e rede** definições da máquina virtual replicada. Isto garante que a máquina virtual está ligada à rede correta após a ativação pós-falha.

## <a name="protect-active-directory"></a>Proteger o Active Directory

### <a name="site-to-site-protection"></a>Proteção de site a site
Crie um controlador de domínio no site secundário. Quando promover o servidor para uma função de controlador de domínio, especifique o nome do mesmo domínio que está a ser utilizado no site primário. Pode utilizar o **serviços e locais do Active Directory** snap-in para configurar as definições no objeto de ligação do site ao qual os sites são adicionados. Ao configurar as definições de uma ligação de site, pode controlar quando ocorre a replicação entre dois ou mais sites e com que frequência ocorre. Para obter mais informações, consulte [agendar a replicação entre sites](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Proteção de site para o Azure
Primeiro, crie um controlador de domínio numa rede virtual do Azure. Quando promover o servidor para uma função de controlador de domínio, especifique o mesmo nome de domínio que é utilizado no site primário.

Em seguida, reconfigure o servidor DNS para a rede virtual utilizar o servidor DNS no Azure.

![Rede do Azure](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Proteção do Azure para o Azure
Primeiro, crie um controlador de domínio numa rede virtual do Azure. Quando promover o servidor para uma função de controlador de domínio, especifique o mesmo nome de domínio que é utilizado no site primário.

Em seguida, reconfigure o servidor DNS para a rede virtual utilizar o servidor DNS no Azure.

## <a name="test-failover-considerations"></a>Considerações de ativação pós-falha de teste
Para evitar o impacto nas cargas de trabalho de produção, a ativação pós-falha de teste ocorre numa rede isolada da rede de produção.

Maioria dos aplicativos exige a presença de um controlador de domínio ou um servidor DNS. Por conseguinte, antes do aplicativo falha, tem de criar um controlador de domínio na rede isolada a ser utilizado para ativação pós-falha de teste. A maneira mais fácil de fazê-lo é utilizar o Site Recovery para replicar uma máquina virtual que aloja um controlador de domínio ou DNS. Em seguida, execute uma ativação pós-falha de teste da máquina de virtual de controlador de domínio antes de executar uma ativação pós-falha de teste do plano de recuperação para a aplicação. Eis como fazer isso:

1. Utilize o Site Recovery para [replicar](vmware-azure-tutorial.md) a máquina virtual que aloja o controlador de domínio ou DNS.
2. Crie uma rede isolada. Qualquer rede virtual que criar no Azure é isolada de outras redes por predefinição. Recomendamos que utilize o mesmo intervalo de endereços IP para esta rede que utiliza na sua rede de produção. Não permitir a conectividade de site a site nesta rede.
3. Forneça um endereço IP de DNS na rede isolada. Utilize o endereço IP que pretende que a máquina virtual DNS para obter. Se estiver a replicar para o Azure, forneça o endereço IP para a máquina virtual que é usada na ativação pós-falha. Introduza o endereço IP, na máquina virtual replicada, no **computação e rede** definições, selecione o **IP de destino** definições.

    ![Rede de teste do Azure](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > O site Recovery tenta criar máquinas virtuais de teste numa sub-rede, o mesmo nome e ao utilizar o mesmo endereço IP que é fornecido na **computação e rede** definições da máquina virtual. Se uma sub-rede, o mesmo nome não está disponível na rede virtual do Azure que é fornecida para a ativação pós-falha de teste, a máquina virtual de teste é criada na sub-rede por ordem alfabética primeira.
    >
    > Se o endereço IP de destino é parte da sub-rede selecionada, o Site Recovery tenta criar a máquina de virtual de ativação pós-falha de teste com o endereço IP de destino. Se o IP de destino não faz parte da sub-rede selecionada, a máquina de virtual de ativação pós-falha de teste é criada utilizando o próximo IP disponível na sub-rede selecionada.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Ativação pós-falha de teste para um site secundário

1. Se estiver a replicar para outro site no local e utilizar o DHCP, [configurar o DNS e DHCP para ativação pós-falha de teste](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Fazer uma ativação pós-falha de teste da máquina de virtual de controlador de domínio que é executado na rede isolada. Utilizar a mais recente disponível *consistente com a aplicação* ponto de recuperação da máquina de virtual do controlador de domínio para fazer a ativação pós-falha de teste.
3. Execute uma ativação pós-falha para o plano de recuperação que contém as máquinas virtuais que o aplicativo é executado.
4. Quando o teste é concluído, *limpar ativação pós-falha de teste* na máquina de virtual do controlador de domínio. Este passo elimina o controlador de domínio que foi criado para ativação pós-falha de teste.


### <a name="remove-references-to-other-domain-controllers"></a>Remover as referências a outros controladores de domínio
Quando iniciar uma ativação pós-falha de teste, não inclua todos os controladores de domínio na rede de teste. Para remover as referências a outros controladores de domínio existentes no seu ambiente de produção, poderá ter de [obter funções de diretório Active Directory de FSMO](https://aka.ms/ad_seize_fsmo) e fazer [limpeza de metadados](https://technet.microsoft.com/library/cc816907.aspx) de ausentes controladores de domínio .


### <a name="issues-caused-by-virtualization-safeguards"></a>Problemas causados pelas proteções de Virtualização

> [!IMPORTANT]
> Algumas das configurações descritas nesta secção não são padrão ou predefinição configurações de controlador de domínio. Se não pretender efetuar estas alterações para um controlador de domínio de produção, pode criar um controlador de domínio dedicado para a recuperação de Site a utilizar para ativação pós-falha de teste. Efetue estas alterações apenas a esse controlador de domínio.  
>
>

A partir do Windows Server 2012, [as proteções adicionais são baseiam-se em serviços de domínio do Active Directory (AD DS)](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Estas salvaguardas ajudar a proteger os controladores de domínio virtualizados contra reversões de USN se suportar a plataforma de hipervisor subjacente **VM-GenerationID**. O Azure suporta **VM-GenerationID**. Por este motivo, os controladores de domínio que executam o Windows Server 2012 ou posterior em máquinas virtuais têm estas salvaguardas adicionais.


Quando **VM-GenerationID** é reposta, o **InvocationID** também é reposto o valor de base de dados do AD DS. Além disso, o conjunto de RID é descartado e pasta sysvol é marcada como não autoritativo. Para obter mais informações, consulte [introdução à virtualização de serviços de domínio do Active Directory](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) e [com segurança virtualizar o DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Realizar a ativação pós-falha para o Azure pode causar **VM-GenerationID** repor. A repor **VM-GenerationID** aciona proteções adicionais, quando a máquina virtual do controlador de domínio é iniciado no Azure. Isto pode resultar numa *atraso significativo* ser capaz de iniciar sessão na máquina virtual do controlador de domínio.

Uma vez que este controlador de domínio é usado apenas uma ativação pós-falha de teste, as proteções de Virtualização não são necessárias. Para garantir que o **VM-GenerationID** não altera o valor para a máquina de virtual do controlador de domínio, pode alterar o valor de DWORD seguinte para **4** no controlador de domínio no local:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Sintomas de proteções de Virtualização

Se as proteções de Virtualização são acionadas após uma ativação pós-falha de teste, poderá ver um ou mais dos sintomas seguintes:  

* O **GenerationID** alterações de valor.

    ![Alteração do ID de geração](./media/site-recovery-active-directory/Event2170.png)

* O **InvocationID** alterações de valor.

    ![Alteração do ID de invocação](./media/site-recovery-active-directory/Event1109.png)

* Pasta SYSVOL e NETLOGON partilhas não estão disponíveis.

    ![Partilha de pasta SYSVOL](./media/site-recovery-active-directory/sysvolshare.png)

    ![Pasta de sysvol do NtFrs](./media/site-recovery-active-directory/Event13565.png)

* Bases de dados DFSR são eliminados.

    ![Bases de dados DFSR são eliminados](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Resolver problemas de controlador de domínio durante a ativação pós-falha de teste

> [!IMPORTANT]
> Algumas das configurações descritas nesta secção não são padrão ou predefinição configurações de controlador de domínio. Se não pretender efetuar estas alterações para um controlador de domínio de produção, pode criar um controlador de domínio dedicado para ativação pós-falha de teste de recuperação de sites. Faça as alterações apenas a esse controlador de domínio dedicado.  
>
>

1. No prompt de comando, execute o seguinte comando para verificar se a pasta sysvol e a pasta NETLOGON são partilhados:

    `NET SHARE`

2. No prompt de comando, execute o seguinte comando para se certificar de que o controlador de domínio está a funcionar corretamente:

    `dcdiag /v > dcdiag.txt`

3. No registo de saída, procure o seguinte texto. O texto confirma que o controlador de domínio está a funcionar corretamente.

    * "passou no teste conectividade"
    * "passou no teste de publicidade"
    * "passou no teste MachineAccount"

Se forem satisfeitas as condições anteriores, é provável que o controlador de domínio está a funcionar corretamente. Se não for, conclua os seguintes passos:

1. Fazer uma restauração autoritativa do controlador de domínio. Considere as seguintes informações:
    * Embora não o recomendamos [replicação de FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), se usar a replicação de FRS, siga os passos para uma restauração autoritativa. O processo é descrito em [usando a chave de registo BurFlags para serviço de replicação de ficheiros](https://support.microsoft.com/kb/290762).

        Para obter mais informações sobre BurFlags, consulte a mensagem de blogue [D2 e D4: Para que ele serve? ](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Se utilizar a replicação de DFSR, conclua os passos para uma restauração autoritativa. O processo é descrito em [forçar uma sincronização autoritativa e não autoritativa para a pasta sysvol replicado por DFSR (como "D4/D2" para FRS)](https://support.microsoft.com/kb/2218556).

        Também pode utilizar as funções do PowerShell. Para obter mais informações, consulte [funções do PowerShell de autoritativo/restauro não autoritativo de DFSR SYSVOL](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. Contornar o requisito de sincronização inicial, definindo a seguinte chave de registo **0** no controlador de domínio no local. Se o DWORD não existir, pode criar-o para baixo a **parâmetros** nó.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Para obter mais informações, consulte [resolver 4013 de ID de evento de DNS: O servidor DNS não foi possível carregar AD integrado zonas DNS](https://support.microsoft.com/kb/2001093).

3. Desabilitar o requisito de que um servidor de catálogo global estejam disponíveis para validar o início de sessão do utilizador. Para tal, no controlador de domínio no local, defina a seguinte chave de registo **1**. Se o DWORD não existir, pode criar-o para baixo a **Lsa** nó.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Para obter mais informações, consulte [desabilitar o requisito de que um servidor de catálogo global estejam disponíveis para validar os inícios de sessão do utilizador](https://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS e controlador de domínio em máquinas diferentes

Se estiver a executar o controlador de domínio e o DNs na mesma VM, pode ignorar este procedimento.


Se o DNS não estiver na mesma VM como o controlador de domínio, tem de criar uma VM de DNS para a ativação pós-falha de teste. Pode utilizar um servidor DNS de raiz e criar todas as zonas. Por exemplo, se o domínio do Active Directory for contoso.com, pode criar uma zona DNS com o nome contoso.com. As entradas que correspondem ao Active Directory têm de ser atualizadas no DNS da seguinte forma:

1. Certifique-se de que estas definições são cumpridos antes de iniciar qualquer outra máquina virtual no plano de recuperação:
   * A zona tem de ser o nome depois do nome de raiz de floresta.
   * A zona tem de ser de segurança de ficheiros.
   * A zona tem de estar ativada para atualizações de seguras e não seguras.
   * O resolvedor da máquina virtual que aloja o controlador de domínio deve apontar para o endereço IP da máquina de virtual DNS.

2. Execute o seguinte comando na VM que aloja o controlador de domínio:

    `nltest /dsregdns`

3. Execute os seguintes comandos para adicionar uma zona no servidor DNS, permitir atualizações não seguras e adicionar uma entrada para a zona no DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [proteger cargas de trabalho empresariais com o Azure Site Recovery](site-recovery-workload.md).
