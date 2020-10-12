---
title: Configurar a recuperação de desastres do Diretório Ativo/DNS com recuperação do local de Azure
description: Este artigo descreve como implementar uma solução de recuperação de desastres para o Ative Directory e DNS com a Recuperação do Site Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mayg
ms.openlocfilehash: 528a24bb64aa8d323b5d63a27af0a52ccdf1abb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86132320"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Configurar a recuperação de desastres para o Ative Directory e DNS

Aplicações empresariais como SharePoint, Dynamics AX e SAP dependem do Ative Directory e de uma infraestrutura de DNS para funcionar corretamente. Quando configura a recuperação de desastres para aplicações, muitas vezes precisa de recuperar o Ative Directory and Domain Name System (DNS) antes de recuperar outros componentes da aplicação, para garantir a correta funcionalidade da aplicação.

Pode utilizar [a Recuperação do Local](site-recovery-overview.md) para criar um plano de recuperação de desastres para o Ative Directory. Quando ocorre uma perturbação, pode iniciar uma falha. Pode ter o Active Directory a funcionar em apenas alguns minutos. Se implementou o Ative Directory para várias aplicações no seu site principal, por exemplo, para SharePoint e SAP, é melhor falhar no site completo. Pode primeiro falhar sobre o Ative Directory utilizando a Recuperação do Site. Em seguida, falhe sobre as outras aplicações, utilizando planos de recuperação específicos da aplicação.

Este artigo explica como criar uma solução de recuperação de desastres para o Ative Directory. Inclui pré-requisitos e instruções de failover. Deve estar familiarizado com o Ative Directory e a Recuperação do Local antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Se estiver a replicar-se para o Azure, [prepare os recursos do Azure,](tutorial-prepare-azure.md)incluindo uma subscrição, uma Rede Virtual Azure, uma conta de armazenamento e um cofre dos Serviços de Recuperação.
- Reveja os [requisitos de suporte](./vmware-physical-azure-support-matrix.md) para todos os componentes.

## <a name="replicate-the-domain-controller"></a>Replicar o controlador de domínio

