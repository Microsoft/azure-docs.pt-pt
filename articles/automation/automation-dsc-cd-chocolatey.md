---
title: Configurar a implementação contínua da Azure Automation com chocolatey
description: Este artigo diz como configurar a implementação contínua com a Configuração do Estado e o gestor de pacotes chocolatey.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: bb5f7b5e8214bd3b04bd7b9544ab4bc589f6c4bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896330"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Configurar a implementação contínua com o Chocolatey

Num mundo de DevOps, existem muitas ferramentas para ajudar com vários pontos no oleoduto de integração contínua. [A Azure](automation-dsc-overview.md) Automation State Configuration é uma nova adição bem-vinda às opções que as equipas de DevOps podem empregar. 

A Azure Automation é um serviço gerido no Microsoft Azure que permite automatizar várias tarefas usando runbooks, nós e recursos partilhados, tais como credenciais, horários e variáveis globais. A Azure Automation State Configuration estende esta capacidade de automatização para incluir ferramentas de Configuração do Estado (DSC) desejadas por PowerShell. Aqui está uma grande [visão geral.](automation-dsc-overview.md)

Este artigo demonstra como configurar a Implementação Contínua (CD) para um computador Windows. Você pode facilmente estender a técnica para incluir o número necessário de computadores Windows no papel, por exemplo, um site, e ir daí para funções adicionais.

