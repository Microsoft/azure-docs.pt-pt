---
title: Dimensionar automaticamente os hosts de sessão do Windows Virtual Desktop Preview - Azure
description: Descreve como configurar o script de dimensionamento automático para hosts de sessão do Windows Virtual Desktop Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: bee02549d68b1dbdba3f0e62477b28bbd475ea32
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402495"
---
# <a name="automatically-scale-session-hosts"></a>Dimensionar automaticamente os hosts de sessão

Para muitas implementações de pré-visualização de ambiente de Trabalho Virtual do Windows no Azure, os custos de máquina virtual representam uma parte significativa do custo total para a implantação de área de Trabalho Virtual do Windows. Para reduzir os custos, é melhor encerrar e desalocar sessão alojar máquinas virtuais (VMs) durante o horário de pico de utilização, em seguida, reiniciá-las durante horas de pico.

Este artigo utiliza um script de dimensionamento simples para dimensionar automaticamente máquinas de virtuais de anfitrião de sessão no seu ambiente de área de Trabalho Virtual do Windows. Para saber mais sobre como funciona o script de dimensionamento, veja a [como funciona o dimensionamento script](#how-the-scaling-script-works) secção.

## <a name="prerequisites"></a>Pré-requisitos

O ambiente em que executar o script tem de ter os seguintes procedimentos:

- Um inquilino de área de Trabalho Virtual do Windows e a conta ou um principal de serviço com permissões para consultar esse inquilino (por exemplo, o colaborador de RDS).
- As VMs do conjunto de host de sessão configurados e registado com o serviço de área de Trabalho Virtual do Windows.
- Uma parte do scaler adicional VM que executa a tarefa agendada por meio de agendamento de tarefas e que tem acesso à rede para hosts de sessão.
- O módulo PowerShell do Resource Manager do Microsoft Azure instalado na VM em execução a tarefa agendada.
- O módulo do PowerShell de ambiente de Trabalho Virtual de Windows instalado na VM em execução a tarefa agendada.

## <a name="recommendations-and-limitations"></a>Recomendações e limitações

Quando executar o script de dimensionamento, tenha em mente, os seguintes procedimentos:

- Este script de dimensionamento pode apenas tratar de um conjunto de anfitrião por instância da tarefa agendada que esteja a executar o script de dimensionamento.
- As tarefas agendadas que executam scripts de dimensionamento tem de ser numa VM que está sempre ativada.
- Crie uma pasta separada para cada instância do script de dimensionamento e a respetiva configuração.
- Este script não suporta contas com multi-factor authentication. É recomendável que utilizar principais de serviço para aceder ao serviço de área de Trabalho Virtual do Windows e o Azure.
- Garantia de SLA do Azure só se aplica a VMs num conjunto de disponibilidade. A versão atual do documento descreve um ambiente com uma única VM ao fazer o dimensionamento, o que talvez não atendam aos requisitos de disponibilidade.

## <a name="deploy-the-scaling-script"></a>Implementar o script de dimensionamento

Os procedimentos seguintes indicam como implementar o script de dimensionamento.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Preparar o ambiente para o script de dimensionamento

Em primeiro lugar, prepare seu ambiente para o script de dimensionamento:

1. Entrar para a VM (**dimensionamento VM**) que será executada a tarefa agendada com uma conta de administrador de domínio.
2. Crie uma pasta na VM do dimensionamento para manter o script de dimensionamento e a respetiva configuração (por exemplo, **c:\\HostPool1 dimensionamento**).
3. Transfira o **basicScaler.ps1**, **Config. XML**, e **funções PSStoredCredentials.ps1** arquivos e a **PowershellModules** pasta a partir da [dimensionamento repositório de scripts](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) e copiá-los para a pasta que criou no passo 2.

### <a name="create-securely-stored-credentials"></a>Criar credenciais armazenadas com segurança

Em seguida, terá de criar as credenciais armazenadas com segurança:

1. Abra o ISE do PowerShell como administrador.
2. Abra o painel de edição e carregue o **PSStoredCredentials.ps1 função** ficheiro.
3. Execute o seguinte cmdlet:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Por exemplo, **Set-Variable - nome KeyPath-escopo Global-valor "c:\\HostPool1 dimensionamento"**
4. Executar o **New StoredCredential - KeyPath \$KeyPath** cmdlet. Quando lhe for pedido, introduza as credenciais de área de Trabalho Virtual do Windows com permissões para consultar o conjunto de anfitrião (o conjunto de host é especificado na **Config. XML**).
    - Se utilizar principais de serviço diferentes ou conta padrão, execute o **New StoredCredential - KeyPath \$KeyPath** cmdlet, uma vez para cada conta criar local armazenado as credenciais.
5. Execute **Get StoredCredentials-lista** para confirmar as credenciais foram criadas com êxito.

### <a name="configure-the-configxml-file"></a>Configurar o arquivo Config. XML

Introduza os valores de relevantes para os campos seguintes para atualizar as definições de script de dimensionamento no arquivo Config. XML:

| Campo                     | Descrição                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | ID de inquilino do AD do Azure que associa a subscrição onde executa o anfitrião de sessões de VMs     |
| AADApplicationId              | ID da aplicação principal de serviço                                                       |
| AADServicePrincipalSecret     | Isso pode ser introduzido durante a fase de teste, mas estarão vazio depois de criar as credenciais com **PSStoredCredentials.ps1 de funções**    |
| currentAzureSubscriptionId    | O ID da subscrição do Azure onde executa o anfitrião de sessões de VMs                        |
| tenantName                    | Nome do inquilino de área de Trabalho Virtual do Windows                                                    |
| hostPoolName                  | Nome do conjunto de anfitrião de área de Trabalho Virtual do Windows                                                 |
| RDBroker                      | URL para o serviço WVD, o valor predefinido <https://rdbroker.wvd.microsoft.com>             |
| Nome de utilizador                      | O ID da aplicação principal de serviço (é possível ter a mesma entidade de serviço, como no AADApplicationId) ou um usuário padrão sem multi-factor authentication |
| isServicePrincipal            | Aceite os valores são **true** ou **falso**. Indica se o segundo conjunto de credenciais que está a ser utilizado é um principal de serviço ou uma conta standard. |
| BeginPeakTime                 | Quando começa o tempo de utilização de pico                                                            |
| EndPeakTime                   | Quando termina o tempo de utilização de pico                                                              |
| TimeDifferenceInHours         | Diferença de tempo entre a hora local e UTC, em horas                                   |
| SessionThresholdPerCPU        | Número máximo de sessões por limite de CPU utilizado para determinar quando um novo servidor RDSH tem de ser iniciada durante o horário de pico.  |
| MinimumNumberOfRDSH           | Número mínimo de VMs para continuar a executar durante o tempo de pico de utilização do agrupamento de anfitrião             |
| LimitSecondsToForceLogOffUser | Número de segundos a aguardar antes de forçar os utilizadores para terminar sessão. Se definido como 0, os utilizadores não é forçado a fim de sessão.  |
| LogOffMessageTitle            | Título da mensagem enviada para um utilizador antes de eles são forçados a terminar sessão                  |
| LogOffMessageBody             | Corpo da mensagem de aviso enviada aos utilizadores antes de eles terminar a sessão. Por exemplo, "irá encerrar esta máquina para baixo no X minutos. . Guarde o seu trabalho e terminar sessão." |

### <a name="configure-the-task-scheduler"></a>Configurar o agendador de tarefas

Depois de configurar o ficheiro. XML de configuração, terá de configurar o agendador de tarefas para executar o ficheiro de basicScaler.ps1 em intervalos regulares.

1. Inicie **Programador de tarefas**.
2. Na **agendador de tarefas** janela, selecione **criar tarefa...**
3. Na **criar tarefa** caixa de diálogo, selecione a **gerais** separador, introduza um **nome** (por exemplo, "RDSH dinâmico"), selecione **execute quer iniciada ou não** e **executar com privilégios mais altos**.
4. Vá para o **Acionadores** separador, em seguida, selecione **New...**
5. Na **novo acionador** caixa de diálogo, em **definições avançadas**, verifique **repetir tarefa cada** e selecione o período de adequado e a duração (por exemplo, **15 minutos** ou **indefinidamente**).
6. Selecione o **ações** separador e **New...**
7. Na **nova ação** caixa de diálogo, introduza **powershell.exe** para o **do programa/script** campo, em seguida, introduza **c:\\dimensionamento\\ RDSScaler.ps1** para o **Adicione argumentos (opcional)** campo.
8. Vá para o **condições** e **definições** separadores e selecione **OK** para aceitar as predefinições para cada um.
9. Introduza a palavra-passe para a conta administrativa em que planeja executar o script de dimensionamento.

## <a name="how-the-scaling-script-works"></a>Como funciona o script de dimensionamento

Este script dimensionamento lê as definições de um arquivo Config. XML, incluindo o início e fim do período de utilização de pico durante o dia.

Durante o tempo de utilização de pico, o script verifica o número atual de sessões e a capacidade RDSH atual em execução para cada coleção. Ele calcula se os servidores RDSH em execução tem capacidade suficiente para suportar sessões existentes com base no parâmetro SessionThresholdPerCPU definido no arquivo Config. XML. Caso contrário, o script começa servidores adicionais de RDSH na coleção.

Durante a hora de pico de utilização, o script determina quais os servidores de RDSH devem encerrar com base no parâmetro MinimumNumberOfRDSH no arquivo Config. XML. O script irá definir os servidores RDSH a drenar modo para impedir que novas sessões ligar aos anfitriões. Se definir o **LimitSecondsToForceLogOffUser** parâmetro no arquivo Config. XML para um valor positivo diferente de zero, o script será notificado qualquer atualmente com sessão iniciada sessão dos utilizadores para guardar o trabalho, aguardar o período de tempo configurado e, em seguida, forçar o utilizadores para terminar sessão. Depois de todas as sessões de utilizador tiveram sido terminou a sessão num servidor RDSH, o script irá encerrar o servidor.

Se definir o **LimitSecondsToForceLogOffUser** parâmetro no arquivo Config. XML para zero, o script irá permitir a definição de configuração de sessão nas propriedades da coleção para processar a aceitação de sessões de utilizador. Se existirem quaisquer sessões num servidor RDSH, ele deixará o servidor RDSH em execução. Caso haja qualquer sessões, o script irá encerrar o servidor RDSH.

O script foi concebido para ser executado periodicamente no servidor VM de scaler usando o agendador de tarefas. Selecione o intervalo de tempo adequado com base no tamanho do seu ambiente de serviços de ambiente de trabalho remoto e lembre-se de que a iniciar e encerrar as máquinas virtuais podem demorar algum tempo. Recomendamos executar o script de dimensionamento a cada 15 minutos.

## <a name="log-files"></a>Ficheiros de registo

O script de dimensionamento cria dois ficheiros de registo **WVDTenantScale.log** e **WVDTenantUsage.log**. O **WVDTenantScale.log** ficheiro registrará os eventos e erros (se houver) durante cada execução do script dimensionamento.

O **WVDTenantUsage.log** ficheiro registrará o Active Directory número de núcleos e o Active Directory número de máquinas virtuais sempre que executar o script de dimensionamento. Pode usar essas informações para calcular a utilização real de VMs do Microsoft Azure e o custo. O ficheiro é formatado como valores separados por vírgulas, com cada item que contém as seguintes informações:

>tempo, coleção, núcleos, VMs

O nome do ficheiro também pode ser modificado para ter uma extensão. csv, carregados no Microsoft Excel e analisados.