---
title: Fluxo de trabalho de arquitetura VM do Azure do Windows | Documentos da Microsoft
description: Este artigo fornece a visão geral dos processos de fluxo de trabalho quando implementa um serviço.
services: cloud-services
documentationcenter: ''
author: genlin
manager: Willchen
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 7c8459a6694663a49203b6ec21a760d3e6bd60c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480761"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Fluxo de trabalho de arquitetura de VM clássica do Windows Azure 
Este artigo fornece uma descrição geral dos processos de fluxo de trabalho que ocorrem ao implementar ou atualizar um recurso do Azure, como uma máquina virtual. 

> [!NOTE]
>O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: Resource Manager e clássica. Este artigo cobre a utilização do modelo de implementação clássica.

O diagrama seguinte apresenta a arquitetura de recursos do Azure.

![Fluxo de trabalho do Azure](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Noções básicas de fluxo de trabalho
   
**A**. RDFE / FFE é o caminho de comunicação do utilizador aos recursos de infraestrutura. RDFE (RedDog Front End) é a API exposta publicamente que é o front-end para o Portal de gestão e a API de gestão do serviço como o Visual Studio, Azure MMC e assim por diante.  Todas as solicitações do usuário passam pelo RDFE. FFE (recursos de infraestrutura de Front-End) é a camada traduz os pedidos de RDFE em comandos de recursos de infraestrutura. Todos os pedidos de RDFE percorrer FFE para aceder o recursos de infraestrutura controladores.

**B**. O controlador de malha é responsável pela manutenção e monitoramento de todos os recursos no Centro de dados. Ele se comunica com agentes de anfitrião de recursos de infraestrutura nos recursos de infraestrutura do envio de informações de sistema operacional, como a versão SO convidado, o pacote de serviço, configuração de serviço e estado do serviço.

**C**. O agente do anfitrião reside no OSsystem anfitrião e é responsável pela configuração do SO convidado e ao comunicar com o agente de convidado (WindowsAzureGuestAgent) para atualizar a função para um Estado de objetivo pretendido e fazer heartbeat verifica com o agente convidado. Se o agente do anfitrião não receber a resposta de heartbeat durante 10 minutos, o agente do anfitrião reinicia o SO convidado.

**C2**. WaAppAgent é responsável por instalar, configurar e atualizar WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent é responsável pelo seguinte:

1. Configurar o SO convidado, incluindo a firewall, ACLs, LocalStorage recursos, o pacote de serviço e configuração e certificados. A configurar o SID da conta de utilizador que a função será executado no.
2. Comunicando o status de função para os recursos de infraestrutura.
3. A partir de WaHostBootstrapper e monitorizá-lo para se certificar de que a função está no estado de objetivos.

**E**. WaHostBootstrapper é responsável por:

1. Ler a configuração da função e, a partir de todas as tarefas adequadas e processos para configurar e executar a função.
2. Monitorização de todos os processos filho.
3. Gerando o evento de StatusCheck sobre o processo de anfitrião de função.

**F**. IISConfigurator é executado se a função estiver configurada como uma função da web de IIS completo (não será executado para funções do SDK 1,2 HWC). Ele é responsável por:

1. A iniciar os serviços padrão do IIS
2. Configurar o módulo de reescrita na configuração da web
3. Como configurar o AppPool para a função configurada no modelo de serviço
4. Configurar o registo do IIS para apontar para a pasta de DiagnosticStore LocalStorage
5. Configurar permissões e ACLs
6. O Web site reside em % roleroot%:\sitesroot\0 e os pontos do apppool para esta localização para executar o IIS. 

**G**. Tarefas de arranque são definidas pelo modelo de função e iniciadas pelo WaHostBootstrapper. Tarefas de arranque podem ser configuradas para executar de maneira assíncrona em segundo plano, e o bootstrapper de anfitrião irá iniciar a tarefa de arranque e, em seguida, avançar para outras tarefas de arranque. Tarefas de arranque também podem ser configuradas para ser executado no modo de simples (predefinição), no qual o bootstrapper de anfitrião irá esperar para a tarefa de arranque, termine a execução e retornar um código de saída de êxito (0) antes de continuar para a próxima tarefa de arranque.

**H**. Essas tarefas fazem parte do SDK e são definidas como plug-ins na definição de serviço da função (. csdef). Quando expandido para tarefas de arranque, o **DiagnosticsAgent** e **RemoteAccessAgent** são exclusivos em que cada um deles definir duas tarefas de arranque, um regular e que tem um **/blockStartup** parâmetro. A tarefa de arranque normal é definida como uma tarefa de arranque em segundo plano, para que ele pode ser executado em segundo plano enquanto a função em si está em execução. O **/blockStartup** tarefa de arranque é definida como uma tarefa de arranque simples para que WaHostBootstrapper aguarda a sair antes de continuar. O **/blockStartup** tarefa aguarda que a tarefa regular concluir a inicialização, e, em seguida, ele é encerrado e permitir que o bootstrapper do anfitrião continuar. Isso é feito para que podem ser configurados antes do início de processos de função (isto é feito através da tarefa de /blockStartup) de diagnóstico e de acesso do RDP. Isto também permite o diagnóstico e de acesso do RDP para continuar a executar após o bootstrapper do anfitrião terminar as tarefas de arranque (isto é feito através da tarefa Normal).

**I**. WaWorkerHost é o processo de host padrão para funções de trabalho normal. Este processo de anfitrião aloja DLLs e código de ponto de entrada, como OnStart e a execução da função.

**J**. WaWebHost é o processo de host padrão para funções da web, se estiverem configurados para utilizar o SDK 1,2 compatível com Alojável Web Core (HWC). Funções podem ativar o modo HWC ao remover o elemento da definição do serviço (. csdef). Neste modo, executam código e DLLs do serviço do processo de WaWebHost. IIS (w3wp) não é utilizado e não há nenhum AppPools configuradas no Gerenciador do IIS, porque está alojado no IIS dentro WaWebHost.exe.

**K**. WaIISHost é o processo de anfitrião para o código de ponto de entrada de função para funções da web que utilizam IIS completo. Este processo carrega a DLL primeiro que se encontra o que utiliza a **RoleEntryPoint** de classe e executa o código a partir dessa classe (OnStart, Run, OnStop). Qualquer **RoleEnvironment** (como StatusCheck e Changed) que são criados na classe RoleEntryPoint são gerados nesse processo.

**L**. W3wp é o processo de trabalho padrão do IIS que é utilizado se a função está configurada para utilizar o IIS completo. Isso é executado o AppPool que está configurado de IISConfigurator. Qualquer RoleEnvironment (como StatusCheck e Changed) que são criadas aqui são gerados nesse processo. Tenha em atenção que os eventos de RoleEnvironment serão acionados em ambos os locais (WaIISHost e w3wp.exe) se subscrever a eventos em ambos os processos.

## <a name="workflow-processes"></a>Processos de fluxo de trabalho

1. Um usuário faz uma solicitação, como carregar ficheiros. cspkg e. cscfg, informar um recurso para parar ou efetuar uma alteração de configuração, e assim por diante. Isso pode ser feito através do portal do Azure ou uma ferramenta que usa a API de gestão de serviço, como o recurso de publicação do Visual Studio. Este pedido vai para RDFE fazer todos os relacionados com a subscrição de trabalho e, em seguida, comunicam o pedido para FFE. O resto destes passos de fluxo de trabalho está a implementar um novo pacote e iniciá-la.
2. FFE localiza o agrupamento de computadores correto (com base na entrada do cliente, que o grupo de afinidade ou a localização geográfica mais a entrada de recurso de infraestrutura, como disponibilidade de máquina) e se comunica com o controlador de malha mestre nesse agrupamento de máquina.
3. O controlador de malha localiza um anfitrião que tenha de núcleos de CPU disponíveis (ou rotações até um novo host). O pacote de serviço e a configuração são copiados para o anfitrião e o controlador de malha comunica com o agente do anfitrião do SO para implementar o pacote de anfitrião (configurar DIPs, portas, SO convidado e assim por diante).
4. O agente do anfitrião é iniciado o SO convidado e comunica com o agente convidado (WindowsAzureGuestAgent). O anfitrião envia heartbeats para o convidado para se certificar de que a função está a funcionar para seu estado de objetivos.
5. WindowsAzureGuestAgent configura o sistema operacional (firewall, ACLs, LocalStorage e assim por diante) do convidado, copia um novo ficheiro de configuração do XML para c:\Config e, em seguida, inicia o processo de WaHostBootstrapper.
6. Para funções de web do IIS completo, WaHostBootstrapper começa IISConfigurator e diz-lhe para eliminar qualquer AppPools existente para a função web do IIS.
7. WaHostBootstrapper lê a **inicialização** tarefas de E:\RoleModel.xml e começa a executar tarefas de arranque. WaHostBootstrapper aguarda até que todas as tarefas de inicialização simples tenham concluído e devolveu uma mensagem de "êxito".
8. Para funções de web do IIS completo, WaHostBootstrapper informa ao IISConfigurator para configurar o AppPool do IIS e aponta o site para `E:\Sitesroot\<index>`, onde `<index>` é um índice 0 com base no número de <Sites> elementos definidos para o serviço.
9. WaHostBootstrapper iniciará o processo de host dependendo do tipo de função:
    1. **Função de trabalho**: WaWorkerHost.exe é iniciado. WaHostBootstrapper executa o método ONSTART (). Após ela retornar, WaHostBootstrapper começa a executar o método Run() e, em seguida, em simultâneo marca a função como pronto e coloca-o para a rotação do Balanceador de carga (se for definidos um InputEndpoints). WaHostBootsrapper, em seguida, entra num loop de verificar o estado de função.
    1. **Função da Web SDK 1,2 HWC**: WaWebHost é iniciado. WaHostBootstrapper executa o método ONSTART (). Após ela retornar, o WaHostBootstrapper começa a executar o método Run() e, em seguida, em simultâneo a marca a função como pronto e coloca-o para a rotação do Balanceador de carga. WaWebHost emite um pedido de aquecimento (GET /do.rd_runtime_init). Todas as solicitações da web são enviadas para WaWebHost.exe. WaHostBootsrapper, em seguida, entra num loop de verificar o estado de função.
    1. **Total de função da Web de IIS**: aIISHost é iniciado. WaHostBootstrapper executa o método ONSTART (). Após ela retornar, começa a executar o método Run() e, em seguida, marca em simultâneo a função como pronto e coloca-o para a rotação do Balanceador de carga. WaHostBootsrapper, em seguida, entra num loop de verificar o estado de função.
10. Os pedidos web recebidos para um IIS completo web acionadores de função IIS para iniciar o processo de W3WP e atender à solicitação, a mesma forma como seria num ambiente de IIS no local.

## <a name="log-file-locations"></a>Localizações de ficheiros de registo

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Este registo contém alterações ao serviço incluindo é iniciado, paradas e configurações de novo. Se o serviço não se altera, pode esperar ver grandes intervalos de tempo neste ficheiro de registo.
- C:\Logs\WaAppAgent.Log.  
Este registo contém as atualizações de estado e notificações de heartbeat e é atualizado a cada 2 a 3 segundos.  Este registo contém uma vista do histórico do Estado da instância e irá informá-lo quando a instância não estava no estado pronto.
 
**WaHostBootstrapper**

C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log
 
**WaWebHost**

C:\Resources\Directory\<guid>.<role>\WaWebHost.log
 
**WaIISHost**

C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log
 
**IISConfigurator**

C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log
 
**Registos do IIS**

C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1
 
**Registos de eventos do Windows**

D:\Windows\System32\Winevt\Logs
 



