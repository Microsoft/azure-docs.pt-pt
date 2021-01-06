---
title: Resolução de problemas Azure VM replicação na Recuperação do Local de Azure
description: Erros de resolução de problemas ao replicar máquinas virtuais Azure para recuperação de desastres.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 6d61a44e671c43754fa7cccbe8ea8fe54eeba387
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900421"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Resolver erros de replicação de VMs do Azure para Azure

Este artigo descreve como resolver erros comuns na Recuperação do Local de Azure durante a replicação e recuperação de máquinas virtuais Azure (VM) de uma região para outra. Para obter mais informações sobre configurações suportadas, consulte a [matriz de suporte para replicar VMs Azure](azure-to-azure-support-matrix.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemas de quota de recursos Azure (código de erro 150097)

Certifique-se de que a sua subscrição está ativada para criar VMs Azure na região alvo que planeia utilizar como região de recuperação de desastres (DR). A sua subscrição necessita de quota suficiente para criar VMs das dimensões necessárias. Por predefinição, a Recuperação do Site escolhe um tamanho VM alvo que é o mesmo que o tamanho VM de origem. Se o tamanho de correspondência não estiver disponível, a Recuperação do Site escolhe automaticamente o tamanho mais próximo disponível.

Se não houver tamanho que suporte a configuração VM de origem, é apresentada a seguinte mensagem:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Possíveis causas

- O seu ID de subscrição não está habilitado a criar VMs na localização da região alvo.
- O seu ID de subscrição não está habilitado, ou não tem quota suficiente, para criar tamanhos VM específicos no local da região alvo.
- Não se encontra qualquer tamanho de VM alvo adequado que corresponda à contagem de cartões de interface de rede (NIC) (NIC) de origem, para o ID de subscrição no local da região alvo.

### <a name="fix-the-problem"></a>Corrigir o problema

Contacte [o suporte de faturação da Azure](../azure-portal/supportability/resource-manager-core-quotas-request.md) para permitir que a sua subscrição crie VMs dos tamanhos necessários no local do alvo. Então, recandidutar a operação falhada.

Se a localização do alvo tiver uma restrição de capacidade, desative a replicação para esse local. Em seguida, permitir a replicação para um local diferente onde a sua subscrição tem quota suficiente para criar VMs dos tamanhos necessários.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificados de raiz fidedignos (código de erro 151066)

Se nem todos os certificados de raiz fidedignos estiverem presentes no VM, o seu trabalho para permitir a replicação para a Recuperação do Local pode falhar. Autenticação e autorização de chamadas de serviço de Recuperação de Sítio do VM falham sem estes certificados.

Se o trabalho de replicação ativa falhar, é apresentada a seguinte mensagem:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Causa possível

Os certificados de raiz fidedignos necessários para autorização e autenticação não estão presentes na máquina virtual.

### <a name="fix-the-problem"></a>Corrigir o problema

#### <a name="windows"></a>Windows

Para um VM que executa o sistema operativo Windows, instale as atualizações mais recentes do Windows de modo a que todos os certificados de raiz fidedignos estejam presentes no VM. Siga o típico processo de gestão de atualização de atualização do Windows ou de atualização de certificados na sua organização para obter os certificados de raiz mais recentes e a lista de revogação de certificados atualizados nos VMs.

- Se estiver num ambiente desligado, siga o processo padrão de atualização do Windows na sua organização para obter os certificados.
- Se os certificados exigidos não estiverem presentes no VM, as chamadas para o serviço de Recuperação do Local falham por razões de segurança.

Para verificar se o problema está resolvido, vá para `login.microsoftonline.com` um browser no seu VM.

Para mais informações, consulte [configurar raízes fidedignas e certificados não permitidos.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11))

#### <a name="linux"></a>Linux

Siga as orientações fornecidas pelo distribuidor da sua versão do sistema operativo Linux para obter os mais recentes certificados de raiz fidedignos e a mais recente lista de revogação de certificados no VM.

