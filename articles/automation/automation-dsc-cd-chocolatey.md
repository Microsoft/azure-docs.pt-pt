---
title: Configurar a implantação contínua da Automação Azure com o Chocolatey
description: Este artigo diz como configurar a implantação contínua com a Configuração do Estado e o gestor de pacotes chocolatey.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: ec954c2da317e2e4b332b959b9627cf96792da84
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837064"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Configurar a implementação contínua com o Chocolatey

Num mundo de DevOps, existem muitas ferramentas para ajudar com vários pontos no pipeline de integração contínua. A [Configuração do Estado](automation-dsc-overview.md) da Automação Azure é uma nova adição bem-vinda às opções que as equipas da DevOps podem empregar. 

O Azure Automation é um serviço gerido no Microsoft Azure que permite automatizar várias tarefas usando livros de corridas, nós e recursos partilhados, tais como credenciais, horários e variáveis globais. A Configuração do Estado de Automação Azure alarga esta capacidade de automatização para incluir ferramentas de configuração do Estado desejada powerShell (DSC). Aqui está uma grande [visão geral.](automation-dsc-overview.md)

Este artigo demonstra como configurar a Implementação Contínua (CD) para um computador Windows. Pode facilmente estender a técnica para incluir o número de computadores Windows necessários na função, por exemplo, um site, e ir a partir daí para funções adicionais.