![Implantação contínua para IaaS VMs](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>A um nível elevado

Há um pouco acontecendo aqui, mas felizmente pode ser dividido em dois processos principais:

- Escrever código e testá-lo, em seguida, criar e publicar pacotes de instalação para versões principais e menores do sistema.
- Criar e gerir VMs que instalam e executam o código nas embalagens.  

Uma vez que ambos os processos principais estão em vigor, é fácil atualizar automaticamente o pacote nos seus VMs à medida que novas versões são criadas e implementadas.

## <a name="component-overview"></a>Visão geral do componente

Os gestores de pacotes como o [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) são bem conhecidos no mundo linux, mas não tanto no mundo windows.
[Chocolatey](https://chocolatey.org/) é uma coisa assim, e o [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) de Scott Hanselman sobre o tema é uma grande introdução. Em resumo, a Chocolatey permite-lhe utilizar a linha de comando para instalar pacotes de um repositório central num sistema operativo Windows. Você pode criar e gerir seu próprio repositório, e Chocolatey pode instalar pacotes de qualquer número de repositórios que você designar.

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) é uma ferramenta PowerShell que permite declarar a configuração que deseja para uma máquina. Por exemplo, se quiser que o Chocolatey seja instalado, o IIS instalado, a porta 80 aberta e a versão 1.0.0 do seu website instalada, o DSC Local Configuration Manager (LCM) implementa essa configuração. Um servidor de puxar DSC contém um repositório de configurações para as suas máquinas. O LCM em cada máquina verifica periodicamente para ver se a sua configuração corresponde à configuração armazenada. Pode reportar o estado ou tentar voltar a alinhar a máquina com a configuração armazenada. Pode editar a configuração armazenada no servidor de puxar para que uma máquina ou conjunto de máquinas se acossem com a configuração alterada.

Um recurso DSC é um módulo de código que tem capacidades específicas, tais como gestão de rede, Diretório Ativo ou SQL Server. O Recurso DSC chocolatey sabe como aceder a um Servidor NuGet (entre outros), descarregar pacotes, instalar pacotes, e assim por diante. Existem muitos outros Recursos DSC na [Galeria PowerShell.](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title) Instale estes módulos no servidor de puxar o estado da automação Azure para utilização pelas suas configurações.

Os modelos de Gestor de Recursos fornecem uma forma declarativa de gerar a sua infraestrutura, por exemplo, redes, sub-redes, segurança de rede e encaminhamento, equilibradores de carga, NICs, VMs, e assim por diante. Aqui está um [artigo](../azure-resource-manager/management/deployment-models.md) que compara o modelo de implementação do Gestor de Recursos (declarativo) com o modelo de implementação (ASM ou clássico) de gestão de serviços (imperativo). Este artigo inclui uma discussão sobre os principais fornecedores de recursos: computação, armazenamento e rede.

Uma característica chave de um modelo de Gestor de Recursos é a sua capacidade de instalar uma extensão VM no VM à medida que for disponibilizada. Uma extensão VM tem capacidades específicas, tais como executar um script personalizado, instalar software antivírus e executar um script de configuração DSC. Existem muitos outros tipos de extensões VM.

## <a name="quick-trip-around-the-diagram"></a>Viagem rápida em torno do diagrama

A partir de cima, escreve-se o seu código, constrói-o, testa-o e cria-se um pacote de instalação. A Chocolatey pode lidar com vários tipos de embalagens de instalação, tais como MSI, MSU, ZIP. E tens todo o poder da PowerShell para fazer a instalação se as capacidades nativas da Chocolatey não estiverem à altura. Coloque a embalagem em algum lugar acessível - um repositório de pacote. Este exemplo de utilização utiliza uma pasta pública numa conta de armazenamento de bolhas Azure, mas pode estar em qualquer lugar. A Chocolatey trabalha nativamente com servidores NuGet e alguns outros para a gestão de metadados de pacote. [Este artigo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descreve as opções. O exemplo de utilização utiliza o NuGet. Um Nuspec é metadados sobre os seus pacotes. A informação nuspec é compilada num NuPkg e armazenada num servidor NuGet. Quando a sua configuração solicita um pacote pelo nome e faz referência a um servidor NuGet, o recurso DSC chocolatey no VM agarra o pacote e instala-o. Também pode solicitar uma versão específica de um pacote.

Na parte inferior esquerda da imagem, há um modelo de Gestor de Recursos Azure. Neste exemplo de utilização, a extensão VM regista o VM com o servidor de puxar o estado de azure automation como nó. A configuração é armazenada duas vezes no servidor de puxar: uma vez como texto simples e uma vez compilada como um ficheiro MOF. No portal Azure, o MOF representa uma configuração de nó, em oposição a uma configuração simples. É o artefacto que está associado a um nó para que o nó saiba a sua configuração. Os detalhes abaixo mostram como atribuir a configuração do nó ao nó.

Criar o Nuspec, compilá-lo e guardá-lo num servidor NuGet é uma coisa pequena. E já estás a gerir os VMs. 

Dar o próximo passo para a implementação contínua requer a configuração do servidor de puxar uma vez, registando os seus nós com ele uma vez, e criando e armazenando a configuração inicial no servidor. À medida que os pacotes são atualizados e implantados no repositório, basta atualizar a configuração e a configuração do nó no servidor de puxar, se necessário.

Se não estás a começar com um modelo de Gestor de Recursos, tudo bem. Existem comandos PowerShell para ajudá-lo a registar os seus VMs com o servidor pull. Para obter mais informações, consulte [máquinas de embarque para gestão através da Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Sobre o exemplo de utilização

O exemplo de utilização neste artigo começa com um VM a partir de uma imagem genérica do Windows Server 2012 R2 da galeria Azure. Pode partir de qualquer imagem armazenada e, em seguida, ajustar a partir daí com a configuração DSC.
No entanto, mudar a configuração que é cozida numa imagem é muito mais difícil do que atualizar dinamicamente a configuração usando DSC.

Não é preciso utilizar um modelo de Gestor de Recursos e a extensão VM para utilizar esta técnica com os seus VMs. E os teus VMs não têm de estar no Azure para estar sob gestão de CD. Tudo o que é necessário é que o Chocolatey seja instalado e o LCM configurado no VM para que saiba onde está o servidor de puxar.

Quando atualiza um pacote num VM que está em produção, tem de tirar esse VM da rotação enquanto a atualização é instalada. A forma como fazes isto varia muito. Por exemplo, com um VM atrás de um Balançador de Carga Azure, pode adicionar uma sonda personalizada. Ao atualizar o VM, o ponto final da sonda devolva um 400. O ajuste necessário para causar esta alteração pode estar dentro da sua configuração, assim como o ajuste para alterá-lo de volta para retornar um 200 uma vez que a atualização esteja completa.

A fonte completa para este exemplo de utilização está [neste projeto do Estúdio Visual](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) no GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Passo 1: Configurar o servidor de puxar e a conta de automação

Numa linha de `Connect-AzAccount` comando PowerShell autenticada: (pode demorar alguns minutos enquanto o servidor de puxar é configurado)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Você pode colocar a sua conta de Automação em qualquer uma das seguintes regiões (também conhecidas como localizações): Leste EUA 2, South Central EUA, EUA Gov Virginia, Europa Ocidental, Sudeste Asiático, Japão Leste, Índia Central e Sudeste da Austrália, Canadá Central, Norte da Europa.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Passo 2: Faça ajustes de extensão VM para o modelo de Gestor de Recursos

Detalhes para o registo VM (utilizando a extensão PowerShell DSC VM) fornecidos neste [Modelo Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Este passo regista o seu novo VM com o servidor pull na lista de nós de configuração do Estado. Parte deste registo é especificar a configuração do nó a aplicar no nó. Esta configuração do nó ainda não tem de existir no servidor de puxar, por isso é bom que o passo 4 seja onde isto é feito pela primeira vez. Mas aqui no Passo 2 você precisa ter decidido o nome do nó e o nome da configuração. Neste exemplo de utilização, o nó é 'isvbox' e a configuração é 'ISVBoxConfig'. Assim, o nome de configuração do nó (a especificar em DeploymentTemplate.js) é 'ISVBoxConfig.isvbox'.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Passo 3: Adicionar os recursos DSC necessários ao servidor pull

A PowerShell Gallery é instrumentada para instalar recursos DSC na sua conta Azure Automation.
Navegue para o recurso que pretende e clique no botão "Implementar para a Automatização Azure".

![Exemplo da Galeria PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Outra técnica recentemente adicionada ao portal Azure permite-lhe puxar novos módulos ou atualizar os módulos existentes. Clique através do recurso de conta Automation, do azulejo 'Activos' e, finalmente, do azulejo dos Módulos. O ícone da Galeria Browse permite-lhe ver a lista de módulos na galeria, aprofundar detalhes e, em última análise, importar para a sua conta Automation. Esta é uma ótima maneira de manter os seus módulos atualizados de vez em quando. E, a funcionalidade de importação verifica as dependências com outros módulos para garantir que nada sai de sincronização.

Há também uma abordagem manual, usada apenas uma vez por recurso, a menos que queira atualizá-la mais tarde. Para obter mais informações sobre a autoria de módulos de integração powerShell, consulte [Módulos de Integração de Autoria para Automação Azure.](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

>[!NOTE]
>A estrutura de pasta de um módulo de integração PowerShell para um computador Windows é um pouco diferente da estrutura de pasta esperada pela Azure Automation. 

1. Instale [o Quadro de Gestão do Windows v5](https://aka.ms/wmf5latest) (não necessário para o Windows 10).

2. Instale o módulo de integração.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Copie a pasta do módulo a partir de **c:\Ficheiros de programa\WindowsPowerShell\Modules\MODULE-NAME** para uma pasta temporária.

4. Elimine as amostras e a documentação da pasta principal.

5. Feche a pasta principal, nomeando o ficheiro ZIP com o nome da pasta.

6. Coloque o ficheiro ZIP numa localização HTTP acessível, como o armazenamento de bolhas numa conta de Armazenamento Azure.

7. Execute o seguinte comando.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

O exemplo incluído implementa estes passos para cChoco e xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Passo 4: Adicione a configuração do nó ao servidor pull

Não há nada de especial na primeira vez que importa a sua configuração para o servidor pull e compila. Todas as importações posteriores ou compilações da mesma configuração são exatamente iguais. Sempre que atualizar o seu pacote e precisar de o empurrar para a produção, faça este passo depois de garantir que o ficheiro de configuração está correto – incluindo a nova versão do seu pacote. Aqui está o ficheiro de configuração **ISVBoxConfig.ps1:**

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

Aqui está o **New-ConfigurationScript.ps1** script (modificado para usar o módulo Az):

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

Estes passos resultam numa nova configuração de nó chamada **ISVBoxConfig.isvbox** sendo colocada no servidor de puxar. O nome de configuração do nó é construído como `configurationName.nodeName` .

## <a name="step-5-create-and-maintain-package-metadata"></a>Passo 5: Criar e manter metadados de pacote

Para cada pacote que colocar no repositório de pacotes, precisa de um Nuspec que o descreva. Deve ser compilado e armazenado no seu servidor NuGet. Este processo é descrito [aqui.](https://docs.nuget.org/create/creating-and-publishing-a-package) 

Pode utilizar **MyGet.org** como servidor NuGet. Você pode comprar este serviço, mas você é um SKU de arranque gratuito. No [NuGet,](https://www.nuget.org/)encontrará instruções sobre a instalação do seu próprio servidor NuGet para os seus pacotes privados.

## <a name="step-6-tie-it-all-together"></a>Passo 6: Amarre tudo junto

Cada vez que uma versão passa QA e é aprovada para implementação, o pacote é criado, e nuspec e nupkg são atualizados e implantados no servidor NuGet. A configuração (passo 4) também deve ser atualizada para concordar com o novo número de versão. Em seguida, deve ser enviado para o servidor de puxar e compilado.

A partir daí, cabe aos VMs que dependem dessa configuração puxar a atualização e instalá-la. Cada uma destas atualizações é simples - apenas uma linha ou duas de PowerShell. Para a Azure DevOps, alguns deles são encapsulados em tarefas de construção que podem ser acorrentadas juntas numa construção. Este [artigo](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) fornece mais detalhes. Este [gitHub repo](https://github.com/Microsoft/vso-agent-tasks) detalha as tarefas de construção disponíveis.

## <a name="related-articles"></a>Artigos relacionados
* [Visão geral da Azure Automation DSC](automation-dsc-overview.md)
* [Máquinas de embarque para gestão pela Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral, consulte [a visão geral da configuração do Estado da Automação Azure](automation-dsc-overview.md).
- Para começar a utilizar a funcionalidade, consulte [Começar com a Configuração do Estado da Automação Azure](automation-dsc-getting-started.md).
- Para saber sobre a compilação de configurações de DSC para que possa atribuí-las aos nós-alvo, consulte [as configurações do Compile DSC na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
- Para obter informações sobre preços, consulte [os preços de configuração do Estado da Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
