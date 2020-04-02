---
title: Configurar a recuperação de desastres de diretório/DNS ativo com a recuperação do site Azure
description: Este artigo descreve como implementar uma solução de recuperação de desastres para Ative Directory e DNS com A Recuperação do Site Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mayg
ms.openlocfilehash: 2cf4f22be2a4407d73fcc7bb340fad647c8aa145
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546522"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Configurar a recuperação de desastres para o Ative Directory e o DNS

Aplicações empresariais como SharePoint, Dynamics AX e SAP dependem do Ative Directory e de uma infraestrutura DNS para funcionar corretamente. Ao configurar a recuperação de desastres para aplicações, muitas vezes precisa de recuperar o Ative Directory e o Domain Name System (DNS) antes de recuperar outros componentes da aplicação, para garantir a correta funcionalidade da aplicação.

Você pode usar [a Recuperação do Site](site-recovery-overview.md) para criar um plano de recuperação de desastres para O Diretório Ativo. Quando ocorre uma perturbação, pode iniciar uma falha. Pode ter o Active Directory a funcionar em apenas alguns minutos. Se implementou o Ative Directory para várias aplicações no seu site principal, por exemplo, para o SharePoint e sAP, poderá querer falhar sobre o site completo. Pode primeiro falhar sobre o Ative Directory utilizando a Recuperação do Site. Em seguida, falhe sobre as outras aplicações, utilizando planos de recuperação específicos da aplicação.

Este artigo explica como criar uma solução de recuperação de desastres para o Ative Directory. Inclui pré-requisitos e instruções de failover. Deve estar familiarizado com o Diretório Ativo e a Recuperação do Site antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Se estiver a replicar-se para o Azure, [prepare os recursos do Azure](tutorial-prepare-azure.md), incluindo uma subscrição, uma Rede Virtual Azure, uma conta de armazenamento e um cofre dos Serviços de Recuperação.
- Reveja os [requisitos de suporte](site-recovery-support-matrix-to-azure.md) de todos os componentes.

## <a name="replicate-the-domain-controller"></a>Replicar o controlador de domínio

