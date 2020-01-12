---
title: Solucionar problemas de replicação de VM do Azure no Azure Site Recovery
description: Solucionar erros ao replicar máquinas virtuais do Azure para recuperação de desastre.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: rochakm
ms.openlocfilehash: 3f97975f09d846cd3277bb8a53a4ad922f1b5b69
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902561"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Solucionar erros de replicação de VM do Azure para o Azure

Este artigo descreve como solucionar erros comuns no Azure Site Recovery durante a replicação e a recuperação de VMs (máquinas virtuais) do Azure de uma região para outra. Para obter mais informações sobre configurações suportadas, consulte a [matriz de suporte para replicar VMs do Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemas de cota de recursos do Azure (código de erro 150097)

Verifique se sua assinatura está habilitada para criar VMs do Azure na região de destino que você planeja usar como sua região de recuperação de desastre. Verifique também se sua assinatura tem cota suficiente para criar VMs dos tamanhos necessários. Por padrão, Site Recovery escolhe um tamanho de VM de destino que seja igual ao tamanho da VM de origem. Se o tamanho correspondente não estiver disponível, Site Recovery escolherá automaticamente o tamanho mais próximo disponível.

Se não houver um tamanho que dê suporte à configuração da VM de origem, a seguinte mensagem será exibida:

> "Não foi possível habilitar a replicação para a máquina virtual *VmName*".

### <a name="possible-causes"></a>Possíveis causas

- Sua ID de assinatura não está habilitada para criar VMs no local da região de destino.
- Sua ID de assinatura não está habilitada ou não tem cota suficiente para criar tamanhos de VM específicos no local da região de destino.
- Nenhum tamanho de VM de destino adequado é encontrado para corresponder à contagem de NIC (placa de interface de rede) da VM de origem (2), para a ID da assinatura no local da região de destino.

### <a name="fix-the-problem"></a>Corrigir o problema

Contate o [suporte de cobrança do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) para habilitar sua assinatura para criar VMs dos tamanhos necessários no local de destino. Em seguida, repita a operação com falha.

Se o local de destino tiver uma restrição de capacidade, desabilite a replicação para ela. Em seguida, habilite a replicação para um local diferente em que sua assinatura tem uma cota suficiente para criar VMs dos tamanhos necessários.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificados raiz confiáveis (código de erro 151066)

Se nem todos os certificados raiz confiáveis mais recentes estiverem presentes na VM, o trabalho de Site Recovery "Habilitar replicação" poderá falhar. A autenticação e a autorização de chamadas de serviço Site Recovery da VM falham sem esses certificados. 

Se o trabalho de "Habilitar replicação" falhar, a seguinte mensagem será exibida:

> "Falha na configuração de Site Recovery."

### <a name="possible-cause"></a>Causa possível

Os certificados raiz confiáveis necessários para autorização e autenticação não estão presentes na máquina virtual.

### <a name="fix-the-problem"></a>Corrigir o problema

#### <a name="windows"></a>Windows

Para uma VM que executa o sistema operacional Windows, instale as atualizações mais recentes do Windows na VM para que todos os certificados raiz confiáveis estejam presentes no computador. Siga o processo típico do gerenciamento de atualizações do Windows Update ou do gerenciamento de certificados em sua organização para obter os certificados raiz mais recentes e a lista de certificados revogados atualizada nas VMs.

Se fizer parte de um ambiente desligado, siga o processo de atualização padrão do Windows na sua organização para obter os certificados. Se os certificados necessários não estão presentes na VM, as chamadas para o serviço de recuperação de Site falharem por motivos de segurança.

Para verificar se o problema está resolvido, vá para login.microsoftonline.com partir de um browser na VM.

Para obter mais informações, consulte [Configurar raízes confiáveis e certificados não permitidos](https://technet.microsoft.com/library/dn265983.aspx).

#### <a name="linux"></a>Linux

Siga as orientações fornecidas pelo distribuidor da sua versão do sistema operacional Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados mais recente na VM.

Como o SUSE Linux usa links simbólicos (ou *symlinks*) para manter uma lista de certificados, siga estas etapas:

1. Inicie sessão como um utilizador de raiz.

1. Execute este comando para alterar o diretório:

    **n º de CD/etc/SSL/Certs**

1. Verifique se o certificado de AC raiz da Symantec está presente:

    **# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Se o certificado de autoridade de certificação raiz da Symantec não for encontrado, execute o comando a seguir para baixar o arquivo. Verifique se há erros e siga as ações recomendadas para falhas de rede.

    **# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Verifique se o certificado de autoridade de certificação raiz do Baltimore está presente:

    **# ls Baltimore_CyberTrust_Root. pem**

1. Se o certificado de autoridade de certificação raiz do Baltimore não for encontrado, execute este comando para baixar o certificado:

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root. pem**

1. Verifique se o certificado de DigiCert_Global_Root_CA está presente:

    **# ls DigiCert_Global_Root_CA. pem**

1. Se o DigiCert_Global_Root_CA não for encontrado, execute os seguintes comandos para baixar o certificado:

    **# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# OpenSSL X509-in DigiCertGlobalRootCA. CRT-informar a der-out PEM-out DigiCert_Global_Root_CA. pem**

1. Execute o script de rehash para atualizar os hashes de entidade do certificado para os certificados recentemente baixados:

    **n º de c_rehash**

1. Execute estes comandos para verificar se os hashes da entidade como symlinks foram criados para os certificados:

    - Command:

        **# ls-l | grep Baltimore**

    - Saída:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - Command:

        **# ls-l | VeriSign_Class_3_Public_Primary_Certification_Authority_G5 grep**

    - Saída:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - Command:

        **# ls-l | DigiCert_Global_Root grep**

    - Saída:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. Crie uma cópia do arquivo VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem com o nome de arquivo b204d74a. 0:

    **# CP VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem b204d74a. 0**

1. Crie uma cópia do arquivo Baltimore_CyberTrust_Root. pem com o nome de arquivo 653b494a. 0:

    **# CP Baltimore_CyberTrust_Root. pem 653b494a. 0**

1. Crie uma cópia do arquivo DigiCert_Global_Root_CA. pem com o nome de arquivo 3513523f. 0:

    **# CP DigiCert_Global_Root_CA. pem 3513523f. 0**

1. Verifique se os arquivos estão presentes:

    - Command:

        **# ls-l 653b494a. 0 b204d74a. 0 3513523f. 0**

    - Saída

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividade de saída para intervalos de IP ou URLs do Site Recovery (código de erro 151037 ou 151072)

Para que a replicação de Site Recovery funcione, a conectividade de saída é necessária da VM para URLs ou intervalos de IP específicos. Se a VM estiver protegido por uma firewall ou utiliza regras de grupo (NSG) de segurança de rede para controlar a conectividade de saída, poderá deparar-se com um desses problemas.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problema 1: falha ao registrar a máquina virtual do Azure com Site Recovery (código de erro 151195)

#### <a name="possible-cause"></a>Causa possível 

A conexão com Site Recovery pontos de extremidade não pode ser estabelecida devido a uma falha de resolução de DNS.

Esse problema ocorre com mais frequência durante a nova proteção, quando você tiver feito o failover da máquina virtual, mas o servidor DNS não estiver acessível da região de recuperação de desastres (DR).

#### <a name="fix-the-problem"></a>Corrigir o problema

Se você estiver usando um DNS personalizado, verifique se o servidor DNS está acessível na região de recuperação de desastre. Para descobrir se você tem um DNS personalizado, na VM, vá para rede de *recuperação de desastres* > **servidores DNS**.

![Lista de servidores DNS personalizados](./media/azure-to-azure-troubleshoot-errors/custom_dns.PNG)

Tente aceder ao servidor DNS a partir da máquina virtual. Se o servidor não estiver acessível, torne-o acessível fazendo o failover do servidor DNS ou criando a linha de site entre a rede de DR e o DNS.

### <a name="issue-2-site-recovery-configuration-failed-error-code-151196"></a>Problema 2: falha na configuração do Site Recovery (código de erro 151196)

#### <a name="possible-cause"></a>Causa possível

Não é possível estabelecer a conexão com os pontos de extremidade IP4 de autenticação e identidade do Office 365.

#### <a name="fix-the-problem"></a>Corrigir o problema

Site Recovery requer acesso aos intervalos de IP do Office 365 para autenticação.
Se você estiver usando regras de NSG do Azure ou proxy de firewall para controlar a conectividade de rede de saída na VM, certifique-se de permitir a comunicação com intervalos de IP do Office 365. Crie uma regra NSG com base em uma [marca de serviço do Azure Active Directory (Azure AD)](../virtual-network/security-overview.md#service-tags), permitindo o acesso a todos os endereços IP correspondentes ao Azure AD. Se novos endereços forem adicionados ao Azure AD no futuro, você deverá criar novas regras do NSG.

> [!NOTE]
> Se as VMs estiverem atrás de um balanceador de carga interno *padrão* , o balanceador de carga por padrão não terá acesso aos intervalos de IP do Office 365 (ou seja, login.microsoftonline.com). Altere o tipo de balanceador de carga interno para *básico* ou crie acesso de saída, conforme descrito no artigo [configurar regras de saída e balanceamento de carga](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-error-code-151197"></a>Problema 3: falha na configuração do Site Recovery (código de erro 151197)

#### <a name="possible-cause"></a>Causa possível

A conexão não pode ser estabelecida com Site Recovery pontos de extremidade de serviço.

#### <a name="fix-the-problem"></a>Corrigir o problema

Site Recovery requer acesso a [intervalos de IP site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges), dependendo da região. Verifique se os intervalos de IP necessários estão acessíveis da máquina virtual.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-an-on-premises-proxy-server-error-code-151072"></a>Problema 4: a replicação do Azure para o Azure falhou quando o tráfego de rede passa por um servidor proxy local (código de erro 151072)

#### <a name="possible-cause"></a>Causa possível

As configurações de proxy personalizadas são inválidas e o agente do serviço de mobilidade Site Recovery não detectou automaticamente as configurações de proxy do Internet Explorer.

#### <a name="fix-the-problem"></a>Corrigir o problema

O agente do serviço de mobilidade detecta as configurações de proxy do Internet Explorer no Windows e do/etc/environment no Linux.

Se preferir definir o proxy somente para o serviço de mobilidade, você poderá fornecer os detalhes do proxy no arquivo ProxyInfo. conf nestes locais:

- **Linux**:/usr/local/InMage/config/
- **Windows**: C:\ProgramData\Microsoft Azure site Recovery\Config

No ProxyInfo. conf, forneça as configurações de proxy no seguinte formato de arquivo de inicialização:

> [*proxy*]

> Endereço = *http://1.2.3.4*

> Porta =*567*


> [!NOTE]
> O agente do serviço de mobilidade Site Recovery dá suporte apenas a *proxies não autenticados*.

### <a name="more-information"></a>Mais informações

Para especificar as [URLs necessárias](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou os [intervalos de IP necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), siga as orientações em [sobre a rede na replicação do Azure para o Azure](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Não foi encontrado na máquina (código de erro 150039) de disco

Um disco novo anexado à VM tem de ser inicializado. Se o disco não for encontrado, a seguinte mensagem será exibida:

> "O disco de dados do Azure *diskname* *DiskURI* com o número de unidade lógica *LUNValue* *LUN* não foi mapeado para um disco correspondente que está sendo relatado de dentro da VM que tem o mesmo valor de LUN.

### <a name="possible-causes"></a>Possíveis causas

- Um novo disco de dados foi anexado à VM, mas não foi inicializado.
- O disco de dados dentro da VM não está relatando corretamente o valor de LUN (número de unidade lógica) no qual o disco foi anexado à VM.

### <a name="fix-the-problem"></a>Corrigir o problema

Verifique se os discos de dados foram inicializados e repita a operação.

- **Windows**: [anexe e inicialize um novo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).

- **Linux**: [inicializar um novo disco de dados no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Se o problema persistir, contacte o suporte.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Um ou mais discos estão disponíveis para proteção (código de erro 153039)

### <a name="possible-causes"></a>Possíveis causas

- Um ou mais discos foram adicionados recentemente à máquina virtual após a proteção.
- Um ou mais discos foram inicializados após a proteção da máquina virtual.

### <a name="fix-the-problem"></a>Corrigir o problema

Para tornar o status de replicação da VM íntegro novamente, você pode optar por proteger os discos ou ignorar o aviso.

#### <a name="to-protect-the-disks"></a>Para proteger os discos

1. Vá para **itens replicados** > *nome* > **da VM discos**.
1. Selecione o disco desprotegido e, em seguida, selecione **habilitar replicação**:

    ![Habilitar a replicação em discos de VM](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>Para ignorar o aviso

1. Vá para **itens replicados** > *nome da VM*.
1. Selecione o aviso na seção **visão geral** e, em seguida, selecione **OK**.

    ![Ignorar aviso de novo disco](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Remover a máquina virtual do cofre concluída com informações (código de erro 150225)

Quando ele protege a máquina virtual, Site Recovery cria alguns links na máquina virtual de origem. Quando você remove a proteção ou desabilita a replicação, Site Recovery remove esses links como parte do trabalho de limpeza. Se a máquina virtual tiver um bloqueio de recurso, o trabalho de limpeza será concluído com as informações. As informações indicam que a máquina virtual foi removida do cofre dos serviços de recuperação, mas que alguns dos links obsoletos não puderam ser limpos no computador de origem.

Você pode ignorar esse aviso se você nunca pretender proteger essa máquina virtual novamente. Mas se você precisar proteger essa máquina virtual posteriormente, siga as etapas em "corrigir o problema" para limpar os links.

> [!WARNING]
> Se você não fizer a limpeza:
>
> - Quando você habilita a replicação por meio do cofre dos serviços de recuperação, a máquina virtual não será listada.
> - Se você tentar proteger a VM usando **as configurações** de > de **máquina virtual** > **recuperação de desastre**, a operação falhará com a mensagem "a replicação não pode ser habilitada devido aos links de recursos obsoletos existentes na VM".

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> Site Recovery não exclui a máquina virtual de origem nem a afeta de forma alguma enquanto você executa essas etapas.

1. Remova o bloqueio do grupo de recursos VM ou VM. Por exemplo, na imagem a seguir, o bloqueio de recurso na VM denominada "MoveDemo" deve ser excluído:

    ![Remover bloqueio da VM](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Baixe o script para [remover uma configuração de site Recovery obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script, que é chamado de Cleanup-stale-asr-config-Azure-VM. ps1. Forneça a ID da assinatura, o grupo de recursos da VM e o nome da VM como parâmetros.
1. Se você for solicitado a fornecer as credenciais do Azure, forneça-as. Em seguida, verifique se o script é executado sem falhas.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>A replicação não pode ser habilitada devido a links de recursos obsoletos na VM (código de erro 150226)

### <a name="possible-cause"></a>Causa possível

A máquina virtual tem uma configuração obsoleta da proteção de Site Recovery anterior.

Uma configuração obsoleta pode ocorrer em uma VM do Azure se você habilitou a replicação para a VM do Azure usando Site Recovery e, em seguida:

- Você desabilitou a replicação, mas a VM de origem tinha um bloqueio de recurso.
- Você excluiu o cofre Site Recovery sem desabilitar explicitamente a replicação na VM.
- Você excluiu o grupo de recursos que contém o cofre de Site Recovery sem desabilitar explicitamente a replicação na VM.

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> Site Recovery não exclui a máquina virtual de origem nem a afeta de forma alguma enquanto você executa essas etapas.

1. Remova o bloqueio do grupo de recursos VM ou VM. Por exemplo, na imagem a seguir, o bloqueio de recurso na VM denominada "MoveDemo" deve ser excluído:

    ![Remover bloqueio da VM](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Baixe o script para [remover uma configuração de site Recovery obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script, que é chamado de Cleanup-stale-asr-config-Azure-VM. ps1. Forneça a ID da assinatura, o grupo de recursos da VM e o nome da VM como parâmetros.
1. Se você for solicitado a fornecer as credenciais do Azure, forneça-as. Em seguida, verifique se o script é executado sem falhas.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Não é possível ver a VM ou o grupo de recursos do Azure para a seleção no trabalho "Habilitar replicação"

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Causa 1: o grupo de recursos e a máquina virtual de origem estão em locais diferentes

Atualmente, Site Recovery requer que o grupo de recursos da região de origem e as máquinas virtuais estejam no mesmo local. Se não forem, você não poderá encontrar a máquina virtual ou o grupo de recursos ao tentar aplicar a proteção.

Como alternativa, você pode habilitar a replicação da VM em vez do cofre dos serviços de recuperação. Vá para **origem VM** > **Propriedades** > **recuperação de desastre** e habilite a replicação.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>Causa 2: o grupo de recursos não faz parte da assinatura selecionada

Talvez você não consiga localizar o grupo de recursos no momento da proteção se o grupo de recursos não fizer parte da assinatura selecionada. Verifique se o grupo de recursos pertence à assinatura que você está usando.

### <a name="cause-3-stale-configuration"></a>Causa 3: configuração obsoleta

Talvez você não veja a VM que deseja habilitar para a replicação se uma configuração de Site Recovery obsoleta tiver sido deixada na VM do Azure. Essa condição pode ocorrer se você habilitou a replicação para a VM do Azure usando Site Recovery e, em seguida:

- Você excluiu o cofre Site Recovery sem desabilitar explicitamente a replicação na VM.
- Você excluiu o grupo de recursos que contém o cofre de Site Recovery sem desabilitar explicitamente a replicação na VM.
- Você desabilitou a replicação, mas a VM de origem tinha um bloqueio de recurso.

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> Certifique-se de atualizar o módulo "AzureRM. resources" antes de usar o script mencionado nesta seção.  Site Recovery não exclui a máquina virtual de origem nem a afeta de forma alguma enquanto você executa essas etapas.

1. Remova o bloqueio, se houver, do grupo de recursos VM ou VM. Por exemplo, na imagem a seguir, o bloqueio de recurso na VM denominada "MoveDemo" deve ser excluído:

    ![Remover bloqueio da VM](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Baixe o script para [remover uma configuração de site Recovery obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script, que é chamado de Cleanup-stale-asr-config-Azure-VM. ps1. Forneça a ID da assinatura, o grupo de recursos da VM e o nome da VM como parâmetros.
1. Se você for solicitado a fornecer as credenciais do Azure, forneça-as. Em seguida, verifique se o script é executado sem falhas.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Não é possível selecionar uma máquina virtual para proteção

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>Causa 1: a máquina virtual tem uma extensão instalada em um estado de falha ou sem resposta

Vá para **máquinas virtuais** > **configurações** > **extensões** e verifique se há extensões em estado de falha. Desinstale qualquer extensão com falha e tente proteger a máquina virtual novamente.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>Causa 2: o estado de provisionamento da VM não é válido

Consulte as etapas de solução de problemas no [estado de provisionamento da VM não é válido](#the-vms-provisioning-state-is-not-valid-error-code-150019), mais adiante neste artigo.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>O estado de provisionamento da VM não é válido (código de erro 150019)

Para habilitar a replicação na VM, seu estado de provisionamento deve ser **bem-sucedido**. Siga estas etapas para verificar o estado de provisionamento:

1. Na portal do Azure, selecione o **Gerenciador de recursos** em **todos os serviços**.
1. Expanda a **subscrições** lista e selecione a sua subscrição.
1. Expanda a **ResourceGroups** lista e selecione o grupo de recursos da VM.
1. Expanda a lista de **recursos** e selecione sua VM.
1. Verifique o campo **provisioningState** na exibição de instância no lado direito.

### <a name="fix-the-problem"></a>Corrigir o problema

- Se **provisioningState** é **falha**, contacte o suporte com detalhes para resolver problemas.
- Se o **provisioningState** estiver sendo **atualizado**, outra extensão poderá estar sendo implantada. Verifique se há alguma operação em andamento na VM, aguarde até que elas sejam concluídas e tente novamente a Site Recovery trabalho "Habilitar replicação" com falha.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Não é possível selecionar a VM de destino (a guia Seleção de rede não está disponível)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Causa 1: sua VM está conectada a uma rede que já está mapeada para uma rede de destino

Se a VM de origem fizer parte de uma rede virtual e outra VM da mesma rede virtual já estiver mapeada com uma rede no grupo de recursos de destino, a caixa de listagem suspensa seleção de rede estará indisponível (aparece esmaecida) por padrão.

![Lista de seleção de rede não disponível](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Causa 2: você protegeu anteriormente a VM usando Site Recovery e, em seguida, desabilitou a replicação

Desabilitar a replicação de uma VM não exclui o mapeamento de rede. O mapeamento deve ser excluído do cofre dos serviços de recuperação onde a VM foi protegida. Vá para *cofre dos serviços de recuperação* > **site Recovery** > **mapeamento de rede**de infraestrutura.

![Excluir mapeamento de rede](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

A rede de destino que foi configurada durante a configuração de recuperação de desastre pode ser alterada após a configuração inicial, depois que a VM for protegida:

![Modificar mapeamento de rede](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

Observe que a alteração do mapeamento de rede afeta todas as VMs protegidas que usam o mesmo mapeamento de rede.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>Erro de serviço de cópias de sombra de volume ou COM+ (código de erro 151025)

Quando esse erro ocorre, a seguinte mensagem é exibida:

> "Falha ao instalar a extensão de Site Recovery"

### <a name="possible-causes"></a>Possíveis causas

- O serviço de aplicativo do sistema COM+ está desabilitado.
- O serviço de cópias de sombra de volume está desabilitado.

### <a name="fix-the-problem"></a>Corrigir o problema

Defina o aplicativo do sistema COM+ e os serviços de cópia de sombra de volume como modo de inicialização automático ou manual.

1. Abra o console de serviços no Windows.
1. Verifique se o aplicativo do sistema COM+ e os serviços de cópia de sombra de volume não estão definidos como **desabilitados** como seu **tipo de inicialização**.

    ![Verificar o tipo de inicialização de aplicativo do sistema COM+ e serviços de cópias de sombra de volume](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Tamanho de disco gerenciado sem suporte (código de erro 150172)

Quando esse erro ocorre, a seguinte mensagem é exibida:

> "Não foi possível habilitar a proteção para a máquina virtual, pois ela tem *diskname* com tamanho de *discosize*) *, que é inferior ao tamanho mínimo com suporte de 1024 MB."

### <a name="possible-cause"></a>Causa possível

O disco é menor do que o tamanho com suporte de 1024 MB.

### <a name="fix-the-problem"></a>Corrigir o problema

Verifique se o tamanho do disco está dentro do intervalo de tamanho com suporte e repita a operação.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>A proteção não foi habilitada porque a configuração de GRUB inclui o nome do dispositivo em vez do UUID (código de erro 151126)

### <a name="possible-cause"></a>Causa possível

Os arquivos de configuração GRUB do Linux (/boot/grub/menu.lst ",/boot/grub/grub.cfg,/boot/Grub2/grub.cfg ou/etc/default/grub) podem especificar os nomes de dispositivo reais em vez de valores UUID para os parâmetros *root* e *resume* . Site Recovery requer UUIDs porque os nomes de dispositivo podem ser alterados. Após a reinicialização, uma VM pode não vir com o mesmo nome no failover, resultando em problemas.

Os exemplos a seguir são linhas de arquivos GRUB em que os nomes de dispositivo (mostrados em negrito) aparecem em vez dos UUIDs necessários:

- /Boot/Grub2/grub.cfg de arquivo

  > linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts

- Arquivo:/boot/grub/menu.lst

  > kernel/boot/vmlinuz-3.0.101-63-Default **raiz =/dev/sda2** **currículo =/dev/sda1** Splash = silencioso crashkernel = 256M-: 128M showopts VGA = 0x314


### <a name="fix-the-problem"></a>Corrigir o problema

Substitua cada nome de dispositivo pelo UUID correspondente:

1. Localize o UUID do dispositivo executando o comando **blkid** ***nome do dispositivo***. Por exemplo:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Substitua o nome do dispositivo pelo UUID, no UUID de **formatos raiz = uuid**=*UUID* e **resume =** UUID *de=.* Por exemplo, após a substituição, a linha de/boot/grub/menu.lst (discutida anteriormente) ficaria assim:

    > kernel/boot/vmlinuz-3.0.101-63-Default **raiz = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** Splash = Silent crashkernel = 256M-: 128M showopts VGA = 0x314

1. Tente novamente a proteção.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>Falha ao habilitar a proteção porque o dispositivo mencionado na configuração do GRUB não existe (código de erro 151124)

### <a name="possible-cause"></a>Causa possível

Os arquivos de configuração do GRUB (/boot/grub/menu.lst,/boot/grub/grub.cfg,/boot/Grub2/grub.cfg ou/etc/default/grub) podem conter os parâmetros *Rd.LVM.lv* ou *rd_LVM_LV*. Esses parâmetros identificam os dispositivos LVM (Gerenciador de volumes lógicos) que serão descobertos no momento da inicialização. Se esses dispositivos LVM não existirem, o próprio sistema protegido não será inicializado e ficará preso no processo de inicialização. O mesmo problema também será visto com a VM de failover. Aqui estão alguns exemplos:

- Arquivo:/boot/Grub2/grub.cfg em RHEL7:

    > linux16/vmlinuz-3.10.0-957.EL7. x86_64 raiz =/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel = 128M\@64M **Rd. LVM. lv = rootvg/raiz Rd. LVM. lv = rootvg/swap** rhgb Quiet LANG = en_US. UTF-8

- Arquivo:/etc/default/grub em RHEL7:

    > GRUB_CMDLINE_LINUX = "crashkernel = auto **Rd. LVM. lv = rootvg/raiz Rd. LVM. lv = rootvg/swap** rhgb Quiet"

- Arquivo:/boot/grub/menu.lst em RHEL6:

    > /vmlinuz-2.6.32-754.el6. de kernel x86_64 ro raiz = UUID = 36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US. UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = auto **rd_LVM_LV = rootvg/lv_root** keyboardvalue = PC keyTable = US **rd_LVM_LV = rootvg/lv_swap** rd_NO_DM rhgb Quiet

Em cada exemplo, a parte em negrito mostra que o GRUB precisa detectar dois dispositivos LVM com os nomes "root" e "swap" do grupo de volumes "rootvg".

### <a name="fix-the-problem"></a>Corrigir o problema

Se o dispositivo LVM não existir, crie-o ou remova os parâmetros correspondentes dos arquivos de configuração do GRUB. Em seguida, tente novamente habilitar a proteção.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Uma atualização do serviço de mobilidade Site Recovery concluída com avisos (código de erro 151083)

O serviço de mobilidade Site Recovery tem muitos componentes, um dos quais é chamado de driver de filtro. O driver de filtro é carregado na memória do sistema somente durante a reinicialização do sistema. Sempre que uma atualização do serviço de mobilidade inclui alterações de driver de filtro, o computador é atualizado, mas você ainda vê um aviso de que algumas correções exigem uma reinicialização. O aviso é exibido porque as correções do driver de filtro só podem entrar em vigor quando o novo driver de filtro é carregado, o que ocorre apenas durante uma reinicialização.

> [!NOTE]
> Isso é apenas um aviso. A replicação existente continua funcionando mesmo após a nova atualização do agente. Você pode optar por reiniciar sempre que desejar os benefícios do novo driver de filtro, mas o driver de filtro antigo continuará funcionando se você não reiniciar.
>
> Além do driver de filtro, os benefícios de quaisquer outros aprimoramentos e correções na atualização do serviço de mobilidade entram em vigor sem a necessidade de uma reinicialização.  

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Não foi possível habilitar a proteção porque o disco gerenciado de réplica já existe, sem as marcas esperadas, no grupo de recursos de destino (código de erro 150161)

### <a name="possible-cause"></a>Causa possível

Esse problema pode ocorrer se a máquina virtual já tiver sido protegida anteriormente e quando a replicação tiver sido desabilitada, o disco de réplica não foi limpo.

### <a name="fix-the-problem"></a>Corrigir o problema

Exclua o disco de réplica identificado na mensagem de erro e repita o trabalho de proteção com falha.

## <a name="next-steps"></a>Passos seguintes

[Replicar máquinas virtuais do Azure](site-recovery-replicate-azure-to-azure.md)
