---
title: Resolução de problemas da réplica do Azure VM na recuperação do site do Azure
description: Erros de resolução de problemas ao replicar máquinas virtuais Azure para recuperação de desastres.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 9f95677211a89c1bb2a18076741dec08e76c5d70
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390404"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Erro de replicação de VM Azure-to-Azure

Este artigo descreve como resolver erros comuns na Recuperação do Site Azure durante a replicação e recuperação de máquinas virtuais Azure (VM) de uma região para outra. Para obter mais informações sobre configurações suportadas, consulte a matriz de [suporte para replicar VMs Azure](azure-to-azure-support-matrix.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemas de quotas de recursos Azure (código de erro 150097)

Certifique-se de que a sua subscrição está ativada para criar VMs Azure na região alvo que pretende utilizar como a sua região de recuperação de desastres (DR). A sua subscrição necessita de quota suficiente para criar VMs dos tamanhos necessários. Por padrão, a Recuperação do Site escolhe um tamanho VM alvo que é o mesmo que o tamanho VM de origem. Se o tamanho correspondente não estiver disponível, a Recuperação do Site escolhe automaticamente o tamanho mais próximo disponível.

Se não houver tamanho que suporte a configuração VM de origem, a seguinte mensagem é exibida:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Possíveis causas

- O seu ID de subscrição não está habilitado a criar quaisquer VMs na localização da região alvo.
- O seu ID de subscrição não está ativado, ou não tem quota suficiente, para criar tamanhos de VM específicos na localização da região alvo.
- Não é encontrado tamanho de VM de destino adequado que corresponda à contagem de interface de rede (NIC) da fonte VM (2), para o ID de subscrição na localização da região alvo.

### <a name="fix-the-problem"></a>Corrigir o problema

Contacte o [suporte de faturação do Azure](/azure/azure-portal/supportability/resource-manager-core-quotas-request) para permitir que a sua subscrição crie VMs dos tamanhos necessários no local-alvo. Então, tente novamente a operação falhada.

Se a localização do alvo tiver uma restrição de capacidade, desative a replicação para esse local. Em seguida, ative a replicação para um local diferente onde a sua subscrição tenha quota suficiente para criar VMs dos tamanhos necessários.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificados de raiz fidedignos (código de erro 151066)

Se não estiverem presentes todos os certificados de raiz fidedignos mais recentes no VM, o seu trabalho para permitir a replicação para a Recuperação do Site pode falhar. Autenticação e autorização de chamadas de serviço de Recuperação do Local do VM falham sem estes certificados.

Se o trabalho de replicação ativa falhar, a seguinte mensagem é apresentada:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Causa possível

Os certificados de raiz fidedignos necessários para autorização e autenticação não estão presentes na máquina virtual.

### <a name="fix-the-problem"></a>Corrigir o problema

#### <a name="windows"></a>Windows

Para um VM que executa o sistema operativo Windows, instale as mais recentes atualizações do Windows de modo a que todos os certificados de raiz fidedignos estejam presentes no VM. Siga o típico processo de gestão de atualizações do Windows ou o processo de gestão de atualizações de certificados na sua organização para obter os mais recentes certificados de raiz e a lista de revogação de certificados atualizados nos VMs.

- Se estiver num ambiente desligado, siga o processo padrão de atualização do Windows na sua organização para obter os certificados.
- Se os certificados necessários não estiverem presentes no VM, as chamadas para o serviço de Recuperação do Site falham por razões de segurança.

Para verificar se o problema `login.microsoftonline.com` está resolvido, vá a partir de um navegador no seu VM.

Para mais informações, consulte [As raízes fidedignas da Configure e os certificados proibidos](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11)).

#### <a name="linux"></a>Linux

Siga as orientações fornecidas pelo distribuidor da sua versão do sistema operativo Linux para obter os mais recentes certificados de raiz fidedignos e a mais recente lista de revogação de certificados no VM.

Como o SUSE Linux utiliza links simbólicos, ou simligações, para manter uma lista de certificados, siga estes passos:

1. Inscreva-se como um utilizador **de raiz.** O símbolo hash é`#`o pedido de comando padrão.

1. Para alterar o diretório, dirija este comando:

   `cd /etc/ssl/certs`