- Deve configurar a replicação de Recuperação do Local, em pelo menos uma máquina virtual (VM) que acolhe um controlador de domínio ou DNS.
- Se tiver vários controladores de domínio no seu ambiente, também deve configurar um controlador de domínio adicional no local do alvo. O controlador de domínio adicional pode estar em Azure, ou num centro de dados secundário no local.
- Se tiver apenas algumas aplicações e um controlador de domínio, talvez queira falhar em todo o site juntos. Neste caso, recomendamos a utilização da Recuperação do Site para replicar o controlador de domínio para o local alvo, seja em Azure ou num centro de dados secundário no local. Pode utilizar o mesmo controlador de domínio replicado ou máquina virtual DNS para [o teste de failover](#test-failover-considerations).
- Se tiver muitas aplicações e mais de um controlador de domínio no seu ambiente, ou se planeia falhar em algumas aplicações de cada vez, além de replicar a máquina virtual do controlador de domínio com a Recuperação do Site, recomendamos que instale um controlador de domínio adicional no local alvo (seja em Azure ou num centro de dados secundário no local). Para [o failover](#test-failover-considerations)do teste, pode utilizar um controlador de domínio que é replicado pela Recuperação do Site. Para o failover, pode utilizar o controlador de domínio adicional no local do alvo.

## <a name="enable-protection-with-site-recovery"></a>Ativar proteção com recuperação do site

Pode utilizar a Recuperação do Site para proteger a máquina virtual que acolhe o controlador de domínio ou o DNS.

### <a name="protect-the-vm"></a>Proteja o VM

O controlador de domínio que é replicado utilizando a Recuperação do Local é utilizado para [o teste de failover](#test-failover-considerations). Certifique-se de que satisfaz os seguintes requisitos:

1. O controlador de domínio é um servidor de catálogo global.
1. O controlador de domínio deve ser o titular flexível de operações master individuais (FSMO) para funções que são necessárias durante uma falha de teste. Caso contrário, estas funções terão de ser [apreendidas](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) após a falha.

### <a name="configure-vm-network-settings"></a>Configurar as definições da rede VM

Para a máquina virtual que acolhe o controlador de domínio ou DNS, na Recuperação do Site, configurar as definições de rede sob as definições **de Compute e Rede** da máquina virtual replicada. Isto garante que a máquina virtual está ligada à rede correta após a falha.

## <a name="protect-active-directory"></a>Proteja o Diretório Ativo

### <a name="site-to-site-protection"></a>Proteção local-a-local

Crie um controlador de domínio no local secundário. Quando promover o servidor para uma função de controlador de domínio, especifique o nome do mesmo domínio que está a ser usado no site principal. Pode utilizar o snap-in **ative Directory Sites e Services** para configurar as definições no objeto de ligação do site ao qual os sites são adicionados. Ao configurar as definições numa ligação do site, pode controlar quando a replicação ocorre entre dois ou mais sites e a frequência com que ocorre. Para mais informações, consulte [a replicação de Agendamento entre os sites](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731862(v=ws.11)).

### <a name="site-to-azure-protection"></a>Proteção local-a-Azure

Primeiro, crie um controlador de domínio numa rede virtual Azure. Quando promover o servidor para uma função de controlador de domínio, especifique o mesmo nome de domínio que é usado no site principal.

Em seguida, reconfigure o servidor DNS para a rede virtual utilizar o servidor DNS em Azure.

:::image type="content" source="./media/site-recovery-active-directory/azure-network.png" alt-text="Rede Azure":::

### <a name="azure-to-azure-protection"></a>Proteção Azure-to-Azure

Primeiro, crie um controlador de domínio numa rede virtual Azure. Quando promover o servidor para uma função de controlador de domínio, especifique o mesmo nome de domínio que é usado no site principal.

Em seguida, reconfigure o servidor DNS para a rede virtual utilizar o servidor DNS em Azure.

## <a name="test-failover-considerations"></a>Teste de failover considerações

Para evitar o impacto nas cargas de trabalho de produção, o teste ocorre numa rede isolada da rede de produção.

A maioria das aplicações requer a presença de um controlador de domínio ou de um servidor DNS. Portanto, antes que a aplicação falhe, deve criar um controlador de domínio na rede isolada para ser utilizado para o teste de falha. A maneira mais fácil de o fazer é usar a Recuperação do Site para replicar uma máquina virtual que acolhe um controlador de domínio ou DNS. Em seguida, faça uma falha de teste da máquina virtual do controlador de domínio antes de executar uma falha de teste do plano de recuperação para a aplicação.

1. Utilize a Recuperação do Site para [replicar](vmware-azure-tutorial.md) a máquina virtual que acolhe o controlador de domínio ou o DNS.
1. Criar uma rede isolada. Qualquer rede virtual que cria em Azure está isolada de outras redes por padrão. Recomendamos que utilize a mesma gama de endereços IP para esta rede que utiliza na sua rede de produção. Não permita a conectividade site-a-site nesta rede.
1. Forneça um endereço IP DNS na rede isolada. Utilize o endereço IP que espera que a máquina virtual DNS obtenha. Se estiver a replicar-se para o Azure, forneça o endereço IP para a máquina virtual que é usada no failover. Para introduzir o endereço IP, na máquina virtual replicada, nas definições **computacionais e** de rede, selecione as definições **ip do Target.**

   :::image type="content" source="./media/site-recovery-active-directory/azure-test-network.png" alt-text="Rede de testes Azure":::

   > [!TIP]
   > A Recuperação do Site tenta criar máquinas virtuais de teste numa sub-rede com o mesmo nome e utilizando o mesmo endereço IP que é fornecido nas definições **computacionais e** de rede da máquina virtual. Se uma sub-rede com o mesmo nome não estiver disponível na rede virtual Azure que está prevista para o teste failover, a máquina virtual de teste é criada na primeira sub-rede alfabética.
   >
   > Se o endereço IP alvo fizer parte da subnet selecionada, a Recuperação do Site tenta criar a máquina virtual failover do teste utilizando o endereço IP alvo. Se o IP alvo não fizer parte da sub-rede selecionada, a máquina virtual failover do teste é criada utilizando o próximo IP disponível na sub-rede selecionada.

### <a name="test-failover-to-a-secondary-site"></a>Teste failover para um local secundário

1. Se estiver a replicar-se noutro local e utilizar o DHCP, [instale DNS e DHCP para o teste de failover](hyper-v-vmm-test-failover.md#prepare-dhcp).
1. Faça uma falha de teste da máquina virtual do controlador de domínio que funciona na rede isolada. Utilize o mais recente ponto de recuperação consistente da _aplicação_ disponível da máquina virtual do controlador de domínio para fazer o teste de failover.
1. Executar uma falha de teste para o plano de recuperação que contém máquinas virtuais em que a aplicação funciona.
1. Quando os testes estiverem concluídos, _limpe a falha do teste_ na máquina virtual do controlador de domínio. Este passo elimina o controlador de domínio que foi criado para a falha do teste.

### <a name="remove-references-to-other-domain-controllers"></a>Remover referências a outros controladores de domínio

Quando iniciar uma falha no teste, não inclua todos os controladores de domínio na rede de teste. Para remover referências a outros controladores de domínio que existem no seu ambiente de produção, poderá ser necessário [apreender funções de Diretório Ativo FSMO](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) e fazer a limpeza de [metadados](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816907(v=ws.10)) para controladores de domínio em falta.

### <a name="issues-caused-by-virtualization-safeguards"></a>Questões causadas por salvaguardas de virtualização

> [!IMPORTANT]
> Algumas das configurações descritas nesta secção não são configurações padrão ou padrão do controlador de domínio. Se não quiser efazer estas alterações num controlador de domínio de produção, pode criar um controlador de domínio dedicado à Recuperação do Site para utilizar para o teste failover. Faça estas alterações apenas para o controlador de domínio.

A partir do Windows Server 2012, [as salvaguardas adicionais são incorporadas nos Serviços de Domínio de Diretório Ativo (AD DS)](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Estas salvaguardas ajudam a proteger os controladores de domínio virtualizados contra os relançamentos do número de sequência de atualização (USN) se a plataforma hipervisor subjacente suportar **o VM-GenerationID**. Azure suporta **VM-GenerationID**. Por isso, os controladores de domínio que executam o Windows Server 2012 ou mais tarde em máquinas virtuais Azure têm estas salvaguardas adicionais.

Quando o **VM-GenerationID** é reposto, o valor **invocacional** da base de dados AD DS também é reposto. Além disso, o conjunto relativo de ID `SYSVOL` (RID) é descartado, e a pasta é marcada como não autorizada. Para mais informações, consulte [introdução à virtualização](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) dos Serviços de Domínio de Diretório Ativo e virtualizando de forma segura a replicação do sistema de [ficheiros distribuídos (DFSR)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/safely-virtualizing-dfsr/ba-p/424671).

Falhar com o Azure pode fazer com que o **VM-GenerationID** reinstale. A reposição **do VM-GenerationID** aciona salvaguardas adicionais quando a máquina virtual do controlador de domínio começa em Azure. Isto pode resultar num atraso significativo em poder entrar na máquina virtual do controlador de domínio.

Como este controlador de domínio é usado apenas numa falha de teste, as salvaguardas de virtualização não são necessárias. Para garantir que o valor **VM-GenerationID** para a máquina virtual do controlador `DWORD` de domínio não se altere, pode alterar o valor do seguinte para **4** no controlador de domínio no local:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`

#### <a name="symptoms-of-virtualization-safeguards"></a>Sintomas de salvaguardas de virtualização

Se as salvaguardas de virtualização forem desencadeadas após uma falha no teste, poderá ver um ou mais dos seguintes sintomas:

- O valor **generationID** muda:

  :::image type="content" source="./media/site-recovery-active-directory/Event2170.png" alt-text="Mudança de ID de geração":::

- O valor **do InvocationID** altera:

  :::image type="content" source="./media/site-recovery-active-directory/Event1109.png" alt-text="Alteração do ID de invocação":::

- `SYSVOL`pasta `NETLOGON` e ações não estão disponíveis.

  :::image type="content" source="./media/site-recovery-active-directory/sysvolshare.png" alt-text="Partilha de pasta SYSVOL":::

  :::image type="content" source="./media/site-recovery-active-directory/Event13565.png" alt-text="Pasta NtFrs SYSVOL":::

- As bases de dados da DFSR são eliminadas.

  :::image type="content" source="./media/site-recovery-active-directory/Event2208.png" alt-text="As bases de dados do DFSR são eliminadas":::

### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Problemas do controlador de domínio de resolução de problemas durante a falha do teste

> [!IMPORTANT]
> Algumas das configurações descritas nesta secção não são configurações padrão ou padrão do controlador de domínio. Se não quiser fazer estas alterações para um controlador de domínio de produção, pode criar um controlador de domínio dedicado ao teste de recuperação do site. Faça as alterações apenas para o controlador de domínio dedicado.

1. No pedido de comando, execute o `SYSVOL` seguinte `NETLOGON` comando para verificar se a pasta e a pasta são partilhadas:

    `NET SHARE`

1. No pedido de comando, execute o seguinte comando para garantir que o controlador de domínio está a funcionar corretamente:

    `dcdiag /v > dcdiag.txt`

1. No registo de saída, procure o seguinte texto. O texto confirma que o controlador de domínio está a funcionar corretamente.

    - `passed test Connectivity`
    - `passed test Advertising`
    - `passed test MachineAccount`

Se as condições anteriores estiverem satisfeitas, é provável que o controlador de domínio esteja a funcionar corretamente. Se não for, complete os seguintes passos:

1. Faça uma restauração autoritária do controlador de domínio. Tenha em mente as seguintes informações:

    - Embora não recomendemos a replicação utilizando o Serviço de Replicação de [Ficheiros (FRS)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-end-is-nigh-for-frs-8211-updated-for-ws2016/ba-p/425379)se utilizar a replicação frs, siga os passos para uma restauração autoritária. O processo é descrito na utilização da chave de [registo BurFlags para reinicializar o Serviço](https://support.microsoft.com/kb/290762)de Replicação de Ficheiros .

      Para mais informações sobre a BurFlags, consulte o post de blog [D2 e D4: Para que é?](/archive/blogs/janelewis/d2-and-d4-what-is-it-for)

    - Se utilizar a replicação do DFSR, complete os passos para uma restauração autoritária. O processo é descrito na [Força como uma sincronização autoritária e não autoritária para a pasta SYSVOL replicada pela DFSR (como "D4/D2" para FRS)](https://support.microsoft.com/kb/2218556).

      Também pode utilizar as funções PowerShell. Para mais informações, consulte [funções de restauração powerShell autorizadas/não autorizadas da DFSR-SYSVOL](/archive/blogs/thbouche/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions).

1. Contorne a exigência inicial de sincronização, definindo a seguinte chave de registo para **0** no controlador de domínio no local. Se `DWORD` não existir, pode criá-lo sob o nó dos **Parâmetros.**

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

   Para mais informações, consulte [Troubleshoot DNS Event ID 4013: O servidor DNS não foi capaz de carregar zonas DNS integradas](https://support.microsoft.com/kb/2001093)de AD .

1. Desative a exigência de que um servidor de catálogo global esteja disponível para validar o login do utilizador. Para tal, no controlador de domínio no local, der a seguinte chave de registo para **1**. Se `DWORD` não existir, pode criá-lo sob o nó **de Lsa.**

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

   Para mais informações, consulte [como funciona o Catálogo Global.](/previous-versions/windows/it-pro/windows-server-2003/cc737410(v=ws.10))

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS e controlador de domínio em diferentes máquinas

Se estiver a executar o controlador de domínio e os DNs no mesmo VM, pode ignorar este procedimento.

Se o DNS não estiver no mesmo VM que o controlador de domínio, precisa de criar um DNS VM para a falha do teste. Pode utilizar um servidor DNS fresco e criar todas as zonas necessárias. Por exemplo, se o seu `contoso.com`domínio de Diretório Ativo for, pode criar uma zona DNS com o nome `contoso.com`. As entradas correspondentes ao Diretório Ativo devem ser atualizadas no DNS da seguinte forma:

1. Certifique-se de que estas definições estão em vigor antes de qualquer outra máquina virtual do plano de recuperação começar:

   - A zona deve ser nomeada em homenagem ao nome da raiz da floresta.
   - A zona deve ser apoiada por ficheiros.
   - A zona deve ser ativada para atualizações seguras e não seguras.
   - A resolver da máquina virtual que acolhe o controlador de domínio deve apontar para o endereço IP da máquina virtual DNS.

1. Executar o seguinte comando no VM que acolhe o controlador de domínio:

   `nltest /dsregdns`

1. Executar os seguintes comandos para adicionar uma zona no servidor DNS, permitir atualizações não seguras e adicionar uma entrada para a zona ao DNS:

   ```cmd
   dnscmd /zoneadd contoso.com  /Primary

   dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1

   dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>

   dnscmd /config contoso.com /allowupdate 1
   ```

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](site-recovery-workload.md) sobre a proteção de cargas de trabalho empresariais com a Recuperação do Site Azure.
