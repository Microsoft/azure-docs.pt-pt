---
title: O Azure Site Recovery de resolução de problemas para erros e problemas de replicação do Azure para o Azure | Documentos da Microsoft
description: Erros e problemas de resolução de problemas ao replicar máquinas virtuais do Azure para recuperação após desastre
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: 4d8ba44cdd5161a1a5ff108837cb57af4cd98835
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034779"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Resolver problemas de replicação de VMS do Azure para o Azure

Este artigo descreve os problemas comuns no Azure Site Recovery quando replicar e recuperar máquinas virtuais do Azure a partir de uma região para outra região e explica como solucionar esses problemas. Para obter mais informações sobre configurações suportadas, consulte a [matriz de suporte para replicar VMs do Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="list-of-errors"></a>Lista de erros
- **[Problemas de cota de recursos do Azure (código de erro 150097)](#azure-resource-quota-issues-error-code-150097)**
- **[Certificados raiz confiáveis (código de erro 151066)](#trusted-root-certificates-error-code-151066)**
- **[Conectividade de saída para Site Recovery (código de erro 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)**

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemas de cota de recursos do Azure (código de erro 150097)
Sua assinatura deve estar ativada para criar VMs do Azure na região de destino que pretende utilizar como a sua região de recuperação após desastre. Além disso, a sua subscrição deve tem quota suficiente ativada para criar VMs de tamanho específico. Por predefinição, o Site Recovery escolhe o mesmo tamanho da VM de destino da VM de origem. Se o tamanho correspondente não estiver disponível, o tamanho mais próximo possível será escolhido automaticamente. Se não houver nenhum tamanho correspondente que suporte a configuração de VM de origem, é apresentada esta mensagem de erro:

**Código de erro** | **Causas possíveis** | **Recomendação**
--- | --- | ---
150097<br></br>**Mensagem**: Não foi possível habilitar a replicação para a máquina virtual VmName. | -O seu ID de subscrição poderá não estar ativada para criar todas as VMs na localização de região de destino.</br></br>-O seu ID de subscrição não pode ser ativada ou não tem quota suficiente para criar os tamanhos VM específicos na localização de região de destino.</br></br>-Um tamanho VM que corresponde à origem de contagem de NIC de VM (2) de destino adequado não for encontrado para o ID de subscrição na localização de região de destino.| Contacte [suporte de faturação do Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) para permitir a criação de VM para os tamanhos VM necessários na localização de destino para a sua subscrição. Depois de ser ativada, repita a operação falhada.

### <a name="fix-the-problem"></a>Corrigir o problema
Pode contactar [suporte de faturação do Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) para ativar a sua subscrição criar VMs de tamanhos necessários na localização de destino.

Se a localização de destino tem uma restrição de capacidade, desative a replicação e ative-o para uma localização diferente em que a sua subscrição tem quota suficiente para criar VMs dos tamanhos necessários.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificados raiz confiáveis (código de erro 151066)

Se todos os certificados de raiz fidedigna mais recentes não estão presentes na VM, poderá falhar a tarefa "ativar replicação". Sem os certificados, a autenticação e autorização de chamadas de serviço de recuperação de sites da VM falharem. É apresentada a mensagem de erro para a tarefa de recuperação de sites "ativar replicação" falhada:

**Código de erro** | **Causa possível** | **Recommendations (Recomendações)**
--- | --- | ---
151066<br></br>**Mensagem**: Falha na configuração do Site Recovery. | Necessários fidedignos utilizados certificados de raiz para autorização e autenticação não estão presentes na máquina. | -Para uma VM com o sistema operativo do Windows, certifique-se de que os certificados de raiz fidedigna estão presentes na máquina. Para obter informações, consulte [configurar raízes confiáveis e não são permitidas certificados](https://technet.microsoft.com/library/dn265983.aspx).<br></br>-Para uma VM a executar o sistema operativo Linux, siga as orientações para certificados de raiz fidedigna publicada pelo distribuidor de versão do sistema operativo Linux.

### <a name="fix-the-problem"></a>Corrigir o problema
**Windows**

Instale todas as atualizações mais recentes do Windows na VM para que todos os certificados de raiz fidedigna estão presentes na máquina. Se fizer parte de um ambiente desligado, siga o processo de atualização padrão do Windows na sua organização para obter os certificados. Se os certificados necessários não estão presentes na VM, as chamadas para o serviço de recuperação de Site falharem por motivos de segurança.

Siga o processo normal do Windows update gestão ou o certificado atualização management na sua organização para obter todos os certificados de raiz mais recentes e a lista de revogação de certificado atualizado nas VMs.

Para verificar se o problema está resolvido, vá para login.microsoftonline.com partir de um browser na VM.

**Linux**

Siga as orientações fornecidas pelo distribuidor do Linux para obter os certificados de raiz fidedigna mais recentes e a mais recente lista de revogação de certificado na VM.

Como o SuSE Linux usa links simbólicos, para manter uma lista de certificados, siga estes passos:

1.  Inicie sessão como um utilizador de raiz.

2.  Execute este comando para alterar o diretório.

      ``# cd /etc/ssl/certs``

1. Verifique se o certificado de AC de raiz da Symantec está presente.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. Se não for encontrado o certificado de AC de raiz da Symantec, execute o seguinte comando para transferir o ficheiro. Verifique se existem quaisquer erros e siga a ação recomendada para falhas de rede.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. Verifique se o certificado de AC de raiz Baltimore está presente.

      ``# ls Baltimore_CyberTrust_Root.pem``

4. Se não for encontrado o certificado de AC de raiz Baltimore, transfira o certificado.  

    ``# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. Verifique se o certificado de DigiCert_Global_Root_CA está presente.

    ``# ls DigiCert_Global_Root_CA.pem``

6. Se não for encontrado o DigiCert_Global_Root_CA, execute os seguintes comandos para transferir o certificado.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. Execute o script de rehash para atualizar o certificado de hashes de assunto para os certificados acabado de transferir.

    ``# c_rehash``

8.  Verifique se o assunto codifica como links simbólicos são criados para os certificados.

    - Comando

      ``# ls -l | grep Baltimore``

    - Saída

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Comando

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Saída

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Comando

      ``# ls -l | grep DigiCert_Global_Root``

    - Saída

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  Criar uma cópia do ficheiro VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem com b204d74a.0 de nome de ficheiro

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. Criar uma cópia do ficheiro Baltimore_CyberTrust_Root.pem com 653b494a.0 de nome de ficheiro

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Criar uma cópia do ficheiro DigiCert_Global_Root_CA.pem com 3513523f.0 de nome de ficheiro

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Verifique se os ficheiros estão presentes.  

    - Comando

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Saída

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividade de saída para intervalos de IP ou URLs do Site Recovery (código de erro 151037 ou 151072)

Para replicação do Site Recovery para o trabalho, a conectividade de saída para URLs ou IP específicos a intervalos é necessária da VM. Se a VM estiver protegido por uma firewall ou utiliza regras de grupo (NSG) de segurança de rede para controlar a conectividade de saída, poderá deparar-se com um desses problemas.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problema 1: Falha ao registrar a máquina virtual do Azure com o Site Recovery (151195) </br>
- **Causa possível** </br>
  - A conexão não pode ser estabelecida com Site Recovery pontos de extremidade devido à falha na resolução do DNS.
  - Isso é mais frequentemente visto durante a reavaliação de proteção, quando tem a ativação pós-falha da máquina virtual, mas o servidor DNS não está acessível a partir da região de DR.

- **Resolução**
   - Se estiver a utilizar o DNS personalizado, certifique-se de que o servidor DNS está acessível a partir da região de recuperação após desastre. Para verificar se tem um DNS personalizado, vá para a VM > rede de recuperação após desastre > servidores DNS. Tente acessar o servidor DNS da máquina virtual. Se não estiver acessível, em seguida, torná-lo acessível ao efetuar a ativação pós-falha do servidor DNS ou ao criar a linha de site entre a rede de DR e DNS.

    ![Erro de com](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: Falha na configuração do Site Recovery (151196)
- **Causa possível** </br>
  - Não é possível estabelecer a ligação para a autenticação do Office 365 e os pontos finais de IPv4 de identidade.

- **Resolução**
  - O Azure Site Recovery necessário acesso aos intervalos de IPs do Office 365 para a autenticação.
    Se estiver a utilizar o proxy de firewall/regras de grupo (NSG) de segurança de rede do Azure para controlar a conectividade de rede de saída na VM, certifique-se de que permite a comunicação com IPranges do Office 365. Criar uma [etiquetas de serviço do Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) com base em regras NSG para permitir o acesso a todos os endereços IP correspondente para o AAD
      - Se novos endereços são adicionados ao Azure Active Directory (AAD) no futuro, terá de criar novas regras NSG.

> [!NOTE]
> Se as máquinas virtuais estiverem atrás do Load balancer interno **padrão** , ele não teria acesso aos IPS do O365, ou seja, login.microsoftonline.com por padrão. Altere-o para o tipo **básico** de balanceador de carga interno ou crie acesso associado conforme mencionado no [artigo](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: Falha na configuração do Site Recovery (151197)
- **Causa possível** </br>
  - Não é possível estabelecer ligação a pontos finais de serviço do Azure Site Recovery.

- **Resolução**
  - O Azure Site Recovery precisavam acessá [intervalos de IP de recuperação de Site](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) consoante a região. Certifique-se de que necessária intervalos ip estão acessíveis a partir da máquina virtual.


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problema 4: Falha na replicação do A2A quando o tráfego de rede passa pelo servidor proxy local (151072)
- **Causa possível** </br>
  - As configurações de proxy personalizadas são inválidas e Azure Site Recovery agente do serviço de mobilidade não detectou automaticamente as configurações de proxy do IE


- **Resolução**
  1. Agente do serviço de mobilidade Deteta as definições de proxy do IE no Windows e /etc/environment no Linux.
  2. Se preferir definir proxy somente para Azure Site Recovery serviço de mobilidade, você poderá fornecer os detalhes do proxy em ProxyInfo. conf localizado em:</br>
     - ``/usr/local/InMage/config/`` no ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` no ***Windows***
  3. O ProxyInfo.conf deve ter as definições de proxy no seguinte formato INI.</br>
                *[proxy]*</br>
                *Endereço =http://1.2.3.4*</br>
                *Porta = 567*</br>
  4. Azure Site Recovery agente do serviço de mobilidade dá suporte apenas a ***proxies não autenticados***.


### <a name="fix-the-problem"></a>Corrigir o problema
Para permitir [as URLs necessárias](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou os [intervalos de IP necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), siga as etapas no [documento diretrizes de rede](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Não foi encontrado na máquina (código de erro 150039) de disco

Um disco novo anexado à VM tem de ser inicializado.

**Código de erro** | **Causas possíveis** | **Recommendations (Recomendações)**
--- | --- | ---
150039<br></br>**Mensagem**: O disco de dados do Azure (diskname) (DiskURI) com o número de unidade lógica (LUN) (LUNValue) não foi mapeado para um disco correspondente que está sendo relatado de dentro da VM que tem o mesmo valor de LUN. | -Um novo disco de dados foi anexado à VM, mas ele não foi inicializado.</br></br>-O disco de dados dentro da VM não está corretamente a comunicar o valor LUN em que o disco foi anexado à VM.| Certifique-se de que os discos de dados são inicializados e, em seguida, repita a operação.</br></br>Para Windows: [Anexe e inicialize um novo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Para Linux: [Inicialize um novo disco de dados no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Corrigir o problema
Certifique-se de que os discos de dados foram inicializados e, em seguida, repita a operação:

- Para Windows: [Anexe e inicialize um novo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Para Linux: [adicione um novo disco de dados no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Se o problema persistir, contacte o suporte.

## <a name="one-or-more-disks-are-available-for-protectionerror-code-153039"></a>Um ou mais discos estão disponíveis para proteção (código de erro 153039)
- **Causa possível** </br>
  - se um ou mais disco (s) foram adicionados recentemente à máquina virtual após a proteção. 
  - se um ou mais discos tiverem sido inicializados posteriormente após a proteção da máquina virtual.

### <a name="fix-the-problem"></a>Corrigir o problema
Você pode optar por proteger os discos ou ignorar o aviso para tornar o status de replicação da VM íntegro novamente.</br>
1. Para proteger os discos. Navegue até itens replicados > discos > VM > clique em disco desprotegido > habilitar a replicação.
 ![add_disks](./media/azure-to-azure-troubleshoot-errors/add-disk.png)
2. Para ignorar o aviso. Vá para itens replicados > VM > clique na seção ignorar alerta em visão geral.
![dismiss_warning](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)


## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information--error-code-150225"></a>Remover a máquina virtual do cofre concluída com informações (código de erro 150225)
No momento da proteção da máquina virtual, Azure Site Recovery cria alguns links na máquina virtual de origem. Ao remover a proteção ou desabilitar a replicação, Azure Site Recovery remover esses links como parte do trabalho de limpeza. Caso a máquina virtual tenha um bloqueio de recurso, o trabalho é concluído com as informações. Ele informa que a máquina virtual foi removida do cofre dos serviços de recuperação, mas alguns dos links obsoletos não puderam ser limpos do computador de origem.

Você poderá ignorar esse aviso se você nunca pretender proteger essa máquina virtual novamente no futuro. No entanto, se você precisar proteger essa máquina virtual posteriormente, deverá limpar os links conforme mencionado nas etapas abaixo. 

**Se você não fizer a limpeza, então:**

1.  Durante o tempo de habilitação da replicação por meio do cofre dos serviços de recuperação, a máquina virtual não será listada. 
2.  Se você tentar proteger a VM por meio de **configurações de > de máquina Virtual > a recuperação** de desastres falhará, com o erro "a*replicação não pode ser habilitada devido aos links de recursos obsoletos existentes na VM*".


### <a name="fix-the-problem"></a>Corrigir o problema

>[!NOTE]
>
>Azure Site Recovery não exclui a máquina virtual de origem nem o afeta de forma alguma enquanto executa as etapas abaixo.
>

1. Remova o bloqueio do grupo de recursos VM ou VM. Por exemplo: Abaixo do nome da VM "MoveDemo" tem o bloqueio de recurso que precisa ser excluído.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Baixar script [remover configuração de Azure site Recovery obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Execute o script *Cleanup-stale-ASR-config-Azure-VM. ps1*.
4. Forneça a ID da assinatura, o grupo de recursos da VM e o nome da VM como um parâmetro.
5. Se as credenciais do Azure forem solicitadas, forneça-as e verifique se o script é executado sem nenhuma falha. 


## <a name="replication-cannot-be-enabled-because-of-the-existing-stale-resource-links-on-the-vm-error-code-150226"></a>A replicação não pode ser habilitada devido aos links de recursos obsoletos existentes na VM (código de erro 150226)

**Causa: A máquina virtual tem uma configuração obsoleta deixada da proteção de Site Recovery anterior**

A configuração obsoleta poderá apresentar numa VM do Azure nos seguintes casos:

- Você habilitou a replicação para a VM do Azure usando Site Recovery e, em seguida, desabilita a replicação, mas a **VM de origem tinha um bloqueio de recurso**.
- Ativar a replicação para a VM do Azure utilizando a recuperação de Site e, em seguida, eliminar o Cofre de recuperação de sites sem explicitamente a desativar a replicação na VM.
- Ativar a replicação para a VM do Azure utilizando a recuperação de Site e, em seguida, eliminar o grupo de recursos que contém o Cofre de recuperação de sites sem explicitamente a desativar a replicação na VM.

### <a name="fix-the-problem"></a>Corrigir o problema

>[!NOTE]
>
>Azure Site Recovery não exclui a máquina virtual de origem nem o afeta de forma alguma enquanto executa as etapas abaixo.


1. Remova o bloqueio do grupo de recursos VM ou VM, se houver algum. *Por exemplo:* Abaixo do nome da VM "MoveDemo" tem o bloqueio de recurso que precisa ser excluído.
   
   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Baixar script [remover configuração de Azure site Recovery obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Execute o script *Cleanup-stale-ASR-config-Azure-VM. ps1*.
4. Forneça a ID da assinatura, o grupo de recursos da VM e o nome da VM como um parâmetro.
5. Se as credenciais do Azure forem solicitadas, forneça-as e verifique se o script é executado sem nenhuma falha.  

## <a name="unable-to-see-the-azure-vm-or-resource-group--for-selection-in-enable-replication"></a>Não é possível ver a VM ou o grupo de recursos do Azure para seleção em "Habilitar replicação"

 **Motivo 1:  O grupo de recursos e a máquina virtual de origem estão em local diferente**
 
O Azure Site Recovery atualmente exige que o grupo de recursos da região de origem e as máquinas virtuais estejam no mesmo local. Se esse não for o caso, você não poderá encontrar a máquina virtual ou o grupo de recursos durante o tempo de proteção. 

**Como alternativa**, você pode habilitar a replicação da VM em vez do cofre dos serviços de recuperação. Vá para origem VM > Propriedades > recuperação de desastre e habilite a replicação.

**Motivo 2: O grupo de recursos não faz parte da assinatura selecionada**

Poderá não conseguir encontrar o grupo de recursos no momento da proteção, se não fizer parte de uma determinada subscrição. Certifique-se de que o grupo de recursos pertence à subscrição que está a ser utilizada.

 **Causa 3: Configuração obsoleta**
 
Se não vir a VM que pretende ativar para a replicação, pode ser devido a uma configuração de recuperação de Site obsoleta deixado na VM do Azure. A configuração obsoleta poderá apresentar numa VM do Azure nos seguintes casos:

- Ativar a replicação para a VM do Azure utilizando a recuperação de Site e, em seguida, eliminar o Cofre de recuperação de sites sem explicitamente a desativar a replicação na VM.
- Ativar a replicação para a VM do Azure utilizando a recuperação de Site e, em seguida, eliminar o grupo de recursos que contém o Cofre de recuperação de sites sem explicitamente a desativar a replicação na VM.

- Você habilitou a replicação para a VM do Azure usando Site Recovery e, em seguida, desabilita a replicação, mas a VM de origem tinha um bloqueio de recurso.

### <a name="fix-the-problem"></a>Corrigir o problema

> [!NOTE]
>
> Certifique-se de atualizar o módulo "" AzureRM. resources "" antes de usar o script abaixo. Azure Site Recovery não exclui a máquina virtual de origem nem o afeta de forma alguma enquanto executa as etapas abaixo.
>

1. Remova o bloqueio do grupo de recursos VM ou VM, se houver algum. *Por exemplo:* Abaixo do nome da VM "MoveDemo" tem o bloqueio de recurso que precisa ser excluído.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Baixar script [remover configuração obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Execute o script *Cleanup-stale-ASR-config-Azure-VM. ps1*.
4. Forneça a ID da assinatura, o grupo de recursos da VM e o nome da VM como um parâmetro.
5. Se as credenciais do Azure forem solicitadas, forneça-as e verifique se o script é executado sem nenhuma falha.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Não é possível selecionar a Máquina Virtual para proteção
 **Motivo 1:  A máquina virtual tem alguma extensão instalada em um estado com falha ou sem resposta** <br>
 Vá para as máquinas virtuais > configuração > extensões e verifique se existem quaisquer extensões em estado de falha. Desinstale a extensão com falha e repita a proteger a máquina virtual.<br>
 **Motivo 2:  [O estado de provisionamento da VM não é válido](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>Estado de aprovisionamento da VM não é válido (código de erro 150019)

Para ativar a replicação da VM, deve ser o estado de aprovisionamento **bem-sucedido**. Pode verificar o estado VM ao seguir os passos abaixo.

1.  Selecione o **Explorador de recursos** partir **todos os serviços** no portal do Azure.
2.  Expanda a **subscrições** lista e selecione a sua subscrição.
3.  Expanda a **ResourceGroups** lista e selecione o grupo de recursos da VM.
4.  Expanda a **recursos** lista e selecione a sua máquina virtual
5.  Verifique os **provisioningState** campo na vista de instância no lado direito.

### <a name="fix-the-problem"></a>Corrigir o problema

- Se **provisioningState** é **falha**, contacte o suporte com detalhes para resolver problemas.
- Se **provisioningState** é **atualização**, outra extensão foi possível obter implementar. Verifique se existem quaisquer operações em curso na VM, aguarde que estas seja concluída e tente novamente a recuperação de Site com falha **ativar a replicação** tarefa.

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>Não é possível selecionar o destino de rede virtual - separador de seleção de rede está a cinzento.

**Motivo 1: Se sua VM estiver conectada a uma rede que já está mapeada para uma "rede de destino".**
- Se a VM de origem faz parte de uma rede virtual e a outra VM na mesma rede virtual já está mapeada com uma rede no grupo de recursos de destino, em seguida, pela seleção de rede predefinida pendente será desativada.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**Motivo 2: Se você protegeu anteriormente a VM usando Azure Site Recovery e desabilitou a replicação.**
 - A desativar a replicação de uma VM não elimina o mapeamento da rede. Tem de ser eliminadas do cofre dos serviços de recuperação onde a VM foi protegida. </br>
 Navegue para cofre dos serviços de recuperação > infraestrutura do Site Recovery > mapeamento de rede. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - Rede de destino configurado durante a configuração de recuperação após desastre pode ser alterado após a configuração, depois da VM está protegida inicial. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Tenha em atenção que a alteração de mapeamento de rede afeta todas protegidos VMs que utilizam esse mapeamento de rede específicas.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM c++ /CLI erro de serviço de cópia sombra de volumes (código de erro 151025)

**Código de erro** | **Causas possíveis** | **Recommendations (Recomendações)**
--- | --- | ---
151025<br></br>**Mensagem**: Falha na instalação da extensão de Site Recovery | -Serviço de "sistema aplicação COM+" desativado.</br></br>-O serviço de "Cópia de sombra de volume" está desativado.| Definir serviços "sistema aplicação COM+" e "Cópia de sombra de volumes" para o modo de arranque automático ou manual.

### <a name="fix-the-problem"></a>Corrigir o problema

Pode abrir a consola de "Serviços" e certifique-se o "sistema aplicação COM+" e "Cópia de sombra de Volume" não estão definida como "Disabled" de tipo de arranque.
  ![Erro de com](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Tamanho de disco gerenciado sem suporte (código de erro 150172)


**Código de erro** | **Causas possíveis** | **Recommendations (Recomendações)**
--- | --- | ---
150172<br></br>**Mensagem**: Não foi possível habilitar a proteção para a máquina virtual porque ela tem (diskname) com tamanho (Discosize) inferior ao tamanho mínimo com suporte de 1024 MB. | -O disco é menor do que o tamanho com suporte de 1024 MB| Verifique se os tamanhos de disco estão dentro do intervalo de tamanho com suporte e repita a operação.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Falha ao habilitar a proteção, pois o nome do dispositivo mencionado na configuração do GRUB em vez de UUID (código de erro 151126)

**Causa possível:** </br>
Os arquivos de configuração do GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/Grub2/grub.cfg" ou "/etc/default/grub") podem conter o valor para a **raiz** dos parâmetros e retomar como os nomes de dispositivo reais em vez de UUID. Site Recovery determina a abordagem do UUID, pois o nome dos dispositivos pode mudar na reinicialização da VM, pois a VM pode não ser exibida com o mesmo nome no failover, resultando em problemas. Por exemplo: </br>


- A linha a seguir é do arquivo GRUB **/boot/Grub2/grub.cfg**. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- A linha a seguir é do arquivo grub **/boot/grub/menu.lst**
  *kernel/boot/vmlinuz-3.0.101-63-Default **raiz =/dev/sda2** **retomar =/dev/sda1** Splash = Silent crashkernel = 256M-: 128M showopts VGA = 0x314*

Se você observar a cadeia de caracteres em negrito acima, GRUB terá nomes de dispositivo reais para os parâmetros "root" e "resume" em vez de UUID.

**Como corrigir:**<br>
Os nomes de dispositivo devem ser substituídos pelo UUID correspondente.<br>


1. Localize o UUID do dispositivo executando o comando "blkid \<nome do dispositivo >". Por exemplo:<br>
   ```
   blkid /dev/sda1
   ```<br>
   ```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
   ```blkid /dev/sda2```<br>
   ```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```<br>
   ```



1. Agora, substitua o nome do dispositivo pelo UUID no formato "root = UUID =\<UUID >". Por exemplo, se substituímos os nomes de dispositivo com UUID para o parâmetro root e resume mencionado acima nos arquivos "/boot/Grub2/grub.cfg", "/boot/Grub2/grub.cfg" ou "/etc/default/grub: as linhas nos arquivos são semelhantes. <br>
   *kernel/boot/vmlinuz-3.0.101-63-Default **raiz = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** Splash = Silent crashkernel = 256M-: 128M showopts VGA = 0x314*
1. Reiniciar a proteção novamente

## <a name="enable-protection-failed-as-device-mentioned-in-the-grub-configuration-doesnt-existerror-code-151124"></a>Falha ao habilitar a proteção, pois o dispositivo mencionado na configuração do GRUB não existe (código de erro 151124)
**Causa possível:** </br>
Os arquivos de configuração do GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/Grub2/grub.cfg" ou "/etc/default/grub") podem conter os parâmetros "rd.lvm.lv" ou "rd_LVM_LV" para indicar o dispositivo LVM que deve ser descoberto no momento da inicialização. Se esses dispositivos LVM não existirem, o próprio sistema protegido não será inicializado e paralisado no processo de inicialização. Mesmo o mesmo será observado com a VM de failover. Abaixo estão alguns exemplos:

Alguns exemplos: </br>

1. A linha a seguir é do arquivo GRUB **"/boot/Grub2/grub.cfg"** em RHEL7. </br>
   *linux16/vmlinuz-3.10.0-957.EL7.x86_64 root =/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel = 128M\@64M **Rd. LVM. lv = rootvg/raiz Rd. LVM. lv = rootvg/swap** rhgb Quiet Lang = en_US. UTF-8*</br>
   Aqui, a parte realçada mostra que o GRUB precisa detectar dois dispositivos LVM com nomes **"root"** e **"swap"** do grupo de volumes "rootvg".
1. A linha a seguir é do arquivo GRUB **"/etc/default/grub"** em RHEL7 </br>
   *GRUB_CMDLINE_LINUX = "crashkernel = auto **Rd. LVM. lv = rootvg/raiz Rd. LVM. lv = rootvg/swap** rhgb Quiet"*</br>
   Aqui, a parte realçada mostra que o GRUB precisa detectar dois dispositivos LVM com nomes **"root"** e **"swap"** do grupo de volumes "rootvg".
1. A linha a seguir é do arquivo GRUB **"/boot/grub/menu.lst"** em RHEL6 </br>
   *kernel/vmlinuz-2.6.32-754.el6.x86_64 ro raiz = UUID = 36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US. UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = auto rd_LVM_LV = rootvg/lv_root keyboardvalue = PC keyTable = US rd_LVM_LV = rootvg/lv_swap rd_NO_DM rhgb Quiet* </br>
   Aqui, a parte realçada mostra que o GRUB precisa detectar dois dispositivos LVM com nomes **"root"** e **"swap"** do grupo de volumes "rootvg".<br>

**Como corrigir:**<br>

Se o dispositivo LVM não existir, corrija-o criando-o ou remova o parâmetro do mesmo dos arquivos de configuração do GRUB e, em seguida, tente novamente habilitar a proteção. </br>

## <a name="site-recovery-mobility-service-update-completed-with-warnings--error-code-151083"></a>Atualização do serviço de mobilidade Site Recovery concluída com avisos (código de erro 151083)
Site Recovery serviço de mobilidade tem muitos componentes, um dos quais é chamado de driver de filtro. O driver de filtro é carregado na memória do sistema somente no momento da reinicialização do sistema. Sempre que houver Site Recovery atualizações do serviço de mobilidade com alterações de driver de filtro, nós atualizaremos o computador, mas ainda forneceremos um aviso de que algumas correções exigem uma reinicialização. Isso significa que as correções de driver de filtro só podem ser percebidas quando um novo driver de filtro é carregado, o que pode ocorrer apenas no momento da reinicialização do sistema.<br>
**Observe** que isso é apenas um aviso e a replicação existente continua funcionando mesmo após a nova atualização do agente. Você pode optar por reinicializar sempre que desejar obter os benefícios do novo driver de filtro, mas se você não reinicializar do que o driver de filtro antigo continua a funcionar. Além do driver de filtro, **os benefícios de quaisquer outros aprimoramentos e correções no serviço de mobilidade são realizados sem qualquer reinicialização quando o agente é atualizado.**  


## <a name="protection-couldnt-be-enabled-as-replica-managed-disk-diskname-replica-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Não foi possível habilitar a proteção porque o disco gerenciado de réplica ' diskname-Replica ' já existe sem as marcas esperadas no grupo de recursos de destino (código de erro 150161

**Motivo**: Isso pode ocorrer se a máquina virtual foi protegida anteriormente no passado e, durante a desabilitação da replicação, o disco de réplica não foi limpo devido a algum motivo.</br>
**Como corrigir:** Exclua o disco de réplica mencionado na mensagem de erro e reinicie o trabalho de proteção com falha.

## <a name="next-steps"></a>Passos Seguintes
[Replicar máquinas virtuais do Azure](site-recovery-replicate-azure-to-azure.md)
