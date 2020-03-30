---
title: Fluxo de trabalho da Arquitetura VM do Windows Azure [ Microsoft Docs
description: Este artigo fornece uma visão geral dos processos de fluxo de trabalho quando implementa um serviço.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 5dd57a87658554bf59acf5cee1b6daf67b8692b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71162153"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Fluxo de trabalho da Arquitetura VM clássica do Windows Azure 
Este artigo fornece uma visão geral dos processos de fluxo de trabalho que ocorrem quando implementa ou atualiza um recurso Azure, como uma máquina virtual. 

> [!NOTE]
>O Azure tem dois modelos de implementação para criar e trabalhar com recursos: Resource Manager e Clássico. Este artigo cobre a utilização do modelo de implementação clássica.

O diagrama seguinte apresenta a arquitetura dos recursos azure.

![Fluxo de trabalho azul](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Básicos do fluxo de trabalho
   
**A.** RDFE/FFE é o caminho de comunicação do utilizador para o tecido. RDFE (RedDog Front End) é a API publicamente exposta que é a extremidade frontal do Portal de Gestão e da API de Gestão de Serviços, como Visual Studio, Azure MMC, e assim por diante.  Todos os pedidos do utilizador passam pelo RDFE. FFE (Fabric Front End) é a camada que traduz pedidos de RDFE em comandos de tecido. Todos os pedidos da RDFE passam pelo FFE para chegar aos controladores de tecido.

**B**. O controlador de tecido é responsável pela manutenção e monitorização de todos os recursos do centro de dados. Comunica com agentes anfitriões de tecido sondar informações como a versão Do guest, pacote de serviço, configuração de serviço e estado de serviço.

**C**. O Agente Anfitrião vive no Os anfitrião e é responsável pela configuração do Os convidado e comunicação com o Agente Convidado (WindowsAzureGuestAgent) de forma a atualizar o papel para um estado-objetivo pretendido e fazer verificações de batimentos cardíacos com o agente Convidado. Se o Agente Anfitrião não receber uma resposta de batimento cardíaco durante 10 minutos, o Agente anfitrião reinicia o Os convidado.

**C2**. O WaAppAgent é responsável pela instalação, configuração e atualização do WindowsAzureGuestAgent.exe.

**D**.  O WindowsAzureGuestAgent é responsável pelo seguinte:

1. Configurar o Os convidado incluindo firewall, ACLs, recursos de Armazenamento Local, pacote de serviço e configuração, e certificados.
2. Configurar o SID para a conta de utilizador que a função será executada.
3. Comunicando o estado do papel com o tecido.
4. Iniciar o WaHostBootstrapper e monitorizá-lo para garantir que o papel está no estado objetivo.

**E.** WaHostBootstrapper é responsável por:

1. Ler a configuração do papel e iniciar todas as tarefas e processos apropriados para configurar e executar o papel.
2. Monitorizando todos os seus processos infantis.
3. Elevar o evento StatusCheck sobre o processo de anfitrião de funções.

**F.** O IISConfigurator funciona se a função for configurada como uma função web Full IIS (não funcionará para funções SDK 1.2 HWC). É responsável por:

1. Iniciar os serviços IIS padrão
2. Configurar o módulo de reescrita na configuração web
3. Configuração do AppPool para o papel configurado no modelo de serviço
4. Configuração do registo do IIS para apontar para a pasta Dearmazenamento Local da Loja de Diagnóstico
5. Configuração de permissões e ACLs
6. O site reside em %roleroot%:\sitesroot\0, e o AppPool aponta para este local para executar o IIS. 

**G.** As tarefas de arranque são definidas pelo modelo e iniciadas pelo WaHostBootstrapper. As tarefas de arranque podem ser configuradas para serem executadas em segundo plano, e o bootstrapper anfitrião iniciará a tarefa de arranque e continuará depois a outras tarefas de arranque. As tarefas de arranque também podem ser configuradas para serem executadas no modo Simple (padrão) em que o bootstrapper anfitrião aguardará que a tarefa de arranque termine de funcionar e devolva um código de saída de sucesso (0) antes de continuar para a próxima tarefa de arranque.

**H.** Estas tarefas fazem parte do SDK e são definidas como plugins na definição de serviço do papel (.csdef). Quando expandidos para tarefas de arranque, o **DiagnosticsAgent** e **o RemoteAccessAgent** são únicos na medida em que definem duas tarefas de arranque, uma regular e outra que tem um parâmetro **/blockStartup.** A tarefa normal de arranque é definida como uma tarefa de arranque de Fundo para que possa funcionar em segundo plano enquanto o papel em si está em execução. A tarefa de arranque **/blockStartup** é definida como uma tarefa de arranque simples para que o WaHostBootstrapper aguarde a sua saída antes de continuar. A tarefa **/blockStartup** aguarda que a tarefa regular termine a inicialização e, em seguida, sai e permite que o bootstrapper anfitrião continue. Isto é feito para que os diagnósticos e o acesso rdp possam ser configurados antes do início dos processos de função (isto é feito através da tarefa /blockStartup). Isto também permite que os diagnósticos e o acesso rdp continuem a funcionar depois de o bootstrapper anfitrião ter terminado as tarefas de arranque (isto é feito através da tarefa Normal).

**Não, não, não, não.** WaWorkerHost é o processo padrão de anfitrião para funções normais do trabalhador. Este processo de anfitrião acolhe todos os DLLs do papel e código de ponto de entrada, tais como OnStart e Run.

**J.** O WaWebHost é o processo padrão de anfitrião para funções web se estiverem configurados para utilizar o Web Core Hostable compatível com SDK 1.2 (HWC). As funções podem ativar o modo HWC removendo o elemento da definição de serviço (.csdef). Neste modo, todo o código do serviço e DLLs são executados a partir do processo WaWebHost. O IIS (w3wp) não é utilizado e não existem AppPools configurados no IIS Manager porque o IIS está hospedado dentro do WaWebHost.exe.

**K.** WaIISHost é o processo de anfitrião para o código de ponto de entrada de papéis para funções web que usam O IIS Completo. Este processo carrega o primeiro DLL que é encontrado que utiliza a classe **RoleEntryPoint** e executa o código desta classe (OnStart, Run, OnStop). Quaisquer eventos **RoleEnvironment** (como StatusCheck e Changed) que são criados na classe RoleEntryPoint são aumentados neste processo.

**L.** W3WP é o processo padrão de trabalhador IIS que é usado se a função estiver configurada para usar O IIS Completo. Isto executa o AppPool que está configurado a partir do IISConfigurator. Quaisquer eventos RoleEnvironment (como StatusCheck e Changed) que são criados aqui são levantados neste processo. Note que os eventos RoleEnvironment dispararão em ambos os locais (WaIISHost e w3wp.exe) se subscrever eventos em ambos os processos.

## <a name="workflow-processes"></a>Processos de fluxo de trabalho

1. Um utilizador faz um pedido, como o upload de ficheiros ".cspkg" e ".cscfg", dizendo a um recurso para parar ou fazer uma alteração de configuração, e assim por diante. Isto pode ser feito através do portal Azure ou de uma ferramenta que utiliza a API de Gestão de Serviços, como a funcionalidade Visual Studio Publish. Este pedido vai para a RDFE para fazer todo o trabalho relacionado com subscrição e, em seguida, comunicar o pedido à FFE. O resto destes passos de fluxo de trabalho são para implementar um novo pacote e iniciá-lo.
2. FfE encontra o pool de máquinas correto (com base na entrada do cliente, como grupo de afinidade ou localização geográfica mais entrada do tecido, como disponibilidade de máquinas) e comunica com o controlador de tecido principal naquela piscina de máquinas.
3. O controlador de tecido encontra um hospedeiro que tem núcleos CPU disponíveis (ou gira um novo hospedeiro). O pacote de serviço e configuração é copiado para o anfitrião, e o controlador de tecido comunica com o agente anfitrião no sistema operativo anfitrião para implementar o pacote (configure DIPs, portas, os sopor de hóspedes, e assim por diante).
4. O agente anfitrião inicia o OS convidado e comunica com o agente convidado (WindowsAzureGuestAgent). O anfitrião envia batimentos cardíacos ao hóspede para se certificar de que o papel está a trabalhar para o seu estado objetivo.
5. O WindowsAzureGuestAgent configura o OS do hóspede (firewall, ACLs, LocalStorage, e assim por diante), copia um novo ficheiro de configuração XML para c:\Config, e inicia o processo waHostBootstrapper.
6. Para funções web Completas IIS, O WaHostBootstrapper inicia o IISConfigurator e diz-lhe para eliminar quaisquer AppPools existentes para o papel web do IIS.
7. WaHostBootstrapper lê as tarefas do **Startup** a partir de E:\RoleModel.xml e começa a executar tarefas de arranque. WaHostBootstrapper espera até que todas as tarefas de arranque simples tenham terminado e devolva uma mensagem de "sucesso".
8. Para as funções web Completas iIS, o WaHostBootstrapper diz ao IISConfigurator para configurar o IIS AppPool e aponta o site para, `E:\Sitesroot\<index>`onde `<index>` está um índice baseado em 0 no número de `<Sites>` elementos definidos para o serviço.
9. WaHostBootstrapper iniciará o processo de anfitrião dependendo do tipo de função:
    1. **Função de Trabalhador**: WaWorkerHost.exe é iniciado. WaHostBootstrapper executa o método OnStart() () Após a sua devolução, o WaHostBootstrapper começa a executar o método Run() e, em seguida, simultaneamente marca o papel como Ready e coloca-o na rotação do equilíbrio de carga (se os Pontos de Entrada estiverem definidos). WaHostBootsrapper entra então num ciclo de verificação do estado do papel.
    1. **SDK 1.2 HWC Função Web**: WaWebHost está iniciado. WaHostBootstrapper executa o método OnStart() () Após o seu regresso, o WaHostBootstrapper começa a executar o método Run() e, em seguida, simultaneamente marca o papel como Ready e coloca-o na rotação do equilíbrio de carga. WaWebHost emite um pedido de aquecimento (GET /do.rd_runtime_init). Todos os pedidos da web são enviados para WaWebHost.exe. WaHostBootsrapper entra então num ciclo de verificação do estado do papel.
    1. **Full IIS Web Role**: aIISHost é iniciado. WaHostBootstrapper executa o método OnStart() () Depois de regressar, começa a executar o método Run() e, em seguida, simultaneamente marca o papel como Ready e coloca-o na rotação do equilíbrio de carga. WaHostBootsrapper entra então num ciclo de verificação do estado do papel.
10. Os pedidos web de entrada para uma função web Full IIS desencadeiam o IIS para iniciar o processo W3WP e servir o pedido, o mesmo que num ambiente IIS no local.

## <a name="log-file-locations"></a>Localizações de ficheiros de registo

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Este registo contém alterações no serviço, incluindo arranques, paragens e novas configurações. Se o serviço não mudar, pode esperar ver grandes lacunas de tempo neste ficheiro de registo.
- C:\Logs\WaAppAgent.Log.  
Este registo contém atualizações de estado e notificações de batimentos cardíacos e é atualizado a cada 2-3 segundos.  Este registo contém uma visão histórica do estado da instância e dir-lhe-á quando a instância não estava no estado ready.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**WaiISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**Registos do IIS**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Registos do Evento Windows**

`D:\Windows\System32\Winevt\Logs`
 



