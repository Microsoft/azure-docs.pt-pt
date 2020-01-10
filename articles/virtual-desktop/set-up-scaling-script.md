---
title: Escala dinâmica hosts de sessão de área de trabalho virtual do Windows – Azure
description: Descreve como configurar o script de dimensionamento automático para hosts de sessão de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
ms.openlocfilehash: a991a41466d216b9f245c20dbd8054f3ae5ef3d0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451327"
---
# <a name="scale-session-hosts-dynamically"></a>Dimensionar os anfitriões de sessão de forma dinâmica

Para muitas implantações de área de trabalho virtual do Windows no Azure, os custos da máquina virtual representam uma parte significativa do custo total de implantação de área de trabalho virtual do Windows. Para reduzir os custos, é melhor desligar e desalocar VMs (máquinas virtuais) do host de sessão fora do horário de pico de uso e, em seguida, reiniciá-las durante o horário de pico de uso.

Este artigo usa um script de dimensionamento simples para dimensionar automaticamente as máquinas virtuais do host de sessão no seu ambiente de área de trabalho virtual do Windows. Para saber mais sobre como o script de dimensionamento funciona, consulte a seção [como o script de dimensionamento funciona](#how-the-scaling-script-works) .

## <a name="prerequisites"></a>Pré-requisitos

O ambiente em que você executa o script deve ter as seguintes ações:

- Um locatário e uma conta de área de trabalho virtual do Windows ou uma entidade de serviço com permissões para consultar esse locatário (como RDS colaborador).
- VMs do pool de hosts de sessão configuradas e registradas no serviço de área de trabalho virtual do Windows
- Uma máquina virtual adicional que executa a tarefa agendada via Agendador de Tarefas e tem acesso à rede para hosts de sessão. Isso será referenciado posteriormente no documento como uma VM scaler.
- O [módulo do PowerShell Microsoft Azure Resource Manager](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) instalado na VM que executa a tarefa agendada.
- O [módulo do PowerShell da área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) instalado na VM que executa a tarefa agendada.

## <a name="recommendations-and-limitations"></a>Recomendações e limitações

Ao executar o script de dimensionamento, tenha em mente as seguintes coisas:

- Esse script de dimensionamento só pode manipular um pool de hosts por instância da tarefa agendada que está executando o script de dimensionamento.
- As tarefas agendadas que executam scripts de dimensionamento devem estar em uma VM que esteja sempre ativa.
- Crie uma pasta separada para cada instância do script de dimensionamento e sua configuração.
- Esse script não dá suporte à entrada como administrador para a área de trabalho virtual do Windows com contas de usuário do Azure AD que exigem autenticação multifator. Recomendamos que você use as entidades de serviço para acessar o serviço de área de trabalho virtual do Windows e o Azure. Siga [este tutorial](create-service-principal-role-powershell.md) para criar uma entidade de serviço e uma atribuição de função com o PowerShell.
- A garantia de SLA do Azure aplica-se somente a VMs em um conjunto de disponibilidade. A versão atual do documento descreve um ambiente com uma única VM fazendo o dimensionamento, o que pode não atender aos requisitos de disponibilidade.

## <a name="deploy-the-scaling-script"></a>Implantar o script de dimensionamento

Os procedimentos a seguir lhe dirão como implantar o script de dimensionamento.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Preparar seu ambiente para o script de dimensionamento

Primeiro, prepare seu ambiente para o script de dimensionamento:

1. Entre na VM (VM de escalar) que executará a tarefa agendada com uma conta administrativa de domínio.
2. Crie uma pasta na VM scaler para manter o script de dimensionamento e sua configuração (por exemplo, **C:\\Scale-HostPool1**).
3. Baixe os arquivos **basicScale. ps1**, **config. JSON**e **Functions-PSStoredCredentials. ps1** , e a pasta **PowershellModules** do repositório de [script de dimensionamento](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) e copie-os para a pasta que você criou na etapa 2. Há duas maneiras principais de obter os arquivos antes de copiá-los para a VM do scaler:
    - Clone o repositório git em seu computador local.
    - Exiba a versão **bruta** de cada arquivo, copie e cole o conteúdo de cada arquivo em um editor de texto e, em seguida, salve os arquivos com o nome de arquivo e o tipo de arquivo correspondentes. 

### <a name="create-securely-stored-credentials"></a>Criar credenciais armazenadas com segurança

Em seguida, você precisará criar as credenciais armazenadas com segurança:

1. Abra o ISE do PowerShell como administrador.
2. Importe o módulo do PowerShell do RDS executando o seguinte cmdlet:

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Abra o painel Editar e carregue o arquivo **Function-PSStoredCredentials. ps1** e, em seguida, execute o script inteiro (F5)
4. Execute o seguinte cmdlet:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Por exemplo, **set-variable-name-caminho KeyPath-scope global-valor "c:\\Scaling-HostPool1"**
5. Execute o cmdlet **New-StoredCredential-KeyPath \$KeyPath** . Quando solicitado, insira suas credenciais de área de trabalho virtual do Windows com permissões para consultar o pool de hosts (o pool de hosts é especificado no **config. JSON**).
    - Se você usar entidades de serviço ou conta padrão diferentes, execute o cmdlet **New-StoredCredential-KeyPath \$KeyPath** uma vez para cada conta para criar credenciais armazenadas locais.
6. Execute **Get-StoredCredential-List** para confirmar que as credenciais foram criadas com êxito.

### <a name="configure-the-configjson-file"></a>Configurar o arquivo config. JSON

Insira os valores relevantes nos campos a seguir para atualizar as configurações de script de dimensionamento em config. JSON:

| Campo                     | Descrição                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | ID de locatário do Azure AD que associa a assinatura em que as VMs de host de sessão são executadas     |
| AADApplicationId              | ID do aplicativo da entidade de serviço                                                       |
| AADServicePrincipalSecret     | Isso pode ser inserido durante a fase de teste, mas será mantido vazio quando você criar credenciais com **Functions-PSStoredCredentials. ps1**    |
| currentAzureSubscriptionId    | A ID da assinatura do Azure em que as VMs host de sessão são executadas                        |
| tenantName                    | Nome do locatário da área de trabalho virtual do Windows                                                    |
| hostPoolName                  | Nome do pool de hosts da área de trabalho virtual Windows                                                 |
| RDBroker                      | URL para o serviço WVD, valor padrão https:\//rdbroker.wvd.microsoft.com             |
| Nome de utilizador                      | A ID do aplicativo da entidade de serviço (é possível ter a mesma entidade de serviço que no AADApplicationId) ou usuário padrão sem autenticação multifator |
| isServicePrincipal            | Os valores aceitos são **true** ou **false**. Indica se o segundo conjunto de credenciais que está sendo usado é uma entidade de serviço ou uma conta padrão. |
| BeginPeakTime                 | Quando o horário de pico de uso começa                                                            |
| Endpicotime                   | Quando o tempo de uso de pico terminar                                                              |
| TimeDifferenceInHours         | Diferença de tempo entre hora local e UTC, em horas                                   |
| SessionThresholdPerCPU        | Número máximo de sessões por limite de CPU usado para determinar quando uma nova VM de host de sessão precisa ser iniciada durante horários de pico.  |
| MinimumNumberOfRDSH           | Número mínimo de VMs do pool de hosts para manter a execução durante o tempo de uso fora do horário de pico             |
| LimitSecondsToForceLogOffUser | Número de segundos a aguardar antes de forçar os usuários a se desconectarem. Se definido como 0, os usuários não serão forçados a sair.  |
| LogOffMessageTitle            | Título da mensagem enviada a um usuário antes de serem forçadas a sair                  |
| LogOffMessageBody             | Corpo da mensagem de aviso enviada aos usuários antes que eles sejam desconectados. Por exemplo, "esta máquina será desligada em X minutos. Salve seu trabalho e saia. " |

### <a name="configure-the-task-scheduler"></a>Configurar o Agendador de Tarefas

Depois de configurar o arquivo JSON de configuração, você precisará configurar o Agendador de Tarefas para executar o arquivo basicScaler. ps1 em um intervalo regular.

1. Iniciar **Agendador de tarefas**.
2. Na janela **Agendador de tarefas** , selecione **criar tarefa...**
3. Na caixa de diálogo **criar tarefa** , selecione a guia **geral** , insira um **nome** (por exemplo, "RDSH dinâmico"), selecione **executar se o usuário está conectado ou não** e é **executado com privilégios mais altos**.
4. Vá para a guia **gatilhos** e selecione **novo...**
5. Na caixa de diálogo **novo gatilho** , em **Configurações avançadas**, marque **repetir tarefa a cada** e selecione o período e a duração apropriados (por exemplo, **15 minutos** ou **indefinidamente**).
6. Selecione a guia **ações** e **novo...**
7. Na caixa de diálogo **nova ação** , insira **PowerShell. exe** no campo **programa/script** e, em seguida, digite **C:\\dimensionamento\\basicScale. ps1** no campo **adicionar argumentos (opcional)** .
8. Vá para as guias **condições** e **configurações** e selecione **OK** para aceitar as configurações padrão de cada uma.
9. Insira a senha da conta administrativa na qual você planeja executar o script de dimensionamento.

## <a name="how-the-scaling-script-works"></a>Como funciona o script de dimensionamento

Esse script de dimensionamento lê as configurações de um arquivo config. JSON, incluindo o início e término do período de pico de uso durante o dia.

Durante o tempo de uso de pico, o script verifica o número atual de sessões e a capacidade de RDSH em execução no momento para cada pool de hosts. Ele calcula se as VMs host da sessão em execução têm capacidade suficiente para dar suporte a sessões existentes com base no parâmetro SessionThresholdPerCPU definido no arquivo config. JSON. Caso contrário, o script inicia VMs de host de sessão adicionais no pool de hosts.

Durante o tempo de uso fora de pico, o script determina quais VMs de host de sessão devem ser desligadas com base no parâmetro MinimumNumberOfRDSH no arquivo config. JSON. O script definirá as VMs do host da sessão para o modo de drenagem para evitar novas sessões se conectando aos hosts. Se você definir o parâmetro **LimitSecondsToForceLogOffUser** no arquivo config. JSON para um valor positivo diferente de zero, o script notificará qualquer usuário conectado no momento para salvar o trabalho, aguardará o período de tempo configurado e forçará os usuários a se desconectarem. Depois que todas as sessões de usuário tiverem sido desconectadas em uma VM de host de sessão, o script desligará o servidor.

Se você definir o parâmetro **LimitSecondsToForceLogOffUser** no arquivo config. JSON como zero, o script permitirá que a configuração de sessão nas propriedades do pool de hosts manipule a assinatura de sessões de usuário. Se houver sessões em uma VM host de sessão, ela deixará a VM host de sessão em execução. Se não houver nenhuma sessão, o script desligará a VM host da sessão.

O script é projetado para ser executado periodicamente no servidor de VM do scaler usando Agendador de Tarefas. Selecione o intervalo de tempo apropriado com base no tamanho do seu ambiente de Serviços de Área de Trabalho Remota e lembre-se de que iniciar e desligar máquinas virtuais pode levar algum tempo. É recomendável executar o script de dimensionamento a cada 15 minutos.

## <a name="log-files"></a>Ficheiros de registo

O script de dimensionamento cria dois arquivos de log, **WVDTenantScale. log** e **WVDTenantUsage. log**. O arquivo **WVDTenantScale. log** registrará os eventos e os erros (se houver) durante cada execução do script de dimensionamento.

O arquivo **WVDTenantUsage. log** registrará o número ativo de núcleos e o número ativo de máquinas virtuais cada vez que você executar o script de dimensionamento. Você pode usar essas informações para estimar o uso real de VMs Microsoft Azure e o custo. O arquivo é formatado como valores separados por vírgula, com cada item contendo as seguintes informações:

>tempo, pool de hosts, núcleos, VMs

O nome do arquivo também pode ser modificado para ter uma extensão. csv, carregada no Microsoft Excel e analisada.
