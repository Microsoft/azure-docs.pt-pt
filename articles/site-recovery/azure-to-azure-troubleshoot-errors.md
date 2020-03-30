---
title: Resolução de problemas da réplica do Azure VM na recuperação do site do Azure
description: Erros de resolução de problemas ao replicar máquinas virtuais Azure para recuperação de desastres.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: rochakm
ms.openlocfilehash: c131609a5622061e2ea49db422bc4e9da96666d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276687"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Erro de replicação de VM Azure-to-Azure

Este artigo descreve como resolver erros comuns na Recuperação do Site Azure durante a replicação e recuperação de máquinas virtuais Azure (VMs) de uma região para outra. Para obter mais informações sobre configurações suportadas, consulte a matriz de [suporte para replicar VMs Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a><a name="azure-resource-quota-issues-error-code-150097"></a>Problemas de quotas de recursos Azure (código de erro 150097)

Certifique-se de que a sua subscrição está ativada para criar VMs Azure na região alvo que pretende utilizar como a sua região de recuperação de desastres. Certifique-se também de que a sua subscrição tem quota suficiente para criar VMs dos tamanhos necessários. Por padrão, a Recuperação do Site escolhe um tamanho VM alvo que é o mesmo que o tamanho VM de origem. Se o tamanho correspondente não estiver disponível, a Recuperação do Site escolhe automaticamente o tamanho mais próximo disponível.

Se não houver tamanho que suporte a configuração VM de origem, aparece a seguinte mensagem:

> "A replicação não podia ser ativada para a máquina virtual *VmName."*

### <a name="possible-causes"></a>Possíveis causas

- O seu ID de subscrição não está habilitado a criar quaisquer VMs na localização da região alvo.
- O seu ID de subscrição não está ativado, ou não tem quota suficiente, para criar tamanhos de VM específicos na localização da região alvo.
- Não é encontrado tamanho de VM de destino adequado que corresponda à contagem de interface de rede (NIC) da fonte VM (2), para o ID de subscrição na localização da região alvo.

### <a name="fix-the-problem"></a>Corrigir o problema

Contacte o [suporte de faturação do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) para permitir que a sua subscrição crie VMs dos tamanhos necessários no local-alvo. Então, tente novamente a operação falhada.

Se a localização do alvo tiver uma restrição de capacidade, desative a replicação. Em seguida, ative a replicação para um local diferente onde a sua subscrição tenha quota suficiente para criar VMs dos tamanhos necessários.

## <a name="trusted-root-certificates-error-code-151066"></a><a name="trusted-root-certificates-error-code-151066"></a>Certificados de raiz fidedignos (código de erro 151066)

Se não estiverem presentes todos os certificados de raiz fidedignos mais recentes no VM, o seu trabalho de recuperação do site "Enable replication" pode falhar. Autenticação e autorização de chamadas de serviço de Recuperação do Local do VM falham sem estes certificados.

Se o trabalho de replicação "Ativar" falhar, aparece a seguinte mensagem:

> "A configuração de recuperação do site falhou."

### <a name="possible-cause"></a>Causa possível

Os certificados de raiz fidedignos necessários para autorização e autenticação não estão presentes na máquina virtual.

### <a name="fix-the-problem"></a>Corrigir o problema

#### <a name="windows"></a>Windows

Para um VM que executa o sistema operativo Windows, instale as mais recentes atualizações do Windows no VM de modo a que todos os certificados de raiz fidedignos estejam presentes na máquina. Siga o típico processo de gestão de atualizações do Windows ou de gestão de certificados na sua organização para obter os mais recentes certificados de raiz e a lista atualizada de revogação de certificados nos VMs.

Se estiver num ambiente desligado, siga o processo padrão de atualização do Windows na sua organização para obter os certificados. Se os certificados necessários não estiverem presentes no VM, as chamadas para o serviço de Recuperação do Site falham por razões de segurança.

Para verificar se o problema está resolvido, vá a login.microsoftonline.com a partir de um navegador no seu VM.

Para mais informações, consulte [As raízes fidedignas da Configure e os certificados proibidos](https://technet.microsoft.com/library/dn265983.aspx).

#### <a name="linux"></a>Linux

Siga as orientações fornecidas pelo distribuidor da sua versão do sistema operativo Linux para obter os mais recentes certificados de raiz fidedignos e a mais recente lista de revogação de certificados no VM.

Uma vez que o SUSE Linux utiliza ligações simbólicas (ou *simlinks)* para manter uma lista de certificados, siga estes passos:

1. Inscreva-se como um utilizador de raiz.

1. Executar este comando para alterar o diretório:

    **# cd /etc/ssl/certs**

1. Verifique se o certificado ca raiz symantec está presente:

    **# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. Se o certificado CA raiz Symantec não for encontrado, execute o seguinte comando para descarregar o ficheiro. Verifique se há erros e siga as ações recomendadas para falhas de rede.

    **# wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. Verifique se o certificado de AC raiz de Baltimore está presente:

    **# ls Baltimore_CyberTrust_Root.pem**

1. Se o certificado de CA raiz de Baltimore não for encontrado, faça este comando para descarregar o certificado:

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem**

1. Verifique se o certificado de DigiCert_Global_Root_CA está presente:

    **# ls DigiCert_Global_Root_CA.pem**

1. Se o DigiCert_Global_Root_CA não for encontrado, execute os seguintes comandos para descarregar o certificado:

    **# wgethttp://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# openssl x509 -in DigiCertGlobalRootCA.crt -inform der-outform pem -out DigiCert_Global_Root_CA.pem**

1. Executar o script de rehash para atualizar o tema do certificado hashes para os certificados recém-descarregados:

    **C_rehash**

1. Executar estes comandos para verificar se o sujeito hashes como symlinks foram criados para os certificados:

    - Comando:

        **# ls -l [ ] grep Baltimore**

    - Saída:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - Comando:

        **# ls -l [ ] grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - Saída:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - Comando:

        **# ls -l [ ] grep DigiCert_Global_Root**

    - Saída:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. Crie uma cópia do ficheiro VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem com o nome de ficheiro b204d74a.0:

    **# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0**

1. Crie uma cópia do ficheiro Baltimore_CyberTrust_Root.pem com o nome de ficheiro 653b494a.0:

    **# cp Baltimore_CyberTrust_Root.pem 653b494a.0**

1. Crie uma cópia do ficheiro DigiCert_Global_Root_CA.pem com o nome de ficheiro 3513523f.0:

    **# cp DigiCert_Global_Root_CA.pem 3513523f.0**

1. Verifique se os ficheiros estão presentes:

    - Comando:

        **# ls -l 653b494a.0 b204d74a.0 3513523f.0**

    - Saída

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividade de saída para URLs de Recuperação do Site ou intervalos IP (código de erro 151037 ou 151072)

Para que a replicação da recuperação do site funcione, a conectividade de saída a URLs específicos é necessária a partir do VM. Se o seu VM estiver por detrás de uma firewall ou utilizar regras do grupo de segurança de rede (NSG) para controlar a conectividade de saída, poderá enfrentar um destes problemas. Por favor, note que enquanto continuamos a apoiar o acesso de saída através de URLs, a utilização de uma lista de intervalos IP já não é suportada.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Edição 1: Falha no registo da máquina virtual Azure com recuperação do site (151195) </br>
- **Possível causa** </br>
  - A ligação não pode ser estabelecida aos pontos finais de recuperação do local devido a falha na resolução do DNS.
  - Esta situação é observada com maior frequência durante reaplicação da proteção, quando é efetuada a ativação pós-falha da máquina virtual, mas o servidor DNS não está acessível a partir da região de RD.

- **Resolução**
   - Se estiver a utilizar DNS personalizado, certifique-se de que o servidor DNS está acessível a partir da região de Recuperação de Desastres. Para verificar se tem um DNS personalizado vá à rede VM> De recuperação de desastres> servidores DNS. Tente aceder ao servidor DNS a partir da máquina virtual. Se não estiver acessível, torne-o acessível falhando no servidor DNS ou criando a linha de site entre a rede DR e o DNS.

    ![erro com](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Edição 2: A configuração de recuperação do site falhou (151196)
- **Possível causa** </br>
  - A ligação não pode ser estabelecida aos pontos finais de autenticação e identidade ip4 do Office 365.

- **Resolução**
  - A Recuperação do Site Azure exigiu o acesso às gamas de IPs do Office 365 para autenticação.
    Se estiver a utilizar regras/proxy de firewall do Grupo de Segurança da Rede Azure (NSG) para controlar a conectividade da rede de saída no VM, certifique-se de que utiliza a regra NSG baseada em serviço simultânico do [Azure Ative Directory (AAD)](../virtual-network/security-overview.md#service-tags) para permitir o acesso ao AAD. Já não apoiamos as regras de NSG baseadas em endereços IP.


### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Edição 3: A configuração de recuperação do site falhou (151197)
- **Possível causa** </br>
  - A ligação não pode ser estabelecida aos pontos finais do serviço de recuperação do local do Azure.

- **Resolução**
  - Se estiver a utilizar regras/proxy do Grupo de Segurança da Rede Azure (NSG) para controlar a conectividade da rede de saída no VM, certifique-se de que utiliza etiquetas de serviço. Já não apoiamos a utilização de uma lista de endereços IP via NSGs para recuperação de sites azure (ASR).


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>Edição 4: A replicação A2A falhou quando o tráfego de rede passa pelo servidor proxy no local (151072)
 - **Possível causa** </br>
   - As configurações de procuração personalizadas são inválidas e o agente do Serviço de Mobilidade ASR não detetou automaticamente as definições de procuração do IE


 - **Resolução**
   1.    O agente do Serviço de Mobilidade deteta as definições de procuração a partir de IE no Windows e /etc/ambiente no Linux.
   2.  Se preferir definir procuração apenas para o Serviço de Mobilidade ASR, então pode fornecer os detalhes proxy em ProxyInfo.conf localizado em:</br>
       - ``/usr/local/InMage/config/``em ***Linux***
       - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``no ***Windows***
   3.    O ProxyInfo.conf deve ter as definições de proxy no seguinte formato INI.</br>
                   *[procuração]*</br>
                   *Endereço=http://1.2.3.4*</br>
                   *Porto=567*</br>
   4. O agente do Serviço de Mobilidade ASR suporta apenas ***proxies não autenticados.***


### <a name="fix-the-problem"></a>Corrigir o problema

Para adicionar [os URLs necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) a uma lista de autorização, siga os passos no documento de [orientação](site-recovery-azure-to-azure-networking-guidance.md)de rede .


### <a name="more-information"></a>Mais informações

Para especificar [urls necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou [intervalos IP necessários,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)siga as orientações em [sobre a replicação em rede em Azure para Azure](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Disco não encontrado na máquina (código de erro 150039)

Deve ser inicializado um novo disco ligado ao VM. Se o disco não for encontrado, aparece a seguinte mensagem:

> "O disco de dados Azure *DiskName* *DiskURI* com o número de unidade lógica *LUN* *LUNValue* não foi mapeado para um disco correspondente a ser reportado dentro do VM que tem o mesmo valor LUN.

### <a name="possible-causes"></a>Possíveis causas

- Um novo disco de dados foi ligado ao VM, mas não foi inicializado.
- O disco de dados no interior do VM não está a reportar corretamente o valor do número de unidade lógica (LUN) no qual o disco foi ligado ao VM.

### <a name="fix-the-problem"></a>Corrigir o problema

Certifique-se de que os discos de dados são inicializados e, em seguida, tente novamente a operação.

- **Janelas**: [Fixe e inicialize um novo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).

- **Linux**: [Inicialize um novo disco](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)de dados em Linux .

Se o problema persistir, o suporte de contacto.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Um ou mais discos estão disponíveis para proteção (código de erro 153039)

### <a name="possible-causes"></a>Possíveis causas

- Um ou mais discos foram recentemente adicionados à máquina virtual após proteção.
- Um ou mais discos foram inicializados após a proteção da máquina virtual.

### <a name="fix-the-problem"></a>Corrigir o problema

Para tornar o estado de replicação do VM saudável novamente, pode optar por proteger os discos ou descartar o aviso.

#### <a name="to-protect-the-disks"></a>Para proteger os discos

1. Vá para **itens** > replicados*VM nome* > **Discos**.
1. Selecione o disco desprotegido e, em seguida, **selecione a replicação ativar:**

    ![Ativar a replicação em discos VM](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>Para descartar o aviso

1. Vá ao > *nome VM*de **itens replicados.**
1. Selecione o aviso na secção **'Visão Geral'** e, em seguida, selecione **OK**.

    ![Descartar o aviso de novo disco](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Retire a máquina virtual do cofre completado com informações (código de erro 150225)

Quando protege a máquina virtual, a Recuperação do Local cria alguns links na máquina virtual de origem. Quando remove a proteção ou desativa a replicação, a Recuperação do Local remove estes links como parte do trabalho de limpeza. Se a máquina virtual tiver um bloqueio de recursos, o trabalho de limpeza fica concluído com a informação. A informação diz que a máquina virtual foi removida do cofre dos Serviços de Recuperação, mas que algumas das ligações velhas não puderam ser limpas na máquina de origem.

Pode ignorar este aviso se nunca mais pretender proteger esta máquina virtual. Mas se tiver de proteger esta máquina virtual mais tarde, siga os passos em "Corrigir o problema" para limpar os links.

> [!WARNING]
> Se não fizer a limpeza:
>
> - Quando ativar a replicação através do cofre dos Serviços de Recuperação, a máquina virtual não será listada.
> - Se tentar proteger o VM utilizando a**recuperação**de desastres da**Settings** >  **máquina** > virtual, a operação falhará com a mensagem "A replicação não pode ser ativada devido às ligações de recursos existentes no VM."

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> A Recuperação do Site não elimina a máquina virtual de origem nem a afeta de forma alguma enquanto executa estes passos.

1. Retire o bloqueio do grupo de recursos VM ou VM. Por exemplo, na seguinte imagem, o bloqueio de recursos no VM denominado "MoveDemo" deve ser eliminado:

    ![Remover o bloqueio da VM](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Descarregue o script para [remover uma configuração de recuperação de site stale](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script, que é chamado Cleanup-stale-asr-config-Azure-VM.ps1. Forneça o ID de subscrição, o VM Resource Group e o nome VM como parâmetros.
1. Se lhe pedirem credenciais Azure, forneça-as. Em seguida, verifique se o script funciona sem falhas.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>A replicação não pode ser ativada devido a ligações de recursos estonadas no VM (código de erro 150226)

### <a name="possible-cause"></a>Causa possível

A máquina virtual tem uma configuração estagnada da proteção anterior de recuperação do site.

Uma configuração estagnada pode ocorrer num VM Azure se tiver ativado a replicação para o Azure VM utilizando a Recuperação do Local e, em seguida:

- Desativou a replicação, mas a fonte vm tinha um bloqueio de recursos.
- Apagou o cofre de recuperação do site sem desativar explicitamente a replicação no VM.
- Eliminou o grupo de recursos que continha o cofre de recuperação do local sem desativar explicitamente a replicação no VM.

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> A Recuperação do Site não elimina a máquina virtual de origem nem a afeta de forma alguma enquanto executa estes passos.

1. Retire o bloqueio do grupo de recursos VM ou VM. Por exemplo, na seguinte imagem, o bloqueio de recursos no VM denominado "MoveDemo" deve ser eliminado:

    ![Remover o bloqueio da VM](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Descarregue o script para [remover uma configuração de recuperação de site stale](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script, que é chamado Cleanup-stale-asr-config-Azure-VM.ps1. Forneça o ID de subscrição, o VM Resource Group e o nome VM como parâmetros.
1. Se lhe pedirem credenciais Azure, forneça-as. Em seguida, verifique se o script funciona sem falhas.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Incapaz de ver o VM Azure ou o grupo de recursos para a seleção no trabalho de replicação "Enable replication"

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Causa 1: O grupo de recursos e a máquina virtual de origem estão em diferentes locais

Atualmente, a Recuperação do Local exige que o grupo de recursos da região de origem e as máquinas virtuais estejam no mesmo local. Se não forem, não conseguirá encontrar a máquina virtual ou o grupo de recursos quando tentar aplicar proteção.

Como suposição, pode permitir a replicação do VM em vez do cofre dos Serviços de Recuperação. Vá à**Recuperação** de Desastres da **Source VM** > **Properties** > e ative a replicação.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>Causa 2: O grupo de recursos não faz parte da subscrição selecionada

Pode não ser capaz de encontrar o grupo de recursos no momento da proteção se o grupo de recursos não fizer parte da subscrição selecionada. Certifique-se de que o grupo de recursos pertence à subscrição que está a usar.

### <a name="cause-3-stale-configuration"></a>Causa 3: Configuração do velho

Pode não ver o VM que pretende ativar para a replicação se tiver sido deixada uma configuração de recuperação de sítio sosseada no VM Azure. Esta condição pode ocorrer se tiver ativado a replicação para o VM Azure utilizando a Recuperação do Local e, em seguida:

- Apagou o cofre de recuperação do site sem desativar explicitamente a replicação no VM.
- Eliminou o grupo de recursos que continha o cofre de recuperação do local sem desativar explicitamente a replicação no VM.
- Desativou a replicação, mas a fonte vm tinha um bloqueio de recursos.

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
> Certifique-se de atualizar o módulo "AzureRM.Resources" antes de utilizar o script mencionado nesta secção.  A Recuperação do Site não elimina a máquina virtual de origem nem a afeta de forma alguma enquanto executa estes passos.

1. Retire a fechadura, se houver, do grupo de recursos VM ou VM. Por exemplo, na seguinte imagem, o bloqueio de recursos no VM denominado "MoveDemo" deve ser eliminado:

    ![Remover o bloqueio da VM](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Descarregue o script para [remover uma configuração de recuperação de site stale](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script, que é chamado Cleanup-stale-asr-config-Azure-VM.ps1. Forneça o ID de subscrição, o VM Resource Group e o nome VM como parâmetros.
1. Se lhe pedirem credenciais Azure, forneça-as. Em seguida, verifique se o script funciona sem falhas.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Incapaz de selecionar uma máquina virtual para proteção

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>Causa 1: A máquina virtual tem uma extensão instalada num estado falhado ou sem resposta

Vá às**extensões** de**definições** > de **máquinas** > virtuais e verifique se há extensões num estado falhado. Desinstale qualquer extensão falhada e tente novamente proteger a máquina virtual.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>Causa 2: O estado de provisionamento da VM não é válido

Veja-se que os passos de resolução de problemas no [estado de provisionamento da VM não são válidos](#the-vms-provisioning-state-is-not-valid-error-code-150019), mais tarde neste artigo.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>O estado de provisionamento da VM não é válido (código de erro 150019)

Para permitir a replicação no VM, o seu estado de provisionamento deve ser **bem sucedido**. Siga estas medidas para verificar o estado de provisionamento:

1. No portal Azure, selecione o Explorador de **Recursos** de **Todos os Serviços.**
1. Expanda a lista de **Subscrições** e selecione a sua subscrição.
1. Expandir a lista **de Grupos** de Recursos e selecionar o grupo de recursos do VM.
1. Expanda a lista **de Recursos** e selecione o seu VM.
1. Verifique o campo **de provisionamento do Estado** em vista por exemplo do lado direito.

### <a name="fix-the-problem"></a>Corrigir o problema

- Se **o fornecimento do Estado** estiver **falhado,** contacte o suporte com detalhes para resolver problemas.
- Se **o fornecimento do Estado** estiver a **atualizar,** poderá ser implementada outra extensão. Verifique se existem operações em curso no VM, espere que terminem e, em seguida, volte a tentar o trabalho falhado de recuperação do site "Enable replication".

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Incapaz de selecionar o Target VM (o separador de seleção de rede não está disponível)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Causa 1: O seu VM está ligado a uma rede que já está mapeada para uma rede de alvos

Se o VM de origem fizer parte de uma rede virtual, e outro VM da mesma rede virtual já estiver mapeado com uma rede no grupo de recursos-alvo, a caixa de lista de seleção de rede não está disponível (aparece reduzida) por padrão.

![Lista de seleção de rede indisponível](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Causa 2: Protegeu previamente o VM utilizando a Recuperação do Site e, em seguida, desativou a replicação

A replicação incapacitante de um VM não elimina o mapeamento da rede. O mapeamento deve ser apagado do cofre dos Serviços de Recuperação onde o VM estava protegido. Vá ao *cofre* > de recuperação de serviços de recuperação de**infraestruturas** > **de recuperação de mapeamento da rede**de infraestruturas.

![Eliminar o mapeamento da rede](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

A rede-alvo que foi configurada durante a configuração de recuperação de desastres pode ser alterada após a configuração inicial, após a proteção do VM:

![Modificar o mapeamento da rede](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

Note que a alteração do mapeamento da rede afeta todos os VMs protegidos que utilizam esse mesmo mapeamento de rede.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>Erro de serviço COM+ ou Volume Shadow Copy (código de erro 151025)

Quando este erro ocorre, aparece a seguinte mensagem:

> "Extensão de recuperação do local não foi instalada"

### <a name="possible-causes"></a>Possíveis causas

- O serviço de aplicação do sistema COM+ está desativado.
- O serviço Volume Shadow Copy está desativado.

### <a name="fix-the-problem"></a>Corrigir o problema

Detete os serviços de Aplicação do Sistema COM+ e volume shadow copy para o modo de arranque automático ou manual.

1. Abra a consola Services no Windows.
1. Certifique-se de que os serviços de aplicação do sistema COM+ e de cópia de volume shadow copy não estão definidos para **desativados** como o seu Tipo de **Arranque**.

    ![Consulte o tipo de arranque de sistema com+ e serviços de cópia de sombra de volume](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Tamanho do disco gerido não suportado (código de erro 150172)

Quando este erro ocorre, aparece a seguinte mensagem:

> "A proteção não podia ser ativada para a máquina virtual, uma vez que tem *o DiskName* com tamanho *DiskSize*)* que é inferior ao tamanho mínimo suportado 1024 MB."

### <a name="possible-cause"></a>Causa possível

O disco é menor do que o tamanho suportado de 1024 MB.

### <a name="fix-the-problem"></a>Corrigir o problema

Certifique-se de que o tamanho do disco está dentro do intervalo de tamanho suportado e, em seguida, tente novamente a operação.

## <a name="protection-was-not-enabled-because-the-grub-configuration-includes-the-device-name-instead-of-the-uuid-error-code-151126"></a><a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>A proteção não foi ativada porque a configuração GRUB inclui o nome do dispositivo em vez do UUID (código de erro 151126)

### <a name="possible-cause"></a>Causa possível

Os ficheiros de configuração Linux GRUB (/boot/grub/menu.lst", /boot/grub/grub.cfg, /boot/grub2/grub.cfg, ou /etc/default/grub) podem especificar os nomes reais do dispositivo em vez dos valores uUID para a *raiz* e *retomar* os parâmetros. A Recuperação do Site requer UUIDs porque os nomes do dispositivo podem mudar. Após o reinício, um VM pode não ter o mesmo nome na failover, resultando em problemas.

Os seguintes exemplos são linhas de ficheiros GRUB onde os nomes do dispositivo (mostrados em negrito) aparecem em vez dos UUIDs necessários:

- Arquivo /boot/grub2/grub.cfg

  > linux /boot/vmlinuz-3.12.49-11-predefinição **raiz=/dev/sda2** ${extra_cmdline} **currículo=/dev/sda1** splash=showopts silenciosos silenciosos

- Arquivo: /boot/grub/menu.lst

  > Kernel /boot/vmlinuz-3.0.101-63-predefinição **raiz=/dev/sda2** **currículo=/dev/sda1** splash=crashkernel silencioso=256M-:128M showopts vga=0x314


### <a name="fix-the-problem"></a>Corrigir o problema

Substitua cada nome do dispositivo pelo UUID correspondente:

1. Encontre o UUID do dispositivo executando o nome do ***dispositivo*** **de blkid** de comando . Por exemplo:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Substitua o nome do dispositivo pelo seu UUID, nos formatos **root=UUID**=*UUID* e **currículo=UUID**=*UUID*. Por exemplo, após a substituição, a linha de /boot/grub/menu.lst (discutido anteriormente) seria assim:

    > Kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **currículo=UUID=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314

1. Tente a proteção.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>A proteção ativa falhou porque o dispositivo mencionado na configuração GRUB não existe (código de erro 151124)

### <a name="possible-cause"></a>Causa possível

Os ficheiros de configuração GRUB (/boot/grub/menu.lst, /boot/grub/grub.cfg, /boot/grub2/grub.cfg, ou /etc/predefinido/grub) podem conter os parâmetros *rd.lvm.lv* ou *rd_LVM_LV*. Estes parâmetros identificam os dispositivos Lógicos de Gestor de Volume (LVM) que devem ser descobertos no momento do arranque. Se estes dispositivos LVM não existirem, o próprio sistema protegido não arranca e ficará preso no processo de arranque. O mesmo problema também será visto com o VM falhado. Aqui estão poucos exemplos:

- Arquivo: /boot/grub2/grub2/grub.cfg no RHEL7:

    > linux16 /vmlinuz-3.10.0-957.el7.x86_64 raiz=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US. UTF-8

- Arquivo: /etc/predefinido/grub no RHEL7:

    > GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"

- Arquivo: /boot/grub/menu.lst no RHEL6:

    > Kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US. UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto **rd_LVM_LV=rootvg/lv_root** KEYBOARDTYPE=pc KEYTABLE=nós **rd_LVM_LV=rootvg/lv_swap** rd_NO_DM rhgb quiet

Em cada exemplo, a porção em negrito mostra que o GRUB tem de detetar dois dispositivos LVM com os nomes "raiz" e "swap" do grupo de volume "rootvg".

### <a name="fix-the-problem"></a>Corrigir o problema

Se o dispositivo LVM não existir, crie-o ou remova os parâmetros correspondentes dos ficheiros de configuração GRUB. Em seguida, tente novamente para permitir a proteção.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Uma atualização do Serviço de Mobilidade de Recuperação do Site terminou com avisos (código de erro 151083)

O Serviço de Mobilidade de Recuperação do Site tem muitos componentes, um dos quais é chamado de condutor de filtros. O controlador de filtro é carregado na memória do sistema apenas durante o reinício do sistema. Sempre que uma atualização do Serviço de Mobilidade inclui alterações no controlador de filtros, a máquina é atualizada, mas ainda assim vê um aviso de que algumas correções requerem um reinício. O aviso aparece porque as correções do controlador de filtro só podem produzir efeito quando o novo condutor do filtro é carregado, o que só acontece durante o reinício.

> [!NOTE]
> Isto é só um aviso. A replicação existente continua a funcionar mesmo após a atualização do novo agente. Pode optar por reiniciar sempre que quiser os benefícios do novo condutor do filtro, mas o antigo condutor do filtro continua a trabalhar se não reiniciar.
>
> Para além do condutor do filtro, os benefícios de quaisquer outras melhorias e correções na atualização do Serviço de Mobilidade fazem efeito sem exigir um reinício.

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>A proteção não podia ser ativada porque a réplica do disco gerido já existe, sem etiquetas esperadas, no grupo de recursos-alvo (código de erro 150161)

### <a name="possible-cause"></a>Causa possível

Este problema pode ocorrer se a máquina virtual foi previamente protegida, e quando a replicação foi desativada, o disco de réplica não foi limpo.

### <a name="fix-the-problem"></a>Corrigir o problema

Elimine o disco de réplica identificado na mensagem de erro e tente novamente o trabalho de proteção falhado.

## <a name="next-steps"></a>Passos seguintes

[Replicar máquinas virtuais do Azure](site-recovery-replicate-azure-to-azure.md)