- Tem de configurar a replicação da Recuperação do Local, em pelo menos uma máquina virtual (VM) que hospeda um controlador de domínio ou DNS.
- Se tiver vários controladores de domínio no seu ambiente, também deve configurar um controlador de domínio adicional no site alvo. O controlador de domínio adicional pode estar em Azure, ou num centro de dados secundário no local.
- Se tiver apenas algumas aplicações e um controlador de domínio, é melhor falhar em todo o site em conjunto. Neste caso, recomendamos a utilização da Recuperação do Local para replicar o controlador de domínio para o local alvo, seja em Azure ou num centro de dados secundário no local. Pode utilizar o mesmo controlador de domínio replicado ou máquina virtual DNS para [o teste de falha](#test-failover-considerations).
- Se tiver muitas aplicações e mais do que um controlador de domínio no seu ambiente, ou se pretender falhar em algumas aplicações de cada vez, além de replicar a máquina virtual do controlador de domínio com a Recuperação do Local, recomendamos que crie um controlador de domínio adicional no site alvo (seja em Azure ou num centro de dados secundário no local). Para [o teste de falha,](#test-failover-considerations)pode utilizar um controlador de domínio que é replicado pela Recuperação do Local. Para falhar, pode utilizar o controlador de domínio adicional no local alvo.

## <a name="enable-protection-with-site-recovery"></a>Ativar proteção com recuperação do local

Pode utilizar a Recuperação do Site para proteger a máquina virtual que hospeda o controlador de domínio ou o DNS.

### <a name="protect-the-vm"></a>Proteger o VM

O controlador de domínio que é replicado através da utilização da Recuperação do Local é utilizado para [o teste de falha](#test-failover-considerations). Certifique-se de que satisfaz os seguintes requisitos:

1. O controlador de domínio é um servidor de catálogo global.
1. O controlador de domínio deve ser o proprietário flexível de operações master operações únicas (FSMO) para funções que são necessárias durante uma falha de teste. Caso contrário, estas funções terão de ser [apreendidas](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) após o fracasso.

### <a name="configure-vm-network-settings"></a>Configurar configurações de rede VM

Para a máquina virtual que acolhe o controlador de domínio ou DNS, na Recuperação do Site, configurar as definições de rede sob as definições de **Computação e Rede** da máquina virtual replicada. Isto garante que a máquina virtual está ligada à rede correta após a falha.

## <a name="protect-active-directory"></a>Proteger diretório ativo

### <a name="site-to-site-protection"></a>Proteção local-local

Crie um controlador de domínio no local secundário. Quando promover o servidor para uma função de controlador de domínio, especifique o nome do mesmo domínio que está a ser utilizado no site principal. Pode utilizar o snap-in **de Sites e Serviços do Diretório Ativo** para configurar as definições no objeto de ligação do site ao qual os sites são adicionados. Ao configurar as definições numa ligação de site, pode controlar quando a replicação ocorre entre dois ou mais locais e a frequência com que ocorre. Para obter mais informações, consulte [a replicação de agendamento entre os locais.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731862(v=ws.11))

### <a name="site-to-azure-protection"></a>Proteção local-a-azure

Primeiro, crie um controlador de domínio numa rede virtual Azure. Quando promover o servidor para uma função de controlador de domínio, especifique o mesmo nome de domínio que é usado no site principal.

Em seguida, reconfigure o servidor DNS para a rede virtual para utilizar o servidor DNS em Azure.

:::image type="content" source="./media/site-recovery-active-directory/azure-network.png" alt-text="Rede do Azure":::

### <a name="azure-to-azure-protection"></a>Proteção Azure-to-Azure

Primeiro, crie um controlador de domínio numa rede virtual Azure. Quando promover o servidor para uma função de controlador de domínio, especifique o mesmo nome de domínio que é usado no site principal.

Em seguida, reconfigure o servidor DNS para a rede virtual para utilizar o servidor DNS em Azure.

## <a name="test-failover-considerations"></a>Considerações de failover de teste

Para evitar o impacto nas cargas de trabalho de produção, o teste de failover ocorre numa rede isolada da rede de produção.

A maioria das aplicações requer a presença de um controlador de domínio ou de um servidor DNS. Portanto, antes que a aplicação falhe, deve criar um controlador de domínio na rede isolada para ser utilizado para o teste de falha. A maneira mais fácil de o fazer é usar a Recuperação do Site para replicar uma máquina virtual que hospeda um controlador de domínio ou DNS. Em seguida, faça um teste de falha da máquina virtual do controlador de domínio antes de executar um teste de falha do plano de recuperação para a aplicação.

1. Utilize a Recuperação do Site para [replicar](vmware-azure-tutorial.md) a máquina virtual que acolhe o controlador de domínio ou o DNS.
1. Criar uma rede isolada. Qualquer rede virtual que crie no Azure está isolada de outras redes por padrão. Recomendamos que utilize a mesma gama de endereços IP para esta rede que utiliza na sua rede de produção. Não ative a conectividade site-to-site nesta rede.
1. Forneça um endereço IP DNS na rede isolada. Utilize o endereço IP que espera que a máquina virtual DNS obtenha. Se estiver a replicar o Azure, forneça o endereço IP para a máquina virtual que é usada no failover. Para introduzir o endereço IP, na máquina virtual replicada, nas definições **de Computação e Rede,** selecione as definições **IP do Target.**

   :::image type="content" source="./media/site-recovery-active-directory/azure-test-network.png" alt-text="Rede do Azure":::

   > [!TIP]
   > A Recuperação do Site tenta criar máquinas virtuais de teste numa sub-rede com o mesmo nome e utilizando o mesmo endereço IP fornecido nas definições de **Computação e Rede** da máquina virtual. Se uma sub-rede com o mesmo nome não estiver disponível na rede virtual Azure que está prevista para o teste de failover, a máquina virtual de teste é criada na primeira sub-rede alfabeticamente.
   >
   > Se o endereço IP alvo fizer parte da sub-rede selecionada, a Recuperação do Site tenta criar a máquina virtual de falha de teste utilizando o endereço IP alvo. Se o IP-alvo não fizer parte da sub-rede selecionada, a máquina virtual de falha de teste é criada utilizando o próximo IP disponível na sub-rede selecionada.

### <a name="test-failover-to-a-secondary-site"></a>Teste falha em um local secundário

1. Se estiver a replicar-se noutro local e utilizar o DHCP, [instale o DNS e o DHCP para o teste de falha](hyper-v-vmm-test-failover.md#prepare-dhcp).
1. Faça um teste de falha da máquina virtual do controlador de domínio que funciona na rede isolada. Utilize o último ponto de recuperação consistente da _aplicação_ disponível da máquina virtual do controlador de domínio para fazer o teste de falha.
1. Executar um teste de failover para o plano de recuperação que contém máquinas virtuais em que a aplicação funciona.
1. Quando o teste estiver concluído, _limpe a falha de teste_ na máquina virtual do controlador de domínio. Este passo elimina o controlador de domínio que foi criado para o teste de failover.

### <a name="remove-references-to-other-domain-controllers"></a>Remover referências a outros controladores de domínio

Quando iniciar uma falha de teste, não inclua todos os controladores de domínio na rede de teste. Para remover referências a outros controladores de domínio que existem no seu ambiente de produção, poderá ser necessário [apreender as funções de Diretório Ativo da FSMO](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) e fazer [a limpeza de metadados](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816907(v=ws.10)) para controladores de domínio em falta.

### <a name="issues-caused-by-virtualization-safeguards"></a>Problemas causados pelas salvaguardas de virtualização

> [!IMPORTANT]
> Algumas das configurações descritas nesta secção não são configurações padrão ou padrão do controlador de domínio. Se não quiser fazer estas alterações a um controlador de domínio de produção, pode criar um controlador de domínio dedicado à Recuperação do Local para utilizar para o teste de failover. Faça estas alterações apenas no controlador de domínio.

Começando pelo Windows Server 2012, [salvaguardas adicionais são incorporadas nos Serviços de Domínio do Diretório Ativo (DS AD)](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Estas salvaguardas ajudam a proteger os controladores de domínio virtualizados contra as reversão do número de sequência de atualização (USN) se a plataforma de hipervisor subjacente suportar **VM-GenerationID**. Azure suporta **VM-GenerationID**. Por isso, os controladores de domínio que executam o Windows Server 2012 ou mais tarde nas máquinas virtuais Azure têm estas salvaguardas adicionais.

Quando **o VM-GenerationID** é reiniciado, o valor **InvocationID** da base de dados AD DS também é reiniciado. Além disso, a piscina relativa ID (RID) é descartada e `SYSVOL` a pasta é marcada como não autoritária. Para obter mais informações, consulte [a virtualização dos Serviços de Domínio do Diretório Ativo](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) e [a virtualização segura da replicação do sistema de ficheiros distribuídos (DFSR).](https://techcommunity.microsoft.com/t5/storage-at-microsoft/safely-virtualizing-dfsr/ba-p/424671)

Falhar em Azure pode fazer com que **o VM-GenerationID** reinicie. A reposição **do VM-GenerationID** dispara salvaguardas adicionais quando a máquina virtual do controlador de domínio começa em Azure. Isto pode resultar num atraso significativo em conseguir entrar na máquina virtual do controlador de domínio.

Como este controlador de domínio é utilizado apenas num teste falhado, as salvaguardas de virtualização não são necessárias. Para garantir que o valor **VM-GenerationID** para a máquina virtual do controlador de domínio não se altere, pode alterar o valor de seguir `DWORD` para **4** no controlador de domínio no local:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`

#### <a name="symptoms-of-virtualization-safeguards"></a>Sintomas de salvaguardas de virtualização

Se as salvaguardas de virtualização forem ativadas após um teste falhado, poderá ver um ou mais sintomas seguintes:

- O valor **GenerationID** muda:

  :::image type="content" source="./media/site-recovery-active-directory/Event2170.png" alt-text="Rede do Azure":::

- O **valor invocaçãoID** altera:

  :::image type="content" source="./media/site-recovery-active-directory/Event1109.png" alt-text="Rede do Azure":::

- `SYSVOL` pasta e `NETLOGON` ações não estão disponíveis.

  :::image type="content" source="./media/site-recovery-active-directory/sysvolshare.png" alt-text="Rede do Azure":::

  :::image type="content" source="./media/site-recovery-active-directory/Event13565.png" alt-text="Rede do Azure":::

- As bases de dados dfsr são eliminadas.

  :::image type="content" source="./media/site-recovery-active-directory/Event2208.png" alt-text="Rede do Azure":::

### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Problemas no controlador de domínio de resolução de problemas durante a falha do teste

> [!IMPORTANT]
> Algumas das configurações descritas nesta secção não são configurações padrão ou padrão do controlador de domínio. Se não quiser fazer estas alterações a um controlador de domínio de produção, pode criar um controlador de domínio dedicado ao teste de recuperação do local. Faça as alterações apenas para o controlador de domínio dedicado.

1. Na pronta do comando, executar o seguinte comando para verificar se `SYSVOL` a pasta e a pasta são `NETLOGON` partilhadas:

    `NET SHARE`

1. Na pronta do comando, executar o seguinte comando para garantir que o controlador de domínio está a funcionar corretamente:

    `dcdiag /v > dcdiag.txt`

1. No registo de saída, procure o seguinte texto. O texto confirma que o controlador de domínio está a funcionar corretamente.

    - `passed test Connectivity`
    - `passed test Advertising`
    - `passed test MachineAccount`

Se as condições anteriores estiverem satisfeitas, é provável que o controlador de domínio esteja a funcionar corretamente. Se não for, complete os seguintes passos:

1. Faça uma restauração autoritária do controlador de domínio. Tenha em mente as seguintes informações:

    - Embora não recomendemos a replicação utilizando o Serviço de Replicação de [Ficheiros (FRS),](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-end-is-nigh-for-frs-8211-updated-for-ws2016/ba-p/425379)se utilizar a replicação de FRS, siga os passos para uma restauração autorizada. O processo é descrito na [utilização da chave de registo da BurFlags para reiniciar o Serviço de Replicação de Ficheiros](https://support.microsoft.com/kb/290762).

      Para mais informações sobre a BurFlags, consulte o blog post [D2 e D4: Para que é?](/archive/blogs/janelewis/d2-and-d4-what-is-it-for)

    - Se utilizar a replicação DFSR, complete os passos para uma restauração autorizada. O processo é descrito em [Força uma sincronização autoritária e não autorizada para a pasta SYSVOL replicada pelo DFSR (como "D4/D2" para FRS)](https://support.microsoft.com/kb/2218556).

      Também pode utilizar as funções PowerShell. Para obter mais informações, consulte [as funções de restauro autorizado/não autorizado da DFSR/SYSVOL](/archive/blogs/thbouche/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions).

1. Contornar o requisito inicial de sincronização definindo a seguinte chave de registo para **0** no controlador de domínio no local. Se o `DWORD` não existir, pode criá-lo sob o nó **parâmetros.**

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

   Para obter mais informações, consulte [o Problema dns Event ID 4013: O servidor DNS não foi capaz de carregar zonas DNS integradas AD](https://support.microsoft.com/kb/2001093).

1. Desative o requisito de que um servidor de catálogo global esteja disponível para validar o login do utilizador. Para tal, no controlador de domínio no local, estaba o seguinte ponto de registo para **1**. Se o `DWORD` não existir, pode criá-lo sob o nó **Lsa.**

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

   Para mais informações, consulte [como funciona o Catálogo Global.](/previous-versions/windows/it-pro/windows-server-2003/cc737410(v=ws.10))

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS e controlador de domínio em diferentes máquinas

Se estiver a executar o controlador de domínio e dNs no mesmo VM, pode ignorar este procedimento.

Se o DNS não estiver no mesmo VM que o controlador de domínio, é necessário criar um DNS VM para o teste de falha. Pode utilizar um servidor DNS fresco e criar todas as zonas necessárias. Por exemplo, se o seu domínio ative directory `contoso.com` for, pode criar uma zona DNS com o nome `contoso.com` . As entradas que correspondam ao Ative Directory devem ser atualizadas em DNS da seguinte forma:

1. Certifique-se de que estas definições estão em vigor antes de qualquer outra máquina virtual no plano de recuperação começar:

   - A zona deve ser nomeada em homenagem ao nome da raiz da floresta.
   - A zona deve ser apoiada por ficheiros.
   - A zona deve ser ativada para atualizações seguras e não seguras.
   - A resolução da máquina virtual que acolhe o controlador de domínio deve apontar para o endereço IP da máquina virtual DNS.

1. Executar o seguinte comando no VM que acolhe o controlador de domínio:

   `nltest /dsregdns`

1. Executar os seguintes comandos para adicionar uma zona no servidor DNS, permitir atualizações nãosecuíveis e adicionar uma entrada para a zona ao DNS:

   ```cmd
   dnscmd /zoneadd contoso.com  /Primary

   dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1

   dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>

   dnscmd /config contoso.com /allowupdate 1
   ```

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](site-recovery-workload.md) sobre como proteger as cargas de trabalho da empresa com a Recuperação do Site Azure.
