---
title: Atualização do sistema operativo para o SAP HANA em Azure (Grandes Instâncias)| Microsoft Docs
description: Executar Atualização do sistema operativo para SAP HANA em Azure (Grandes Instâncias)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 97c07f010fad6c12424b1684d4ff5e12c7cac3ce
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553248"
---
# <a name="operating-system-upgrade"></a>Atualização do sistema operativo
Este documento descreve os detalhes sobre as atualizações do sistema operativo nas Grandes Instâncias HANA.

>[!NOTE]
>A atualização do SO é da responsabilidade do cliente, o suporte de operações da Microsoft pode guiá-lo para as áreas-chave para ter cuidado durante a atualização. Deve consultar também o seu fornecedor do sistema operativo antes de planear uma atualização.

Durante o fornecimento da unidade HLI, a equipa de operações da Microsoft instala o sistema operativo.
Ao longo do tempo, é necessário manter o sistema operativo (Exemplo: Remendos, afinação, atualização, etc.) na unidade HLI.

Antes de fazer grandes alterações no sistema operativo (por exemplo, Atualizar SP1 para SP2), deverá contactar a equipa da Microsoft Operations abrindo um bilhete de apoio para consultar.

Inclua no seu bilhete:

* O seu ID de assinatura HLI.
* O nome do seu servidor.
* O nível de remendo que planeia aplicar.
* A data em que estás a planear esta mudança. 

Recomendamos que abra este bilhete pelo menos uma semana antes da atualização desejável, que irá informar a equipa de opration sobre a versão de firmware desejada.

Para obter a matriz de suporte das diferentes versões SAP HANA com as diferentes versões Linux, consulte [a SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).

## <a name="known-issues"></a>Problemas conhecidos

Seguem-se as poucas questões conhecidas durante a atualização:
- Na classe SKU Type II SKU, o software de fundação de software (SFS) é removido após a atualização do SISTEMA. É necessário reinstalar o SFS compatível após a atualização do SISTEMA.
- Os controladores de cartões Ethernet (ENIC e FNIC) voltaram para a versão mais antiga. É necessário reinstalar a versão compatível dos controladores após a atualização.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>Configuração recomendada SAP HANA Large Instance (Tipo I)

A configuração do sistema operativo pode derivar das definições recomendadas ao longo do tempo devido a remendos, atualizações do sistema e alterações feitas pelos clientes. Além disso, a Microsoft identifica as atualizações necessárias para os sistemas existentes para garantir que estão configuradas de forma ótima para o melhor desempenho e resiliência. As instruções descrevem recomendações que abordam o desempenho da rede, a estabilidade do sistema e o desempenho ideal do HANA.

### <a name="compatible-enicfnic-driver-versions"></a>Versões compatíveis do controlador eNIC/fNIC
  Para ter um desempenho adequado da rede e estabilidade do sistema, é aconselhável garantir que a versão adequada específica do SISTEMA e dos controladores fNIC e FNIC seja instalada como indicado na tabela de compatibilidade. Os servidores são entregues a clientes com versões compatíveis. Em alguns casos, durante o patching OS/Kernel, os condutores podem ser revirados para as versões do controlador predefinido. Certifique-se de que a versão adequada do controlador está a executar operações de remendação pós-OS/Kernel.
       
      
  |  Fornecedor de OS    |  Versão do pacote de SO     |  Versão do Firmware  |  Condutor eNIC |  condutor fNIC | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3i           |  4.0.0.6     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP5            |   4.1.1b           |  4.0.0.6     |   2.0.0.59   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
  |   Red Hat     |  RHEL 7.6               |   3.2.3i           |  3.1.137.5   |   2.0.0.50   |
  |   Red Hat     |  RHEL 7.6               |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Comandos para upgrade de motorista e para limpar pacotes antigos rpm

#### <a name="command-to-check-existing-installed-drivers"></a>Comando para verificar os controladores instalados existentes
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>Eliminar as rpm eNIC/fNIC existentes
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>Instale as embalagens recomendadas do controlador eNIC/fNIC
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>Ordens para confirmar a instalação
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>Etapas para instalação de condutores eNIC/fNIC durante a atualização do OS

* Atualizar versão OS
* Remover pacotes antigos rpm
* Instale controladores compatíveis eNIC/fNIC de acordo com a versão OS instalada
* Sistema de reinicialização
* Após o reboot, verifique a versão eNIC/fNIC


### <a name="suse-hlis-grub-update-failure"></a>Falha na atualização do SuSE HLIs GRUB
SAP on Azure HANA Large Instances (Tipo I) pode estar em estado não inicial após a atualização. O procedimento abaixo corrige esta questão.
#### <a name="execution-steps"></a>Etapas de Execução


*   Executar `multipath -ll` o comando.
*   Obtenha o ID LUN cujo tamanho seja aproximadamente 50G ou use o comando: `fdisk -l | grep mapper`
*   Atualizar `/etc/default/grub_installdevice` o ficheiro com linha `/dev/mapper/<LUN ID>` . Exemplo: /dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>O ID LUN varia de servidor para servidor.


### <a name="disable-edac"></a>Desativar o EDAC 
   O módulo de deteção e correção de erros (EDAC) ajuda na deteção e correção de erros de memória. No entanto, o hardware subjacente para SAP HANA em Azure Large Instances (Tipo I) já está a desempenhar a mesma função. Ter a mesma funcionalidade ativada nos níveis de hardware e sistema operativo (OS) pode causar conflitos e pode levar a encerramentos ocasionais e não planeados do servidor. Por isso, recomenda-se desativar o módulo do SISTEMA.

#### <a name="execution-steps"></a>Etapas de Execução

* Verifique se o módulo EDAC está ativado. Se uma saída for devolvida abaixo do comando, significa que o módulo está ativado. 
```
lsmod | grep -i edac 
```
* Desative os módulos, anexando as seguintes linhas ao ficheiro `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
É necessário reiniciar para que se realizem alterações. Execute `lsmod` o comando e verifique se o módulo não está presente na saída.

### <a name="kernel-parameters"></a>Parâmetros kernel
   Certifique-se de que a regulação correta para `transparent_hugepage` , , e são `numa_balancing` `processor.max_cstate` `ignore_ce` `intel_idle.max_cstate` aplicadas.

* intel_idle.max_cstate=1
* processador.max_cstate=1
* transparent_hugepage=nunca
* numa_balancing=desativar
* mce=ignore_ce

#### <a name="execution-steps"></a>Etapas de Execução

* Adicione estes parâmetros à `GRB_CMDLINE_LINUX` linha no ficheiro `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Crie um novo arquivo de comida.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Sistema de reinicialização.


## <a name="next-steps"></a>Passos seguintes
- Consulte [a Cópia de Segurança e restaure](hana-overview-high-availability-disaster-recovery.md) para a classe SKU de backup oss.
- Consulte [o OS Backup para SKUs tipo II de revisão 3 selos](os-backup-type-ii-skus.md) para a classe SKU tipo II.
