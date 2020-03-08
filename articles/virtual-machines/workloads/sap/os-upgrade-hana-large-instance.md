---
title: Atualização do sistema operativo para o SAP HANA on Azure (Grandes Instâncias) Microsoft Docs
description: Realizar atualização do sistema operativo para SAP HANA em Azure (Grandes Instâncias)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a0a5d39a7cb2162186291ea534a623ef45c40d4
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675617"
---
# <a name="operating-system-upgrade"></a>Atualização do sistema operativo
Este documento descreve os detalhes sobre as atualizações do sistema operativo nas Grandes Instâncias HANA.

>[!NOTE]
>A atualização do OS é da responsabilidade do cliente, o suporte de operações da Microsoft pode guiá-lo para as áreas-chave para ter cuidado durante a atualização. Deve consultar o fornecedor do seu sistema operativo também antes de planear uma atualização.

Durante o fornecimento da unidade HLI, a equipa de operações da Microsoft instala o sistema operativo.
Ao longo do tempo, é-lhe exigido que mantenha o sistema operativo (Exemplo: Remendar, afinar, atualizar, etc.) na unidade HLI.

Antes de fazer grandes alterações no sistema operativo (por exemplo, Upgrade SP1 para SP2), precisa de contactar a equipa da Microsoft Operations abrindo um bilhete de suporte para consultar.

Inclua no seu bilhete:

* O seu ID de subscrição do HLI.
* O nome do seu servidor.
* O nível de remendo que está a planear aplicar.
* A data em que está saciando esta mudança. 

Recomendamos que abra este bilhete pelo menos uma semana antes da data de upgrade desejável devido à verificação da equipa de Operações se será necessária uma atualização de firmware na lâmina do seu servidor.


Para a matriz de suporte das diferentes versões SAP HANA com as diferentes versões Linux, consulte [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Problemas conhecidos

Seguem-se as poucas questões conhecidas comuns durante a atualização:
- Na classe SKU Type II SKU, o software de fundação de software (SFS) é removido após a atualização do OS. É necessário reinstalar o SFS compatível após a atualização do OS.
- Os condutores de cartões Ethernet (ENIC e FNIC) voltaram para a versão mais antiga. É necessário reinstalar a versão compatível dos controladores após a atualização.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>Configuração recomendada por SAP HANA (Tipo I)

A configuração do sistema operativo pode derivar das definições recomendadas ao longo do tempo devido a remendos, atualizações do sistema e alterações feitas pelos clientes. Além disso, a Microsoft identifica as atualizações necessárias para os sistemas existentes para garantir que estão idealmente configuradas para o melhor desempenho e resiliência. Seguindo instruções delineia recomendações que abordam o desempenho da rede, a estabilidade do sistema e o ótimo desempenho hana.

### <a name="compatible-enicfnic-driver-versions"></a>Versões compatíveis de controlador eNIC/fNIC
  Para ter um desempenho adequado da rede e estabilidade do sistema, é aconselhável garantir que a versão adequada dos controladores eNIC e fNIC específicos do OS são instaladas como descrito na tabela de compatibilidade sinuosa. Os servidores são entregues aos clientes com versões compatíveis. Note que, em alguns casos, durante a correção de OS/Kernel, os condutores podem ser reenrolados de volta para as versões padrão do controlador. Certifique-se de que a versão adequada do condutor está a executar operações de remendo pós OS/Kernel.
       
      
  |  Fornecedor DE OS    |  Versão do pacote osso     |  Versão do Firmware  |  ENIC Driver |  fNIC Driver | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Comandos para upgrade do condutor e para limpar pacotes antigos da RPM
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>Ordens para confirmar
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>Falha na atualização suSE HLIs GRUB
SAP em 14 grandes instâncias (Tipo I) pode estar em estado não-sabotável após a atualização. O procedimento abaixo corrige esta questão.
#### <a name="execution-steps"></a>Passos de Execução


*   Executar `multipath -ll` comando.
*   Obtenha o ID LUN cujo tamanho seja de aproximadamente 50G ou utilize o comando: `fdisk -l | grep mapper`
*   Atualizar `/etc/default/grub_installdevice` ficheiro com `/dev/mapper/<LUN ID>`de linha . Exemplo: /dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>O ID LUN varia de servidor para servidor.


### <a name="disable-edac"></a>EDAC DEsativado 
   O módulo de deteção e correção de erros (EDAC) ajuda a detetar e corrigir erros de memória. No entanto, o hardware subjacente ao SAP HANA em Grandes Instâncias Azure (Tipo I) já está a desempenhar a mesma função. Ter a mesma funcionalidade ativada nos níveis do hardware e do sistema operativo (OS) pode causar conflitos e pode levar a paralisações ocasionais e não planeadas do servidor. Por isso, recomenda-se desativar o módulo do Sistema Operativo.

#### <a name="execution-steps"></a>Passos de Execução

* Verifique se o módulo EDAC está ativado. Se uma saída for devolvida abaixo do comando, significa que o módulo está ativado. 
```
lsmod | grep -i edac 
```
* Desative os módulos, afunilando as seguintes linhas ao ficheiro `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
É necessário reiniciar para fazer alterações. Execute `lsmod` comando e verifique se o módulo não está presente na saída.


### <a name="kernel-parameters"></a>Parâmetros kernel
   Certifique-se de que são aplicadas as definições corretas para `transparent_hugepage`, `numa_balancing`, `processor.max_cstate`, `ignore_ce` e `intel_idle.max_cstate`.

* intel_idle.max_cstate=1
* processador.max_cstate=1
* transparent_hugepage=nunca
* numa_balancing=desativar
* mcE=ignore_ce


#### <a name="execution-steps"></a>Passos de Execução

* Adicione estes parâmetros à linha `GRB_CMDLINE_LINUX` no ficheiro `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Crie um novo arquivo de comida.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Sistema de reiniciar.


## <a name="next-steps"></a>Passos seguintes
- Consulte [a cópia de segurança e restaure](hana-overview-high-availability-disaster-recovery.md) para a classe SKU de backup os.
- Consulte a cópia de [segurança osso para os selos tipo II sKUs de carimbos de Revisão 3](os-backup-type-ii-skus.md) para classe SKU tipo II.