Como a SUSE Linux utiliza ligações simbólicas, ou symlinks, para manter uma lista de certificados, siga estes passos:

1. Inscreva-se como um utilizador **de raiz.** O símbolo de `#` haxixe é o pedido de comando predefinido.

1. Para mudar o diretório, executar este comando:

   `cd /etc/ssl/certs`

1. Verifique se o certificado Symantec raiz CA está presente:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Se o certificado Symantec root CA não for encontrado, execute o seguinte comando para descarregar o ficheiro. Verifique se há erros e siga as ações recomendadas para falhas na rede.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Verifique se o certificado de CA raiz de Baltimore está presente:

   `ls Baltimore_CyberTrust_Root.pem`

   - Se o certificado de A.C. raiz de Baltimore não for encontrado, execute este comando para descarregar o certificado:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Verifique se o certificado de DigiCert_Global_Root_CA está presente:

   `ls DigiCert_Global_Root_CA.pem`

    - Se o DigiCert_Global_Root_CA não for encontrado, execute os seguintes comandos para descarregar o certificado:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Para atualizar os hashes do sujeito de certificado para os certificados recentemente descarregados, execute o script rehash:

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

1. Criar uma cópia do ficheiro _VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem_ com nome de arquivo _b204d74a.0_:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Criar uma cópia do ficheiro _Baltimore_CyberTrust_Root.pem_ com o nome de ficheiro _653b494a.0_:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Criar uma cópia do ficheiro _DigiCert_Global_Root_CA.pem_ com o nome de ficheiro _3513523f.0_:

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

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>GAMAS DE SAÍDA URLs ou IP (código de erro 151037 ou 151072)

Para que a replicação da Recuperação do Local funcione, a conectividade de saída para URLs específicos é necessária a partir do VM. Se o seu VM estiver por detrás de uma firewall ou utilizar regras de grupo de segurança de rede (NSG) para controlar a conectividade de saída, poderá enfrentar um destes problemas. Enquanto continuamos a apoiar o acesso de saída via URLs, usando uma lista de permitis de gamas IP já não é suportado.

#### <a name="possible-causes"></a>Possíveis causas

- Não é possível estabelecer uma ligação aos pontos finais de recuperação do local devido a uma falha de resolução do Sistema de Nome de Domínio (DNS).
- Este problema é mais comum durante a reproteção quando falhou sobre a máquina virtual, mas o servidor DNS não é acessível a partir da região de recuperação de desastres (DR).

#### <a name="fix-the-problem"></a>Corrigir o problema

Se estiver a utilizar DNS personalizado, certifique-se de que o servidor DNS está acessível a partir da região de recuperação de desastres.

Para verificar se o VM utiliza uma definição de DNS personalizada:

1. Abra **as máquinas Virtuais** e selecione o VM.
1. Navegue para as **Definições VMs** e selecione **Networking**.
1. Na **rede virtual/sub-rede,** selecione o link para abrir a página de recursos da rede virtual.
1. Vá a **Definições** e selecione **servidores DNS**.

Tente aceder ao servidor DNS a partir da máquina virtual. Se o servidor DNS não estiver acessível, torne-o acessível falhando no servidor DNS ou criando a linha de site entre a rede DR e o DNS.

> [!NOTE]
> Se utilizar pontos finais privados, certifique-se de que os VMs podem resolver os registos privados de DNS.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-erro.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Edição 2: A configuração da recuperação do local falhou (151196)

#### <a name="possible-cause"></a>Causa possível

Não é possível estabelecer uma ligação com os pontos finais ip4 de autenticação e identidade do Microsoft 365.

#### <a name="fix-the-problem"></a>Corrigir o problema