1. Verifique se o certificado ca raiz symantec está presente:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Se o certificado ca raiz Symantec não for encontrado, execute o seguinte comando para descarregar o ficheiro. Verifique se há erros e siga as ações recomendadas para falhas de rede.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Verifique se o certificado de AC raiz de Baltimore está presente:

   `ls Baltimore_CyberTrust_Root.pem`

   - Se o certificado de ca raiz de Baltimore não for encontrado, faça este comando para descarregar o certificado:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Verifique se o certificado de DigiCert_Global_Root_CA está presente:

   `ls DigiCert_Global_Root_CA.pem`

    - Se o DigiCert_Global_Root_CA não for encontrado, execute os seguintes comandos para descarregar o certificado:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Para atualizar as hashes do sujeito do certificado para os certificados recém-descarregados, execute o script de rehash:

   `c_rehash`

1. Para verificar se o sujeito hashes como symlinks foram criados para os certificados, executar estes comandos:

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. Crie uma cópia do ficheiro _VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem_ com o nome de ficheiro _b204d74a.0_:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Crie uma cópia do ficheiro _Baltimore_CyberTrust_Root.pem_ com o nome de ficheiro _653b494a.0:_

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Crie uma cópia do ficheiro _DigiCert_Global_Root_CA.pem_ com o nome de ficheiro _3513523f.0:_

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. Verifique se os ficheiros estão presentes:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>UrLs de saída ou intervalos IP (código de erro 151037 ou 151072)

Para que a replicação da recuperação do site funcione, a conectividade de saída a URLs específicos é necessária a partir do VM. Se o seu VM estiver por detrás de uma firewall ou utilizar regras do grupo de segurança de rede (NSG) para controlar a conectividade de saída, poderá enfrentar um destes problemas. Embora continuemos a apoiar o acesso de saída através de URLs, a utilização de uma lista de intervalos IP já não é suportada.

### <a name="issue-1-failed-to-register-azure-vm-with-site-recovery-151195"></a>Edição 1: Falha no registo do Azure VM com a Recuperação do Local (151195)

#### <a name="possible-causes"></a>Possíveis causas

- Não é possível estabelecer uma ligação aos pontos finais de recuperação do site devido a uma falha de resolução do Sistema de Nome de Domínio (DNS).
- Este problema é mais comum durante a reproteção quando falhou sobre a máquina virtual, mas o servidor DNS não é acessível a partir da região de recuperação de desastres (DR).

#### <a name="fix-the-problem"></a>Corrigir o problema

Se estiver a utilizar DNS personalizados, certifique-se de que o servidor DNS está acessível a partir da região de recuperação de desastres.

Para verificar se o VM utiliza uma definição dNS personalizada:

1. Abra **as máquinas virtuais** e selecione o VM.
1. Navegue para as **Definições** de VMs e selecione **Networking**.
1. Na **rede Virtual/sub-rede,** selecione o link para abrir a página de recursos da rede virtual.
1. Vá a **Definições** e selecione **servidores DNS**.

Tente aceder ao servidor DNS a partir da máquina virtual. Se o servidor DNS não estiver acessível, torná-lo acessível falhando no servidor DNS ou criando a linha de site entre a rede DR e o DNS.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-erro.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Edição 2: A configuração de recuperação do site falhou (151196)

#### <a name="possible-cause"></a>Causa possível

Não é possível estabelecer uma ligação aos pontos finais de autenticação e identidade do Office 365.

#### <a name="fix-the-problem"></a>Corrigir o problema

