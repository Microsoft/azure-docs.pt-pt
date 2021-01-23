---
title: Fluxo de trabalho do Windows Azure VM Architecture | Microsoft Docs
description: Este artigo fornece uma visão geral dos processos de fluxo de trabalho quando implementa um serviço.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: bda066dd50d2f95776981eafc01e3ddd04d33e54
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741065"
---
# <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Fluxo de trabalho do Windows Azure clássico VM Architecture 

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Este artigo fornece uma visão geral dos processos de fluxo de trabalho que ocorrem quando implementa ou atualiza um recurso Azure, como uma máquina virtual. 

> [!NOTE]
>O Azure tem dois modelos de implementação para criar e trabalhar com recursos: Resource Manager e Clássico. Este artigo cobre a utilização do modelo de implementação clássica.

O diagrama seguinte apresenta a arquitetura dos recursos Azure.

:::image type="content" source="./media/cloud-services-workflow-process/workflow.jpg" alt-text="<alt A imagem sobre o fluxo de trabalho Azure>":::

## <a name="workflow-basics"></a>Básicos do fluxo de trabalho
   
**A**. RDFE / FFE é o caminho de comunicação do utilizador para o tecido. RDFE (RedDog Front End) é a API publicamente exposta que é a extremidade frontal do Portal de Gestão e da API de Gestão de Serviços, como o Visual Studio, Azure MMC, e assim por diante.  Todos os pedidos do utilizador passam pelo RDFE. FFE (Parte frontal do tecido) é a camada que traduz pedidos de RDFE em comandos de tecido. Todos os pedidos da RDFE passam pelo FFE para chegar aos controladores de tecido.

**B** B . O controlador de tecidos é responsável pela manutenção e monitorização de todos os recursos no centro de dados. Comunica com agentes hospedeiros de tecido no sistema operativo de tecido, enviando informações como a versão Guest OS, pacote de serviço, configuração de serviço e estado de serviço.

**C**. O Agente Anfitrião vive no Host OS e é responsável pela criação do Guest OS e pela comunicação com o Agente Convidado (WindowsAzureGuestAgent) a fim de atualizar o papel para um estado de meta pretendido e fazer verificações de batimentos cardíacos com o agente Convidado. Se o Agente Anfitrião não receber a resposta do batimento cardíaco durante 10 minutos, o Agente Anfitrião reinicia o SO convidado.

**C2**. O WaAppAgent é responsável pela instalação, configuração e atualização WindowsAzureGuestAgent.exe.

**D**.  O WindowsAzureGuestAgent é responsável pelo seguinte:

1. Configurar o SISTEMA convidado incluindo firewall, ACLs, recursos locais de armazenamento, pacote de serviço e configuração, e certificados.
2. Configuração do SID para a conta de utilizador que a função será executada.
3. Comunicar o estado do papel ao tecido.
4. Iniciar o WaHostBootstrapper e monitorizá-lo para garantir que o papel está no estado de objetivo.

**E**. WaHostBootstrapper é responsável por:

1. Ler a configuração de funções e iniciar todas as tarefas e processos apropriados para configurar e executar o papel.
2. Monitorização de todos os seus processos infantis.
3. Elevar o evento StatusCheck sobre o processo de anfitrião de funções.

**F**. O IISConfigurator funciona se o papel for configurado como um papel web Full IIS. É responsável por:

1. Iniciar os serviços IIS padrão
2. Configurar o módulo de reescrita na configuração web
3. Configuração do AppPool para o papel configurado no modelo de serviço
4. Configuração do registo do IIS para apontar para a pasta Detoragem Local da Loja de Diagnóstico
5. Configuração de permissões e ACLs
6. O website reside em %roleroot%:\sitesroot\0, e o AppPool aponta para este local para executar IIS. 

**G.** As tarefas de arranque são definidas pelo modelo e iniciadas por WaHostBootstrapper. As tarefas de arranque podem ser configuradas para serem executadas em segundo plano assíncronamente, e o bootstrapper anfitrião iniciará a tarefa de arranque e, em seguida, continuará para outras tarefas de arranque. As tarefas de arranque também podem ser configuradas para serem executadas no modo Simple (predefinido) em que o bootstrapper anfitrião irá aguardar que a tarefa de arranque termine de executar e devolva um código de saída de sucesso (0) antes de continuar para a próxima tarefa de arranque.

**H**. Estas tarefas fazem parte do SDK e são definidas como plugins na definição de serviço da função (.csdef). Quando expandidos para tarefas de arranque, o **DiagnosticsAgent** e **o RemoteAccessAgent** são únicos na medida em que cada um define duas tarefas de arranque, uma regular e outra que tem um parâmetro **/blockStartup.** A tarefa normal de arranque é definida como uma tarefa de startup de fundo para que possa funcionar em segundo plano enquanto o papel em si está em execução. A tarefa de arranque **/blockStartup** é definida como uma tarefa de arranque simples para que o WaHostBootstrapper aguarde que saia antes de continuar. A tarefa **/blockStartup** aguarda que a tarefa regular termine a inicialização e, em seguida, sai e permite que o arranque de botas do anfitrião continue. Isto é feito para que os diagnósticos e o acesso RDP possam ser configurados antes do início dos processos de função (isto é feito através da tarefa /blockStartup). Isto também permite que os diagnósticos e o acesso RDP continuem a funcionar depois de o bootstrapper anfitrião ter terminado as tarefas de arranque (isto é feito através da tarefa Normal).

