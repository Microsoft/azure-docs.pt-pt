---
title: A Máquina Virtual não responde ao aplicar a política "Política de Grupo Utilizadores Locais & Grupos"
description: Este artigo fornece medidas para resolver problemas em que o ecrã de carga está preso aplicando uma política durante o arranque numa Máquina Virtual Azure (VM).
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80620860"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>A Máquina Virtual não responde ao aplicar a política "Política de Grupo Utilizadores Locais & Grupos"

Este artigo fornece medidas para resolver problemas em que o ecrã de carga está preso aplicando uma política, durante o arranque, numa Máquina Virtual Azure (VM).

## <a name="symptom"></a>Sintoma

Quando utilizar os [diagnósticos do Boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para visualizar a imagem do VM, verá que o ecrã está preso a carregar com a mensagem: Aplicação da política do grupo Utilizadores *locais e de grupos.*

![Texto alt: Ecrã mostrando aplicação da política do grupo Utilizadores locais e carregamento de políticas de grupos (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Texto alt: Ecrã mostrando aplicação da política do grupo Utilizadores locais e carregamento de políticas de grupos (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Causa

Os sintomas deste congelamento são causados por um defeito de código na Biblioteca de Ligações Dinâmicas do Serviço de Perfil do Windows *(profsvc.dll).*

> [!NOTE]
> Este defeito aplica-se apenas no Windows Server 2012 e no Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>A política em questão

A política que está a ser aplicada que não terminará os seus processos é:

* *Configuração do computador\Policies\Modelos Administrativos\Sistema/Perfis de utilizador\Eliminar perfis de utilizador mais antigos do que um número especificado de dias no reinício do sistema*

Esta política só será pendurada se as seis condições seguintes forem verdadeiras:

* Os perfis de *utilizador Eliminar perfis mais antigos do que um número específico de dias na* política de reinício do sistema estão ativados.
* Tem perfis que cumpriram os requisitos de idade para exigir limpeza.
* Tem componentes que se registaram para eliminar notificação de perfis.
* Os componentes fazem chamadas (diretas ou indiretas) que precisam de adquirir dados dos componentes do Gestor de Controlo de Serviços (SCM) do Windows, tais como Iniciar, Parar ou Consultar informações sobre um serviço.
* Tem um serviço configurado para começar como *automático*.
* Este serviço está definido para ser executado no contexto de uma conta de domínio (em oposição à utilização de uma conta incorporada, como um sistema local).

### <a name="the-code-defect"></a>O defeito de código

O defeito de código deve-se ao Gestor de Controlo de Serviços (SCM) e aos serviços de Perfil que tentam aplicar fechaduras entre si simultaneamente. Existem bloqueios para impedir que vários serviços eacrescentuem alterações nos mesmos dados ao mesmo tempo, o que provocaria corrupção. Normalmente, vários pedidos de fechadura não causariam problemas. No entanto, uma vez que isso acontece durante o arranque, nenhum dos serviços pode completar os seus processos, uma vez que estão presos à espera uns dos outros.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OS Bug 5880648 - Service Control Manager bloqueia com a política "Eliminar perfis de utilizador no reinício"

Há duas ações que se sobrepõem para que:

* A Ação 1 adquire o bloqueio de perfil, mas ainda não adquiriu o bloqueio SCM.

  **E E**

* A Ação 2 adquire o bloqueio SCM, mas ainda não adquiriu o bloqueio de perfil.

Uma vez que este impasse ocorre, a tentativa de adquirir o segundo bloqueio necessário pende a ação.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Ação 1 - Notificação de eliminação de perfil antigo (tem Bloqueio de **perfil,** precisa **de bloqueio SCM)**

1. Em primeiro lugar, a política definida para eliminar perfis antigos adquire um bloqueio de serviço de perfil interno.

   * Este bloqueio está lá para evitar que dois fios interajam com os perfis enquanto a operação de eliminação é *progredida.*

2. A política encontra perfis suficientemente velhos para serem eliminados.
3. Como parte da eliminação do perfil, um componente que se registou para notificações das supressões de um perfil tenta **iniciar um serviço**.
4. Antes de iniciar o serviço, o Gestor de Controlo de Serviços (SCM) precisa de adquirir um **bloqueio SCM interno** mantido por fios na **Ação 2**.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Ação 2 - Carga de perfil/criação para dados específicos do utilizador (tem **Bloqueio SCM,** precisa de bloqueio de **perfil)**

1. No arranque, a SCM precisa de encomendar todos os serviços *de arranque automático* pelo seu grupo, bem como quaisquer serviços de que esses serviços estejam dependentes.

2. **A SCM adquire um bloqueio SCM interno** utilizado para controlar o acesso a serviços de arranque, paragem ou configuração, à medida que encomenda os serviços.

3. Uma vez que os serviços estão em ordem, o SCM passa por cada serviço e inicia-o.

4. Se o serviço estiver em execução no contexto de uma conta de domínio, um perfil precisa de ser carregado ou criado para a conta de domínio, para que possa armazenar dados específicos do utilizador.

5. Este pedido é enviado para o Serviço de **Perfis.**

6. O serviço de perfis necessita de acesso ao **bloqueio interno** adquirido na **Ação 1**.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

1. Criar e aceder a um VM de reparação
2. Ativar a coleção de despejo de consola sonorar e memória
3. Reconstruir o VM
4. Recolher o ficheiro de despejo de memória

   > [!NOTE]
   > Ao encontrar este erro de arranque, o Os convidado não está operacional. Vais estar a resolver problemas no modo Offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Utilize [os passos 1-3 dos Comandos de Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para preparar um VM de reparação.
2. Utilizando a ligação remota de ambiente de trabalho ao VM de reparação.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Ativar a coleção de despejo de consola sonorar e memória

Para permitir a recolha de despejo de memória e consola em série, execute o script abaixo:

1. Abra uma sessão de solicitação de comando elevada (Corra como administrador).
2. Execute os seguintes comandos:

   * Ativar a consola em série:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Verifique se o espaço livre no disco OS é tanto quanto o tamanho da memória (RAM) no VM.

   * Se não houver espaço suficiente no disco OS, deverá alterar o local onde será criado o ficheiro de despejo de memória e encaminhá-lo para qualquer disco de dados ligado ao VM que tenha espaço livre suficiente. Para alterar a `%SystemRoot%` localização, substitua-a pela letra de unidade (como "F:") do disco de dados nos comandos abaixo.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Configuração sugerida para ativar o despejo de OS

**Disco operativo osso avariado de carga:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Ativar no ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Ativar no ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>Reconstruir o VM

Utilize [o passo 5 dos Comandos de Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para remontar o VM.

### <a name="collect-the-memory-dump-file"></a>Recolher o ficheiro de despejo de memória

Para resolver este problema, você precisaria primeiro de recolher o ficheiro de despejo de memória para o acidente e suporte de contato com o ficheiro de despejo de memória. Para recolher o ficheiro de despejo, siga estes passos:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Fixe o disco OS a um novo VM de reparação

1. Utilize os passos [1-3 dos Comandos de Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para preparar um novo VM de reparação.

2. Utilizando a ligação remota de ambiente de trabalho ao VM de reparação.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localize o ficheiro de despejo e envie um bilhete de apoio

1. No VM de reparação, vá para a pasta das janelas no disco OS anexado. Se a carta de condutor que é atribuída ao disco operativo em anexo for F, tem de ir para F:\Windows.

2. Localize o ficheiro memory.dmp [e,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) em seguida, envie um bilhete de suporte com o ficheiro de despejo de memória.

3. Se estiver com problemas em localizar o ficheiro memory.dmp, poderá desejar utilizar chamadas de [interrupção não-máscara (NMI) em consola](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) em série. Pode seguir o guia para gerar um ficheiro de despejo de [miolo ou completo](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) utilizando chamadas NMI.