A Recuperação do Site Azure exigiu o acesso às gamas IP do Office 365 para autenticação.
Se estiver a utilizar regras/proxy de firewall do Grupo de Segurança da Rede Azure (NSG) para controlar a conectividade da rede de saída no VM, certifique-se de que utiliza a regra NSG baseada em serviço simultânico do [Azure Ative Directory (AAD)](/azure/virtual-network/security-overview#service-tags) para permitir o acesso ao AAD. Já não apoiamos as regras de NSG baseadas em endereços IP.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Edição 3: A configuração de recuperação do site falhou (151197)

#### <a name="possible-cause"></a>Causa possível

Não é possível estabelecer uma ligação aos pontos finais do serviço de recuperação do site Azure.

#### <a name="fix-the-problem"></a>Corrigir o problema

Se estiver a utilizar regras/proxy do Grupo de Segurança da Rede Azure (NSG) para controlar a conectividade da rede de saída no VM, certifique-se de que utiliza etiquetas de serviço. Já não apoiamos a utilização de uma lista de endereços IP via NSGs para recuperação do site Azure.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>Edição 4: A replicação falha quando o tráfego de rede utiliza o servidor proxy no local (151072)

#### <a name="possible-cause"></a>Causa possível

As definições de procuração personalizadas são inválidas e o agente de serviço de Mobilidade não detetou automaticamente as definições de procuração do Internet Explorer (IE).

#### <a name="fix-the-problem"></a>Corrigir o problema

1. O agente de serviço de Mobilidade deteta `/etc/environment` as definições de procuração do IE no Windows e no Linux.
1. Se preferir definir procuração apenas para o serviço de Mobilidade, então pode fornecer os detalhes proxy em _ProxyInfo.conf_ localizado em:

   - **Linux:**`/usr/local/InMage/config/`
   - **Janelas:**`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. O _ProxyInfo.conf_ deve ter as definições de procuração no seguinte formato _INI._

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> O agente de serviço de Mobilidade apenas suporta **proxies não autenticados.**

### <a name="more-information"></a>Mais informações

Para especificar os [URLs necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou as [gamas IP necessárias,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)siga as orientações em [Sobre a rede em Azure para a replicação Azure.](azure-to-azure-about-networking.md)

## <a name="disk-not-found-in-vm-error-code-150039"></a>Disco não encontrado em VM (código de erro 150039)

Deve ser inicializado um novo disco ligado ao VM. Se o disco não for encontrado, a seguinte mensagem é exibida:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Possíveis causas

- Um novo disco de dados foi ligado ao VM, mas não foi inicializado.
- O disco de dados dentro do VM não está a reportar corretamente o valor do número de unidade lógica (LUN) no qual o disco foi ligado ao VM.

### <a name="fix-the-problem"></a>Corrigir o problema

Certifique-se de que os discos de dados são inicializados e, em seguida, tente novamente a operação.

- **Janelas**: [Fixe e inicialize um novo disco](/azure/virtual-machines/windows/attach-managed-disk-portal).
- **Linux**: [Inicialize um novo disco](/azure/virtual-machines/linux/add-disk)de dados em Linux .

Se o problema persistir, o suporte de contacto.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>Vários discos disponíveis para proteção (código de erro 153039)

### <a name="possible-causes"></a>Possíveis causas

- Um ou mais discos foram recentemente adicionados à máquina virtual após proteção.
- Um ou mais discos foram inicializados após a proteção da máquina virtual.

### <a name="fix-the-problem"></a>Corrigir o problema

Para tornar o estado de replicação do VM saudável novamente, pode optar por proteger os discos ou descartar o aviso.

#### <a name="to-protect-the-disks"></a>Para proteger os discos

1. Vá para **itens** > replicados_VM nome_ > **Discos**.
1. Selecione o disco desprotegido e, em seguida, **selecione a replicação ativar:**

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Ativar a replicação nos discos VM.":::

#### <a name="to-dismiss-the-warning"></a>Para descartar o aviso

1. Vá ao > _nome VM_de **itens replicados.**
1. Selecione o aviso na secção **'Visão Geral'** e, em seguida, selecione **OK**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Descartar o aviso de disco novo.":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>VM removido do cofre completo com informação (código de erro 150225)

Quando a Recuperação do Site protege a máquina virtual, cria ligações na máquina virtual de origem. Quando remove a proteção ou desativa a replicação, a Recuperação do Local remove estes links como parte do trabalho de limpeza. Se a máquina virtual tiver um bloqueio de recursos, o trabalho de limpeza fica concluído com a informação. A informação diz que a máquina virtual foi removida do cofre dos Serviços de Recuperação, mas que algumas das ligações velhas não puderam ser limpas na máquina de origem.

Pode ignorar este aviso se nunca mais pretender proteger esta máquina virtual. Mas se tiver de proteger esta máquina virtual mais tarde, siga os passos nesta secção para limpar os links.

> [!WARNING]
> Se não fizer a limpeza:
>
> - Quando ativar a replicação através do cofre dos Serviços de Recuperação, a máquina virtual não será listada.
> - Se tentar proteger o VM utilizando a recuperação de**Settings** > **desastres**da **máquina** > virtual, a operação falhará com a mensagem **A replicação não pode ser ativada devido**às ligações de recursos existentes no VM .

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> A Recuperação do Site não elimina a máquina virtual de origem nem a afeta de forma alguma enquanto executa estes passos.

1. Retire o bloqueio do grupo de recursos VM ou VM. Por exemplo, na seguinte imagem, o bloqueio `MoveDemo` de recursos no VM denominado deve ser eliminado:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Retire a fechadura da VM.":::

1. Descarregue o script para [remover uma configuração de recuperação de site stale](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script, _Cleanup-stale-asr-config-Azure-VM.ps1_. Forneça o ID de **Subscrição,** **o Grupo de Recursos VM**e o nome **VM** como parâmetros.
1. Se for solicitado para credenciais Azure, forneça-as. Em seguida, verifique se o script funciona sem falhas.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>Replicação não ativada em VM com recursos esfumativos (código de erro 150226)

### <a name="possible-causes"></a>Possíveis causas

A máquina virtual tem uma configuração estagnada da proteção anterior de recuperação do site.

Uma configuração estagnada pode ocorrer num VM Azure se tiver ativado a replicação para o Azure VM utilizando a Recuperação do Local e, em seguida:

- Desativou a replicação, mas a fonte vm tinha um bloqueio de recursos.
- Apagou o cofre de recuperação do site sem desativar explicitamente a replicação no VM.
- Eliminou o grupo de recursos que continha o cofre de recuperação do local sem desativar explicitamente a replicação no VM.

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> A Recuperação do Site não elimina a máquina virtual de origem nem a afeta de forma alguma enquanto executa estes passos.

1. Retire o bloqueio do grupo de recursos VM ou VM. Por exemplo, na seguinte imagem, o bloqueio `MoveDemo` de recursos no VM denominado deve ser eliminado:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Retire a fechadura da VM.":::

1. Descarregue o script para [remover uma configuração de recuperação de site stale](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script, _Cleanup-stale-asr-config-Azure-VM.ps1_. Forneça o ID de **Subscrição,** **o Grupo de Recursos VM**e o nome **VM** como parâmetros.
1. Se for solicitado para credenciais Azure, forneça-as. Em seguida, verifique se o script funciona sem falhas.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>Não se pode selecionar VM ou grupo de recursos para ativar trabalho de replicação

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>Edição 1: O grupo de recursos e a Fonte VM estão em diferentes locais

Atualmente, a Recuperação do Local exige que o grupo de recursos da região de origem e as máquinas virtuais estejam no mesmo local. Se não forem, não conseguirá encontrar a máquina virtual ou o grupo de recursos quando tentar aplicar proteção.

Como suposição, pode permitir a replicação do VM em vez do cofre dos Serviços de Recuperação. Vá à**Recuperação** de Desastres da **Source VM** > **Properties** > e ative a replicação.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Edição 2: O grupo de recursos não faz parte da subscrição selecionada

Você pode não ser capaz de encontrar o grupo de recursos no momento da proteção se o grupo de recursos não fizer parte da subscrição selecionada. Certifique-se de que o grupo de recursos pertence à subscrição que está a usar.

### <a name="issue-3-stale-configuration"></a>Edição 3: Configuração do velho

Pode não ver o VM que pretende ativar para a replicação se existir uma configuração de recuperação de sítio staleada no VM Azure. Esta condição pode ocorrer se tiver ativado a replicação para o VM Azure utilizando a Recuperação do Local e, em seguida:

- Apagou o cofre de recuperação do site sem desativar explicitamente a replicação no VM.
- Eliminou o grupo de recursos que continha o cofre de recuperação do local sem desativar explicitamente a replicação no VM.
- Desativou a replicação, mas a fonte vm tinha um bloqueio de recursos.

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> Certifique-se de `AzureRM.Resources` atualizar o módulo antes de utilizar o script mencionado nesta secção. A Recuperação do Site não elimina a máquina virtual de origem nem a afeta de forma alguma enquanto executa estes passos.

1. Retire a fechadura, se houver, do grupo de recursos VM ou VM. Por exemplo, na seguinte imagem, o bloqueio `MoveDemo` de recursos no VM denominado deve ser eliminado:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Retire a fechadura da VM.":::

1. Descarregue o script para [remover uma configuração de recuperação de site stale](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script, _Cleanup-stale-asr-config-Azure-VM.ps1_. Forneça o ID de **Subscrição,** **o Grupo de Recursos VM**e o nome **VM** como parâmetros.
1. Se for solicitado para credenciais Azure, forneça-as. Em seguida, verifique se o script funciona sem falhas.

## <a name="unable-to-select-a-vm-for-protection"></a>Incapaz de selecionar um VM para proteção

### <a name="possible-cause"></a>Causa possível

A máquina virtual tem uma extensão instalada num estado falhado ou sem resposta

### <a name="fix-the-problem"></a>Corrigir o problema

Vá às**extensões** de**definições** > de **máquinas** > virtuais e verifique se há extensões num estado falhado. Desinstale qualquer extensão falhada e tente novamente proteger a máquina virtual.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>O estado de provisionamento vm não é válido (código de erro 150019)

Para permitir a replicação no VM, o seu estado de provisionamento deve ser **bem sucedido**. Siga estas medidas para verificar o estado de provisionamento:

1. No portal Azure, selecione o Explorador de **Recursos** de **Todos os Serviços.**
1. Expanda a lista de **Subscrições** e selecione a sua subscrição.
1. Expandir a lista **de Grupos** de Recursos e selecionar o grupo de recursos do VM.
1. Expanda a lista **de Recursos** e selecione o seu VM.
1. Verifique o campo **de provisionamento do Estado** na vista, por exemplo, do lado direito.

### <a name="fix-the-problem"></a>Corrigir o problema

- Se o Estado de **fornecimento** estiver **falhado,** contacte o suporte com detalhes para resolver problemas.
- Se o Estado de **fornecimento** estiver **a atualizar,** poderá ser implementada outra extensão. Verifique se existem operações em curso no VM, espere que terminem e, em seguida, volte a tentar o trabalho falhado de recuperação do site para permitir a replicação.

## <a name="unable-to-select-target-vm"></a>Incapaz de selecionar o alvo VM

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Edição 1: VM está ligado a uma rede que já está mapeada para uma rede-alvo

Durante a configuração da recuperação de desastres, se a fonte VM fizer parte de uma rede virtual, e outro VM da mesma rede virtual já estiver mapeado com uma rede no grupo de recursos-alvo, a caixa de lista de seleção de rede não está disponível (aparece reduzida) por padrão.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="Lista de seleção de rede indisponível.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>Edição 2: Protegeu previamente o VM e depois desativou a replicação

A replicação incapacitante de um VM não elimina o mapeamento da rede. O mapeamento deve ser apagado do cofre dos Serviços de Recuperação onde o VM estava protegido. Selecione o **cofre dos Serviços** de Recuperação e vá **gerir** > a Infraestrutura > de**Recuperação**do Local para o > **Mapeamento da rede**de**máquinas virtuais Azure**.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Eliminar o mapeamento da rede.":::

A rede-alvo que foi configurada durante a configuração da recuperação de desastres pode ser alterada após a configuração inicial, e após a proteção do VM. Para modificar o mapeamento da **rede,** selecione o nome da rede:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Modificar o mapeamento da rede.":::


## <a name="com-or-vss-error-code-151025"></a>COM+ ou VSS (código de erro 151025)

Quando ocorre o erro do Serviço de Cópia de Sombra seleções (VSS) com O COM+ ou volume, é apresentada a seguinte mensagem:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Possíveis causas

- O serviço de aplicação do sistema COM+ está desativado.
- O Serviço de Cópia de Sombra de Volume está desativado.

### <a name="fix-the-problem"></a>Corrigir o problema

Detete o serviço de aplicação do sistema COM+ e o serviço de cópia de sombra de volume para o modo de arranque automático ou manual.

1. Abra a consola Services no Windows.
1. Certifique-se de que a aplicação do sistema COM+ e o Serviço de Cópia de Sombra de Volume não estão definidos para **desativados** como o seu Tipo de **Arranque**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Verifique o tipo de arranque de COM plus System Application e Volume Shadow Copy Service.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Tamanho do disco gerido não suportado (código de erro 150172)

Quando este erro ocorrer, é apresentada a seguinte mensagem:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Causa possível

O disco é menor do que o tamanho suportado de 1024 MB.

### <a name="fix-the-problem"></a>Corrigir o problema

Certifique-se de que o tamanho do disco está dentro do intervalo de tamanho suportado e, em seguida, tente novamente a operação.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>Proteção não ativada quando a GRUB utiliza o nome do dispositivo (código de erro 151126)

### <a name="possible-causes"></a>Possíveis causas

Os ficheiros de configuração do Botão Unificado Linux (GRUB)_(/boot/grub/menu.lst,_ _/boot/grub/grub.cfg,_ _/boot/grub2/grub.cfg,_ ou _/etc/default/grub)_ podem especificar os `root` `resume` nomes reais do dispositivo em vez dos valores do identificador universalmente único (UUID) para os parâmetros e parâmetros. A Recuperação do Site requer UUIDs porque os nomes do dispositivo podem mudar. Após o reinício, um VM pode não ter o mesmo nome na failover, resultando em problemas.

Os seguintes exemplos são linhas de ficheiros GRUB onde os nomes do dispositivo aparecem em vez dos UUIDs necessários:

- Arquivo _/boot/grub2/grub.cfg:_

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Arquivo: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Corrigir o problema

Substitua cada nome do dispositivo pelo UUID correspondente:

1. Encontre o UUID do dispositivo executando o comando `blkid <device name>`. Por exemplo:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Substitua o nome do dispositivo pelo seu `root=UUID=<UUID>` `resume=UUID=<UUID>`UUID, nos formatos e . Por exemplo, após a substituição, a linha de _/boot/grub/menu.lst_ seria a seguinte linha:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Tente a proteção.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>A proteção falhou porque o dispositivo GRUB não existe (código de erro 151124)

### <a name="possible-cause"></a>Causa possível

Os ficheiros de configuração GRUB _(/boot/grub/menu.lst,_ _/boot/grub/grub.cfg,_ _/boot/grub2/grub.cfg,_ ou `rd.lvm.lv` _/etc/predefinido/grub_) podem conter os parâmetros ou `rd_LVM_LV`. Estes parâmetros identificam os dispositivos Lógicos de Gestor de Volume (LVM) que devem ser descobertos no momento do arranque. Se estes dispositivos LVM não existirem, o próprio sistema protegido não arranca e ficará preso no processo de arranque. O mesmo problema também será visto com o VM falhado. Aqui estão poucos exemplos:

- Arquivo: _/boot/grub2/grub2/grub.cfg_ no RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Arquivo: _/etc/predefinido/grub_ no RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Arquivo: _/boot/grub/menu.lst_ no RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

Em cada exemplo, a GRUB tem de detetar `root` `swap` dois dispositivos `rootvg`LVM com os nomes e do grupo de volume .

### <a name="fix-the-problem"></a>Corrigir o problema

Se o dispositivo LVM não existir, crie-o ou remova os parâmetros correspondentes dos ficheiros de configuração GRUB. Em seguida, tente novamente para permitir a proteção.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>Atualização do serviço de mobilidade terminada com avisos (código de erro 151083)

O serviço de Mobilidade de Recuperação do Site tem muitos componentes, um dos quais é chamado de condutor de filtros. O controlador de filtro é carregado na memória do sistema apenas durante o reinício do sistema. Sempre que uma atualização do serviço mobility inclui alterações no controlador de filtros, a máquina é atualizada, mas ainda assim vê um aviso de que algumas correções requerem um reinício. O aviso aparece porque as correções do controlador de filtro só podem produzir efeito quando o novo condutor do filtro é carregado, o que só acontece durante o reinício.

> [!NOTE]
> Isto é só um aviso. A replicação existente continua a funcionar mesmo após a atualização do novo agente. Pode optar por reiniciar sempre que quiser os benefícios do novo condutor do filtro, mas o antigo condutor do filtro continua a trabalhar se não reiniciar.
>
> Para além do condutor do filtro, os benefícios de quaisquer outras melhorias e correções na atualização do serviço mobility fazem efeito sem exigir um reinício.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>Proteção não ativada se existir um disco gerido por réplica

Este erro ocorre quando o disco gerido da réplica já existe, sem etiquetas esperadas, no grupo de recursos-alvo.

### <a name="possible-cause"></a>Causa possível

Este problema pode ocorrer se a máquina virtual foi previamente protegida, e quando a replicação foi desativada, o disco de réplica não foi removido.

### <a name="fix-the-problem"></a>Corrigir o problema

Elimine o disco de réplica identificado na mensagem de erro e tente novamente o trabalho de proteção falhado.

## <a name="next-steps"></a>Passos seguintes

[Replicar VMs Azure para outra região de Azure](azure-to-azure-how-to-enable-replication.md)