**I.** WaWorkerHost é o processo de anfitrião padrão para funções normais de trabalhador. Este processo de anfitrião acolhe todos os DLLs e código de ponto de entrada da função, tais como OnStart e Run.

**J.** WaIISHost é o processo de anfitrião para código de ponto de entrada de funções para funções web que usam Full IIS. Este processo carrega o primeiro DLL que se encontra que utiliza a classe **RoleEntryPoint** e executa o código a partir desta classe (OnStart, Run, OnStop). Quaisquer eventos **RoleEnvironment** (como StatusCheck e Changed) que sejam criados na classe RoleEntryPoint são levantados neste processo.

**K.** W3WP é o processo padrão de trabalhador IIS que é usado se o papel estiver configurado para usar O IIS Completo. Isto executa o AppPool que está configurado a partir do IISConfigurator. Quaisquer eventos RoleEnvironment (como StatusCheck e Changed) que sejam criados aqui são levantados neste processo. Note que os eventos roleEnvironment dispararão em ambos os locais (WaIISHost e w3wp.exe) se subscrever eventos em ambos os processos.

## <a name="workflow-processes"></a>Processos de fluxo de trabalho

1. Um utilizador faz um pedido, como carregar ficheiros ".cspkg" e ".cscfg", dizer a um recurso para parar ou fazer uma alteração de configuração, e assim por diante. Isto pode ser feito através do portal Azure ou de uma ferramenta que utiliza a API de Gestão de Serviços, como a funcionalidade Visual Studio Publish. Este pedido vai para a RDFE para fazer todo o trabalho relacionado com a subscrição e, em seguida, comunicar o pedido à FFE. O resto destes passos de fluxo de trabalho é para implementar um novo pacote e iniciá-lo.
2. A FFE encontra o pool de máquinas correto (com base na entrada do cliente, como o grupo de afinidade ou a localização geográfica mais a entrada do tecido, como a disponibilidade da máquina) e comunica com o controlador de tecido principal naquela piscina de máquinas.
3. O controlador de tecido encontra um hospedeiro que tem núcleos CPU disponíveis (ou gira um novo hospedeiro). O pacote de serviço e configuração é copiado para o anfitrião, e o controlador de tecido comunica com o agente anfitrião no so anfitrião para implementar o pacote (configurar DIPs, portas, sos convidados, e assim por diante).
4. O agente anfitrião inicia o Guest OS e comunica com o agente convidado (WindowsAzureGuestAgent). O anfitrião envia batimentos cardíacos ao hóspede para se certificar de que o papel está a trabalhar para o seu estado de meta.
5. O WindowsAzureGuestAgent configura o SISTEMA convidado (firewall, ACLs, LocalStorage, etc.), copia um novo ficheiro de configuração XML para c:\Config e, em seguida, inicia o processo WaHostBootstrapper.
6. Para as funções web Full IIS, o WaHostBootstrapper inicia o IISConfigurator e diz-lhe para eliminar quaisquer AppPools existentes para o papel web do IIS.
7. WaHostBootstrapper lê as tarefas de **Startup** a partir de E:\RoleModel.xml e começa a executar tarefas de arranque. O WaHostBootstrapper aguarda até que todas as tarefas de arranque simples tenham terminado e devolva uma mensagem de "sucesso".
8. Para as funções web Full IIS, o WaHostBootstrapper diz ao IISConfigurator para configurar o IIS AppPool e aponta o site para `E:\Sitesroot\<index>` , onde está um índice baseado em `<index>` 0 no número de `<Sites>` elementos definidos para o serviço.
9. O WaHostBootstrapper iniciará o processo de hospedar-se dependendo do tipo de função:
    1. **Papel do Trabalhador**: WaWorkerHost.exe é iniciado. WaHostBootstrapper executa o método OnStart(). Após o seu regresso, o WaHostBootstrapper começa a executar o método Run() e, em seguida, marca simultaneamente o papel como Ready e coloca-o na rotação do balançador de carga (se os pontos de entrada forem definidos). WaHostBootsrapper entra então num ciclo de verificação do estado da função.
    2. **Full IIS Web Role**: aIISHost é iniciado. WaHostBootstrapper executa o método OnStart(). Depois de regressar, começa a executar o método Run() e, em seguida, marca simultaneamente o papel como Ready e coloca-o na rotação do balançador de carga. WaHostBootsrapper entra então num ciclo de verificação do estado da função.
10. Os pedidos de entrada na Web para uma função web Full IIS desencadeiam o IIS para iniciar o processo W3WP e servir o pedido, o mesmo que seria num ambiente IIS no local.

## <a name="log-file-locations"></a>Registar localizações de ficheiros

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Este registo contém alterações no serviço, incluindo arranques, paragens e novas configurações. Se o serviço não mudar, pode esperar ver grandes lacunas de tempo neste ficheiro de registo.
- C:\Logs\WaAppAgent.Log.  
Este registo contém atualizações de estado e notificações de batimentos cardíacos e é atualizado a cada 2-3 segundos.  Este registo contém uma visão histórica do estado do caso e dir-lhe-á quando o caso não estava no estado de Ready.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**Registos do IIS**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Registos do Evento Windows**

`D:\Windows\System32\Winevt\Logs`