A Azure Site Recovery exigiu o acesso às gamas IP do Microsoft 365 para autenticação.
Se estiver a utilizar as regras do Azure Network Security Group (NSG) /proxy firewall para controlar a conectividade da rede de saída no VM, certifique-se de que utiliza a regra NSG baseada na etiqueta de [serviço Azure Ative (AAD)](../virtual-network/network-security-groups-overview.md#service-tags) para permitir o acesso ao AAD. Já não apoiamos as regras NSG baseadas em endereços IP.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Edição 3: A configuração da recuperação do local falhou (151197)

#### <a name="possible-cause"></a>Causa possível

Não é possível estabelecer uma ligação com os pontos finais do serviço de recuperação do local de Azure.

#### <a name="fix-the-problem"></a>Corrigir o problema

Se estiver a utilizar as regras do Azure Network Security Group (NSG) para controlar a conectividade da rede de saída no VM, certifique-se de que utiliza tags de serviço. Já não apoiamos usando uma lista de endereços IP através de NSGs para recuperação do site Azure.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>Edição 4: A replicação falha quando o tráfego de rede utiliza o servidor proxy no local (151072)

#### <a name="possible-cause"></a>Causa possível

As definições de procuração personalizadas são inválidas e o agente de serviço de mobilidade não conseguiu automatizar as definições de procuração do Internet Explorer (IE).

#### <a name="fix-the-problem"></a>Corrigir o problema

1. O agente de serviço de mobilidade deteta as definições de procuração do IE no Windows e `/etc/environment` no Linux.
1. Se preferir definir procuração apenas para o serviço de Mobilidade, então pode fornecer os detalhes do proxy em _ProxyInfo.conf_ localizado em:

   - **Linux:**`/usr/local/InMage/config/`
   - **Janelas:**`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. O _ProxyInfo.conf_ deve ter as definições de procuração no seguinte formato _INI._

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> O agente de serviço de mobilidade só suporta **proxies não autenticados.**

### <a name="more-information"></a>Mais informações

Para especificar os [URLs necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou os [intervalos IP necessários,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)siga as orientações em [Sobre a rede em Azure para a replicação do Azure](azure-to-azure-about-networking.md).

## <a name="disk-not-found-in-vm-error-code-150039"></a>Disco não encontrado em VM (código de erro 150039)

Deve ser inicializado um novo disco ligado ao VM. Se o disco não for encontrado, é apresentada a seguinte mensagem:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Possíveis causas

- Um novo disco de dados foi anexado ao VM, mas não foi inicializado.
- O disco de dados dentro do VM não está a reportar corretamente o valor do número de unidade lógica (LUN) no qual o disco foi ligado ao VM.

### <a name="fix-the-problem"></a>Corrigir o problema

Certifique-se de que os discos de dados são inicializados e, em seguida, re-tentar a operação.

- **Windows**: [Prenda e inicialize um novo disco](../virtual-machines/windows/attach-managed-disk-portal.md).
- **Linux**: [Inicialize um novo disco de dados em Linux](../virtual-machines/linux/add-disk.md).

Se o problema persistir, contacte o suporte.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>Vários discos disponíveis para proteção (código de erro 153039)

### <a name="possible-causes"></a>Possíveis causas

- Um ou mais discos foram recentemente adicionados à máquina virtual após proteção.
- Um ou mais discos foram inicializados após a proteção da máquina virtual.

### <a name="fix-the-problem"></a>Corrigir o problema

Para tornar o estado de replicação do VM saudável novamente, pode optar por proteger os discos ou rejeitar o aviso.

#### <a name="to-protect-the-disks"></a>Para proteger os discos

1. Aceda a **itens replicados**  >  _VM discos_ de nome  >  .
1. Selecione o disco desprotegido e, em seguida, **selecione Ativar a replicação**:

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Ativar a replicação nos discos VM.":::

#### <a name="to-dismiss-the-warning"></a>Para rejeitar o aviso

1. Aceda aos **itens replicados**  >  _nome VM_.
1. Selecione o aviso na secção **'Vista Geral'** e, em seguida, selecione **OK**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Dispense o aviso de disco novo.":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>VM removido do cofre concluído com informação (código de erro 150225)

Quando a Recuperação do Site protege a máquina virtual, cria ligações na máquina virtual de origem. Quando remove a proteção ou desativa a replicação, a Recuperação do Local remove estas ligações como parte do trabalho de limpeza. Se a máquina virtual tiver um bloqueio de recursos, o trabalho de limpeza é concluído com a informação. A informação diz que a máquina virtual foi removida do cofre dos Serviços de Recuperação, mas que algumas das ligações velhas não podiam ser limpas na máquina de origem.

Pode ignorar este aviso se nunca mais pretender proteger esta máquina virtual. Mas se tiver de proteger esta máquina virtual mais tarde, siga os passos desta secção para limpar os links.

> [!WARNING]
> Se não fizer a limpeza:
>
> - Quando ativar a replicação através do cofre dos Serviços de Recuperação, a máquina virtual não será listada.
> - Se tentar proteger o VM utilizando a **recuperação** de  >  **desastres de configurações** de máquinas  >  virtuais, a operação falhará com a mensagem **A replicação não pode ser ativada devido às ligações existentes de recursos existentes no VM**.

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> A Recuperação do Site não apaga a máquina virtual de origem nem a afeta de forma alguma enquanto executa estes passos.

1. Retire o bloqueio do grupo de recursos VM ou VM. Por exemplo, na seguinte imagem, o bloqueio de recursos no VM nomeado `MoveDemo` deve ser eliminado:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Retire o cadeado do VM.":::

1. Descarregue o script para [remover uma configuração de recuperação do site.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. Executar o guião, _Cleanup-stale-asr-config-Azure-VM.ps1._ Forneça o **ID de subscrição,** **o VM Resource Group** e o nome **VM** como parâmetros.
1. Se te pedirem credenciais do Azure, fornece-as. Em seguida, verifique se o script funciona sem falhas.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>Replicação não ativada em VM com recursos antigos (código de erro 150226)

### <a name="possible-causes"></a>Possíveis causas

A máquina virtual tem uma configuração antiga da proteção anterior da Recuperação do Local.

Uma configuração velha pode ocorrer num VM Azure se tiver ativado a replicação para o VM Azure utilizando a Recuperação do Local e, em seguida, :

- Desativaste a replicação, mas a fonte VM tinha um bloqueio de recursos.
- Apagou o cofre de recuperação do local sem desativar explicitamente a replicação no VM.
- Eliminou o grupo de recursos que contém o cofre de recuperação do local sem desativar explicitamente a replicação no VM.

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> A Recuperação do Site não apaga a máquina virtual de origem nem a afeta de forma alguma enquanto executa estes passos.

1. Retire o bloqueio do grupo de recursos VM ou VM. Por exemplo, na seguinte imagem, o bloqueio de recursos no VM nomeado `MoveDemo` deve ser eliminado:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Retire o cadeado do VM.":::

1. Descarregue o script para [remover uma configuração de recuperação do site.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. Executar o guião, _Cleanup-stale-asr-config-Azure-VM.ps1._ Forneça o **ID de subscrição,** **o VM Resource Group** e o nome **VM** como parâmetros.
1. Se te pedirem credenciais do Azure, fornece-as. Em seguida, verifique se o script funciona sem falhas.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>Não é possível selecionar VM ou grupo de recursos para permitir o trabalho de replicação

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>Edição 1: O grupo de recursos e a VM de origem estão em locais diferentes

A recuperação do local exige atualmente que o grupo de recursos da região de origem e as máquinas virtuais estejam no mesmo local. Se não forem, não conseguirá encontrar a máquina virtual ou o grupo de recursos quando tentar aplicar proteção.

Como uma solução alternativa, pode permitir a replicação a partir do VM em vez do cofre dos Serviços de Recuperação. Vá à Recuperação de **Desastres de Propriedades Source VM**  >    >   e ative a replicação.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Edição 2: O grupo de recursos não faz parte da subscrição selecionada

Pode não ser capaz de encontrar o grupo de recursos no momento da proteção se o grupo de recursos não fizer parte da subscrição selecionada. Certifique-se de que o grupo de recursos pertence à subscrição que está a utilizar.

### <a name="issue-3-stale-configuration"></a>Edição 3: Configuração velha

Pode não ver o VM que pretende ativar para a replicação se existir uma configuração de recuperação do local no VM Azure. Esta condição pode ocorrer se ativar a replicação para o Azure VM utilizando a Recuperação do Local e, em seguida, :

- Apagou o cofre de recuperação do local sem desativar explicitamente a replicação no VM.
- Eliminou o grupo de recursos que contém o cofre de recuperação do local sem desativar explicitamente a replicação no VM.
- Desativaste a replicação, mas a fonte VM tinha um bloqueio de recursos.

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> Certifique-se de atualizar o `AzureRM.Resources` módulo antes de utilizar o script mencionado nesta secção. A Recuperação do Site não apaga a máquina virtual de origem nem a afeta de forma alguma enquanto executa estes passos.

1. Retire a fechadura, se houver, do grupo de recursos VM ou VM. Por exemplo, na seguinte imagem, o bloqueio de recursos no VM nomeado `MoveDemo` deve ser eliminado:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Retire o cadeado do VM.":::

1. Descarregue o script para [remover uma configuração de recuperação do site.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. Executar o guião, _Cleanup-stale-asr-config-Azure-VM.ps1._ Forneça o **ID de subscrição,** **o VM Resource Group** e o nome **VM** como parâmetros.
1. Se te pedirem credenciais do Azure, fornece-as. Em seguida, verifique se o script funciona sem falhas.

## <a name="unable-to-select-a-vm-for-protection"></a>Não é possível selecionar um VM para proteção

### <a name="possible-cause"></a>Causa possível

A máquina virtual tem uma extensão instalada num estado falhado ou sem resposta

### <a name="fix-the-problem"></a>Corrigir o problema

Aceda a **extensões de configurações de configurações de**  >    >  **máquinas virtuais** e verifique se há extensões num estado falhado. Desinstale qualquer extensão falhada e tente novamente proteger a máquina virtual.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>O estado de provisionamento de VM não é válido (código de erro 150019)

Para permitir a replicação no VM, o seu estado de provisionamento tem de ser **bem sucedido**. Siga estas medidas para verificar o estado de provisionamento:

1. No portal Azure, selecione o Explorador de **Recursos** de **Todos os Serviços**.
1. Expanda a lista **de Subscrições** e selecione a sua subscrição.
1. Expandir a lista **de Grupos de Recursos** e selecionar o grupo de recursos do VM.
1. Expanda a lista **de Recursos** e selecione o seu VM.
1. Verifique o campo **de provisionamento No** ponto de vista do lado direito.

### <a name="fix-the-problem"></a>Corrigir o problema

- Se o **Estado de provisionamento** **estiver falhado,** contacte o suporte com detalhes para resolver problemas.
- Se o **Estado de Provisionamento** estiver **atualizado,** poderá ser implementada outra extensão. Verifique se existem alguma operação em curso no VM, aguarde que terminem e, em seguida, volte a tentar o trabalho falhado de Recuperação do Local para permitir a replicação.

## <a name="unable-to-select-target-vm"></a>Incapaz de selecionar O VM alvo

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Edição 1: A VM está ligada a uma rede que já está mapeada para uma rede alvo

Durante a configuração de recuperação de desastres, se o VM de origem fizer parte de uma rede virtual, e outro VM da mesma rede virtual já estiver mapeado com uma rede no grupo de recursos-alvo, a caixa de lista de retirada de rede está indisponível (parece escurecida) por padrão.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="Lista de seleção de rede indisponível.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>Edição 2: Protegeste previamente o VM e depois desativaste a replicação

A desativação da replicação de um VM não apaga o mapeamento da rede. O mapeamento deve ser eliminado do cofre dos Serviços de Recuperação onde o VM foi protegido. Selecione o **cofre dos Serviços de Recuperação** e vá para **gerir** a  >  **infraestrutura de recuperação do local** para o  >  Mapeamento da rede de **máquinas virtuais Azure**  >  .

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Apague o mapeamento da rede.":::

A rede-alvo que foi configurada durante a configuração da recuperação de desastres pode ser alterada após a configuração inicial, e após a proteção do VM. Para modificar o **mapeamento da rede,** selecione o nome da rede:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Modificar o mapeamento da rede.":::


## <a name="com-or-vss-error-code-151025"></a>COM+ ou VSS (código de erro 151025)

Quando ocorre o erro do Serviço de Cópia Sombra com++ ou volume (VSS), é apresentada a seguinte mensagem:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Possíveis causas

- O serviço de aplicação do sistema COM+ está desativado.
- O Serviço de Cópia Sombra de Volume está desativado.

### <a name="fix-the-problem"></a>Corrigir o problema

Desactore o Serviço de Cópia de Sombra do Sistema COM+ para o modo de arranque automático ou manual.

1. Abra a consola serviços no Windows.
1. Certifique-se de que o serviço de cópia de sombra do sistema COM+ e do volume não estão definidos para **desativar** como o seu **Tipo de Arranque**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Verifique o tipo de arranque de COM plus System Application e Volume Shadow Copy Service.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Tamanho do disco gerido não suportado (código de erro 150172)

Quando este erro ocorre, é apresentada a seguinte mensagem:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Causa possível

O disco é menor do que o tamanho suportado de 1024 MB.

### <a name="fix-the-problem"></a>Corrigir o problema

Certifique-se de que o tamanho do disco está dentro do intervalo de tamanho suportado e, em seguida, re-tentar a operação.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>Proteção não ativada quando o GRUB utiliza o nome do dispositivo (código de erro 151126)

### <a name="possible-causes"></a>Possíveis causas

Os ficheiros de configuração linux Grand Unified Bootloader (GRUB)_(/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_, ou _/etc/default/grub_) podem especificar os nomes reais do dispositivo em vez de valores de identificador universalmente únicos (UUID) para os `root` `resume` parâmetros e parâmetros. A recuperação do site requer UUIDs porque os nomes do dispositivo podem mudar. Após o reinício, um VM pode não ter o mesmo nome no failover, resultando em problemas.

Os exemplos a seguir são as linhas dos ficheiros GRUB onde os nomes dos dispositivos aparecem em vez dos UUIDs necessários:

- Arquivo _/bota/grub2/grub.cfg:_

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Arquivo: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Corrigir o problema

Substitua cada nome do dispositivo pelo UUID correspondente:

1. Encontre o UUID do dispositivo executando o comando `blkid <device name>` . Por exemplo:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Substitua o nome do dispositivo pelo seu UUID, nos formatos `root=UUID=<UUID>` e `resume=UUID=<UUID>` . Por exemplo, após substituição, a linha de _/boot/grub/menu.lst_ seria a seguinte linha:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Recandidutar a proteção.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>A proteção falhou porque o dispositivo GRUB não existe (código de erro 151124)

### <a name="possible-cause"></a>Causa possível

Os ficheiros de configuração GRUB _(/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_, ou _/etc/default/grub_) podem conter os parâmetros `rd.lvm.lv` ou `rd_LVM_LV` . Estes parâmetros identificam os dispositivos Lógicos Volume Manager (LVM) que devem ser descobertos no momento do arranque. Se estes dispositivos LVM não existirem, o sistema protegido em si não será iniciado e ficará preso no processo de arranque. O mesmo problema também será visto com o VM de failover. Aqui estão alguns exemplos:

- Arquivo: _/boot/grub2/grub.cfg_ no RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Arquivo: _/etc/predefinido/grub_ no RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Arquivo: _/boot/grub/menu.lst_ on RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

Em cada exemplo, a GRUB tem de detetar dois dispositivos LVM com os nomes `root` e do grupo de volume `swap` `rootvg` .

### <a name="fix-the-problem"></a>Corrigir o problema

Se o dispositivo LVM não existir, crie-o ou remova os parâmetros correspondentes dos ficheiros de configuração GRUB. Então, tente novamente para permitir a proteção.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>Atualização do serviço de mobilidade terminada com avisos (código de erro 151083)

O serviço de Mobilidade de Recuperação do Local tem muitos componentes, um dos quais é chamado de controlador de filtros. O controlador do filtro é carregado na memória do sistema apenas durante o reinício do sistema. Sempre que uma atualização do serviço Mobility inclui alterações no controlador de filtros, a máquina é atualizada, mas ainda vê um aviso de que algumas correções requerem um reinício. O aviso aparece porque as correções do controlador do filtro só podem produzir efeitos quando o novo controlador do filtro estiver carregado, o que só acontece durante o reinício.

> [!NOTE]
> Isto é só um aviso. A replicação existente continua a funcionar mesmo depois da nova atualização do agente. Pode optar por reiniciar sempre que quiser os benefícios do novo controlador de filtros, mas o antigo controlador de filtros continua a funcionar se não reiniciar.
>
> Para além do controlador de filtros, os benefícios de quaisquer outras melhorias e correções na atualização do serviço mobility fazem efeito sem exigir um reinício.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>Proteção não ativada se o disco gerido por réplica existir

Este erro ocorre quando o disco gerido por réplica já existe, sem etiquetas esperadas, no grupo de recursos-alvo.

### <a name="possible-cause"></a>Causa possível

Este problema pode ocorrer se a máquina virtual foi previamente protegida, e quando a replicação foi desativada, o disco de réplica não foi removido.

### <a name="fix-the-problem"></a>Corrigir o problema

Elimine o disco de réplica identificado na mensagem de erro e relemque o trabalho de proteção falhado.

## <a name="enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137"></a>Ativar a proteção falhada uma vez que o instalador não consegue encontrar o disco raiz (código de erro 151137)

Este erro ocorre para máquinas Linux onde o disco OS é encriptado usando encriptação do disco Azure (ADE). Esta é uma emissão válida apenas na versão 9.35 do Agente.

### <a name="possible-causes"></a>Causas Possíveis

O instalador não consegue encontrar o disco raiz que acolhe o sistema de ficheiros raiz.

### <a name="fix-the-problem"></a>Corrigir o problema

Siga os passos abaixo para corrigir este problema -

1. Encontre as brocas de agente sob o diretório _/var/lib/waagent_ nas máquinas RHEL e CentOS utilizando o comando abaixo: <br>

    `# find /var/lib/ -name Micro\*.gz`

   Resultado esperado:

    `/var/lib/waagent/Microsoft.Azure.RecoveryServices.SiteRecovery.LinuxRHEL7-1.0.0.9139/UnifiedAgent/Microsoft-ASR_UA_9.35.0.0_RHEL7-64_GA_30Jun2020_release.tar.gz`

2. Crie um novo diretório e mude o diretório para este novo diretório.
3. Extrair o ficheiro agente encontrado no primeiro passo aqui, utilizando o comando abaixo:

    `tar -xf <Tar Ball File>`

4. Abra o ficheiro _prereq_check_installer.js_ ligado e elimine as seguintes linhas. Guarde o ficheiro depois disso.

    ```
       {
          "CheckName": "SystemDiskAvailable",
          "CheckType": "MobilityService"
       },
    ```
5. Invoque o instalador utilizando o comando: <br>

    `./install -d /usr/local/ASR -r MS -q -v Azure`
6. Se o instalador tiver sucesso, ressacaia o trabalho de replicação ativa.

## <a name="next-steps"></a>Passos seguintes

[Replicar VMs do Azure para outra região do Azure](azure-to-azure-how-to-enable-replication.md)