![Implantação contínua para VMs IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>A um nível elevado

Há um pouco acontecendo aqui, mas felizmente pode ser dividido em dois processos principais:

- Escrever código e testá-lo, criando e publicando pacotes de instalação para versões importantes e menores do sistema.
- Criação e gestão de VMs que instalam e executam o código nos pacotes.  

Uma vez que ambos os processos principais estão em vigor, é fácil atualizar automaticamente o pacote nos seus VMs à medida que novas versões são criadas e implementadas.

## <a name="component-overview"></a>Visão geral do componente

Gestores de pacotes como [o apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) são bem conhecidos no mundo linux, mas não tanto no mundo windows.
[Chocolatey](https://chocolatey.org/) é uma coisa, e o [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) de Scott Hanselman sobre o tema é uma grande introdução. Em resumo, o Chocolatey permite-lhe utilizar a linha de comando para instalar pacotes a partir de um repositório central num sistema operativo Windows. Pode criar e gerir o seu próprio repositório, e a Chocolatey pode instalar pacotes a partir de vários repositórios que designasse.

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) é uma ferramenta PowerShell que lhe permite declarar a configuração que deseja para uma máquina. Por exemplo, se quiser que o Chocolatey seja instalado, o IIS instalado, a porta 80 aberta e a versão 1.0.0 do seu website instalado, o DSC Local Configuration Manager (LCM) implementa essa configuração. Um servidor de puxar DSC contém um repositório de configurações para as suas máquinas. O LCM de cada máquina faz verificaperiodicamente para ver se a sua configuração corresponde à configuração armazenada. Pode relatar o estado ou tentar realinhar a máquina com a configuração armazenada. Pode editar a configuração armazenada no servidor pull para fazer com que uma máquina ou um conjunto de máquinas entrem em alinhamento com a configuração alterada.

Um recurso DSC é um módulo de código que tem capacidades específicas, tais como gestão de networking, Ative Directory ou SQL Server. O Chocolatey DSC Resource sabe como aceder a um NuGet Server (entre outros), descarregar pacotes, instalar pacotes, e assim por diante. Existem muitos outros Recursos DSC na [Galeria PowerShell.](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title) Instala estes módulos no seu servidor de pull de configuração de configuração de automação Azure para utilização pelas suas configurações.

Os modelos do Gestor de Recursos fornecem uma forma declarativa de gerar a sua infraestrutura, por exemplo, redes, subredes, segurança de rede e encaminhamento, equilibradores de carga, NICs, VMs, e assim por diante. Aqui está um [artigo](../azure-resource-manager/management/deployment-models.md) que compara o modelo de implementação do Gestor de Recursos (declarativo) com o modelo de implementação de Serviços Azure (ASM ou clássico) (imperativo). Este artigo inclui uma discussão sobre os principais fornecedores de recursos: computação, armazenamento e rede.

Uma característica chave de um modelo de Gestor de Recursos é a sua capacidade de instalar uma extensão VM no VM tal como é provisionado. Uma extensão VM tem capacidades específicas, tais como executar um script personalizado, instalar software antivírus e executar um script de configuração DSC. Existem muitos outros tipos de extensões VM.

## <a name="quick-trip-around-the-diagram"></a>Viagem rápida ao redor do diagrama

Começando por cima, escreve-se o seu código, constrói-o, testa-o e cria-se um pacote de instalação. O chocolate pode lidar com vários tipos de pacotes de instalação, tais como MSI, MSU, ZIP. E tens todo o poder da PowerShell para fazer a instalação real se as capacidades nativas do Chocolatey não estiverem à altura. Coloque o pacote em algum lugar acessível - um repositório de pacotes. Este exemplo de utilização usa uma pasta pública numa conta de armazenamento de blob Azure, mas pode estar em qualquer lugar. O Chocolatey funciona de forma nativa com servidores NuGet e alguns outros para a gestão de metadados de pacotes. [Este artigo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descreve as opções. O exemplo de utilização utiliza o NuGet. Um Nuspec é metadados sobre os seus pacotes. A informação Nuspec é compilada num NuPkg e armazenada num servidor NuGet. Quando a sua configuração solicita um pacote pelo nome e refere um servidor NuGet, o recurso Chocolatey DSC no VM agarra o pacote e instala-o. Também pode solicitar uma versão específica de um pacote.

No canto inferior esquerdo da imagem, há um modelo de Gestor de Recursos Azure. Neste exemplo de utilização, a extensão VM regista o VM com o servidor de puxar de configuração do Estado de Automação Azure como nó. A configuração é armazenada duas vezes no servidor de puxar: uma como texto simples e uma vez compilada como um ficheiro MOF. No portal Azure, o MOF representa uma configuração de nó, em oposição a uma configuração simples. É o artefacto que está associado a um nó para que o nó conheça a sua configuração. Os detalhes abaixo mostram como atribuir a configuração do nó ao nó.

Criar o Nuspec, compilar e armazená-lo num servidor NuGet é uma coisa pequena. E já estás a gerir VMs. 

Dar o próximo passo para a implementação contínua requer configurar o servidor de puxar uma vez, registar os seus nós com ele uma vez, e criar e armazenar a configuração inicial no servidor. À medida que os pacotes são atualizados e implantados para o repositório, só tem de renovar a configuração e a configuração do nó no servidor de puxar, conforme necessário.

Se não estás a começar com um modelo de Gestor de Recursos, tudo bem. Existem comandos PowerShell para ajudá-lo a registar os seus VMs com o servidor de puxar. Para mais informações, consulte [máquinas de embarque para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Sobre o exemplo de utilização

O exemplo de utilização neste artigo começa com um VM a partir de uma imagem genérica do Windows Server 2012 R2 da galeria Azure. Pode começar a partir de qualquer imagem armazenada e depois ajustar a partir daí com a configuração DSC.
No entanto, mudar a configuração que é cozida numa imagem é muito mais difícil do que atualizar dinamicamente a configuração usando DSC.

Não é preciso usar um modelo de Gestor de Recursos e a extensão VM para utilizar esta técnica com os seus VMs. E os teus VMs não têm de estar no Azure para estarem sob gestão de CD. Tudo o que é necessário é que o Chocolatey seja instalado e o LCM configurado no VM para saber onde está o servidor de puxar.

Quando atualizas um pacote num VM que está em produção, precisas de tirar esse VM da rotação enquanto a atualização está instalada. A forma como o fazes varia muito. Por exemplo, com um VM atrás de um Balancer de Carga Azure, pode adicionar uma Sonda Personalizada. Ao atualizar o VM, mande a sonda devolver um 400. O ajuste necessário para causar esta alteração pode estar dentro da sua configuração, assim como o ajuste para alternar para devolver um 200 assim que a atualização estiver completa.

Fonte completa para este exemplo de uso está [neste projeto do Estúdio Visual](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) no GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Passo 1: Configurar o servidor de puxar e a conta de Automação

Numa linha de comando autenticada `Connect-AzAccount` () PowerShell: (pode demorar alguns minutos enquanto o servidor de puxar está configurado)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Você pode colocar a sua conta de Automação em qualquer uma das seguintes regiões (também conhecidas como localizações): Leste DOS 2, Centro-Sul dos EUA, EUA Gov Virginia, Europa Ocidental, Sudeste Asiático, Japão Leste, Índia Central e Austrália Sudeste, Canadá Central, Norte da Europa.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Passo 2: Fazer ajustes de extensão VM para o modelo de Gestor de Recursos

Detalhes para o registo VM (utilizando a extensão PowerShell DSC VM) fornecidos neste [modelo De Arranque Rápido Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Este passo regista o seu novo VM com o servidor de puxar na lista de nós de configuração do Estado. Parte deste registo é especificar a configuração do nó a aplicar ao nó. Esta configuração do nó ainda não tem de existir no servidor pull, por isso é bom que o passo 4 seja onde isto é feito pela primeira vez. Mas aqui no passo 2 precisa de ter decidido o nome do nó e o nome da configuração. Neste exemplo de utilização, o nó é 'isvbox' e a configuração é 'ISVBoxConfig'. Assim, o nome de configuração do nó (a especificar em DeploymentTemplate.json) é 'ISVBoxConfig.isvbox'.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Passo 3: Adicionar os recursos DSC necessários ao servidor de puxar

A PowerShell Gallery está instrumentada para instalar recursos DSC na sua conta Azure Automation.
Navegue para o recurso que deseja e clique no botão "Implementar para a Automatização Azure".

![Exemplo da Galeria PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Outra técnica recentemente adicionada ao portal Azure permite-lhe puxar novos módulos ou atualizar os módulos existentes. Clique no recurso da conta Automation, no azulejo Assets e, finalmente, no azulejo módulos. O ícone da Galeria De Navegação permite-lhe ver a lista de módulos na galeria, aprofundar detalhes e, em última análise, importar para a sua conta Deautomação. Esta é uma ótima maneira de manter os seus módulos atualizados de vez em quando. E, a funcionalidade de importação verifica dependências com outros módulos para garantir que nada se dessincroniza.

Há também uma abordagem manual, usada apenas uma vez por recurso, a menos que queira atualizá-la mais tarde. Para obter mais informações sobre a autoria de módulos de integração PowerShell, consulte [Módulos de Integração de Autorpara Automação Azure](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>A estrutura de pasta de um módulo de integração PowerShell para um computador Windows é um pouco diferente da estrutura de pasta esperada pela Automação Azure. 

1. Instale o [Windows Management Framework v5](https://aka.ms/wmf5latest) (não necessário para o Windows 10).

2. Instale o módulo de integração.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Copie a pasta do módulo a partir de **c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** para uma pasta temporária.

4. Eliminar amostras e documentação da pasta principal.

5. Feche a pasta principal, nomeando o ficheiro ZIP com o nome da pasta.

6. Coloque o ficheiro ZIP numa localização HTTP acessível, como armazenamento de blob numa conta de Armazenamento Azure.

7. Execute o seguinte comando.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

O exemplo incluído implementa estes passos para cChoco e xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Passo 4: Adicione a configuração do nó ao servidor de puxar

Não há nada de especial na primeira vez que importas a tua configuração para o servidor de puxar e compilar. Todas as importações posteriores ou compilações da mesma configuração são exatamente iguais. Cada vez que atualiza o seu pacote e precisa de o empurrar para a produção, faz este passo depois de garantir que o ficheiro de configuração está correto – incluindo a nova versão do seu pacote. Aqui está o ficheiro de configuração **ISVBoxConfig.ps1:**

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

Aqui está o script **New-ConfigurationScript.ps1** (modificado para utilizar o módulo Az):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Estes passos resultam numa nova configuração do nó chamada **ISVBoxConfig.isvbox** sendo colocada no servidor pull. O nome de configuração do nó é construído como `configurationName.nodeName` .

## <a name="step-5-create-and-maintain-package-metadata"></a>Passo 5: Criar e manter metadados de pacotes

Para cada pacote que coloca no repositório de pacotes, precisa de um Nuspec que o descreva. Deve ser compilado e armazenado no seu servidor NuGet. Este processo é descrito [aqui.](https://docs.nuget.org/create/creating-and-publishing-a-package) 

Pode utilizar **MyGet.org** como servidor NuGet. Você pode comprar este serviço, mas você é um sku de entrada grátis. No [NuGet,](https://www.nuget.org/)encontrará instruções para instalar o seu próprio servidor NuGet para os seus pacotes privados.

## <a name="step-6-tie-it-all-together"></a>Passo 6: Amarre tudo junto

Cada vez que uma versão passa qA e é aprovada para implementação, o pacote é criado, e nuspec e nupkg são atualizados e implantados para o servidor NuGet. A configuração (passo 4) também deve ser atualizada para concordar com o número da nova versão. Deve então ser enviado para o servidor de puxar e compilado.

A partir daí, cabe aos VMs que dependem dessa configuração puxar a atualização e instalá-la. Cada uma destas atualizações é simples - apenas uma linha ou duas de PowerShell. Para a Azure DevOps, algumas delas estão encapsuladas em tarefas de construção que podem ser acorrentadas numa construção. Este [artigo](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) fornece mais detalhes. Este [repo GitHub](https://github.com/Microsoft/vso-agent-tasks) detalha as tarefas de construção disponíveis.

## <a name="related-articles"></a>Artigos relacionados
* [Visão geral do DSC da Automação Azure](automation-dsc-overview.md)
* [Máquinas de embarque para gestão pela Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, consulte a visão geral da Configuração do Estado da [Automatização do Azure](automation-dsc-overview.md).
- Para começar a usar a funcionalidade, consulte [Start start with Azure Automation State Configuration](automation-dsc-getting-started.md).
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [configurações de DSC compile na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automatização do Azure](https://azure.microsoft.com/pricing/details/automation/).